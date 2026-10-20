# Kernel installed website schema state

Decision 113 continues step 03 with the **first** half of its numbered work:
determining actual installed source state and persisting verified installed
history. It is not a plugin SDK/loader and it is not a migration executor. Read
STATE.md for verification and the remaining authorized step, plus
[approval storage](PLUGIN_APPROVAL_STORAGE.md),
[metadata storage](PLUGIN_METADATA_STORAGE.md) and
[schema inspection](PLUGIN_SCHEMA_INSPECTION.md).

## What this boundary is

`InstallationService` adopts one **verified physical baseline** for one
website-owned plugin into a kernel journal. It applies no DDL, executes no
migration resource, parses no SQL and grants no runtime plugin, payment or
native game authority. An enabled package keeps its installed state; a removed
folder keeps its installed state; only the physical observation, the approval
ledger and the caller's stated expectations may change it.

| Operation | Required separate website-administrator permission | Authority |
| --- | --- | --- |
| `inspect` | `plugins.inspect` | Retained verified state summary |
| `initialize` | `plugins.provision` | Empty head creation only |
| `verify` | `plugins.migrate` | Fresh physical comparison |
| `adopt` | `plugins.migrate` | One verified installed record |

No existing role receives these permissions automatically and nothing exposes
this service over HTTP, an operator CLI or a plugin-facing port. The trusted
comparison target is a `MigrationSchemaFootprint` supplied by composition, as in
[schema inspection](PLUGIN_SCHEMA_INSPECTION.md); a request or client payload can
never supply the expected schema.

## Fresh checks before any write

`adopt` validates the stated identity, then re-checks, in order: a fresh bounded
discovery scan (exact ID, publisher, version, fingerprint and no diagnostics);
the approval ledger (exact approved ID, version and fingerprint, enabled or
not); the migration receipts (no pending `in-progress` attempt for that plugin);
and a fresh read-only physical inspection of that plugin's website tables.
Refusal happens at the earliest failing boundary, so a missing package never
reaches storage, and an unapproved package is never inspected.

The caller states both `installedRevision` (the verified schema revision it
claims) and `expectedRevision` (the installed head it expects). Stating the
expected revision is what makes an exact retry after a lost commit
acknowledgement replay instead of conflicting; the service never invents it.

## Recorded state

One fixed head retains canonical JSON with schema/revision, provisioner/time
and a sorted list of installed records. Each record binds the plugin ID, the
installed revision, the canonical schema digest **and** the canonical footprint
that produced it, the approved version/fingerprint, the adoption attempt, and
the verifying actor/time. Bounds: 128 plugins, 10,000 transitions, 64 KiB of
ASCII JSON. Capacity refuses; nothing is pruned or archived automatically.

The footprint is rebuilt and re-digested on every read, so a claimed digest
cannot replace the schema it names. `adopt` is the only command: it refuses a
reused attempt identity, a regressing installed revision, a changed physical
schema reusing an already claimed revision, and a verification clock that
moves backwards. Head and audit row move in one acknowledged transaction with
CAS, actor-scoped retry identity, exact readback and checked commit, over the
same shared transport as approvals, configuration and migration receipts.

## Storage

Reviewed installation input: `database/mssql/014-plugin-installation.sql`. Only
an explicitly designated website-owned installation/lab target may apply it. It
refuses existing tables, seeds nothing and is never run by constructors, reads,
discovery or an ordinary command. `PdoJournalStore` gained exactly one closed
family, `installation`; callers still cannot supply table names, and a command
from another family is refused. Reads validate every bounded row, contiguous
revisions, unique actor/keys and the whole digest chain, so corruption denies
reads instead of repairing them.

The existing bounded ODBC ASCII/hex profile, owned transaction, table
serialization, CAS, retry key, checked row/server acknowledgement, readback and
commit/replay release are reused unchanged. This boundary adds no DDL
authority, no DELETE, no audit UPDATE and no fallback to disk settings.

## Reviewed declarative execution (decision 117)

Installed state is no longer only observed. A reviewed transition now applies
website schema and installed state in **one transaction**: the journal validates
the command, the bound participant applies the declarative operations inside that
same transaction, the postcondition is checked, and only then are the head and
audit row written and committed. A refused audit row therefore rolls the schema
change back with it, which the offline suite proves directly.

InstalledCommand gained an advance action carrying id, version, fingerprint,
fromRevision, toRevision, beforeDigest, afterDigest, planDigest, operations and
attempt. The operation set travels as **data** and is re-parsed through
MigrationOperationSet on the way in, so only bytes the reviewed format accepts
can ever reach statement construction. The resulting footprint is **derived**
from the verified baseline plus the operations; it is never accepted from the
caller, and an afterDigest the operations do not produce is refused.

A transition cannot create a baseline: without a verified installed record it is
refused, and it also refuses a mismatched source revision, a baseline that drifted
from its claimed precondition, a reused attempt identity, a regressing clock and
a reversed transition. PdoDeclarativeMigration builds statements only from
identifiers the canonical format already validated, and only inside the plugin's
own zenda_plugin_id_suffix namespace, so no plugin can reach another table, a
schema it does not own, or DML.

InstallationService::advance re-checks discovery, approval identity, receipt
reconciliation and a fresh physical inspection before the transaction, and
InstallationComposition binds the participant. Only the installation journal may
bind a participant; a journal without one refuses to execute rather than silently
skipping the schema change.

DDL 014 now admits both adopt and advance in its operation check.
## Evidence and remaining work

`tests/plugin-installation.php` covers canonical/adversarial installed state,
authorization before any I/O, per-boundary refusal order, pending-attempt
reconciliation, adoption, replay, idempotency and stale-CAS conflicts, physical
drift reporting, folder removal, cross-family refusal, audit rollback, forged
audit history, and two independent two-process SQLite races for provisioning and
CAS. Every non-fast scope runs it.

Still pending for step 03, and **not** claimed here: the reviewed executor that
applies `MigrationOperationSet` operations atomically with the installed-state
commit, guarded operator provisioning/CLI or endpoint, configuration-schema
migration, the MainAPI executable host (step 04) and any HTTP/worker lifecycle
proof. Actual SQL Server evidence for DDL 014 and this family is not part of the
opt-in owned suite yet; only offline and independent SQLite process evidence
exists. No plugin is executable, enabled or loaded, and no native game or
payment gate changed.