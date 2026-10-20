# Public contracts and evolution

Decision 109 adds private Kernel/Apis boundaries for Chaos planning, Order governance and Singularity handlers. These are not new public V1-V5 or plugin SDK contracts. Main/GameType/DatabaseType/SchemaAdapter remain independently versioned derived contracts under the three primary APIs. Existing manifest keys and all frozen catalogs/hashes are preserved. See [implementation profile and consumer map](docs/api/WEBSITE_INTEGRATION.md).

Decisions 106-107 add only private Kernel/Plugins metadata and migration-review
models/services, consuming frozen V1.Authorization. Review packets are not
execution authority or a new public SDK. All V1-V5 signatures/catalogs/baselines
remain unchanged. See docs/PLUGIN_METADATA_STORAGE.md.

Decision 105 adds only an internal `Kernel/Plugins/Application/ApprovalStore`
port and service using frozen V1.Authorization. Kernel contracts/layers are
explicitly allowlisted in policy. No SDK or V1-V5 signature/catalog/baseline is
changed; no existing module may import the new private owner. See
docs/PLUGIN_APPROVAL_STORAGE.md.

Decision 104 selects future independent `Zenda\PluginAPI\<Layer>\V1`
namespaces/catalogs in docs/PLUGIN_RUNTIME_MIGRATION.md, without creating or
freezing SDK interfaces. The canonical distribution format in
docs/PLUGIN_MANIFEST.md is parsed by internal Bootstrap discovery classes;
it is not a mutation of V1-V5 or evidence of executable plugin registration.

## Planned distinct external API contracts (not frozen)

The new SDK must define **four independently versioned interfaces** without mutating frozen `V1..V5`: `MainAPI`/`PluginHost API` for shared registration, `GameTypeAPI` for game families and normalized capabilities, `DatabaseTypeAPI` for optional external game transport (not essential website MSSQL), and `SchemaAdapterAPI` for emulator/layout-specific verified mappings. Publish canonical provider/consumer IDs, per-profile versions, typed required/optional capability dependencies, missing-provider results, and additive migration policies. The concrete PHP signatures and manifest syntax in `MAIN_API.md`, `GAME_TYPE_API.md`, `DATABASE_TYPE_API.md`, `SCHEMA_ADAPTER_API.md` are **illustrative targets**, not already frozen or implemented. Freeze after code-level review and contract tests.


## Planned additive PluginAPI contract (not yet frozen or implemented)

The external plugin host must publish a versioned capability/extension contract
with stable `PluginEntrypoint::register(PluginContext)` semantics, scoped route,
event, admin, asset, command, job, settings and GrapesJS registries, and a safe
unavailable-provider result. Plugins may consume only declared **public ports**,
not another plugin's private classes or SQL tables. Plugin IDs/versions are
stable and owned; optional consumers handle absence, required ones are blocked
without bootstrap fatals. Core never statically imports optional PHP from
`/plugins`. All registration metadata is discovered from validated manifests;
absence overrides stale MSSQL/route/service cache. See `PLUGIN_SYSTEM.md`.

Do **not** edit/falsely rebaseline frozen V1-V5 contracts. Propose new additive
ports and record their versions with independent compatibility tests when the
loader is actually implemented. Current V1-V5 are **not** evidence of a working
general plugin SDK.

Decision 098 adds independently frozen V5.WebshopCatalogInspection and
V5.VipShopCatalogInspection, leaving V1-V4 source/catalogs/baselines unchanged.
Each has render(string $actorId, string $basePath): string. Callers supply the
server-owned separate website administrator ID and trusted mount; each package
checks database.inspect on admin before reading or connecting. The result is a
private full HTML page through unchanged V2.PageLayout: stored draft status,
entry count, canonical SHA-256 and escaped document, with no mutation forms,
player/account projection or public storefront. Denial precedes all storage;
missing/corrupt/ambiguous state and transport failures refuse without defaults,
DDL, repair or private exception chains. Fixed typed inspection factories stay
lazy; core imports only these public ports. HTTP authorization also precedes
package selection. Catalog selection is a fixed webshop/vip-shop allowlist;
file mode is unavailable. This adds private inspection without reinterpreting
frozen synthetic previews as native commerce. See docs/PREMIUM_CATALOGS.md.
Unknown V6 namespaces, unknown V5 ports, wrong generations/signatures and altered
entrypoint paths remain rejected by explicit static conformance cases.

Decision 097 adds independently frozen V4.WebshopCatalogProvisioning and
V4.VipShopCatalogProvisioning. They expose CLI-only redacted source inspection
and guarded immutable website catalog initialization, with lazy SQL connections
after scope/backend/fingerprint validation. They provide no storefront, license
or native activation. Each premium owner implements its own port/store/factory;
free composition imports no commercial internals. Normative shapes, limits,
preconditions and failure behavior are in [premium catalogs](docs/PREMIUM_CATALOGS.md).
V1–V3 source/catalogs/baselines remain unchanged; unknown versions/ports/aliases refuse.

Decision 096 introduces independently frozen V3.WebshopPreview while preserving
all V1/V2 files, metadata and freezes. V2.PageLayout remains the free shared shell.
The new port exposes escaped catalog/configurator rendering and deterministic
synthetic proposals over strict typed options and exact `{credits,wcoinC,zen}`
balances. It provides no account/writer/warehouse/clock, native bytes/serial/slot,
order, custody, debit or delivery. The old internal purchaseItem and buildMuItem
APIs are removed without aliases; URLs/forms/prices/display and saved components
remain compatible. Full normative shapes, limits and migration decisions are in
[Webshop preview](docs/WEBSHOP_PREVIEW.md). The checker supports exactly the declared
V1/V2/V3 identities at 096; 097 explicitly adds V4 provisioning while unknown
ports/aliases still refuse. The V3 catalog and
baseline are initial artifacts, never a refresh of V1/V2 hashes.

Decision 095 adds two independently frozen V2 ports without changing any V1
interface, catalog, component or baseline. `contracts/v2/catalog.json` uses
version-qualified IDs (`V2.PageLayout`, `V2.VipShopPreview`); V1 IDs retain their
original spelling. The strict checker resolves each exact version/file, enforces
module declarations/provider dependencies and inspects exact binding signatures
without executing implementation files. At 095, V3 was unsupported; 096 explicitly
adds its separately frozen Webshop port. Unknown ports and version aliases refuse.
`contracts/v2/baseline.json` is the initial freeze, not a refreshed V1 baseline.

| V2 interface | Trusted input | Behavior and bounds |
| --- | --- | --- |
| PageLayout | Server-owned title, active navigation key, body HTML, mount path and staff display flag | Shared core document/header/theme around trusted module markup; title/path/navigation use existing escaped rendering. Body is already server-rendered HTML, never a sanitizer or a public editable-HTML endpoint. The staff flag affects display only and grants no authority. |
| VipShopPreview | render: server-owned synthetic model (mount, staff display, CSRF, notices, optional synthetic balances/status); preview: package ID, credits/wcoinc/wcoin, exact nonnegative integer `{credits,wcoinC}` | Escaped premium page or deterministic calculation. Unknown package/currency/insufficient balance returns `{success:false,error:string}`. Malformed balance shapes throw InvalidArgumentException. Success has `success=true,sandboxPreview=true,nativeFulfilled=false,package,proposedVip:{tierCode,tierName,durationDays},balancesAfter:{credits,wcoinC}`. There is no account identity, transport, writer, subscription activation, clock, audit/fulfillment promise or persistent debit in the calculation. |

The free Content implementation provides PageLayout and depends on no premium
code. Premium VipShop consumes that port and provides VipShopPreview through its
own controller. Bootstrap alone loads the fixed reviewed composition closure in
explicit local file-sandbox mode and validates its public result. Missing package
returns unavailable; malformed composition is redacted. Database mode never loads
the preview package. The ports do not implement licenses, catalogs in MSSQL or
native mutation. HTTP keeps separate staff authentication, Origin/CSRF and throttle.

Compatibility: the old GameData VipShopService/VipPackage and Content
renderVipShop were internal, unfrozen APIs; they move without aliases. The old
simulated purchasePackage(writer) API is deliberately replaced with a pure preview,
with corresponding consumer/test migration. Existing HTTP URLs, forms, layout and
preview-only notices remain. V1 consumers and saved component fixtures are unchanged.

## Frozen V1

Status: defined and signature-frozen. Authorization has a verified local-sandbox
implementation; the remaining ports have no runtime providers. There is no
production MU integration. Interfaces
are in `src/Contracts/V1`; the machine catalog is `contracts/v1/catalog.json`.
All callers must validate declared input shapes. Array shapes below are normative
design contracts; runtime shape enforcement/conformance tests are phase-4 work.

| Interface | Preconditions / input | Result and behavior |
| --- | --- | --- |
| Authorization | authenticated server actor, explicit permission and resource ID | void if allowed; deny by default on absent/invalid scope |
| AccountReader | actor is authorized for account | null for absence; `{id:string,displayName:string,characters:list<{name:string,classId:int}>}`; never password/hash/email/IP/token |
| SchemaInspector | allowlisted connection identifier; database.inspect at caller | `{profileId:string,fingerprint:string,entities:map,capabilities:map<string,{supported:bool,reasons:list<string>}>}`; metadata only, no data rows or credentials |
| RankingReader | allowlisted ranking ID; integer limit 1–100 | `{rows:list<{position:int,name:string,value:int}>,generatedAt:string,stale:bool}`; stable tie order, explicit TTL, no private fields |
| GameMutationGateway | mutation-command shape; capability, permission and ownership verified inside boundary | mutation-result; acquire cross-worker account lock, authoritative offline check/recheck, compare-and-swap, idempotency, atomic ledger/audit; fail closed if unsupported |
| AuditLog | trusted application boundary and owning transaction | append immutable audit-event; audit failure prevents success; never a public arbitrary-event endpoint |
| TemplateStore | server-authorized actor; exact expected revision/published pointer | load exact authorized revision or null; save creates draft revision ID, never publishes; publish validates exact revision and atomically replaces recoverable pointer |
| ComponentRenderer | registered compatible component, authorized projections in render-context | escaped HTML fragment; no mutation, SQL, arbitrary PHP or template scripts; preview uses synthetic read-only data |

`mutation-command = {actorId:string,accountId:string,operation:string,
idempotencyKey:string,requestHash:string,expectedVersion:string,payload:object}`.
Operation and payload schemas must be registered by an approved provider before
use. Never interpret arbitrary operation strings, SQL or item bytes supplied by
the client. Idempotency scope includes actor/account/operation and full canonical
request hash. Same key/different payload is a conflict; exact replay returns the
original result without another debit/delivery. Caller-provided hashes are
recomputed and compared, not trusted.

`mutation-result = {transactionId:string,status:"committed"|"replayed",
resourceVersion:string,auditId:string}`. No success result before durable commit.
`audit-event = {actorId:string,occurredAt:string,transactionId:string,
operation:string,resourceId:string,before:object,after:object}`. before/after are
minimal redacted state, not entire account/inventory dumps. Persistent ordering,
replay uniqueness and durable atomicity require tested adapters.

`render-context = {mode:"public"|"preview",actorId:?string,
permissions:list<string>,projections:object}`. It is created server-side and never
taken directly from editable project JSON. `project-envelope` is the V1 fixture
envelope documented in TEMPLATE_SYSTEM. Future schemas must specify maximum
sizes, identifier encodings and errors before activation.

Failure vocabulary: InvalidInput, AccessDenied, UnsupportedCapability,
UnknownSchema, OfflineStateUnknown, AccountOnline, LockUnavailable,
ConcurrencyConflict, IdempotencyConflict, IncompatibleProject and StorageFailure.
These are semantic error reasons, not implemented exception classes yet. HTTP
adapters will map them to stable redacted API errors; internal SQL/provider
exceptions and credentials must never be emitted. Null means absence only.

Evolution: identify consumers with the impact tool and source search; document
why the change is needed; assess compatibility; add a new namespace/version or
explicit migration; update implementations and tests; verify existing consumers;
obtain review. `contracts/v1/baseline.json` detects interface token and component
metadata changes. Never refresh that file as a routine fix. Comments/formatting
are ignored for interface hashes. Behavioral compatibility still needs explicit
conformance tests once implementations exist; hashes cannot prove it.

Contract coverage still pending before HTTP/runtime implementation: a public-safe
published-template resolver, authentication/session and recovery ports, detailed
payment/transaction/codec/currency ports and complete input/output schemas. The
eight interfaces are the initial architectural boundary set, not every service
contract for the full CMS. Do not make public visitors impersonate an actor to
use TemplateStore.load; add the dedicated read contract through the evolution
workflow with published-only/private-draft conformance tests.
