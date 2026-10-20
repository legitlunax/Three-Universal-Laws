# Prepared Webshop and VIP catalog storage

Decision 098 binds private administrator inspection at GET /admin/catalogs
(default module=webshop; module=vip-shop selects VIP). The dashboard links to it.
The separate website staff session must pass database.inspect on admin before
factory loading or catalog connection. Players, native GM flags and browser
actor/permission claims grant no access. Each package checks authorization again
in its own application service before the lazy store opens the website connection.
Only database settings mode can read stored drafts; file mode returns 503.

New independently frozen V5.WebshopCatalogInspection/VipShopCatalogInspection
ports render private escaped HTML through existing V2.PageLayout. Each package
owns its service/controller/view and fixed InspectionComposition.php entrypoint;
core sees public ports only. One authorized selected read shows draft status,
entry count, canonical SHA-256 and a collapsible escaped document. It exposes no
forms, purchases, balances or native game information. Missing packages/tables,
empty or corrupt documents and transport failures produce private redacted 503,
without DDL, repair or code/file fallback. Invalid selections return 422 before
connecting; POST is 405 under the existing global Origin policy. Responses keep
private/no-store, CSP and nosniff. This does not provide storefront selection,
licensing, public SQL catalog projections or installed draft editing/history.

Owned unit/SQLite and loopback HTTP tests cover permission-before-connection,
same-name player role isolation, exact stored documents, escaping, immutable
query-only reads, independent failures and unchanged public storefront denial.
Manual Playwright inspection covered both pages at 1366x900 and 390x844 with
expanded documents: text remains inert, the selected tab renders correctly and width
stays within the viewport. Screenshots are disposable reports/catalog-inspection-*
artifacts; this is not an automated visual regression suite or SQL Server evidence.

Decision 097 adds immutable website-owned draft catalogs inside each premium
package. Package domains validate documents; module-local CatalogStore ports read
them; PdoCatalogStore owns SQL reads and explicit initialization. The services'
fromStore factories use only the selected stored document and propagate failure,
without choosing code defaults. Existing constructors/default factories remain
local file-sandbox examples. Browser/database storefront activation is unchanged:
GET and valid authenticated POST still return private 503 in database mode.

| Owner | Provisioning input | Installed table | Reviewed installation input |
| --- | --- | --- | --- |
| Webshop | webshop-catalog-v1.json | dbo.zenda_webshop_catalog | packages/premium/webshop/database/mssql/001-catalog.sql |
| VipShop | vip-shop-catalog-v1.json | dbo.zenda_vip_shop_catalog | packages/premium/vip-shop/database/mssql/001-catalog.sql |

Both documents require integer schemaVersion=1, status="draft" and an ordered,
nonempty collection (`items`: 1..500; `packages`: 1..50). Canonical JSON is at most
512 KiB. Source JSON must preserve object/list distinctions; numeric-key objects
and empty objects cannot masquerade as lists. Object keys normalize to declared
order; collection and label order are retained. Unknown/missing fields, coercion,
duplicate IDs/labels, invalid UTF-8, control/format characters, surrounding
whitespace and out-of-range values refuse. Text remains escaped plaintext in
consumers; validation does not make it executable HTML or certify native items.

IDs are lowercase `[a-z][a-z0-9_-]{0,63}`. Names/tier names are 1..120 bytes.
Prices are exact integers 0..1,000,000,000. Webshop additionally requires group
0..15, index 0..511, category 1..64 bytes, description 0..1000, maxLevel 0..15,
maxOption 0..28, maxSockets 0..5, four boolean option flags, currency
credits/wcoinC/zen and at most six unique Excellent labels of 1..96 bytes.
VIP requires tierCode 1..10, durationDays 1..365, currency credits/wcoinC, one of
bronze/silver/gold/platinum and at most 32 unique perks of 1..160 bytes. All fields
from each existing model's toArray shape are required; no model constructor clamp
or default supplies missing catalog metadata. Explicit empty Excellent lists stay
empty through WebshopItem::fromCatalog, including SQL-backed service reads.

These are draft metadata limits, not native MU schema or currency compatibility.
Existing V2/V3 previews retain their synthetic arithmetic and cannot debit,
activate VIP, allocate item bytes/serials/slots, create orders or fulfill purchases.
Their public signatures/behavior and saved template components remain unchanged.

## SQL ownership and provisioning

Constructors/reads never install, seed, repair or fall back to files. Only
exception-mode sqlsrv or owned SQLite fixture connections are accepted. Reads
own transactions and validate exactly one typed singleton, schema, raw JSON digest,
size and full document. Initializers own a whole-table empty check and lock,
require one affected INSERT, validate readback and check commit/rollback. SQL
Server uses TABLOCK/HOLDLOCK or TABLOCKX/HOLDLOCK; SQLite uses a writer boundary
before its empty check. Committed but unacknowledged seeds cannot be overwritten
on retry. A transaction supplied by a caller is refused and left to that caller.

Runtime is SELECT only; operators separately require SELECT/INSERT on the
installed table. DDL is a package-owned installation input, never request code.
No update/delete, browser editor, revision history or installed-catalog replacement
exists. A future change needs an explicit reviewed version/revision migration;
dropping/resetting a table is not a routine edit. The two families remain independent
even in one website database. No game/account connection is used by these stores.

Privately prepare each explicit input in an existing new-project directory outside
webroots/legacy. Files must be bounded, regular, unlinked and single-link. Readers
hold shared locks and check open-file/path identity before and after reading.
Freeze source edits from dry-run review through cutover; no compatible live source
writer or globally atomic two-catalog source snapshot is provided. These files are
provisioning inputs only, never runtime configuration authority or fallback.

```powershell
& 'C:/xampp/php/php.exe' scripts/import-webshop-catalog.php
& 'C:/xampp/php/php.exe' scripts/import-vip-shop-catalog.php
```

Both commands require an existing ZENDA_SANDBOX_DIR. Default dry runs print only
the canonical SHA-256 and do not connect or edit sources. Apply additionally needs
--apply, --expected-sha256=<reviewed-digest>, ZENDA_SETTINGS_MIGRATION_SCOPE=
owned-nonproduction, ZENDA_SETTINGS_BACKEND=mssql and the existing private designated
website connection inputs. The source is reread and its canonical digest checked
before the lazy connection factory runs. Targets must already be installed/empty.
This is not authorization to apply in production. No actual SQL Server connection,
DDL or migration was executed here; the four-family preflight excludes these catalogs.

New independently frozen V4.WebshopCatalogProvisioning and
V4.VipShopCatalogProvisioning expose CLI-only inspect(directory) returning
`{schemaVersion:1,count:int,sha256:string}`, and initialize(directory,expectedSha256)
returning void only after acknowledgement. Source, SQL and factory failures are
redacted without private exception chains. Fixed SourceLayout catalog entrypoints
and typed factories prevent arbitrary PHP path selection. Free scripts/loaders
use public ports only; premium code uses its own modules and public contracts.
Each package includes its own storage/provisioner/DDL and does not require the
other package. V1–V3 source, catalogs and baselines were not refreshed.

Owned tests exercise typed/source/SQL corruption, exact model retention, strict
JSON shapes/byte bounds, transaction and insert failures, ambiguous commits,
guarded/lazy fingerprint checks, source lock contention, hard-link refusal and
independent-process initializer races. Actual SQL Server syntax, UTF-8 driver
results, table locks, grants, TLS and backup/restore remain unverified. Website
storefront bindings/activation, licensing/encryption, native commerce, full
distribution manifests and automated visual regression remain pending. No catalog or license
record by itself can activate native fulfillment.
