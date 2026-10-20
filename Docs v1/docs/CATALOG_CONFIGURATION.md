# Catalog ownership and schema profile drafts

Decision 098 now exposes these stored drafts only through private administrator
inspection at /admin/catalogs using independent V5 ports and package-owned views.
Separate website staff authorization precedes lazy SQL access; reads show exact
stored documents without code/file fallback. This adds Identity authorization
and private inspection dependencies without changing V2/V3 preview semantics.
Public SQL catalog projections, licensing/selection and native fulfillment remain
pending; database storefronts/purchases still return 503.

Decision 093 begins the remaining catalog migration with free/core schema mapping
declarations. It does not implement premium catalog persistence or physical
package separation. Decision 095 extracts the VIP preview/catalog/view physically;
Decision 096 also extracts the Webshop preview/catalog/view. Other GameData paid
responsibilities and final distribution manifests remain pending. Decision 097 now
prepares isolated paid draft catalog persistence/provisioning; see
docs/PREMIUM_CATALOGS.md. Database storefront activation is still unavailable.

## Consumer inventory and distribution destination

| Existing responsibility | Direct consumers | Ownership and migration destination |
| --- | --- | --- |
| WebshopItem::defaultCatalog, WebshopOrder, WebshopService (now moved) | Application /shop and /shop/purchase through V3.WebshopPreview; tests/webshop.php; package view via free V2.PageLayout | Premium packages/premium/webshop owns catalog/pricing/display/controller/view and the unused prototype order value object. Old writer/order simulation and fake binary/serial/slot API are removed; pure proposals never debit or deliver. Immutable draft MSSQL preparation and guarded provisioning now belong to this package under 097; storefront activation, licensing and native boundary remain pending. |
| VipPackage::defaultPackages, VipShopService (now moved) | Application /vip and /vip/purchase through V2.VipShopPreview; tests/vip-shop.php; package-owned view with free V2.PageLayout | Premium packages/premium/vip-shop/src/Modules/VipShop owns synthetic catalog/calculation/view. Internal purchase/writer API is removed; pure preview never writes. Immutable draft MSSQL preparation/provisioning now belong to this package under 097; storefront activation, entitlements and native boundary remain pending. |
| MuDatabaseProfile example factories and PdoSchemaAutodetector presets | SchemaAutodetectorService, internal readers/writers with explicitly supplied profiles, tests/gamedata.php; Application /admin/schema | Free/core mapping metadata stays in GameData. Stored drafts now supply the database-mode inspector only; native readers/writers and live autodetection are not configured by this catalog. |
| CurrencyReader/Writer, WarehouseReader, MuItem, AccountVipStatus and SandboxOnlyEconomy | Other mixed GameData consumers; Bootstrap supplies synthetic shop/VIP balance display/input only | Neither extracted calculation depends on these internals. Webshop now owns a display projection without native bytes/serial/slot. Remaining extraction needs explicit ownership/contracts; paid modules cannot import GameData internals. |
| Market, inventory/warehouse, guild and siege responsibilities | Other existing GameData services, infrastructure, Bootstrap and views | Separate premium packages remain required under 083; this catalog change moves none of them. |

Decisions 095/096 install the first-party VIP and Webshop preview sources at
their reviewed roots, with no production module activation. Webshop uses a new
independent V3 freeze to preserve the existing V1/V2 catalogs and baselines; see
docs/WEBSHOP_PREVIEW.md for input/output and explicit internal API migration.
Decision 094 now implements package-aware source discovery/autoloading, static
binding inspection, ownership/layer checks and impact for these exact roots.
SourceLayout is immutable distribution code, never editable website settings or
capability/license activation. Composer prefixes must agree. Unknown package PHP,
including nested vendor/var paths, reaches lint and architecture rejection; core
shadow classes cannot satisfy runtime or binding lookup for an absent package,
including namespace case variants on Windows.
Core/public contracts cannot import paid internals/composition, and premium sources
use contracts for other owners. Package-local composition belongs in Bootstrap.
Webshop/VipShop stay production-planned with V3/V2 preview bindings and free
Content layout dependencies. SourceLayout/module metadata fix their separate
offline composition paths. Add versioned contracts for remaining cross-module
reads/native operations as needed, with
consumer analysis and compatibility tests. Do not refresh V1 hashes or permit
core imports of paid implementations. Package absence must leave free identity,
news, rankings, guides and the free builder usable. The new isolated HTTP fixture
proves four existing routes run with optional roots absent. VIP classes/view are
now absent from core; Webshop classes/view are also removed. Both storefronts
refuse when absent, share persistent HTTP budgets and run independently in actual
package-isolated HTTP fixtures. Broken composition refuses privately. Other
paid GameData code remains, so full free-distribution/editor absence evidence
is missing. License/entitlement gates remain unimplemented and must not
activate native operations by themselves. Free PayPal/MercadoPago gateways and
free/premium template bundles retain their separate distribution requirements.

Draft persistence and CLI bindings are prepared under 097, but website activation
and storefront bindings remain pending. Database mode still returns 503 for
/shop, /vip and their authenticated valid purchase requests. Purchase requests
retain separate staff authentication, Origin/CSRF and the existing persistent
budget. They perform no synthetic debit, order preparation or detached audit.
The explicitly standalone file sandbox retains its prior synthetic previews.

## Implemented mapping catalog boundary

GameData owns SchemaProfileCatalogStore, SchemaProfileCatalogService and its PDO
adapter. The service checks database.inspect/admin through V1 Authorization before
reading. Bootstrap alone supplies the private website connection. Database mode
/admin/schema uses this store and the stored default; unknown/malformed selections
return 422. Missing/empty/corrupt SQL state returns a private 503 for this consumer,
without taking down unrelated settings routes or selecting a code/file default.
Standalone file mode keeps its previous example-preset inspector.

The exact version-1 envelope contains schemaVersion=1, status=draft,
defaultProfileId and a profiles list. Each entry contains description and profile;
the profile retains profileId/name/seasonFamily/tables/columns/binarySpecs/
capabilities. Unknown fields, coercions, credentials/connection fields and executable
identifiers refuse. Table names permit one optional schema prefix; column names
are simple identifiers. This is bounded metadata validation, not proof those
objects exist or permission to execute queries with them. Native adapters receive
none of these stored declarations.

Limits: 1..50 unique profiles, 64-byte lowercase IDs, 64 table aliases, 64 columns
per alias, existing five integer binary-spec keys (0..1048576), ten existing boolean
metadata flags and 512 KiB canonical UTF-8 JSON. Map key ordering is canonical;
profile list ordering is retained. Missing binary/flag values are rejected rather
than replaced with defaults. The legacy item-width field has ambiguous historic
units; inspection preserves its value and labels those units unverified instead
of claiming a working codec. Declared flags never establish schema compatibility,
ownership, offline state, native write support or license availability.

The PDO adapter owns read/initialization transactions, rejects caller transactions,
requires exception-mode sqlsrv/owned SQLite and checks singleton identity,
schema/digest/bounds and the whole typed document. Initialization locks/checks the
whole selected table empty, requires one affected row, validates readback and
checks commit/rollback. A committed but unacknowledged seed cannot be overwritten
by retry. Constructors/requests never install/seed/repair. There is no update,
delete, browser editor, revision history or automatic seed generation. Changes to
an installed catalog need a separately reviewed version/revision migration; do
not drop/reset the table as a routine edit. Runtime grants need SELECT only.

## Explicit provisioning and evidence

Review database/mssql/011-schema-profiles.sql for a designated website-owned SQL
Server target. Runtime SELECT and separate operator SELECT/INSERT grants, SQL
Server syntax/driver/TLS/collation/locks and restore remain unverified. No MSSQL DDL or
real SQL Server connection ran here.

Privately prepare schema-profiles-v1.json in an existing new-project sandbox
outside webroots/legacy. The source must be regular, canonical/unlinked and
single-link. Reads are bounded and shared-locked with open-file identity checks;
freeze edits from reviewed dry run through cutover because no compatible live
source writer is supplied. Missing source never means an empty/default catalog.

```powershell
& 'C:/xampp/php/php.exe' scripts/import-schema-profiles.php
```

This defaults to a redacted canonical SHA-256 dry run with no database access or
source edits. Apply requires the installed empty target, explicit
ZENDA_SETTINGS_MIGRATION_SCOPE=owned-nonproduction, ZENDA_SETTINGS_BACKEND=mssql,
private existing connection inputs and --apply --expected-sha256=<reviewed-digest>.
This is not production apply authorization. The four-family configuration
preflight does not include this new catalog.

tests/schema-profiles.php adds 117 owned SQLite/process/CLI assertions for typed
source/direct/SQL validation, canonicalization/bounds, authorization before access,
escaped inspection, owning reads, failed/zero-row/false/ambiguous initialization,
immutable seed races and guarded source-preserving CLI. The shared HTTP suite adds
15 cases (222 shared total) covering custom defaults, strict selection, private
missing/corrupt state, no GET seeding/fallback, draft labelling and unavailable
database-mode shop/VIP execution with unchanged audit/state. Existing 53 core,
58 operational and all market/native-safety assertions remain. New browser QA,
actual SQL Server and complete physical distribution remain missing. Decision 097
adds isolated draft catalog persistence/provisioning, with its own owned evidence
in docs/PREMIUM_CATALOGS.md; this does not enable the database storefronts.
