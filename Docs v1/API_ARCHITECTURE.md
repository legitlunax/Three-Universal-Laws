# Three primary APIs and derived website contracts

The user selected Chaos, Order and Singularity as the primary architecture on
2026-10-20. This supersedes the earlier interpretation of four primary plugin APIs.
The four specialized provider contracts remain separately versioned and keep
their storage, trust, realm and compatibility guarantees.

```text
Known operation -------------------+
                                    v
Open objective -> Chaos -> plan -> Order -> Singularity handler -> owning port
                                    ^              |
                                    +-- result ----+
```

Chaos proposes; Order governs; Singularity performs one operation.
Known website requests call Order directly. No microservice or AI provider is
required. Complete generic planning, dynamic provider activation and durable
effect orchestration remain future work.

## Derived API map

| Existing contract | Derivation | Responsibilities retained |
| --- | --- | --- |
| [MainAPI / PluginHost](MAIN_API.md) | Order specialization | Approved scoped registration, lifecycle, routes/admin/events/jobs/widgets, capability resolution |
| [GameTypeAPI](GAME_TYPE_API.md) | Singularity domain contracts under Order | Game family, normalized read operations, explicit per-realm capabilities |
| [DatabaseTypeAPI](DATABASE_TYPE_API.md) | Singularity transport contracts under Order | Bounded external game/account reads and metadata; no feature-owned SQL |
| [SchemaAdapterAPI](SCHEMA_ADAPTER_API.md) | Singularity schema contracts under Order | Read-only probing and reviewed mapping of one game/emulator layout |
| Settings, templates, support, commerce APIs | Focused Singularity operations under Order | Owner-local rules, permissions, transactions, versions and audit |
| Guided workflows / optional planning | Chaos plans governed by Order | Data-only proposals; no authority inferred from a plan |
| [URL](docs/api/URL.md) | Pure Singularity operation governed by Order | Named versioned same-origin URL generation; initial builder consumer |
| [Math](docs/api/MATH.md) | Pure Singularity operation governed by Order | Named versioned declarative arithmetic; reusable layout formulas first |

A provider can publish multiple focused operations. Derivation does not merge a
game family, database engine and emulator schema. Each provider retains its own
version, explicit realm binding, trust and removal behavior. Features consume
normalized GameType read ports, never raw transport sessions or native tables.

Every future plugin still enters through the derived MainAPI registration
contract. Registration is scoped and side-effect-free. Order mediates execution
after registration; a route, job or widget cannot bypass equivalent policy.
Unknown code must be approved before it executes. Trusted in-process PHP is not
an OS sandbox; discovery only reads data.

## Runtime and compatibility

The private `src/Kernel/Apis` owner implements the initial bounded-read profile.
`src/Bootstrap/Apis` binds liveness and synthetic ranking reads; a V1 RankingReader
adapter preserves existing services, page variants and template projections.
All other static module paths remain transitional and still use existing guards.
This is not a completed conversion of all website actions or a folder-plugin SDK.

The sealed registry is built from trusted composition, with lazy factories.
It is neither a mutable global locator nor persisted configuration.
Order checks exact ID/version, permission/resource, input size/schema, effects and
deadline before constructing a handler, then validates the bounded result.
The planner only sees the permission-filtered catalog. Plans never carry identity.

Existing `plugin.json` v1 fields `contracts.mainApi`, `gameTypeApi`,
`databaseTypeApi` and `schemaAdapterApi` retain their meanings and validation.
A capability operation descriptor is independent of that distribution manifest.
No package fingerprint, stored approval, migration history or frozen V1–V5 hash
is rewritten by the architectural mapping. Future public Chaos/Order/Singularity
SDK contracts need independent catalogs and compatibility tests before freezing.

## Persistence and availability

Kernel website-owned MSSQL remains mandatory with an empty plugins directory.
A removable `zenda.db.mssql` provider only supplies external game/account reads.
Website administrator authority stays separate from players and native GM flags.

Physical folder absence must eventually override stored enabled status, route
caches and stale classmaps on the next request/worker claim. Removing a folder
does not delete website or native data. Re-addition requires the same verified
trust, version, dependencies, migration state and realm binding.
Read-only schema probes never authorize native writes.

## Continuation and acceptance

See [the precise implemented profile and consumer map](docs/api/WEBSITE_INTEGRATION.md)
and [the implementation sequence](docs/IMPLEMENTATION_PLAN.md).
Finish step 03's physical schema verification, installed-state persistence,
explicit approved execution and recovery before enabling executable packages.
Step 04's MainAPI HTTP slice must register/invoke capabilities through Order.

Acceptance still requires live folder add/remove/readd across routes, AdminCP,
widgets, events/jobs and independent providers, with retained MSSQL data and
healthy kernel/admin operation. No internal registry test substitutes for it.
Native MU writes, payments and live template publication retain all prior gates.
