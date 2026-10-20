# ZendaMod Next: instructions for every agent

## Three primary APIs and derived plugin contracts

The user selected **Chaos API, Order API and Singularity API** as the three
primary APIs (decision 109). Read `PROJECT.md`, `API Summary.md`,
`docs/api/WEBSITE_INTEGRATION.md` and `API_ARCHITECTURE.md` after STATE.
Chaos proposes plans without authority; Order governs contracts, identity,
resource authorization and execution; Singularity performs one focused operation.
Known operations can call Order directly. No LLM is required.

`MainAPI`/PluginHost is a specialized Order lifecycle/registration contract.
`GameTypeAPI`, `DatabaseTypeAPI` and `SchemaAdapterAPI` define separately versioned
Singularity provider contracts mediated by Order. Every future plugin still
enters through scoped MainAPI registration. Preserve canonical plugin.json v1
keys and frozen V1-V5; new primary APIs do not authorize a manifest/baseline rewrite.
The initial private Kernel/Apis read profile is not a finished public plugin SDK.
Existing modules remain transitional until their operation/HTTP consumers migrate.

Never confuse game type (MU), engine (MSSQL) and emulator schema (MuEmu/Louis).
Website-owned MSSQL is kernel infrastructure, independent of removable external
game transports. Features consume normalized read ports; schema probes never
authorize native writes. Before provider work read `MAIN_API.md`,
`GAME_TYPE_API.md`, `DATABASE_TYPE_API.md` and `SCHEMA_ADAPTER_API.md`.

Read `STATE.md` first, then the relevant document. The original site is
`C:/xampp/htdocs/ZendaMod`; it is READ ONLY. Never run its bootstrap, requests,
scheduler, installers, tests, or database tools during discovery. Do not copy
installed settings, player data, credentials, licenses, or third-party assets.
All new work belongs in this project. Do not require legacy PHP at runtime.

## Primary architectural mandate: Minecraft-style drop-in website plugins

The end-state of **ZendaMod Next is plugin-first**, NOT only a modular monolith with
static service composition. A trusted, compatible complete folder
`plugins/<vendor>.<slug>/` registers itself with a versioned `PluginAPI` without
editing core/routes/Composer mappings/another package. Removing that folder must
revoke all its contributions on the **next request** while the kernel and unrelated
plugins keep operating, without erasing plugin data; re-adding an approved version
can resume retained MSSQL state. New untrusted executable code requires approval.
Core should start with an empty plugins directory. See `PLUGIN_SYSTEM.md` for the
normative semantics, `PLUGIN_DEVELOPMENT.md` for the authoring contract, and
`PLUGIN_IMPLEMENTATION_PROMPT.md` for the implementation assignment.

**This is a requirement, not an implemented feature.** Existing `src/Modules/*`,
`packages/premium/*`, static `architecture/*.json`, partial offline slices and
V1-V5 contracts remain until explicitly migrated and tested. All module work
should prefer new runtime plugin extension points; avoid adding business features
to the kernel. Filesystem folder drag/drop is distinct from GrapesJS visual blocks.
Keep the legacy read-only rules and all native MU/payment/permission safety gates.
Read `PLUGIN_SYSTEM.md` immediately after `STATE.md` for extension-related work.

## Current authorization

The user approved continuation after the phases 1–3 review on 2026-10-17:
"just the possibility, if everything is ready continue". Phase 4 implementation
is authorized; do not request this same approval again. MU Online is the first
target. Preserve the ability to add game adapters later without implementing
Metin2/Minecraft now. A local offline sandbox is not a live MU account integration.
Do not describe interfaces, fixtures or tool tests as working CMS functionality.
Gemini/Antigravity entry points must reference this file and STATE.md rather than
maintaining another progress ledger. See docs/ANTIGRAVITY.md. Codex and OpenAI coding
agents must similarly reference this file and STATE.md; see docs/CODEX.md.
Sessions running Minimax models (MiniMax-M3.1-Flash-Preview, MiniMax-M3,
MiniMax-M2.7) use the same canonical references through MINIMAX.md and
docs/MINIMAX.md. Minimax exposes no automatic model or reasoning-effort
selector: select model and effort per task, and never weaken a contract,
security or persistence check because a cheaper effort level was used.

## Target scope & legacy feature parity vision

In the end, ZendaMod Next must achieve complete capability parity with everything the legacy website is capable of:
- **Universal MU Online database compatibility**: Autodetection and autoconfiguration across any MU Online server file/database structure (Season 1 to Season 19+, Louis, MuEmu, IGCN, WebZen standard, and custom schemas).
- **Comprehensive game data models & binary decoding**: Full support for character warehouse (including multi-vaults/expanded warehouses), character inventory (standard/expanded slots, equipment, personal store, MuHelper), character progression (stats, master levels, class evolutions, PK level/status), connection logs, game currencies/credits (Zen, WCoinC, WCoinP, Goblin Points, Ruud, custom coin columns), and VIP systems (VIP tiers, expiration timestamps).
- **AdminCP power tools**: Player and account manager (search, ban, unban, edit credentials, adjust balances, assign GM/staff privileges, character editor/teleport/reset/level).
- **All game-connected modules**: Visual webshop, VIP shop, player-to-player item market, character marketplace, guilds (emblems, rosters, alliances), public player & guild profiles, comprehensive rankings/Hall of Fame/Hall of Honor, Castle Siege registration & cycles, event schedules/invasions, ticket support system, and drop/loot tables.
- All capabilities are implemented through modern, clean domain boundaries, declared ports, and safe testable adapters without relying on legacy code at runtime.
- Plan an easy guided website installer/setup and recovery flow, plus an AdminCP
  factory reset that restores default website configuration and allows setup again.
  Keep installation state authoritative in MSSQL, preserve separate website
  administrator authority and never reset MU game/account data. This is a planned
  requirement, not an enabled reset or permission to erase current data. See
  docs/INSTALLATION_LIFECYCLE.md (decision 100).

## Architecture and extension points

- All website configuration must be authoritative in Microsoft SQL Server
  (MSSQL), in website-owned tables/database. Files may hold disposable cache;
  they must not become configuration storage or a fallback source of settings.
  Existing file/SQLite settings are offline development prototypes awaiting
  MSSQL migration, not the production persistence design. This includes the
  Drupal integration's website configuration. Keep deployment credentials out
  of source and ordinary configuration records.
- **Three-part database topology & flexible configuration**: The database
  architecture supports separation into three distinct parts:
  1. **Game Data** (MSSQL `MuOnline` database): characters, stats, inventory
     blobs, warehouses, guilds, Castle Siege, event schedules.
  2. **Account Data** (MSSQL `Me_MuOnline` or `MuOnline` database): master player
     credentials, account status, online connection logs, cash shop currencies.
  3. **Web Data** (SQLite offline prototype or MSSQL website database): website
     settings, admin credentials, market listings & escrow, visual templates,
     event timetables, loot guides, support tickets, and audit logs.
  These parts can be configured in any combination: unified into a single
  MSSQL database, split across 3 separate databases, shared game/account with
  independent web storage, or multi-realm setups with individual connection inputs.
- PHP 7.4-compatible modular monolith; frozen public contracts live in `src/Contracts/V1`
  and explicitly evolved additive `src/Contracts/V2` through `src/Contracts/V5` (see CONTRACTS.md).
  `contracts/v1` through `contracts/v5` record independent metadata/frozen signatures; `architecture` records
  policy and explicit module dependencies. These are protected review surfaces.
- **Transitional existing modules** live in `src/Modules/<Name>/` with the current
  Domain/Application/Infrastructure/Presentation isolation. **New optional
  features should target independently droppable `plugins/<vendor>.<slug>/`
  packages**, with package-local layers as needed, instead of increasing the
  permanent static module graph. Within both layouts, cross-owner dependencies
  consume only frozen/additive public Contracts or approved versioned PluginAPI
  services; never another plugin's private implementation.
- Kernel composition belongs in `src/Bootstrap`, never in a view, domain object
  or module constructor with side effects. The target `PluginManager` composes
  scoped, approved runtime contributions through explicit `PluginAPI` extension
  points, **not by editing a static switch for every new plugin**. No mutable
  global service locator or side-effectful manifest inspection.
- Only Infrastructure may use PDO/SQL or external transports. No game writes
  outside the protected mutation gateway. Modules declare capabilities, versions,
  dependencies, permissions, contracts and test paths in the module catalog.
  An enabled module must have real bindings; planned modules are not enabled.
- AdminCP and frontend use the same application boundaries. All administrative
  permissions are checked server-side. UI visibility is never authorization.
- AdminCP login must use a separate website administrator account, never a game
  account. Game/player authentication and in-game GM/staff flags must not grant
  AdminCP access. Keep administrator credentials, sessions and permissions
- **Open-source core & commercial encrypted module licensing model**: In the
  future, the website distribution will combine an open-source core with
  commercial encrypted modules:
  - **Open-source unencrypted kernel and free plugin bundles**: Keep only the
    trusted foundation (CMS kernel, routing, plugin loader, auth/AdminCP base,
    contracts, free GrapesJS host) mandatory; distribute rankings, news, drops,
    profiles and other nonessential capabilities as independent **free plugins**.
    During migration the currently implemented modules remain transitional.
  - **Premium/paid modules**: Inventory and warehouse functionality, visual webshop,
    player-to-player item market, character marketplace, guilds and castle siege
    are premium/paid. They may be encrypted under per-function commercial licenses.
  - **Payment methods**: PayPal and MercadoPago modules are free. All other payment
    method modules are premium/paid. Free gateways depend only on core/contracts,
    never on paid gateway internals or a paid gateway license.
  - **Builder and template packs**: The GrapesJS visual website/template builder,
    with the old Enjin.com Minecraft builder as reference, is free/core. Free/default
    templates may be provided alongside separately sold premium predesigned templates.
    Free and premium template bundles, including their assets and starter project
    definitions, must occupy physically separate directories/packages. The free
    builder and free templates must work without premium template packages. Live
    templates must work seamlessly with visual drag-and-drop building, and must be
    compatible with any feature, module, or page across the entire website (including
    news, rankings, account, webshop, market, guilds, castle siege, profiles, support,
    and custom content pages).
  - **Physical separation**: Free/core and premium/paid modules must live in
    separate physical directories/packages and be independently distributable.
    A flag, namespace or license check inside one mixed module is insufficient.
    Existing mixed offline responsibilities still need an explicit catalog/path
    migration; this requirement does not claim they are already separated.
  - **Modular isolation requirement**: Module boundaries must remain strictly
    decoupled so commercial modules can be distributed, licensed, or swapped
    independently: public contracts (`src/Contracts/V1`) and open-source core
    must never depend on commercial internals, and license verification gates
    must cleanly govern module capability enablement.
- See `ARCHITECTURE.md`, `CONTRACTS.md`, `INVARIANTS.md`, `MODULES.md` and `DATABASE.md`.

## Building blocks and crafts: the user''s law

Derivative APIs are the building blocks; webshop, AdminCP, the live template
builder, rankings, markets and support are the crafts assembled from them. This is
a structural invariant of the three laws, not an optional style note.

- **Singularity is the block.** One verified responsibility, closed inputs, bounded
  execution, no executable payload. Perfected once per exact version (`id@x.y.z`)
  and blind to the crafts that consume it. Changing declared behavior means a new
  version plus explicit consumer migration, never an in-place edit.
- **Order is the registry and the gate.** It pins exact versions, enforces strict
  schemas and resource-level RBAC, separates enablement from authority, and quarantines
  extension proposals instead of widening a block in use.
- **Chaos is the craft composer.** It shapes and reshapes crafts as DAG plans over
  existing blocks, replans freely, and emits `capability_gap` with `consuming_crafts`
  when a second craft needs the same primitive.
- **Configuration only enables.** In Order''s invocation algorithm, enablement is its
  own step *before* permission checks. A preference may turn a capability on or off;
  it never grants authority, permissions, MSSQL access or native game/payment
  capability, and disabling one never erases data or contracts.
- **No craft reinvents reusable logic.** When two crafts need the same primitive,
  duplicating private logic is forbidden: raise the gap, get it reviewed, ship it as a
  versioned block, then have both crafts replan onto it. One craft may keep something
  private until a second asks for it, or the user asks explicitly.
- **Adaptability and potential come first.** Persisted schemas are declared and
  extensible rather than narrow, so diverse future functions stay possible without
  implementing them today, and blocks never need rewriting for them.
- **Terminology:** *block* and *craft* are canonical; the *workshop* is only the
  authoring/replanning space.

Read [docs/api/BUILDING_BLOCKS.md](docs/api/BUILDING_BLOCKS.md) for the inventory, the
triad matrix and the lifecycle from craft experiment to perfected block.
## Non-negotiable rules for implementation after review

- No production testing or assumed game-server write safety. Unknown schemas or
  allocator behavior disable capabilities. Use fixtures/mock adapters offline.
- Game mutations require verified ownership, authoritative offline verification,
  a cross-worker/account lock, recheck under lock, idempotency and immutable audit.
  A SQL transaction alone does not synchronize an active game server.
- Payment fulfillment uses verified callbacks, immutable order values, replay
  protection, unique idempotency keys and an audited atomic state transition.
- Auth, registration and mutations require throttling and CSRF (webhooks use their
  provider verification boundary). Never log credentials, bindings or player data.
- GrapesJS JSON is canonical; HTML/CSS are derived. Preserve stable component IDs,
  versions, drafts, revisions and publication state. No PHP, SQL, arbitrary scripts
  or executable uploads in editable templates. Preview is read-only/synthetic and
  private. Read `TEMPLATE_SYSTEM.md` and `GRAPESJS_INTEGRATION.md` before editing.
- Asset uploads need MIME and extension allowlists, byte limits, generated names,
  image decoding and storage outside executable paths; no SVG/HTML by default.
- Never weaken checks to hide failures. Missing integration, security or visual
  coverage must be recorded as missing, not replaced by a passing placeholder.

## Mandatory 11-step workflow

1. Understand the change and read STATE.
2. Discover existing relevant functionality using focused searches.
3. Identify affected contracts.
4. Analyze direct and indirect consumers with `scripts/impact.php`.
5. Reuse existing infrastructure where appropriate.
6. Implement through declared extension points.
7. Update relevant tests and representative fixtures.
8. Run architectural validation.
9. Run appropriate functional tests.
10. Verify consumer/template compatibility.
11. Document evidence and update STATE last.

Contract changes require consumer analysis, justification, compatibility decision,
a new version or explicit migration, implementation and test updates, consumer
verification and review. Never refresh frozen baselines merely to pass a check.
Use a new version for incompatible signatures or component property changes.

## Verification and continuity

Run `php scripts/verify.php --level=fast` during development; use `--level=affected
--changed=path1,path2` for impact, `--level=complete` before integration and
`--level=migration` for migration fixtures. Composer aliases and IDE tasks expose
the same runner. On this Windows host use `C:/xampp/php/php.exe` if PHP is not on PATH.
See `TESTING.md` for the precise coverage and limitations.
On this upgraded Windows runtime, `scripts/verify.ps1 -Level complete` wraps the
same runner with process-scoped installed GD/PDO_ODBC extensions for fixtures;
it never changes XAMPP php.ini or starts an actual SQL lab. Use it when those
extensions are not loaded by the ordinary CLI.

CI is configured but branch protection and reviewer identities must be configured by the repository
owner. Never claim CI checks block merging until that hosting rule exists.

Keep docs concise and synchronized. Update `STATE.md` last with actual checks,
unimplemented features, pending migrations and the next authorized step. IDE
readonly settings are convenience guards, not OS security or terminal protection.

## 5. Prompt Maestro para Agentes de IA (Copiar y Pegar)

Puedes incluir este bloque en tu archivo `AGENTS.md`, `.cursorrules`, `GEMINI.md` o pegarlo al inicio de cualquier sesión con una IA cuando le pidas un cambio:

```text
Actúa como Arquitecto Principal del proyecto ZendaMod-Next (CMS modular en PHP 7.4 con visión evolutiva multi-juego para MU Online, Minecraft, Counter-Strike, Rust, Metin2, Lineage 2, etc.).

Tu objetivo principal es implementar el cambio solicitado con CERO REGRESIONES, respetando estrictamente la evolución aditiva:

1. LECTURA PREVIA OBLIGATORIA:
   - Lee `STATE.md`, `CONTRACTS.md`, `INVARIANTS.md`, `MODULES.md` y `DATABASE.md` antes de proponer o tocar código.

2. PROHIBIDO ROMPER CONTRATOS CONGELADOS:
   - NUNCA edites firmas, comportamientos ni archivos en `src/Contracts/V*` que ya estén congelados, ni alteres `contracts/v*/baseline.json` para silenciar errores.
   - Si necesitas ampliar la comunicación entre módulos o paquetes, crea un contrato aditivo en una nueva versión o usa puertos internos dentro del mismo módulo propietario.

3. AISLAMIENTO MODULAR Y MULTI-JUEGO:
   - Un módulo nunca puede importar clases `Application`, `Domain` o `Infrastructure` de otro módulo; solo puede depender de `src/Contracts/V*` declarados en `architecture/modules.json`.
   - El código en `src/` jamás puede importar nada de `packages/premium/*`. Los paquetes premium deben ser 100% opcionales.
   - Mantén las reglas específicas de un juego (como formatos hexadecimales de MU Online, tablas nativas o protocolos RCON/Steam de otros juegos) encapsuladas en los adaptadores de datos de juego, nunca acopladas al Core del CMS, al sistema de plantillas GrapesJS ni a la gestión de usuarios web.

4. SEGURIDAD Y PERSISTENCIA FAIL-CLOSED:
   - Valida permisos del servidor (`Authorization`) antes de conectar a la base de datos o cargar paquetes.
   - En modo base de datos, nunca hagas fallback silencioso a archivos JSON/locales ni inventes valores por defecto si una tabla está vacía o corrupta.
   - Toda escritura SQL debe usar consultas preparadas, poseer su transacción, validar revisiones CAS (Compare-And-Swap), comprobar filas afectadas y verificar el commit o hacer rollback limpio.

5. VERIFICACIÓN DE IMPACTO Y PRUEBAS:
   - Antes de modificar un archivo, ejecuta `php scripts/impact.php --changed=<archivo>` para revisar todos sus consumidores.
   - Diseña el cambio de forma aditiva (Open-Closed) para que todos los consumidores actuales sigan funcionando exactamente igual.
   - Al terminar, ejecuta `php scripts/verify.php --level=complete` y agrega pruebas para los casos de éxito, fallo, concurrencia y denegación de permisos.
```

## Plugin-specific non-negotiable verification

Every new optional plugin must have an add/remove/readd filesystem test with live
HTTP routing plus any registered AdminCP/event/widget/job surfaces; the kernel,
AdminCP and independent providers must pass throughout. `register` is scoped and
side-effect-free, while migrations are explicit and authorized. Never execute
untrusted PHP upon discovery. A missing plugin must never be revived by a stale
manifest cache, Composer classmap or stored MSSQL status. `STATE.md` stays the
single truthful implementation ledger; update it last after actual verification.
