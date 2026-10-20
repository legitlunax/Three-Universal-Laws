# Identity boundary

Read root STATE.md and AGENTS.md. This module implements separate local website
staff and registered-player sessions. Neither establishes native MU account
ownership. Do not present these credentials as a live MU integration.

AdminCP must authenticate a separate website administrator account, never a game
account. Native MU login, player sessions and in-game GM/staff flags confer no
AdminCP authority, including when the username matches an administrator. Keep
administrator credentials, session realms and permission checks independent.

Keep credentials/session/throttle ports in Domain, coordination in Application and
native/file adapters in Infrastructure. SandboxAuthorization implements frozen V1
Authorization and re-reads the current server actor. Browser permissions, form
actor IDs and old session claims cannot grant authority. Preserve CSRF, exact
same-origin POST validation, session ID/token rotation, expiry and persistent
client-wide login limits. No default password or automatic credential overwrite.

State must be outside source/webroots in a dedicated private directory. Validate
resolved ancestors before creating files; never reach the legacy tree through a
junction. Windows chmod does not enforce ACLs; POSIX ownership/mode checks fail
closed. Do not log credentials, session IDs or tokens. Run identity + HTTP tests
and complete verification after changes.

Native MU Online identity domain models reside in `Domain/`:
- `PasswordCodec`: constant-time password verification, hashing, and rehash checks
  supporting bcrypt, WebZen uppercase MD5 (`wz-md5`), and plaintext with null-byte rejection.
- `AccountRegistrationPolicy`: strict validation of native MU account ID (4..10 alphanumeric,
  reserved word blacklist), password length (6..12, no username reuse), and email (5..50 chars).
- `PasswordResetToken`: cryptographically secure 64-hex single-use recovery token bound to
  an account ID, storing a SHA-256 hash with explicit TTL and expiry validation.
Future MU auth requires its own verified adapter and contract review; do not reuse sandbox usernames as native accounts.

Website players use PlayerIdentityService/PlayerAuthorization and NativeSession's
separate player realm. Never derive staff permissions from player/game roles.
Recheck account status and credential revision on every authorized player request.
Recovery must use PlayerAccountStore::consumeResetAndUpdatePassword; never split
token use from password replacement. Preserve the file adapter's private consumed
token marker and projection redaction. Old writers that ignore it require token
revocation/reconciliation before rollback. Corrupt account/token files must never
be replaced with empty defaults. See docs/PLAYER_SUPPORT.md.

Website profiles require self-scoped profile.manage and explicit publication;
missing preferences are private. Preserve the public allowlist, isolated native
fixtures, read-only owner preview and atomic preference/replay/journal writes.
Keep identifier syntax consistent with signup (4–10 ASCII letters/digits/_/-),
lowercase canonical actors and exact actor/username matching. No staff authority
comes from these identifiers. Older public readers ignore privacy choices; read
docs/WEBSITE_PROFILES.md before changing policy or proposing rollback.

Authenticated password changes use internal WebsitePasswordStore. Preserve the
session credential-version/current-password/active-status recheck under the
account write boundary, atomic token invalidation and replay/history, separate
limiter and session revocation. Keep password/replay material out of projections,
forms and logs. Old constructors deny unbound password workflows. See
docs/WEBSITE_PASSWORDS.md and docs/WEBSITE_ROLLBACK.md; proposed operations are
not implemented rollback tooling or production verification.

PlayerAccountStorageImport must read accounts and optional reset storage under
their one existing stable accounts.lock, with no source creation/repair or silent
omission of invalid records. Direct imports validate too, own their transaction,
check all three targets empty under a write boundary, and commit credentials,
complete password replay/history and recovery state together. Preserve consumed
file-token markers by folding matching tokens into SQL used=1; migration must
never revive a previously consumed recovery link. Unknown metadata requires an
explicit migration. See docs/PLAYER_ACCOUNT_STORAGE.md and retain process/rollback
coverage. This website migration never establishes MU ownership or staff authority.

SQL reset issuance must never delete the previous link before replacing it. Keep
issuance/recovery/self-service under their owning transaction and common account
lock/recheck, with exact token/credential CAS and rollback on false commits.
Unknown used flags/corrupt rows refuse without repair. Reissuing the same token
cannot clear its consumed state or alter expiry. SQL Server and owned SQLite are
the supported adapter families; do not restore unverified generic driver claims.
See docs/WEBSITE_RECOVERY.md and retain barrier/process/failure/HTTP coverage.

updatePassword atomically revokes its current recovery link with replacement,
preserving status and self-service history. Retain the file marker or SQL account-
first transaction/CAS and strict WebsitePasswordReplacement validation. Identical
bytes do not independently revoke hash-derived sessions; administrative command
version/retry/atomic audit and global session revocation remain separate gaps.
HTTP account actions require staff/Origin/CSRF and the persistent limiter; missing
password targets produce no success audit. Do not log changed email values.

Email/status changes also revoke the current recovery link atomically. Preserve
canonical email/active no-ops, revoke on all status transitions/inactive writes,
and never revive a link on unban. Both issuers must recheck active status under
the same stable account boundary; same-token issuance cannot clear used or extend
expiry. Unknown HTTP statuses refuse; missing metadata targets produce no success
audit. Preserve valid imported mixed-case email and reason bytes. Read decision
082 and retain rollback/process/HTTP coverage. Secure delivery must bind recipient
verification and issuance to current account state; no delivery exists here.

WebsiteAccountRecord validates website creation and metadata/credential shapes;
do not restore coercion of corrupt rows or weaken encoding checks for fixtures.
Creation/role SQL commands own their transaction/account boundary and refuse
zero-row/false commits. New accounts cannot inherit orphan reset/history rows.
Search is bounded literal text with newest-first/ID ordering before limits.
Keep valid v1 optional file roles, imported zero clocks and email/reason bytes.
Both rehash adapters validate expected/replacement encodings. Registration storage
failures are 503, real throttles 429 and concurrent duplicate conflicts 409.
See decision 084; these core/free changes grant no native or staff authority.

Administrator SQL credentials use PdoAdminCredentialDocument: sqlsrv/owned SQLite
or the verified Identity-owned SQL Server ODBC ASCII profile (decision 101).
No Admin internal imports or generic ODBC compatibility. Preserve null only for
an empty installed table, strict state and multi-row denial, owning transaction/
locked empty recheck/one inserted row/exact readback/checked commit. Redact rollback
errors too. DDL 009 and 32 administrator SQL cases now have owned local evidence;
decision 102 adds scoped real administrator/settings HTTP with explicit injected
lab stores. Full default-factory SQL composition and trusted TLS remain
unverified. See docs/MSSQL_HTTP.md. Runtime
needs SELECT/INSERT only; do not add UPDATE/DELETE/DDL for the SQLite write lock.
CredentialStorageImport validates direct input and bounded existing source under
the same file lock used exclusively by FileCredentialStore creation. Reject
unknown metadata/linked sources without creation or repair. Keep the file
prototype's load-denial behavior distinct from SQL readiness errors; no fallback.
See decision 086 and docs/ADMIN_CREDENTIAL_STORAGE.md; preserve process/HTTP cases.

Profile imports use the complete WebsiteProfileState validator, not just individual
profile checks. Require existing source JSON/stable website-profiles.lock and
read under that lock without creation/defaults. Direct import validates, owns its
transaction, rechecks all three targets empty and requires every inserted row and
commit to succeed. Preserve all replay/history and later privacy opt-outs. Runtime
profile save/replay also check false commits and redact driver errors; strict
SQL row/replay/capacity concurrency still needs audit. See decision 087 and
docs/WEBSITE_PROFILES.md; no live SQL Server evidence is implied.
