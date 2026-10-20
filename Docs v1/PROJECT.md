# ZendaMod Next

ZendaMod Next reconstructs the legacy MU Online CMS as a PHP modular website,
with a trusted kernel and independently distributable folder plugins.
MU Online comes first; future games use adapters.
Read [STATE.md](STATE.md) for the current checkpoint and [AGENTS.md](AGENTS.md)
for mandatory development rules.

## Three primary APIs

| API | Responsibility | Current source |
| --- | --- | --- |
| [Chaos](docs/api/CHAOS.md) | Propose bounded plans using the authorized capability catalog; no execution authority | `src/Kernel/Apis/Application/ChaosApi.php` |
| [Order](docs/api/ORDER.md) | Resolve exact versions, authorize operation/resource, validate contracts and govern execution | `src/Kernel/Apis/Application/OrderApi.php` |
| [Singularity](docs/api/SINGULARITY.md) | Execute one declared operation with limited dependencies | `src/Kernel/Apis/Application/SingularityHandler.php` and owner-specific handlers |

Known operations call Order directly. Chaos is optional and has no LLM dependency.
Plans remain untrusted data; Order rechecks authority on every operation.
See [website integration](docs/api/WEBSITE_INTEGRATION.md) for implemented limits
and [API architecture](API_ARCHITECTURE.md) for the derivation map.

MainAPI/PluginHost, GameTypeAPI, DatabaseTypeAPI and SchemaAdapterAPI remain
separately versioned **specialized contracts derived from the three primary APIs**.
MainAPI belongs to Order's plugin lifecycle/registration boundary.
Game, transport and schema operations are specialized Singularity contracts,
registered and invoked under Order. These are distinct provider roles:
MU is a game, MSSQL is an engine, MuEmu/Louis are schema layouts.
The existing distribution manifest `plugin.json` v1 and its four contract range
keys are preserved; an operation descriptor is a separate document.

Private [URL](docs/api/URL.md) and [Math](docs/api/MATH.md) derivatives provide
named URL resolution and declarative formula evaluation through Order-governed
Singularity handlers. The [live template editor](docs/LIVE_BUILDER.md) is their
first consumer; other site URLs/calculations remain transitional.

## Directory and ownership map

```text
public/                 Only webroot and static site assets
src/
  Bootstrap/            HTTP composition and compatibility adapters
    Apis/               Website bindings into the three-API foundation
    Plugins/            Data-only package discovery and private composition
  Kernel/
    Apis/Domain/        Capability/schema/context value objects
    Apis/Application/   Chaos planner, Order gateway, sealed registry, handler port
    Urls/Domain/        Named versioned same-origin URL catalog
    Math/Domain/        Bounded declarative formula interpreter
    Plugins/            Existing approval/configuration/migration-review services
  Contracts/V1..V5/      Frozen cross-owner ports; no baseline regeneration
  Modules/              Transitional static feature owners
packages/premium/       Optional, physically separate webshop/VIP implementations
plugins/                Target operational folder-plugin root (loader still pending)
contracts/              Frozen catalogs and hashes, independent of runtime settings
architecture/           Static policy, ownership, dependency and sandbox declarations
database/               Reviewed explicit website installation DDL
tests/                  Offline, process, HTTP and opt-in owned SQL fixtures
frontend/builder/       Self-hosted GrapesJS/Material/layout UI and Math interpreter
scripts/                Impact, verification and guarded local tools
docs/api/               Canonical Chaos/Order/Singularity manuals and integration map
docs/discovery/         Existing static legacy inventories
docs/archive/           Historical snapshots; never the active checkpoint
reports/                Disposable verification output; never configuration authority
```

Existing module namespaces, source paths, Composer mappings and public URLs are
compatibility boundaries. Move each owner only with consumer analysis and parity
evidence. Kernel APIs contain no MU rules, raw SQL, payment logic or premium imports.
Optional business features belong in plugins, rather than new permanent kernel modules.

## Product and safety requirements

- Approved complete `plugins/<vendor>.<slug>/` folders must eventually contribute
  routes, AdminCP, events, jobs, assets and widgets without core edits. Removing
  a folder revokes its contributions on the next request, retaining MSSQL state.
  The executable loader and this full lifecycle remain unimplemented.
- Website settings, identities, templates, audits and plugin state are authoritative
  in kernel-owned MSSQL. Optional external-game transports never supply that store.
  Website/game/account data can share a physical database without sharing authority.
- Separate website administrator credentials and permissions govern AdminCP.
  Player logins, matching names and native GM flags grant no administrator authority.
- All V1–V5 contracts remain frozen. New public SDKs need independent version review.
  The new Kernel/Apis interfaces are private implementation boundaries.
- Native game writes require verified ownership, authoritative offline rechecks,
  cross-worker locks, CAS, durable idempotency and immutable audit. Payments and
  template publication retain their independent verification gates.
- The open-source kernel and free GrapesJS host must work without premium packages.
  Inventory, warehouses, webshop, markets, guilds and siege are premium modules.
  PayPal/MercadoPago gateways are free; other payment gateways are premium.
  Free and paid templates/assets must be physically separate.
- GrapesJS project JSON is canonical; editable content cannot execute PHP/SQL/scripts.
  Saved missing-plugin widgets must eventually retain IDs/versions and render safely.
  The restricted real editor is implemented; optional provider registration,
  full editor features, publication and MSSQL template cutover remain pending.
- Drupal owns news authoring/revisions; Zenda consumes anonymous published projections.
  Guided installation, recovery and website-only factory reset remain planned.
- `C:/xampp/htdocs/ZendaMod` is read-only reference material. Never execute it,
  copy secrets/player data/licenses/assets, or use it as a runtime dependency.

## Development and continuation

Local XAMPP CLI currently reports PHP 8.2.12. Source remains PHP 7.4 compatible;
Composer's platform target is 7.4.33. Neither setting certifies a second runtime.
Operators and future customers can follow the
[manual XAMPP 8.2 setup guide](docs/installation/XAMPP_8_2.md) for GD, PDO_ODBC,
SQL Server dependencies and separate CLI/Apache validation.

```powershell
& 'C:/xampp/php/php.exe' scripts/impact.php '--changed=src/Bootstrap/Application.php'
& ./scripts/verify.ps1 -Level fast
& ./scripts/verify.ps1 -Level complete
```

Follow the eleven-step workflow in AGENTS. Record evidence and update STATE last.
The Windows wrapper loads installed GD/PDO_ODBC extensions only for the verifier
and its fixture children when absent; it restores the environment and removes its
temporary INI. It never edits XAMPP configuration or opens an SQL lab automatically.
The [execution plan](docs/IMPLEMENTATION_PLAN.md) retains the plugin-first sequence:
finish verified website migration state/execution, then build the live folder-plugin
host through Order before migrating feature owners. See [documentation index](docs/README.md)
for storage, contracts, templates, local operation and history.
