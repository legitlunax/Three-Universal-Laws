# Plugin API Architecture: Core / Game Type / Database Type / Schema Adapter

Decision 104 implements only data-only package discovery. The canonical manifest
is docs/PLUGIN_MANIFEST.md; selected future SDK namespaces and actual consumer
boundaries are in docs/PLUGIN_RUNTIME_MIGRATION.md. All four executable API
layers and provider binding remain pending.

**Status: normative proposed design, not an implemented or tested SDK.** This document completes the earlier generic `PluginAPI` concept. Use `MAIN_API.md`, `GAME_TYPE_API.md`, `DATABASE_TYPE_API.md`, `SCHEMA_ADAPTER_API.md` for specific contracts. Existing frozen V1–V5 source signatures/baselines remain unchanged. Confirm exact repository composition before implementing any interface.

## 1. Four independently versioned interfaces

```text
              ZendaMod-Next trusted Kernel (works with /plugins empty)
                ├── MainAPI / PluginHost API v1
                │    ├── lifecycle/discovery and capability registry
                │    ├── website auth, route, event, job, admin UI, template APIs
                │    └── website-owned MSSQL settings, audit and migrations
                │
/plugins/       ├── GameTypeAPI v1 (game abstraction, no fixed games in kernel)
 zenda.game.mu ─┼──── provider: MU Online
 zenda.game.minecraft └─ provider: Minecraft [future example only]
                │
                ├── DatabaseTypeAPI v1 (game/external data transport only)
 zenda.db.mssql ── provider: Microsoft SQL Server transport
 zenda.db.mysql ── provider: MySQL transport [future example only]
                │
                └── SchemaAdapterAPI v1 (game-specific schema/emulator interpretation)
 zenda.schema.muemu ── MUEMU schema adapter
 zenda.schema.louis ── Louis schema adapter
 zenda.schema.custom ── explicitly reviewed custom mapping [future]
                │
 zenda.rankings ──── uses normalized game read ports, not native SQL
 zenda.webshop ───── uses typed catalog/read ports and guarded mutation gateway
```

**This is one plugin host with four interfaces, not four independent CMS cores.** The kernel defines immutable interface versions. Each plugin folder can provide zero or several declared extension kinds and is activated only if present, trusted, compatible, authorized and dependency-safe.

### Precise responsibilities

| Layer | Provides | Must not do |
| --- | --- | --- |
| `MainAPI` / `PluginHost` | Discover/remove, register routes, events, capabilities, AdminCP pages, jobs, widgets; auth/context; permissions and plugin settings | Hardcode games/emulators, load missing folders, let plugin manifest self-grant privileges |
| `GameTypeAPI` | Define game identity, realm/server model, normalized player/character/item/currency capabilities | Assume all games have MU items/levels, expose native tables, bypass actor permissions |
| `DatabaseTypeAPI` | Declared external game database connection types, safe database metadata/introspection and typed parameter transport | Replace the kernel's website-owned MSSQL settings database; let feature plugins run arbitrary game SQL |
| `SchemaAdapterAPI` | Detect/validate one game's emulator/schema layout and map native fields/blobs to versioned normalized read ports | Treat an ambiguous heuristic guess as verified compatibility; write to native game data during discovery |

This separation prevents the common error of treating **database engine** (MSSQL) as the same thing as **game type** (MU Online) or **MU emulator schema** (MuEmu/Louis/IGCN). The type of connection does not prove the schema or supported game operations.

## 2. Operator example

```text
/plugins/
  zenda.game.mu/              # registers game type "mu.online"
  zenda.db.mssql/             # registers external game DB engine "mssql"
  zenda.schema.muemu/         # maps some verified MUEMU database structures
  zenda.rankings/             # consumes capability "game.rankings.read"
  zenda.webshop/              # consumes commerce contracts; may depend on game items
```

At boot/request, the host scans exact direct child folders, validates manifests without executing PHP, computes an approved request-local graph, initializes only approved compatible providers, and publishes their scoped contributions. An administrator can select a **game profile** (a game type + selected transport/provider + selected schema adapter + validated capabilities + realm/account/data topology). Profiles/settings live only in website-owned MSSQL (offline sandbox exceptions are explicitly labeled).

- Remove `zenda.webshop/`: webshop routes/menus/hooks disappear next request; rankings and the kernel remain usable. Webshop-specific MSSQL configuration/data are retained.
- Remove `zenda.schema.muemu/`: profiles using that specific adapter become unavailable/blocked. Profiles using another verified adapter remain available. No fallback to an unrelated schema.
- Remove `zenda.db.mssql/`: profiles that require **that external transport** are unavailable. The CMS **website MSSQL kernel connection remains independent**, so AdminCP still starts.
- Remove `zenda.game.mu/`: its MU profiles/capabilities become unavailable, and only plugins with a *required* dependency on those capabilities are blocked. Other game types and the main site remain operational.
- Restore same approved folder/version: reconcile the next request and restore verified compatible contributions without deleting or remigrating retained state.

Every runtime request or worker claim rechecks actual physical presence. A folder cannot be hot-unloaded from a PHP request already executing it. In-process PHP cannot safely sandbox malicious PHP code; plugins are trusted code after operator review.

## 3. Capability resolution, not native cross-imports

- Provider registers a canonical versioned capability, e.g. `game.characters.read@1`.
- Consumer declares a required/optional capability contract and minimum accepted version.
- A profile/realm binds a **specific approved provider ID**, not a random matching implementation. If no matching provider, return typed `UNAVAILABLE`; if multiple competing choices, require explicit selection, never first-match guessing.
- Plugins share only versioned host-owned read interfaces or guarded commands, never PHP internals, DB tables, file paths, or passwords.
- New optional capabilities add new interface versions. Never modify published V1–V5 baseline hashes to silence compatibility tests.

## 4. Implementation order

1. MainAPI discovery/activation/service registry, trust checks, and add/remove/re-add HTTP proofs with an empty `/plugins` baseline.
2. Database Type API for **external game-data transports** with strict read-only restricted profiles and separate website SQL persistence.
3. Game Type API using one synthetic generic game and a real but initially read-only MU provider.
4. Schema Adapter API using reviewed fixtures (one known emulator first); no guessing at universal game compatibility.
5. Bind a feature plugin (`zenda.rankings` preferred) through normalized typed capabilities without direct SQL.
6. Add guarded native write support only once the existing protected mutation, ownership, lock, replay, rollback and transaction boundaries are proven.

## 5. Non-negotiable tests

Empty plugin folder boots; adding each provider changes capabilities only after trust; removing each provider removes precisely its contributions next request, blocks required dependents with diagnostic rather than fatals; no DB cleanup on folder disappearance; multi-profile provider selection deterministic; incorrect SQL engine, wrong emulator schema, ambiguous schema and disabled license reject; AdminCP and website SQL still work if optional game `DatabaseType` is absent; no extra trusted PHP executes on discovery; old templates retain missing-provider references and render safely.
