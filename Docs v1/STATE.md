# Session state

## Current checkpoint: live builder and URL/Math derivatives (2026-10-20)

Decision 111 installs the real restricted self-hosted GrapesJS layout editor.
Decision 112 adds URL and reusable Math derivatives, starting with layout as
the user requested. Their first migrated consumer is the editor. Final PHP,
JavaScript and real Chromium checks pass. The website remains a partial offline
CMS; actual MSSQL template cutover, publication, verified installed plugin
history/execution and executable folder plugins remain pending. Plugin step 03
remains open. Decision 110's physical SQL inspector is retained unchanged.

Read [AGENTS](AGENTS.md), [PROJECT](PROJECT.md),
[API summary](API%20Summary.md) and
[implemented profile / consumer map](docs/api/WEBSITE_INTEGRATION.md) next.
[Live builder](docs/LIVE_BUILDER.md), [URL](docs/api/URL.md) and
[Math](docs/api/MATH.md) describe this slice. The
[physical inspector and limits](docs/PLUGIN_SCHEMA_INSPECTION.md) remain relevant.
[docs/README](docs/README.md) indexes supporting documentation.
This file is the only active implementation ledger.

## Implemented live layout editor and derivative APIs

- Free compiled local assets: GrapesJS 0.23.8, Google Material Color Utilities
  0.4.0, Moveable 0.53.0 and Floating UI DOM 1.8.0. Exact npm lock/integrity,
  retained notices for 39 production packages and artifact hashes accompany the
  distribution. Node is needed for development builds, not a customer service.
  Source is in frontend/builder; output is public/assets/template-editor.
- Existing authorized `/admin/templates/edit` now provides a real canvas,
  nested Container/Columns/Stack/Card/Button Row/Text blocks, native Grid/Flexbox
  gaps and wrapping, geometry/overflow inspection, spacing/alignment controls,
  bounded mouse/keyboard resizing, Material light/dark colors, page/device
  switching, undo/redo and draft saving/reloading with stable component IDs.
  Frame-specific theme painting retains contrast across pages. Inspector values
  use actual computed CSS even when GrapesJS moves styles into class rules.
- Templates owns the private BuilderProject/BuilderView profile. Before canvas
  load/save, bounded known tags/types/attributes/styles/selectors refuse scripts,
  event handlers, arbitrary HTML/CSS, external/protocol-relative links and data
  sources. Dedicated text nodes preserve literal text safely. Escaped host data,
  scoped self-only script CSP and an iframe without script permission apply.
  This restricted profile is not a general sanitizer or publication validator.
- New builder-save POST retains origin/body limits, CSRF, separate administrator
  templates.edit authority, template identity and exact CAS revision through the
  existing TemplateStore. Acknowledged saves preserve newer in-flight edits.
  Configured storage stays authoritative; no new settings/file fallback exists.
  Existing explicit offline prototypes remain development-only. Actual MSSQL
  template integration, publication, editor uploads/shared-section UI, full
  accessibility/visual coverage and dynamic optional-provider blocks are pending.
- Existing zenda.rankings/account metadata is retained with synthetic placeholders,
  without native reads. Fixture image references may remain unresolved; no legacy
  assets/settings/player data were copied. A new captured real GrapesJS fixture
  accompanies the unchanged old frozen fixtures, component catalogs and V1-V5.
- Isolated private Kernel/Urls and Kernel/Math Domain owners consume neither
  each other, Order internals, module implementations nor SQL/transports.
  Bootstrap registers pure url.resolve/math.evaluate 1.0.0 Singularity handlers
  through Order; Chaos can propose them. These are derivatives, not new primary
  APIs, a public RPC endpoint, frozen SDK or executable plugin registry.
- Named exact-version URLs replace editor link/asset/save literals and associated
  Application route checks. Closed path/query parameters, safe encoding and
  same-origin declarations refuse unknown/unsafe routes. Destination authorization
  remains independent. Other website URLs and whole-site mount deployment still
  await migration; static web-server asset allowlists remain explicit.
- Declarative named layout.snapWidth, layout.innerWidth and layout.columnWidth
  formulas replace consumer equations. PHP and browser interpreters enforce exact
  versions, ranges, bounded arithmetic trees, finite values and no executable text,
  implicit coercion, division by zero or overflow. Mouse/keyboard sizing use the
  same padding calculation. Browser results are display-only, not write authority.
  Reviewed built-in declarations are composition data, not editable settings.
  Future configurable catalogs need authoritative MSSQL/revisions/authorization/
  audit and version migration. No game/shop/payment formulas were enabled.

## Current verification: decisions 111-112

Pre-edit impact conservatively selected all nine static module groups.
Actual final checks used PHP 8.2.12 with base GD/PDO_ODBC already loaded:

| Check | Result |
| --- | --- |
| Final verify fast | Passed: 470 PHP, 329 source, 30 JSON, 2 unchanged saved template fixtures |
| Final verify complete | Passed: 6,825 assertions, including architecture, frozen contracts, template/consumer and existing HTTP suites |
| Focused builder profile/view/artifacts | Passed: 38 assertions |
| Focused URL/Math derivatives | Passed: 57 assertions; full three-API suite 158 |
| npm run build | Passed: local JS/CSS, 39 retained production notices and matching artifact hashes |
| npm run test:builder | Passed: 7 tests, including 74 matching actual PHP/browser calculations |
| npm run test:browser | Passed: 6 real Chromium scenarios; desktop/mobile screenshots inspected |
| npm audit, production and full dependency sets | Zero known advisories observed at this checkpoint; not a security guarantee |
| Edited supporting documentation | 158 local links resolve before this STATE update |
| CLI startup / final owned-fixture inventory | No startup warning, no PHP processes, no builder HTTP fixture or reports/php-cli roots |

Reports: reports/verification-builder-utilities-{fast,complete}.json,
reports/builder-browser.json, reports/builder-npm-audit.json and
reports/builder-{desktop,mobile-canvas}.png. Earlier builder-only affected/full
checks passed 6,758/6,766 assertions before adding the derivatives. Node 24.21.0 /
npm 11.19.0 were used; source remains PHP 7.4-compatible, not rerun on PHP 7.4.
No SQL DDL/adapter/grant changed, so the actual SQL lab was not rerun for this slice.

Browser cases exercise real drag/nesting, geometry/gaps, parent-bounded resizing,
mobile wrapping, Material colors across pages, undo/redo, stable-ID save/reload,
plain literal text, forged CSRF, hostile/stale saves and in-flight edits. One
response-data substitution case proves named URLs/formulas drive consumers while
saves reach the real guarded PHP route. It is not a production catalog editor.
The sixth initial login hit the existing throttle; the fixture now authenticates
once and reuses only its synthetic session. No production guard was weakened.

An initial Vite default copied public recursively into public/assets/builder.
The owned build was stopped; automatic approval review rejected cleanup as
"blocked by policy". The user manually removed that generated directory and its
absence was verified. The corrected build uses publicDir:false, a different
template-editor output and no automatic recursive clearing. Historical rejected
roots remain untouched; retain the residue instructions linked below.

## Implemented physical website schema inspection

- Kernel/Plugins owns the private SchemaFootprintInspector port,
  SchemaInspectionService, strict MssqlSchemaCatalog decoder and
  PdoSchemaFootprintInspector. Bootstrap bridges the isolated Kernel owners
  through SchemaFootprintRead and SchemaInspectionComposition; existing
  modules and frozen V1-V5 contracts/catalogs/hashes are unchanged.
- Separate administrator `plugins.migrate` authority on `admin` precedes
  connection. Expected schema and connection factory come from trusted
  composition. The private `plugins.schema.inspect@1.0.0` capability takes
  empty input and returns an explicitly non-executable bounded observation.
  Chaos planning opens no connection; Order rechecks policy before Singularity.
- Profile `mssql-simple-v1` observes bounded owned dbo tables with exact scalar
  widths/nullability, binary text collation and one ordinary clustered PK.
  Unsupported indexes, constraints, defaults, triggers, physical flags,
  dependencies, metadata visibility and unknown SQL profiles refuse.
- A separate dbo operator connection is required for complete metadata
  visibility; no runtime privilege elevation or grants occur. Fixed prepared
  catalog SELECTs use an explicitly ASCII-typed prefix and hex identifiers.
  Two matching ordered scans and checked rollback own the read transaction;
  existing caller transactions are refused unchanged. Failed instances discard
  the connection. No native rows, resource scripts, DDL or installed-state writes
  are performed by the inspector.
- Matching scans are an observation, not an atomic DDL snapshot or reservation.
  They cannot prove row preservation, package presence, installed history,
  global trigger/permission safety or execution consent. The future executor
  must freshly recheck and bind the physical profile under its own lock.
- Actual SQL evidence is SQL Server 17 Express / ODBC Driver 18 only.
  Versions 13-17 and editions 2/3/4 are accepted by the bounded profile;
  other accepted versions have not been separately integration-tested.
  No public endpoint/SDK, ordinary HTTP binding, production DDL or native game
  capability was added. Existing approval/configuration/receipt schemas remain.

## Primary architecture and actual runtime changes

- **Chaos** proposes deterministic plans from Order's permission-filtered catalog.
  It executes no handler and requires no AI provider. The implemented profile
  supports 1–16 known independent operations; open-goal search, DAG dependencies
  and result references remain pending and refuse in this profile.
- **Order** resolves exact capability/version bindings from a sealed trusted
  registry, checks server actor/resource permissions before lazy factories,
  validates closed bounded inputs/outputs and applies read/deadline/quota policy.
  Only none/read effects execute. Write/external effects are unavailable.
- **Singularity** defines the internal single-operation handler port. Handlers
  receive bounded server context and explicit dependencies, not a global locator.
- Source: `src/Kernel/Apis/{Domain,Application}` and `src/Bootstrap/Apis`.
  Kernel owner/layer/contract policy is explicit; only frozen Authorization is
  imported by the new owner. No existing module imports this private owner.
- HTTP health, home ranking projections, ranking page variants and template
  ranking preview now use Order through Bootstrap compatibility adapters.
  Complete synthetic V1 RankingReader projections, filters, saved components,
  health JSON and existing HTTP error/status behavior are retained.
  These remain synthetic reads, not live MU or verified realm/provider binding.
- Other static Admin/player/settings/support/commerce actions keep their existing
  application, authorization, CSRF/throttle and persistence boundaries. Migrate
  each focused operation through reviewed adapters rather than claiming wholesale
  website conversion.

**Derived contracts:** MainAPI/PluginHost specializes Order lifecycle/registration;
GameTypeAPI, DatabaseTypeAPI and SchemaAdapterAPI define independently versioned
Singularity provider contracts governed by Order. Every future plugin still
enters via scoped MainAPI registration. Game family, external transport engine
and emulator/schema identity remain distinct. Existing plugin.json v1 keys and
all frozen V1–V5 source/catalogs/hashes are unchanged. The private read gateway
is not a public SDK or approved dynamic plugin loader.

Deadlines are cooperative checks, not forced interruption of in-process PHP.
Quotas/telemetry are bounded per Order instance; telemetry holds status/duration
only, not durable audit. Durable effects, idempotency, DAG/reference execution,
public SDK catalogs, trusted dynamic registration and worker lifecycle remain pending.

## Project organization and customer setup

Canonical primary specifications now live in `docs/api/CHAOS.md`, `ORDER.md`
and `SINGULARITY.md`, alongside the integration map. Original root filenames
remain working compatibility pointers. PROJECT is a concise requirements/source
map; architecture, agent rules, derived API manuals and implementation plan
reference the three-primary-API hierarchy. Existing source owners, Composer
mappings, package roots, DDL and frozen contracts remain at their tested paths.

The previous full PROJECT, API_ARCHITECTURE and STATE are preserved in
`docs/archive/*-2026-10-19.md`. Historical next actions do not override this file.
No legacy code or installed settings were copied.

The user requested manual instructions for themselves and future customers:
[Manual XAMPP / PHP 8.2 setup](docs/installation/XAMPP_8_2.md).
It covers GD, PDO_ODBC/PDO_SQLSRV, Windows ODBC Driver 18 and Visual C++ prerequisites,
other required/recommended/optional components, matching PHP/TS/architecture,
php.ini edits/restarts, CLI versus Apache checks, private paths, upload/log limits,
local public webroot, verification and troubleshooting/rollback.
Sources are linked to PHP, Apache Friends, Apache and Microsoft documentation.
The user enabled extensions manually. The agent did not edit host configuration,
restart Apache or install Microsoft drivers. Apache's loaded runtime was not verified.

## Minimax agent entry point (documentation only)

Added `MINIMAX.md` and `docs/MINIMAX.md` in the application plus a parent
`MINIMAX.md`, mirroring the existing Codex/Antigravity entry points, and referenced
them from `AGENTS.md`, `AI_DEVELOPMENT.md`, `README.md`, `docs/README.md` and
`docs/WORKSPACE.md`. The guide records the explicit per-task model and
reasoning-effort choice because Minimax exposes no automatic selector, and keeps
provider credentials in user-level agent configuration outside the repository.
No PHP source, contract, baseline, architecture policy, DDL or test was changed,
so no verification level applies; this is an agent-documentation entry point only,
and no Minimax client run was executed or verified.

## Verified environment and retained decision 110 checks

Observed CLI: `C:/xampp/php/php.exe`, **PHP 8.2.12, x64, TS, Visual C++ 2019**.
Source remains PHP 7.4-compatible; Composer's platform target remains 7.4.33.
PHP 7.4 itself was not rerun. The base CLI now loads GD, PDO_ODBC, PDO_SQLite,
cURL, OpenSSL, DOM, iconv and mbstring without the duplicate OpenSSL warning.

`scripts/verify.ps1` invokes the existing verify.php and can add installed
GD/PDO_ODBC through a disposable INI when absent. Current runs used the base
loaded extensions. It restores PHP_INI_SCAN_DIR and removes any temporary
directory it creates. The agent did not change XAMPP php.ini, Apache,
services, PATH or production runtime settings. The wrapper
does not run the opt-in actual SQL lab.

The preceding decision 110 checkpoint selected all nine static module groups.
Its retained checks under PHP 8.2.12 with base GD/PDO_ODBC loaded were:

| Check | Result |
| --- | --- |
| `scripts/verify.ps1 -Level fast` | Passed: 459 PHP, 321 source, 26 JSON, 2 saved template fixtures |
| `scripts/verify.ps1 -Level affected -Changed ...` | Passed: 6,722 assertions; all nine groups plus private kernel suites |
| `scripts/verify.ps1 -Level migration` | Passed: 6,730 assertions |
| `scripts/verify.ps1 -Level complete` | Passed: 6,730 assertions |
| Focused `tests/plugin-schema-inspection.php` | Passed: 75 assertions, including Chaos/Order/Singularity composition |
| `scripts/test-mssql-odbc.ps1 -RunOwnedFixture` | Passed: 305 actual SQL assertions, including 41 existing administrator/settings HTTP cases; owned database removed |
| Edited supporting documentation | 91 local links resolve |
| Base CLI startup / modules | No startup warning; GD/PDO_ODBC and supporting extensions loaded |

Disposable reports:
`reports/verification-schema-inspection-{fast,affected,migration,complete}.json`
and `reports/mssql-odbc-integration.json`.
Complete verification includes real loopback health/ranking variants, existing
Admin/player/settings/catalog HTTP suites, full synthetic V1 ranking parity,
all frozen hashes and both unchanged saved template fixtures.
The owned SQL suite exercises exact physical types, namespace isolation,
restricted visibility, unsupported alterations/incoming dependencies, expected
digest changes and actual pending-DDL lock timeout, alongside existing journals
and HTTP cases. It selected only its new marked website database and removed it.
Lab-only self-signed certificate trust is not trusted TLS/deployment proof;
strictTlsValidated remains false. Plugin HTTP lifecycle, whole-site SQL cutover,
full editor coverage and live MU behavior remain unverified.

Initial actual inspector tests exposed ODBC's implicit wide-parameter byte-length
failure; explicit VARCHAR typing preserves the bounded ASCII namespace.
Removing a synthetic MAX column also left unsupported LOB storage metadata.
The inspector correctly continues to refuse that residue; fixture code alone
rebuilds its owned test table. Cursor cleanup preserves the first failure before
private redaction. All failed-run owned databases were removed. No check was
weakened or required test skipped; previous PHP 8.2 fixture compatibility remains.

Decision 110 final inventory: zero PHP processes and zero temporary `reports/php-cli-*` roots.
Historical previously rejected residues were not reopened/reused/restarted/removed.

## Verified installed website schema state (decision 113)

Step 03 item 1 is implemented and verified offline: the kernel now determines
actual installed source state and persists verified installed history. A fourth
closed journal family, `installation`, holds one canonical head of verified
plugin-owned website schema. `InstalledCommand`/`InstalledState` bind the
installed revision, the canonical footprint plus its digest, the approved
version/fingerprint, a unique adoption attempt and the verifying actor/time;
a claimed digest cannot replace the schema it names.

`InstallationService` requires separate administrator `plugins.inspect`,
`plugins.provision` or `plugins.migrate` on resource `admin` before any I/O,
then freshly rechecks bounded discovery, exact approval identity, reconciliation
against pending migration receipts and a read-only physical inspection before a
single acknowledged CAS commit of head and audit. The caller states both the
installed revision and the expected head revision, so an exact retry after a
lost acknowledgement replays instead of conflicting. Adopted state survives
folder removal and package enable/disable.

Added: `src/Kernel/Plugins/Domain/InstalledCommand.php`,
`Domain/InstalledState.php`, `Application/InstallationService.php`,
`src/Bootstrap/Plugins/InstallationComposition.php`,
`database/mssql/014-plugin-installation.sql` and `tests/plugin-installation.php`
(72 assertions, registered in every non-fast scope). `PdoJournalStore` gained
exactly one closed family; callers still cannot supply table names and
a cross-family command is refused.

Actual verification: `scripts/verify.php --level=complete` passed with
`PASSED [complete] foundation: 475 PHP files, 30 JSON files, 6897 assertions,
2 project fixtures`. The new suite covers canonical/adversarial state,
authorization before I/O, per-boundary refusal order, pending-attempt
reconciliation, adoption, replay, idempotency conflict, stale-CAS refusal,
physical drift reporting, folder removal, cross-family refusal, audit-trigger
rollback, forged audit history and two independent two-process SQLite races.

Not implemented and not claimed: the reviewed executor that applies
`MigrationOperationSet` operations atomically with the installed-state commit,
guarded operator provisioning/CLI or endpoint, configuration-schema migration,
and any HTTP/worker surface. Actual SQL Server evidence for DDL 014 and this
family is **not** yet part of the opt-in owned suite; only offline and
independent SQLite process evidence exists. No plugin is executable, enabled or
loaded, no SQL or migration resource is applied or executed, and no native game
or payment gate changed. See docs/PLUGIN_INSTALLATION_STORAGE.md.
## Style derivative over reviewed design tokens (decision 114)

Following the user's approval to create derivatives of the three primary APIs,
`style.resolve@1.0.0` is implemented as a focused private Singularity operation
governed by Order and proposable through Chaos, beside the existing URL and Math
derivatives rather than as a fourth primary API. It resolves one reviewed,
exactly versioned design value for a viewport width, mobile-first: the token base
value applies at every width and each override applies from its breakpoint minimum
width upward. The response reports the matched breakpoint and its `minWidth`, so
a consumer can emit one base declaration plus deterministic media queries.

Only an integer value and a `px`/`rem`/`%` unit cross the boundary. There is no
CSS text, selector, property name, declaration string, `url()`, `calc()`,
expression, comment or font payload in the profile, so a reviewed token cannot
smuggle authored CSS into a derived stylesheet, and existing template safety
rules are unchanged. Breakpoints must start at zero and increase strictly,
overrides may only target declared non-base breakpoints, and catalogs are bounded
to 128 tokens, 16 breakpoints and 64 KiB with values in 0-10000.

Reviewed data follows Bulma's published mobile-first breakpoint convention
(mobile 0, tablet 769, desktop 1024, widescreen 1216, fullhd 1408) plus a reviewed
spacing, column-gap/stacking, type-size, radius, font-weight and touch-target
scale. **No Bulma stylesheet, Sass, JavaScript, icon font or other third-party
asset is vendored, imported, copied or served**, and no Bulma code is in this
repository; it is cited as the numeric reference only under MIT.

Added: `src/Kernel/Styles/Domain/StyleCatalog.php`,
`src/Bootstrap/Apis/StyleResolve.php`, `WebsiteDefinitions::breakpoints()` and
`::styles()`, the `style.resolve` binding and builder payload in `UtilityApis`,
the Styles Domain owner in `architecture/policy.json`, `docs/api/STYLE.md`, the
integration map row and 23 new assertions in `tests/utility_apis.php`.

Actual verification: `scripts/verify.php --level=complete` passed with
`PASSED [complete] foundation: 477 PHP files, 30 JSON files, 6930 assertions,
2 project fixtures`. Coverage includes breakpoint ordering, unknown token/version/
viewport, unsupported units, non-integer and unbounded values, override targets,
CSS/expression/injection payloads, independent catalog snapshots, Order
invocation and refusal, and a Chaos-proposed plan executing the derivative.

Not implemented and not claimed: an MSSQL-backed editable token catalog, an
administrator token editor, theme switching, per-tenant customization or plugin
token overrides (which need the unimplemented MainAPI lifecycle). Derived CSS is
still the consumer's responsibility; existing views keep literal styles until they
migrate consumer by consumer.
## Building blocks and crafts law, and optional feature switches (decision 115)

The user fixed a design law on 2026-10-20: **derivative APIs are the building
blocks; webshop, AdminCP, the live template builder, rankings, markets and support
are the crafts assembled from them.** Blocks are perfected once and then versioned;
crafts are intentionally malleable and never final; configuration is optional and
only enables behavior that already exists; when two crafts need the same thing it
becomes a block; adaptability and potential come first, so persisted schemas are
declared and extensible rather than narrow.

The law was integrated into the three primary APIs without inventing a fourth:

| Law | Role | Documented at |
| --- | --- | --- |
| Singularity | A singular operation **is** a block: one verified responsibility, perfected once, never grown with craft logic | `docs/api/SINGULARITY.md` section 13 |
| Order | The registry keeps blocks stable: exact versions, capability gap instead of block edits, configuration that only enables and never grants authority | `docs/api/ORDER.md` section 13 |
| Chaos | Where a craft changes shape: compose plans from existing blocks, replan freely, raise a capability gap when two crafts need the same primitive | `docs/api/CHAOS.md` section 11 |

Normative law: `EVOLUTION_PRINCIPLES.md` 0.0 (Spanish, matching that document),
with an English summary in `AGENTS.md`. `docs/api/BUILDING_BLOCKS.md` holds the
block inventory, the three-law mapping and the **candidate** blocks that are not
implemented: price resolution, workflow/state transitions, selection
(filter/sort/pagination), presentation projection and an audit envelope. They are
recorded as candidates only and are promoted to blocks when a second craft needs
the primitive or the user asks explicitly.

To prove the law, `feature.resolve@1.0.0` was added as the fourth derivative and
the first block existing purely for optionality: a reviewed boolean default plus
unique boolean scopes, resolved with OR, reporting whether the default or a scope
produced the answer. It has `effects: none`, no required permission, and it cannot
create authority, widen a resource, reach MSSQL, execute a migration or bypass the
game/payment gate. Declared switches are reviewed composition, not editable
settings: no switch editor, no per-tenant override, no disk fallback.

Added: `src/Kernel/Features/Domain/FeatureCatalog.php`,
`src/Bootstrap/Apis/FeatureResolve.php`, `WebsiteDefinitions::features()`, the
`feature.resolve` binding and builder payload, the Features Domain owner in
`architecture/policy.json`, `docs/api/FEATURE.md`, the integration-map row, the
`API Summary.md` pointer and 19 new assertions in `tests/utility_apis.php`.

Actual verification: `scripts/verify.php --level=complete` passed with
`PASSED [complete] foundation: 479 PHP files, 30 JSON files, 6949 assertions,
2 project fixtures`. An earlier run of the same level **failed** with
`src/Kernel/Features/Domain/FeatureCatalog.php: undeclared kernel owner/layer`
because the new owner was not yet registered in `architecture/policy.json`; the
owner was declared and the full scope re-run passed. No check was weakened.

Not implemented and not claimed: no craft was migrated or assembled, no MSSQL
switch catalog, no admin switch editor, no plugin token/switch override, and no
MainAPI executable lifecycle. Frozen V1-V5, static modules and native
game/payment gates are unchanged.
## Blocks and crafts theory promoted to structural invariants (decision 116)

The user refined the building-blocks/crafts theory the same day it was fixed and
required it stop being an afterthought appended to the bottom of each document.
The theory now lives in the structural invariants, contracts and lifecycle of the
triad:

- `docs/api/SINGULARITY.md` section 13 rewritten with correct Spanish diacritics
  (the previous generated text contained concatenation typos such as
  `Lareutilizacion`, `unicoreutilizacion` and `con la artes`) and a five-point block
  contract: immutable behavior per exact version, agnostic to consuming crafts,
  two-craft promotion pressure, closed inputs with extensible persisted schemas,
  and no self-granted authority.
- `docs/api/ORDER.md` section 13 rewritten with the registry-and-gate framing.
  The fourth compatibility bullet in section 3 now **mandates exact-version
  pinning** for production plans and crafts; the previous text allowed implicitly
  admitted major ranges, which conflicted with the block-stability law.
- `docs/api/ORDER.md` section 6: the mandatory invocation algorithm grew from nine
  to ten steps. Optional enablement is now its own verified step **immediately before**
  actor permission and resource/tenant checks, and a disabled switch returns
  `CAPABILITY_UNAVAILABLE`. This makes "configuration only enables, never authorizes"
  executable rather than declarative.
- `docs/api/CHAOS.md` section 11 rewritten as the craft composer, and section 7's
  `capability_gap` payload upgraded with `proposed_version`, `kind`,
  `promotion_trigger: multi_craft_demand` and `consuming_crafts`, using price
  resolution with `decimal-string` amounts as the worked example. The Two-Craft Rule
  now exists in the machine contract, not only in prose.
- `docs/api/BUILDING_BLOCKS.md` gained the unified triad matrix (core essence,
  purpose, mutability, reuse rule, authority/configuration), the canonical-role and
  architectural-invariant table, a terminology note reserving *taller* for the
  authoring/replanning space, and a five-step lifecycle from single-craft incubation
  to explicit craft replanning.
- `EVOLUTION_PRINCIPLES.md` section 0.0 and the `AGENTS.md` summary were rewritten to
  match, including corrected relative documentation links: the canonical principles
  file lives at the repository root, so `docs/api/*.md` must link
  `../../EVOLUTION_PRINCIPLES.md`.

Verification: documentation and specification only. No PHP source, contract,
baseline, DDL or test changed, so `scripts/verify.php --level=fast` is the relevant
scope and it passed with `PASSED [fast] foundation: 479 PHP files, 30 JSON files,
2 project fixtures`. The complete scope verified earlier for decision 115 is
unaffected because no executable file changed.

Still not implemented and not claimed: every candidate block (price resolution,
workflow transitions, selection, presentation projection, audit envelope), any
assembled craft, any MSSQL-backed switch catalog and any MainAPI lifecycle. The
upgraded `capability_gap` contract is a specification; no Chaos proposal emitter
produces that payload in runtime yet.
## Reviewed declarative execution and the Language derivative (decisions 117-118)

Two slices completed after the blocks/crafts law was fixed.

**Decision 117 finishes the execution half of plugin step 03 item 2.** Installed
state is no longer only observed. A reviewed transition now applies website
schema and installed state in **one transaction**: PdoJournalStore validates the
command, runs the bound participant inside its own transaction, verifies the
postcondition, then writes head and audit and commits. A refused audit row rolls
the schema change back with it.

InstalledCommand gained an advance action (id, version, fingerprint, fromRevision,
toRevision, beforeDigest, afterDigest, planDigest, operations, attempt). The
operation set travels as data and is re-parsed through MigrationOperationSet
before anything can reach statement construction, and the resulting footprint is
derived from the verified baseline plus the operations rather than accepted from
the caller. A transition cannot create a baseline and refuses a mismatched source
revision, a drifted baseline, a reused attempt identity, a regressing clock, a
reversed transition and an afterDigest the operations do not produce.
PdoDeclarativeMigration builds statements only from already-validated identifiers
inside the plugin owned namespace; InstallationService::advance repeats discovery,
approval, receipt reconciliation and a fresh physical inspection before the
transaction; InstallationComposition binds the participant and only the
installation journal may bind one. DDL 014 now admits adopt and advance.

**Decision 118 adds locale.resolve@1.0.0**, the fifth derivative and the block
that makes the website multi-language: one named, exactly versioned message for
one declared locale carrying a text direction and exactly one reviewed fallback,
returning the resolved locale and whether the text was borrowed. Direction
follows the locale the text came from, so a right-to-left locale borrowing English
copy reports ltr. The block returns stored text only: no interpolation,
formatting, pluralisation, markup, HTML, URL or executable payload, because
placeholder and plural rules are candidate blocks awaiting the Rule of Two.
Reviewed locales: es-AR, es-MX, en-US, pt-BR and right-to-left ar-EG. There is no
translation editor, tenant override or disk fallback, and existing views still
carry literal strings.

Actual verification: scripts/verify.php --level=complete passed with
PASSED [complete] foundation: 483 PHP files, 30 JSON files, 7006 assertions,
2 project fixtures. The new coverage proves a real created table and an added
column, that a refused audit rolls the schema change back, that the same
transition then succeeds, that an exact retry replays without reapplying, that a
transition refuses without a bound executor or a non-administrator actor, plus
the locale fallback, direction, refusal and snapshot cases. An earlier complete
run failed with src/Bootstrap/Plugins/InstallationComposition.php: database access
is restricted to Infrastructure because a local variable was named pdo and the
token check matched it; the name was changed and the full scope re-run. No check
was weakened.

Still not implemented and not claimed: actual SQL Server execution of the
migration participant, guarded operator provisioning or CLI, HTTP or worker
surfaces, MainAPI executable plugin lifecycle, any assembled craft, and
consumer-by-consumer migration of existing views to the URL/Math/Style/Feature/
Language blocks. No plugin is executable or enabled, no native game or payment
gate changed, and no game write occurred.
## Next authorized work: finish plugin step 03

The requested initial builder and URL/Math derivatives are implemented above.
Broader named-URL consumers, explicit MSSQL template cutover and additional
reviewed formulas can migrate in separate verified slices. Do not claim complete
website conversion or turn generic derivatives into game/business modules.

Decision 108's logical MigrationOperationSet/MigrationSchemaFootprint already
exists and is verified offline; decision 107's migration review remains inert.
Approvals (105), scalar configuration and unverified attempt receipts (106) also
exist. Do not rebuild these as another generic registry or treat them as installation proof.

Decision 110 now supplies the read-only physical inspector; it does not invoke
the inert review automatically or certify installed state from an empty namespace.

1. Done and verified offline in decision 113: actual installed source state is
   determined and verified installed history is persisted atomically. Actual SQL
   Server evidence for DDL 014 is still absent.
2. Add explicit approval-bound execution with fresh package/approval/source checks,
   concurrency, drift refusal, postconditions, atomic installed-version/audit commit,
   interruption reconciliation and ambiguous-commit recovery.
3. Add guarded operator provisioning and explicit configuration-schema migration.
   Verify offline and in fresh owned website-only SQL targets before closing step 03.
4. Step 04 then derives the MainAPI HTTP host from Order and proves live folder
   add/approve/remove/readd plus retained MSSQL state. Steps 05–06 extend surfaces,
   workers and AdminCP; independent game/transport/schema providers follow.

Read [metadata storage](docs/PLUGIN_METADATA_STORAGE.md),
[approval storage](docs/PLUGIN_APPROVAL_STORAGE.md) and
[schema inspection](docs/PLUGIN_SCHEMA_INSPECTION.md), plus the
[implementation plan](docs/IMPLEMENTATION_PLAN.md) and existing MigrationPlan,
MigrationReviewService, MigrationCommand/State and PdoJournalStore.
Retain the reviewed public SDK/consumer/version workflow before allowing optional
plugins to consume new private boundaries.

## Continuing authority and safeguards

Phase 4 continuation and fresh owned SQL lab use remain authorized; no repeated
continuation approval is needed. Follow [MSSQL lab instructions](docs/MSSQL_LAB.md)
for private inputs and limits. Existing MU/account/ZendaWeb databases are not
owned empty migration targets. Kernel website MSSQL remains mandatory and cannot
depend on removable external-game DatabaseType providers.

Separate website administrator identity remains authoritative for AdminCP.
Native MU writes, payment fulfillment and template publication retain all gates.
Complete editor/feature/plugin migration and template publication remain pending.
Keep the original `C:/xampp/htdocs/ZendaMod` strictly read-only and never execute it.

Before local-server, SQL or temporary-root operations, read the retained
[local operation / residue restrictions](docs/archive/STATE-2026-10-19.md#local-operation-and-residue).
Do not reuse, restart or attempt alternate cleanup of any historical rejected
fixture listed there. Its private credential references are not runtime fallback.

STATE was updated last after actual verification.
