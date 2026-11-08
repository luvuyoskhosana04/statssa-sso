# Stats SA sign-in screen

Source for the Stats SA Asset Management and Verification Solution sign-in screen, including the responsive layout, credential form, support email, office-asset artwork and hand/page-pull animation.

This repository contains the SSO screen only. It does not contain the full asset-management application, its database, uploaded user files or credentials.

## Run the visual preview

Clone this repository, open a terminal in its root folder, and run:

```sh
python3 -m http.server 8766 --bind 127.0.0.1
```

Open http://127.0.0.1:8766/preview/ in Chrome. Press Ctrl+C in the terminal to stop the preview server. You can also open `preview/index.html` directly.

The preview demonstrates the UI and animation. Clicking Sign in displays a preview-only message and does not authenticate. Reload to replay the animation; Escape or Skip intro ends it. Reduced-motion preferences skip it.

## Project layout

| Path | Purpose |
| --- | --- |
| `preview/` | Standalone visual preview |
| `laravel/` | Maintained Snipe-IT integration source, with application paths preserved |
| `laravel/public/css/` | Base welcome styles and isolated Stats SA sign-in theme |
| `laravel/public/img/` | Logo, office-asset collage and hand artwork |
| `laravel/resources/views/auth/partials/` | Hand animation markup and script |
| `INTEGRATION.md` | Laravel integration and authentication setup |
| `integration/` | Initial handover patch and provenance |
| `licenses/` | Snipe-IT and Inter licence notices |

The initial patch targets Snipe-IT v8.8.0, commit `8d10063af98aff0e3d5bb40aa56e6c5d732b5181`. It is an initial handover artifact, not an automatically updated copy of subsequent changes. Use the current `laravel/` files for ongoing development.

## Authentication

The current screen uses username and password. Existing account permissions determine access; the passwordless role picker has been removed. The `laravel/` folder is a partial integration and requires the host Snipe-IT application. It is not an independently runnable Laravel project or a configured identity-provider SSO integration.

For the complete application, developer setup and seven role guides, use [statssa-asset-management](https://github.com/luvuyoskhosana04/statssa-asset-management).

The `integration/initial-snipe-it-8d10063.patch` is historical and includes the superseded role picker. Do not apply it to implement current sign-in. Use the maintained files or the full application repository.

## Working together

Before starting work, pull the latest changes. Keep related changes in a short-lived branch:

```sh
git switch main
git pull --ff-only
git switch -c feature/describe-your-change
# Make and check your changes
git add .
git commit -m "Describe the change"
git push -u origin feature/describe-your-change
```

Open a pull request into `main` for review. Once merged, everyone can pull the updated `main` branch. Public visibility allows anyone to read and clone the code; pushing still requires owner or collaborator permission. Developers without write access can fork the repository and submit a pull request.

For visual changes, update both the preview and the corresponding Laravel markup or styles, then check desktop, mobile, keyboard navigation and reduced motion. For backend changes, run the credential sign-in tests against an isolated test database as described in `INTEGRATION.md`.

## Validation and licences

The current credential sign-in suite passed 12 tests with 49 assertions on 8 November 2026 using an isolated test configuration. `VALIDATION.txt` records that handover check; it does not certify later commits.

The PHP integration derives from Snipe-IT and carries its AGPL-3.0 terms. Inter carries the SIL Open Font License. Relevant notices are in `licenses/` and alongside the font. The Stats SA mark was supplied for the project; this repository does not grant ownership of that mark. The office collage and hand artwork were edited for this project with an image-generation tool.
