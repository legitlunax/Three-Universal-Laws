# ZendaMod Next: Plugin-first Architecture (normative target)

The primary APIs are Chaos, Order and Singularity. The layered plugin contracts below specialize them: MainAPI is Order lifecycle/registration; game/transport/schema operations are Singularity contracts governed by Order. The new internal bounded-read bridge does not implement this document's folder-plugin lifecycle. See [API architecture](API_ARCHITECTURE.md).

## Layered APIs (explicit architecture requirement)

`PluginAPI` is the **MainAPI / PluginHost API**. The plugin ecosystem must additionally expose three independently versioned, dynamically registered extension interfaces: **GameTypeAPI** (game family and normalized gameplay capabilities), **DatabaseTypeAPI** (optional external/game database transport types, never the kernel website MSSQL store), and **SchemaAdapterAPI** (verified emulator/schema mapping for a given game + database engine). See `API_ARCHITECTURE.md`, `MAIN_API.md`, `GAME_TYPE_API.md`, `DATABASE_TYPE_API.md` and `SCHEMA_ADAPTER_API.md` for contracts and examples. Each provider is a normal removable `/plugins/<id>/` folder, not an `if (game == ...)` branch in the kernel. Remove game, driver or schema provider: only bound profiles/dependents become unavailable; AdminCP, website-owned MSSQL and independent features keep operating. These API interfaces are **planned**, not available in the current runtime. Do not treat driver discovery as validated emulator/game schema or native write safety.


**Status: specification only.** The provided repository documentation does **not** establish an executable general-purpose plugin manager. This file sets the intended architecture and tests; it is not a claim that copying a folder already works. `STATE.md` remains the authoritative implementation handoff. See `PLUGIN_DEVELOPMENT.md` for package authoring and `PLUGIN_IMPLEMENTATION_PROMPT.md` for an execution plan for Codex/Antigravity.

## 1. The non-negotiable experience: folder in = available, folder out = gone

The desired experience resembles Minecraft Paper/Spigot plugin deployment, applied to a PHP CMS. The operator installs a trusted, compatible plugin by placing **one complete, self-contained directory** in `plugins/<vendor>.<plugin>/` (or by safely extracting a ZIP to that directory). A complete directory is discoverable without changing any core source, Composer configuration, central hardcoded module mapping, or other plugin.

When that directory disappears, **on the next HTTP request**, all of its contribution surfaces are absent: routes, menus, page handlers, APIs, event listeners, jobs not yet claimed, commands, GrapesJS blocks, template widgets, assets and payment providers. Missing files never cause `require`/`include` fatals, dangling menus, phantom endpoints or the accidental execution of cached plugin definitions. Its own saved website-owned data and configuration stay intact for possible restoration. Adding back a compatible, trusted version resumes it after normal checks; no hidden reinstall or destructive migration is permitted. Other unrelated features keep running.

**Important distinction:** presence is necessary but not sufficient for executable activation. Newly supplied executable code is untrusted until authorized. A known, administrator-approved, previously installed plugin with an unchanged trusted publisher identity and valid policy may automatically resume as soon as its directory returns. An unknown or changed plugin is discovered and listed as **pending approval**, not silently executed. Initial migrations/permissions/licenses require explicit review. Once trusted, the normal operator workflow is indeed copy/remove only. A stricter trusted-distribution policy may allow auto-activation of preapproved signed first-party packages, but never auto-run arbitrary PHP dropped by an attacker.

The **core must not need a plugin folder or database plugin row in order to boot**. A removed optional module must never be a bootstrap dependency. Removal is not uninstallation or data erasure. Removing any code while its PHP request is already running cannot rewind that request; guarantees cover subsequent requests and workers checking freshness before claiming a new job. Native hard-kill/isolation guarantees require separate processes and cannot be promised by ordinary in-process PHP.

### Acceptance behavior (a mandatory example)

1. Start with `plugins/zenda.ranking` and `plugins/zenda.webshop` installed, approved and enabled. Verify both, core/AdminCP and an unrelated `zenda.news` route are functional.
2. Rename/move `plugins/zenda.webshop` out of `plugins/` **while PHP/server remains running**. On the next request its routes respond with an explicit unavailable/404 result, admin menus and GrapesJS blocks are gone, API calls refuse, pending jobs are not claimed, and a saved page using its widget displays a safe missing-provider placeholder.
3. Core/AdminCP, rankings, news, profiles and every plugin with **no required dependency** on webshop still work; neither bootstrap nor CLI verification fatals. No other package has been edited or rebuilt. No SQL data is erased.
4. Return the **same previously approved package** to the folder. With database state and permissions unchanged it can resume without rewriting the site, subject to runtime compatibility/license checks. Existing preferences/data remain available. Repeat across two independent HTTP workers and an independent scheduler.
5. Remove a provider with *declared required dependents*: dependents are marked `blocked_dependency`, not broken/fatal; independent plugins still work. A provider referenced *optionally* is absent gracefully. A provider must never be silently replaced by a different provider.

## 2. Directory model and ownership

```text
ZendaMod-Next/
├── public/                         # Only public webroot; no executable plugin path exposed
├── src/
│   ├── Kernel/                     # bootstrap, router, plugin manager, auth, event bus
│   ├── Contracts/V1..Vn/           # immutable published ports
│   └── Modules/                    # currently existing packages; migrate incrementally
├── plugins/                        # The drop-in ROOT scanned at runtime
│   ├── zenda.news/                 # completely independent free plugin package
│   │   ├── plugin.json             # data-only manifest, no PHP execution during scan
│   │   ├── src/Plugin.php           # optional entrypoint loaded only when trusted
│   │   ├── assets/
│   │   ├── migrations/
│   │   ├── views/
│   │   └── tests/
│   ├── zenda.webshop/              # physically separate premium package
│   └── vendor.example/
├── packages/premium/               # existing legacy packaging until explicitly migrated
└── tests/
```

Each `plugins/<id>` package owns all its feature-specific PHP, templates, translations, assets, migrations, configuration schema, tests and bundled dependencies (where compatible). **Do not put free and premium implementations inside one mixed plugin folder**, and do not make free/optional packages import premium internals. `packages/premium/*` and existing `src/Modules/*` must be transitioned through reviewed adapters, not deleted or bulk moved without equivalence tests. `plugins/` is the **single operational discovery root** for the eventual system; packaging tiers describe independently distributed packages, not mandatory additional roots.

A *small mandatory kernel* includes routing, discovery/validation/loader, contractual service registry, event bus, security and administration foundation, separate administrator authentication, migrations orchestration, owned website configuration, observability and optional GrapesJS host integration. Business capabilities (news, rankings, game connectors, guilds, inventory, webshop, payments, player features etc.) should ultimately be installable plugins, including first-party free plugins. This is a target modularization, **not a claim that all currently implemented features have already migrated**. A minimal installer and health/status UI must work with an empty `/plugins` directory.

## 3. Manifest and stable plugin identity

Each directory must contain exactly one supported `plugin.json` data document.
Decision 104 selects [the canonical v1 format](docs/PLUGIN_MANIFEST.md), including
independent API ranges, versioned dependencies/providers and a complete SHA-256
file inventory. Its data-only parser/discovery is implemented; decision 105
adds internal approval persistence. Operator UI, executable registration and
HTTP/worker activation remain pending. Earlier illustrative
`api`/string-range formats were never installed contracts and are rejected.

The manifest contains **declarations, not executable actions**. Validate UTF-8/JSON/schema, ID equals directory name, maximum length/depth/total size, version/API intervals, relative paths, canonical case, complete/unambiguous directories, file integrity and publisher/trust policy. Deny symlinks, junctions, traversal, double extensions, malformed files, duplicate IDs, duplicate route IDs, path conflicts and case-fold collisions. Enumerate direct children only and never execute a PHP file just to discover its metadata. `plugin.json` describes *requested* capabilities and permissions; they do **not** grant authority without server-side approval/checks. Executable entrypoint names and namespace prefixes must be canonicalized and loaded **only** from that exact approved directory through bounded plugin-scoped autoloading. No global uncontrolled recursive `include` or `eval`.

A folder being removed must revoke its **runtime** contributions regardless of previously persisted status; installed-state rows are audit/history/configuration, not proof that executable files still exist.

## 4. Runtime services and the registration protocol

The kernel owns a small, stable, versioned `PluginAPI` exposed only to approved compatible plugins. Proposed interfaces, introduced **additively** without changing frozen V1-V5 contracts:

- `PluginManager` / `PluginDiscovery`: deterministic file discovery, fingerprinting, trust and compatibility resolution, dependency graph, diagnostics.
- `PluginContext`: scoped actor-independent registration context (not a mutable universal global or a raw application container).
- `RouteRegistry`: route ID, path, verb, middleware/permission, handler factory, priority with explicit collision rejection.
- `EventBus`: typed event subscriptions dispatched only to currently-active providers; idempotent external callbacks and queue messages.
- `ServiceRegistry` / `CapabilityRegistry`: stable versioned service contract, provider selection, `hasCapability()` and safe unavailable responses. No imports of another plugin's Domain/Application/Infrastructure code.
- `AdminExtensionRegistry`: plugin menus, tabs, settings screens and UI permissions. Display does not replace server-side authorization.
- `WidgetRegistry` / `TemplateRegistry`: server-provided component schema, GrapesJS blocks, dynamic projections, versioned component IDs and safe placeholders when absent.
- `JobRegistry`, `AssetRegistry`, `CommandRegistry`, `TranslationRegistry`, `SettingsAPI`, `StorageAPI`: plugin-local ownership and namespacing.
- `PaymentGatewayRegistry` / `GameAdapterRegistry`: gateway and game integrations *only* through explicit secured domain contracts. No permissions or game mutation are implied by installation.

`register(PluginContext $context): void` is called **only after** validation, trust, compatibility, dependency resolution and activation permission. It registers **declarative scoped contributions**. It must not perform SQL migrations, grant permissions, mutate player/game data, send money, create administrators, or run network requests as a discovery side effect. Defer heavy connections until an authorized application handler executes. Use safe cancellation/absence checks when dispatching listeners/jobs. Each registered ID must be namespaced by its owning plugin and may not override kernel routes or unrelated providers.

The first implementation can be a PHP 7.4-compatible modular monolith. Do not introduce mandatory always-on Node/RabbitMQ/Docker infrastructure to satisfy drag/drop. The free builder may compile JS at build time; production PHP hosting must not need a permanent Node process.

## 5. Lifecycle, detection and crash safety

State machine (website-owned MSSQL authoritative for installed configuration and approvals):

```text
ABSENT (no folder) <--- physical removal --- ACTIVE / DISABLED / BLOCKED
   |                                                 ^
   +-- folder added --> DISCOVERED --> PENDING_TRUST --> APPROVED
                                     (first trust)        |
                                                  CHECKED_COMPATIBLE
                                                          |
                                                  PENDING_MIGRATION
                                                  (if reviewed DDL)
                                                          |
                                                      ACTIVE
                                                          |
                                                  DISABLED / BLOCKED
```

Not every addition requires a manual click: known trusted and fully installed unchanged packages can transition `DISCOVERED -> ACTIVE` automatically on the next request if the saved operator policy allows it. Unknown/modified code **must** remain pending until approved. `ABSENT` overrides all persisted statuses; `BLOCKED` includes missing required dependency, invalid signature, incompatible API/core/PHP, invalid license, unverified schema or corrupt stored state.

**Runtime presence freshness:** at the start of every HTTP request perform a cheap bounded filesystem enumeration of direct `plugins/` children and read the manifests/fingerprints required to validate the active set. Build a request-local immutable contribution snapshot, or compare against a cache keyed by the complete discovered-set fingerprint *and verify it against the current directory set first*. A stale DB/cached catalog can never resurrect a missing plugin. Route dispatch must first revalidate its provider is present; scheduler, queue workers and CLI processes revalidate before claiming/processing work. For long-running workers, use a generation token/rebuild and do not reuse stale services. Never load a plugin from an old Composer optimized classmap if its source disappeared. Handle Windows rename/deployment atomicity with staging outside the scanned root and promote only complete directories; incomplete copies are `PENDING_INCOMPLETE`, not executed.

A bad plugin may be quarantined or disabled based on validation, explicit exceptions, circuit-breakers and health checks. **In-process PHP cannot guarantee protection against fatal engine errors, `exit`, infinite loops or arbitrary malicious code**. Untrusted plugins require external process/container isolation with an authenticated, narrowly scoped API, otherwise executable plugins are a *trusted-code* boundary. Document this clearly in AdminCP. Do not claim a safe sandbox merely because an API is narrow.

## 6. Dependencies and removal semantics

Required dependencies must be declared by stable ID and compatible API/semantic versions. Resolve deterministically, detect cycles and reject ambiguous providers. When A requires B and B is missing, A becomes `BLOCKED_DEPENDENCY` and adds no routes/listeners/jobs; it does not crash kernel. Optional dependencies are checked at the point of use and the caller must provide explicit degraded behavior. **No undeclared reach-through** to classes, static files, SQL tables, global helpers or private routes of another plugin. The kernel must not require an optional provider's entrypoint at bootstrap, even for premium shop route generation.

Removal is intentionally nondestructive: plugin-owned MSSQL tables/rows, versioned settings and historic content **remain**; no `onRemove()` PHP callback is necessary (it cannot run if code is absent) and no automatic `DROP TABLE`, revoke of historical data or player-account mutation occurs. AdminCP can separately offer a reviewed **permanent uninstall and purge** workflow with backup, permission checks and scope verification; it never touches MU native tables. Re-adding resumes only after checking schema and package migrations against retained state.

Removing a plugin with saved template references must not destroy project JSON or blindly rewrite published content. Read renderer/editor resolves a missing component to escaped safe placeholder (public fallback could omit block; editor shows diagnostic) with no fatal error; remove its editor block from the insertion palette, but preserve saved ID/schema/version for later restoration. Record dependency health in administrative diagnostics. A removed plugin's direct URL returns controlled 404/410 or a defined unavailable screen, never a partial route with leaked internal errors. User content and core routes remain unaffected.

## 7. Website MSSQL, migrations, and game safety

`plugins/` holds **code and immutable distribution resources only**. Configuration (including approvals, disablement, granted operations, installed versions, package fingerprints, plugin settings, migration ledger and entitlement state) belongs in website-owned MSSQL and is revisioned/audited; no JSON/file fallback in production. Secrets remain in approved secret storage, never manifest/client JSON/logs. The offline SQLite/file sandbox remains an explicit development-only exception until reviewed migration. Plugin migrations require admin authorization, backups/rehearsal, ownership-scoped additive DDL, transaction/commit checking where supported, explicit schema/version metadata and rollback/recovery plan. Do not run migrations on every HTTP request or merely because a folder appeared. A removed plugin must not trigger DDL/data removal or a migration during discovery.

Only plugin-owned Infrastructure adapters may touch DB/transport. A `Webshop`, `Workshop`, `ItemMarket`, `GameAdapter` or gateway plugin **must not bypass** the existing protected native game mutation, escrow, payment verification, idempotency, locks, actor/permission separation, verification and audit rules. Claims of native support require specific verified adapter evidence. Premium plugins require license checks on actual service boundaries, not just a visible menu flag. Free plugin packages (including free PayPal/MercadoPago gateway modules) must operate without commercial code.

## 8. GrapesJS is a separate kind of drag-and-drop

**Two distinct user experiences:**
1. **Filesystem drag/drop**: place/remove plugin *folders* under `/plugins`; integrates capabilities into the CMS.
2. **Editor drag/drop**: drag **visual blocks** onto a page in the planned GrapesJS editor. A plugin may expose widgets/blocks, but the visual editor must not itself execute arbitrary plugin PHP/SQL from editable project JSON.

The GrapesJS builder remains free core. Plugin-provided blocks are defined by versioned schema/registered read projections, not direct PHP markup from the browser. Existing project JSON, revisions and publication pointers survive plugin removal and restoration. A `PluginAPI` compatibility change must have an additive contract version or an explicit migration for old packages/projects.

## 9. First-party features become normal plugins over time

Do **not** add a special privileged plugin mechanism for today's built-in modules. Design `zenda.news`, `zenda.rankings`, `zenda.support`, `zenda.game.mu`, `zenda.webshop`, `zenda.market`, `zenda.guilds`, `zenda.payment.paypal`, `zenda.payment.mercadopago`, `zenda.events`, `zenda.loot`, `zenda.referrals` and similar packages using the same `PluginAPI`. Every package may be absent when not essential to the kernel. Core-only administrator and installer functions are exceptions necessary to run an empty plugin folder.

Existing `architecture/modules.json` / `capabilities.json` are **current static responsibility catalogs**, not a plugin discovery implementation. During migration, represent old modules through explicit compatibility adapters where necessary, without changing their frozen contracts. The intended eventual runtime registry is generated from trusted installed manifests **plus explicit core services**, not from manually adding an entry to a static central PHP switch statement every time a plugin is dropped. Keep a reviewable derived dependency/capability graph for tests, without turning it into a second authoritative list of which folders exist.

## 10. Mandatory end-to-end proof before declaring the goal complete

Tests must actually start a local PHP server against a sandbox with a real plugin directory, create a signed/approved or admin-trusted test plugin, add/remove/readd it while the server remains running, verify the next HTTP request, routes/admin page/event/asset/widget/scheduler behavior, compare unaffected core/plugin outputs, and inspect DB state. Cover cache invalidation across separate workers, missing manifest, broken PHP, wrong namespace, duplicate IDs/routes, symlink/junction escape, incomplete uploads, unsupported versions, cycle/missing dependency, disabled/licensed-off state, permission denial, migrations rollbacks/false commits and app/worker restarts. Include a fixture verifying **zero enabled plugins** still boots and AdminCP can report diagnostics.

No artificial interface-only or mock-only test counts as filesystem hot unplug/replug evidence. Record implemented vs pending and exact tests in `STATE.md` last. Never modify frozen `contracts/v1..v5` signatures/baselines to satisfy this. This specification is not a request to alter production MSSQL, live MU accounts, or the read-only legacy site.
