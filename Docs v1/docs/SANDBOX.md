# Local website sandbox

The first phase 4 slice provides a MU Online themed public portal, synthetic
rankings, local website administrator sign-in and plaintext announcement
publishing. The user approved continuation after the phases 1–3 review on
2026-10-17. [STATE.md](../STATE.md) remains the current checkpoint;
[architecture/runtime.json](../architecture/runtime.json) records this partial
offline implementation. Full production modules remain planned.

## Run locally

Run from `C:/xampp/htdocs/ZendaWeb/ZendaMod-Next`:

```powershell
& 'C:/xampp/php/php.exe' -S 127.0.0.1:8091 -t public public/router.php
```

Open `http://127.0.0.1:8091/`. VS Code and Cursor also expose the
`Development: PHP sandbox server` task and a prepared browser launch profile.
Stop the terminal server with Ctrl+C, or use **Tasks: Terminate Task** for the IDE
task. Read [WORKSPACE.md](WORKSPACE.md) for task and debugger prerequisites.

Serve only `public/`, with `public/router.php` for PHP's development server.
Source-denying `.htaccess` files provide an additional Apache guard when honored
by the host; they do not replace the required webroot. The router serves the
application and owned assets without exposing helper PHP or source files. This
loopback development setup is not a production deployment procedure.

## Create a local administrator

There are no default credentials. Before signing in, run:

```powershell
& 'C:/xampp/php/php.exe' scripts/setup-sandbox.php --username=youradmin
```

Enter a new password on standard input when prompted. Input may be visible in
the terminal. The password must contain valid UTF-8 and be 12–72 bytes long;
multibyte characters consume more than one byte. The username must start with
an ASCII letter and contain 3–32 ASCII letters, digits, dots, underscores or
hyphens. It is normalized to lowercase.

Setup creates one local website administrator and refuses to overwrite an
existing credential record. It stores a password hash, not the plaintext
password. Account recovery and multi-user administration are not implemented.
This administrator is independent of MU Online player accounts.

AdminCP must always use a separate website administrator account. A game-account
login, an in-game GM/staff flag or a matching player username must never sign a
user into AdminCP or grant its permissions. Website-player and administrator
credentials/session realms stay separate. This is a permanent requirement, not
only a limitation of the offline sandbox.

Drupal news mode uses a separate newsroom and disables local announcement
mutations; see [DRUPAL_NEWS.md](DRUPAL_NEWS.md). All production website
configuration belongs in MSSQL; the file-backed settings here are temporary
offline prototypes, with files permitted for disposable cache only.

In database mode (`ZENDA_SETTINGS_BACKEND=mssql`), website administrator credentials
persist authoritatively in website-owned MSSQL storage via `PdoCredentialStore`
(`database/mssql/009-admin-credentials.sql`), with guarded one-time migration from
offline sandbox files provided by `scripts/import-admin-credentials.php`.
Website player accounts and password resets similarly persist in website-owned MSSQL
storage via `PdoPlayerAccountStore` (`database/mssql/010-player-accounts.sql`), with
guarded one-time migration provided by `scripts/import-player-accounts.php`.

Core settings now also have a prepared MSSQL mode with strict revisioned saves;
see [WEBSITE_CONFIGURATION.md](WEBSITE_CONFIGURATION.md). The default standalone
runner remains the file prototype. Actual SQL Server integration and remaining
configuration migration are not certified by its SQLite/HTTP fixtures.

## Private state

Credentials, sessions, throttling state and published news live outside the
source tree and webroot. The default Windows directory is
`%LOCALAPPDATA%/ZendaMod-Next/sandbox`. On POSIX hosts, the default is a per-user
directory beneath the operating system's temporary directory.

`ZENDA_SANDBOX_DIR` can override that location. It must be an absolute, dedicated
private directory outside the project and webroot; never point it at project
`var/`, the legacy tree, a user home root or the temporary-directory root. Setup
and the server must use the same setting. Existing path ancestors are checked
before creation, including junction/symlink resolution.

POSIX credential ownership and mode checks require `posix_geteuid` and fail closed
when unavailable. Windows protection depends on the host's directory and file ACLs. This project
does not install or change system ACLs. Malformed persisted content fails closed
and is not silently replaced with seed data.

## Available behavior

| Route | Behavior |
| --- | --- |
| `GET /` | Published announcements and explicitly synthetic rankings. |
| `GET /admin/login` | Local website administrator sign-in; unavailable until setup. |
| `POST /admin/login` | Rate-limited sign-in with same-origin and CSRF checks. |
| `GET /admin` | Server-authorized announcement dashboard. |
| `GET /news`, `GET /news/article?id=...` | Published announcement search/archive and full article; 20 per page and five on the homepage. |
| `POST /admin/news` | Authorized plaintext announcement publication. |
| `POST /admin/logout` | CSRF-protected sign-out. |
| `GET/POST /login`, `GET /account`, `POST /logout` | Separate registered website-player session; no native game identity. |
| `GET/POST /account/profile` | Owned display/bio/visibility preferences; default Private. |
| `GET /account/profile/preview` | Owner-only read-only preview of saved fields. |
| `GET/POST /account/password` | Current-password recheck, atomic reset invalidation/history and player-session revocation. |
| `GET /player?username=...` | Opted-in website profile; unmatched native fixtures are labelled synthetic. |
| `/support`, `/admin/support` | Private player conversations and authorized staff inbox/replies/status actions. |
| `/siege`, `/admin/siege` | Synthetic UTC cycle/history and disabled native capability diagnostics. |
| `/drops`, `/admin/drops` | Public website-maintained loot information and authorized entry editor. |
| `/events`, `/admin/events` | Public persistent UTC timetable and authorized schedule editor. |
| `GET /events/calendar` | Bounded UTC calendar snapshot; optional `days=1..14`. |

Requests are restricted to loopback clients and trusted local Host values.
Administrative POSTs require the expected Origin and server-generated CSRF
token. Sessions rotate on sign-in, use strict, HttpOnly cookies, expire after
20 minutes idle or eight hours total, and clear authority on sign-out.

News titles allow 120 Unicode characters and bodies allow 4,000. Content is
stored as plain text and escaped when rendered. The JSON repository serializes
writes under a cross-process lock and uses atomic replacement. Storage is
bounded to 1,000 rows and 16 MiB. Staff editing/deletion and public archive
pagination use the existing content boundary. See [ANNOUNCEMENTS.md](ANNOUNCEMENTS.md).
These website content operations do not mutate game data.

## Scope and verification

The offline item/character markets at `/market` and `/market/characters` support
synthetic draft creation and cancellation, integer pricing/fees and read-only
transfer requirements. `/admin/market` provides draft history, moderation and
its atomic market journal. Drafts do not reserve assets or execute trades; no MU
account association is inferred from the sandbox administrator's username.
See [MARKETPLACE.md](MARKETPLACE.md) for limits and routes.

Registered website players can sign in independently of the sandbox staff
account. Support tickets and drop-guide edits persist in private website files
with revision/replay/audit protection. See [PLAYER_SUPPORT.md](PLAYER_SUPPORT.md)
and [SIEGE_AND_LOOT.md](SIEGE_AND_LOOT.md). Native market and Siege operations stay
disabled. Webshop/VIP actions are request-local synthetic previews; they do not
deliver native items or activate native subscriptions.

Website event editing uses separate `events.manage`, CSRF/Origin, throttling and
revision/replay protection. Calendar files reflect the website timetable only;
they do not execute events. See [EVENT_TIMETABLE.md](EVENT_TIMETABLE.md).

Profile publication requires explicit owner choice and never infers native
ownership from a matching name. Registered underscores/hyphens work through login,
sessions, profiles and support. Older public readers ignore privacy choices;
read [WEBSITE_PROFILES.md](WEBSITE_PROFILES.md) before considering rollback.

Signed-in players can change their website password at `/account/password`.
Changes require the current password and confirmation, invalidate existing reset
tokens and sign out player sessions. See [WEBSITE_PASSWORDS.md](WEBSITE_PASSWORDS.md)
for policy, limits, persistence and compatibility. Staff/native credentials remain
separate. The [rollback proposal](WEBSITE_ROLLBACK.md) requires tooling/rehearsal.

No live MU accounts, game database, email, payments or game writes are connected.
GrapesJS, SQL Server adapters and production deployment remain future work.
Other games are an architectural possibility; no universal game adapter or
Metin2/Minecraft integration is implemented.

Run the commands in [TESTING.md](../TESTING.md). Complete verification includes
identity, content, view and loopback HTTP tests in addition to tooling and
synthetic migration checks. Consult [STATE.md](../STATE.md) for actual results
and browser inspection evidence; these checks do not certify live integrations.
