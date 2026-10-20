# Canonical template SQL persistence

Database mode prepares PdoTemplateStore/PdoPublishedTemplateResolver behind the
existing V1 TemplateStore and internal published resolver. Public signatures,
component properties and frozen baselines are unchanged. This is owned SQLite
evidence, not actual SQL Server certification or live GrapesJS editing.

SQL draft saves own a transaction, lock/recheck and CAS the current pointer. Null
expectedRevision is **create-if-absent**, never an overwrite wildcard; existing
SQL callers must send the exact revision. The offline JsonTemplateStore retains
its older prototype behavior. Reject nested writes, malformed/corrupt heads,
dangling current pointers and mismatched revision identity/digests. Preserve
unknown editor-owned fields and old canonical revision envelopes, with typed,
bounded server-owned author/time metadata. Each document
is bounded to eight MiB and revision IDs 1..10000; template IDs remain lowercase
letters/digits/hyphens, at most 64 bytes, including numeric string IDs.

Draft saving never publishes. Direct SQL publish checks authorization and refuses
while the complete publication validator/lifecycle is unavailable. The inherited
implementation rewrote historical revision JSON; that path was removed. Imported
publication pointers can still resolve their historical data: the public view
overlays status/publisher metadata without altering canonical revision rows.
Anonymous resolution retains its existing null-for-absent/unpublished/corrupt
contract. Actor-authorized reads return redacted unavailability for corruption,
and null only for genuine absence. No draft data is exposed by the public resolver.

TemplateStorageDocument validates persistence identity, bounds, schema and
digests. It is not an HTML/CSS sanitizer, executable-content validator, asset
existence check or authentic GrapesJS compatibility test. Publication remains
disabled until those boundaries and browser tests exist.

## Explicit complete-source import

Review database/mssql/005-template-storage.sql in the designated website-owned
database. It declares an immediate revision-to-head foreign key. Constructors and
requests execute no DDL; new writes and imports insert heads before revisions.
Runtime needs SELECT/INSERT on immutable revision rows and SELECT/INSERT/UPDATE
on heads. Deployment grants, SQL Server driver/TLS/locking/restore remain untested.

Select the existing new sandbox with ZENDA_SANDBOX_DIR, outside webroots/legacy.
TemplateStorageImport requires actual template directories, stable .lock files,
bounded revision files and coherent draft/public snapshots and pointers. It
never substitutes draft.json for a missing immutable revision or skips unsafe
entries. All references, template/revision identities and collection bounds
(100 templates, 10000 revisions each, 64 MiB total canonical JSON) validate before
a connection. Freeze edits: individual template locks do not create one atomic
source snapshot. Existing files are neither rewritten nor deleted.

```powershell
& 'C:/xampp/php/php.exe' scripts/import-template-storage.php
```

The default dry run prints a canonical collection SHA-256 and opens no database.
Apply additionally requires the designated owned-nonproduction/MSSQL scope,
private connection inputs and --apply --expected-sha256=<reviewed-digest>. The
entire empty-target collection commits in one transaction, retaining every
revision and current/previous pointer. Any later insert failure rolls back all
heads/revisions. Re-run the dry run after this stricter source-validation change;
older fingerprints may no longer match. No production apply ran.

The explicit single-project initialize helper remains available for owned setup;
CLI migration uses initializeCollection once, replacing the inherited per-revision
loop that could commit a partial template before colliding with its head key.
Never use stale file writers/readers as fallback after SQL edits.

tests/template-storage.php exercises FK-enforced creation, immutable history,
CAS/rollback, null/stale denial, full imports, source preservation, pointer/identity
corruption, publication denial, anonymous projections, ambiguous commit recovery,
numeric IDs and independent-process revision races. Existing template/view tests
remain. Real SQL Server, complete import/publication content security and live
GrapesJS round trips are still missing evidence.
