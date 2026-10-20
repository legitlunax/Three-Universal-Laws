# Kernel plugin approval persistence

Decision 105 implements the **approval portion of step 03**. It is not a plugin
SDK/loader, installed migration ledger, configuration store or HTTP interface.
Read STATE.md for verification and the next unfinished step.

## Ownership and authority

`src/Kernel/Plugins` owns Domain rules, the internal Application `ApprovalStore`
port/service and Infrastructure PDO adapter. Kernel owners/layers/contracts are
explicit in `architecture/policy.json`; tooling rejects undeclared owners,
cross-owner internals, Domain contract imports, layer bypasses, SQL outside
Infrastructure, executable includes and globals. Current modules cannot import
kernel private classes. Bootstrap alone composes a private connection factory.
Frozen V1-V5 and the static module/Composer/package graph remain unchanged.

`ApprovalComposition::service` wraps a trusted private connection factory. Its
service checks frozen `V1.Authorization` on resource `admin` before connecting:

| Operation | Required separate website-administrator permission |
| --- | --- |
| `inspect` | `plugins.inspect` |
| `initialize` | `plugins.provision` |
| `change` | `plugins.manage` |

No existing role receives these new permissions automatically. Current HTTP
routes do not expose the service. Player credentials, matching names and native
GM flags grant no authority. HTTP CSRF/Origin/throttle and administration grant
workflows must be added and tested at their later endpoint boundaries. Direct
Infrastructure access is an internal trusted-kernel boundary, not a public API.

Production website MSSQL is independent of optional external-game DatabaseType
plugins. The adapter accepts actual PDO_ODBC SQL Server 2016+ after its own
bounded ASCII probe, plus explicitly supplied SQLite offline fixtures. It
imports no Admin/GameData Infrastructure helper. It rejects unknown drivers,
non-exception mode, caller-owned transactions and SQLite dirty-read mode.
`sqlsrv` support is not claimed or silently substituted. No runtime credential
factory reads the private DPAPI lab record; the explicit test launcher does.

## Provisioning and commands

Reviewed installation input: `database/mssql/012-plugin-approvals.sql`.
Only explicitly designated website-owned installation/lab targets may apply it.
It refuses existing tables, seeds nothing and is never run by constructors,
reads, discovery or an ordinary command. Explicit authorized initialization
requires both tables empty, creates revision zero and records provisioner/time.
Missing/corrupt/uninitialized state is unavailable; no defaults or disk fallback.
No operator CLI or public provisioning endpoint is provided by this slice.

| Command | Payload and behavior |
| --- | --- |
| `approve` | Exact `id,publisher,version,fingerprint,permissions`. Validates canonical identities, release version, SHA-256 and unique permissions; replaces only this ID's approval and always sets `enabled=false`. |
| `enable` | Exact `id,fingerprint`; requires an existing approval with the same hash. This changes an operator-policy bit, not runtime execution authority. |
| `disable` | Exact `id`; retains the approved identity/grants and the other records. |

All commands carry server-owned administrator actor, exact expected aggregate
revision, a 32-character lowercase hex request key and bounded server time.
Request identity includes actor/action/normalized payload/expected revision;
time is excluded so retries can return their original outcome. Keys are scoped
to actor. Same key/different request conflicts. Replay checks the complete
current head/history before returning the historic revision and never overwrites
later state. New commands use a new key; even an explicit repeat approval is a
new disabled approval unless it is the same retry.

The service is an internal operator boundary. Future endpoints must construct
approval candidates from fresh server-side discovery and limit grants against
reviewed requests/policy; neither a client-provided digest nor a persisted enabled
bit can authorize PHP execution. Actual loading additionally needs present code,
unchanged full identity, compatibility, dependencies, migrations and licenses.
No removal/purge callback or deletion command exists. Folder removal does not
write the ledger, erase preferences or revoke unrelated records. Re-add must be
checked by the future host; SQL tests alone cannot establish filesystem lifecycle.

## Data, transactions and transport

One fixed head retains canonical JSON with schema/revision, provisioner/time
and a sorted list of approved package records. Limits are 128 retained IDs,
16 grants per record, 64 KiB JSON and 10,000 commands. Capacity refuses rather
than truncating or automatically archiving. Configurations, migration receipts,
installation versions, entitlements and native/game state are outside this format.
They require distinct reviewed records/commands and retention/recovery rules.

`zenda_plugin_approval_commands` is append-only to the runtime principal. It
stores actor/key/request hash, operation/plugin ID, before/result revisions,
time and before/after document digests, without source code, settings, passwords
or request payloads. Reads validate every bounded row, contiguous revisions,
unique actor/keys and the entire digest chain from the initial provisioned head
to the current document. This detects corruption under the stated permissions;
it is not independently certified tamper-proof storage against a database owner.

Each operation owns its transaction and fixed head-table lock first. SQL Server
uses table/HOLDLOCK read or exclusive write locks, including the empty
initialization case. SQLite fixtures acquire their writer boundary before reads.
Commands check bindings, execute, exactly one affected row, exact full state/audit
readback and commit. Replay checks rollback acknowledgement. Failures roll back;
cleanup exceptions and underlying transport details stay private. If rollback
cannot be acknowledged, discard the connection. A lost commit acknowledgement
returns failure; an exact durable retry resolves a committed command safely.

All persisted fields and canonical JSON are ASCII, so the website tables use
binary-collated VARCHAR/CHAR, not another owner's NVARCHAR wire implementation.
The independent ODBC profile verifies the full 64-KiB ASCII parameter and
128-KiB hex result. Head reads use validated SQL style-2 hex before exact JSON,
digest and state validation. This does not certify arbitrary Unicode/native data.

The first real SQL runs exposed a driver distinction: zero-column DML results
have no cursor to close, while PHP 7.4 PDO_ODBC forwards closeCursor directly to
SQLCloseCursor. See the [PHP driver implementation](https://github.com/php/php-src/blob/PHP-7.4/ext/pdo_odbc/odbc_stmt.c#L821).
The adapter now appends a fixed acknowledgement SELECT to each ODBC write and
requires exactly one affected row and transaction depth one. Unexpected trigger
rowsets or absent/mismatched acknowledgements refuse. Rowset cursor releases
remain checked, followed by exact readback and checked commit; no raw false
return is accepted as proof of a successful mutation.

Runtime grants: SELECT/UPDATE on head; SELECT/INSERT on commands. Provisioning
requires a separate SELECT/INSERT head principal and SELECT on commands; DDL
authority is separate. Runtime needs neither head INSERT/DELETE nor audit
UPDATE/DELETE. The aggregate serializes only website policy, never an active
game server or payment fulfillment.

## Evidence and pending boundaries

`tests/plugin-approvals.php` covers architecture/owner rules, lazy authorization,
normalization/capacity, revisions/replay/identity replacement, typed corruption,
transaction/acknowledgement faults, triggers and independent SQLite processes.
`tests/plugin-approval-worker.php` is CLI-only and accepts only marked fresh
SQLite fixtures or ownership-verified SQL lab attachments. Test schema helpers
never load into the website.

The separately opt-in `scripts/test-mssql-odbc.ps1 -RunOwnedFixture` applies DDL
012 only inside its fresh marked database. Actual SQL cases cover the full ASCII
wire, initialization/change/retry process races, runtime grants, audit-denial
rollback, trigger changes/unexpected results, earlier-history corruption, lock
timeouts, large valid stored documents and ambiguous real commits. Its existing
41 HTTP cases still concern administrator/core/operational settings, **not plugin
approval endpoints**. The fixture alone trusts the local test certificate;
strict deployment TLS, backup/restore and whole-site cutover remain unverified.

Decision 106 adds independent configuration and unverified migration-attempt
receipts through a shared fixed-family journal transport; see
[metadata storage](PLUGIN_METADATA_STORAGE.md). Approval schema v1 and its
boundary remain intact. Step 03 still needs verified installed migration state,
a reviewed executor and explicit guarded operator provisioning. Steps 04-06 still need executable host, HTTP/worker removal proofs
and the administration lifecycle UI. No native game or payment gate changes.
