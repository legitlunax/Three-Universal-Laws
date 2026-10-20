# Separate website administrator storage

AdminCP uses an independently provisioned website administrator. Matching player
names, player roles and native GM flags confer no authority. This core/free slice
does not bind native MU authentication or activate premium features.

PdoCredentialStore supports sqlsrv, owned SQLite and a verified SQL Server ODBC
profile with exception mode. Generic ODBC is refused. Identity owns its bounded
ASCII VARCHAR probe; it imports no Admin internals and claims no Unicode/game
storage compatibility. Actual driver identity, explicit ODBC UTF-8 policy,
SQL Server 2016+ and exact 100-byte ASCII parameter/result semantics are checked.
Constructors/requests execute no DDL. DDL 009 remains an explicit installation
input with runtime SELECT/INSERT only. Decision 101 adds owned local PDO_ODBC
evidence; decision 102 adds scoped administrator/settings HTTP evidence via an
explicit injected lab connection. Trusted application TLS, full default-factory
HTTP composition and deployment remain unverified.

## Read and initialization boundary

SQL load returns null only for an empty installed table. Missing schema, driver
errors, corrupt typed values or multiple administrator rows are redacted storage
failures. Never silently select the first administrator or fall back to files.
Strict validation retains the original five-field v1 credential, deterministic
actor identity, bcrypt cost/encoding policy and 64-hex secret. SQL ID/version/clocks
accept supported canonical integer driver representations without coercing unknown
values. Reads return no driver exception chain. The file prototype retains its
older null-on-invalid-load denial behavior, with no repair or automatic overwrite.

Create and direct import share fixed-table PdoAdminCredentialDocument. It refuses
caller transactions, owns begin/lock/empty recheck/insert/commit and requires one
inserted row, exact validated readback and a checked commit. Typed parameter
bindings preserve the bounded ASCII credential fields; raw passwords never reach
this table. SQL Server uses UPDLOCK/HOLDLOCK on its empty-table check. SQLite
acquires its writer boundary before the check with a zero-row INSERT SELECT, so
no UPDATE/DELETE/DDL permission is needed. This is an application invariant over
cooperating writers: DDL 009 itself does not enforce a singleton row. An externally
created second administrator makes reads unavailable.

Failed inserts, zero-row or trigger-altered inserts and false commits roll back;
rollback exceptions are redacted without a private chain. Ambiguous committed
initialization retains one complete administrator. Reload the target to resolve
that outcome. Retry refuses the nonempty table rather than replacing credentials.
Provisioning has no durable request key, same-commit audit or rotation workflow.
The existing administrator session model is unchanged; global revocation/rotation
and a production provisioning/rollback rehearsal remain separate missing work.

## Source-only migration

CredentialStorageImport reads only the configured existing sandbox-admin.json.
Its directory/path must resolve canonically; linked/unsafe/nonregular files refuse.
The reader opens read-only, takes a shared lock on that file, verifies its open
file identity and bounds the complete read to 16 KiB. FileCredentialStore takes
an exclusive lock while exclusively creating/writing/flushing that same file.
No lock file, source directory, defaults or repair is created by discovery.
The file writer never replaces an existing credential; operators must still freeze
manual source replacement/edits between dry run and cutover. Locks do not prevent
an uncooperative external writer or establish certified Windows ACL protection.

Validate exact version/types/actor/hash/secret and reject unknown metadata instead
of silently dropping it. Valid reordered JSON yields one canonical five-field
fingerprint; source bytes, credential hash and secret are preserved. Direct SQL
import validates too, rather than trusting only the CLI reader.

scripts/import-admin-credentials.php remains a source-only redacted dry run by
default. Apply still requires owned-nonproduction scope, MSSQL backend, private
connection inputs and the reviewed matching SHA-256. Re-run dry run after this
stricter validation/canonicalization. No production apply, DDL or actual SQL Server
connection occurs in the source-only dry run. The separate opt-in fixture below
does connect and apply synthetic credentials. Never reopen stale file credentials
as SQL fallback.

## Evidence and limitations

tests/admin-storage.php has 79 assertions for strict source/direct validation,
source preservation, corruption/ambiguity denial, unsupported drivers, nested/
failed/false/ambiguous transactions, INSERT-only initialization, guarded CLI,
source-lock contention, profile rejection, altered-row readback, rollback error
redaction and barrier races between different-name create/import workers.
tests/admin-storage-http.php adds ten loopback cases with SQLite administrator
storage plus conflicting file/player credentials, CSRF/Origin, private errors and
missing/corrupt/multiple-row denial. These ordinary fixtures do not prove actual
SQL Server behavior or deployment isolation. Existing identity assertions remain.

The explicit scripts/test-mssql-odbc.ps1 -RunOwnedFixture suite adds 32 real
administrator assertions (103 storage cases with Admin journals, before the 41
HTTP cases added by 102). A fresh ownership-marked
database receives reviewed DDL 009, exact imports, corrupt/multiple-row denial,
suppressed/altered/failed inserts, real SELECT/INSERT-only grants and permission
denial, independent lock timeouts, three two-process different-name initialization
races and lost acknowledgement after an actual commit. The fixture is removed.
Only this isolated lab trusts its test certificate; no existing MU/account data,
legacy runtime, deployment certificate or machine trust is changed. Decision 102
now verifies administrator login, realm denial, sessions, SQL corruption/missing
schema and throttling over loopback HTTP. The fixed router uses SELECT-only
administrator grants; file player fixtures provide the same-name realm test.
Full default-factory SQL HTTP, trusted TLS, administrator provisioning audit/
rotation and recovery remain pending. See MSSQL_LAB.md and MSSQL_HTTP.md.
