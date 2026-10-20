# Local SQL Server lab inputs and transport checks

Decision 110 adds read-only plugin physical-schema inspection cases, still only
inside the newly created marked database. A separate dbo operator connection
checks actual scalar metadata, namespace isolation, hidden-metadata refusal,
unsupported shapes/incoming dependencies, retained LOB storage after dropping
a MAX column, expected-footprint changes and schema-lock contention. Order and
the focused Singularity handler are exercised privately; no plugin HTTP route,
migration executor or installed-version authority is inferred. The lab performs
its own synthetic DDL; the inspector uses catalog SELECTs and rollback only.
See [physical profile and limits](PLUGIN_SCHEMA_INSPECTION.md). Actual totals
and cleanup are recorded in STATE; existing 41 HTTP cases retain their scope.

Decision 105 extends the opt-in fixture to kernel plugin approvals using DDL 012
only in its newly created marked database. The new owner has its own ASCII
VARCHAR/hex profile and explicit server write acknowledgements, with restricted
provision/runtime grants, races, rollback/lock/corruption/trigger/ambiguous-commit
and long-document cases. SQL approval HTTP, installed configuration/migration
state and trusted deployment TLS are not verified by these tests. The existing
41 HTTP cases remain administrator/core/operational settings only. See
PLUGIN_APPROVAL_STORAGE.md; exact totals and cleanup evidence are in STATE.md.

Decision 103 extends the owned suite to website event/loot guides via a separately
owned GameData SQL/Unicode profile and eight-MiB memory-stream/hex boundary. The
suite now passes 187 assertions: 144 prior storage/administrator HTTP cases plus
43 guide cases, including real process races/grants/locks, complete guide history
above sixteen KiB and the exact eight-MiB wire limit. It additionally applies
reviewed DDL 003/004 only in its fresh marked database. Guide-specific SQL HTTP,
strict application TLS and whole-site cutover remain pending. See GUIDE_ODBC.md.

Decision 099 adds an explicit Admin ODBC binding without changing V1-V5 contracts,
components, stored JSON, schema versions or replay fingerprints. Set the private
bootstrap input ZENDA_SETTINGS_DRIVER=odbc to select it; an unset input retains
sqlsrv. Empty/unknown drivers refuse, and a failed selected driver never falls
back to another transport. Decision 101 additionally admits Identity's separate
administrator store using its own bounded ASCII VARCHAR profile; other stores
and complete HTTP/database cutover remain pending. Decision 102 adds the scoped
administrator/settings HTTP fixture described in MSSQL_HTTP.md; it does not
change the strict application factory.

Admin's connection parameters fix Microsoft ODBC Driver 18, encryption and
TrustServerCertificate=No. SQL user/password values use ODBC brace escaping and
are not passed again to PHP's legacy UID/PWD appending path. ODBC authentication
inputs are explicitly bounded printable ASCII; existing sqlsrv Unicode credential
handling stays separate. Missing credentials mean Windows integrated authentication,
never a stored credential fallback. The factory never reads the DPAPI lab file.
ODBC pooling must be disabled at PHP process startup (for example,
-d pdo_odbc.connection_pooling=off); no global php.ini or editor setting is changed.
The ODBC session sets a five-second lock timeout. PHP 7.4 PDO_ODBC does not expose
sqlsrv's bounded login/general-query timeout attributes; those waits are not
certified equivalent. Preserve strict certificate validation for application use.

The first actual write tests exposed two problems hidden by the narrow initial
probe: implicit UTF-8 parameters can supply the wrong byte count, and long wide
results can be corrupted by PHP 7.4's retrieval buffer path. The owned Admin
Infrastructure binding therefore sends each declared NVARCHAR parameter as an
explicit bounded UTF-16LE LOB stream. Integer and ASCII parameters keep their
own types. Snapshot reads project NVARCHAR through SQL Server's style-2 hex
VARCHAR representation and validate/decode the exact UTF-16LE bytes, including
surrogate pairs, without trimming or replacement. Size, digest and full typed
journal checks still run after decoding. See the primary
[PHP 7.4 parameter/result implementation](https://github.com/php/php-src/blob/PHP-7.4/ext/pdo_odbc/odbc_stmt.c).
This is a declared Admin transport strategy, not driver impersonation or native
inventory/binary compatibility. Profile probes require actual ODBC identity,
exception mode, explicit UTF-8 policy, SQL Server 2016+ and exact mixed-language
parameter/server-result semantics before the journal accepts the connection.

Run the separately opt-in owned SQL suite:

```powershell
./scripts/test-mssql-odbc.ps1                 # dry plan, no connection
./scripts/test-mssql-odbc.ps1 -RunOwnedFixture
```

The launcher validates the private local test designation and credential ACLs,
decrypts in memory and passes credentials through stdin to an isolated PHP
process. The fixture alone explicitly trusts the local test certificate; the
application factory retains strict trust and returns a redacted failure on this
host. The suite creates a random ZendaNextLab_<24 hex> database that must not
already exist, adds/verifies its ownership marker, installs reviewed DDL 001/002/
003/004/009, and tests only synthetic settings, guides and separate administrator credentials.
Cleanup checks creation ownership and the
live marker before removing that same database. No existing MU/account/ZendaWeb
records, server configuration or legacy runtime are touched.

The real suite covers bounded Unicode/LOB values, checked writes, replay and
stale keys, corrupt digests, nested transaction denial, suppressed/failed writes,
two-process initializer/save races, real shared/exclusive table lock contention,
runtime grants/permission-denied rollback and lost acknowledgement after a real
commit followed by replay. Its report is reports/mssql-odbc-integration.json;
assertions are separate from the offline runner. The ordinary
tests/mssql-connection.php suite covers input/DSN guards, wire validation and
negative profile cases without needing SQL Server. Identity's 32 additional SQL
cases cover exact credential preservation, missing/corrupt/multiple-row refusal,
trigger-altered/suppressed/failed insert rollback, SELECT/INSERT-only grants,
permission denial, read/write timeouts, three different-name create/import races
and ambiguous committed setup without overwrite. Decision 102 adds 41 actual
HTTP cases for administrator login and core/operational settings, bringing the
real suite to 144 assertions. Its fixed loopback router injects verified stores;
private inputs are child-process environment only, and each connection verifies
the ownership marker and impersonates the restricted database runtime user.
The server/private root are removed before database cleanup. Identity's ordinary
administrator suite keeps its adapter independent of Admin internals. Strict TLS,
other module stores, whole-site HTTP/default-factory composition, backup/restore
and deployment remain unverified. See MSSQL_HTTP.md for scope and exact limits.

## Initial metadata-only probe

The user confirmed this local SQL Server is for testing on 2026-10-19. The default
MSSQLSERVER service is running; localhost reaches engine 17.0.1000.7, Express
Edition (64-bit). The owned manual transport probe lists database names/state
only and evaluates synthetic string expressions. It does not inspect player rows,
execute legacy PHP, create tables/databases, initialize settings or modify game
data. An existing database named ZendaWeb is not proof of website ownership or an
empty migration target. Never automatically select or seed it.

The supplied lab credential is stored outside the repository and webroots at:

`%LOCALAPPDATA%/ZendaMod-Next/private-connections/mssql-local-test.dpapi.json`

Its password is encrypted with Windows DPAPI for the current Windows account;
the containing directory has protected access rules for that account and SYSTEM.
The record designates localhost and owned-nonproduction. Do not print its contents,
copy the password into source, ordinary website settings, VS Code settings or
reports, or automatically load it as runtime configuration. Administrator lab SQL
access is separate from website administrator identity and future deployment
principals. Source-controlled configuration remains free of connection secrets.

PHP 7.4.33 already exposes mysql/odbc/sqlite through PDO. pdo_sqlsrv is absent;
that does not mean SQL Server is unreachable. Both ODBC Driver 17 and 18 and
sqlcmd are installed. VS Code has Microsoft's MSSQL extension and SQLTools with
its MSSQL driver. No extension, global DSN, php.ini or editor configuration was
changed; the owned PHP diagnostic uses a DSN-less ODBC connection.

The ignored reports/mssql-transport-probe.php and its JSON report record manual
evidence, not an application adapter or automated integration suite. Credentials
are decrypted in memory by PowerShell and sent through the PHP process's stdin,
never written as plaintext connection inputs. These artifacts are disposable;
STATE.md remains the progress checkpoint.

The probe first uses encryption with certificate validation. The local certificate
chain is untrusted (Windows HRESULT 0x80090325). An explicitly separate encrypted
lab diagnostic permits the self-signed certificate to read metadata. This is not
TLS identity validation. MssqlSettingsConnection retains Encrypt=1 and
TrustServerCertificate=0; no production/default verification gate was relaxed.

Default PDO_ODBC text handling is insufficient: a byte-for-byte parameter/result
round trip appeared successful while the server stored a different code point.
The stronger probe checks UNICODE() of the parameter and server-generated Unicode.
PDO::ODBC_ATTR_ASSUME_UTF8=true preserves the tested parameter code point and
server-generated NVARCHAR(MAX) results (accented, Greek and CJK text). Direct
NCHAR results still fail the same comparison; normalize text through an explicitly
tested NVARCHAR projection. See the [PHP PDO_ODBC reference](https://www.php.net/manual/en/ref.pdo-odbc.php).
These expressions are limited encoding evidence, not full binary/LOB/SQL adapter
conformance or arbitrary MU schema compatibility.

At the initial probe checkpoint, prepared stores accepted only sqlite/sqlsrv.
Decision 099 now adds Admin core/operational ODBC support and the separately owned
SQL suite above. Other module stores still require deliberate transport work;
none may infer native compatibility or cutover from the initial successful SELECT.
