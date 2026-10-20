# Drupal news integration

The user selected [Drupal](https://github.com/drupal/drupal) for news, replacing
the proposed phpBB/Superdesk choices. Drupal owns authoring, publication and
revisions; Zenda keeps its frontend, accounts, game boundaries and separate
GrapesJS live website/template builder. Drupal staff accounts are separate from
Zenda staff accounts; there is no SSO or credential forwarding.

## Runtime boundary

`PublishedNewsReader` is an internal Content query port. `NewsRepository` extends
it without changing its existing methods; file/PDO consumers remain compatible.
NewsService accepts the broader reader and refuses writes when no repository is
bound. Bootstrap alone selects the adapter. Public V1 contracts, template
properties and frozen baselines are unchanged. See decision 059.

Database mode obtains backend/base URL/loopback permission from the version-2
operational record, edited through `/admin/configuration`. It ignores the news
environment variables below. Missing operational configuration fails closed;
there is no automatic file fallback. See [operational settings](OPERATIONAL_CONFIGURATION.md).

Development connection parameters for the old standalone file prototype only:

| Parameter | Behavior |
| --- | --- |
| `ZENDA_NEWS_BACKEND=drupal` | Select Drupal; `local` remains the default for the old offline sandbox |
| `ZENDA_DRUPAL_URL` | Drupal base URL, HTTPS, optional installation path, no credentials/query/fragment |
| `ZENDA_DRUPAL_ALLOW_LOOPBACK=1` | Explicit development exception for `127.0.0.1`, including HTTP |

These environment parameters are development/bootstrap inputs, not a production
settings store. **All authoritative website configuration must live in MSSQL**;
files may hold disposable cache only. The new website connection record is
prepared for MSSQL but tested with SQLite fixtures; Drupal's own configuration
database still needs MSSQL integration. Credentials remain private
deployment secrets. See [database policy](../DATABASE.md).

The anonymous GET adapter uses Drupal's
[JSON:API](https://www.drupal.org/docs/core-modules-and-themes/core-modules/jsonapi-module/api-overview)
`/jsonapi/node/article` collection and UUID detail endpoints. It requests only
title, processed body, created time and publication status, with `status=1`, and
follows Drupal's next links, including sparse pages. Responses must explicitly
confirm published status. Private drafts return no public detail. The existing
`publishedAt` field uses Drupal's authored `created` time in UTC, not the exact
moment of publication. Drupal UUIDs replace local IDs in this mode; old local
announcements remain untouched and are not imported or redirected automatically.

HTML becomes escaped plaintext; scripts/styles/embedded objects are discarded.
Titles must fit 120 Unicode characters and bodies 4000 after conversion. The
provisioning script requires plaintext bodies but does not enforce these length
limits in Drupal's editor. Oversized/malformed articles refuse the read rather
than silently truncate. Images, rich formatting, localization, scheduled
publication, incremental synchronization and database search are not integrated.

Reads are bounded to 50 rows/page, 20 pages/1000 articles, 1 MiB/response and
16 MiB combined. Each request has a five-second timeout; pagination has a
12-second inter-page budget. Changing upstream pages are not an atomic snapshot.
Redirects, credentials, foreign pagination links and private/reserved network
addresses are denied; DNS results are validated and pinned for the connection.
There is no content cache or fallback to old local news in Drupal mode. An
unavailable/invalid upstream produces redacted 503 responses. Staff can still
open administration when the configured Drupal service is unavailable.

The staff dashboard links to Drupal's `/admin/content` and removes local news
forms. Local mutation POSTs still require staff authorization/Origin/CSRF and
then return 409; the local edit GET also returns 409. Drupal mode never writes
local news or appends the older local news audit. Drupal revisions do not certify
an immutable atomic audit or the planned local revision/idempotency mechanism.

## Isolated development setup

Keep Drupal outside XAMPP/webroots and run it with its own PHP 8.4 binary.
Zenda remains PHP 7.4 compatible. Do not alter XAMPP's PHP installation.
Create an isolated `drupal/recommended-project:^11.4` with Composer, require
`drush/drush:^13` there, and install an owned development site with a generated
administrator password and private SQLite database. SQLite is a temporary offline
fixture, **not compliant production configuration persistence**.

From that Drupal project, using its PHP binary and Drush script:

```powershell
& $drupalPhp vendor/drush/drush/drush.php en jsonapi -y
$env:ZENDA_DRUPAL_CONFIGURE = '1'
& $drupalPhp vendor/drush/drush/drush.php php:script "$zendaRoot/integrations/drupal/configure-news.php"
Remove-Item Env:ZENDA_DRUPAL_CONFIGURE
```

Provisioning explicitly creates/reuses Article plus its body field, requires
plaintext, enables new revisions and makes JSON:API read-only. It changes Drupal
configuration and is intended for the designated isolated news site. It creates
no users or articles. Review existing content before applying to another site.
Grant anonymous published-content access only and audit Drupal's entity/field
permissions and exposed resources before deployment.

Run the two local servers from Zenda:

```powershell
./scripts/serve-drupal-news.ps1 -DrupalProject $drupalProject -DrupalPhp $drupalPhp -SandboxDirectory $privateSandbox
```

It binds only `127.0.0.1`, defaults to Drupal 18881/Zenda 18882, keeps logs outside
webroots and stops its owned child servers on exit. It uses PHP development
servers; production hosting is not provided. Zenda staff setup still uses
`scripts/setup-sandbox.php` with the same private sandbox selected explicitly.

This workstation's ignored `reports/drupal-runtime.json` records the owned
installation directory and verified PHP/Composer download hashes. Drupal 11.4.10,
PHP 8.4.26, Composer 2.10.3 and Drush 13.8.0 were installed there. The generated
local administrator password is private in that directory; it is not in source,
reports or documentation. A published synthetic article and private draft remain
for development; no personal/game records were imported.

For production, the Drupal
[SQL Server driver](https://www.drupal.org/project/sqlsrv) advertises Drupal 11
support in 5.x. Its actual PHP/ODBC/SQL Server behavior, configuration storage,
migrations and deployment remain unverified here. No MSSQL server was contacted.

## Evidence

`tests/drupal.php` covers mapping/privacy, URL/network guards, sparse pagination,
cycles/duplicates, malformed/capacity failures, safe text, denied service writes
and dashboard compatibility. `tests/drupal-http.php` connects real Zenda HTTP to
a synthetic HTTP source and covers staff/CSRF denial, upstream edits/withdrawals,
failure behavior and absence of local writes. These tests need no Drupal install.

Separate real Drupal browser QA verified login, editing, new revisions,
unpublication (archive removal/detail 404), republication, hidden drafts, Zenda's
staff newsroom link and desktop/mobile output. Screenshots are in ignored
`reports/drupal-news-{desktop,mobile,admin}.png`. This manual run is not an
automated Drupal regression suite or production/MSSQL certification.
An anonymous POST to the real Drupal Article JSON:API returned 405. Both owned
development servers were stopped after verification; the isolated installation
and synthetic articles remain available for an explicit local restart.
