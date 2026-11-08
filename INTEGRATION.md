# Integration notes

## Current source

Use the complete [Stats SA application repository](https://github.com/luvuyoskhosana04/statssa-asset-management) for a runnable project, setup instructions, page map and role guides. This repository contains only the sign-in surface and selected integration files.

The target is Snipe-IT v8.8.0, upstream commit `8d10063af98aff0e3d5bb40aa56e6c5d732b5181`, using Laravel 12 and PHP 8.2 or later.

## Credential sign-in

`laravel/resources/views/auth/login.blade.php` renders username and password fields in the branded welcome layout. The controller retains the existing local/LDAP credential checks, inactive-account rejection and throttling. Account and group permissions determine role access. The old role-selection bypass is removed, even if a legacy environment still sets DEMO_ROLE_LOGIN.

Review and merge the selected `laravel/` files at their matching paths in the host application. Do not blindly replace a controller or config file in a different Snipe-IT version. The welcome layout references the isolated `welcome-theme.css` supplied here. The full project instead uses `stats-sa-theme.css`.

`integration/initial-snipe-it-8d10063.patch` is an immutable historical export with the previous role picker. Do not apply it for the current credential form.

## Preview

The preview is visual only, intercepts submission and does not authenticate or send credentials. Do not use real credentials in a static preview. Reload plays the welcome animation; reduced-motion preferences skip it.

## Checks

Run `tests/Feature/Auth/RoleLoginTest.php` in the host project against a separate test database. The suite now tests credentials, all seven role types, inactive accounts, wrong passwords and bypass prevention. See the full project's validation guide for container environment/cache isolation.

Real identity-provider SSO requires separate configuration and validation. No identity-provider credentials, live users, database dump or uploaded files are included.
