# Offline verification

On the current Windows PHP 8.2.12 CLI, run `scripts/verify.ps1 -Level complete`
(or fast/affected/migration; use -Changed for affected paths). The wrapper checks
the selected runtime and loads installed matching GD/PDO_ODBC DLLs only for this
CLI process and its fixture children when absent, through a disposable INI under
reports. It restores PHP_INI_SCAN_DIR and removes that file/directory afterward.
It does not edit php.ini, restart Apache, skip tests or connect to a real SQL lab.
Plain `php scripts/verify.php` remains valid with those extensions already loaded.

Decision 112 adds `tests/utility_apis.php` through the three-API suite, covering
URL validation/encoding, declarative formula bounds/attacks and Order/Chaos
composition. Builder units compare 74 actual PHP calculations with the browser
interpreter. Chromium also substitutes reviewed response-data definitions to
verify named link/save URL and formula injection against the real PHP save route.
It does not establish a production route/formula editing interface. See
[URL](docs/api/URL.md) and [Math](docs/api/MATH.md).

Decision 111 adds `tests/builder.php` through the existing Templates test entry:
restricted project attacks/bounds, actual captured GrapesJS compatibility,
configuration escaping and compiled asset provenance. Run `npm.cmd run build`,
`npm.cmd run test:builder` and `npm.cmd run test:browser` separately. The latter
starts only a fresh marked private offline HTTP fixture and real Chromium,
exercising drag, nested layouts, geometry, resizing, responsive wrapping, themes,
undo/redo, literal text, CSRF/hostile/stale saves, in-flight edits and reload with stable IDs.
Ordinary PHP verification does not execute JavaScript or certify publication,
MSSQL template cutover or complete visual/accessibility coverage. See
[live builder scope](docs/LIVE_BUILDER.md) and STATE for actual results.

Decision 110 adds `tests/plugin-schema-inspection.php` to every non-fast scope:
strict physical metadata decoding, bounds, drift, transport/rollback faults,
authority before I/O and private Chaos/Order/Singularity composition. Scripted PDO is
fault evidence only. The separate opt-in owned SQL suite adds actual catalog
types/widths, namespace isolation, metadata visibility, unsupported alterations,
incoming dependencies, physical drift and DDL lock contention. It preserves
the existing administrator/settings HTTP scope; it is not plugin HTTP lifecycle
coverage. See [physical profile and limits](docs/PLUGIN_SCHEMA_INSPECTION.md)
and STATE for actual results. No production DDL, installed-state record or
migration execution was introduced.

Decision 109 adds tests/three-apis.php to every non-fast scope, covering schema/identity/resource denial before factories, exact versions, bounded reads/plans, permission revocation, private failures and full synthetic V1 ranking parity. tests/http.php checks unchanged health and ranking variants over real loopback HTTP. Active CLI is PHP 8.2.12; old PDO test doubles use ReturnTypeWillChange metadata for their PHP 7.4-compatible mixed-return overrides, without changing behavior or suppressing warnings globally. That decision included no new SQL adapter/DDL or actual MSSQL verification.

Decision 107 adds `tests/plugin-migration-review.php` to every non-fast scope.
It verifies read-only plan/resource/package/approval bindings, authority before
I/O, schema-step bounds/chains, drift/removal during review and private errors.
Real discovery and SQLite approval composition keep throwing PHP resources inert.
Packets remain unverified/non-executable; installed-state and SQL migration
execution are still pending. This adds no new actual MSSQL evidence.

Decision 106 adds `tests/plugin-metadata.php` to every non-fast scope. It covers
configuration/receipt authority, canonical Unicode/scalar bounds, schema binding,
retained interruption/recovery, revision/replay, audit rollback and four process
races. Owned SQL applies DDL 013 and exercises both independent families with
restricted grants, process races, trigger/permission rollback, real ambiguous
commits and escaped Unicode settings beyond 32 KiB. Receipt success is always
unverified. See docs/PLUGIN_METADATA_STORAGE.md and STATE.md for actual results.

Decision 105 adds `tests/plugin-approvals.php` to every non-fast scope, including
kernel architecture denial, lazy authorization, complete audit validation,
transaction/commit/rollback faults and independent SQLite process races. The
opt-in SQL suite adds reviewed DDL 012, a full bounded ASCII wire, restricted
provision/runtime grants, real races/locks/trigger/permission failures and large
approval records. Existing SQL HTTP evidence still covers only the previous
administrator/settings routes. Plugin operator endpoints, verified installed
migration execution, SDK and filesystem HTTP/worker lifecycle remain pending. See
docs/PLUGIN_APPROVAL_STORAGE.md and STATE.md for actual checks/counts.

Decision 104 adds `tests/plugin-discovery.php` to every non-fast verification
scope. It exercises the actual data-only parser/scanner with fresh marked
filesystem fixtures, throwing/syntax-broken PHP that is never executed, exact
inventory/fingerprint changes, removal/re-add discovery, independent candidates,
namespace collisions, malformed/duplicate JSON, size/path bounds and actual
Windows junction/hardlink refusal. These are discovery tests, **not** live HTTP
plugin activation/removal, persistent approvals, MSSQL plugin state or SDK tests.
The runtime audit and remaining proofs are in docs/PLUGIN_RUNTIME_MIGRATION.md.

## Additional acceptance matrix: four-layer APIs (pending)

Prove in real HTTP/worker integration tests: (1) `MainAPI` core boots with `/plugins` empty; (2) `zenda.game.mu` plug/unplug changes only MU game-type capabilities; (3) `zenda.db.mssql` plug/unplug changes only bound *external game* connection profiles while kernel website MSSQL/AdminCP stay functional; (4) `zenda.schema.muemu` plug/unplug invalidates only profiles needing its specific verified layout; (5) `zenda.rankings` survives unrelated plugin removal and uses normalized game read port only; (6) ambiguous multiple providers require explicit binding; (7) no random auto-selection or native write from schema probing; (8) missing-provider placeholders preserve GrapesJS project revisions; (9) game/account/website credentials isolated. This suite is **specified, not run**. See `API_ARCHITECTURE.md`.


## New acceptance suite required for drop-in plugins (not yet run)

Do not claim a working plugin system from PHP lint, manifests, frozen contract
fixtures or interface-only tests. Add real local HTTP and independent worker
coverage: (1) empty `/plugins` boots; (2) approved compatible plugin folder
appears and exposes API/routes/admin/event/widget/job; (3) physically remove it
while HTTP server remains running, all contributions vanish by the **next
request**; (4) core and independent plugin still work; (5) stored MSSQL-backed
settings/data remain; (6) restore folder and previous config works; (7) missing
required deps block only dependent plugin; (8) incompatible, unapproved, corrupt,
symlinked, half-copied, duplicate, licensed-off plugins never execute; (9) stale
multi-worker snapshots do not revive removed code; (10) saved GrapesJS projects
keep missing widget references with safe fallback. Include permission, migration,
commit failure and path traversal cases. See `PLUGIN_SYSTEM.md`.

The original external Markdown design did not implement this lifecycle. The
repository has a partial website runtime and decision 104 now adds data-only
discovery tests; the HTTP/worker plugin lifecycle suite above remains pending.

Decision 103 adds 50 shared guide transaction cases and 34 wire/profile guards,
bringing event-settings to 114 and loot-settings to 80 assertions. Cases verify
false begin/execute/commit/replay release, zero-row/trigger-altered initialization,
exact write readback and private rollback failures. Existing process/source/HTTP/
calendar consumers remain. A separate 43 real SQL guide cases extend the opt-in
suite to 187 assertions over DDL 003/004, including Unicode/NUL, exact eight-MiB
wire and valid histories over sixteen KiB, grants/timeouts/races/replay and real
ambiguous commits. Guide SQL HTTP/calendar and deployment memory/TLS remain
unverified. See docs/GUIDE_ODBC.md; fixtures never certify native scheduling/drops.

Decision 102 extends the separately opt-in SQL suite to 144 assertions, including
41 actual loopback HTTP cases for administrator authentication, settings and
operational configuration. A fixed guarded router injects the existing SQL stores
under restricted grants; private connection inputs stay in child-process memory/
environment. Cases include request/session/realm checks, Unicode/escaping, exact
and historical replay, conflicts, permission rollback/retry, corrupt/missing state,
maintenance, logout, persistent limiters and secret-free logs. Servers/private roots
and owned databases are removed. No ordinary test silently substitutes for an
absent SQL server. Full default-factory TLS/HTTP, other SQL stores, visual regression
and deployment remain unverified. See docs/MSSQL_HTTP.md for precise scope.

Decision 101 extends tests/admin-storage.php to 79 assertions, adding strict
Identity-owned ODBC profile denial, trigger-altered readback and rollback exception
redaction. The separate opt-in SQL suite now has 103 assertions, including 32 for
reviewed DDL 009 administrator credentials: real grants, lock timeouts, corrupt/
multiple-row state, trigger faults, three competing-initializer process pairs
and an acknowledgement fault after a real commit. Only newly created marked
fixtures are selected and removed. Ordinary HTTP tests still use owned SQLite;
trusted TLS and whole-site SQL HTTP integration remain unverified. Public V1-V5,
templates and module activation are unchanged. See docs/ADMIN_CREDENTIAL_STORAGE.md.

Decision 099 adds tests/mssql-connection.php to ordinary verification and the Admin
module's declared tests. It exercises exact driver selection, safe DSN handling,
malformed or partial credentials, strict wire bounds/surrogates, parameter typing
and negative SQL/Unicode profile cases. The optional real SQL suite is separate:
scripts/test-mssql-odbc.ps1 defaults to a no-connection plan; -RunOwnedFixture uses
private DPAPI lab inputs to create/remove a unique ownership-marked website-only
database. tests/mssql-odbc.php tests reviewed DDL 001/002 and synthetic core/
operational settings through actual PDO_ODBC, including Unicode/long documents,
real process races/locks, rollback, replay, grants, permission errors and ambiguous
commit acknowledgement. It never runs automatically or counts an absent SQL
server as passing. This lab's certificate is trusted explicitly by the fixture;
strict application TLS remains unvalidated locally. Other module/HTTP integration
is unimplemented, not a passing placeholder. See docs/MSSQL_LAB.md.

The user-authorized local lab additionally has manual PDO_ODBC metadata and
synthetic text-expression evidence under PHP 7.4. This is separate from the
offline runner's counts. Strict certificate validation fails locally; the isolated
lab probe explicitly trusts that test certificate. UTF-8 parameter code points
and NVARCHAR results pass with the ODBC UTF-8 option; direct NCHAR results fail.
That initial probe exercised no store/DDL/locking/grant/HTTP path; 099 now adds
owned Admin DDL/store/lock/grant evidence, with HTTP still unverified.
See docs/MSSQL_LAB.md; no existing game/account/database records were modified.

Decision 098 adds tests/catalog-inspection.php and tests/catalog-inspection-http.php.
Owned SQL/HTTP fixtures exercise authorization before lazy connection, separate
administrator/player sessions including a same-name player with admin role,
strict route selection/methods, no-store/security headers, exact stored count/SHA,
escaped hostile text, no forms or synthetic balances, SQL-only failure and
unchanged catalog/administrator records. HTTP catalog connections are explicitly
query-only. Missing/corrupt/empty family failures stay isolated, never install or
seed tables and leave database-mode shops/purchases at 503. Package tests cover
V5 identity/signature conformance, unknown V6/V5 ports and fixed absent/malformed
inspection composition. Shared file-mode HTTP refuses stored inspection.
V1-V4 freezes remain unchanged; V5 is an independent initial freeze.

Manual browser QA used only an owned synthetic SQL fixture. Both private pages
were inspected at desktop/mobile sizes with expanded JSON; no document overflow,
mutation forms or injected image elements appeared. Disposable screenshots live
in reports/catalog-inspection-*. This is limited manual evidence, not a visual
regression suite, real SQL Server operation, licensed public CMS commerce or
native game fulfillment. STATE.md records final check counts.

Decision 097 adds tests/premium-catalogs.php: strict independent Webshop/VIP draft
metadata, exact object/list JSON shapes, byte/collection limits and unchanged
stored model policies; no constructor/GET seeding or missing/corrupt code fallback.
Owned SQLite tests exercise checked inserts/transactions, false/ambiguous commits,
immutable retries, corrupt identities/digests/full documents and same-database
record independence. Real processes exercise shared source locks and competing
initializers. Source and CLI cases verify missing/invalid/hard-linked file refusal,
unchanged bytes, redacted lazy inspect, scope/backend/source fingerprint guards
before connecting and guarded public initialization. These are synthetic website
records, not real SQL Server/grant/TLS/table-lock evidence or native commerce.

The package suite adds exact V4 static provider signatures/alias/version refusal
without executing PHP, fixed provisioning paths, absent/malformed public factory
results and no eager connections. Existing package-isolated HTTP, Webshop/VIP
preview, settings and saved template consumers remain in the complete suite.
V1 through V3 files/catalogs/freezes are unchanged; V4 is a new independent initial freeze.
Database storefronts still return 503. See docs/PREMIUM_CATALOGS.md; actual current
check results/counts belong to STATE.md.

Decision 096 moves Webshop entity/pricing/view coverage from gamedata/views into
`tests/webshop.php` (81 assertions), replacing simulated debits/orders/fake bytes
with deterministic proposals, unchanged caller balances, no native delivery fields,
writer rejection and exact option/price/filter/escaping/theme compatibility. The
legacy prototype order model's existing value-object cases move with it and claim
no functioning order persistence. Strict options/balances, catalog bounds and
V3 composition/removed-core APIs have negative coverage. The new `fieldList` HTTP
parser rejects scalar/nested/keyed/oversized/overlong excellent lists; shared HTTP
now has 459 assertions including no detached Webshop audit and unchanged view.

At decision 096, `tests/packages.php` had 109 assertions: prior source/V1/V2/VIP cases plus exact
V3 binding/version/signature/unknown-port refusals without executing PHP, inactive/
malformed factories and actual Webshop absence/authentication/Origin/CSRF/shared
budget/privacy. A copied site proves Webshop works without VIP source and VIP
still works with broken Webshop composition. Core routes remain available with
both storefronts absent/broken. The Webshop affected run passes 871 assertions
across packages/webshop/shared HTTP/unchanged 222 settings-HTTP cases. All three
contract freezes and saved project/component fixtures are included in verification.
Full distribution manifests, actual SQL Server, native commerce/licensing and new
visual QA remain unverified. See docs/WEBSHOP_PREVIEW.md.

Decision 095 migrates the VIP entity/view assertions to tests/vip-shop.php (43
assertions) and removes the old simulated-write assertions in favor of pure
proposal/no-mutation cases. Coverage retains four tiers, domain validation,
missing/insufficient/unsupported input, exact 490-to-90 credit arithmetic, isolated
WCoin alias calculation, repeat determinism, strict typed balances, writer
injection rejection, removed core APIs, public composition, mounted/escaped views
and existing shared CSP theme rules. No test claims a VIP subscription activates.
At 095, the shared 452 HTTP and unchanged 222 settings-HTTP assertions covered present-package
forms/redirects/notices, no detached VIP audit and database-mode denial/security/state retention.

At decision 095, tests/packages.php had 85 assertions, adding exact V2 binding/version/
namespace/dependency refusals, nonexecuting static inspection, inactive/malformed
composition and actual absent-package HTTP behavior. The copied core contains
neither VIP implementation/class nor VIP view: public VIP and authenticated valid
POST return 503, while staff login/core routes work and CSRF/Origin/authentication
still deny. Missing-package attempts exhaust the persistent commerce budget at
the twenty-first request without appending a purchase/activation audit. A broken
composition returns private 503 without its exception, path or trace, while the
home page and authenticated administration remain available. These four additional
HTTP assertions were verified on 2026-10-19. Other paid responsibilities remain
mixed; free-distribution manifests,
paid persistence/licensing, actual SQL Server and new visual QA remain pending.

Decision 094 initially added 60 tests/packages.php assertions on this Windows host: reviewed
root ownership, layer/SQL/include restrictions, core/premium isolation, unknown
and hidden PHP discovery/refusal, unchanged exact V1 binding signatures without
executing implementation files, impact ownership, unsafe class paths, fresh PHP
autoload (including case-variant shadow refusal) and an isolated copied site with
both optional package roots physically absent. Home, separate staff login,
rankings and drops return 200. GameData still
contains the old paid responsibilities; this proves optional-root absence only,
not a completed free distribution, licensing or native commerce. The symlink
fixture executes only if the OS creates the link; Windows denied creation here,
so that branch has no local evidence and adds no passing assertion. Composer
prefix equality is checked; generated Composer loading has not been executed.

Schema profile catalogs add 117 tests/schema-profiles.php owned SQLite/process/
CLI assertions and 15 shared HTTP cases (222 shared settings HTTP assertions).
Coverage includes canonical/typed complete catalogs, unknown metadata and unsafe
identifier rejection, aggregate limits, corrupt SQL with a valid digest, owning
read/seed transactions, failed/zero-row/false/ambiguous initialization, competing
initializers, application authorization, escaped draft views and guarded private
source-preserving CLI. HTTP proves custom stored defaults/strict selectors,
missing/empty/corrupt catalog refusal without code/file fallback or GET seeding,
independent settings availability and unavailable database-mode shop/VIP requests
with authentication/CSRF/Origin and unchanged state/audit. No native schema or
codec verification, full premium-distribution absence, new visual QA or real SQL Server is
claimed. Physical premium separation and paid catalog persistence remain pending.

Configuration journal hardening adds 336 tests/configuration-journal.php SQLite/
process assertions for both core and operational records, plus 14 shared HTTP
cases (207 shared settings HTTP assertions). Coverage includes typed complete
head/journal agreement, canonical driver integers, corrupt read/replay/mutation
refusal, owning transactions, failed/zero-row write/seed stages, false begin/
commit/replay/rollback acknowledgements, ambiguous saves/initializers, exact
10000-revision capacity and replay, barrier races for initialization/CAS/retries,
and a concurrent commit between head and journal reads. HTTP proves rollback and
corrupt-history refusal without file fallback or detached success audit. The
existing 53 core and 58 operational assertions remain; the old capacity fixture
now carries a valid latest hash, and damaged-history tests also require failed
reads and unchanged raw state. Current hashes/formats remain compatible; old
values were not retained and their hashes are opaque. Actual SQL Server remains
unverified. The first new HTTP fixture referenced a missing outer variable; it
now resolves its helper relative to __DIR__. Its failed cleanup residue is recorded
in STATE; later fixtures clean normally, with strict warning handling unchanged.

Market runtime hardening adds 575 tests/market-runtime.php SQLite/process
assertions and five shared HTTP cases (193 settings HTTP assertions total).
Every SQL view, exact retry and new command rejects corrupt heads/commands/events/
escrows privately without repair. Coverage includes owning read transactions,
canonical driver integer strings, invalid numeric representations, v1 row order
and historical outcomes, regressive clocks, global capacities across all commands,
active-asset uniqueness, non-JSON/overwide payload outcomes, ambiguous commits and
retry/asset/terminal/capacity process races. A concurrent writer commits between
table reads while the reader retains its original coherent snapshot. HTTP rejects
corrupt browse and exact retries without fallback or detached success audit.
Actual SQL Server behavior and stronger versioned request identity remain unverified.

Market migration/write acknowledgement hardening adds 177 tests/market-import.php
file/SQLite/process/CLI assertions and 11 SQL market HTTP assertions in the shared
settings suite (188 shared total). Coverage includes complete source/head/history/
escrow validation, actual moderator/buyer/operation/clocks, structured retry payloads,
opaque creation-digest retention, source locks/identity/bounds, four-target emptiness,
every failed/zero-row import/runtime write stage, false/ambiguous commits, importer/
runtime barrier races, guarded CLI, CSRF/Origin, rollback/no-file-fallback and native
purchase denial. Existing 122 marketplace and 212 safety assertions remain. Tests
preserve the old payload-independent deposit replay without claiming full payload
identity; strict SQL runtime validation now has the owned evidence above. Actual
SQL Server is unverified. An initial negative fixture changed a moderator actor,
which is valid metadata; it now checks forgery of a seller-only operation. No runtime
guard, existing assertion or warning handler was weakened.

Support hardening adds 187 tests/support-storage.php file/SQLite/process/CLI and
25 tests/support-storage-http.php loopback assertions. They check complete v1
history/digests/heads and SQL event/type agreement, private corruption denial,
strict existing/linked/missing/oversized sources, stable source-lock contention,
all-three-target emptiness, owning reads/writes/imports, every failed/zero-row stage,
false/ambiguous commits, exact capacities, 100-message limits and barrier races
for retries/replies/active caps/global bounds/importers/runtime. SQL subject tests
preserve the exact DDL 007 UTF-16 bound without truncation. HTTP verifies separate
authority, ownership/masking/escaping, historical replay, CSRF/Origin/throttle,
rollback and no file fallback. Existing 84 support assertions remain. Actual SQL
Server driver/types/grants/locking/deadlocks, deployment and new visual QA remain
unverified. The initial HTTP fixture tried to obtain staff CSRF from an empty inbox
without a mutation form; it now reads the real selected-ticket form. No runtime
guard or warning/assertion was suppressed.

Profile runtime hardening adds 137 tests/profile-storage.php SQLite/process and
19 tests/profile-storage-http.php loopback assertions. All bounded profile,
command and event rows are checked together for types, replay digests, continuity
and exact privacy heads. Coverage includes nested read/write refusal, malformed/
orphan/duplicate histories, every failed/zero-row write stage, checked/ambiguous
commits, capacities and barrier races for revisions, identical retries, independent
owners, global limits and import versus runtime creation. HTTP covers private
preview, publication/opt-out/old replay, escaping, ownership, CSRF/Origin/throttle,
rollback, corruption and missing schema without file fallback. SQL Server locks,
driver/grants/collations/deadlocks and new browser QA remain unverified.

Profile migration adds 52 tests/profile-import.php file/SQLite/process/CLI
assertions: complete history/digest/current-state validation, source preservation,
missing/linked/oversized source denial, all-three-target emptiness, nested/failure/
zero-row/false/ambiguous transaction rollback, historical replay/privacy retention,
stable source-lock contention and competing importers. It also verifies false
runtime save/replay commits roll back and driver errors are redacted. Existing
75 profile assertions remain. Runtime SQL row/history/concurrency now has the
separate coverage above; neither suite certifies actual SQL Server behavior.

Administrator storage adds 68 tests/admin-storage.php and ten admin-storage-http
assertions: strict canonical source/direct imports, private source preservation,
corrupt/multiple-row SQL denial, driver/nested/false/ambiguous transaction refusal,
zero-row insert rollback, SELECT/INSERT-only writes, source-lock contention,
different-name initializer barrier races, guarded CLI and separate administrator
HTTP authentication alongside conflicting file/player identities. No actual SQL
Server or production provisioning/rotation verification is claimed.
The initial HTTP readiness probe failed under the strict warning handler; the
fixture now uses the existing health-request readiness loop and releases caught
error contexts before Windows cleanup. An early failed root remains after
automatic cleanup review rejected it; see STATE.md. The storage no-DDL assertion
normalizes SQLite's string COUNT result before comparing zero, without changing
runtime validation or suppressing an assertion/warning.

tests/account-crud.php adds 142 file/SQLite/process assertions: validated creation,
strict metadata/credential reads, corrupt-state preservation, orphan-state denial,
literal searches/order-before-limit, role/history isolation, encoding-safe rehash,
owning transactions, zero-row/failure/false/ambiguous commits and barrier races for
identity/email uniqueness and role/replacement/ban serialization. Existing identity
duplicate checks now use valid bcrypt inputs so they reach duplicate enforcement.
SQL HTTP adds 11 assertions (177 shared total) for role rollback, CSRF/Origin,
registration failure/success/audit/no-file-fallback and corrupt metadata reads.
The settings audit check verifies its exact baseline before allowing the one
intentional registration audit extension; it does not absorb settings writes.
No actual SQL Server, premium packaging or browser coverage is claimed.

tests/account-metadata.php adds 105 file/SQLite/process assertions for canonical
email/status recovery revocation, no-op preservation, post-unban denial, invalid/
duplicate/missing inputs, active issuance rechecks, sticky used/expiry, import
markers, compatible imported metadata/canonical email collisions, corrupt rows,
owning transactions, rollback/zero-row CAS, false/ambiguous commits and barrier
races. The settings HTTP suite
adds 12 assertions (166 total), and file HTTP adds five: authorization, rollback,
unknown-status denial, missing-target/no-success-audit and no file fallback.
Recovery fixtures explicitly require fresh post-unban tokens (36 assertions);
identity retains 196 assertions with a genuine initial PDO bcrypt fixture. The
existing SQL password-write fault fixture now also supplies a valid hash so its
trigger is reached. All evidence remains offline; real SQL Server, delivery/outbox,
durable administrative retries/atomic audit and browser QA remain unverified.

Administrative credential replacement adds 87 tests/credential-replacement.php
file/SQLite/process assertions for revocation, staff authority/session behavior,
status/codec/history/replay preservation, strict inputs/corruption, file capacity,
SQL credential/token CAS/rollback, nested/false/ambiguous transactions, import
markers and barrier races with recovery/issuance/self-service. Strict Windows
cleanup now releases the foreach alias retaining PDO; no warning is suppressed.

Shared SQL settings HTTP adds 12 staff replacement/CSRF/Origin/missing/rollback/
throttle/no-file-fallback/audit-privacy assertions (154 total), with injected
SQLite player storage. File HTTP adds three revocation/status/privacy assertions.
The settings audit check retains a byte-for-byte baseline after the new intentional
account audit; settings must still append nothing. Administrative command version/
idempotency/atomic audit and actual SQL Server remain unverified.

SQL recovery storage adds 72 tests/reset-storage.php assertions and owned barrier
workers. Tests deny DELETE, inject insert/update/zero-row/start/commit failures,
preserve consumed state/expiry, reject corrupt used/hash/expiry values and invalid
replacement encodings, and retain application throttling for inactive accounts.
Concurrent issuers, consumers, issuance/recovery/self-service and recovery versus
self-service exercise the common account write boundary. Existing 196 identity,
34 recovery, 92 password and 66 player-import assertions remain, plus HTTP suites.
This is owned SQLite/process evidence; actual SQL Server remains unverified.

The shared settings HTTP suite additionally has 12 recovery-gate assertions
(within the current 154 total). With SQL accounts injected and conflicting file credentials/tokens,
database-mode recovery GET/POSTs refuse before touching account/session state or
revealing tokens/forms. Tests retain private headers/global Origin checks and
verify unchanged SQL/file credentials, reset links, history and detached audit.
Secure delivery and an enabled SQL recovery HTTP flow remain unimplemented.

Player migration hardening adds 66 tests/player-import.php assertions for coherent
stable-lock source reads, strict private state/metadata, preservation of credentials
and complete password replay/history, consumed-token marker protection, fresh-token
retention, nested transactions, all-three-table empty checks, rollback, ambiguous
commits, concurrent importers and redacted CLI dry-run/apply guards. A process test
holds the actual file-writer lock and verifies the reader cannot complete until
released. It uses a fixed owned signal instead of blocking Windows pipe reads
under that lock. Existing identity positives now use real bcrypt fixture hashes,
retaining all 196 assertions. No SQL Server/native MU certification is implied.

Template continuation audit adds tests/template-storage.php (34 assertions) and
retains 112 tests/templates.php assertions. SQLite fixtures enforce the actual
head/revision foreign-key relationship, catching the inherited child-first insert
bug. Coverage includes exact/null draft CAS, unknown-field preservation, typed
metadata, immutable history, rollback/zero-row CAS, caller transaction ownership,
one-transaction complete-history imports, all three pointers, source preservation,
corrupt/dangling identities, SQL publication denial, public imported projections,
ambiguous commits and independent-process draft races. CLI tests compare full
collection fingerprints and reject stale input before connecting. This is not
SQL Server certification, publication content security or live editor coverage.

Marketplace continuation audit adds tests/market-safety.php (212 assertions) for
file/SQLite item/character records with absent, offline, unmarked-native and
partial adapter inputs. It requires authorization then native-execution denial,
unchanged website/escrow/replay/audit state, no writer calls and unchanged balances/
warehouses. The older successful direct-writer assertions were replaced because
they could not establish atomic lifecycle safety. tests/marketplace.php retains
122 draft/planning/storage assertions, including explicitly labelled metadata-only
escrow transitions. Native escrow/settlement/delivery remain unimplemented.

Website settings have 53 tests/settings.php assertions, 58
tests/operational.php assertions and 154 tests/settings-http.php assertions for
explicit schema/seed, strict state, authorization, revisions/retry, atomic history,
rollback/ambiguous commit, worker CAS, guarded imports, no file fallback and shared
public/player/staff behavior. They use owned SQLite databases and HTTP fixtures;
the production entry point has no fixture backend switch. MSSQL DDL/driver/TLS/
locking remain unexecuted; see docs/WEBSITE_CONFIGURATION.md.

Operational coverage includes strict policy/URLs, unchanged core command replay,
independent records, journal rollback, source-preserving import/apply guards,
distinct SQL constraint names, database-selected Drupal despite conflicting
environment inputs, upload disablement/byte limits on both routes and corrupt/
missing policy refusal. Desktop (1440px) and mobile (390px) browser QA checks the
private form, save/reload and asset controls on an owned synthetic SQLite fixture.
The HTTP fixture explicitly releases caught readiness exceptions before removing
its database: PHP 7.4's legacy error-handler context can retain PDO in a trace.
Cleanup remains mandatory under the runner's strict error handler.

Event SQL adds 55 tests/event-settings.php assertions and 27 cases within that
shared settings HTTP suite. It checks constructor/seed denial, existing port
results, file-to-SQL initial/current definitions and historical replay, rollback/
CAS and unacknowledged commit recovery, strict state/digests, nested transactions,
history/byte limits, authorization consumers, guarded imports and independent
worker revision/exact-retry races. Public timetable/calendar and private editor
consume the injected SQL store despite conflicting files, retaining CSRF/Origin,
escaping, read privacy, mutation throttling and redacted missing/corrupt-state
failures. This is owned SQLite evidence; actual SQL Server remains unverified.

Loot SQL adds 55 tests/loot-settings.php assertions and another 27 shared HTTP
cases. It covers generated-ID create retries, operation-scoped keys, preserved
file histories/deletions, exact chances/filters, application authorization,
independent event storage, rollback/zero-row CAS, ambiguous commit recovery,
missing/corrupt state, 500-entry/1000-command limits and independent worker creates/
duplicates. Shared GameData SQL/import helpers accept only fixed guide identities.
Existing file suites still pass without changing v1 formats or valid-command semantics.
The shared history validators additionally check reconstructed command fingerprints,
nonnegative clocks and intermediate loot-entry capacity. Invalid documents that
older readers accepted now intentionally fail closed; legitimate existing v1
documents/retries remain compatible. No frozen signatures or fixtures were refreshed.

Configuration preflight adds 19 assertions for complete four-source plans,
schema/retained revisions, exact dry-run importer fingerprints, redacted output,
no database/apply path, all-or-nothing failure and unchanged source bytes. These
source-only checks cannot verify SQL installation, grants, cutover or restore.

Drupal news adds 52 adapter/service/view assertions and 17 real Zenda HTTP cases
against a synthetic upstream. Both run in the portable verification suite with
no Drupal dependency. Separate manual QA used an actual isolated Drupal 11.4.10
site on PHP 8.4.26, exercising published/draft articles, edits, revisions,
withdrawal/republication and desktop/mobile Zenda pages. See docs/DRUPAL_NEWS.md.
The Drupal SQLite site is an offline prototype; all production website settings
must move to MSSQL. No SQL Server driver/migration test has run.

Run from the application root. PHP 7.4+, JSON and tokenizer are required. No
Composer install, external database, game server, legacy bootstrap or external network
is needed. HTTP tests start an isolated loopback PHP server and require process
and local socket support; they use temporary private state and synthetic data.
On this host PHP is `C:/xampp/php/php.exe`; it is not on PATH. Composer is not
installed on PATH, so its aliases have not been executed locally.

```powershell
& 'C:/xampp/php/php.exe' scripts/verify.php --level=fast
& 'C:/xampp/php/php.exe' scripts/verify.php --level=affected --changed=src/Modules/GameData/Domain/Inventory.php
& 'C:/xampp/php/php.exe' scripts/verify.php --level=complete
& 'C:/xampp/php/php.exe' scripts/verify.php --level=migration
```

Composer aliases: verify (complete), verify:fast, verify:affected,
verify:complete and verify:migration. For affected paths use
`composer verify:affected -- --changed=path1,path2`. IDE tasks expose both direct
PHP and Composer variants. No dependencies are currently required to run them.

| Level | Actual current coverage |
| --- | --- |
| Fast | all PHP syntax, JSON parsing, token-based architecture, module/capability/config/component declarations, frozen contracts and structural project fixtures |
| Affected | fast checks plus tests declared for direct/indirect affected modules; unknown/shared changes conservatively include all modules |
| Complete | fast plus tooling, synthetic migrations, identity, content, view and loopback HTTP suites |
| Migration | complete current offline suite, including source-preserving synthetic project/configuration migrations and rejection cases; no live SQL or game-data migration |

Complete and migration currently run the same suite set; migration adds no live
database execution. Affected checks select the declared runtime tests for direct
and indirect module consumers. Fast checks do not execute application tests.
`reports/verification.json` is a generated local report with counts, errors and
explicit coverage limits. It is ignored by Git and replaced by each verification.
Test infrastructure is protected; do not regenerate contract baselines to hide a
failure. Current results are recorded in [STATE.md](STATE.md). The
[phases 1–3 review](docs/PHASE_1_3_REVIEW.md) preserves the historical checkpoint;
the user approved continuation into phase 4 on 2026-10-17.

| Suite | Boundary exercised |
| --- | --- |
| `tests/tooling.php` | Architecture, catalogs, contracts, impact analysis and negative fixture validation. |
| `tests/migrations.php` | Copy-based synthetic saved-project migration and rejection cases. |
| `tests/identity.php` | Credential hashing and storage, authorization, CSRF, persistent throttling and isolated native-session lifecycle. |
| `tests/content.php` | Authorized publication, Unicode validation, safe public projections, malformed-state rejection and concurrent atomic JSON writes. |
| `tests/news.php` | Published archive/detail reads through file/SQLite adapters, literal Unicode search, deterministic pages/counts, excerpts, corrupt SQL/file fields, mounted/hostile views and unchanged full-data consumers. |
| `tests/views.php` | Hostile model escaping, mounted form actions, CSRF attributes, unavailable sign-in, accessible labels and empty states. |
| `tests/http.php` | Actual loopback requests through the front controller: sign-in/session rotation, Origin and CSRF enforcement, authorized publication, sign-out, headers and source-path denial. |
| `tests/marketplace.php` | Offline market pricing, permission/ownership denial, persisted drafts and atomic audit/replay, corruption rejection, concurrent process creates/cancellations and disabled native transfers. |
| `tests/market-import.php` | Complete v1 source/history/escrow retention, strict migration locks/bounds/empty targets, failed/ambiguous SQL boundaries and importer/runtime races. |
| `tests/market-runtime.php` | Coherent complete four-table SQL snapshots, corruption denial across reads/replay/writes, typed integers, historical compatibility, capacities/clocks and concurrent retry/asset/terminal/capacity commands. |
| `tests/configuration-journal.php` | Core/operational coherent head/journal snapshots, typed complete history, checked writes/initialization/replay, ambiguous acknowledgements, capacity and process races. |
| `tests/schema-profiles.php` | Immutable mapping draft catalogs, typed/bounded source/SQL validation, guarded provisioning, authorization, escaped inspection and initializer races. |
| `tests/playeridentity.php` | Website account login, fixed player permissions, ban/password session revocation, scoped throttling and isolated native player/staff session expiry. |
| `tests/profiles.php` | Owner/public/preview boundaries, private defaults and compatibility helper, exact public fields, Unicode/numeric/punctuated IDs, revision/replay races, corrupt histories and capacity refusal. |
| `tests/recovery.php` | Atomic reset/password replacement, exact expiry, inactive-account denial, SQL failure rollback, owning-transaction refusal, process confirmation races and corrupt state preservation. |
| `tests/passwords.php` | Authenticated website password policy, atomic file/SQLite credential/reset/history changes, exact replay, later recovery, SQL insert/update failures, stale sessions, capacity/corruption and file-worker races. |
| `tests/password-http.php` | Isolated loopback password form, owner/codec forgery denial, Origin/CSRF, malformed fields, empty secret errors, separate persistent throttle and native player-session revocation/new login. |
| `tests/support.php` | Owned private tickets, masked replies, summary search/pagination/privacy, text/cap/status policies, atomic persistence/replay, corruption denial and process reply/cap races. |
| `tests/siege.php` | UTC stage boundaries, synthetic cycle/history rollover, unsupported/unknown profile denial, native registration gates and escaped views. |
| `tests/loot.php` | Exact integer chances, authorized website guide CRUD/search, revision/replay, corrupt-state denial and concurrent edit conflicts. |
| `tests/events.php` | Canonical UTC configuration round trips, permission/revision/replay rules, corrupt-history denial, concurrent writers and bounded calendar windows/escaping/UTF-8 folding. |

Milestone 6 adds market view/HTTP cases for throttling, CSRF/Origin,
replay/conflicts, manager actions and transfer denial. See
[marketplace scope](docs/MARKETPLACE.md). These website-owned draft tests do not
certify escrow, settlement or native character/item moves.

Event regression coverage also checks chronological fixed times, weekday-filtered
intervals, midnight active windows, day-boundary recurrence, host DST independence
and stable deadline ties. Existing V1 consumers/template fixtures remain unchanged.
The event HTTP workflow additionally exercises persistent editing/deletion, role,
Origin/CSRF/throttle denial, strict calendar query windows, MIME/filename headers,
live configuration projection and dense/empty/oversized export responses. These
tests do not import files into calendar clients or verify native event execution.
See [event timetable scope](docs/EVENT_TIMETABLE.md).
Server views use external CSS rules under the original strict CSP; view checks
verify that every extracted presentation reference has a stylesheet rule.
Profile HTTP cases check opt-in/revocation, private preview, forged ownership,
native-name collision isolation, bans and throttling. Signup, login, profile and
support use the same underscore/hyphen account; native-session subprocesses also
reject undeclared identifier syntax. AccountReader/template consumers and frozen
fixtures remain separate. See [profile scope](docs/WEBSITE_PROFILES.md).
Announcement HTTP cases cover public search/detail, actual pagination links,
strict queries, updated/deleted URLs and corrupt-state denial. Staff news POSTs
also have shared-throttle/cross-route denial and read-independence coverage;
revision/replay and same-commit mutation/audit remain pending. See
[announcement scope](docs/ANNOUNCEMENTS.md).
`tests/browser-fixture.php` is an optional interactive manual-QA harness, not part
of the automated suites. It creates isolated synthetic identities/server state and
releases them when sent `stop`; it never uses personal sandbox state.

The application slice and its limits are described in
[docs/SANDBOX.md](docs/SANDBOX.md). Tests create their own temporary state and
credentials; they do not require a personal sandbox administrator. Identity
ownership/mode checks on POSIX depend on `posix_geteuid`; Windows file protection
depends on host ACLs and is not certified by the POSIX permission branch.

The static checker enforces a documented PHP subset and does not replace PHPStan,
whole-program type checking, runtime contract shape tests or a security audit.
Pinned PHPStan/lint/build dependencies remain future verification work.
No JS sources exist, so JavaScript/TypeScript lint/build checks are not implemented.

The PHP subset requires one unbracketed namespace and explicit leaf imports or
fully qualified types. Namespace-prefix aliases, grouped/function/constant imports,
unreviewed external qualified dependencies, inherited/trait implementation bindings
and zero-contract capability activation fail closed. Static bindings require direct
concrete own-module classes with exact public instance signatures. Implementation
PHP is inspected without autoloading it. Add reviewed support and regression cases
before using syntax outside this subset; do not suppress policy failures.
The offline identity/content/support/guide and HTTP coverage applies only to the
implemented local website slice. Live MSSQL behavior, native game synchronization,
payments, deployment upload isolation, authentic GrapesJS round trips, browser
regression automation and universal game integration are not certified. There is
no JS build pipeline. Manual
browser inspection, when recorded in STATE, is not a browser regression suite.

CI is configured for PHP 7.4 and 8.2 on hosted Linux. Only local PHP 7.4 has been
executed here; neither remote workflow nor the 8.2 job has been run. Branch
protection/review ownership must be configured by the repository owner; see
.github/REVIEW_POLICY.md. Place the application's contents at a repository root
for the included workflow paths to work.

Workflow action choices follow the checked primary documentation for
[checkout](https://github.com/actions/checkout),
[setup-php](https://github.com/shivammathur/setup-php) and
[upload-artifact](https://github.com/actions/upload-artifact). No remote workflow
has been run to certify those integrations.

Optional legacy preservation evidence: scripts/legacy-audit.php hashes reference
files without executing them and compares against a local snapshot in reports.
It is separate from the portable offline suite; CI never requires the legacy tree.
