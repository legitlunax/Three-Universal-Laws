# Website recovery storage

Email/status policy now matches across both website adapters (decision 082).
A changed canonical email, any status transition and every inactive status write
revoke the current recovery link in the same account snapshot or SQL transaction.
Unchanged email/active no-ops preserve it. Unban clears its reason but never
reactivates an old link; recovery needs a fresh token afterward. Omitted inactive
reasons retain their existing bytes, including valid imported whitespace.

SQL uses the shared account-first lock, strict stored credential/metadata/token
validation, metadata/token CAS and rollback on failure/false commit. File storage
uses its existing stable account lock and consumed-token marker. Both issuers now
recheck active status inside that boundary; same-token issuance cannot clear a
stored consumed flag or extend expiry. A fresh link serialized after an email
change is allowed. Future secure delivery must verify the current recipient and
bind issuance/outbox state under the same boundary; a prior application email
lookup alone is insufficient. No delivery/outbox is implemented here.

HTTP metadata actions retain staff/CSRF/Origin/throttling. Unknown status values
return 400 rather than unbanning, and missing email/status/role targets return 404
without a success audit. The audit remains detached from the SQL transaction;
administrative version/retry/atomic audit and session generations remain missing.

This boundary manages website player credentials only. It does not authenticate
MU accounts or grant AdminCP authority. Application recovery service requests consume
their attempt budget and return null for absent, inactive or mismatched-email
accounts. Existing HTTP CSRF/Origin, limiter and separate session boundaries remain.

Public database-mode GET/POST /forgot-password and /reset-password are currently
disabled with a constant private/no-store 503 before credential/session lookup,
token generation/consumption or form reflection. Global Origin validation still
rejects foreign POSTs first. The older file sandbox displays generated tokens as
an explicit offline demonstration; database-backed accounts must never inherit
that flow. Secure delivery/outbox and safe endpoint integration are missing, so
storage tests do not establish a working database-backed public recovery flow.
There is no environment/configuration bypass for this gate.

PdoPlayerAccountStore supports the designated sqlsrv adapter and owned SQLite
fixtures with PDO exception mode. Previously advertised generic MySQL/ODBC paths
had no verified synchronization policy and now refuse construction. Public/module
port signatures and DDL 010 are unchanged; no baseline was refreshed.

Issuance, recovery consumption and authenticated password changes each own their
transaction and acquire the website account boundary first. SQL Server uses
UPDLOCK/HOLDLOCK; SQLite obtains its write boundary before reading. Recheck active
account state under that boundary. This is website database synchronization,
not a native MU offline/account lock or game mutation authorization.

Issuance inserts an absent token or compare-and-swaps the existing hash/expiry/used
snapshot. It never deletes the prior row and needs no DELETE permission. Failures,
including zero-row updates or false commits, preserve the prior link and credentials.
Missing/inactive accounts cannot receive orphan recovery rows. Constructors and
requests execute no DDL. Runtime still needs the reviewed SELECT/INSERT/UPDATE
permissions described by DDL 010; no grants were applied or certified here.

A retry using the same hash cannot clear used=1. Reusing that hash with a different
expiry refuses, preventing an old link from being extended/reactivated. A genuinely
different token can replace it normally. The store has one current link per account,
not a token archive or new idempotency-key contract. After an ambiguous committed
issuance, reload and an exact retry retain one row and any consumed state.

Stored reset identity, lowercase 64-hex hash, used bit (exact 0/1) and positive
integer expiry are validated before use. Unknown flags or corrupt values refuse
without coercion, repair or credential changes. Recovery checks the token under the
same account boundary, then CASes token expiry/hash/used and prior active credential;
both changes commit together or neither does. Replacement credential encodings and
the existing 255-byte SQL bound validate before consumption. SQL details are redacted.

Self-service password changes acquire that same account boundary before their
existing password/replay/history checks. Concurrent issuance either precedes the
change and is invalidated, or follows it as a fresh link. Recovery and a self-service
change based on the old credential cannot both replace it. Existing history and
exact retry behavior remain; this does not add a recovery audit archive or durable
delivery/email outbox. Native mutation and payment providers remain unbound.

tests/reset-storage.php has 72 assertions, including DELETE-denied replacement,
insert/update/zero-row/transaction/commit failures, consumed-state and expiry
protection, strict corruption refusal, inactive/missing account requests, ambiguous
commits and invalid credential replacements. Barrier-synchronized owned processes
race first issuers, duplicate consumers, issuance against consumption/self-service,
and recovery against self-service. Existing identity, recovery, password, migration
and HTTP suites remain. Source/server layouts are unchanged; no browser QA is claimed.

tests/settings-http.php adds 12 real HTTP refusal/no-effect cases with injected
owned SQLite player storage and conflicting file accounts/tokens. GET/POSTs expose
no forms/tokens/cookies, cannot enumerate accounts, retain Origin validation, and
leave both SQL/file credentials, links, history and detached audit unchanged.

Actual SQL Server driver/TLS/result types, grants, lock waits/deadlocks and cutover
remain unverified. Administrative updatePassword now atomically revokes its current
link and preserves self-service history; see WEBSITE_PASSWORDS.md. Other CRUD and
administrative revision/idempotency/same-commit audit still need review.
No real SQL Server, native game, production account or legacy bootstrap was tested.
