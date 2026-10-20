# Migration and reconstruction procedure

The inspected runtime consumer/migration map is
[docs/PLUGIN_RUNTIME_MIGRATION.md](docs/PLUGIN_RUNTIME_MIGRATION.md).
Decision 104 implements only data-only distribution discovery. No installed
format uses the earlier illustrative manifests; reject those instead of silently
converting. Existing static bindings, package paths and data remain unchanged.

## Migration to a folder-discovered plugin runtime (not executed)

Migrate in *strangler* stages instead of replacing the current working
`src/Modules`/`packages/premium` bootstrap in one unsafe move. First implement
trusted `PluginAPI` and loader with an empty-plugin boot test. Next extract a
simple read-only first-party feature into its own `/plugins/<id>/` directory
without losing routes/contracts/data. Prove physical add/remove/readd in a live
HTTP sandbox. Only then incrementally extract other features using explicit
compatibility bridges; move premium packages to their own independently shipped
plugin folders and retain core/free functionality when premium code is absent.

Migration plans must preserve published V1-V5 contracts, player/admin sessions,
MSSQL configuration and native game/payment gates. Data stays in website-owned
MSSQL on removal; *uninstall with purge* is a distinct authorized action. No
production SQL, game writes, legacy runtime execution, physical moves or migration
have been performed by this Markdown update. See `PLUGIN_SYSTEM.md`.

Website player-account import now validates the complete private source under its
stable shared account lock, then owns one empty-target transaction for accounts,
password replay/history and reset rows. Invalid present recovery storage cannot
be silently skipped. Matching consumed file-token markers become SQL used=1 so
cutover cannot revive old recovery links. Re-run the guarded dry run and preserve
source edits/freeze and credential privacy. See docs/PLAYER_ACCOUNT_STORAGE.md;
no actual SQL Server cutover occurred.

Template storage now imports a complete validated source collection in one owning
transaction, replacing the inherited per-revision loop. Heads insert before their
FK-constrained revisions; all historical envelopes and draft/current/previous
publication references are retained. Re-run the guarded dry run after the stricter
source/metadata checks and preserve the source edit freeze. SQL null expected
draft revisions are create-only, so callers must migrate existing edits to exact
expected revisions. Active SQL publication remains disabled. See
docs/TEMPLATE_STORAGE.md; no actual SQL Server migration ran.

Core website configuration now has explicit MSSQL installation DDL and a guarded,
default-dry-run import tool for this project's five-setting file prototype. Only
owned SQLite fixture imports/transactions have been exercised; no SQL Server DDL
or migration has run. See docs/WEBSITE_CONFIGURATION.md. The general migration
procedure below remains pending for real data and the other configuration families.

News/runtime policy adds a separate version-2 record and explicit installation
input 002-operational-settings.sql, preserving the core version-1 tables. Its
source-preserving provisioning importer defaults to a redacted fingerprinted dry
run. Both records must be initialized before database-mode cutover. No combined
transaction spans their histories. See docs/OPERATIONAL_CONFIGURATION.md for
strict policy, apply guards and remaining SQL Server evidence; no actual SQL ran.
Event timetable installation input 003 and import-event-timetable.php retain the
existing v1 initial/current definitions, full history and source revision. The
default dry run and designated-nonproduction/fingerprint guards remain mandatory;
an empty target is initialized once without overwriting source. SQL event readers
never fall back to the stale file after cutover. See docs/EVENT_TIMETABLE.md.
Loot-guide installation input 004 and import-loot-guide.php similarly retain
generated IDs and full replay history. Source-only configuration-preflight.php
checks all four explicit documents/guide locks and emits redacted hashes, versions,
target initial revisions and installation/import paths. It opens no database,
accepts no apply arguments and fails all-or-nothing. Freeze source edits; separate
reader locks do not create a combined atomic snapshot. No new apply authorization
or actual SQL Server validation follows from a successful source plan.

The user approved continuation after the Phase 1–3 review on 2026-10-17. A local phase-4 website/administrator/news slice is implemented; see STATE.md. Real data exporters/importers, SQL migrations, profile activation, asset copying, deployment and rollback rehearsal are **not implemented or executed**. The migration fixture adapter verifies a synthetic version transition; it does not migrate real accounts, website records or saved production GrapesJS projects.

Use the [functional inventory and eight extraction artifacts](docs/discovery/FUNCTIONAL_INVENTORY.md), [feature migration matrix](docs/discovery/MIGRATION_MATRIX.md), [data and integration evidence](docs/discovery/DATA_AND_INTEGRATIONS.md), [contracts](CONTRACTS.md), workspace configuration and [STATE.md](STATE.md) before each migration step. The matrix preserves the discovery baseline; STATE records current implementation. Continuation approval does not authorize production database changes.

## Scope and ownership

Legacy at `C:/xampp/htdocs/ZendaMod` remains read-only. New runtime files must be independently deployable from `ZendaMod-Next` without requires, symlinks or runtime source paths into legacy. Preserve necessary public routes/statuses/body shapes through explicit adapters, and use the contract evolution workflow for intentional breaking changes. Source filename parity is not feature parity.

Keep native game/account data at its authoritative owner. Website-owned content/configuration/histories/claims/orders can be imported only from an explicit allowlisted export or separately authorized read-only source. Do not copy installed configuration, credentials, player records into tests, sessions, caches, locks, operational logs, private history, license payloads, keys, generated archives or reference-vendor projects. Re-enter secrets through the new secret configuration mechanism.

Assets require a curated path/hash/MIME/size/rights/attribution manifest before reuse. Preserve applicable WebEngine MIT notices; do not infer that notice licenses every game image, font, commercial theme or nested reference package. PHP templates and plugin source are executable code, never visual-template import data.

## Future staged procedure

1. Establish the approved target environment, source export scope, active profile/vendor build, identity/password mappings, website-owned schema inventory, native synchronization protocol, secret handling, asset rights, downtime/freeze and rollback owners. Unknown critical schema or write capability blocks affected operations.
2. Capture read-only metadata/export fingerprints and provenance without player secrets in reports. Create clean schema-known defaults and map source IDs/references to target IDs. Keep configuration migration, additive website schema installation and operation activation separate.
3. Implement deterministic, versioned importers with dry-run as the default. Validate types, limits, ownership, foreign keys, timestamps, locale links, amounts, idempotency keys, provider transaction references, codec/layout versions and asset safety. Quarantine ambiguous records; never guess a native mapping.
4. Use synthetic fixtures first. Reconcile counts, key uniqueness, relationships and balances/awards/order/delivery states. For money/market/Workshop histories, preserve original destinations, amounts, mapping fingerprints, outcomes and undelivered payloads. A replay of the same import manifest must not duplicate records, rewards or purchases.
5. Validate the actual MSSQL adapters, schemas, locks, concurrent native writers, serial allocator and offline guards in the separately designated non-production environment described in [DATABASE.md](DATABASE.md). Provider sandboxes and callback contract tests are distinct from database tests. No production write follows merely from a successful read.
6. Produce a reviewable dry-run report with proposed changes, unresolved rows, required capabilities, source/target fingerprints and a recoverable backup/rollback plan. A real import must use a frozen export and tracked manifest under the explicitly approved operational scope.
7. Rehearse restoration in isolation. Verify route parity, rendered content, permissions, payment acknowledgment/retry, claims, native ownership, queues and published-only template access. Do not enable a feature whose acceptance evidence is incomplete.
8. Activate controlled capabilities and publish/deploy only under the user's explicit operational instruction. Preserve the prior deployable artifact, website data backup, previous published templates and import manifest. Monitor secret-free diagnostics and reconcile unfinished transactions before reopening writers.

## Website profile compatibility

Website profile publication now requires explicit owner consent, including for
previously registered accounts. Missing preferences mean Private; old public
readers/helpers must not be restored without retaining this policy. The new file
requires no account-row rewrite and imports no consent. Website identifiers now
match accepted signup syntax across sessions/profiles/support; older support
validators reject new punctuated owner histories. Keep compatible readers or
reconcile before rollback; never drop history to make validation pass. No production
rollout/rollback ran. See [profile compatibility](docs/WEBSITE_PROFILES.md).

## Website credential compatibility

Authenticated password changes add optional private `passwordChanges` to current
version-1 account snapshots and a website-only PDO `zenda_password_changes` table.
Keep history/replay evidence and consumed-token markers with the latest committed
credentials; never restore old hashes independently. No account data rewrite,
production schema change or real token/session revocation ran. The
[website rollback proposal](docs/WEBSITE_ROLLBACK.md) defines privacy, identifier,
token and session requirements; freeze/drain, reconciliation and restoration
tooling/rehearsal remain pending. It is not a runnable certified rollback.

## Visual project compatibility

GrapesJS canonical project JSON needs stable component identifiers/versions, required capabilities, asset references, schema versions and retained revisions. Future project migrations must preserve the editable source and prior revision, validate before/after structure and safe rendering, and provide a restore path. A draft save cannot publish. A publish cannot bypass capability, permission, security or compatibility validation. Preview must use simulated or authorized side-effect-free read data.

The synthetic migration fixture's success demonstrates only its specific test cases. Real project migration, visual regression, real template persistence and editor/server integration remain Phase 7+ work and must receive separate evidence.

## Reporting and handoff

Update [STATE.md](STATE.md) last with implementation status, import/schema/contract versions, exact executed checks, pending operations and unresolved blockers. Distinguish implemented/verified, implemented/unverified, partial, unimplemented and unsupported. Do not report successful discovery or fixture validation as a completed functional migration, safe game-server write, production payment integration or deployment.
