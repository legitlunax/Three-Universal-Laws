# Legacy functional inventory

Discovery date: 2026-10-17. This is a static, read-only source inventory for the mandatory Phase 1–3 review. No legacy PHP, HTTP entry, scheduler, installer, database query, provider transport, or legacy test was executed. No installed setting, credential, player record, private key, license payload, or operational log content is reproduced.

## Evidence and limits

Unless prefixed otherwise, every source reference is relative to `C:/xampp/htdocs/ZendaMod/license/private/commercial/website/`. A `file:line` reference points to inspected source, not proof of deployment activation or successful runtime operation. Exact filenames in the appendices were enumerated from their narrowly scoped directories. Source may change after discovery; recheck an affected anchor before implementation.

The governing legacy instructions were read at `C:/xampp/htdocs/ZendaMod/AGENTS.md:1`, `C:/xampp/htdocs/ZendaMod/PROJECT.md:1`, and website `dev/AGENTS.md:1`. The existing `dev/docs/architecture/MODULE_MAP.md:1` is useful historical context but is stale: the current source includes Commerce Core, WebShop, item market, Workshop, warehouse, and GlobalMu assets beyond that guide's earlier inventory. Current source takes precedence.

**Status vocabulary:** **runtime source** means a production entry or service is present and wired or referenced; **conditional** means configuration/schema/capabilities gate it; **adapter** means a compatibility redirect/include rather than an independent implementation; **reference-only** means developer material, not a live feature. None of these labels certifies the live installation. All new website feature reconstruction remains unimplemented at this gate.

## Artifact 1 — Functional requirements

| ID | Feature and user-visible requirement | Evidence and legacy status |
| --- | --- | --- |
| F01 | Public shell/home, news, server information, downloads, legal pages, contact, responsive navigation, ranking ticker, event cards, server branding, SEO, robots and sitemap. | `templates/default/index.php:9`, `:26`, `:31`, `:62`, `:109`; `includes/backend/src/PublicSite/Seo.php:7`; `includes/backend/src/Kernel.php:69`; `.htaccess:8`. Runtime source; theme/config dependent. |
| F02 | Registration, login/logout, account recovery, email verification, password/email change, account overview and connection history. | `includes/backend/src/Account/Workflows.php:44`, `:105`, `:134`, `:185`, `:236`, `:273`, `:312`, `:344`, `:365`; `includes/classes/class.login.php:33`, `:124`; `Kernel.php:103`. Runtime source. |
| F03 | Unified character selection/services: configured reset/stat/PK/master-skill/unstick/name/class/currency operations, character market and gift redemption integration. | `modules/usercp/character.php:2`, `:17`, `:26`, `:42`; `includes/classes/class.characterpanel.php:128`; `modules/usercp/changeclass.php:3`. Conditional runtime source; examine each action in CharacterPanel before migrating it. |
| F04 | Character sales: list, browse, inspect, cancel, purchase, transfer ownership and account slots, retain historical currency and commission. | `includes/backend/src/Character/Market.php:7`, `:50`, `:144`, `:170`, `:183`, `:235`; `modules/usercp/marketpj.php:2`. Unified runtime source with compatibility redirects. |
| F05 | Item marketplace: warehouse selection, list/buy/cancel/reclaim, listing history, public categories/browse, owner cancellation. | `modules/usercp/itemmarket.php:21`, `:31`, `:56`, `:73`, `:87`; `includes/backend/src/Modules/CommerceModule.php:31`, `:53`, `:168`, `:256`. Conditional runtime source. Public `/market` currently requires login and redirects (`modules/market.php:3`). |
| F06 | Multiwarehouse: inspect unlocked vaults, item/tooltips/grid occupancy, active vault switch, safe contiguous placement. | `modules/usercp/warehouse.php:2`, `:15`; `includes/commerce-core/Domain/Storage/MultiWarehouseService.php:59`, `:128`, `:259`, `:485`, `:613`, `:727`. Conditional runtime source. |
| F07 | WebShop: anonymous catalog browsing, account checkout, categories, single/custom/package products, option validation, tooltip preview, orders, stock/currency/vault delivery. | `modules/webshop.php:2`; `modules/usercp/webshop.php:2`, `:38`, `:85`, `:105`; `includes/backend/src/Modules/CommerceModule.php:406`, `:484`, `:504`, `:516`; `includes/commerce-core/Domain/Webshop/WebshopService.php:24`. Runtime source; anonymous POST/orders redirect to login. |
| F08 | Workshop: recipes, owned available character, warehouse ingredients and reservations, execution, outcome and delivery history, redirect-after-POST. | `modules/usercp/workshop.php:2`, `:5`, `:10`; `includes/backend/src/Commerce/WorkshopPage.php:37`; `includes/commerce-core/Domain/Recipes/WorkshopService.php:37`, `:53`, `:58`, `:163`. Conditional runtime source. |
| F09 | Economy ledger, idempotent submit/recipe operations, delivery queue, deferred retry, administrative retry/refund, audit. | `includes/commerce-core/Domain/Economy/TransactionCoordinator.php:38`, `:113`, `:120`, `:257`, `:288`, `:305`, `:307`, `:321`. Runtime source; website-owned schemas required. |
| F10 | VIP/real-money store: saved orders and destinations, product configuration, purchase status, gateway checkout, fulfillment and provider-specific callback rules. | `includes/backend/src/Kernel.php:71`; `includes/backend/src/Payments/Endpoint.php:20`; `includes/payments/Shop.php:1`; `modules/usercp/vipshop.php:1`. Runtime source for PayPal, Mercado Pago, Tebex, Paymentwall, Xsolla and a test provider; activation unverified. |
| F11 | Vote rewards/exchange, vote-site cooldowns, separate earning/delivery currencies, VIP durations, redemption and history. | `includes/classes/class.voteshop.php:57`, `:96`, `:209`, `:223`, `:326`, `:336`, `:357`, `:418`; `modules/usercp/voteshop.php:1`; old `vote.php` remains source. Conditional runtime source. |
| F12 | Social-sharing campaign preparation/claims, account-scoped cooldown and awarded-history state. | `includes/backend/src/Modules/SocialSharingModule.php:14`, `:19`; `Kernel.php:89`, `:99`. Runtime source. Do not interpret a user claim as provider-verifiable proof of sharing without examining provider rules. |
| F13 | Discord linking: consent/start, OAuth callback, verified profile, unique permanent account link and one-time configured award. | `includes/backend/src/Modules/DiscordLinkModule.php:13`, `:17`, `:21`; `includes/backend/src/Discord/Client.php:11`; `Kernel.php:90`, `:94`, `:96`; `api/discord/callback.php:1`. Runtime source, external credentials required. |
| F14 | Gift codes: administrator creation/enable/delete/history, normalization, configurable currency/VIP rewards, account redemption, character-required retry flow. | `includes/backend/src/Account/GiftCodes.php:35`, `:47`, `:75`, `:91`, `:101`, `:121`; `modules/usercp/giftcode.php:10`, `:15`, `:25`. Runtime source. |
| F15 | Lottery: staff catalog/scheduling/participants, joining, randomized ending, awards/claims, history and winner messages, automatic end. | `includes/backend/src/Lottery/Service.php:18`, `:55`, `:70`, `:93`, `:103`, `:119`, `:141`; `includes/cron/lottery.php:1`. Conditional runtime source. |
| F16 | Rankings, guild/player profiles, Hall of Fame/Honor, season-specific classes/events, ranking visibility and cached read displays. | `modules/rankings.php:1`, `modules/newrankings.php:1`; `includes/classes/class.newrankings.php:1`; `modules/profile/player.php:1`, `modules/profile/guild.php:1`; `Kernel.php:118`. Runtime source; ranking aliases and privacy need preservation. |
| F17 | Guild directory, guild create/join/leave, invitations/requests/accept/reject/cancel, master settings/kick/rank, chat/moderation and audit. | `includes/classes/class.guilds.php:171`, `:198`, `:217`, `:263`, `:357`, `:363`, `:366`; `modules/usercp/guilds.php:16`, `:57`, `:78`. Conditional native operations plus website extension tables. |
| F18 | Event calendar/home events, timezone and schedule configuration. | `includes/backend/src/Modules/EventsModule.php:12`; `Kernel.php:87`; `modules/events.php:1`; `admincp/modules/events.php:1`. Runtime source. |
| F19 | Castle Siege public cycles/ownership, guild registration/review, rewards/claim history, synchronization and task cache. | `includes/backend/src/CastleSiege/Service.php:35`, `:71`, `:106`, `:123`, `:161`, `:171`, `:196`, `:207`; `modules/usercp/guilds/castlesiege.php:1`; `api/castlesiege.php:1`. Conditional capability; absent natively in Kayito profile. |
| F20 | Support tickets: user-owned creation/replies/status, category/length/open-ticket limits, staff inbox/moderation and staff display masking. | `includes/classes/class.supporttickets.php:54`, `:59`, `:64`, `:86`, `:118`, `:175`, `:214`, `:271`, `:301`, `:318`. Runtime source; database reconnect compatibility exists at `:337`. |
| F21 | AdminCP: player/account search/edit/ban/balances/GM, news/translations, site content/navigation, currencies/VIP, module settings, events/rewards/guild/market, email, DB mappings, tasks/cache and diagnostics. | `admincp/index.php:99`, `:167`; `includes/classes/class.handler.php:224`; full current entry index below. Runtime source, server-side roles/elevation. |
| F22 | Language selection, visitor country fallback, manual locale choice/cookie, locale editor, localized copy, responsive preferences/palettes. | `includes/backend/src/Localization/VisitorLanguage.php:21`, `:43`; `Kernel.php:34`; `templates/default/inc/stylesheets.php:27`, `:48`; `modules/language/switch.php:1`; `admincp/modules/phrases.php:1`. Runtime source. |
| F23 | Configurable server profiles/mappings, metadata discovery, capability diagnostics, password/storage compatibility, controlled setup and licensing. | `includes/backend/src/Game/ServerModes.php:84`, `:121`, `:238`, `:260`, `:416`; `includes/backend/src/Licensing/LicenseVerifier.php:24`; `install/index.php:14`. Runtime/source plus explicitly operational installation paths. |
| F24 | Scheduler/cache producers, rankings/server/online/country/hunt/event/top3/market/bans/lottery jobs, task catalog/backoff/locks and manual management. | `includes/backend/src/Tasks/Scheduler.php:38`, `:53`, `:80`, `:91`, `:108`, `:127`; `includes/webengine.php:182`; full task index below. Runtime source; reads can cause writes. |
| F25 | API module registry/health/session/me/status/characters/events, admin module control, OpenAPI and legacy JSON/image/payment endpoint compatibility. | `Kernel.php:210`, `:219`, `:224`; `includes/backend/src/Modules/SystemModule.php:13`; `.htaccess:20`; endpoint index below. Runtime source. Health explicitly does not check database connectivity. |
| F26 | Trusted plugin/provider extension hooks and administrator plugin pages. | `Kernel.php:147`, `:216`; `includes/classes/class.plugins.php:1`; `admincp/modules/plugins.php:1`, `admincp/modules/plugin_install.php:1`. Source exists; installed plugin list, trust and safe portability unverified. |
| F27 | Older character operations and extension routes (`dies`, `gmission`, `vote`, older sell/buy routes). | Exact entry index below; legacy `modules/usercp/reset.php:14`, `:20`; compatibility `modules/usercp/marketpj.php:2`. Preserve route review coverage; do not treat an old filename as a second authoritative feature or copy historical unsafe code. |

### Routing and HTTP contracts

The rewrite layer converts clean paths to `page`, `subpage`, and paired `request` arguments (`.htaccess:26`; `includes/classes/class.handler.php:81`, `:90`). Handler performs module existence/configuration dispatch (`:184`) and server-side admin authorization (`:224`). Preserve the route/query/redirect contract by explicit adapters rather than copying include/global behavior. `/newrankings/...` is a 301 alias (`.htaccess:7`); `changeclass` and old character market paths are adapters. Nested `guilds/*.php` and `castlesiege/view.php` are internal includes, not independently established routes.

API suffixes below belong under `/api/v1` unless a separate legacy path is named. Registration line anchors establish source presence; access must be copied from the complete route closure and enforced centrally.

| Owner/source | Methods and suffixes |
| --- | --- |
| `Modules/SystemModule.php:13` (under `includes/backend/src/`) | GET `/health`, `/session`, `/me`, `/admin/modules`, `/openapi`; PATCH `/admin/modules/{id}`. |
| `Modules/GameModule.php:11` | GET `/status`, `/me/characters`. |
| `Modules/EventsModule.php:12` | GET `/events`. |
| `Modules/SocialSharingModule.php:14` | GET `/me/social-shares`; POST `/me/social-shares/claim`. |
| `Modules/DiscordLinkModule.php:13` | GET `/me/discord-link`; POST `/me/discord-link/start`. |
| `Modules/CommerceModule.php:31` | GET `/market/categories`, `/market/config`, `/market/browse`, `/market/listings/{id}`. |
| `Modules/CommerceModule.php:112` | GET `/me/market/listings`, `/me/market/history`, `/me/market/warehouse`; POST `/me/market/list`, `/buy`, `/cancel`, `/reclaim` with the same `/me/market` prefix. |
| `Modules/CommerceModule.php:256` | POST `/admin/market/listings/{id}/cancel`; GET `/admin/commerce/queue`, `/admin/commerce/transactions`; POST `/admin/commerce/queue/{id}/retry`, `/admin/commerce/transactions/{id}/refund`. |
| `Modules/CommerceModule.php:321` | GET and POST `/admin/commerce/settings`; POST `/admin/commerce/settings/validate`. |
| `Modules/CommerceModule.php:388` | GET `/user/warehouses`, `/me/warehouses`; POST `/user/warehouses/switch`, `/me/warehouses/switch`. `.htaccess:21` also sends `/api/user/...` to the v1 entry; exact request normalization is an adapter concern. |
| `Modules/CommerceModule.php:406` | GET `/webshop/categories`, `/webshop/items`, `/webshop/packages`; POST `/webshop/preview-tooltip`, `/webshop/validate-custom`, `/webshop/buy`. `.htaccess:21` also routes the `/api/webshop/...` family. |

Separate legacy entries: `api/index.php`, `api/v1/index.php`, `api/online.php`, `api/servertime.php`, `api/castlesiege.php`, `api/guildmark.php`, `api/version.php`, `api/cron.php`, `api/discord/callback.php`; gateway wrappers under `api/payments/` for PayPal, Mercado Pago, Tebex checkout/webhook, Paymentwall checkout/webhook, Xsolla checkout/webhook and test checkout. `api/vipshop/` retains PayPal, Mercado Pago, Tebex checkout, Paymentwall checkout/webhook, Xsolla checkout/webhook and test checkout; its Tebex webhook file is absent. Dispatcher evidence: `includes/backend/src/Payments/Endpoint.php:20`. Dedicated Tebex webhook rewrite: `.htaccess:22`. `robots.php` and `sitemap.php` are lightweight utility routes (`.htaccess:8`). Historical `api/events.php`, `api/invasiones.php`, `api/binance/` are absent from the current API filename enumeration and are not supported-current claims.

## Artifact 2 — Business rules

| Rule | Required preservation or deliberate reviewed change | Evidence |
| --- | --- | --- |
| BR01 Identity and account lifecycle | Match vendor credential codec, verification requirements, recovery expiry and mail behavior; prevent duplicate pending registrations. Never migrate credentials by guessing their format. | `Account/Workflows.php:44`, `:134`, `:185`, `:236`, `:423`, `:441`, `:455`; `Game/PasswordCodec.php:1` under backend src. |
| BR02 Session/CSRF/rate controls | Session-scoped identity; CSRF on writes; login/registration abuse protection; admin role, IP binding, rotated session and short-lived password elevation. New shared limiting must preserve meaningful existing limits and improve cross-session enforcement explicitly. | `modules/login.php:16`, `:22`; `modules/register.php:15`, `:17`; `Security/SessionIdentity.php:20`, `:29`, `:46`; `admincp/index.php:32`, `:47`; `Security/AdminPolicy.php:28`. |
| BR03 Character mutations | Owned character, offline account, locked state, replay rejection, offline attack/store exclusion, correct class/progression/inventory requirements and one transaction owner. | `includes/classes/class.characterpanel.php:128`, `:137`, `:139`, `:140`, `:144`, `:194`, `:237`. |
| BR04 Character market | Preserve listing currency/commission, no guild transfer, validate buyer slots/native indices, lock both accounts in stable order, verify completed ownership. | `Character/Market.php:7`, `:74`, `:80`, `:144`, `:183`, `:190`, `:240`. |
| BR05 Commerce storage | Require usable account and offline status with transaction-owned application lock, escrow reservation, compare-and-swap of exact blobs, vault password/unlocked limits, no overlapping grid placement. SQL locks do not establish independent game-server synchronization. | `includes/commerce-core/Domain/Storage/StorageService.php:36`, `:53`, `:69`, `:87`, `:166`; `includes/commerce-core/Domain/Storage/MultiWarehouseService.php:259`, `:409`, `:613`, `:727`. |
| BR06 Idempotency and refunds | Same key must match actor/module/request hash; preserve debit/delivery ledger and mapping fingerprint. Retry only eligible undelivered entries. Refund only eligible debited transactions with no delivered entry; recipe escrow requires reconciliation, not currency-only refund. | `includes/commerce-core/Domain/Economy/TransactionCoordinator.php:38`, `:63`, `:68`, `:288`, `:307`, `:321`, `:328`, `:331`, `:332`. |
| BR07 Gateway acknowledgment | Preserve each provider's signature/evidence/currency/amount/order matching, duplicate handling, refunds/cancellation and response status/body. Tebex explicitly returns retryable 503 on delivery failure. Do not collapse providers into one generic acknowledgment rule. | `includes/payments/tebex/webhook.php:20`, `:44`; `includes/payments/paymentwall/webhook.php:11`; `includes/payments/xsolla/webhook.php:43`, `:48`, `:51`; `Payments/Endpoint.php:9`, `:20`. |
| BR08 Wallet identity | Configured credit reference is authoritative; earning points, payment currency and reward destination are separate. Preserve actual historical awards and chosen character/account owner. | `Kernel.php:94`, `:99`, `:127`; `includes/classes/class.voteshop.php:57`, `:223`, `:357`; `includes/backend/src/Commerce/CreditCurrencies.php:1`. |
| BR09 Guild and support ownership | Validate selected character belongs to actor, master-only operations, native capability/offline rules, user-only ticket access, masked staff identity, category/length/open-ticket limits. | `includes/classes/class.guilds.php:201`, `:217`, `:223`, `:263`; `includes/classes/class.supporttickets.php:54`, `:86`, `:118`, `:175`, `:214`. |
| BR10 Gifts/lottery/siege | Normalize codes, preserve caps/expiry/replay/one-time awards and eligibility; lottery participant limits/claims; siege cycle/roster/registration/reward ownership and review. | `Account/GiftCodes.php:35`, `:121`; `Lottery/Service.php:55`, `:63`, `:70`, `:103`; `CastleSiege/Service.php:123`, `:149`, `:161`, `:207`. |
| BR11 Reads may mutate | Page shutdown runs due tasks; cache reads may refresh producers; lottery admin catalog can end due draws. New preview/read-only adapters must exclude these effects. | `includes/webengine.php:182`; `Tasks/Scheduler.php:80`, `:91`; `Lottery/Service.php:138`. |

Backend namespace references in this table omit the common `includes/backend/src/` prefix. No bug found in a historical guide is assumed to remain in current runtime source. No unsafe behavior is implicitly approved for preservation.

## Artifact 3 — Data requirements

| Ownership | Data families | Migration treatment |
| --- | --- | --- |
| External game/account authority | Account identity/password format, online state, characters/slots/progression, guilds/members/siege state, cash/Zen/VIP, inventory/warehouse binary and serial allocation. | Read mappings/schema evidence first. Keep native authority; no bulk copy or schema invention. Writes remain unavailable until explicit non-production integration and synchronization evidence. |
| Website configuration | Clean schema-based site/module/server-mode/backend settings; currencies/credit mappings, provider enablement, product/recipe/catalog settings, ranking/event/branding/legal/localization preferences. | Export only an allowlisted sanitized representation. Re-enter secrets through new environment/secret storage. Never copy `includes/config` wholesale. |
| Website transactional state | Saved orders/payment events/fulfillment state; credit and reward history; character action/market ledgers; ticket/reply history; gifts/claims; lottery participants/winners; siege cycles/awards; Discord links/social cooldowns; commerce ledger/queue/escrow/audit/catalog/recipes/WebShop orders. | Preserve IDs, actor ownership, timestamps, historical amounts, references and immutable evidence with idempotent import manifests, reconciliation and rollback. Unknown retention/privacy requirements stay open. |
| Website content | News/translations, legal/public content, navigation, downloadable file metadata, language catalogs, approved image assets. | Dry-run parser and sanitization, source fingerprints, ID mapping, link validation and rights inventory. Never execute imported PHP. |
| Derived/runtime | Caches, temporary files, sessions, rate counters, task locks, logs, previews, generated packages. | Regenerate or explicitly retire. Do not import secrets, live sessions, locks or operational logs into fixtures. |

Source evidence: legacy `includes/classes/class.database.php:16`, `:146`; backend `Database/ConnectionProvider.php:12`; `Config/Repository.php:11`, `:24`, `:54`; `Game/ServerModes.php:170`, `:238`; Commerce Core `includes/commerce-core/Migrations/001_core.sql:3`, `:37`, `:60`, `:72`, `:95`, `:117`, `includes/commerce-core/Migrations/002_item_market.sql:6`, `includes/commerce-core/Migrations/003_webshop.sql:7`, `:29`, `:72`, `:98`, `:123`.

The Commerce Core SQL sources declare website-owned migrations/config/catalog versions/catalog/item locks/currencies/transactions/delivery queue/recipes/recipe components/audit logs, market listings, and WebShop categories/items/packages/package items/orders log. This is a source schema inventory, not a report of installed tables. Other website table names must be derived from their current repositories/installer snapshots during migration implementation.

## Artifact 4 — Compatibility knowledge

| Compatibility surface | Grounded finding | Boundary |
| --- | --- | --- |
| Runtime | Legacy instructions require PHP 7.4, Windows/XAMPP and MSSQL behavior. Legacy `dB` supports PDO driver variants and explicit transaction ownership. | `dev/AGENTS.md:9`; `includes/classes/class.database.php:16`, `:146`. New runtime must explicitly test its declared versions. |
| Registered website game profiles | Kayito 0.97k, IGCN, X-Team/MuEmu/Louis, Custom IGCN-based, SPK, IGCN Season 21, MUDevs Season 21. | `includes/config/compatibility.php:18`. Registration is not proof of a compatible live schema. |
| Kayito | 10-byte items/760-byte inventory, five classic slots; website account-ID addon; no native master progression/Gens/cash shop/siege. JoinServer password mode matters. | `includes/config/compatibility.php:19`; backend `Game/InventoryFormats.php:15`; `Game/ServerModes.php:304`, `:416`. Addon installation is operational and was not run. |
| SPK/S21 | SPK has separate master/cash/VIP mappings and preview decoding gates. MUDevs S21 indexed characters/master/enhancement mappings and skill refund limitations differ from IGCN S21. | `includes/config/compatibility.php:43`, `:56`, `:67`. Do not assume a season label defines a schema. |
| Commerce profiles | Kayito97k, MuEmu, MUDevs, SPK Mobile MSSQL and Custom MSSQL presets. Default write-supported profiles are **only Kayito97k, MuEmu and MUDevs**. SPK Mobile/Custom presence is not write authorization. | `includes/commerce-core/Adapters/Presets.php:8`, `:41`; `includes/commerce-core/Adapters/AdapterFactory.php:7`. |
| Binary formats | Dedicated 10/16/32-byte codecs, profile option limits/layouts, inventory/warehouse dimensions, grid/offset/multivault models and serial provider contracts. | `includes/commerce-core/Domain/Items/Hex10ByteCodec.php:1`, `includes/commerce-core/Domain/Items/Hex16ByteCodec.php:1`, `includes/commerce-core/Domain/Items/Hex32ByteCodec.php:1`; `includes/commerce-core/Adapters/Presets.php:15`; `includes/commerce-core/Resources/codec-layouts.php:1`, `includes/commerce-core/Resources/serial-allocators.php:1`. No generic codec may replace vendor evidence. |
| Capability discovery | Schema metadata/manual mapping drafts, registered adapter PHP only, draft revisions and capability flags. | backend `Game/ServerModes.php:9`, `:89`, `:99`, `:238`, `:260`; `Game/Features.php:10`. New writes must deny unknown capability. |
| Password/identity | Vendor-specific credential modes, case sensitivity and numeric/indexed account identity need independent validation. | backend `Game/PasswordCodec.php:1`, `Game/KayitoAccountIdentity.php:1`; `includes/config/compatibility.php:21`. Do not convert credentials by inference. |

## Artifact 5 — Configuration information

Installed **values were not read for this inventory**. Filenames and source readers establish configuration families. `includes/backend/src/Config/Repository.php:11`, `:24`, `:41`, `:54`, `:55` provide JSON/XML/legacy reads and lock-based updates; `Game/ServerModes.php:194` validates mapping overrides. Settings schemas, dependency validation, secret references, optimistic revision checks, defaults and migration versions belong in the new project.

Current families enumerated under `includes/config/`: `backend`, `castlesiege`, `characterpanel`, `commerce-core`, `discordlink`, `droplist`, `events`, `giftcode`, `guilds`, `halloffame`, `hallofhonor`, `itemdrop`, `lottery`, `navbar`, `setup-access`, `socialshares`, `supporttickets`, `usercp`, `vipshop`, `voteshop`, `webengine`, `writable.paths`; XML email configuration; PHP compatibility/table maps/timezone/season content. `.lock` sidecars are runtime files, not configuration source to import.

Current module XML names: `contact`, `downloads`, `forgotpassword`, `login`, `marketchar`, `newrankings`, `news`, `profiles`, `register`, `usercp.addstats`, `usercp.buyzen`, `usercp.clearpk`, `usercp.clearskilltree`, `usercp.gmission`, `usercp.itemmarket`, `usercp.myaccount`, `usercp.myemail`, `usercp.mypassword`, `usercp.reset`, `usercp.resetstats`, `usercp.sellchar`, `usercp.unstick`, `usercp.vote`, `usercp.warehouse`, `usercp.webshop`, `usercp.workshop`, `webshop`.

No file's presence establishes that a module is enabled. Provider tokens, OAuth/SMTP credentials, database credentials, setup access and administrator assignments are protected operational settings. New defaults must be clean and non-operational.

## Artifact 6 — Integration requirements

| Integration | Requirement and evidence | Verification still required |
| --- | --- | --- |
| MSSQL game/account + website stores | Parameterized statements, validated identifiers and explicit connection/transaction ownership; PDO/sqlsrv driver adapters plus metadata mapping. `includes/classes/class.database.php:16`, `:146`; backend `Database/ConnectionProvider.php:12`; commerce `includes/commerce-core/Adapters/PdoMssqlConnection.php:1`, `includes/commerce-core/Adapters/NativeSqlsrvConnection.php:1`. | Designated synthetic/non-production driver/schema/lock/cursor tests; offline adapters cannot prove live compatibility. |
| Active MU server | Offline checks + concurrency locks around native mutation; compare exact blobs and mappings; retain serial/identity rules. Commerce `includes/commerce-core/Domain/Storage/StorageService.php:36`; backend `Character/Market.php:190`. | Define safe game-server synchronization per profile. Independent server writers may not honor website SQL locks. |
| Payment providers | PayPal, Mercado Pago, Tebex, Paymentwall, Xsolla callback/checkout/status/evidence/error contracts and test-provider isolation. `Payments/Endpoint.php:20`; source branches under `includes/payments/`. | Offline signed fixtures, HTTP contract checks and authorized provider sandboxes; no real charge/refund/fulfillment. |
| Discord | OAuth code exchange and verified user profile over HTTPS; session state/consent/link uniqueness/reward atomicity. Backend `Discord/Client.php:11`, `:26`; `Modules/DiscordLinkModule.php:17`. | Mock transport and callback replay/expiry/account-switch fixtures; deliberate credentials later. |
| Mail | Template variables + welcome/verification/recovery/password/email notifications using configured mail/SMTP. `includes/classes/class.email.php:38`, `:73`, `:119`; backend `Account/Workflows.php:402`. | Recording transport, token/link expiration tests, no actual outgoing mail. |
| Localization/geo | Visitor country lookup can call `ipwho.is`; locale selection supports fallback/manual cookie. backend `Localization/CountryLookup.php:13`, `:32`; `includes/backend/src/Localization/VisitorLanguage.php:21`. | Offline deterministic fallback, consent/privacy decision before external IP lookup activation. |
| Vote/social sites and community embeds | Configured external vote URLs/campaign content plus optional chat/embed launch; legacy presentation supports preferences and remembered account UI. `includes/classes/class.voteshop.php:96`; `templates/default/index.php:117`; `templates/default/inc/stylesheets.php:48`. | URL allowlists, safe links/embeds and reward claim semantics. Do not infer provider verification. |
| Item parsers/uploads/assets | TXT/XML parsers, profile column mapping, text/number validation, image sanitization/media/catalog import. Commerce `includes/commerce-core/Domain/Items/ItemTxtParser.php:8`, `:35`, `:68`; `includes/commerce-core/Domain/Items/ItemXmlParser.php:8`, `:13`; `includes/commerce-core/Security/UploadSanitizer.php:12`, `:42`; `includes/commerce-core/Admin/MediaService.php:1`. | Malformed/oversize/XXE/MIME/path/import regression fixtures with no executable storage. |
| Licensing/setup/distribution | Signed domain-bound edition/license envelope; reusable installer and commercial package ownership. Backend `Licensing/LicenseVerifier.php:14`, `:24`, `:31`, `:35`; `install/index.php:14`. | Define Next ownership/product licensing explicitly. Do not clone installed license/keys or bypass source licensing. |
| GrapesJS | New required integration, no production editor or canonical project lifecycle established by inspected legacy entries. | Phase 7+ only. Canonical JSON, versioned components, safe read-only preview, sanitization and controlled publishing need independent implementation and fixtures. |

## Artifact 7 — Important behavior, edge cases and unknowns

1. Traffic-driven scheduling is a hidden write path; even GET/rendering or an admin lottery catalog may perform maintenance. Discovery therefore did not open the legacy site. Safe previews need a separate side-effect-free read model (`includes/webengine.php:182`; `Lottery/Service.php:138`).
2. Multiple route families and compatibility adapters must not create duplicate transactions. `/market` is authenticated redirect, `/webshop` is anonymous catalog read, while checkout/order tabs require login (`modules/market.php:3`; `modules/usercp/webshop.php:2`).
3. Character markets and item markets are distinct models. Unified character transfer preserves native slots/index identity; item market uses serialized items/escrow and delivery. Never infer their persistence from similarly named legacy files.
4. An unknown online state, missing mapping, ambiguous ownership, unsupported codec/capability, absent ledger schema or unmapped serial allocator must block mutation. Source preset availability does not establish safe writes.
5. Preserve payment retries and provider-specific bodies/statuses. A webhook may arrive before its order/evidence; replay, partial refund, dispute and already-delivered outcomes differ (`includes/payments/xsolla/webhook.php:43`).
6. Credits may target account/character or separate databases. Historical order destination/amount/currency and wallet mapping fingerprints must survive configuration changes; a refund cannot silently use a new mapping (`includes/commerce-core/Domain/Economy/TransactionCoordinator.php:331`).
7. Multiwarehouse availability, native vault locks, disjoint blobs, active-vault selector and contiguous 2D space are independent constraints; free slot count alone is insufficient (`includes/commerce-core/Domain/Storage/MultiWarehouseService.php:128`, `:409`, `:613`, `:727`).
8. Old guide references to backup theme and historical controllers/APIs are not evidence of current source. Themes now present are `default` and `GlobalMu`; older modules `ayuda.php`, `changelogs.php`, `info.itemdrop.php` and historical APIs were not present in the enumerated runtime directories.
9. Default shell still references `inc/modules/sidebar-i.php` and `sidebar-d.php` (`templates/default/index.php:99`). File existence/reachability needs a focused migration characterization; do not assume this branch works.
10. Admin permissions use numeric levels with highest existing level treated as owner; owner is not always literally level 100 (`Security/AdminPolicy.php:17`). Commerce owner/elevation guard is stronger than ordinary moderator access (`includes/commerce-core/Security/Guard.php:11`).
11. Legacy sources mix localization catalogs, inline copy and domain errors. Preserve escape/encoding/fallback behavior and audit sensitive error paths; do not expose database/provider exception detail in the new renderer.
12. Known source/documentation gaps are analyzed in [Data and integrations](DATA_AND_INTEGRATIONS.md): metadata readiness can overstate serial allocator evidence; current online verification does not reject every absent/ambiguous timing/status case; market listing/cancel/reclaim escrow transitions span separate transactions; market replay identity is narrower than the full requested listing/destination; Workshop multi-vault source differs from older main-warehouse-only documentation. These are reconstruction requirements to correct and characterize, not behavior to copy as safe.
13. Unknown: installed module activation, actual database schema/data volume, active profile and custom overrides, production deploy root/version, real provider configuration, external server cache/write model, required browser support, rights for game art/custom assets, plugin trust/dependency state, retention requirements, and migration downtime window. No installation activation is inferred from filenames.

## Artifact 8 — Presentation assets and ownership

| Asset family | Current source references | Planned use |
| --- | --- | --- |
| Themes/layout/copy | `templates/default/index.php:1`; `templates/GlobalMu/index.php:1`; shared `inc/` partials and `css/components/routes.json`. | Reference information hierarchy and current flows. Rebuild templates against prepared view data; never execute imported PHP in a visual component. |
| CSS/JS | Default route-specific selection `templates/default/inc/stylesheets.php:6`, `:10`, `:36`; gaming/auth/profile/ranking/calendar/commerce/guild/character/Workshop scripts and styles. | Curate design tokens, useful assets and behavior fixtures; avoid wholesale legacy JS/global coupling. |
| Images | Each theme `img/`; default subfamilies `account`, `character-avatars`, `events`, `flags`, `icons`, `items`, `profiles`; favicons. | Record path/hash/type/size/rights/alt text before copying. No artwork copied at this gate. |
| Fonts/vendor UI | Theme fonts, Font Awesome, self-hosted Google font styles, jQuery 3.7.1 and Bootstrap 3.4.1 (`templates/default/index.php:126`; `templates/default/inc/stylesheets.php:6`); Admin vendor manifest `admincp/assets.json:1`. | Confirm licenses per distribution/version; bundle only needed files. Presence in a package does not establish redistribution rights for all game artwork. |
| Mail/languages | `includes/emails/` template family; `includes/languages/` locale families `cn`, `en`, `es`, `hi`, `ja`, `lt`, `ph`, `pt`, `ro`, `ru`, `vi`; runtime catalogs/inline copy. | Sanitize content and preserve variable/link contracts; completeness and translation accuracy need per-flow fixtures. |
| License notices | `LICENSE-WebEngine.txt:1` records MIT license for covered WebEngine software, with notice-preservation condition. Custom commercial/game/vendor material is separately scoped. | Preserve applicable notices if code/assets are reused; maintain a third-party manifest. Do not assume MIT covers every nested/reference asset. |

`dev/WebShop Reference/`, `dev/private/`, developer databases/dumps, logs, archives, downloaded vendor source, generated packages and license-owner-panel private material are **not approved asset pools**. They are reference-only or protected operational/development material. No legacy source/runtime asset was copied to Next.

## Current runtime entry index

The following exact filenames were enumerated on the discovery date. Every entry begins at line 1; file presence alone does not prove activation. Nested includes are marked separately. All paths here are relative to their named directory.

### Public/account modules (including nested includes) — 60 files

- `modules/castlesiege.php:1`
- `modules/castlesiege/view.php:1`
- `modules/contact.php:1`
- `modules/downloads.php:1`
- `modules/events.php:1`
- `modules/forgotpassword.php:1`
- `modules/info.php:1`
- `modules/language/switch.php:1`
- `modules/login.php:1`
- `modules/logout.php:1`
- `modules/market.php:1`
- `modules/myaccount.php:1`
- `modules/newrankings.php:1`
- `modules/news.php:1`
- `modules/privacy.php:1`
- `modules/profile/guild.php:1`
- `modules/profile/player.php:1`
- `modules/rankings.php:1`
- `modules/refunds.php:1`
- `modules/register.php:1`
- `modules/rules.php:1`
- `modules/tos.php:1`
- `modules/usercp.php:1`
- `modules/usercp/addstats.php:1`
- `modules/usercp/buychar.php:1`
- `modules/usercp/buyzen.php:1`
- `modules/usercp/changeclass.php:1`
- `modules/usercp/character.php:1`
- `modules/usercp/characters.php:1`
- `modules/usercp/clearpk.php:1`
- `modules/usercp/clearskilltree.php:1`
- `modules/usercp/dies.php:1`
- `modules/usercp/giftcode.php:1`
- `modules/usercp/gmission.php:1`
- `modules/usercp/guilds.php:1`
- `modules/usercp/guilds/castlesiege.php:1`
- `modules/usercp/guilds/chat.php:1`
- `modules/usercp/guilds/home.php:1`
- `modules/usercp/guilds/manage.php:1`
- `modules/usercp/itemmarket.php:1`
- `modules/usercp/lottery.php:1`
- `modules/usercp/marketpj.php:1`
- `modules/usercp/myaccount.php:1`
- `modules/usercp/myemail.php:1`
- `modules/usercp/mypassword.php:1`
- `modules/usercp/renamecharacter.php:1`
- `modules/usercp/reset.php:1`
- `modules/usercp/resetstats.php:1`
- `modules/usercp/sellchar.php:1`
- `modules/usercp/supporttickets.php:1`
- `modules/usercp/unstick.php:1`
- `modules/usercp/venderpj.php:1`
- `modules/usercp/vipshop.php:1`
- `modules/usercp/vote.php:1`
- `modules/usercp/voteshop.php:1`
- `modules/usercp/warehouse.php:1`
- `modules/usercp/webshop.php:1`
- `modules/usercp/workshop.php:1`
- `modules/verifyemail.php:1`
- `modules/webshop.php:1`

### AdminCP modules (including mconfig/workspace includes) — 80 files

- `admincp/modules/accountinfo.php:1`
- `admincp/modules/accountservices.php:1`
- `admincp/modules/accountsfromip.php:1`
- `admincp/modules/addnews.php:1`
- `admincp/modules/addnewstranslation.php:1`
- `admincp/modules/admincp_access.php:1`
- `admincp/modules/backend.php:1`
- `admincp/modules/banaccount.php:1`
- `admincp/modules/blockedips.php:1`
- `admincp/modules/cachemanager.php:1`
- `admincp/modules/character.php:1`
- `admincp/modules/commerce_core.php:1`
- `admincp/modules/commerce_core/workspace.php:1`
- `admincp/modules/connection_settings.php:1`
- `admincp/modules/creditsconfigs.php:1`
- `admincp/modules/creditsmanager.php:1`
- `admincp/modules/cronmanager.php:1`
- `admincp/modules/discordlink.php:1`
- `admincp/modules/editcharacter.php:1`
- `admincp/modules/editnews.php:1`
- `admincp/modules/editnewstranslation.php:1`
- `admincp/modules/emaildelivery.php:1`
- `admincp/modules/events.php:1`
- `admincp/modules/giftcodes.php:1`
- `admincp/modules/gmmanager.php:1`
- `admincp/modules/gmsettings.php:1`
- `admincp/modules/grandreset.php:1`
- `admincp/modules/guilds.php:1`
- `admincp/modules/home.php:1`
- `admincp/modules/lottery.php:1`
- `admincp/modules/managenews.php:1`
- `admincp/modules/marketchar.php:1`
- `admincp/modules/mconfig/addstats.php:1`
- `admincp/modules/mconfig/buyzen.php:1`
- `admincp/modules/mconfig/castlesiege.php:1`
- `admincp/modules/mconfig/clearpk.php:1`
- `admincp/modules/mconfig/clearskilltree.php:1`
- `admincp/modules/mconfig/contact.php:1`
- `admincp/modules/mconfig/downloads.php:1`
- `admincp/modules/mconfig/email.php:1`
- `admincp/modules/mconfig/forgotpassword.php:1`
- `admincp/modules/mconfig/halloffame.php:1`
- `admincp/modules/mconfig/hallofhonor.php:1`
- `admincp/modules/mconfig/login.php:1`
- `admincp/modules/mconfig/myaccount.php:1`
- `admincp/modules/mconfig/myemail.php:1`
- `admincp/modules/mconfig/mypassword.php:1`
- `admincp/modules/mconfig/newrankings.php:1`
- `admincp/modules/mconfig/news.php:1`
- `admincp/modules/mconfig/profiles.php:1`
- `admincp/modules/mconfig/rankings.php:1`
- `admincp/modules/mconfig/register.php:1`
- `admincp/modules/mconfig/reset.php:1`
- `admincp/modules/mconfig/resetstats.php:1`
- `admincp/modules/mconfig/unstick.php:1`
- `admincp/modules/mconfig/vote.php:1`
- `admincp/modules/modules_manager.php:1`
- `admincp/modules/navbar.php:1`
- `admincp/modules/newrankings_manager.php:1`
- `admincp/modules/newregistrations.php:1`
- `admincp/modules/newssettings.php:1`
- `admincp/modules/onlineaccounts.php:1`
- `admincp/modules/phrases.php:1`
- `admincp/modules/players.php:1`
- `admincp/modules/plugin_install.php:1`
- `admincp/modules/plugins.php:1`
- `admincp/modules/publiccontent.php:1`
- `admincp/modules/rankings_manager.php:1`
- `admincp/modules/searchaccount.php:1`
- `admincp/modules/searchban.php:1`
- `admincp/modules/searchcharacter.php:1`
- `admincp/modules/socialshares.php:1`
- `admincp/modules/supporttickets.php:1`
- `admincp/modules/topvotes.php:1`
- `admincp/modules/usercp.php:1`
- `admincp/modules/vipconfigs.php:1`
- `admincp/modules/vipshop.php:1`
- `admincp/modules/voteshop.php:1`
- `admincp/modules/webshop.php:1`
- `admincp/modules/website_settings.php:1`

### API wrappers — 27 files

- `api/bootstrap.php:1`
- `api/castlesiege.php:1`
- `api/cron.php:1`
- `api/discord/callback.php:1`
- `api/guildmark.php:1`
- `api/index.php` (empty file; no executable route established)
- `api/online.php:1`
- `api/payments/mercadopago.php:1`
- `api/payments/paymentwall.php:1`
- `api/payments/paymentwall-webhook.php:1`
- `api/payments/paypal.php:1`
- `api/payments/tebex.php:1`
- `api/payments/tebex-webhook.php:1`
- `api/payments/test.php:1`
- `api/payments/xsolla.php:1`
- `api/payments/xsolla-webhook.php:1`
- `api/servertime.php:1`
- `api/v1/index.php:1`
- `api/version.php:1`
- `api/vipshop/mercadopago.php:1`
- `api/vipshop/paymentwall.php:1`
- `api/vipshop/paymentwall-webhook.php:1`
- `api/vipshop/paypal.php:1`
- `api/vipshop/tebex.php:1`
- `api/vipshop/test.php:1`
- `api/vipshop/xsolla.php:1`
- `api/vipshop/xsolla-webhook.php:1`

### Scheduled task include files — 16 files

- `includes/cron/account_country.php:1`
- `includes/cron/castle_siege.php:1`
- `includes/cron/character_country.php:1`
- `includes/cron/cron.php:1`
- `includes/cron/hunt_ranking.php:1`
- `includes/cron/hunt_ranking_wek.php:1`
- `includes/cron/lottery.php:1`
- `includes/cron/marketlist.php:1`
- `includes/cron/online_characters.php:1`
- `includes/cron/rankings.php:1`
- `includes/cron/rankings_golden.php:1`
- `includes/cron/server_info.php:1`
- `includes/cron/temporal_bans.php:1`
- `includes/cron/top3bc.php:1`
- `includes/cron/top3cc.php:1`
- `includes/cron/top3ds.php:1`


## Reference-only development material

`dev/README.md:1`, `:13`, `:17` separates reviewed offline fixtures from installation-aware runners, SQL tools and HTTP. `dev/tests/`, `dev/fixtures/` and focused docs can supply behavior examples after inspecting their includes/transports/cleanup. `dev/run.php` default or `--unit-only` must not be mistaken for safe offline verification. No legacy runner was executed.

`dev/sql/`, `dev/db/`, `dev/tools/`, test `Integration/` workers and installation/maintenance scripts are operational/reference source, not proof that schemas are installed or integrations pass. `dev/WebShop Reference/` contains unrelated commercial/third-party examples and is not part of current runtime feature claims. `dev/artifacts/`, `dev/logs/`, `dev/private/` are not input data for migration or public fixtures. Historic roadmap/closure documents describe prior engagement decisions; the user's new project authorizes reconstruction only in Next, with the mandatory review gate before Phase 4.

## Phase-gate acceptance and continuation

This inventory establishes static discovery coverage and migration questions. It does not claim that legacy functionality was run, the new functionality exists, the database is compatible, assets are cleared for redistribution, or the project is deployment-ready. Review it alongside `MIGRATION_MATRIX.md`, the new architecture/contracts/workspace and `STATE.md`. Phase 4+ implementation must wait for the user's review response.
