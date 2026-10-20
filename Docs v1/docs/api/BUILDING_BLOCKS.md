# Building blocks and crafts

This is the inventory behind the user's building-block law (fixed 2026-10-20,
normative text in [EVOLUTION_PRINCIPLES.md](../EVOLUTION_PRINCIPLES.md) §0.0 and
summarized for agents in [AGENTS.md](../AGENTS.md)).

## The law

- **Building blocks are derivative APIs**: focused private Singularity
  operations governed by Order and proposable through Chaos, each with an exact
  name and version, closed inputs, explicit bounds and no executable payload.
- **Crafts are webshop, AdminCP, the live template builder, rankings, markets,
  player support and every future product assembled from blocks.** A craft is
  intentionally malleable and never final: design, preferences and configuration
  change.
- **Configuration is optional and only enables.** A preference may switch on or
  off behavior that already exists behind its own authorization checks. It never
  grants authority, permissions, MSSQL access or native game/payment capability.
- **Blocks are perfected once.** Changing declared behavior means a new version
  plus explicit consumer migration, never an in-place edit of a block in use.
- **No craft reinvents reusable logic.** When two crafts need the same thing, it
  becomes a block. A single craft may keep something private until a second one
  asks for it.
- **Adaptability and potential come first.** Persisted schemas are declared and
  extensible rather than narrow, so diverse future functions are possible without
  implementing all of them today.

## The unified triad matrix: blocks vs. crafts

| Dimension | Singularity (Law of Singularity) | Order (Law of Order) | Chaos (Law of Chaos) |
| --- | --- | --- | --- |
| Core essence | **The Block** (*El Ladrillo*) | **The Registry & Gate** (*El Registro y la Compuerta*) | **The Composer of Crafts** (*El Compositor de Artes*) |
| Purpose | Execute exactly one verified, closed-input responsibility. | Govern interoperability, exact version contracts, enablement and security boundaries. | Explore, assemble and replan malleable crafts from existing blocks. |
| Mutability | Perfected once. Immutable behavior per exact version (`id@x.y.z`); never expanded with craft logic. | Deliberately stable. The catalog does not reorganize when a craft changes shape. | Intentionally malleable. Plans, step order and parameters change freely (`chaos.replan`). |
| Reuse rule | Blind to consumers; knows nothing of webshop, AdminCP or rankings. | Rejects in-place contract widening; isolates quarantined extension proposals. | Keeps single-craft glue local; emits `capability_gap` when two or more crafts need the same primitive. |
| Authority and config | Never self-authorizes; receives a restricted execution context from Order. | Separates enablement (configuration switches) from authority (server-side RBAC, MSSQL/payment gates). | Proposes plans with zero execution privilege; treats all plans and outputs as untrusted data. |

## How the three laws carry it

| Law | Canonical role | Architectural invariant | Where it is written |
| --- | --- | --- | --- |
| **Singularity** | **The Block** (*El Ladrillo*) | One verified responsibility, closed inputs, bounded execution, no executable payload. Perfected once per exact version (`id@x.y.z`); completely blind to the crafts consuming it. | [SINGULARITY.md](SINGULARITY.md) §§1-4, §13 |
| **Order** | **The Registry & Gate** (*El Registro*) | Enforces exact version pinning, strict input/output schemas and resource-level RBAC. Separates optional feature enablement from authority; routes capability gaps to quarantine rather than widening blocks in place. | [ORDER.md](ORDER.md) §§3-6, §10, §13 |
| **Chaos** | **The Craft Composer** (*El Compositor*) | Shapes and reshapes malleable crafts via DAG plans over existing blocks (`chaos.plan`, `chaos.replan`). Enforces the Two-Craft Rule by raising a `capability_gap` with `consuming_crafts` evidence when a second craft needs the same primitive. | [CHAOS.md](CHAOS.md) §§2-7, §11 |

**Terminology:** *ladrillo* and *arte* are the canonical terms. *Taller* is reserved
strictly for the authoring and replanning space where an arte is being composed,
never for the shipped artefact itself.

## Lifecycle: from craft experiment to perfected block

1. **Single-craft incubation.** A single craft (for example `webshop`) implements a
   domain-specific workflow using existing blocks (`url.resolve@1.0.0`,
   `math.evaluate@1.0.0`, `style.resolve@1.0.0`, `feature.resolve@1.0.0`) plus
   private craft-local orchestration.
2. **Second-craft pressure (the Rule of Two).** A second craft (for example
   `vip_catalog` or `player_support`) requires the exact same underlying primitive
   (price resolution, state transitions). Duplicating private logic across crafts is
   forbidden.
3. **Capability gap proposal (Chaos).** Chaos emits a `capability_gap` via
   `chaos.propose_extension`, declaring `consuming_crafts` (minimum two, or an
   explicit user mandate), closed input/output schemas, bounds and required offline
   tests.
4. **Perfection and registration (Singularity + Order).** Following human review and
   offline contract testing, the primitive is implemented as a zero-side-effect or
   strictly bounded Singularity handler and registered in Order under an immutable
   `domain.action@1.0.0` contract.
5. **Explicit craft replanning.** Both crafts update their Chaos plans to bind
   explicitly to `domain.action@1.0.0`. Future changes to either craft's preferences
   or UI re-run through Chaos without ever mutating the perfected block.
## Current blocks

| Block | Owner | Purpose | Docs |
| --- | --- | --- | --- |
| `url.resolve@1.0.0` | `Kernel/Urls` | Named, versioned same-origin paths | [URL](URL.md) |
| `math.evaluate@1.0.0` | `Kernel/Math` | Named, versioned bounded arithmetic | [Math](MATH.md) |
| `style.resolve@1.0.0` | `Kernel/Styles` | Named, versioned mobile-first design values | [Style](STYLE.md) |
| `feature.resolve@1.0.0` | `Kernel/Features` | Named, versioned optional capability switches | [Feature](FEATURE.md) |
| `locale.resolve@1.0.0` | `Kernel/Locales` | Named, versioned message lookup with one reviewed fallback per locale | [Language](LOCALE.md) |
| `plugins.schema.inspect@1.0.0` | `Kernel/Plugins` | Read-only physical website schema inspection | [schema inspection](../PLUGIN_SCHEMA_INSPECTION.md) |

The verified installed plugin schema state (decision 113) is kernel control-plane
storage rather than a published capability; see
[installed storage](../PLUGIN_INSTALLATION_STORAGE.md).

## Candidate blocks, in the order two crafts would demand them

These are **not** implemented. They become blocks when a second craft needs the
same primitive, or when the user asks for them explicitly:

| Candidate | Craft pressure that would justify it |
| --- | --- |
| Price resolution (named strategies, currency and tier context) | webshop plus any future VIP/catalog storefront |
| Workflow/state transitions (order, ticket and publication lifecycles) | webshop orders, player support, template publication |
| Selection (filtering, sorting, pagination definitions) | every list surface: shops, rankings, news, market, admin tables |
| Presentation projection (approved view models for templates) | live template builder plus storefronts |
| Audit envelope (immutable event shape for mutations) | every guarded write path behind the mutation gateway |

A candidate is only promoted to a block after consumer impact analysis,
contract versioning and offline tests. It is never smuggled in as private logic
inside a craft.

## What is deliberately still missing

No block grants authority, none writes native game data, none performs payment
fulfillment, and none is reachable from a plugin through the MainAPI lifecycle,
which remains unimplemented. Blocks stay private to reviewed server composition;
they are not a public plugin SDK.

See [integration profile](WEBSITE_INTEGRATION.md), [plugin system](../PLUGIN_SYSTEM.md)
and [STATE](../STATE.md).