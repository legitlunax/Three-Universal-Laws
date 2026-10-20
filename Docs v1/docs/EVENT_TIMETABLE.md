# Website event timetable

Decision 103 adds GameData-owned verified PDO_ODBC and owned DDL 003/store/lock/
grant evidence. Initialization, writes and replay check acknowledgements and exact
readback; cleanup errors stay private. Ports, v1 history and calendar behavior are
unchanged. Guide-specific SQL HTTP and trusted TLS remain pending. See GUIDE_ODBC.md.

`GET /events` reads persistent website configuration. `GET /admin/events` is the
staff editor; `POST /admin/events/save` and `/admin/events/delete` require the
server-owned `events.manage` permission, Origin, CSRF and a persistent limiter
(30 attempts per 15 minutes). Website-player sessions grant no staff authority.
An identifier updates that existing schedule; identifiers are stable, start with
a lowercase letter and contain only lowercase letters, digits and hyphens.

The editor supports intervals anchored to UTC midnight, an initial offset smaller
than the interval, or up to 24 unique fixed HH:MM times. Weekdays are optional
(0 Sunday through 6 Saturday); durations are 1–1440 minutes. Names are bounded to
64 UTF-8 bytes and descriptions to 2000. Times/days are sorted canonically.
Timing-mode changes require clearing unused fixed times when choosing intervals.
Calculated statuses describe timetable windows, not observed native events.

The internal `EventScheduleStore` extends the existing read port, with unchanged
signatures/results. In database settings mode, Bootstrap binds the new module-owned
PdoEventScheduleStore for the public timetable, calendar and staff editor. Missing
schema/seed/corrupt state returns a redacted 503 with no file/default fallback.
The old standalone file mode alone uses FileEventScheduleStore. SQL Server is
prepared, not exercised: this host has no pdo_sqlsrv or designated SQL target.

Both adapters share pure EventTimetableState rules and preserve the existing
state semantics. SQL adapters for events/loot reuse GameData's fixed-identity
PdoGuideDocument transaction engine; WebsiteGuideImport shares bounded locked
source reads without crossing module boundaries. They retain separate tables and
histories. Event storage preserves the existing
schema-version-1 document, initial definitions, full command history, request
fingerprints, revisions, results and audit projection. Its file adapter
uses private `event-schedules.json` and a stable `event-schedules.lock`, sharing
one snapshot for definitions, replay results and the minimal audit projection.
Each accepted command requires the expected global revision. An actor-scoped key
replays the original result; changed operation/payload/revision with that key
conflicts. Deleted schedules cannot be resurrected by replaying old saves.
There are at most 100 schedules, 1000 commands and eight MiB per snapshot.
Archive/retention tooling is pending; capacity exhaustion refuses new commands.

In the old standalone file prototype, before the first save, reads use the nine existing synthetic examples without
writing a data file. The first change freezes those initial definitions into
the schema-version-1 snapshot, so later code/default changes cannot rewrite its
history. Replay validation reconstructs current state from that initial snapshot
and journal; malformed/corrupt/divergent state fails closed and is preserved.
Data-file checks occur under the stable lock. Audit is immutable through the API,
not independently certified tamper-proof storage. Windows ACLs, power-loss
durability and production database migration/retention remain unverified.

`EventSchedule::definition/fromDefinition` are module-internal canonical helpers.
The computed projection adds offset/times/weekdays; existing rendering consumers
retain their previous fields and fallbacks. V1 contracts and component/property
baselines are unchanged. No existing data format is rewritten. Rolling back to
older code hides the new configuration because older readers ignore this file;
preserve it for restoring the new reader. No production rollout/rollback ran.

## Calendar download

`GET /events/calendar` downloads `zenda-events.ics`. `?days=1` includes today only;
the default is seven UTC days including today, with a maximum of fourteen. Query
values must be canonical integers; malformed/array inputs are rejected rather
than silently defaulted. The request helper's new strict option is opt-in;
existing tolerant query consumers retain their behavior.

Downloads use explicit starts within a half-open UTC window, including weekday
filters and daily interval resets. They refuse more than 2000 occurrences or four
MiB, without truncation; use fewer days for dense schedules. Empty windows return
an explanatory 404 rather than a component-free calendar. Each occurrence has
a site-origin/schedule/start-derived stable UID and the snapshot revision.
Calendar text uses escaped separators/newlines, CRLF and UTF-8-safe folding at
75 octets, following [RFC 5545](https://www.rfc-editor.org/rfc/rfc5545).
Entries are tentative and transparent; no alarms, invitations, attachments,
external URLs or private journal/actor data are included.

This is a committed website snapshot from the selected store. Calendar-client import/update/cancellation behavior and
remote subscriptions are unverified; later website edits/removals do not update
previous downloads automatically. Changing the site origin changes UID namespace.
The extensionless route avoids development-server dotted-path normalization;
the download filename still has the standard `.ics` extension.

These workflows never edit MU server files, execute events, synchronize Castle
Siege cycles, reserve game resources or send rewards. Native scheduling adapters
remain missing. See STATE.md for current verification and next authorized work.

## SQL representation and explicit migration

Review database/mssql/003-event-timetable.sql for a designated website-owned SQL
Server 2016+ database. It creates dbo.zenda_event_timetable only, with a singleton,
schema version, revision, state_json and SHA-256. It never touches game scheduling
or the core/operational settings tables. The canonical eight-MiB document keeps
initial/current definitions and replay/history together; SQL revision must equal
the journal length. The digest detects accidental corruption, not privileged
tampering, and historical scopes/digests retain their original format. History is
not independently tamper-proof; archive/retention remains absent.

Runtime requires SELECT/UPDATE, without DDL/DELETE; explicit initialization uses
a separate INSERT permission. Constructors/requests perform no DDL or seeding.
Mutations own a transaction, reject nesting, lock/read/recheck and use a revision
CAS before committing the complete document. SQL Server uses UPDLOCK/HOLDLOCK;
the separately tested SQLite writer-lock path does not certify SQL Server behavior.
An exact retry returns its original result without rewriting later state, including
after deletion or an unacknowledged committed command. No transaction spans event,
core or operational records. Event failures leave unrelated settings routes usable.

The importer accepts only an existing event-schedules.json and stable lock in the
explicitly selected new sandbox outside webroots/legacy. It locks, bounds and
validates the complete document, including history reconstruction; it never fills
missing data with current example schedules. For an unconfigured installation,
prepare an explicit canonical initial document and lock as operator provisioning
input. This is not live file configuration in database mode.

```powershell
& 'C:/xampp/php/php.exe' scripts/import-event-timetable.php
```

ZENDA_SANDBOX_DIR selects the existing source. The default dry run connects to no
database, prints no event/actor values and emits a canonical SHA-256. Apply requires
ZENDA_SETTINGS_MIGRATION_SCOPE=owned-nonproduction, ZENDA_SETTINGS_BACKEND=mssql,
private settings connection inputs and --apply --expected-sha256=<reviewed-digest>.
It inserts into an installed empty target, retains the source revision/history and
refuses replacement. Freeze edits, back up and compare source/target privately
before cutover. Never reopen stale file writers or older readers as fallback after
SQL edits. Owned local SQL guide tests now run under 103; production migration/
restore and trusted deployment remain unverified.

tests/event-settings.php verifies owned SQLite schema/state, history-preserving
imports, authorization consumers, corruption refusal, rollback/CAS, ambiguous
commit recovery, capacity/byte limits and independent-worker revision/retry races.
The existing event/file/calendar tests remain unchanged in behavior. Shared HTTP
fixtures verify all three SQL consumers, CSRF/Origin/staff boundaries, edits/
deletes/replays, escaped public data, calendar MIME/privacy, throttle and no file
fallback. No new visual layout or browser regression suite was introduced here.
