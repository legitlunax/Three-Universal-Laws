# Website profiles and publication policy

Players manage preferences at `GET/POST /account/profile`, reached from `/account`.
`profile.manage` is a server-owned player permission for the current owner only.
Staff cookies/roles, submitted actor IDs and URL owner parameters cannot select
another profile. POST requires Origin, player CSRF and a separate persistent
limiter (20 attempts per client/owner in fifteen minutes). Account status and
credential/session validity are rechecked at the application boundary.

Preferences are a 1–40-character display name, optional bio up to 600 Unicode
characters, and Private/Public visibility. Text is bounded UTF-8 plaintext with
control-character rejection and escaped output. Missing preferences are private,
including previously registered accounts. Publication exposes website username,
display name and bio only. Email, credentials, role, status, join time, internal
owner IDs and history are excluded.

`GET /player?username=...` displays opted-in active website profiles. Private,
inactive and missing profiles have the same not-found response. Choosing Private
removes the public projection; replaying an earlier publish cannot undo that
choice. Previously copied content cannot be recalled. Banning suppresses
publication without deleting preferences. Native fixture dossiers remain labelled
synthetic for names with no website account. Existing website accounts always
take precedence: matching names never associate native characters or online state.

`GET /account/profile/preview` is an authenticated, owner-only preview of saved
fields, even while private. It is read-only and non-cacheable and cannot publish.
Visitors and staff-only sessions go to player sign-in. It does not preview unsaved
form edits or provide GrapesJS/template-editor functionality.

## Storage and compatibility

The new Identity-local `WebsiteProfileStore` has a file adapter using private
schema-version-1 `website-profiles.json` and a stable lock. Expected owner revision,
scoped replay result, preferences and change journal commit in one replacement.
Normalized semantic payloads determine replay identity; changed payload/revision
with the same owner key conflicts. Different owners have independent keys and
revisions. Journal replay validates current state and request digests; corruption
is preserved and refused. Data-file checks occur under the lock.

Limits: 500 owners, 1000 commands, eight MiB in the offline prototype. In database mode,
website profile persistence is stored authoritatively in website-owned MSSQL tables.
The prepared adapter is PdoWebsiteProfileStore with installation input 008 for
profiles, commands and events. Owned SQLite fixtures exercise revision/replay and
audit persistence; SQL Server driver/collation/locking behavior is unverified.
Runtime reads, events and saves now own a transaction and validate all three
bounded tables together. Strict typed rows accept canonical integer driver strings,
but refuse fractional/coerced values, malformed results, orphan/duplicate events,
digest/actor/clock disagreement and snapshot/history mismatches. Replay validates
current state too; a corrupt privacy head cannot be bypassed by an old result.
SQL result JSON reads are bounded before decoding. Missing schema is unavailable,
never an empty/default/file fallback. Generic ODBC is refused. These consistency
checks do not protect against privileged, coordinated rewriting of all history.

All cooperating writers, including import, acquire the profiles boundary first.
SQL Server uses TABLOCKX/HOLDLOCK for writes and TABLOCK/HOLDLOCK for reads;
SQLite acquires its writer boundary before the first SELECT. This deliberately
serializes the bounded store's global owner/command limits, including empty state,
rather than independently locking only an owner or retry key. Read snapshots
verify every retained profile/command/event; corruption in one owner makes this
store unavailable until explicit reconciliation. Reads and events are separate
transactions under the existing port, so an editor may span consecutive revisions.

New saves validate expected revision and monotonic owner clock, then require one
affected profile, command and event row before checked commit. Caller transactions
are refused without rollback/commit. Failed/zero-row writes and false commits roll
back; ambiguous commits can be resolved by exact replay without extra events or
republishing a later private head. Storage failures have no private exception chain.
Valid v1 fingerprints/history remain compatible; malformed SQL state intentionally
refuses under decision 088. No DDL, V1 signatures or baseline changes were needed.
SQL Server locks/grants/collations/deadlocks and production operation remain unverified.

## Source-preserving profile migration

WebsiteProfileState shares the existing complete v1 file validator with the
importer: exact envelope/command shapes, 500-owner/1000-command/eight-MiB bounds,
unique scopes, per-owner revision/clock continuity, reconstructed payload digests
and exact current-snapshot/history agreement. Valid file formats/fingerprints and
privacy choices are unchanged. These consistency checks are not tamper-proof
storage or authorization of an untrusted writer.

WebsiteProfileStorageImport requires the existing regular private source and
website-profiles.lock; it takes that writer's stable lock shared and creates no
missing state/lock/default. Missing data no longer means an empty migration.
Freeze manual source edits after reviewing the dry run and throughout cutover.
Direct import validates too, accepts sqlsrv/owned SQLite exception-mode connections,
refuses caller transactions and rechecks all three targets empty inside its owning
write boundary. Every profile/command/event insert must affect one row; failed or
false commits roll back the entire collection. A committed but unacknowledged
import can be reloaded, and retry refuses the nonempty target without overwrite.

All historical command results, latest preferences (including later opt-out), and
ordered event projections survive import. Replaying an old public command returns
its historical result without republishing the current private profile. The
guarded scripts/import-website-profile-storage.php remains source-only dry run by
default; no actual SQL Server migration ran. Re-run its fingerprint review after
the stricter source validation. Tests/profile-import.php adds 52 source/SQLite/
process/CLI assertions, including fault injection, exact history/privacy retention,
stable-lock contention, competing importers and runtime false-commit rollback.

Runtime coverage adds 137 tests/profile-storage.php SQLite/process assertions and
19 tests/profile-storage-http.php loopback assertions. They cover typed/corrupt
rows and histories, owning reads/writes, exact privacy-preserving replay, all
failed/zero-row insert/update stages, false/ambiguous commits, exact capacities,
barrier races for revisions/retries/independent owners/global limits/import,
private HTTP previews, escaping, forged owners, CSRF/Origin/throttle, rollback,
missing/corrupt schema and conflicting-file denial. Existing 75 profile and 52
import assertions remain. No new visual QA or actual SQL Server evidence is claimed.

V1 AccountReader/component contracts are unchanged. Their authenticated account/
character projection is separate. Registration/auth/reset/list callers retain
their signatures; an optional constructor argument supplies publication policy to
the legacy `getPublicProfile` helper. It defaults to private without that policy
and returns the same three-field public allowlist when explicitly bound.

This intentionally migrates implicit website publication: old profile URLs become
unavailable until the owner opts in; no account rewrite or automatic consent runs.
**Do not roll back public readers/routes to code that ignores these preferences.**
It would expose old account metadata despite Private choices. A privacy-preserving
rollback proposal is in [WEBSITE_ROLLBACK.md](WEBSITE_ROLLBACK.md); operational
tooling/rehearsal remain pending and no production rollout/import occurred.
Future account deletion or identifier reuse must reconcile profile history/consent.

## Registered identifiers

Signup already permits ASCII letters, digits, underscores and hyphens (4–10
characters). Player login/session identity, profile and support validators now use
that same syntax, canonicalized to lowercase. Dots, slashes, colons, spaces and
mismatched actor/username pairs remain invalid. Fixed permissions ignore stored
game/admin roles. Numeric account IDs preserve ownership. Account/recovery storage
already supported these identifiers and is unchanged.

Old alphanumeric records retain their shape. Support version-1 structures are
unchanged, but older validators reject newly valid punctuated owner IDs. Keep
compatible readers/writers or reconcile affected records/sessions before rollback;
never discard history, rename identities blindly or reset a rejected store.

Unit/process/HTTP cases cover authorization, migration policy, exact public fields,
Unicode/numeric/punctuated IDs, corrupt/full histories, revision/replay races,
preview isolation, native-name collisions and ban/session revocation. Browser QA
uses owned website fixtures. File history is immutable through its API, not
certified tamper-proof storage. Windows ACLs, power-loss recovery, actual SQL Server profile
persistence and native integration remain unverified. See STATE.md for results.
