# Kernel plugin configuration and migration receipts

Decisions 106-107 continue step 03; they do not complete it. This is internal
website control-plane storage, not MainAPI or an executable plugin host.
Read [approval storage](PLUGIN_APPROVAL_STORAGE.md) for the retained approval
boundary and [implementation plan](IMPLEMENTATION_PLAN.md) for the ordered work.

## Ownership and authority

`MetadataComposition` supplies a private lazy website connection. The checked
`Kernel/Plugins` owner contains Domain models, Application ports/services and
Infrastructure SQL. No Admin/module private implementation or optional external
DatabaseType is imported. Frozen V1-V5 and approval schema/document v1 remain
unchanged. `ApprovalCommand` adds only a private `JournalCommand` interface;
`PdoApprovalStore` retains its existing methods and delegates to the shared
transport. No new public SDK signature is frozen.

Before connecting, `MetadataService` checks the separate website administrator
resource `admin`: `plugins.inspect`, `plugins.provision`, `plugins.configure`
or `plugins.migrate`. Configure and migrate permissions are distinct; none is
automatically granted to existing roles. Commands validate before connecting;
factory/type/family errors refuse without private exception chains. This service
is not yet exposed through HTTP, an operator CLI or scoped plugin configuration
ports. Supplying an actor name alone is never administrator authentication.

## Configuration

The independent configuration head stores sorted plugin IDs and, per plugin,
`schemaVersion`, `schemaDigest` and a sorted scalar `values` map. `replace`
replaces the whole map under the aggregate expected revision. An existing schema
identity cannot change through replace; it requires an explicit future migration.
There is no default merge, inferred schema acceptance or missing-table fallback.
Schema identity binding is not schema-definition evaluation: that SDK boundary
is still pending.

This first internal version accepts at most 32 keys per plugin: lowercase
identifier keys up to 48 bytes; UTF-8 strings up to 4,096 bytes; signed 32-bit
integers; booleans; null. Arrays, objects, floats and executable payload types
are unsupported. Empty values use `[]` in the PHP/JSON representation. Store
deployment credentials in the private connection boundary, never these ordinary
configuration records. Inspection is administrator-only until scoped ports exist.

Unicode is preserved through canonical JSON escapes, keeping the SQL wire ASCII.
Each head is bounded to 64 KiB and 128 plugin records, with at most 10,000
transitions. Capacity exhaustion refuses; no automatic pruning removes records.

## Migration attempt receipts

This ledger records **operator assertions only**. It contains no SQL body,
executable callback, installed-version proof or mechanism for granting runtime
authority. `start` records exact ID/version/package fingerprint/plan digest,
attempt identity unique within that plugin, actor and time. A pending attempt blocks another start.
After investigating an interruption, an authorized operator may `record` the
matching pending attempt as `interrupted`, `reported-failure` or
`reported-success`, supplying an evidence digest. Every receipt permanently
remains `verification: unverified`; reported success cannot enable a plugin.

Closed receipts cannot be edited or their attempt IDs reused. A recovery attempt
appends a new record; earlier attempts remain in the head with their evidence
digests, actors and timestamps. Chronology is checked. The complete ledger holds
at most 128 attempts across all plugins and the same 64-KiB/10,000-transition
bounds. It cannot automatically infer whether interrupted DDL committed.

Installed-version state and a reviewed website-only migration executor must
still define plan approval, verified postconditions, interruption reconciliation,
configuration-schema migration and the relationship to exact approved packages.
Neither schema discovery nor these receipts authorize native game writes.

## Read-only migration plan review

Decision 107 adds `MigrationPlan`, `MigrationReviewService` and private Bootstrap
`MigrationReviewComposition`. The service requires `plugins.migrate` on separate
administrator resource `admin` before discovery or connection. It validates the
plan, scans with existing data-only Discovery, reads the approval ledger, and
scans again before returning a packet. The exact approved ID/version/fingerprint
must match and the plugin must be disabled. Missing/changed packages, unmatched
resource hashes and any discovery diagnostic refuse. This conservatively blocks
review while the inventory has diagnostics; it does not change website boot.

An internal plan has exact keys `planVersion: 1`, `id`, `version`, `fingerprint`,
`target: website`, `fromRevision` and ordered `steps`. Steps describe the complete
retained history, numbered consecutively from 1, with at most 32 steps in this
first internal format. The claimed source revision is between zero and the last
reviewed revision. Historical steps stay reviewed; only revisions above the
verified source could later be scheduled. Each step contains exact
`revision`, `resource`, `sha256`, `beforeDigest`, `afterDigest`; adjacent schema
digests must chain and each transition changes the expected digest. Resources
are unique flat lowercase `migrations/<name>.json|sql|php` inventory paths with
a 1-64 character basename. Other layouts currently refuse. Every file under the
case-folded migrations prefix must be listed: omitting migrations cannot produce
a migration-free plan. Empty history requires source revision zero and no such resources.

Key order is normalized before computing the plan SHA-256. The packet contains
the normalized plan/digest and the approval snapshot revision, with permanent
`verification: unverified` and `executable: false`. It writes no journal,
creates no installed-version record, and includes/parses/executes no migration
resource. JSON/SQL/PHP extensions identify reviewed bytes only; their content
format and executable safety are not accepted or certified here. Declaring a
website target cannot prove that a script stays within website-owned objects.

The expected schema digests are assertions; decisions 108/110 supply the bounded
logical canonicalizer and a private [physical MSSQL inspector](PLUGIN_SCHEMA_INSPECTION.md).
The review packet does not invoke that inspector or prove installed source state.
The packet is neither durable operator approval nor an execution
token; the future executor must freshly recheck package presence, approval,
installed source state and plan identity under its own concurrency boundary.
Resource ownership, explicit migration consent, backup/rehearsal evidence,
postcondition verification, atomic installed-state commit and interruption
reconciliation remain required. Existing operator receipts remain unverified.

## Declarative website-table operations (execution not implemented)

`MigrationOperationSet` defines a bounded JSON-only format for a future executor.
It accepts canonical compact ASCII JSON of at most 16 KiB and 16 operations,
with exact fields and version 1; byte round-tripping rejects duplicate keys and
ambiguous encodings.
The only operations are `createTable` and `addNullableColumn`; the latter can
only add nullable columns. Tables are restricted to `dbo.zenda_plugin_<plugin-id>_<suffix>`,
where dots in the canonical plugin ID are encoded as double underscores and the
suffix is a lowercase identifier. Each table has 1–64 columns and a 1–4-column
primary key. Column names are lowercase identifiers; supported types are `int`,
`bigint`, `bit`, bounded `varchar`/`nvarchar`, and bounded `varbinary`. Defaults,
identity/computed columns, foreign keys, arbitrary constraints/indexes, DML, raw
SQL, PHP, and non-`dbo` schemas are outside this format. Unsupported resource
extensions remain inert in review and must be refused by any future executor.

`MigrationSchemaFootprint` canonicalizes the logical form (owned schema/table,
column name/type/length/nullability and primary-key order), sorts tables/columns,
and hashes normalized JSON. Applying a parsed operation set to an explicitly
supplied source footprint checks the before and after digests. This is a pure
domain model, **not** a database introspector, installed-state record, migration
runner, or proof that SQL Server has the represented physical schema. Decision
110's read-only MSSQL inspector validates the supported physical profile,
including extra indexes, constraints, defaults, triggers and ownership.
Its observation grants no execution or installed-state authority; those remain pending.

## SQL and transaction behavior

Reviewed `database/mssql/013-plugin-metadata.sql` explicitly creates four fixed
website-owned tables, independently of DDL 012, in one transaction. It seeds no
heads and refuses existing targets. Apply it only to a designated website target
through the future guarded installation flow. Constructors, discovery and reads
perform no DDL. SQLite helpers exist only in tests; other PDO drivers refuse.

`PdoJournalStore` has a closed allowlist of three kernel record families:
approval, configuration and migration. These are kernel storage categories,
not a plugin registry requiring edits for every plugin. Callers cannot provide
table identifiers or cross family bindings/command types. Each head and audit
are independent. The existing bounded ODBC ASCII/hex profile, owned transaction,
table serialization, CAS, actor-scoped retry key, checked row/server acknowledgement,
full readback and checked commit/replay release are shared. Every read validates
the full contiguous digest-linked minimal command audit. Settings values never
appear in that audit; migration receipt details remain in the retained head.

Provisioner grants are SELECT/INSERT head and SELECT audit. Runtime grants are
SELECT/UPDATE head and SELECT/INSERT audit, without DELETE or audit UPDATE.
Replays return the original revision and preserve later state. A lost commit
acknowledgement reports failure until an exact retry resolves the durable result.
No folder-removal cleanup or file fallback exists.

## Verification and remaining work

`tests/plugin-metadata.php` covers authority before connection, wrong bindings,
Unicode/scalar bounds, schema conflicts, canonical/corrupt state, replay/CAS,
receipt immutability/recovery, audit rollback and four two-process SQLite races.
The existing approval suite continues to exercise shared acknowledgement/fault
paths. Every non-fast verification scope runs all three kernel suites.

The separately opt-in owned SQL suite applies DDL 013 only inside its freshly
marked lab. It verifies restricted initializer/CAS/retry races, runtime grants,
audit-denial and trigger rollback, ambiguous real commits, retained interruption
receipts, independent approvals and Unicode settings beyond 32 KiB. All existing
41 SQL HTTP cases still cover administrator/core/operational routes; they are
not plugin metadata HTTP tests. Lab certificate trust is not strict TLS proof.

Next: finish the verified installed-migration boundary and guarded operator
provisioning before calling step 03 complete. Step 04 then implements executable
MainAPI and live HTTP add/remove/readd tests. No executable plugin, game write,
payment mutation, authentic editor or deployment integration was enabled here.

`tests/plugin-migration-review.php` covers plan bounds/chains/resource completeness,
separate authority before I/O, approval/fingerprint/version drift, enabled-plugin
denial, redacted failures and removal during review. An owned filesystem fixture
uses real discovery plus the SQLite approval adapter, with throwing PHP resources
that remain inert. Every non-fast scope runs this suite. No SQL adapter or DDL
changed in decision 107; its checks do not constitute new MSSQL execution evidence.
