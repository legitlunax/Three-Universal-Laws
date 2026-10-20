# Website guide SQL Server ODBC boundary

Decision 103 adds verified PDO_ODBC to GameData's fixed website event/loot
document adapters. It does not read or modify native MU scheduling, drops or
account data. Existing ports, v1 documents, command hashes, generated loot IDs,
eight-MiB limit and V1-V5 freezes remain unchanged. Bootstrap keeps its existing
injection/factory signatures. No Admin internal helper is imported.

MssqlGuideOdbc belongs to GameData Infrastructure. It requires actual ODBC,
exception mode, explicit UTF-8 handling, SQL Server 2016+ and exact mixed-language
parameter/server-result probes. Declared text uses bounded UTF-16LE streams in
memory; results use validated uppercase SQL style-2 hex, strict surrogate checks
and lossless decoding. Byte iteration avoids a large PHP integer array. Unlike
Admin's smaller helper, this transport accepts up to eight MiB UTF-8, sixteen MiB
UTF-16 bytes and thirty-two MiB hex. Oversize, malformed text or unknown scalar
bindings refuse. Deployment memory/concurrency sizing remains unverified.

PdoGuideDocument retains its fixed SQLite/sqlsrv paths and adds the SQL Server
dialect for verified ODBC, including dbo names and UPDLOCK/HOLDLOCK. Both guide
families now check writer acquisition, execute, inserted-row count, exact complete
readback and commit. Replay checks its rollback acknowledgement. Cleanup failures
are private errors without chained driver details. A failed rollback may leave an
unusable connection: discard it rather than continuing an application workflow.
No success result follows an unacknowledged boundary. Reads reject ambiguous rows
and noncanonical schema metadata. Constructors/requests never seed or install.

Runtime remains SELECT/UPDATE only. An explicit provisioning principal needs
SELECT/INSERT for initialization/readback; schema creation is separate. Validated
full-history initialization retains its original revision and replay data. Core,
operational, events and loot still have independent transactions; no cross-record
snapshot or native game synchronization is implied.

## Evidence

tests/event-settings.php has 114 assertions and tests/loot-settings.php has 80.
They include 50 shared transaction-failure cases and 34 wire/profile guards,
alongside existing capacity, source/import, process and consumer cases. Synthetic
profile tests are not SQL Server evidence. The ordinary HTTP/calendar/public/
staff suites still use owned SQLite and retain their existing authorization and
rendering contracts.

The explicit scripts/test-mssql-odbc.ps1 -RunOwnedFixture suite additionally
executes reviewed DDL 003/004 in the same newly created ownership-marked website
database. Real cases cover the exact eight-MiB wire boundary, Unicode/NUL/long
values, complete typed guide history beyond sixteen KiB, initializer/update process
races, generated IDs, exact/historical replay, caller-transaction denial, corrupt
state, trigger-altered readback rollback, actual SELECT/UPDATE-only grants,
permission denial, lock timeouts and lost acknowledgement after real commits.
The owned database is removed. No existing MU/account/ZendaWeb database is selected.

The lab alone trusts its local certificate; application TLS remains strict.
Guide-specific SQL HTTP/calendar integration, strict default-factory deployment,
backup/restore, native event/drop execution and memory/load sizing remain pending.
The existing 41 SQL HTTP cases still cover administrator/core/operational settings
only. See MSSQL_LAB.md and MSSQL_HTTP.md.
