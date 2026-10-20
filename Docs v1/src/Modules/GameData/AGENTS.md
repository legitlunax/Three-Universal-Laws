# GameData boundary

Read root STATE.md and AGENTS.md first. GameData provides read-only ranking
projections and offline schema diagnostics. Game mutations, serial allocation,
item codecs, and payment fulfillment remain strictly disabled offline pending
live non-production synchronization verification.

Domain rules:
- `RankingEntry` models ordered leaderboard positions without private player fields.
- `RankingReader` serves public allowlisted projections (e.g., characters, guilds)
  with stable tie ordering and bounded limits (1..100).
- `SchemaInspector` provides metadata-only inspection reports; no data rows, player
  records, or connection credentials may ever be emitted.
- `SchemaInspectionService` requires explicit `database.inspect` authorization
  evaluated server-side via the `Authorization` contract port.
- `CharacterProfile` encapsulates native MU Online character properties (level 1..400,
  resets >= 0, name 1..10 chars, class code translations across all standard classes) without live writes.
- `GameAccount` encapsulates MU Online account properties (accountId 1..10 chars, blocked/online state,
  server name) with strict credential omission and status labeling.
- `CharacterReader` and `OfflineCharacterReader` provide query ports for offline character lookups
  by name and account identifier using deterministic synthetic fixtures.
- `CharacterService` provides public character profile queries and server-authorized account character
  inspections requiring `database.inspect` permission.
- No direct database writes or simulated mutation gateways may be created without
  authoritative offline verification, concurrency locks, and immutable audit logs.

Website-owned timetable configuration uses the internal EventScheduleStore with
server-authorized events.manage, global revisions and atomic replay/audit. Store
canonical definitions, never computed countdowns. Keep corruption and capacity
failures closed. UTC calendar export is read-only, bounded and explicit about its
snapshot scope; no calendar data may execute events or gain native authority.
See docs/EVENT_TIMETABLE.md and preserve unit/process/HTTP coverage.

Database settings mode binds GameData's PdoEventScheduleStore for timetable,
calendar and staff editing; no missing-row/schema fallback to file or synthetic
defaults is permitted. EventTimetableState preserves the original v1 document/
history/retry semantics for both adapters. DDL/import are explicit and never run
from requests/constructors. Only Bootstrap may wire the shared private connection
factory into this adapter; never import Admin internals inside GameData. SQL Server
locking/grants and PDO_ODBC now have owned guide evidence under decision 103.
GameData owns its eight-MiB MssqlGuideOdbc wire/profile; never import Admin internals.
Preserve checked execute/row count/exact readback/commit and replay rollback,
redacted cleanup failures and no constructor/request installation. Strict TLS,
guide-specific SQL HTTP/calendar and deployment remain unverified. See
docs/GUIDE_ODBC.md; owned SQLite HTTP evidence remains separate.

LootGuideStore likewise has a SQL binding for public/staff guide routes in database
mode, never file/empty fallback. LootGuideState retains v1 operation-scoped retries
and generated IDs. Both SQL adapters reuse the same module-owned PdoGuideDocument
with fixed identities, separate tables, owning transactions and CAS. WebsiteGuideImport
reads only declared existing new-project files/locks and retains histories. No
native loot/scheduler write can be introduced through these configuration adapters.

Marketplace execution is unavailable while GameMutationGateway is unbound. Never
activate it from a MarketEscrowStore row, injected WarehouseWriter/CurrencyWriter/
CharacterWriter, sorted account-name array or optional MEMB_STAT read. Required
ownership, fresh authoritative presence under actual cross-worker locks, rechecks,
idempotency and consistent escrow/ledger/delivery/audit must be enforced by the
protected mutation provider. Low-level escrow-store transitions are website-only
metadata fixtures, not proof of extraction/payment/delivery. Preserve negative
market-safety tests and do not relabel metadata as a completed M6 lifecycle.

