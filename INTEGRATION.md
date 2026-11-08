# Integration notes

## Target

This export targets Snipe-IT v8.8.0, upstream commit `8d10063af98aff0e3d5bb40aa56e6c5d732b5181`, using Laravel 12, PHP 8.2 or later, and Blade. It is a partial integration, not a standalone Laravel application. It relies on the host application's existing users, policies, sessions, routes, helpers and test factories. No database migrations or dependency changes are included.

If the destination is another framework, start from the static preview and assets. The PHP controller is specific to Snipe-IT.

## Merge into an existing Snipe-IT checkout

Work on a branch in the developers' repository. The patch contains only sign-in-related files and supporting assets:

```sh
git switch -c feature/statssa-sign-in
git apply --check /path/to/statssa-sso/integration/initial-snipe-it-8d10063.patch
git apply /path/to/statssa-sso/integration/initial-snipe-it-8d10063.patch
php artisan optimize:clear
```

If the patch check fails on a different version, merge manually from `laravel/`. Do not overwrite an existing authentication controller or `config/auth.php` without reviewing their current changes. Existing security patches and identity-provider behaviour must be preserved.

The host application's existing routes are reused: GET `/login` renders the screen, POST `/login` processes the selection, and `home` is the success destination. No route changes are required for the recorded base commit. If integrating elsewhere, verify these route names and the CSRF/session middleware.

The exported layout loads `welcome.css` and `welcome-theme.css`. It deliberately does not load `stats-sa-theme.css`. Assets are ordinary files in `public/`; no npm build is needed for these additions. The existing application uses Laravel Mix, not Vite. If changing its bundled assets, use its established `npm run dev` or `npm run prod` workflow.

## Local role-selection demo

Set these values only in a local development environment:

```dotenv
APP_URL=http://localhost:8000
DEMO_ROLE_LOGIN=true
```

The configured usernames must already exist and be activated:

| Role | Username |
| --- | --- |
| Superuser | admin |
| Asset Manager | assetmgr |
| License Manager | licensemgr |
| Accessory Manager | accessorymgr |
| Consumable Manager | consumablemgr |
| Component Manager | componentmgr |
| User Manager | usermgr |

Mappings are in `config/auth.php`. This package does not create accounts or modify their permissions. Missing or inactive accounts return a form error. The handler validates the selected role, regenerates the session and retains the account's existing permissions. A submitted username cannot replace the mapped account.

The guard checks the configured APP_URL host against localhost, 127.0.0.1 and ::1. That is not a replacement for network isolation. Bind a demo service to loopback and do not expose the passwordless demo publicly. Set `DEMO_ROLE_LOGIN=false` to restore the host's normal sign-in flow, then clear configuration cache.

## Actual SSO

No identity-provider metadata, client ID, client secret, tenant configuration or SSO callback is supplied. For SAML/OIDC deployment, keep the demo branch disabled and connect the page to the team's approved provider flow. Identity and roles must be established server-side. The role selector in the demo is not an authorisation control suitable for production.

## Files to know

| File | Purpose |
| --- | --- |
| resources/views/layouts/welcome.blade.php | Page shell, logo, support email and stylesheet references |
| resources/views/auth/role-login.blade.php | Heading, role form, artwork and submitting/recovery states |
| resources/views/auth/partials/welcome-intro.blade.php | Hand overlay, crease canvas and Skip intro control |
| resources/views/auth/partials/welcome-intro-script.blade.php | Timed hand/page movement, damped crease surface and cleanup |
| resources/lang/en-US/auth/role-login.php | All page copy and role labels |
| public/css/welcome.css | Layout, responsive rules and intro geometry |
| public/css/welcome-theme.css | Isolated Stats SA colour and typography overrides |
| public/img/ | Supplied logo, current asset collage and hand artwork |
| app/Http/Controllers/Auth/LoginController.php | Optional local role-login branch within the existing controller |
| config/auth.php | Opt-in flag and account mappings |
| tests/Feature/Auth/RoleLoginTest.php | Role-login feature regression tests |

The intro plays on first arrival and reload, lasts about 3.8 seconds, and respects reduced motion. Escape, Skip intro, resize, page hiding or an error state leave the form usable. Browsers without `inert` support skip the intro. The animation's timing and surface parameters are in the intro script; its geometry is in `welcome.css`.

The support link is `mailto:statssa@assetverification.co.za`. The artwork is one composite image; replacing individual objects requires editing that image.

## Validation

On 8 November 2026 the role-login feature suite passed: **18 tests, 96 assertions**. It covers role mappings, invalid/missing roles, inactive/missing accounts, attempted username override, access after login, rejection of non-local APP_URL and the disabled-demo fallback.

Run tests only against a disposable test database using the host project's `.env.testing.example`. Set a separate DB_DATABASE and test credentials; never use a live or demo database containing required records. Test factories and migrations are supplied by the host application.

A Docker process can inherit production APP_ENV and cached configuration. For this validation the process was explicitly given APP_ENV=testing, SESSION_DRIVER=array, CACHE_DRIVER=array, an isolated test DB_DATABASE and an APP_CONFIG_CACHE path that does not exist. This bypasses the production cache without deleting it. Equivalent invocation after configuring the dedicated test database:

```sh
APP_ENV=testing \
APP_CONFIG_CACHE=/tmp/statssa-sso-test-config-not-cached.php \
DB_DATABASE=your_isolated_test_database \
SESSION_DRIVER=array CACHE_DRIVER=array \
php artisan test tests/Feature/Auth/RoleLoginTest.php
```

In Docker, pass these as `docker compose exec -e NAME=value ... app ...` process variables. Supplying APP_ENV alone is insufficient if the application still loads a production configuration cache. Do not run `config:cache` using the test-only cache path.

Manual acceptance checks: desktop and mobile layout, every role and a missing-account response, keyboard focus, invalid submission, back navigation after sign-in, support email destination, slow or failed artwork loading, reduced motion, Skip intro and reload playback. A provider-connected SSO flow requires its own integration and authorisation tests.

## Assets and licensing

Snipe-IT's AGPL notice is included in `licenses/`. Inter's OFL notice accompanies its local font asset. The active sign-in theme uses Arial/Helvetica Neue with system fallbacks; the base stylesheet retains a local Inter font declaration, so its font file and licence are included as a dependency.

The Stats SA logo was supplied by the client. The office collage and hand artwork were edited using the image-generation tool for this project. Keep their provenance and confirm the client's branding and asset approvals before public release. This package does not assert ownership of the supplied government mark.
