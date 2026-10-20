# Administration boundary

Read root STATE.md/AGENTS.md and docs/WEBSITE_CONFIGURATION.md first.
For news/runtime policy also read docs/OPERATIONAL_CONFIGURATION.md. Version-2
OperationalSettingsStore and core version-1 settings use the same fixed-table
transaction journal but independent revisions. Do not mutate the core schema or
allow environment/file fallback in database mode. Protected runtime values may
not enable native operations or weaken request/preview/upload safety.
All authoritative website configuration belongs in website-owned MSSQL tables.
File settings are an old offline prototype, not a fallback for database failures.
Do not read/copy legacy installed settings or game database credentials.

PdoSettingsStore is an internal RevisionedSettingsStore implementation. Missing,
corrupt or unsupported state must fail closed; constructors/HTTP never install
schema or initialize defaults. Database saves require expected revision, a
server-authorized administrator, validated retry key and atomic minimal history.
Reject nested transactions; preserve later settings on historical retries. Keep
SQL and connection secrets in Infrastructure, and errors/history redacted.

AdminCP credentials/sessions are separate from game/player identity. Matching
usernames and native GM flags never confer administrator permissions.
Bootstrap composes one selected settings store for public, staff and player
consumers. No interface/frozen-baseline shortcuts or implicit file migration.

Decision 100 records a planned easy installer/setup, verified recovery and AdminCP
factory reset to website defaults with setup available again. It is unimplemented.
Keep installation state in MSSQL, prevent installer takeover and scope reset to
explicit website-owned data, including unified database layouts. Never reset MU
game/account records. See docs/INSTALLATION_LIFECYCLE.md before implementation.

Decision 099 adds owned local PDO_ODBC evidence for reviewed DDL 001/002 and the
core/operational journal, including Unicode, processes, grants and locks. Strict
application TLS, other stores and whole-site/default-factory cutover remain
unverified. Decision 102 adds 41 actual SQL HTTP cases for administrator login
and core/operational settings through explicitly injected lab stores and minimal
runtime grants. This fixture is not a public backend/trust option. SQLite tests
verify only their own fixture adapter/HTTP paths. Follow the explicit
source-preserving, default-dry-run migration procedure; never test production.

MssqlSettingsConnection::connectionFromEnvironment is an Infrastructure factory
for explicit Bootstrap wiring into module-owned adapters. Other modules must not
import Admin internals. ConfigurationImportPlan/preflight compose fixed source
readers only: no values/actors in output, no database/apply path, no source defaults
or partial successful plan. Freeze source edits; separate locks are not a combined
atomic snapshot. See docs/WEBSITE_CONFIGURATION.md.
