# Castle Siege views and website drop guide

Decision 103 adds GameData-owned verified PDO_ODBC and owned DDL 004/store/lock/
grant evidence for the website loot guide. Initialization/writes/replay now check
acknowledgements and exact readback, with private cleanup errors. Native drops and
Castle Siege remain outside this boundary. See GUIDE_ODBC.md; guide-specific SQL
HTTP and trusted deployment TLS remain unverified.

## Castle Siege

`/siege` displays explicitly synthetic owner, contenders, mark totals, UTC cycle
stages and three prior cycles. `/admin/siege` adds native capability diagnostics
behind `database.inspect`. `CastleSiegeCycle` validates ordered boundaries and
unique contenders, computes exact stage transitions and sorts tied marks by guild.
The demonstration calendar rolls over only after the Sunday 20:00–22:00 UTC battle.
This is an owned demo schedule, not an imported server configuration.

`CastleSiegeReader` is an internal read port. Its only runtime binding is
`OfflineCastleSiegeReader`. Known standard/MuEmu/IGCN/modern profiles receive demo
projections; classic/Kayito and unknown profiles receive unavailable state. No
profile name enables native reads, registration or rewards. The authenticated
player POST `/siege/register` verifies CSRF/throttling and then rejects the
operation at the service boundary. Client capability flags cannot enable it.

Native guild-master ownership, roster/cycle snapshots, profile-specific mappings,
registration/review/synchronization, reward ledgers and claims remain unimplemented.
No Siege SQL, native registration or reward delivery was executed.

## Drop and loot guide

`/drops` is a real website-owned guide with item/source/map search, source type
filters and inclusive source-level ranges. It starts empty. Staff create/edit/
delete entries through forms at `/admin/drops`; no synthetic chances are published
by default. This guide does not parse or modify native game drop files. Displayed
rates are independent informational chances supplied by staff, not verified
game-server probabilities or mutually exclusive loot weights.

`LootRule` validates names, monster/event/box types, source levels 0–1,000 and
probabilities. Percentages accept up to four decimal places and are stored as
integer parts per million; no floating-point rounding is used. IDs remain stable
on edits. `LootGuideService` enforces `loot.manage` on every management operation.
`LootGuideStore` is a module-local port with unchanged signatures. Database settings
mode binds PdoLootGuideStore for public/staff consumers. Missing schema/row or
corrupt configuration fails closed with no file/empty fallback. Only the old
standalone offline runner uses FileLootGuideStore. Exact stored probabilities
remain informational website data, never native drop configuration.

A private locked snapshot atomically commits guide entries, immutable API history
and scoped replay results. Global expected revisions prevent lost edits across
workers. Exact replay does not duplicate or resurrect deleted entries. Corrupt
state/journal mismatches fail closed. State is limited to 500 entries, 1,000
changes and 8 MiB. SQL persistence and full-history import are now prepared;
actual SQL Server, independent tamper protection and archival/retention remain
unverified. Windows ACLs determine private file-prototype protection.
POST save/delete routes require staff authorization, same Origin, CSRF and a
dedicated 30-request/15-minute persistent limiter. Public projections omit actor
metadata; audit events omit arbitrary entry text.

## Compatibility and evidence

Both slices remain inside GameData, use module-local ports and consume frozen V1
Authorization. Bootstrap composes providers; views import no other module's
implementation. Module/runtime catalogs declare scope, permissions and tests
without enabling the planned production modules. No public contract or component
schema changed. No SQL migration or legacy access was required.

`tests/siege.php`, `tests/loot.php` and expanded loopback HTTP coverage exercise
phase boundaries, unknown profiles, disabled registration, chance precision,
CRUD/search, escaped hostile labels, permissions, revisions/replay, corruption
and actual concurrent guide edits. Browser QA covers public/admin views and
guide publication/editing at desktop/mobile widths. Exact results are in STATE.md.

## SQL drop-guide migration

GameData's PdoGuideDocument provides the common owned transaction/lock/CAS and
bounded digest-checked document engine for event and loot adapters. Only fixed
events/loot identities select their separate tables; it is not a dynamic table
API or cross-module service. Domain state rules preserve each existing v1 document,
fingerprints, operation scopes, generated IDs and historical results. Core,
operational, event and loot records remain independent transactions.

Review database/mssql/004-loot-guide.sql for the designated website-owned SQL
Server 2016+ database. dbo.zenda_loot_guide holds schema/revision/state_json/digest;
the complete retained journal and current entries commit together. Constructors/
requests install no schema or seed. Runtime needs SELECT/UPDATE only; a separate
operator INSERT initializes an empty target. Native drop files/tables are untouched.
SQL Server PDO_ODBC locks/grants now have owned local evidence under 103. Strict
TLS/restore/deployment remain untested; SQLite HTTP certifies only its own path.

An existing new-project loot-guide.json and stable loot-guide.lock are required
in the explicit private ZENDA_SANDBOX_DIR outside webroots/legacy. For a new guide,
prepare an explicit LootGuideState::initial() provisioning document and lock;
requests never infer an empty database row. Importing a guide with history retains
that revision rather than resetting IDs/retry results. Freeze source edits and
preserve reviewed backups before cutover.

```powershell
& 'C:/xampp/php/php.exe' scripts/import-loot-guide.php
```

The default dry run validates the locked bounded document, prints only validation
and canonical SHA-256 and opens no database. Apply also requires
ZENDA_SETTINGS_MIGRATION_SCOPE=owned-nonproduction, ZENDA_SETTINGS_BACKEND=mssql,
private connection inputs and --apply --expected-sha256=<reviewed-digest>. It
refuses existing target state and preserves source bytes/history. Stale file
writers/readers are not a safe fallback after SQL edits. Actual reviewed DDL 004
and synthetic SQL records now have owned evidence; no production cutover ran.

tests/loot-settings.php verifies generated-ID retries, full-history imports,
permission consumers, exact rates, independent event storage, rollback/CAS,
unacknowledged commit recovery, corruption/capacity and independent-worker create/
retry races. Shared settings HTTP cases cover real SQL-selected guide forms,
filters/public redaction, Origin/CSRF/staff denial, conflict/replay, throttling and
unavailable state without file recovery. Existing views/layout are unchanged;
no new manual browser run is claimed for this storage change. Each read/audit
projection is from its own snapshot; the original port has no atomic combined
read/events method, so a management view may span consecutive revisions.
