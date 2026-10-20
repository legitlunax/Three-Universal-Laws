# Runtime audit and incremental migration map

Decision 109 adds the three-primary-API integration map in [api/WEBSITE_INTEGRATION.md](api/WEBSITE_INTEGRATION.md). MainAPI/provider namespaces below remain future derived SDK contracts. Health/home/ranking/template reads now use a private Order bridge; all other consumers and lifecycle requirements below still apply.

Decision 105 implements the approval portion of the selected Kernel owner and
lazy Bootstrap composition described below. See PLUGIN_APPROVAL_STORAGE.md for
its internal service/storage contract. Decision 106 adds separate configuration
and unverified attempt receipts; see PLUGIN_METADATA_STORAGE.md for the remaining
verified-installation/provisioning boundaries. This does
not turn the remaining SDK/HTTP/worker hooks into implemented functionality.

Decision 104; implementation-plan step 01. This is a design/consumer map, not a
second progress ledger. `STATE.md` records completion and verification. No static
module, public V1-V5 contract or installed database is migrated by this map.

## Current bindings and consumers

| Boundary / owner | Actual current source and consumers | Target hook and compatibility requirement | Evidence required |
| --- | --- | --- | --- |
| HTTP composition / Bootstrap | `public/index.php` creates `Application`; `Application::handle` checks loopback, origin, body size, methods, then a fixed `$allowedPaths` list. `Request::path` already rejects encoded traversal. All HTTP suites consume this ordering. | Integrate a request-local plugin route table before unknown-path rejection, after shared request guards; keep fixed paths reserved during migration. Route collision must block registration, not shadow a handler. No per-plugin PHP branch. | Existing `tests/http.php`, `settings-http.php`, player/staff/catalog HTTP plus new live plugin lifecycle suite. |
| Autoload / Bootstrap | `autoload.php` delegates to `SourceLayout::locate`; fixed Webshop/VipShop maps also feed Composer prefixes and tooling. `tests/packages.php` exercises absent packages and core shadows. | Preserve this loader for current sources. Separate approved per-request plugin namespace maps; no plugin entry in Composer or `SourceLayout::packages`. Check package identity before loading and handle persistent workers/OPcache explicitly. | Absence, changed source, wrong-case namespaces, stale classmaps, two HTTP workers and worker generations. |
| Security / Identity + Bootstrap | `Application` builds `NativeSession`, `SandboxIdentityService`, `SandboxAuthorization`; `PlayerApplication` has a separate player boundary. The authorization adapter accepts limited resource IDs including `admin`. | Host actions use trusted administrator actor, explicit permission and `admin` scope through frozen `V1.Authorization`. Introduce new grants deliberately; do not invent plugin resource IDs the existing adapter rejects or treat player role names as staff authority. | Authorization before connection/action, CSRF/Origin/throttle, same-name player and GM denial; existing identity/password/admin HTTP consumers. |
| Website storage / Admin, Identity, Kernel/Plugins, Bootstrap | Existing settings composition remains; Kernel/Plugins now owns separate approval/configuration/unverified attempt journals through private lazy Bootstrap connections. Verified installation/execution and operator endpoints remain pending. | Keep website SQL independent of optional DatabaseType, with no Admin private imports, production file fallback or request DDL. Retain current settings and SQL HTTP consumers while completing step 03. | Kernel CAS/replay/audit/process and owned SQL grant/rollback cases exist; executable empty-plugin website SQL/admin HTTP remains pending. |
| Diagnostics/AdminCP / Content + Bootstrap | `SiteView::renderDashboard` builds fixed links; routes and permissions are fixed inside `Application`. No general Admin extension registry exists. | Scoped namespaced menu/page projections from active contributions; website identity remains separate. Current links keep working until their feature migrates. UI filtering cannot authorize handlers. | Plugin page/menu lifecycle plus existing `tests/views.php`, admin and catalog HTTP. |
| Templates / Templates + Bootstrap | `DefaultComponentRenderer::render` branches on frozen `zenda.rankings` and `zenda.account`; `TemplateService`, `TemplateStore`, published resolvers and Bootstrap manage projects/preview. No live GrapesJS registry/editor exists. | Adapter from frozen component IDs to a versioned active widget registry; unknown/missing plugin handler renders a safe placeholder without changing JSON/revisions/pointers. Preserve actor-exact draft access and anonymous published-only reads. | `tests/templates.php`, `template-storage.php`, migration fixtures and later real editor/browser round trips; saved-project lifecycle tests. |
| Assets / public router + Templates | `public/router.php` serves only two fixed assets; private template assets use protected application routes. | Namespaced plugin asset dispatch tied to active package identity, separate from private uploaded media. Plugin PHP and arbitrary filesystem paths never become a public webroot. | Asset add/remove/readd, MIME/path/auth/security-header checks and existing template asset consumers. |
| Events/jobs/commands / future host | Focused searches find no general EventBus, JobRegistry or plugin worker. Existing event timetables are website guide configuration, not a scheduler extension. CLI scripts are fixed explicit commands. | New scoped event/job/command registries and worker generation checks; never retrofit discovery into existing migrations or imply native event execution. | Independent worker and HTTP proofs; disappearance before claim/dispatch, retries, registration failures and partial-snapshot rollback. |
| Free read features / GameData + Content | `RankingService`, `SyntheticRankingReader`, `PdoRankingReader` and fixed `/rankings` feed public views; news consumes Drupal published projections. Current mapping catalog is draft metadata only. | `zenda.rankings` consumes a normalized per-profile read port; adapters keep old `V1.RankingReader` consumers stable. New feature plugin cannot import GameData readers/SQL. Drupal authoring remains external. | Synthetic second-game contract fixture, realm isolation, read-only adapter evidence, existing game/content/news/view suites. |
| Provider layers / future host | No executable GameType/DatabaseType/SchemaAdapter registries exist. `API_ARCHITECTURE.md` and four manuals are specifications. | Independently versioned provider interfaces, explicit website-stored binding per game/account/realm. Transport cannot infer emulator; probe cannot grant writes. | Independent removal of all three provider kinds; wrong/ambiguous schema/driver, no auto-fallback, website SQL remains usable. |
| Premium / optional existing packages | `OptionalWebshop*`/`OptionalVipShop*`, fixed package composition and V2-V5 ports preserve preview/provision/inspection. Current public database shops refuse. | Keep these bridges until each complete premium plugin has equivalent consumer tests. Free host/features/templates/gateways cannot import paid internals or require paid files. | Existing packages/webshop/vip/catalog suites; new per-package absence and lifecycle evidence; no native fulfillment enabled by catalogs/licenses. |
| Tooling / scripts | `Checks::architecture` checks Contracts/Modules/Bootstrap plus explicit Kernel owner/layer/contract policy, frozen V1-V5 and static module layers. `SourceLayout::sources` walks `src` and `packages`, not `plugins`; impact conservatively includes all module groups for kernel changes. | Keep all old checks. Add SDK catalog and plugin source ownership rules when executable packages are introduced; token inspection must not execute unknown PHP. Runtime membership comes from discovery, never a static list of installed plugins. | Kernel owner/layer denial tests exist. SDK/private-plugin import, module/SDK adapter, bundled vendor and executable source checks remain pending. |

The initial safe HTTP proof can use a small first-party read-only example rather
than moving a current feature immediately. A fixture package is not a migrated
module or working MU integration. Every newly executable optional package needs
the mandated real HTTP add/remove/readd proof for all registered surfaces.

## Selected contract and composition design

1. Distribution format: exactly `docs/PLUGIN_MANIFEST.md`; bounded explicit
   release-version intervals, typed provider/capability declarations and full
   file inventory. Remove conflicting illustrative JSON from other manuals.
2. Future SDK namespaces: `Zenda\PluginAPI\Main\V1`,
   `Zenda\PluginAPI\DatabaseType\V1`, `Zenda\PluginAPI\GameType\V1`,
   `Zenda\PluginAPI\SchemaAdapter\V1`; separate catalogs under
   `contracts/plugin-api/<layer>/v1/`. These are selected **future** locations,
   not existing/frozen contracts. Each layer gets independent interface/shape/
   behavioral tests and an initial freeze only when implemented and reviewed.
   Never whitelist arbitrary `V6` or regenerate old V1-V5 baselines.
3. Discovery/autoload composition belongs to `src/Bootstrap/Plugins`. New kernel
   ledger domain/application/SQL adapters belong to a separately checked
   `src/Kernel/Plugins/{Domain,Application,Infrastructure}` owner, introduced by
   decision 105 and extended by 106. Kernel policy is checked; adding `PluginAPI` requires explicit architecture,
   exact catalog and adversarial test support; do not exempt these roots from
   layer/database/import restrictions. No new public PHP contract is needed for
   today's internal data-only parser/discovery classes.
4. Registration produces a staged per-plugin contribution set; publish it into
   the request snapshot only after all declarations/dependencies/collisions pass.
   Core paths and currently owned static paths are reserved. In step 04 reject
   all cross-plugin collisions deterministically; no priority/first-loaded wins.
   Later feature extraction transfers ownership explicitly, with old-consumer tests.
5. Scoped handles must enforce manifest declarations and server-owned policy.
   Never expose a global container, credential, PDO or generic SQL/write session.
   Missing required plugin deps block the consumer; optional missing ports return
   typed unavailable. Provider selection is explicit and profile-specific.
6. Fingerprints cover raw manifest and all distributed files/directories.
   Publisher name and matching hashes do not establish trust. Approval must bind
   exact ID/version/publisher/fingerprint and permissions in website MSSQL;
   changed executable/dependency bytes return to pending approval. Revalidate
   presence/identity before activation/dispatch and each worker claim. PHP's
   already-declared classes cannot be unloaded: version changes in persistent
   workers require a fresh process/generation strategy before execution.

## Next concrete changes after discovery

Step 03 designs and implements a minimal kernel plugin state store, explicit
MSSQL DDL/provisioning and authorized revisioned commands. Pass private connection
factories from Bootstrap and reuse public authorization; do not import Admin's
private journal or driver helper. Verify actual SQL behavior in fresh marked lab
fixtures only. Store decisions and retained data independently of folder presence.

Step 04 adds only the necessary first MainAPI contracts/registries, trusted
autoload and route integration, with unchanged-package HTTP removal/re-add and
changed-package approval denial. Add truthful coverage metadata before claiming
runtime behavior. Steps 05-06 complete surfaces/workers and AdminCP lifecycle UI;
the provider APIs then follow the independent order in IMPLEMENTATION_PLAN.md.

Known integration gaps remain: whole-site SQL/default factory/TLS, existing
eager module reads, arbitrary persistent-worker/OPcache upgrades, all SDK and
native provider behavior, real editor bridge and installation/reset. No legacy,
game/account database, payment or template publication gate is widened here.
