# Website player-account migration

## Runtime storage and compatibility

Decision 084 adds shared pure WebsiteAccountRecord validation for file/PDO
creation and projections. Canonical IDs of 1..10 ASCII letters/digits/_/- remain
storage-compatible; application signup still enforces its stricter 4..10 policy.
Metadata never includes credentials, reset markers or private replay material.
Credential reads validate their own selected fields; metadata SQL reads do not
query password hashes. Corrupt stored values refuse rather than becoming strings,
zero clocks, default roles or empty directories. Valid optional file roles default
to player; SQL roles remain required. Valid imported zero clocks, mixed-case email
and reason bytes survive unchanged. File writers retain unknown private fields;
the strict importer still refuses unreviewed metadata rather than omitting it.

Creation validates identity/email/hash/codec/time before writing. Negative clocks
now refuse; zero still selects current time. SQL owns an account-first transaction,
checks canonical email under its write boundary and requires one inserted row.
SQLite acquires its writer boundary before reading; SQL Server uses UPDLOCK/
HOLDLOCK. Both adapters refuse orphan reset rows for a new identity; SQL also
refuses orphan password history. Failed/false commits roll back. After an ambiguous
creation commit, reload the account: retry refuses the existing identity and does
not overwrite it. This is not durable command idempotency or an atomic audit.

Role updates serialize on that account boundary, preserve credentials/status/
recovery/history and confer no administrator authority. Bcrypt rehash validates
both encodings, accepts only the exact active expected credential and preserves
logical password/recovery/history; SQL retains one owning autocommit statement.
SQL failure diagnostics no longer retain chained driver exceptions on these paths.

Directories/search sort by createdAt descending, then canonical ID ascending,
before applying the existing 1..100 limit clamp. Search is a literal substring in
ID/email, case-insensitive for ASCII, bounded to 128 bytes and valid UTF-8 without
NUL. SQL %, _, [, and the escape marker do not broaden queries. This explicitly
replaces SQL wildcard behavior and fixes the file adapter's insertion-order limit.
Registration keeps CSRF/Origin/throttling: storage failures yield private 503,
genuine throttles 429 and concurrent duplicate conflicts 409. Validation/precheck
duplicates retain 422. Successful HTTP audit remains a separate append.

tests/account-crud.php has 142 file/SQLite/process assertions; shared SQL HTTP
adds 11 cases (177 total). Actual SQL Server drivers/collations/byte comparisons,
query-plan/range locks, grants, deadlocks and deployment remain unverified.
This core/free account work does not move or activate premium modules.

## Explicit import boundary

This migrates only this project's private website accounts into the designated
website-owned MSSQL database using DDL 010. It never imports native MU credentials
or establishes game ownership. Player roles, including admin/GM labels, confer no
AdminCP authority; administrator accounts and sessions remain separate.

PlayerAccountStorageImport reads existing player-accounts.json and, if present,
password-resets.json while holding the existing accounts.lock shared. Both file
writers use that same stable exclusive lock, so these two files form one coherent
source snapshot. The reader creates no lock, state, permissions or defaults.
Missing recovery storage is legitimate before any recovery activity; present
empty/malformed/oversized/linked/invalid storage refuses the entire import.
Require regular private files outside webroots, with a 16 MiB cap per document.

The strict version-1 plan retains every account, password command/replay secret
and recovery record. Reject orphan/mismatched tokens, unknown fields, invalid
history/credential encodings, duplicate emails and values that cannot fit DDL 010.
Existing canonical file IDs are lowercase letters/digits/underscore/hyphen up to
ten bytes; display usernames must match those IDs ignoring case. Compatible
mixed-case recovery account IDs and hex hashes canonicalize to lowercase SQL
values without changing token meaning or the private source files. Unknown future
metadata requires an explicit migration decision rather than silent omission.
Freeze source edits after reviewing the dry run and throughout cutover; the lock
does not prevent later edits once the reader returns.

The file adapter consumes recovery tokens atomically through consumedResetToken
in its account snapshot, leaving the old token file unchanged. Import must fold a
matching marker into SQL's used=1 bit, even if the token file still says false.
Otherwise an already-consumed recovery link could become usable again. Fresh
unmatched tokens retain their state. Password history/replay bytes are preserved,
not regenerated. Source files retain their marker and are never rewritten/deleted.

Email/status recovery revocations use that same marker and survive import without
reviving links. Runtime SQL account locks retain valid imported mixed-case emails
and reason bytes; corrupt credential/metadata encodings refuse without repair.
Email existence/update checks compare canonical lowercase addresses, so an
imported mixed-case address cannot be silently reused on case-sensitive SQLite.
See WEBSITE_RECOVERY.md and decision 082 for the explicit behavior migration.

```powershell
& 'C:/xampp/php/php.exe' scripts/import-player-accounts.php
```

Select the existing new sandbox explicitly with ZENDA_SANDBOX_DIR. The default
prints only a full-plan SHA-256 and opens no database. Apply requires the reviewed
--expected-sha256, --apply, owned-nonproduction migration scope, MSSQL backend and
private designated connection inputs. Re-run the dry run after these stricter
validation changes. No production migration or actual SQL Server test ran here.

Direct import validates too; the CLI is not the sole security boundary. It refuses
caller transactions/unsupported drivers, takes its own write boundary and checks
all three target tables empty under that boundary. Accounts, password history and
reset rows commit together. Later insert failures roll back all three; SQL errors
are redacted. After an ambiguous commit, inspect the complete designated target;
retry refuses a nonempty target rather than overwriting it. Never reopen stale
file writers/readers as fallback after SQL changes. Rollback requires the explicit
credential/token/history reconciliation in WEBSITE_ROLLBACK.md.

tests/player-import.php has 66 assertions for strict all-or-nothing source checks,
consumed-token protection, exact password replay/history retention, nested/occupied
target refusal, insert rollback, ambiguous commits, guarded CLI fingerprints,
source preservation, independent source-lock contention and concurrent empty-target
imports. Tests use owned files/processes/SQLite only. The contention fixture uses
a fixed owned completion signal because Windows process pipes remain blocking;
it never waits for pipe EOF while holding the source lock. No assertion/warning is
suppressed. SQL Server driver/TLS/types/locking/grants and actual cutover remain
unverified; this slice does not certify every inherited account runtime operation.
