# Website configuration in MSSQL

Decision 100 records the planned guided installer/setup, verified recovery and
AdminCP factory reset to default website configurations with setup available
again. This is a requirement note, not an implemented or enabled operation.
Installation state remains in MSSQL; MU game/account data is outside reset scope,
including shared database topologies. See docs/INSTALLATION_LIFECYCLE.md.

All authoritative website configuration belongs in website-owned Microsoft SQL
Server tables/database. Files may hold disposable cache only. AdminCP uses a
separate website administrator account; game logins, GM flags and matching player
names never grant administration rights.

This checkpoint prepares **core settings, news/runtime policy, event/loot guides
and schema mapping drafts** for MSSQL.
The five core fields remain realm title, website registration enabled, maintenance
mode, rankings TTL and server-status TTL. A separate version-2 record stores the
Drupal connection and bounded runtime policy; see [operational settings](OPERATIONAL_CONFIGURATION.md).
The shared journal's transaction/HTTP behavior was exercised with owned SQLite fixtures.
The host has mysql/odbc/sqlite drivers but no `pdo_sqlsrv`. A later user-authorized
local lab probe now connects through PDO_ODBC and reads only metadata/synthetic
expressions. Admin core/operational stores have explicit ODBC binding under 099;
101 additionally verifies separate website administrator storage with DDL 009.
Decision 102 adds 41 scoped real SQL HTTP cases for administrator login and
core/operational settings through explicit injected lab stores. Other stores,
strict certificate validation and whole-site/default-factory HTTP cutover remain
pending. See [owned SQL HTTP evidence](MSSQL_HTTP.md) and
[local SQL lab](MSSQL_LAB.md). This is not complete configuration migration or
SQL Server adapter certification.

## Sources and remaining migration inventory

| Configuration | Current authority / next migration |
| --- | --- |
| Five core website settings | Explicit MSSQL mode binds the new SQL adapter; the default standalone offline runner still uses FileSettingsStore |
| News backend/Drupal URL/loopback permission | Database mode uses the version-2 operational record and /admin/configuration; old standalone file mode alone uses development environment inputs |
| Runtime security/capability/preview/upload settings | Database mode uses the strict version-2 record; protected code gates refuse unsafe values, and upload enablement/byte limits control both image routes |
| Template projects, revisions and publication settings | FK-correct SQL draft/history storage and atomic full-collection import prepared; null expected revision is create-only, active SQL publication disabled; see TEMPLATE_STORAGE.md; actual SQL Server unverified |
| Event schedules | Full-history SQL adapter/importer bound; owned PDO_ODBC locks/grants/transport verified under 103; guide-specific SQL HTTP/calendar and trusted deployment pending |
| Website loot definitions | Full-history SQL adapter/importer bound; owned PDO_ODBC locks/grants/transport verified under 103; guide-specific SQL HTTP and trusted deployment pending |
| Market listings, drafts, and replays | PdoMarketDraftStore, DDL 006 and MarketStorageImport prepared; database mode binds SQL store, actual SQL Server unverified |
| Support tickets, messages, and replays | PdoSupportStore, DDL 007 and SupportStorageImport prepared; database mode binds SQL store, actual SQL Server unverified |
| Website player profiles and replays | PdoWebsiteProfileStore, DDL 008 and WebsiteProfileStorageImport prepared; database mode binds SQL store, actual SQL Server unverified |
| Website administrator credentials | PdoCredentialStore, DDL 009 and CredentialStorageImport bound; owned PDO_ODBC grants/locks/races under 101 and scoped loopback HTTP under 102; trusted TLS/full default-factory cutover pending |
| Website player accounts and password resets | PdoPlayerAccountStore and DDL 010 prepared; atomic import retains history/consumed recovery state; see PLAYER_ACCOUNT_STORAGE.md and WEBSITE_RECOVERY.md; public recovery disabled pending secure delivery; actual SQL Server unverified |
| Schema profile mapping drafts | DDL 011, immutable SQL store and guarded importer supply /admin/schema in database mode; no live detection/native adapter activation; see CATALOG_CONFIGURATION.md |
| Shop/VIP catalogs and other module settings | Isolated immutable draft stores/CLI and private V5 administrator inspection prepared; public database storefronts and purchases still refuse; other module configuration remains pending |
| Drupal configuration | Separate SQLite development site; Drupal SQL Server driver/migration still unverified |

Credentials, accounts, sessions, player profiles and support records are separate
persistence concerns; this settings adapter does not migrate them or merge staff
and game/player identities. Deployment connection credentials are private bootstrap
secrets, outside ordinary settings records/source. Source-controlled contracts,
schemas and security rules are development policy, not editable site configuration.

## Storage and mutation boundary

`SettingsStore` keeps its original signatures. `RevisionedSettingsStore` is an
internal extension; SettingsService uses its snapshot/command methods when bound.
Unversioned database `save`/`updateSettings` intentionally refuse operations:
existing database callers must migrate, rather than silently bypass concurrency
checks. Old file-adapter callers remain compatible. Public V1 contracts/template
properties/frozen baselines are unchanged. See decision 062.

The SQL adapter has no DDL/default-writing side effects. Missing tables/seed,
corrupt/oversized documents, wrong schema/revision/digest or invalid typed settings
are unavailable. Reads never repair state or select a file fallback. A complete
document and digest are stored together with a bounded integer revision in the
singleton `dbo.zenda_website_settings`; `dbo.zenda_settings_commands` records only
key, actor, request fingerprint, prior/new revision and UTC epoch. It contains no
configuration values or credentials and is not a historic settings restore store.
The digest detects accidental corruption, not privileged database tampering.

Decision 092 checks the complete bounded journal on every owning read and before
every command/replay. There must be exactly one valid singleton and exactly the
contiguous revisions 1 through its current revision, with unique valid keys,
actors, hashes and typed UTC epochs. The latest command hash must match the current
normalized document. Older configuration values were never retained, so their
hashes remain opaque; this does not reconstruct old settings or detect a privileged
attacker coherently rewriting both tables. Existing schema versions, normalized
documents and command hashes remain unchanged. Existing in-range historical clocks
need not be monotonic, matching the original v1 semantics.

Saves own one transaction, reject nesting, lock/recheck the snapshot, enforce an
expected revision and commit the snapshot plus command history together. Exact
retries return the original revision without changing later settings; changed
actor/payload/revision with the same key conflicts. SQL Server uses
[UPDLOCK/HOLDLOCK](https://learn.microsoft.com/en-us/sql/t-sql/queries/hints-transact-sql-table);
actual driver/isolation/lock behavior still needs non-production evidence. SQLite
uses its own writer-lock path and does not certify SQL Server synchronization.

Reads now also own their transaction and reject a caller transaction. All readers,
writers and initializers acquire the selected snapshot table first: shared
TABLOCK/HOLDLOCK or exclusive TABLOCKX/HOLDLOCK on SQL Server, with SQLite's
pre-read writer boundary for writes. Reads retain one coherent head/journal view;
core and operational records still have independent boundaries. Every snapshot,
history and initialization write must affect exactly one row. Resulting state and
every commit, including replay, are checked. Failed/zero-row/false boundaries
return private errors and roll back owned work; ambiguous committed saves resolve
through exact replay. A failed rollback never produces success; discard/reconcile
an uncertain connection. Initialization checks both selected tables empty under
the same boundary and never overwrites a committed but unacknowledged seed.

The history/revision cap is 10000 changes; full state remains readable and refuses
further mutations. No pruning/retention/archive tooling exists. A database failure
returns a redacted 503; stale/reused commands return 409 and invalid input 422.
HTTP saves require separate staff authorization, Origin/CSRF and a persistent
20-attempt/900-second client/scoped-actor budget. Public/player maintenance and
registration read the selected store through Bootstrap; no consumer directly
reopens the old settings file in database mode. Database saves skip the old
separate file audit append, because their minimal history is in the owning commit.

SettingsService memoizes only within its request/service instance and clears that
memo after a successful command, including replay. Each new request reads the
database. No cross-request settings cache exists, so removing any old cache/file
cannot remove database settings. The stored TTL fields do not establish a working
cross-request rankings/server-status cache; those readers remain offline prototypes.

## Connection and installation inputs

Set `ZENDA_SETTINGS_BACKEND=mssql` explicitly for the designated environment.
`file` or an unset value selects only the old standalone offline prototype;
unknown values fail closed. There is no automatic SQL/file failover.

| Private bootstrap variable | Meaning |
| --- | --- |
| `ZENDA_SETTINGS_SQLSERVER` | Host, optional named instance and/or comma-separated port; no DSN fragments |
| `ZENDA_SETTINGS_DATABASE` | Explicit website-owned database; system databases are refused |
| `ZENDA_SETTINGS_DB_USER`, `ZENDA_SETTINGS_DB_PASSWORD` | Both supplied privately for SQL authentication, or both absent for driver-integrated authentication |

Use a PHP/ODBC driver combination compatible with PHP 7.4. ZENDA_SETTINGS_DRIVER
is an explicit private bootstrap input: unset means sqlsrv; `odbc`
selects ODBC Driver 18. It never falls back between drivers. Both require TLS
certificate validation and no persistent/MARS reuse. ODBC requires pooling off
at process startup and sets a five-second lock timeout; PHP 7.4 PDO_ODBC does not
provide sqlsrv-equivalent bounded login/general-query attributes. SQL Server mode
retains its existing bounded login/query settings. See
[Microsoft connection options](https://learn.microsoft.com/en-us/sql/connect/php/connection-options).
Do not disable TLS validation merely to make a connection succeed. Raw connection
errors, strings and bindings are never included in application errors.

Review `database/mssql/001-website-settings.sql` for a designated owned database.
It requires SQL Server 2016+ JSON validation and creates two new tables in one
transaction, refusing an existing target. It does not alter/import the old
`zenda_settings` key/value table. No script automatically executes that DDL.
Runtime permission should allow SELECT/UPDATE on the singleton and SELECT/INSERT
on history, with no DDL/history UPDATE/DELETE. Also review the additive
`database/mssql/002-operational-settings.sql` and explicitly initialize its separate
record before selecting database mode. Both records are required; the older
five-setting document/table stays version 1. Seed initialization uses a separate
operator permission. The opt-in SQL suite exercises reviewed DDL 001/002/009 and runtime grants only in a
fresh marked local fixture; hosting grants/backup/restore and whole-site cutover
remain unverified. DDL review is not a successful deployment migration.

## Explicit migration of this project's file prototype

For a complete **source-only** plan of the four prepared families, select the
existing new sandbox with ZENDA_SANDBOX_DIR and run:

```powershell
& 'C:/xampp/php/php.exe' scripts/configuration-preflight.php
```

All four explicit source documents and event/loot locks must exist. The command
accepts no apply/connection arguments, opens no database and prints only logical
record IDs, schema/revision metadata, canonical hashes and installation/import
paths. One invalid/missing source refuses the whole plan with no partial output.
It never invents defaults or copies source values into a report. Freeze source
edits: reads are separately locked, not a combined atomic snapshot. A valid plan
does not certify SQL Server or authorize deployment; each importer still checks
its reviewed fingerprint and designated nonproduction scope before connecting.
Core/operational imports start at revision zero; event/loot imports retain history.

Freeze configuration edits, preserve the source, and select the **existing new
sandbox directory** explicitly through `ZENDA_SANDBOX_DIR`, outside webroots and
the legacy tree. No installed legacy settings may be used. Then:

```powershell
& 'C:/xampp/php/php.exe' scripts/import-website-settings.php
```

The default dry run opens no database, prints no settings/credentials and leaves
the source unchanged. It requires all five typed fields and emits a canonical
SHA-256. Review the source privately and retain that fingerprint. For a separately
authorized, installed, empty non-production target, select MSSQL connection inputs
privately, set `ZENDA_SETTINGS_MIGRATION_SCOPE=owned-nonproduction`, then invoke
`--apply --expected-sha256=<reviewed-digest>`. Scope, backend and fingerprint must
match before any connection attempt. Initialization inserts revision zero only;
it refuses an existing snapshot/history and does not delete the source file.

Freeze/drain, verified backups, source/target comparison, restoring a compatible
database snapshot and explicit cutover remain operator responsibilities. Never
reopen the old file adapter as automatic fallback after database edits: its values
are stale and its writers lack the new command history. This tool has no production
apply authorization or certified operational rollback rehearsal.

## Verification

`tests/settings.php` covers strict initialization/reads, revisions, historical
retries/conflicts, caller-owned transactions, journal failure rollback, ambiguous
commit recovery, capacity refusal, two-process CAS, safe imports and default CLI
dry run. `tests/settings-http.php` exercises real HTTP with an explicitly injected
SQLite store, conflicting file state, staff/CSRF/Origin denial, strict inputs,
throttling, fresh public reads, shared player maintenance and corrupt-state refusal.
No fixture-mode environment switch is available in the public entry point.

tests/configuration-journal.php adds 336 SQLite/process assertions for both
records: typed/complete history and latest-head agreement, corrupt read/replay/
mutation refusal without repair, caller transactions, failed/zero-row writes,
false begin/commit/replay/rollback acknowledgements, ambiguous save/initialization,
10000-revision capacity/replay, initializer/CAS/retry races and a concurrent commit
between head/journal reads. The shared HTTP suite adds 14 cases (207 assertions
total) covering both editors' rollback, private corrupt-history reads/retries and
no file fallback or detached success audit. Existing 53 core and 58 operational
assertions remain; the old capacity fixture now supplies a valid latest hash and
the corrupt-journal assertion additionally requires read refusal and raw-state
preservation. Catalog/VIP/shop/profile-mapping migration and physical premium
packaging remain pending; this repair does not implement those families.

`tests/operational.php` checks the version-2 policy, shared journal, unchanged core
fingerprints, import guards and distinct SQL constraint names. The HTTP suite also
checks database-selected Drupal reads, operational editing/replays/conflicts,
upload disablement/limits on both routes, preserved existing assets and unsafe or
missing operational-state refusal across public/player/staff consumers. Browser
QA exercises the form at desktop/mobile sizes on an owned SQLite fixture.

Earlier Admin/content/template/identity consumers remain covered by the shared
runner. tests/configuration-preflight.php verifies all-or-nothing redacted output,
source preservation and fingerprints matching each actual dry-run importer.
SQL Server DDL, authentication/TLS, lock contention/deadlocks, driver result
types, trigger/row-count behavior, least-privilege grants and operational recovery
remain missing evidence. A 200 `/health` is liveness, not database readiness.
