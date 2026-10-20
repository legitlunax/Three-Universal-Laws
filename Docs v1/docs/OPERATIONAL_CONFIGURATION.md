# News and runtime configuration

Decision 099 adds an explicit Admin PDO_ODBC dialect and owned local SQL tests for
this existing version-2 journal alongside core version 1. Stored documents,
command hashes and authorization interfaces remain unchanged. Unicode binding,
complete journal reads, grants, process races and locks have dedicated fixture
evidence. Decision 102 additionally verifies operational editor reads, saves,
independent revisions, replay/conflicts and request guards over actual loopback
HTTP with explicit injected SQL stores and restricted runtime grants. Full
default-factory cutover and trusted TLS remain unverified. See MSSQL_LAB.md and
MSSQL_HTTP.md.

In database mode, Admin's internal OperationalSettingsStore is the authority for
news connection and runtime policy. Bootstrap reads it before composing public,
player or staff workflows. Environment news variables, config.example.json and
import files are not fallback sources. Missing/corrupt/unsafe state returns a
redacted 503; requests never install or seed tables. `/health` returns constant
liveness information and is not a database readiness check.

## Representation and compatibility

The new record is an exact schemaVersion 2 envelope with news and runtime fields.
News holds backend (drupal or the explicitly offline local prototype), baseUrl
and allowLoopback. Drupal URLs use HTTPS, no credentials/query/fragment, with an
optional installation path. Only explicit 127.0.0.1 permits the local HTTP
development exception. Content's existing transport still validates/pins DNS,
denies private targets/redirects and bounds response sizes and pagination.

Runtime contains the offline environment/fixture descriptor, disabled game writes,
payments/template publication, required CSRF/throttling/server authorization,
private read-only synthetic preview, and image upload policy. Only uploads.enabled
and uploads.maxBytes are editable: 1024..2097152 bytes. MIME/extension allowlists
remain fixed to PNG/JPEG/WebP, executable storage is forbidden, and unknown keys,
wrong types or unsupported policy values refuse the entire record. No database
credentials or game account connection can be entered here. Cache TTLs derive
from the existing core record rather than being stored twice.

The original core document/table remains schema version 1 and its command
fingerprints and public/internal signatures remain compatible. A fixed-table
PdoConfigurationJournal now supplies the common transaction implementation for
both typed adapters. Public V1/template contracts and frozen baselines are unchanged.
Decision 092 strengthens that journal for both records: owning coherent reads,
complete contiguous typed history, current document/latest command agreement and
validation before exact replay. Every write and transaction acknowledgement is
checked, including initialization and replay; failed/zero-row stages roll back
privately. Old v1 request hashes remain compatible, and older hashes stay opaque
because historic values were not stored. See the shared boundary and 336 new
SQLite/process plus 14 HTTP cases in [website configuration](WEBSITE_CONFIGURATION.md).
Core and operational records have independent revisions and transactions; there
is no atomic combined snapshot or command spanning both records. Each operational
request snapshot is reused by its composition and editor; services invalidate
their request-local memo after successful commands. No cross-request cache exists.

## Staff editing and upload consumers

`/admin/configuration` requires the separate website administrator session and
admin.access at the application service. Saves additionally require Origin/CSRF,
an expected revision, a random retry key and the persistent 20-attempt/900-second
client/scoped-actor budget. Successful saves redirect; stale or changed-key saves
return 409 and malformed commands 422. Exact historical retries preserve later
configuration. Minimal command metadata and the snapshot commit together.

In database mode, uploads.enabled controls POST /admin/assets and
POST /admin/templates/api/upload. Both still require templates.edit, Origin/CSRF,
and share a 20-attempt/900-second image-upload budget. The selected byte limit is
passed to every uploader composition. Existing image decoding, generated names,
MIME/extension checks and private non-executable storage remain required. Disabling
uploads hides the form and rejects new uploads, preserving existing asset reads
and authorized removal. Enabling website image uploads never enables template
publication or native capabilities. The old file prototype remains offline-only.

## Explicit installation and initialization

Review database/mssql/002-operational-settings.sql for a designated website-owned
SQL Server 2016+ database. It adds dbo.zenda_operational_settings and
dbo.zenda_operational_commands without altering the version-1 tables. Both records
use the same 16 KiB document and 10000-revision limits, strict digests, owning
transactions, revision CAS and retry semantics described in
[website configuration](WEBSITE_CONFIGURATION.md). History stores no policy/URL
values and is not a restore archive. Production SQL permissions/recovery remain
unverified. Reviewed DDL and synthetic records now have owned local SQL evidence;
no existing database was selected for migration.

Prepare an explicit provisioning document named operational-settings-v2.json in
the selected **existing new offline sandbox**, outside webroots and the legacy
tree. It is import input, never runtime configuration. Construct the envelope from
OperationalSettings with NewsSettings and RuntimeSettings; inspect it privately.
The default dry run opens no database and prints only validation plus canonical SHA:

```powershell
& 'C:/xampp/php/php.exe' scripts/import-operational-settings.php
```

Select the source with ZENDA_SANDBOX_DIR. Apply additionally requires
ZENDA_SETTINGS_MIGRATION_SCOPE=owned-nonproduction, ZENDA_SETTINGS_BACKEND=mssql,
private connection inputs and --apply --expected-sha256=<reviewed-digest>. The
target tables must already exist and be empty. Initialization inserts revision
zero, refuses replacement and preserves the source. Provision both core and
operational records before cutover; do not fall back to stale files or an older
application that ignores operational policy after database edits.

Owned SQLite unit/HTTP/browser fixtures verify this application boundary, not
SQL Server driver/TLS/locking behavior. Drupal's own configuration database,
template/other module settings and production staff persistence still need
their separate MSSQL migrations. Event/loot adapters and retained-history imports
are now also prepared with owned SQLite evidence; actual SQL Server cutover for
all these families remains pending. This is partial configuration migration.
