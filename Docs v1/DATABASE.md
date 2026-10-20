# Database boundaries and compatibility

Decision 110 adds private read-only physical plugin schema inspection, using a
separate dbo operator connection and a bounded plain-table profile. It compares
actual catalog observations with existing logical footprints through an
authorized Order/Singularity composition. It reads no native rows, installs no
objects, changes no grants and records no installed version. Two matching scans
are not an atomic DDL snapshot or execution consent. See
[schema inspection](docs/PLUGIN_SCHEMA_INSPECTION.md) for profile/visibility
limits, SQL evidence and the remaining executor/provisioning gates.

Decision 105 adds kernel-owned plugin approval head/audit storage through
reviewed DDL 012 and an independently verified bounded ASCII PDO_ODBC profile.
Its lazy operator service checks separate administrator permissions first;
approval, enable/disable, revisions/replay and audit commit together. Runtime
cannot insert/delete a head or update/delete audit. No constructor/request DDL,
implicit initialization, optional transport dependency or disk fallback exists.
Decision 106 adds independent schema-bound scalar configuration and unverified
migration-attempt receipts through explicit DDL 013 and the shared kernel journal
transport. See docs/PLUGIN_METADATA_STORAGE.md. Verified installed migration
execution, guarded provisioning UI/CLI and plugin HTTP integration remain pending. Actual owned SQL
evidence is local lab evidence, not trusted TLS/deployment or MU compatibility.
See docs/PLUGIN_APPROVAL_STORAGE.md.

## External game DatabaseType vs kernel MSSQL

`DatabaseTypeAPI` is specifically for removable **game/account external transports** (`zenda.db.mssql`, future `zenda.db.mysql`/`zenda.db.pgsql`, etc.), **not** an alternative to authoritative website-owned MSSQL settings, administrator identities, templates, approvals and plugin ledgers. Kernel website persistence must work when `/plugins` is empty. Connection profiles specify separate logical website/game/account permissions and never expose secrets to feature plugins. `GameTypeAPI` declares the game; `SchemaAdapterAPI` independently verifies emulator/table/codec support. No direct arbitrary SQL/DDL/native writes during discovery. See `API_ARCHITECTURE.md`, `DATABASE_TYPE_API.md`, and `SCHEMA_ADAPTER_API.md`. No new database adapter or migration is implemented by this documentation.


## Plugin code presence versus authoritative MSSQL state (target)

`/plugins/<id>/` contains distributable code/resources only, never the source of
persistent plugin settings. An approved package's *physical presence* is
necessary for any runtime route/listener/job/widget to exist; its installed,
approved, enabled, licensed and migration state belongs in **website-owned MSSQL**.
Physical removal immediately overrides the stored enabled bit at the next
request and is *non-destructive*: preserve settings, audit, plugin-owned tables,
plugin-owned records, template references and version ledger. Re-adding a
compatible approved folder may resume. Unknown/changed code needs approval,
DDL requires a separately guarded migration, and no missing database row is
silently synthesized from file metadata. `PLUGIN_SYSTEM.md` is normative.

Website-owned plugin storage must never auto-migrate or purge native MU databases.
The explicit offline SQLite/file fixtures do not change production MSSQL policy.
No plugin SQL schemas or runtime installer have yet been applied.

Decision 103 adds actual owned SQL guide evidence over reviewed DDL 003/004 using
GameData's independent ODBC profile and bounded eight-MiB Unicode transport.
The suite now passes 187 assertions, including 43 guide transport/history/grant/
lock/race/failure cases; runtime remains SELECT/UPDATE and provisioning uses
SELECT/INSERT. No existing/native database is selected. Guide SQL HTTP/calendar,
strict TLS, deployment and restore remain unverified. See docs/GUIDE_ODBC.md.

Decision 102 verifies administrator login and core/operational settings over
actual loopback HTTP using the prepared SQL stores and restricted runtime grants.
The opt-in suite now has 144 assertions, including 41 HTTP cases, and removes
its marked database/private HTTP root. Injection uses explicit lab certificate
trust; strict default-factory TLS and whole-site cutover remain unverified. The
same-name player realm case uses file fixtures, not SQL player storage. No existing
database or MU record is touched. See docs/MSSQL_HTTP.md.

Decision 101 extends owned PDO_ODBC evidence to Identity's separate website
administrator credentials and reviewed DDL 009. Runtime SELECT/INSERT-only grants,
different-name process races, strict readback, corruption/failure denial, locks
and ambiguous commits pass in the fresh marked fixture. Identity owns its ASCII
VARCHAR profile; remaining stores still refuse unverified ODBC. This is separate
from native MU data, trusted TLS, whole-site HTTP and deployment evidence. See
docs/ADMIN_CREDENTIAL_STORAGE.md.

Decision 099 now verifies Admin core/operational settings against a newly created
ownership-marked local SQL fixture database through PDO_ODBC. Reviewed DDL 001/002,
Unicode/long values, real process races/locks, rollback, replay and runtime grants
have owned evidence. Existing databases are never selected for initialization.
Application certificate validation remains strict; the lab alone trusts its
local test certificate. Other module stores and HTTP integration remain unverified.
See docs/MSSQL_LAB.md for the explicit transport and bounded wire representation.

A user-confirmed local test server now has read-only PHP 7.4 PDO_ODBC transport
evidence (metadata and synthetic expressions only). It exposes certificate trust
and Unicode conversion requirements; only the later owned Admin/administrator
suites verify their specific adapters. Remaining stores need explicit transport
work. No existing database was
selected for website initialization; the later 099 suite runs reviewed DDL only
in a newly created owned fixture. Private encrypted
lab input handling and precise limits are in docs/MSSQL_LAB.md.

Decision 098 adds private database-mode administrator reads of these installed
drafts with database.inspect checked before lazy connection. This uses website
storage only and displays an escaped read-only document. Owned HTTP uses a
query-only SQLite connection; actual SQL Server remains unverified. Missing,
empty or corrupt state is unavailable without code/file fallback, DDL or repair.
Public database storefronts and purchase processing stay disabled.

Template DDL 005's revision-to-head foreign key is now enforced in SQLite fixtures
and respected by creation/import ordering. Complete-source imports commit all
heads/revisions/pointers once, without partial per-revision initialization. Draft
CAS and immutable canonical rows are distinct from publication; active SQL publish
is unavailable pending its real validator. Native market mutations are likewise
unbound; escrow-store transactions cover website metadata, not game custody,
balances or delivery. See docs/TEMPLATE_STORAGE.md and docs/MARKETPLACE.md.

## Required website configuration storage

User direction (2026-10-18): **all website configuration belongs in MSSQL**,
using website-owned tables/database and module-owned infrastructure adapters.
Files may hold disposable cache only; cache eviction must never erase settings,
and a database failure must not silently fall back to file configuration.
This covers site/module/template/publication settings and Drupal-managed website
configuration. Source-controlled contracts/schema definitions are development
artifacts, not mutable site settings. Deployment connection credentials remain
private bootstrap secrets, outside ordinary settings records and source control.

Current FileSettingsStore, JSON runtime configuration and SQLite-only integration
fixtures are development prototypes; they do not satisfy production persistence.
The core five-setting and separate version-2 news/runtime SQL adapters, additive
MSSQL DDL and guarded provisioning import tools are prepared. Database mode requires
both explicitly initialized records; missing operational policy never falls back
to environment/JSON. Transactions/HTTP/browser tests use owned SQLite fixtures.
See docs/OPERATIONAL_CONFIGURATION.md for independent revisions and policy limits.
Decision 092 validates each complete core/operational journal under an owning
coherent snapshot and before replay, checks the latest request against the current
document and requires every write/commit acknowledgement. Empty initialization,
zero-row failures, ambiguity and concurrency have owned SQLite/process/HTTP
evidence only; no SQL Server migration, DDL or deployment was executed.
Event timetables additionally have a module-owned SQL adapter, explicit
003-event-timetable.sql and source-preserving full-history importer. Database mode
requires its initialized row for event routes; it never opens the old file/default
reader on failure. Owned SQLite worker/HTTP tests verify the application path,
not SQL Server. See docs/EVENT_TIMETABLE.md for ownership and history cutover.
The website loot guide has the corresponding 004 installation input and retained-
history importer; shared GameData SQL infrastructure uses fixed identities and
separate tables. scripts/configuration-preflight.php validates all four explicit
sources and prints only fingerprints/metadata, without connecting or applying.
It requires a source-edit freeze for a coherent operator plan; it does not create
a globally atomic source snapshot or replace actual SQL Server verification.
Actual MSSQL DDL/driver/transaction verification and migration of the remaining
configuration are still required. See docs/WEBSITE_CONFIGURATION.md.
DDL 011 and a module-owned SQL adapter now prepare immutable schema profile
mapping drafts for /admin/schema, with explicit guarded provisioning and no
code/file fallback in database mode. Stored declarations configure no game
connection/reader/writer or capability. Actual SQL Server and editable revision
migration remain unverified. See docs/CATALOG_CONFIGURATION.md.
Decision 097 prepares isolated immutable shop/VIP draft catalog stores, package-owned
DDL and guarded V4 CLI provisioning. Both use website storage only; installed
missing/corrupt state refuses without code/file fallback. Their services can read
the selected stored catalog, but database storefront routes remain unavailable.
No SQL Server DDL/connection/cutover ran here; owning locks/driver/grants/TLS still
require actual evidence. See docs/PREMIUM_CATALOGS.md for exact package paths,
provisioning and independent record boundaries.
Drupal's isolated SQLite development site is also
temporary: Drupal SQL Server driver/version compatibility and configuration
persistence must be verified before production deployment. No MSSQL migration
or live database connection is claimed by this note.

Current phase-4 addition: local website credentials, sessions, login throttling and
plaintext announcements use private files outside the webroot. This is an offline
development adapter, not production website persistence or a MU database adapter.
The earlier phase-1–3 assessment below remains the game/database boundary baseline.

At the Phase 1–3 review gate, database contracts and policy are defined; production adapters, native writers, website persistence, schema discovery and real migration tooling are **not implemented**. No database connection, SQL statement, schema change, player-data test or legacy bootstrap was executed. Synthetic migration fixtures demonstrate fixture compatibility only.

The full static evidence, profile matrix, binary formats, SQL definitions, provider behavior and known legacy gaps are in [Data and integrations](docs/discovery/DATA_AND_INTEGRATIONS.md). Feature ownership is in the [functional inventory](docs/discovery/FUNCTIONAL_INVENTORY.md); reconstruction order is in the [migration matrix](docs/discovery/MIGRATION_MATRIX.md). These documents distinguish source presence, historical reported tests and actual verification.

## Ownership and access

External game/account systems remain authoritative for native accounts, characters, online state, inventories, warehouses, guilds, currency and siege data. Website-owned content/configuration, orders, claims, ledgers, delivery queues, audits and template projects need separate persistence ownership. A shared physical SQL Server does not collapse these boundaries or permit a module to access another module's internal repository.

Use the versioned [contract catalog](contracts/v1/catalog.json) and [CONTRACTS.md](CONTRACTS.md) as the authoritative public interface definitions. `SchemaInspector` is metadata-only; `GameMutationGateway` must deny unsupported or unproven writes. Proposed future repository, codec, currency, serial allocator and unit-of-work ports must follow the contract evolution process rather than silently expanding those interfaces.

All production SQL belongs in approved infrastructure adapters. Queries use bound values and validated identifiers; each mutation keeps one owning connection/session and explicit transaction boundary. Preserve failure/rollback/cursor cleanup semantics and reject hidden nested transactions. Do not expose bindings, credentials, request bodies or raw exceptions in diagnostics.

## Three-part database topology & connection configuration

User specification (2026-10-18): the database architecture must support separation into three distinct logical or physical parts, configurable in any combination:

1. **Game Data** (MSSQL MU Online database, typically `MuOnline`):
   - Stores game characters, progression, stats, inventory blobs, vaults/warehouses, guilds, rosters, Castle Siege cycles, event states, and in-game currencies (`Character`, `warehouse`, `ExtWarehouse`, `Guild`, `GuildMember`, `MuCastle_DATA`, etc.).
2. **Account Data** (MSSQL master account database, typically `Me_MuOnline` or `MuOnline`):
   - Stores master player credentials, account state, online status, server connection tracking, and cash shop currency balances (`MEMB_INFO`, `MEMB_STAT`, `AccountCharacter`, `T_InGameShop_Point` / `CashShopData`, etc.).
3. **Web Data** (SQLite or MSSQL website database):
   - Stores website-owned configuration, website administrator accounts/sessions, announcement cache, market listings & escrow records, visual template projects & revisions (`zenda_template_head`, `zenda_template_revision`), event timetables, loot guides, ticket support, and immutable audit logs.

### Topology flexibility:
The system must be configurable across any database arrangement:
- **Unified single MSSQL database**: All three parts reside in a single MSSQL database (e.g., `MuOnline`).
- **Split Game + Account + Web**: Separate database for Game Data (`MuOnline`), separate database for Account Data (`Me_MuOnline`), and an independent MSSQL database (`ZendaWeb`) or local SQLite file (`var/zenda.sqlite`) for Web Data.
- **Shared Game/Account + Independent Web**: Game and Account Data share `MuOnline`, while Web Data uses SQLite or a separate MSSQL database.
- **Multi-realm architectures**: Multiple Game Data databases (e.g. `MuOnline_Realm1`, `MuOnline_Realm2`) sharing a centralized `Me_MuOnline` account database and a single website database.
- **Arbitrary configuration**: Connection configuration allows specifying individual connection inputs (driver, host, port, database, user, password) for each of the three targets, or binding two or all three to the same underlying connection.


## Compatibility evidence and activation

Game server profiles and Commerce Core presets are different catalogs. A registered name, discovered table, matching byte width or successful connection cannot enable a writer. Compatibility progresses through: unknown/unsupported → read-only mapping draft → structural validation → operation-specific non-production evidence → explicitly controlled activation. Revalidate schema/mapping/codec/allocator fingerprints when executing a protected operation.

Legacy source contains website profiles for Kayito, IGCN, X-Team, Custom, SPK and S21 variants, while commerce presets include Kayito97k, MuEmu, MUDevs, SPK Mobile and Custom. Default commerce write support exists only for Kayito97k, MuEmu and MUDevs; it still requires deployment evidence. Unknown item layouts remain opaque and read-only. Keep raw bytes losslessly, serials as decimal strings, exact container identity/dimensions, equipment offsets and supported option versions. Native identity/password formats and account/character ownership must be explicit.

Before any native mutation, the owning service must establish exactly one valid actor/owner/status record, known offline timing and required disconnect grace, explicit off-trade/off-level safeguards, native account/character usability, operation capability, and an approved concurrency lock. Missing, ambiguous or malformed evidence denies writes. Container updates compare-and-swap the whole expected blob and verify exactly one affected owner/state. Currency limits and debit source order are part of the contract.

SQL locks alone do not coordinate independent game-server writers. New item serial allocation requires an independently reviewed native concurrency contract tied to the exact routine definition; routine existence or a website-only application lock is insufficient. Freeze and retain transaction request identity, mapping/codec/catalog fingerprint, outcomes and delivery payloads. Replays must not reroll, allocate serials or debit again. Partial delivery/refund and consumed escrow require explicit reconciliation.

## Offline and integration verification

Routine verification uses owned synthetic fixtures and in-memory/mock adapters with no legacy source dependency, installed configuration, provider transport or live database. A future SQLite adapter may validate website-owned persistence but cannot certify MSSQL locking, drivers, native binary interpretation or synchronization.

MSSQL integration requires a separately designated non-production environment with synthetic accounts, explicit schema ownership, isolated setup/cleanup and approved operation scope. Validate the actual PHP/driver/SQL version, metadata mapping, collations/password modes, transaction/lock/cursor behavior, codecs, serial allocation, races, rollback and delivery recovery. Do not run a legacy installer, maintenance script or ordinary page to test connectivity; legacy reads can execute background writes. Actual SQL migrations, profile activation and deployment remain future controlled operations after the phase gate.

See [TESTING.md](TESTING.md) for verification levels and [MIGRATION.md](MIGRATION.md) for staged import/rollback policy. Offline verification must never be reported as production compatibility.

Website self-service password changes add optional private per-account history
to the version-1 file snapshot and `zenda_password_changes` to the website PDO
fixture schema. Credential replacement, reset invalidation and history share the
account lock/snapshot or one owning transaction with credential CAS. Only owned
SQLite fixtures were exercised; production DDL, MSSQL locking/driver behavior
and rollback tooling remain unverified. See docs/WEBSITE_PASSWORDS.md.
