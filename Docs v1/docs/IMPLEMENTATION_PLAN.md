# Step-by-step implementation sequence

This is an execution plan, not a progress ledger. Read `STATE.md` first for the
current step and evidence, then `AGENTS.md`. Normative requirements remain in
`PLUGIN_SYSTEM.md`, `PLUGIN_DEVELOPMENT.md`, `API_ARCHITECTURE.md` and the four
API manuals. Complete one bounded step at a time; continue within the available
context, and leave a precise checkpoint rather than starting an oversized rewrite.

## Priority and compatibility

Primary API manuals: [Chaos](api/CHAOS.md), [Order](api/ORDER.md),
[Singularity](api/SINGULARITY.md). The four provider manuals referenced above
describe derived contracts. On the current Windows CLI use `scripts/verify.ps1`
for process-scoped GD/PDO_ODBC fixture support; it invokes the same verify.php.

Build the drop-in host first, then its external database/game/schema providers,
then migrate optional business features. Existing prepared-storage hardening is
retained work, not a reason to keep adding features to static modules. Address
storage prerequisites when their dependent step needs them.

- Three primary APIs: Chaos plans, Order governs, Singularity executes one operation.
- One trusted kernel; four independently versioned derived contracts: MainAPI,
  DatabaseTypeAPI, GameTypeAPI and SchemaAdapterAPI. Every plugin uses MainAPI.
- Kernel-owned website MSSQL and separate AdminCP identity work with `plugins/`
  empty. Optional external game MSSQL transport cannot supply website persistence.
- Physical presence controls executable availability; website MSSQL controls
  approval, enablement, permissions, migrations, licensing and retained settings.
- Preserve frozen V1-V5 and static consumers through reviewed compatibility
  adapters. Existing `src/Modules/*` and `packages/premium/*` remain until migrated.
- No legacy execution, existing MU/account database initialization, native game
  writes, payment fulfillment or template publication is enabled by this plan.

## Grounding in the current source

| Existing surface | Consequence for implementation |
| --- | --- |
| `public/index.php`, `src/Bootstrap/Application.php` | Current fixed request composition/routes; add one generic host integration boundary instead of a branch per plugin. |
| `src/Bootstrap/autoload.php`, `SourceLayout.php`, `composer.json` | Fixed core and two premium roots; retain compatibility and add bounded approved plugin autoloading without per-plugin Composer changes. |
| `architecture/*.json`, `scripts/verify.php`, `scripts/impact.php`, `scripts/lib/Checks.php` | Static declarations/checks need reviewed dynamic plugin support, not skipped architecture validation or a manually maintained installed-plugin list. |
| `contracts/v1..v5`, `src/Contracts/V1..V5` | Existing freezes stay unchanged. Review a distinct additive SDK namespace/catalog and teach verification its exact rules. |
| `tests/packages.php`, `tests/*-http.php`, `tests/fixtures/mssql-http/router.php` | Reuse owned process/HTTP fixture patterns and separate authority; copied-source absence tests alone do not establish runtime plugin removal. |
| `docs/GUIDE_ODBC.md`, decision 103, GameData guide adapters | Guide ODBC work already exists beyond the older STATE 102 narrative. Preserve it; guide SQL HTTP/calendar and whole-site TLS remain separate gaps. |

Inspect the precise files and nearest owner `AGENTS.md` again before editing
them. This table identifies starting points, not a completed implementation audit.
The concrete consumer map and selected SDK/storage boundaries are in
[PLUGIN_RUNTIME_MIGRATION.md](PLUGIN_RUNTIME_MIGRATION.md); canonical distribution
rules are in [PLUGIN_MANIFEST.md](PLUGIN_MANIFEST.md). Use STATE for their actual
implementation/verification status.

## Three-API integration before continuing

Decision 109 supplies the private bounded-read bridge under Kernel/Apis and
Bootstrap/Apis. See [the exact profile and consumer map](api/WEBSITE_INTEGRATION.md).
Health and synthetic ranking projections use Order; Chaos can plan independent
reads without running them. Other static actions retain their current guards.
This does not close step 03 or replace the public plugin SDK work.

Decision 110 supplies private read-only physical MSSQL schema inspection; see
[its limits](PLUGIN_SCHEMA_INSPECTION.md) and STATE for actual evidence.
Continue step 03 with verified installed state, explicit approval-bound
execution, reconciliation and provisioning. In
step 04, derive MainAPI from Order and register focused Singularity capabilities
into the request snapshot. Preserve the canonical distribution manifest v1.
Publish primary/derived public SDK catalogs only after consumer/version review;
never expose the private Kernel owner directly to optional feature modules.
The exit checks for all fourteen steps remain mandatory.

## Ordered steps and exit checks

### 01. Reconcile the design and map consumers

Read the plugin/API specifications and required architecture/contract/database
docs. Inspect actual bootstrap, routing, auth, settings, workers, templates and
verification tooling. Run impact analysis for every proposed changed path.
Record a concise migration matrix: owner, current binding, future extension
point, consumers, compatibility adapter and tests.

Resolve the differing illustrative manifest formats (`api` versus
`contracts.mainApi`, dependency versions and provider declarations) into one
canonical schema. Decide SDK namespace/version catalogs, trust fingerprint scope,
route precedence and kernel storage ownership before freezing signatures.
Do not accept multiple contradictory formats silently or introduce core imports
of Admin/private plugin Infrastructure.

**Exit:** documented concrete file/contract changes, one canonical manifest
design and tests to prove it; existing complete verification establishes baseline.

### 02. Build bounded data-only discovery

Implement direct-child enumeration, manifest validation, package integrity and
redacted diagnostics. Reject traversal, symlinks/junctions/reparse escapes,
case/namespace collisions, oversized manifests, incomplete packages and unsafe
autoload paths. Fingerprint executable resources and bundled dependencies, not
only `plugin.json`; changed code invalidates prior approval. Do not load PHP
during discovery. Keep deployment staging outside the scanned directory.

**Exit:** empty root works; malformed/unapproved packages cannot execute even a
sentinel PHP side effect; Windows filesystem cases are tested or explicitly missing.

### 03. Add authoritative kernel plugin state

Approval storage design and its internal boundary are documented in
[PLUGIN_APPROVAL_STORAGE.md](PLUGIN_APPROVAL_STORAGE.md). Read STATE for which
parts are implemented. Decision 106 adds separate configuration and unverified
attempt receipts; read [PLUGIN_METADATA_STORAGE.md](PLUGIN_METADATA_STORAGE.md).
Decision 107 adds read-only plan/package/approval review; its packets grant no
migration execution or installed-state proof.
Verified installed migration state/execution and guarded operator provisioning
remain required before this step is complete.

Introduce reviewed website-owned MSSQL approval/install/configuration/migration
and audit storage with explicit provisioning. Preserve separate administrator
authorization before connection/action. Commands own transaction, CAS, durable
request identity, affected-row/readback/commit checks and audit. Retain records
on removal; no constructor/request DDL, auto-approval, seeding or file fallback.
Use explicit offline fixture stores for ordinary tests and fresh ownership-marked
SQL lab targets for actual SQL verification. A fixture success is not MSSQL proof.

**Exit:** approval/disable/retry/conflict/corruption/rollback/race tests pass;
changed fingerprints require approval; kernel SQL has no optional provider dependency.

### 04. Deliver the first running MainAPI slice

Implement the minimum additive MainAPI entrypoint, scoped registration, dependency
resolver, trust checks, plugin-local autoloader and immutable request registry.
Wire it once into actual HTTP dispatch. Reject cycles, collisions and attempts
to override kernel auth/AdminCP routes; discard partial registrations on failure.
Missing required dependencies block dependents; optional ones return unavailable.

Provide a small read-only first-party example plus an independent test plugin.
Use a complete approved folder, not a hardcoded plugin ID. Recheck physical
presence/fingerprint each request and before dispatch; never revive stale DB,
classmap or cache entries. Retain the existing static runtime during this slice.

**Exit:** with the same HTTP server running, add -> approve -> route works ->
remove whole folder -> next request unavailable -> re-add unchanged folder ->
route and retained setting return. Core/AdminCP and independent plugin work
throughout. Modified code remains pending. This is the first vertical runtime
milestone; steps 01-03 alone must not be reported as a working plugin CMS.

### 05. Complete scoped surfaces and removal behavior

Add AdminCP pages/menus, typed services/capabilities, events, jobs, commands,
assets, translations and versioned widget definitions through MainAPI. Keep
registration declarative and side-effect-free. Check authorization at handlers,
not just menu rendering. Revalidate long-running workers before every claim and
dispatch; document that already executing requests/jobs cannot be hot-unloaded.
Missing widgets render safe placeholders while preserving saved IDs/versions/JSON.

**Exit:** every registered surface passes live add/remove/readd checks across
two independent HTTP workers and a scheduler. Test stale caches, dependency
loss, partial copy, namespace/route conflicts and independent providers. Template
references survive unchanged; missing assets cannot execute or expose private files.

### 06. Add the AdminCP plugin lifecycle UI

Expose present/missing/active/disabled/blocked/pending states, provenance,
versions, requested versus granted permissions and useful redacted reasons.
Implement CSRF-protected, throttled, authorized approve/enable/disable commands
with revision/replay/audit boundaries. Migrations remain a separate explicit
operation; removal performs no purge. ZIP upload is a later optional feature.

**Exit:** player identity/same-name account/GM flag cannot approve code; denied
actions do not connect or change state; concurrent approval and changed-code
approval races fail closed; empty plugins still allow administrator access.

### 07. Introduce DatabaseTypeAPI for external reads

Implement the independently versioned transport registry and a removable
`zenda.db.mssql` provider. Expose bounded metadata and typed approved read plans,
not PDO, arbitrary SQL, DSNs or secrets to feature plugins. Persist game/account
connection profile references in website MSSQL with explicit principal scopes.
Keep emulator-specific tables out of the transport. Verify real driver behavior
only in designated synthetic targets; retain strict application TLS.

**Exit:** removing the transport blocks only its bound external profiles;
website MSSQL/login/AdminCP continue. Test read-only grants, query bounds,
Unicode/binary handling, timeout/TLS/permission failures and secret redaction.

### 08. Introduce GameTypeAPI and the MU read model

Add the independently versioned game registry and `zenda.game.mu` package with
normalized, permission-filtered read capabilities. Use a synthetic second game
only as a contract fixture to prove the kernel/consumer has no MU switch; do not
implement Minecraft/Metin2 products. MU/provider work reads the MU development
skill and all relevant safety specifications before implementation.

**Exit:** game presence alone grants no schema/read/write capability; removing
MU blocks only its selected profiles; realm IDs and optional capabilities remain
isolated. Unsupported reads return typed unavailable rather than fabricated data.

### 09. Introduce SchemaAdapterAPI and explicit profile binding

Implement a separate adapter registry and one reviewed emulator fixture package
(for example `zenda.schema.muemu`). Probe read-only; distinguish candidates from
verified mappings. Bind explicit game + transport + schema provider IDs/versions
per realm with revision/history in website MSSQL. Unknown/ambiguous/mixed layouts,
unknown codecs and schema changes revoke affected capabilities. No first-match
selection, automatic alternate provider or schema-probe write authorization.

**Exit:** independently add/remove/readd game, transport and schema folders;
only selected profiles/required consumers block. Cover wrong game/engine/schema,
two matching adapters, changed fingerprints and multi-realm isolation. Real MU
compatibility remains unverified until designated adapter-specific evidence exists.

### 10. Ship the normalized feature proof

Implement `zenda.rankings` using only `game.rankings.read@1` for an explicitly
selected realm. No native SQL or provider-private imports. Verify against fixture
providers, then designated real read-only evidence when available. Migrate a
safe existing read-only feature through a compatibility adapter and compare its
URLs, authorization, output escaping and template consumers before retiring its
static binding. News continues to consume Drupal published projections.

**Exit:** swapping compatible fixture providers requires no consumer changes;
provider loss affects only dependent projections. Full package add/remove/readd
and old consumer/template tests pass. Fixture output is labeled synthetic.

### 11. Migrate remaining features and commercial boundaries

Use the migration matrix one owner at a time: free news/rankings/profiles/guides
and other website features, then premium inventory/warehouse/webshop/markets/
guilds/siege. Keep existing Webshop/VipShop freezes and independent packages
until equivalent new plugin behavior is verified. Free PayPal/MercadoPago and
free templates cannot depend on paid packages. Add licensing at actual service
boundaries; no stored license/catalog row grants native fulfillment.

**Exit per package:** complete independently distributed folder, owned MSSQL
state, declared public dependencies and lifecycle/consumer tests. Remove a static
binding only after equivalence evidence; eventually only foundation stays required.

### 12. Integrate the free GrapesJS host and template bundles

Read both template specifications and owner instructions. Self-host reviewed
pinned editor assets; canonical JSON, stable component IDs, drafts/revisions and
publication remain website-MSSQL-owned. Wire plugin widget schemas into the host;
preserve missing-plugin JSON and safe rendering. Keep free/premium bundle assets
physically separate. Build tooling must not require permanent Node hosting.

**Exit:** real browser save/reload/undo/responsive and missing-provider tests;
private synthetic preview; executable content/upload rejection. Enable publication
only after its separate validated pipeline and consumer compatibility checks.

### 13. Implement installer, recovery and website-only reset

Follow `docs/INSTALLATION_LIFECYCLE.md`: authoritative MSSQL lifecycle state,
surviving separate setup/recovery authority, explicit ownership/retention,
audited transitions and interrupted-operation recovery. Empty plugins install
and recover. Rehearse factory reset only in fresh owned fixtures, with unified
database ownership tests ensuring MU/account tables remain untouched.

**Exit:** install/retry/recovery/reset interruption and authorization tests;
reviewable reset scope and recovery plan. No permission to erase existing data
or execute a real reset is implied by implementing the workflow.

### 14. Close storage/deployment and native capability gaps

Finish remaining website MSSQL families, Drupal configuration compatibility,
guide SQL HTTP/calendar, full default-factory HTTP, trusted TLS, backup/restore
and load sizing before claiming deployable production persistence. Track full
legacy parity against `docs/discovery/MIGRATION_MATRIX.md` rather than guessing.
Native mutations and commerce require a separately verified coordinator:
ownership, authoritative offline verification, cross-worker/game synchronization,
recheck under lock, CAS, idempotency, immutable audit and recovery. Payment
callbacks need provider verification and atomic replay-safe fulfillment.

**Exit:** evidence is specific to each adapter/operation/environment. Missing
native/payment/deployment verification keeps those capabilities unavailable.

## Checks and session handoff

Before edits, run impact with the actual proposed paths. During development run
fast; before integration run complete; run affected for changed consumers and
migration when contracts, project formats or persisted data change. These are
offline checks; opt-in owned SQL and real browser evidence are additional.

```powershell
& 'C:/xampp/php/php.exe' scripts/impact.php --changed=path1,path2
& 'C:/xampp/php/php.exe' scripts/verify.php --level=fast
& 'C:/xampp/php/php.exe' scripts/verify.php --level=affected --changed=path1,path2
& 'C:/xampp/php/php.exe' scripts/verify.php --level=complete
& 'C:/xampp/php/php.exe' scripts/verify.php --level=migration
```

At a checkpoint, synchronize relevant specs and update `STATE.md` last with the
completed step/substep, changed paths, actual command/results, missing evidence
and one concrete next action. Do not keep a second status checklist here. An
unfinished step stays unfinished even when interfaces or fixtures pass.

Resume prompt for a fresh session:

> Continue ZendaMod Next. Read STATE.md first, AGENTS.md next, then
> docs/IMPLEMENTATION_PLAN.md and the specifications needed by the current step.
> Implement the next unfinished bounded step recorded in STATE, preserving
> frozen V1-V5, kernel website MSSQL, separate AdminCP identity and legacy
> read-only/native-write restrictions. Run impact and appropriate verification,
> record actual evidence, and update STATE last. Continuation is authorized;
> do not ask again for the existing Phase 4 approval.
