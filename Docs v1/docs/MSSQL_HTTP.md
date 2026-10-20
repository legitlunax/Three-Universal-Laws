# Owned SQL administrator HTTP evidence

Decision 102 verifies the existing Bootstrap Application and module services
through actual loopback HTTP with PDO_ODBC SQL Server stores. No production code,
public contract, schema, session policy or module activation changes are needed.
The explicit test composition injects the verified core/operational settings and
separate administrator stores. This is limited integration evidence, not a deployed
CMS, complete SQL cutover or native MU account integration.

Run `scripts/test-mssql-odbc.ps1 -RunOwnedFixture` as described in MSSQL_LAB.md.
The opt-in suite now has 144 assertions: the earlier 103 storage/transport cases
and 41 HTTP cases. An absent server or failed prerequisite is a failure, never
a skipped passing integration. Ordinary verification remains offline and does
not start this SQL fixture automatically.

## Fixture boundary

The lab creates a fresh random ownership-marked database and applies reviewed DDL
001/002/009. HTTP cases replace only synthetic records from that same run. The
fixed tests/fixtures/mssql-http/router.php accepts only cli-server, explicit opt-in,
127.0.0.1 and its exact bound Host. It handles a small route allowlist and never
serves source paths or provides a database/debug API. The public entry point has
no selector for this router.

The child receives private connection inputs in its process environment from
the parent lab's memory. No connection password is written to files, forms,
reports or command arguments; the application factory never reads these fixture
inputs. The attach helper verifies the live ownership marker. HTTP cannot create
a database. Each request impersonates a fixed database-only lab_http_runtime user
and verifies USER_NAME before constructing the real adapters. Runtime grants are
SELECT on administrator credentials, SELECT/UPDATE on the two heads and
SELECT/INSERT on their command histories; no HTTP provisioning/DDL is granted.

Sessions and persistent limiters use a fresh private directory outside webroots.
Conflicting administrator/settings files are intentional ignored inputs, checked
unchanged. The same-name player with admin role uses explicitly injected file
fixtures to test realm denial; this provides no SQL player-account evidence.
Servers stop and the strictly identified private root is removed, then the lab
checks its creation marker before removing its database. Older rejected cleanup
residues are never reused or retried.

## HTTP cases and limits

The 41 cases cover private headers, method/source-route denial, administrator
authentication and conflicting file credentials, Origin/CSRF, session/token
rotation, anonymous/player denial, SQL Unicode form round trips and escaping,
independent core/operational revisions, exact and historical replay, stale/changed
commands, malformed fields, actual permission-denied rollback and retry,
maintenance, disabled shops, corrupt configuration/administrator state, logout,
persistent settings/login throttling, missing administrator schema, no DDL/file
fallback/detached success audit, and credential-free server logs.

`/health` remains constant liveness even without administrator schema. It is not
database readiness. Maintenance remains active after logout: the anonymous core
editor returns 503 while the operational editor redirects to administrator login.
Core/operational records still have independent transactions, not one atomic
cross-record snapshot. HTTP history is minimal command metadata, not a backup.

The fixture alone explicitly trusts the local test certificate. SQL strict TLS
through MssqlSettingsConnection still fails locally; browser transport is loopback
HTTP. Full default-factory composition, other stores/routes, actual SQL player
storage, native game operations, backups/restore, visual/browser regression and
deployment remain unverified. No machine certificate/trust, SQL service, php.ini,
editor settings, existing MU/account/ZendaWeb data or legacy runtime is changed.
The report records applicationHttpIntegration=true only for the stated httpScope,
with wholeSiteSqlCutover=false and strictTlsValidated=false on this host.
