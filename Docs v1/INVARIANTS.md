# Domain invariants and enforcement status

Decision 105 enforces exact stored package approvals, disabled reapproval,
fingerprint-bound enablement, expected revisions, actor-scoped durable retries
and same-transaction digest-linked audit in the kernel approval store. Approval
is operator policy, never PHP execution, schema/migration permission, licensing
or native game authority. Missing physical code cannot be authorized by an
enabled row; the executable host/presence integration is still pending. No
new plugin permissions are granted implicitly. See docs/PLUGIN_APPROVAL_STORAGE.md.

## Plugin-removal safety invariant (planned; not yet verified)

An optional plugin is executable only if its canonical complete folder is
present **in the current runtime generation**, trust/approval and license checks
pass, dependencies are satisfiable and its services were registered safely.
Removing the folder must prevent new route/listener/job/widget/asset/admin
contributions on the next request; even a persisted enabled bit or stale cache
cannot resurrect it. Core and plugins without a required dependency must remain
healthy. Folder removal **never** erases website-owned MSSQL data or MU game
records. Missing provider references in saved GrapesJS JSON remain immutable
and render safely without calling absent PHP. See `PLUGIN_SYSTEM.md`.

Security boundary: in-process PHP execution is **trusted code**, not sandboxing;
unknown packages do not run at discovery. A plugin currently executing cannot
be forcibly unloaded just by deleting its folder. This invariant requires new
integration evidence before it can be marked implemented.

Profile migration validates the complete current snapshot and retained command
history together, under the existing stable source lock, without missing-source
defaults. Direct import owns its transaction, checks all three targets empty and
commits all profiles/replays/events or none. Historical public retries must not
undo a later private preference. Runtime profile save/replay must never report
success on a false commit. Broader runtime SQL row/history safety remains pending.

SQL administrator credentials are empty, one valid separate identity, or
unavailable. Missing/corrupt/multiple rows must never look unconfigured or select
an arbitrary administrator. Create/import own their locked empty-target check and
commit exactly one valid row, or roll back. Shared source reads coordinate with
exclusive file creation; no defaults/repair/fallback or unknown metadata omission.
These are website-only offline guarantees, not native or production certification.

Website account creation must validate storage encodings before writing and must
not inherit orphan recovery/password-history state. SQL creation/role changes own
their transaction and account boundary; failed/zero-row/false commits cannot
report success. Metadata/credential reads refuse corrupt values without coercion,
repair or file fallback. Directory searches treat SQL wildcard syntax literally
and sort before limiting. Website role labels never authorize AdminCP. These are
offline guarantees; actual SQL Server behavior remains unverified.

Website email/status changes revoke the current recovery link in their owning
snapshot/transaction. Canonical email no-ops and active status no-ops preserve it;
status transitions and inactive writes revoke it. Unban never revives an old link.
Issuance rechecks active status under the same account boundary in both adapters.
Unknown HTTP statuses refuse rather than becoming unban, and missing metadata
targets produce no success audit. These changes do not implement secure delivery,
session generations or durable administrative command/audit semantics.

Website credential replacement revokes the current recovery link in the same
file snapshot or owning SQL transaction, preserving status and self-service
history. Invalid replacement/state cannot partially change credentials or erase
history. Administrative authority remains separate from player/game roles. The
HTTP limiter/redacted audit do not establish durable command idempotency or an
atomic administrative audit; those remain missing.

Website SQL reset issuance/recovery/self-service own their transactions and lock
the account first. Replacement never deletes the old link, and a same-token retry
cannot clear consumed state or extend expiry. Corrupt reset values refuse without
repair; credential/token transitions roll back on any failed/false commit. These
website locks grant no native MU authority. See docs/WEBSITE_RECOVERY.md.

Database-mode public recovery remains disabled until secure delivery is bound.
The file sandbox's browser token preview must never issue/reflect tokens for
database-backed accounts. Reject before lookup/session/token mutation, with a
constant private response and unchanged state; retain global Origin validation.

SQL template null expectedRevision means create-if-absent. Existing drafts require
exact revision CAS, and inserts/imports honor the revision-to-head foreign key.
Whole-source template imports validate all identities/pointers before one owning
transaction; failures leave no partial template. Canonical revisions are immutable.
SQL publication remains unavailable until content/asset/lifecycle validation is
implemented; imported metadata projections do not rewrite historical envelopes.

MarketplaceService refuses native escrow/extraction/reclaim/purchase while the
protected mutation gateway is unbound, even with keys, account-name inputs,
escrow metadata and injected game writers. Metadata persistence cannot prove
custody, payment, delivery, offline verification or lock ownership. The inherited
direct-writer lifecycle was removed because independent metadata/native effects
were not atomic and optional dependencies could be skipped. Draft workflows and
metadata fixtures remain; native M6 execution is incomplete.

All authoritative website configuration must be stored in website-owned MSSQL
tables. File caches are disposable and never a fallback authority for settings.
Existing file/JSON/SQLite settings are offline prototypes; production MSSQL
persistence/migration are unverified, and other configuration adapters remain
unimplemented. See DATABASE.md.

The prepared core settings database adapter now refuses missing/corrupt state,
implicit initialization, unversioned saves and nested mutations. It commits
settings/revision/minimal history together and preserves later state on replay.
The version-2 news/runtime record uses that same journal and must also be present
in database mode. Strict policy refuses unsupported capabilities and unsafe
security/preview/upload values. Only bounded image upload enablement/size and news
connection are editable. No environment/JSON fallback or implicit seed is allowed.
Independent core/operational commands do not establish global snapshot atomicity.
Event timetable SQL commands independently commit their complete current/initial
definitions and retained replay journal in one CAS-protected document. Missing/
corrupt schema/seed/history is unavailable, never replaced with file/examples.
No command starts native MU events or broadens game capabilities. Module-domain
rules and original v1 history/result formats are shared with the offline file
prototype; imports must retain prior revisions/retries rather than reset history.
The loot adapter follows the same owning transaction/CAS boundary with its own
table and state rules, preserving generated IDs and operation-scoped retries.
History validation also rejects fingerprint/payload mismatches, negative clocks
and intermediate loot-entry overflow. These are corruption checks, not privileged
tamper protection. Source preflight must never print configuration/actor values,
open a database, invent missing records or partially report a valid migration plan.
Only owned SQLite transactions/HTTP were exercised; MSSQL evidence and the rest
of the configuration migration remain pending. See docs/WEBSITE_CONFIGURATION.md.

AdminCP requires an independently provisioned website administrator account.
Game-account authentication, website-player sessions, matching usernames and
in-game GM/staff privileges never confer administrative access. The current
sandbox separates staff/player credentials and session realms; native account
integration and production administrator persistence remain future work.

The rules below are mandatory constraints. The local sandbox now enforces server
actor permissions at NewsService, same-origin/CSRF boundaries, persistent login
throttling, secure session lifecycle, escaped content, bounded requests and private
storage. Unit, concurrency and actual HTTP tests exercise that slice. Game,
payment, upload and template publication enforcement remains **not implemented**;
their capabilities stay disabled. Offline fixture success is not live MU evidence.

The newer website support/loot slices enforce permission, ownership, revision,
replay and minimal-audit rules through atomic private snapshots. Player sessions
are separate from staff and revoke on account ban/password changes. Native market
and Siege operations remain denied; sandbox shop paths reject native economy
writers. These local guarantees do not establish game-server synchronization or
production database/audit durability. See docs/PLAYER_SUPPORT.md and
docs/SIEGE_AND_LOOT.md for the actual implemented boundaries.

Website timetable edits must commit definitions, expected revision, replay result
and minimal journal together. Public statuses/calendar downloads derive only from
that configuration, preserve UTC day/weekday boundaries and expose no journal
identity. Export limits refuse oversized results; they must never silently omit
scheduled occurrences. These rules establish no native event scheduling authority.

| Rule for phase-4+ implementation | Required evidence before enabling |
| --- | --- |
| Unknown schema, capability, item format or allocator disables mutation | schema metadata fixtures; parameterized SQL/identifier tests; isolated adapter integration |
| Actor owns each affected resource and has operation permission | negative ownership/authorization tests at shared service boundary |
| Game account is authoritatively offline and stays protected throughout writes | trusted presence source, missing-state denial, grace policy, account lock, recheck under lock, coordinated game-server integration |
| Writes are serialized and stale state cannot overwrite new state | cross-worker lock, transaction-owned connection, CAS/version tests and fault injection |
| Purchase/trade/workshop/callback is idempotent | unique scoped key, canonical payload hash, replay/conflict/concurrent delivery tests |
| Currency, item movement and lifecycle state commit consistently | ledger/escrow/serial/ownership checks, rollback/failure scenarios; no split independent transactions |
| Audit is durable and immutable with minimal before/after state | same-transaction append, write-only role and retention/tamper controls |
| Preview has no mutation authority; draft remains private | separate read-only context, denied mutation and unpublished-access HTTP tests |
| Visual editing cannot change authorization, prices or serialization | server-owned component registry and approved read projections only |
| Uploads/imports never execute content | MIME+extension/byte limits, decoding, random names, non-executable storage and adversarial tests |
| Auth/registration/transactions have rate limiting and CSRF | endpoint integration tests; provider-signature webhook exception is explicit |

A database transaction alone cannot make an independently running game server
safe. A missing presence record/timestamp is unknown, not evidence of disconnection.
No production player data may be used for destructive tests. Known legacy gaps
and mismatches are listed in docs/discovery/DATA_AND_INTEGRATIONS.md.

Website profiles are private without explicit owner publication. Public fields
are allowlisted; matching names never prove native ownership. Preview requires
self authorization and cannot change visibility. Revocation cannot be undone by
replaying an old publish. Registered identifier syntax must stay consistent across
sessions and owned website data, without broadening staff authority or accepting
path/control syntax. See docs/WEBSITE_PROFILES.md for migration/rollback limits.

Authenticated website password changes recheck current password, active account
and the session credential version under the owning write boundary. Password,
reset-token invalidation and revision/replay/history commit together or not at
all. Successful changes revoke player sessions. Never expose credentials or
private replay material in forms, actors, audit projections or logs. See
docs/WEBSITE_PASSWORDS.md; this grants no native MU credential authority.
