# API summary

ZendaMod Next has exactly three primary APIs:

| API | Purpose | Execution authority |
| --- | --- | --- |
| [Chaos](docs/api/CHAOS.md) | Explore and compose plans from available capabilities | None; submits data to Order |
| [Order](docs/api/ORDER.md) | Govern interoperability, policy, contracts and execution | Validates each operation and delegates |
| [Singularity](docs/api/SINGULARITY.md) | Implement one focused operation | Only within its authorized context |

All other APIs derive from these roles. MainAPI/PluginHost specializes Order's
lifecycle and registration boundary. GameType, DatabaseType and SchemaAdapter
define specialized Singularity operation contracts mediated by Order; they remain
independently versioned. Website-owned MSSQL stays mandatory kernel infrastructure.

Private [URL](docs/api/URL.md) and [Math](docs/api/MATH.md) derivatives now provide
named URL resolution and declarative formula evaluation through Order-governed
Singularity operations. Their first real consumer is the live template editor;
other website URL/calculation migrations and editable MSSQL catalogs remain pending.

The first private implementation supports bounded reads, exact versions,
permission/resource checks, input/output validation, a sealed registry and
deterministic planning. Health and synthetic ranking reads use Order.
The initial plan profile contains up to 16 independent steps; DAG references,
writes, external effects, executable plugin loading and public SDKs remain pending.
See [integration and migration map](docs/api/WEBSITE_INTEGRATION.md),
[API architecture](API_ARCHITECTURE.md) and [STATE.md](STATE.md).
Building blocks and crafts: derivative APIs are the building blocks (focused
Singularity operations governed by Order, proposable through Chaos); webshop,
AdminCP, the live template builder, rankings, markets and support are the crafts
assembled from them. Blocks are perfected once and then versioned, crafts are
intentionally malleable, and configuration only enables behavior that already
exists behind its own authorization. See [docs/api/BUILDING_BLOCKS.md](docs/api/BUILDING_BLOCKS.md).