# Website integration of Chaos, Order and Singularity

Decision 109 maps the three primary APIs into the existing PHP website without
changing frozen V1–V5 contracts. See [PROJECT](../../PROJECT.md), [architecture](../../API_ARCHITECTURE.md)
and [STATE](../../STATE.md). This document defines the implemented profile;
the general manuals describe the broader target.

## Implemented private profile

| Concern | Implementation and limit |
| --- | --- |
| Contract identity | Exact ID + release version, canonical descriptor schema 1.0, owner and single purpose |
| Schema | Closed objects, required fields, bounded arrays/strings, strict integers/finite numbers/booleans/null, enum and numeric bounds; string/null union for legacy optional guild names |
| Unknown schema | Unsupported keywords, open objects, unbounded arrays/strings and malformed descriptors refuse |
| Identity | Server-created immutable ExecutionContext; actor, resource, request ID, monotonic deadline; never accepted from request/plan fields |
| Authorization | Frozen V1.Authorization on every required permission/resource before lazy handler creation; anonymous only for explicitly public operations |
| Registry | Sealed trusted in-memory composition, maximum 64 bindings, duplicate ID/version refusal; no plugin discovery/execution or mutable global access |
| Execution | Input JSON bound up to 64 KiB per descriptor; output up to 256 KiB; at most 64 attempts per Order instance |
| Deadline | Maximum 1 second, checked before/after calls; bounded context passed to handler; in-process calls cannot be forcibly interrupted |
| Effects | Only none/read execute; write/external refuse even if a descriptor claims idempotency |
| Errors | Redacted stable codes; no raw exceptions/SQL/paths or partial handler results |
| Telemetry | Up to 64 request-local status/duration records; no args, identity, resources, results or durable audit claim |
| Chaos | Deterministic planning of 1–16 known independent operations from Order's authorized catalog, with no I/O/handler execution |
| Plans | Strict independent-read profile 1.0; Order rechecks each step, stops on failure and returns completed results; no retry |
| Missing | DAG edges/references/output_ref, LLM/open-goal search, durable quotas/audit/idempotency, effects, public SDK, remote execution, full plugin lifecycle |

The supported schema subset is deliberately explicit; it is not a general JSON
Schema engine. Internal object envelopes use PHP associative arrays (including
an empty array for an empty object). No generic public JSON invocation endpoint
is exposed. Encrypted/premium plugins have no special route around Order policy.

## Consumer and ownership migration

| Consumer/source | Current binding | Migration/compatibility decision |
| --- | --- | --- |
| Bootstrap /health | website.health.read 1.0.0 via Order -> HealthRead | Exact JSON/status preserved; no disk/database access; Chaos optional |
| Home ranking projection | game.rankings.read 1.0.0 via OrderRankingReader | Frozen V1 RankingReader and complete synthetic projection preserved |
| /rankings, filters, search, hall of fame, guilds | RankingService -> V1 adapter -> Order -> RankingRead -> existing synthetic reader | URLs, filtering, ordering and nullable/extra public row fields retained |
| Template ranking preview | V1 adapter -> Order -> RankingRead | Saved component IDs/properties and renderer contracts untouched |
| Live builder links/assets/save endpoint | url.resolve 1.0.0 -> UrlResolve -> isolated Kernel/Urls catalog | Named versioned trusted composition replaces consumer literals; remaining site URLs are transitional |
| Live builder mouse/keyboard sizing | Reviewed named formula definitions -> bounded local Math interpreter; server math.evaluate 1.0.0 -> MathEvaluate -> Kernel/Math | Matching PHP/browser layout results; display-only client values; no business formula/persistence migration |
| Live builder and views derived styles | Reviewed mobile-first tokens/breakpoints -> server style.resolve 1.0.0 -> StyleResolve -> isolated Kernel/Styles catalog | Numbers and bounded units only; consumers derive CSS; no vendored framework, no authored CSS text and no token editor |
| Optional craft capability switches | Reviewed feature declarations -> server feature.resolve 1.0.0 -> FeatureResolve -> isolated Kernel/Features catalog | Enables already-guarded behavior only; never grants authority, permissions, data access or native capability; no editor or tenant override |
| Multi-language message lookup | Reviewed locales/messages -> server locale.resolve 1.0.0 -> LocaleResolve -> isolated Kernel/Locales catalog | Stored text only with one reviewed fallback per locale; no interpolation, markup or formatting; existing views still carry literal strings |
| Composed dependency-aware execution | Order-governed dataflow engine -> OrderApi::executeGraph over registered capabilities | Internal Order component, not a fourth API; typed references, no coercion, read/none effects only; rankings workflow migrated with its V1 payload unchanged |
| Admin/player/settings/support/commerce controllers | Existing application services and current auth/CSRF/throttle/store boundaries | Still transitional; migrate one focused operation at a time with resource, denial, retry/race and HTTP tests |
| Kernel/Plugins and Bootstrap/Plugins | Existing discovery, approvals, scalar configuration, inert migration reviews; private plugins.schema.inspect 1.0.0 via Order -> SchemaFootprintRead | Trusted expected footprint and separate admin authority precede operator connection; physical read only, executable false; Bootstrap bridges isolated owners |
| Future MainAPI plugin host | Planned scoped registration + dynamic registry | Feed only currently present, approved compatible contributions into Order; recheck presence before dispatch |
| GameType / DatabaseType / SchemaAdapter | Planned independently versioned provider contracts | Singularity specializations with Order policy, explicit verified realm binding and protected native gateway |

Bootstrap compatibility adapters depend on existing public ports; Kernel/Apis
imports only its own owner and the declared frozen Authorization contract.
No existing module imports the new private Kernel owner. A reviewed public SDK
will be required before removable plugins consume these interfaces directly.

The synthetic reader remains an offline demonstration, including in the inherited
website composition. This bridge does not establish live MU reads or a verified
GameType provider. It creates no database connection, migration or native authority.

## How to extend safely

1. Choose one owner and a purpose that can be stated as one operation.
2. Define exact ID/version, closed input/output, effect, permissions, resource
   binding, limits, idempotency requirements and stable failures.
3. Register a lazy factory in trusted composition; eventually use the approved,
   scoped derived MainAPI registry. Never grant permissions from a descriptor.
4. Have the owning handler enforce domain/ownership rules and use declared
   ports/infrastructure. Keep SQL, credentials and native layouts out of Kernel/Apis.
5. Retain old consumers behind an adapter until HTTP/template and negative cases pass.
6. Publish a new additive public contract only after catalog/consumer review;
   never relabel private classes as a finished plugin SDK.

Decision 110's [physical website schema inspector](../PLUGIN_SCHEMA_INSPECTION.md)
uses an explicitly composed private operator connection; it does not change the
health/ranking bridge or ordinary HTTP composition. Verified installed migration
state/execution and provisioning remain next, followed by Order-mediated
folder-plugin dispatch.
See [execution plan](../IMPLEMENTATION_PLAN.md).
