# Authenticated website password changes

`GET/POST /account/password` changes only the signed-in registered website
account. Account navigation exposes it. Anonymous and staff-only sessions go to
player sign-in; submitted account/actor/role/codec fields grant no authority.
No native MU credentials, staff passwords, email or game data are changed.

The application checks player CSRF and same Origin, bounds request fields and
uses a separate persistent `player-password` limiter (five attempts per client
and scoped owner within 900 seconds). Confirmation, current-password verification,
active status, session credential version, expected revision and replay key must
all agree. New passwords retain signup's existing 6–12-byte policy, reject NUL,
username reuse and current-password reuse, and are stored with fresh bcrypt.
Invalid fields are never repopulated in responses. Modernizing the website's
password policy independently of native MU codecs remains future work.

Success clears the current player session/CSRF and redirects to sign-in with a
notice. Every other player session loses authority on its next credential recheck.
Staff sessions remain separate. This does not terminate requests that already
passed authorization before the change; the password command itself rechecks
the session's private credential version and account status under its write
boundary. The credential version is never put in the form or public actor.

## Persistence, replay and compatibility

New internal `WebsitePasswordStore` extends website persistence without changing
`PlayerAccountStore`, V1 contracts or component properties. Both account adapters
implement it. `PlayerIdentityService` keeps its old three-argument constructor;
password storage and its dedicated limiter are optional explicit bindings and
absence denies this workflow. Registration, login, recovery, account management,
profile/support and authenticated AccountReader consumers retain signatures and
fixed permissions. Bootstrap supplies the real website bindings.

The older registration authentication helper now rechecks credentials before
returning success and upgrades bcrypt only through an exact active-credential
compare-and-swap. A concurrent replacement makes it fail without restoring the
old password. Stores without the optional extension still authenticate but skip
automatic rehash; they are never sent an unconditional credential write.

Both rehash adapters now validate expected/replacement bcrypt encodings with the
same persistence policy, rejecting invalid costs/bytes before a write. SQL retains
its single active-credential compare-and-swap statement and redacts transport
failures without chaining driver errors. See PLAYER_ACCOUNT_STORAGE.md and
decision 084; actual SQL Server collation/byte-comparison behavior remains unverified.

The file adapter holds the existing stable account lock, rereads account and
token state, then atomically replaces one account snapshot containing the new
password, current consumed-token marker, replay outcome and journal. Existing
version-1 rows gain optional private `passwordChanges`; absent history means
revision zero. Account projections omit it. Corrupt account/history/token state
is preserved and refused. Account snapshots now refuse writes over the existing
16 MiB read cap, preventing a successful write that subsequent reads cannot use.

The PDO adapter owns a transaction, reads history, compares active status and
exact prior hash/codec in the credential UPDATE, invalidates existing reset rows
and writes `zenda_password_changes` before commit. Nested transactions are
refused. History insert/update failures roll back credentials, token state and
journal. Schema setup adds this website-only table in owned SQLite fixtures;
no production schema migration or other driver certification occurred.

SQL self-service now acquires the account boundary before reading credentials and
history, matching reset issuance and consumption. This serializes their common
credential/token state and preserves the original exact replay/history semantics.
See [website recovery storage](WEBSITE_RECOVERY.md); this is not native MU locking.

Per-account history has at most 100 commands, ordered revisions/timestamps and
unique 32-hex keys. Keyed private request fingerprints bind credential version,
expected revision and both passwords; neither passwords nor fingerprints/keys/
secret enter journal projections or diagnostic logs. Private history includes a
replay secret, so it must receive the same protection as credential storage; this
is not a separate secret vault. Audit output contains exactly operation, revision
and UTC time. Full history remains readable and refuses new writes without
pruning. Retention, archival and tamper-proof storage remain unimplemented.

Exact internal retries return the original revision without hashing a new
credential, appending history, reverting a later reset or invalidating a newly
issued recovery link. A changed payload conflicts. HTTP retries after successful
sign-out must authenticate again; revoked sessions cannot use replay to regain
authority. A new login has another credential version, so it must submit a fresh
command. Existing recovery tokens cannot undo the committed change; tokens issued
afterward use the normal recovery policy.

## Administrative website replacements

AccountManagementService requires separate server-side admin.access and validates
the website password policy before hashing. Player/game roles named admin/GM grant
no authority. Its existing updatePassword port now commits the replacement and
current recovery-link revocation together. This is an explicit internal behavior
migration, not a native MU write binding.

File storage retains its consumed-token marker in one atomic account snapshot;
the token file stays unchanged and readers overlay revocation. SQL owns an account-
first transaction, validates relevant token/history state and CASes credential and
token together. Failed/false commits roll back both; nested transactions refuse.
Both preserve status, role and self-service revision/replay/history. A reset never
unbans an account. Historical self-service retries cannot restore its old password
or revoke a fresh post-reset link. Missing accounts return false; HTTP responds 404
without a false success redirect/audit.

Shared pure WebsitePasswordReplacement validates codec encodings and the 255-byte
persistence bound for replacement/recovery/import. Invalid dummy hash strings now
refuse; positive fixtures use real bcrypt. No V1 signature, DDL or baseline changed.
Default staff bcrypt replacement changes the hash-derived session version and
revokes player sessions on their next check. Identical credential bytes still
revoke the current link but cannot independently change that session version.
Global session revocation, a historical token archive and durable administrative
idempotency are absent. Repeated administrative commands are new operations.
The detached HTTP audit remains separate from the credential transaction.

Email/status actions now atomically revoke their current recovery links too;
canonical email/active no-ops preserve them, and unban never revives an old link.
They retain credential bytes and complete self-service replay/history. Missing
metadata targets and unknown HTTP statuses produce no false success audit. See
WEBSITE_RECOVERY.md and decision 082; session generations and durable administrative
command/audit semantics remain unimplemented.

POST /admin/players/action retains staff/Origin/CSRF checks and adds a persistent
20-attempt/900-second client/scoped-staff budget before payload parsing. Invalid
authorized commands consume it; management reads remain available. New email audit
entries record only an update flag; historical audits were not rewritten.

tests/credential-replacement.php has 87 file/SQLite/process assertions for revocation,
history/replay/session/status/codec preservation, corruption/capacity/rollback,
ambiguous commits, import preservation and barrier races. SQL HTTP adds 12 cases
(154 shared assertions); file HTTP adds three revocation/privacy cases. Actual SQL
Server and atomic administrative audit remain unverified. Public database recovery
stays disabled pending secure delivery; see WEBSITE_RECOVERY.md.

## Rollback boundary and evidence

Preserve password history and consumed-token markers with their owning account
snapshot/table. Never restore an older credential snapshot independently or send
old writers into current token files: that can revive credentials/recovery links
and lose audit/replay evidence. See the proposed offline procedure in
[website rollback](WEBSITE_ROLLBACK.md); no production rollback was performed.

The explicit file-to-SQL importer now retains complete password replay/history and
folds a matching consumedResetToken marker into SQL's used recovery bit. It reads
both source documents under their one stable account lock and refuses malformed
present token storage instead of skipping it. See
[player-account storage](PLAYER_ACCOUNT_STORAGE.md); actual SQL Server remains unverified.

`tests/passwords.php` covers file/SQLite persistence, policy, projections, exact
retry/conflicts, fresh recovery links, later-reset protection, SQL insert/update
failure rollback, nested transaction refusal, corrupt/full histories and session
revocation including an interleaved credential replacement. Cases also cover
low-cost rehash and interleaved helper authentication. Real file workers race
competing changes, duplicates and recovery against self-service.
`tests/password-http.php` uses its own loopback fixture to cover Origin/CSRF,
methods, owner/codec forgery, throttles, empty password errors, session separation
and post-change login/history. Isolation preserves the main HTTP suite's genuine
client-wide login limit rather than changing the limit or clearing its counters.
Manual browser QA covers desktop/mobile and the real form flow. See STATE.md for
actual checks. SQLite tests and private file locks do not certify production SQL,
power-loss durability, Windows ACLs, native identity or remote deployment.
