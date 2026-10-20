# Modules and capabilities

Decision 109 introduces the mandatory private Kernel/Apis owner, checked in architecture/policy.json with only V1.Authorization allowed. Bootstrap/Apis adapts existing V1 RankingReader consumers into Order without cross-owner module imports. Chaos/Order/Singularity are primary; provider APIs below are derived. Static production declarations remain planned. See [integration](docs/api/WEBSITE_INTEGRATION.md).

Decision 105 introduces mandatory kernel approval persistence, not a removable
business module or an enabled static feature. Kernel owner/layer/contract edges
live in architecture/policy.json. The nine module declarations and optional
premium packages remain unchanged; new plugin permissions are not automatically
granted to current roles. See docs/PLUGIN_APPROVAL_STORAGE.md.

Decision 104 adds kernel composition support for data-only package discovery,
not another business module or executable provider. The nine static declarations
and all production activation gates are unchanged. See
docs/PLUGIN_RUNTIME_MIGRATION.md for inspected ownership/consumer migrations and
docs/PLUGIN_MANIFEST.md for the canonical distribution format.

## Main / game / database / schema provider package types

All packages use `/plugins/<id>/` and the same `MainAPI` lifecycle. A plugin can provide a **GameType** (e.g. `zenda.game.mu`), an optional **DatabaseType** for external game data (e.g. `zenda.db.mssql`), a **SchemaAdapter** for one game's emulator/layout (e.g. `zenda.schema.muemu`), or be an ordinary feature consuming typed capabilities (`zenda.rankings`). No engine, game or emulator should be hardcoded in the kernel registry. Explicit game profiles in website MSSQL bind verified providers; missing providers block only affected profiles/required dependencies. The kernel's website-owned MSSQL connection is **not optional** and is not provided by removable `DatabaseType` plugins. See `API_ARCHITECTURE.md` and detailed API docs. These registries are planned, not deployed.


## New priority: truly hot-unpluggable folder plugins (target only)

The authoritative product requirement is **not** merely modular source code.
Eventually each optional feature is a standalone folder at
`plugins/<vendor>.<slug>/` and uses the same versioned `PluginAPI`, whether free,
premium, first-party or third-party. Adding an already trusted compatible folder
makes its service capabilities, routes, admin menus, listeners, jobs and widgets
available on the next request, without manually editing central module lists or
rebuilding Composer. Removing that folder removes these surfaces on the next
request, without affecting core/unrelated packages or deleting MSSQL state.
Unknown executable plugins need approval before activation; required dependents
are blocked safely; optional dependents degrade gracefully. A plugin can be
reintroduced and reuse its saved data. See `PLUGIN_SYSTEM.md` and
`PLUGIN_DEVELOPMENT.md` for the normative loader/dependency/lifecycle contract.

**Current reality:** `architecture/modules.json`, `capabilities.json`,
`packages/premium/*` and `src/Modules/*` are static/partial and do not constitute
a filesystem-discovered runtime plugin manager. The existing phrase *'no general
plugin installer'* below remains accurate. Migrate old static modules gradually;
protect frozen contracts and keep free/premium implementations in physically
separate plugin packages. Every ordinary website feature should ultimately be
a removable plugin; the small core keeps bootstrap, admin security and plugin
infrastructure. Do not enable unverified MU-native write/payment capabilities.

Decision 103 adds GameData-owned verified PDO_ODBC for website event/loot documents
and hardens shared initialization/write/readback/replay/rollback acknowledgements.
There is no Admin internal dependency, new public port/schema or native activation.
Owned SQL evidence includes locks, grants, races and the eight-MiB wire boundary;
guide SQL HTTP/calendar remains pending. See docs/GUIDE_ODBC.md.

Decision 102 adds actual SQL HTTP evidence for the existing administrator/core/
operational settings boundaries via test-only Bootstrap injection, with no module
activation, public contract or production runtime change. Its restricted principal
and private loopback composition are described in docs/MSSQL_HTTP.md. Other routes/
stores, strict default-factory TLS and whole-site cutover remain pending.

Decision 101 adds an Identity-owned ODBC SQL/ASCII profile for its separate
administrator store, with exact initialization readback and private rollback
errors. Its existing CredentialStore/import/Bootstrap signatures remain unchanged;
no Admin implementation import, public contract or module activation is added.
The opt-in SQL suite now includes reviewed DDL 009 and real grants/locks/races.
Other Identity stores and full SQL HTTP composition remain pending.

Decision 100 records the planned guided installer/setup, verified recovery and
AdminCP factory reset to default website configurations with setup available
again. This is a requirement note, not an implemented or enabled operation.
Installation state remains in MSSQL; MU game/account data is outside reset scope,
including shared database topologies. See docs/INSTALLATION_LIFECYCLE.md.

Decision 099 adds explicit PDO_ODBC support to Admin's existing core/operational
journal only, plus its ordinary connection guard suite and separate opt-in owned
SQL suite. Driver/Unicode probes, safe private connection parameters and wire
encoding stay in Admin Infrastructure. Public contracts, module activation and
other owners' SQLite/sqlsrv allowlists are unchanged. Bootstrap/importers continue
using the existing factory signature; no consumer imports another module's store.
Whole-site ODBC/HTTP integration remains pending. See docs/MSSQL_LAB.md.

Decision 098 declares V5.WebshopCatalogInspection/VipShopCatalogInspection,
webshop.catalog.inspect/vip-shop.catalog.inspect, database.inspect permissions,
Identity authorization dependencies and fixed inspection entrypoints for each
premium owner. Their private service/controller/view/lazy store remain inside the
respective package and use only public Authorization/PageLayout for other owners.
Core loads typed optional ports after authorization and never imports commercial
internals. Module status remains planned; private reads activate no public shop,
license or native operation. See docs/PREMIUM_CATALOGS.md.

`architecture/modules.json` declares nine planned responsibility groups:
Identity, GameData, Content, Community, Commerce, Templates, Admin, Webshop and
VipShop. Decision 096 extracts Webshop into its premium root with a planned V3
preview binding; production module status stays planned. Decision
095 physically extracts the VIP catalog, pure preview service, controller and
view to its premium root, with a V2 preview binding and fixed offline entrypoint.
Initial boundaries were reviewed and continuation approved on 2026-10-17. The
catalog names dependencies, versions, permissions, contracts, bindings and tests.
Full production modules remain planned. Identity and Content now have a real
partial local sandbox implementation, recorded separately in runtime.json; this
does not activate unimplemented MU account or game capabilities.

Decision 097 adds Webshop/VipShop module-local draft CatalogStore ports, strict
documents, immutable SQL stores and explicit source readers in their respective
premium packages. V4 CLI provisioning bindings, capabilities and fixed catalog
entrypoints are declared while all production statuses remain planned. Inspect
never connects; apply validates designation and source SHA before its lazy
website connection. Neither package imports another owner's store or requires the
other package. Database storefronts, native commerce and licensing remain unavailable.
See docs/PREMIUM_CATALOGS.md; V1–V3 freezes and preview ports are unchanged.

Identity binds the existing Authorization contract to SandboxAuthorization.
Content consumes that port through its declared Identity dependency. Internal
module-local ports describe credentials, sessions, throttling and news storage;
their implementations stay inside the owning module. Bootstrap composes them.
NewsService validates content and requires content.manage before any repository
write. Session claims and submitted actor IDs never grant authority.

Content also has an internal PublishedNewsReader and anonymous HTTP transport
port for Drupal news. The explicit Drupal binding is read-only in Zenda;
authoring/publication/revisions are handled by Drupal. Existing offline file/PDO
news repositories implement the same query port. See docs/DRUPAL_NEWS.md.

`architecture/capabilities.json` ties stable capability IDs to one provider,
contracts and permissions. Schema-specific operational availability must be
derived from a verified adapter, not from catalog presence. For example,
`game.mutate` is declared for planning and is disabled in config.example.json.

Capability permissions describe the provider's operation boundary, not a blanket
requirement for every public read. Public sanitized content/community/catalog read
capabilities have no management permission. Template edit and publish permissions
are separate; the existing `templates.edit` store capability lists the union of
its operation permissions. It never grants publication authority by itself:
saveDraft requires templates.edit, publish requires templates.publish. Implement
and test that method-level mapping before enabling the provider.

**Current transitional procedure (not the destination):** to add a static module,
declare its identity/dependencies/capabilities/contracts, implement ports in its
Infrastructure, compose bindings in Bootstrap, and add permissions/tests. Consumers
import Contracts only; no cross-owner internal imports or unauthorized DB access.
**New default instead:** create a complete standalone folder in `/plugins` and
register contributions dynamically via `PluginAPI`, without editing central
bindings/capability lists for each package. See `PLUGIN_SYSTEM.md`. There is
**currently no general plugin installer**, so this must be implemented and
tested before claiming the desired drag/drop experience. AdminCP never executes
arbitrary uploaded PHP; newly installed unknown packages require explicit trust.

## Open-source core and commercial encrypted modules

Future distribution architecture combines an open-source core with commercial encrypted modules:
- **Open-source kernel plus free plugin packages**: The always-present kernel
  contains framework/bootstrap, authentication/AdminCP authority, plugin loader,
  public contracts and free builder host. Rankings, news, profiles, drops and
  other optional first-party functions move into independent free plugin folders;
  current mixed modules remain transitional until extracted and verified.
- **Premium/paid modules**: Inventory/warehouse functionality, visual webshop,
  player item market, character marketplace, guilds and castle siege are paid;
  individual commercial modules may be encrypted under per-function licenses.
- **Payment modules**: PayPal and MercadoPago are free; every other payment
  method module is premium/paid.
- **Template builder and packs**: The GrapesJS builder is free/core. Free/default
  templates may be included; premium predesigned templates are paid. Template
  bundles and their assets must be physically separated into free and premium
  directories/packages, independently distributable. Free editing and free
  templates must not depend on a paid template pack.
- **Physical packaging**: A common `/plugins` discovery root holds separate
  self-contained directories for each independently installable free or premium
  package (for example `/plugins/zenda.news` and `/plugins/zenda.webshop`). Never
  mix free and paid implementation inside one folder or require a premium package
  to run the kernel/free gateways. The earlier `packages/premium/*` roots are
  migration sources, not an additional permanent runtime discovery requirement.
- **Contract and isolation invariants**:
  1. The open-source core and public contracts (`src/Contracts/V1`) must remain completely
     independent of commercial module internals.
  2. Commercial modules implement declared public or module-local ports cleanly so they can
     be dynamically enabled or swapped without altering the core codebase.
  3. Feature licensing checks must occur at the capability/service boundary, failing closed
     if an active valid license is not present.

These licensing checks apply to paid capabilities. Free PayPal/MercadoPago
modules do not require a paid gateway license. All gateways still require their
provider verification, replay protection and audited fulfillment boundary.
Current GameData/offline responsibilities mix future free and paid functionality;
their physical split and distribution manifests remain unimplemented. This note
classifies future packages and does not activate modules or move existing paths.

Decision 094 prepares package-aware validation/autoloading for Webshop and VipShop.
SourceLayout fixes their module/composition roots in code and the catalog records
matching packageRoot values. Every PHP file under src/ and packages/ reaches lint
and architecture validation, including unknown roots and nested vendor/var paths.
Core cannot import premium internals or composition; each premium owner can use
its own layers and declared public contracts. Package composition stays under
Bootstrap. Exact static binding checks and conservative impact analysis use these
same roots. This neither installs packages nor proves complete free distribution:
the original GameData split and licensing migration were still pending at that checkpoint.

Decision 095 now moves VipPackage, VipShopService and the VIP view out of core into
packages/premium/vip-shop. It provides V2.VipShopPreview and consumes the free
Content V2.PageLayout contract through a declared dependency. Content provides the
shared theme shell and imports no premium code. All production statuses remain
planned; vip.preview is a synthetic local-sandbox declaration, not native commerce
or paid-license activation. SourceLayout/module metadata agree on the fixed owned
Bootstrap composition closure; OptionalVipShop loads it only when explicitly
selected for offline mode, verifies the public result and redacts failures.
No runtime registry, user-selected PHP path or service locator was introduced.
Decision 096 also moves Webshop's catalog/pricing, display projection, prototype
order model and presentation to packages/premium/webshop. It provides the new
independently frozen V3.WebshopPreview and consumes V2.PageLayout. OptionalWebshop
selects its fixed local-sandbox factory with strict result checks and private
failures. The pure proposal has no readers/writers/warehouse/order/audit or native
bytes/serial/slot output; it cannot fulfill an item. V1/V2 remain frozen unchanged.
Actual package-isolated HTTP cases exercise missing/broken storefronts and core
availability. See docs/WEBSHOP_PREVIEW.md. Other GameData paid responsibilities, distribution/test manifests,
licensing and installed catalog revisions remain pending. Decisions 097/098 now
prepare immutable catalog storage and private administrator inspection.
See decision 083, which refines decision 070.

Decision 085 extends physical separation to template bundles. Template packaging,
catalog/entitlement handling and the live editor remain pending. Existing
projects/assets have not been moved by this documentation change.

Identity's separate administrator credentials now share strict file/direct-import
validation and SQL owning initialization with ambiguity/corruption denial.
No player/game role grants access, and SQL failure never selects file credentials.
See docs/ADMIN_CREDENTIAL_STORAGE.md and decisions 086/101; owned local SQL Server
credential evidence exists, while provisioning/rotation/audit operations and
trusted-TLS HTTP deployment remain unverified.

Identity's prepared SQL profile store now validates complete bounded current,
command and event snapshots inside owning read/write transactions. Runtime saves
and imports share the global profiles boundary so concurrent owners cannot exceed
capacity; every write requires one affected row and checked commit. Historical
replay preserves later privacy. Decision 088 records strict corrupt-state refusal
without V1/DDL changes; new SQLite/process/HTTP evidence does not certify SQL Server.

Admin's internal RevisionedSettingsStore supports the core settings snapshot,
expected revisions and atomic command history. Bootstrap binds an explicit
MSSQL connection or the old standalone offline file prototype; public/player
maintenance consumers use the same selected store. SQL Server operation is not
certified by the SQLite/HTTP fixtures. See docs/WEBSITE_CONFIGURATION.md.
Admin also owns the internal OperationalSettingsStore and typed version-2 news/
runtime record. Bootstrap alone composes its Drupal and upload consumers; modules
do not import Admin internals. The adapters share the fixed-table journal while
preserving core version-1 records and separate revision histories. New operational
unit/HTTP/browser coverage is detailed in docs/OPERATIONAL_CONFIGURATION.md.
Decision 092 hardens their shared PdoConfigurationJournal without adding module
families or changing signatures: coherent owning reads, contiguous typed history,
latest document/request agreement, checked one-row writes and checked commits/
rollback for read/replay/save/initialization. Existing history hashes are retained;
older settings are not a restore archive. Actual SQL Server and remaining module
catalog/package migrations are still pending.

Use `php scripts/impact.php --format=dot` for the dependency graph, and
`--changed=src/Modules/GameData/Domain/Inventory.php` to find direct and indirect
consumers and declared tests. Unknown paths and shared contract/policy changes
conservatively select all modules. Dynamic integrations need explicit edges and
cannot be reliably inferred from PHP source alone. Declared tests now include
Identity, Content, view and loopback HTTP behavior for the implemented slice;
future game modules still have no runtime behavior tests.

Decision 093 adds GameData's module-local schema profile catalog store/service.
It supplies only the database-mode administrative mapping inspector, under
database.inspect, with immutable explicit SQL provisioning and draft-only labels.
It configures no native reader/writer and grants no capabilities. Missing SQL
catalogs refuse without built-in/file fallback. Database-mode shop/VIP routes now
refuse while their catalog persistence is missing; standalone synthetic previews
remain. docs/CATALOG_CONFIGURATION.md inventories current consumers and intended
premium paths. No classes were moved and physical distribution remains pending.

Milestone 6 adds MU-specific sandbox market preparation inside GameData, alongside
the existing shop slice. Internal asset/draft ports reuse V1 Authorization with
explicit `market.draft` and `market.manage` permissions. Atomic website draft
persistence does not activate Commerce or game mutation capabilities. See
[market scope and pending transfer boundaries](docs/MARKETPLACE.md).

GameData's market import now shares complete v1 metadata history validation with
the file writer, preserving escrow payloads and actual event actors/operations/
clocks. Runtime/import acquire the same website listings boundary and require
every affected row/commit. Decision 090 covers strict existing-source/all-empty-
target migration and checked SQL writes. Decision 091 adds module-local
PdoMarketSnapshot, validating all four tables under owning coherent reads and
before replay/after writes. Typed bounded history/head/escrow agreement, clocks,
global capacities and legacy outcomes are checked without changing contracts.
Metadata never establishes native custody/payment/delivery. Stronger request
identity and actual SQL Server verification remain pending.
No premium packaging migration or core dependency on premium internals was added.

Community now has a real website-owned support slice using internal SupportStore
and frozen Authorization. Separate website-player sessions provide support.use;
sandbox staff receive support.manage. GameData additionally provides guarded
synthetic Siege views and a persistent website-maintained drop guide (loot.manage).
Their module test paths/runtime scopes are declared without enabling unverified
production module capabilities. See docs/PLAYER_SUPPORT.md and docs/SIEGE_AND_LOOT.md.

Community's SupportState now shares full v1 command/history validation across
file/SQL/import. Its fixed-table PdoSupportDocument owns coherent bounded reads,
revision/replay/message/event writes and complete empty-target imports under one
global cooperating-writer boundary. Decision 089 records strict corruption/source
refusal, checked commits, active/global cap serialization and existing DDL subject
bounds. New owned file/SQLite/process/HTTP/CLI tests do not certify SQL Server.
Core/free support imports no premium internals; physical distribution remains pending.

GameData also owns persistent website timetable editing (`events.manage`) and
read-only bounded calendar downloads. EventScheduleStore/Manager/CalendarService
are internal extension points composed by Bootstrap; website-player roles cannot
grant editing authority. GameData declares event and HTTP suites so affected
validation includes its executable route consumers. See docs/EVENT_TIMETABLE.md.

Identity additionally owns website profile editing/publication/private preview
under self-scoped profile.manage, with private defaults and atomic revision/replay
history. It declares profiles/playeridentity/HTTP suites. Identifier validation
matches signup across Identity and Community support; this grants no staff or
native authority. V1 consumers are unchanged. See docs/WEBSITE_PROFILES.md.

Identity's authenticated website password workflow binds internal
WebsitePasswordStore and a separate limiter to PlayerIdentityService. Both file
and PDO account adapters implement the atomic credential/token/history boundary.
Dedicated passwords/password-http tests are declared alongside existing identity,
recovery, profile/support and HTTP consumers; fixed player permissions and public
contracts remain unchanged. See docs/WEBSITE_PASSWORDS.md.

Content's published announcement archive/search/detail queries reuse its existing
reader and public content capability; no new write authority is added. Its
declared news/content/view/HTTP suites cover query/escaping/consumer compatibility.
See docs/ANNOUNCEMENTS.md. Production module activation remains planned.

GameData's unchanged internal EventScheduleStore now also binds a module-owned SQL
adapter through Bootstrap. Pure EventTimetableState is shared by its offline file
and SQL infrastructure; no Admin implementation is imported inside GameData.
Timetable/calendar/staff consumers use the selected binding. Dedicated event-
settings unit/worker tests and shared settings HTTP cases cover full-history
migration, revision/retry behavior and fail-closed database authority. Actual SQL
Server remains unverified; production module activation stays planned.
LootGuideStore now has its own SQL binding and declared loot-settings worker/unit
suite. GameData shares only its own fixed-identity transaction/import helpers;
event/loot tables, revisions and histories stay separate. Admin's declared source
preflight tests cover Bootstrap's four-reader metadata plan without a database.
