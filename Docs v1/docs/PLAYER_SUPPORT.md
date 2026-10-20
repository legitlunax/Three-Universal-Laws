# Website player sessions and support

Registered website accounts can sign in at `/login`, use `/account`, and create
private support tickets at `/support`. These sessions establish website ownership
only. They do not authenticate native MU accounts or enable game mutations.

## Identity boundary

`PlayerIdentityService` uses the existing private `PlayerAccountStore`, codecs and
persistent attempt limiter. Unknown/inactive users still perform a dummy password
verification. Successful sign-in rotates the session ID and CSRF token. The
session stores a private credential revision; every authorization re-reads the
current account. Bans and password changes revoke existing sessions. Account roles,
submitted actor IDs and session permission claims never grant website staff rights.

`NativeSession` gained an optional internal realm argument; its default sandbox
realm and existing consumers remain compatible. Player cookies use `ZendaPlayer…`
and `player-sessions/`; staff cookies retain `ZendaNext…` and `sessions/`. Both use
strict cookie mode, HttpOnly/SameSite Strict, session rotation, a 20-minute idle
timeout and an eight-hour absolute lifetime. Logging out one realm leaves the
other intact. Logout remains available during maintenance.

The new `PlayerAuthorization` implements existing frozen V1 Authorization.
Players receive only `account.read` and `support.use` scoped to their own actor.
Website staff receive `support.manage` from the server-owned sandbox credential
boundary, not the player account role field. Bootstrap composes both surfaces.
No V1 signature, component property or frozen baseline changed.

Bootstrap routes website-player requests before opening a staff session, so a
single HTTP request never starts both PHP session realms. Existing registration
creates the website accounts used here and now describes that scope explicitly.

## Atomic password recovery

The internal PlayerAccountStore port now includes `consumeResetAndUpdatePassword`;
the registration service and both file/PDO adapters use it. Confirmation rejects
malformed tokens, exact-expiry timestamps and inactive accounts. Two workers
cannot consume the same token. Public V1 contracts remain unchanged.

The file adapter stores a private consumed-token digest with the password in one
atomic account snapshot under the account lock. `findResetToken` overlays used
state on the older token file; public projections omit the digest. The version-1
account format accepts this optional field without replacing existing records.
**Writer rollback to code that ignores this marker is unsafe** until outstanding
tokens are revoked/reconciled. No production rollout or rollback migration was
performed. The PDO adapter consumes the token and replaces the password in its
own transaction, rolling both back on failure and refusing caller-owned/nested
transactions. SQLite tests do not certify other production drivers.

SQL issuance now replaces tokens atomically without DELETE, preserving consumed
state and rejecting changed expiry for the same token identity. Issuance, recovery
and self-service share the account write boundary and recheck under lock. Invalid
stored flags/hashes/expiry refuse without repair. Application requests return null
for inactive accounts after consuming their limiter budget. See
[website recovery storage](WEBSITE_RECOVERY.md) for compatibility and evidence limits.

The file-only recovery token preview is an offline demonstration. Database-mode
GET/POST recovery routes now refuse with private 503 responses until secure
delivery/outbox exists; they never generate, reveal, consume or reflect tokens.
Do not enable that demo for database-backed website accounts. Storage and process
tests are not proof of a working public delivery/recovery integration.

Malformed account/token JSON fails closed and never appears as an empty database
to registration or issuance. Private file guards reject linked state files.
Historical synthetic short-token tests now use the actual 64-hex token protocol;
their positive persistence assertions remain in place.

Authenticated website password changes use a separate internal extension to
commit credentials, current reset-token invalidation and minimal replay/history
together, then revoke player sessions. Existing recovery consumers retain their
signatures. See [WEBSITE_PASSWORDS.md](WEBSITE_PASSWORDS.md) and the proposed
[rollback boundary](WEBSITE_ROLLBACK.md); operational tooling/rehearsal remain
unimplemented.

## Support ownership and history

`SupportService` owns permission and projection rules; `SupportStore` is a
module-local port implemented by `FileSupportStore`. Players can create tickets,
reply, close and reopen their own conversations. Staff can list/read all tickets,
reply, close and reopen. Foreign tickets return the same not-found response as
missing IDs. Staff identities are projected as **Support team** to players.

- Categories: account, gameplay, website, other.
- Subject: 1–120 Unicode characters; message: 1–4,000, with UTF-8/control validation.
- Three active tickets per owner; up to 100 messages per ticket.
- Statuses: open, awaiting-staff, awaiting-player, closed. Replying to a closed
  ticket requires an explicit reopen first. Reopening also checks the active cap.
- Each change requires the current revision and scoped idempotency key. Exact
  replay returns the original result; altered requests and stale revisions conflict.
- Stable cross-worker locks and atomic replacement commit state, replay results
  and the minimal audit journal together. Audit projections contain no subjects
  or message bodies. The private state necessarily retains ticket conversations.
- Storage is bounded to 500 historical tickets, 1,000 changes and 8 MiB in the offline
  prototype. In database mode, authoritative persistence is stored in website-owned SQL tables.
- Database persistence uses `PdoSupportStore` with `database/mssql/007-support-storage.sql`
  creating `dbo.zenda_support_tickets`, `dbo.zenda_support_commands`, and `dbo.zenda_support_events`.
  Its internal PdoSupportDocument owns read/write transactions and locks tickets first:
  TABLOCKX/HOLDLOCK for SQL Server writes, TABLOCK/HOLDLOCK for reads, or SQLite's
  writer boundary before SELECT. Cooperating runtime/import writers share this global
  boundary, serializing ticket/command capacity and three-active-ticket create/reopen
  limits. Every ticket/command/event write and commit is checked; failed/zero-row/
  false boundaries roll back. Exact replay resolves committed ambiguity without
  creating another random identity, duplicating messages or undoing later status.
- Migration from existing offline file state is supported via `SupportStorageImport` and
  `scripts/import-support-storage.php`, which validates source fingerprints and imports
  tickets, commands, and events into three empty target tables in one owning transaction.
- Testing in `tests/support.php` exercises `PdoSupportStore` on SQLite in-memory fixtures
  via `SupportStorageSchema`, covering create, replay, conflicts, staff/player replies,
  close, reopen, open ticket caps, and file-to-SQL import validation.

SupportState now shares command/transitions/full v1 validation across file, SQL and
import. It reconstructs every immutable result, request digest, actor/message role,
revision, clock and status transition, including active caps at each create/reopen,
and requires exact current-head/history agreement. SQL also verifies typed rows and
ordered event projections. Canonical integer driver strings remain compatible;
fractional/coerced values, corrupt/orphan/duplicate histories and missing schema
refuse without repair or file fallback, including on replay. JSON/row collections
are bounded. One corrupt ticket makes this bounded store unavailable; consistency
checks are not protection against privileged rewriting of the entire history.
Caller transactions are refused without committing/rolling back the caller.
Inbox, detail and event calls retain separate snapshots under the existing port.

Source discovery requires existing regular support.json and support.lock, canonical
unlinked directories, single-link/open-file identity checks and a bounded read under
the existing stable writer lock. Missing source never means an empty migration;
discovery creates/repairs nothing. Freeze source edits after reviewing the dry run
throughout cutover. Direct import validates too and refuses any occupied target
under the same runtime write boundary. All heads/messages, historical results and
minimal ordered events commit once or roll back together. A committed but
unacknowledged import can be reloaded; retry cannot overwrite the collection.

Decision 089 preserves internal/V1 signatures, v1 shapes, valid fingerprints and
DDL 007. Previously accepted malformed history intentionally refuses and needs
explicit reconciliation, never history deletion or a refreshed baseline. DDL 007's
subject NVARCHAR(120) fits 120 UTF-16 units; the file prototype allows 120 Unicode
code points. SQL writes/import preflight explicitly refuse a subject exceeding
that existing SQL bound rather than truncating it. Supplementary characters count
as two units. Widening that DDL for every 120-code-point subject needs a separate
reviewed schema migration; no source text is rewritten here. JSON storage uses
unescaped Unicode while semantic command fingerprints remain unchanged.

tests/support-storage.php adds 187 file/SQLite/process/CLI assertions and
tests/support-storage-http.php adds 25 loopback assertions, alongside the existing
84 support assertions. Coverage includes strict sources/direct imports, complete
history/replay retention, corruption, linked/missing/oversized state, owning
transactions, every failed/zero-row stage, false/ambiguous commits, exact capacities,
100-message limits, stable-lock contention, barrier races for retry/reply/active/
global bounds/import/runtime, guarded CLI and private HTTP authorization/masking/
escaping/CSRF/Origin/throttle/rollback/no-file-fallback. Actual SQL Server driver,
UTF-16/result types/collations/locks/grants/deadlocks and deployment remain unverified.
No new browser QA, email delivery or native game integration is claimed.

All POST routes require same Origin and realm-specific CSRF. Persistent login
throttling is isolated from staff login; support uses separate player/staff limits
(20/30 requests per client in 15 minutes). No email, attachment or native game
integration is configured. Windows privacy depends on host ACLs.

## Routes and verification

Player and staff inboxes accept subject search (`q`, up to 120 Unicode characters),
status/category filters and `page=1..25`. Search checks authorized summaries only,
not message bodies or hidden account fields. Totals are calculated after ownership
and filters; players cannot infer another owner's queue size. Results retain the
existing update-time/ID ordering and show at most 20 tickets per page. Filtering
resets the page, out-of-date pages clamp to the last available page, and links
preserve filters when opening a conversation. Invalid/array/oversized query values
return 422 instead of silently widening the inbox. Reads never append ticket or
audit state. Staff mode still requires server-side support.manage authorization.

The new internal inbox query is additive; existing listTickets consumers retain
their result shape and behavior. There are no support data or V1 contract changes.

| Surface | Routes |
| --- | --- |
| Player identity | `GET/POST /login`, `GET /account`, `POST /logout` |
| Player tickets | `GET /support`, `POST /support/new`, `/support/reply`, `/support/close`, `/support/reopen` |
| Staff tickets | `GET /admin/support`, `POST /admin/support/reply`, `/admin/support/close`, `/admin/support/reopen` |

Tests cover real file persistence, native session isolation/expiry, ban/password
revocation, server permissions, ownership denial, masking, text bounds, replay,
revision conflicts, corruption and cross-process reply/cap races. Loopback HTTP
tests exercise the same player and staff routes with distinct cookie jars. Browser
QA covers sign-in, creation, staff replies, close/reopen and independent sign-out.
Search/pagination cases additionally exercise owner-scoped totals, stable tied
ordering, empty/stale pages, Unicode/hostile filters and actual next-page links
through HTTP. Desktop/mobile QA includes player and staff filtered pages.
See STATE.md for exact final results and remaining work.
