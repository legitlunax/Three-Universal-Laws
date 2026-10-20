# Milestone 6: market preparation and escrow metadata

The sandbox supports persistent item and character listing drafts, asking prices
in Zen/Credits/WCoinC, owner cancellation, administrator moderation, read-only
transfer requirements, and website-only escrow metadata persistence. Native
extraction, reclaim, settlement and delivery remain unavailable while the protected
mutation gateway is unbound. An escrow row or injected writer does not establish
asset custody or permit a transfer. The former direct-writer implementation was
removed: it changed warehouses/balances before an independent metadata commit,
skipped offline checks for missing/unknown accounts and could report settlement
without payment/delivery adapters. Sorting account names acquired no actual lock.
Character custody/transfer was also absent. Those tests did not establish a
complete or atomic M6 lifecycle.

## Boundaries and compatibility

MU-specific preparation lives in GameData beside the existing webshop/VIP slice.
`MarketplaceService` uses frozen V1 `Authorization` and new module-local
`MarketAssetReader` / `MarketDraftStore` ports. No public V1 signature, component
property or frozen baseline changed. Bootstrap composes implementations; SiteView
receives projections. The production Commerce module and game mutation gateway
remain planned, without fake bindings. A future shared market API needs contract
evolution and consumer review.

`scripts/impact.php` conservatively selected all seven modules for Bootstrap and
metadata changes. The declared GameData tests now include `tests/marketplace.php`;
complete verification also exercises migration and template compatibility.

Bootstrap explicitly maps the website administrator to a synthetic `sandbox`
account with a demo vault and `Trader` character. This is unrelated to their
username or native MU ownership. Unmapped actors receive no assets. Existing
warehouse/character readers select assets server-side. Clients cannot supply
seller identity, item bytes, fingerprints or fee policy. Locked warehouses,
empty slots and online/guild-affiliated characters are excluded from demo
selectors. This does not prove authoritative offline status on a live server.

## Draft rules

- Prices are exact integers from 1 to 2,000,000,000. Currency IDs are `zen`,
  `credits`, `wcoinc`. Seller commission is 500 basis points (5%), rounded up;
  asking price, commission and proceeds are frozen together.
- Creation/owner cancellation require `market.draft`; selectors/history require
  `account.read`. Owner matching is checked under lock. Manager listing, journal
  access and moderation require `market.manage` at the application boundary.
- HTTP mutations require the session, same Origin, CSRF and a dedicated persistent
  limiter (20 requests per client per 15 minutes).
- A stable file lock and atomic snapshot replacement commit website drafts,
  scoped replay results and minimal audit events together. `draft` revision 1
  can only become `cancelled` revision 2. Stale revisions, duplicate active assets
  and changed-command replays conflict; exact replays return the original result.
- Private state is bounded to 500 historical listings, 1,000 events and 8 MiB.
  Corruption fails closed without resetting state. Guarded one-time SQL migration
  is prepared below; no purge exists.
  The journal is append-only through this API; independent tamper protection and
  crash/power-loss durability are not certified. Windows privacy needs host ACLs.

Public models omit seller IDs, account mappings, native bytes and fingerprints.
The manager displays the atomic market draft journal, not a separately committed
copy in the global audit file. Ordinary drafts may be cancelled; records marked
escrowed require reconciliation and cannot be cancelled as if assets were returned.

## Routes

| Route | Behavior |
| --- | --- |
| `GET /market`, `/market/characters` | Public synthetic previews; authenticated draft selectors/history. |
| `POST /market/draft` | Authorized, idempotent sandbox draft creation. |
| `POST /market/cancel` | Owned draft cancellation with expected revision. |
| `GET /market/prepare?id=…` | Authenticated requirements; always `ready=false`, no reservation. |
| `POST /market/purchase` | Authenticated/CSRF/rate-limited rejection; no transfer provider. |
| `GET /admin/market` | Authorized draft history and audit journal. |
| `POST /admin/market/cancel` | Authorized, idempotent moderation with expected revision. |

## Evidence and remaining work

Decision 090 adds MarketState shared by the file writer and source/direct import.
It validates complete v1 heads/replays/events, transitions, immutable prices/asset
identity, per-listing UTC clocks, historical active-asset uniqueness and retained
escrows. Escrow owner/status/buyer/times must match deposit and terminal events.
Unknown envelope/escrow metadata, orphan/missing histories and non-JSON typed
values refuse without repair. Opaque nested payload data survives unchanged and
is not a decoded/validated native item, character or custody record.

v1 did not retain the original asset input/order, so creation digests remain
opaque rather than being guessed. Other operation digests are reconstructed.
The inherited deposit digest omits payload; an exact historical retry cannot
replace retained payload, but this is not full payload-bound command identity.
Do not use these metadata ports as the protected native mutation protocol.

MarketStorageImport requires existing regular market-drafts.json and its stable
writer lock, canonical/unlinked paths, single-link/open-file identity checks and
a bounded shared-lock read. Missing source never means an empty migration.
Discovery creates/defaults/repairs nothing. Freeze edits after the reviewed dry
run through cutover. Direct imports validate too and own one listings-first
transaction checking all four targets empty. Every insert and commit is required;
failed/zero-row/false boundaries roll back all tables. Ambiguous retry cannot
overwrite a committed collection. Actors, moderation/settlement operations,
prior states and clocks come from validated events, including epoch zero, instead
of being inferred from final revisions. Latest heads retain actual update times.
Escrow kind/assetKey come from the listing, never payload guesses; terminal retry
outcomes retain the SQL adapter's structured listing/payload shape.

DDL 006 stays unchanged. Its NVARCHAR(4000) command outcome limit is checked in
UTF-16 units before connecting/applying, including structured terminal payloads;
overwide sources refuse without truncation and need a separate schema migration.
Valid four-field pre-escrow sources and current five-field sources retain format
and fingerprints. Re-run source review after stricter validation; do not discard
history, invent metadata or reset hashes to bypass refusal.

Runtime SQL metadata methods now share PdoMarketWriteBoundary with import:
TABLOCKX/HOLDLOCK on listings for SQL Server or SQLite's writer boundary before
SELECT. All write stages require one affected row and checked commit; false replay
commits refuse, failed stages roll back and storage errors omit private chains.
Only sqlsrv/owned SQLite exception-mode connections are supported. No constructor
DDL, DELETE, file fallback, new V1 signature or baseline refresh was introduced.
Decision 091 adds PdoMarketSnapshot: every SQL view owns one coherent read
transaction with a listings-first shared boundary; every command validates the
complete four-table snapshot before replay and validates the resulting snapshot
before commit. Bounded typed rows, canonical integer driver strings, JSON/UTF-16
widths, command/event metadata, historical digests/transitions/actors, current
heads/update clocks and escrow identity/status/buyer/payload must agree. Corrupt
or missing history makes all views/retries unavailable privately, with no repair.
Caller transactions refuse; one damaged listing makes this bounded store unavailable.
All commands respect 500 heads/1000 events/8 MiB canonical state and monotonic
per-listing clocks. Exact historical retries work at capacity without reversing
later transitions. Active escrowed assets cannot gain another draft. Non-JSON
new payloads and overwide terminal outcomes roll back atomically. New payloads
may fit escrow storage yet exceed the existing terminal command width; such a
terminal transition refuses and needs a separately reviewed schema migration.
Valid v1 row key order and opaque creation digests remain intact; deposit retry
identity remains payload-independent. Stronger request identity needs an explicit
version/migration. These metadata checks confer no native transfer authority.

tests/market-import.php adds 177 file/SQLite/process/CLI assertions and the shared
settings HTTP suite originally added 11 SQL market cases. They cover
full history/metadata/payload retention, historical retries, strict corrupt source
denial, all-target emptiness, owning/fault/zero-row/false/ambiguous transactions,
stable source locks, importer/runtime barriers, guarded CLI, real route rollback,
CSRF/Origin, no file fallback and continued native-purchase denial. Existing 122
marketplace and 212 safety assertions remain. tests/market-runtime.php adds 575
SQLite/process assertions for corruption across every view/replay/command, owning
reads, typed numbers, capacity/clocks, ambiguous commits, v1 compatibility,
simultaneous retry/asset/terminal/capacity races and a concurrent commit between
table reads. Five additional HTTP cases reject corrupt browse/exact retry without
fallback/success audit and preserve restored history (193 shared assertions).
Actual SQL Server driver/types/
collations/UTF-16/locks/grants/deadlocks and new browser QA remain unverified.
Physical premium packaging/catalog migration remains pending; no core/free
consumer or frozen contract gained a dependency on premium internals.

Continuation adds pure `MarketTransferPlan` and `MarketRecoveryPolicy` foundations.
They model canonical two-account lock order, fresh offline/grace checks, stale
asset fingerprints, native item/character requirements, rectangular vault
placement and ambiguous-settlement recovery. A draft remains blocked as
unescrowed in planning. The pure planner can describe a candidate from supplied
evidence, but acquires no locks and authorizes no mutation. MarketplaceService
preparation always reports ready=false/nativeFulfilled=false; persisted escrow
state is labelled unverified-record. escrowDraft, reclaimEscrow, purchase and
escrow cancellation require server authorization and then refuse execution before
reading/writing native dependencies or changing website custody/settlement state.
Legacy optional constructor inputs remain accepted for caller compatibility, but
cannot activate execution. No fake gateway was introduced.

Low-level FileMarketDraftStore/PdoMarketDraftStore deposit/reclaim/settle methods
retain their metadata transitions for isolated persistence fixtures and future
migration. A held/delivered/sold label there is not evidence that a game asset was
extracted or delivered. Their transactions cover website rows only; no native
warehouse/currency/character effect is coordinated by them. Do not wire these
methods directly into controllers or a live execution service.

The older webshop/VIP services now refuse non-sandbox economy writers and explicit
native mode before invoking a writer. The existing offline writer carries the
internal SandboxOnlyEconomy marker; native PDO writers do not. This safety guard
does not bind or implement a production mutation gateway, durable commerce
settlement or native delivery. Existing webshop/VIP behavior remains an offline
simulation and must not be described as real account fulfillment.

Preview outcomes now explicitly report `sandboxPreview=true` and
`nativeFulfilled=false`; item orders use `prepared` status. UI notices and
`webshop.sandbox_preview` / `vipshop.sandbox_preview` audit operation names make
that scope clear. Existing route paths/notice query values are retained for
compatibility, and HTTP preview submissions have persistent throttling. The
Bootstrap mapping is the explicit synthetic `sandbox` account, never an inferred
native identity. This corrects the earlier optimistic delivery descriptions;
assertions now require both the preview result and absence of native claims.

`tests/marketplace.php` covers fees, authorization/ownership, projections, frozen
values, persistence, replay/conflicts, cancellation, corruption and native-write
denial. tests/market-safety.php adds a 212-assertion matrix across item/character,
file/SQLite and unbound/complete-offline/unmarked-native/partial writer inputs.
It verifies deposit/purchase/reclaim/moderation denial, service authorization,
unchanged listing/escrow/replay/audit state, unchanged warehouses/balances and zero
native writer invocations. Storage-only transition tests remain explicitly labelled
metadata evidence. Actual concurrent processes exercise distinct creates, duplicate assets,
exact replays and cancellation races. Character fixtures include owned, foreign,
online and guilded cases. View/HTTP suites cover escaping, mounted forms, method
policy, CSRF/Origin, persisted state and throttling.

Live transfers need trusted identity/presence, ordered locks for both accounts,
rechecks, atomic escrow/ledger/audit, listing CAS, scoped purchase replay,
codec/serial evidence, vault grid placement, native character index/slot checks,
guild restrictions and recovery. No SQL migration, production database access or
legacy execution occurred. [STATE.md](../STATE.md) records actual verification.
