# Read-only plugin website schema inspection

Decision 110 continues plugin implementation step 03 with a private MSSQL
catalog inspector. It compares physical website tables with decision 108's
canonical `MigrationSchemaFootprint`; it installs nothing and produces no
installed-version record or migration authority.

## Boundaries and composition

`SchemaInspectionService` checks separate administrator `plugins.migrate`
authority on `admin` before invoking its lazy connection factory. Bootstrap's
`SchemaInspectionComposition` exposes one private Order capability,
`plugins.schema.inspect@1.0.0`, through the focused `SchemaFootprintRead`
Singularity handler. Its empty input cannot supply an actor, connection,
table name or expected digest. Trusted composition supplies the expected
footprint and server context supplies identity. Chaos can describe the read
through the existing catalog, but cannot grant authority or execute a migration.

Kernel/Apis and Kernel/Plugins retain their separate private owners; Bootstrap
bridges them. There is no public SDK, HTTP endpoint, plugin registration or
optional game DatabaseType dependency. Frozen V1-V5 contracts, approval/config/
receipt schemas and production DDL remain unchanged.

Success returns only profile, plugin ID, digest, table count, `matches: true`
and `executable: false`. SQL/transport/factory/mismatch failures are redacted
without private exception chains. Permission denial occurs before I/O.

## Physical profile `mssql-simple-v1`

The implemented transport is PDO_ODBC in exception mode, on SQL Server major
versions 13-17 and EngineEdition 2, 3 or 4. Unknown versions and cloud editions
refuse. Actual local SQL evidence is limited to version 17 Express with ODBC
Driver 18; the other accepted versions are not separately integration-tested.

The connection must be an explicit private operator connection executing as
`dbo` (principal ID 1). SQL Server metadata visibility can hide individual
objects through permissions, including object/column DENY; database
VIEW DEFINITION alone is insufficient for this completeness profile. The
inspector neither grants privileges nor elevates the ordinary website runtime.
Future guarded operator provisioning must manage this separate boundary.
See Microsoft's [metadata visibility rules](https://learn.microsoft.com/en-us/sql/relational-databases/security/metadata-visibility-configuration?view=sql-server-ver17).

The bounded namespace is `zenda_plugin_<id with dots encoded as __>_`.
Names are searched case-insensitively across schemas so wrong-case or non-dbo
objects refuse rather than appearing absent. A suffix beginning with `_` is
reserved for descendant plugin IDs and excluded from the parent namespace.
Actual table/column names must match the exact lowercase logical model.
Identifiers travel as UTF-16LE style-2 hex and must decode to bounded ASCII;
numeric metadata must use canonical integer encodings.

Accepted shape:

- At most 32 dbo tables, 64 columns each and 256 columns total; no explicit
  object ownership override, replication, temporal, graph, ledger, external,
  memory-optimized, FileTable, FILESTREAM or archive features.
- Only `int`, `bigint`, `bit`, bounded `varchar`/`nvarchar` and `varbinary` with
  exact widths/nullability. Text uses `Latin1_General_100_BIN2`; variable-length
  types require ANSI padding. Identity, computed, defaults, rules, alias types,
  sparse/hidden/generated/encrypted/masked and unsupported columns refuse.
- Exactly one clustered ascending primary key, with 1-4 nonnullable columns,
  default checked index options, PRIMARY filegroup and one uncompressed partition.
  Extra indexes, constraints, triggers and child objects refuse.
- Incoming foreign keys or expression dependencies, row security predicates,
  full-text indexes, explicit extra statistics and object/column extended
  properties refuse. Automatic statistics and the primary-key constraint's
  generated name do not participate in the logical digest.

Catalog flags follow Microsoft's [tables](https://learn.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?view=sql-server-ver17),
[columns](https://learn.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?view=sql-server-ver17)
and [indexes](https://learn.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql?view=sql-server-ver17)
references. Version-dependent flags are gated before their queries.

## Transaction and observation limits

The inspector refuses an existing caller transaction without altering it. It
owns a read transaction, runs fixed prepared catalog SELECTs twice, requires
identical ordered raw observations, canonicalizes the supported shape and
checks rollback acknowledgement. It never commits or reads native/player rows.
Queries have row/byte bounds. A failed instance discards its connection and
cannot be reused; cleanup failures cannot become success.

Two matching scans detect observable drift; they do not provide an atomic DDL
snapshot, prevent an ABA change or reserve the schema after return. The digest
describes logical schema only, not row contents, data preservation, permission
policy, global database/server triggers, package presence or installed history.
An empty observed namespace is not proof that a package was never installed.

The future executor must bind the physical profile version, recheck fresh
package/approval/source state and schema under its own concurrency boundary,
verify postconditions, and commit installed history/audit atomically with
interruption and ambiguous-commit recovery. This read cannot bypass those gates.

## Verification and next step

`tests/plugin-schema-inspection.php` covers strict decoding, bounds, unsupported
shapes, canonical hashes, drift, transport/transaction faults, private failures,
authority before connection and Chaos/Order/Singularity composition. Planning
opens no storage and respects the permission-filtered catalog. Its scripted PDO
double is transport fault evidence, not SQL Server conformance evidence.

`tests/lib/MssqlPluginSchemaCases.php` runs separately through the opt-in
[owned SQL lab](MSSQL_LAB.md), only in a newly created marked website database.
It exercises actual scalar metadata, namespace isolation, restricted visibility,
physical alterations/dependencies, expected-digest changes and DDL lock timeout.
Existing loopback SQL administrator/settings cases remain independent; these
are not plugin HTTP lifecycle tests. Read [STATE](../STATE.md) for actual results.

Next: verified installed-source state/history, approval-bound execution/recovery
and guarded provisioning, before closing step 03. Live folder-plugin
add/remove/readd and worker surfaces remain later mandatory exit checks.
