# Website identity and privacy rollback proposal

This is a procedure for a future separately authorized operational rehearsal.
No deploy, production backup/restore, token revocation, account rewrite or real
data migration has run. Its acceptance checks are requirements, not executed
evidence. Prefer a compatible forward fix. An old artifact is unsuitable if it
cannot retain private profile defaults, registered identifiers, token consumption
and current credential history.

1. Identify the exact target artifact, website-owned storage and operator, and
   freeze website identity/profile/support writers, recovery issuance/confirmation
   and all public profile readers at the serving layer. Maintenance UI alone is
   insufficient: logout/recovery/background jobs and other workers must be
   accounted for. Stop/drain owned workers so PHP session files and cross-worker
   account/profile locks cannot outlive the snapshot. Do not stop or edit the
   legacy site or game server as part of this website procedure.
2. Preserve one consistent private website snapshot plus the current application
   artifact. For files, include accounts, reset records, password/profile/support
   histories, configuration and replay metadata together. For PDO, use one
   consistent backup containing accounts, resets and password history. Keep this
   evidence outside source/webroots with approved access/retention controls;
   do not copy it into developer tests, reports or the legacy tree. Record only
   nonsecret artifact/schema/count summaries in the operational report.
3. Inventory compatibility using the current trusted readers while writers stay
   frozen. Count optional consumed-token markers and password histories,
   profile consent records and punctuated account/support owners. Do not export
   credentials, emails, token hashes, profile text or private messages in reports.
   Unknown/corrupt state blocks rollback; never reset a rejected file to empty.
4. Retain the current privacy-aware `/player` route and public helper policy.
   If the target cannot run them, disable **all** website-profile public readers,
   aliases, APIs, template projections and caches before it serves traffic.
   Confirm private/missing/banned profiles remain indistinguishable and native
   fixture fallback cannot disclose a matching website account. Do not generate
   consent or mark profiles public to fit an old reader.
5. Retain readers/writers for 4–10-character lowercase website owners containing
   letters, digits, underscores and hyphens. Never rename accounts or discard
   support/profile journals to fit an old validator. If compatibility cannot be
   preserved, keep affected workflows disabled and the original histories intact.
6. Keep credential and history data at the latest committed state. Do not restore
   old password hashes from a pre-change backup into active service. Before any
   writer that ignores consumed-token markers is allowed to operate, reconcile
   **every** outstanding website recovery token under the owning account lock or
   database transaction and invalidate it durably. The target must read that
   invalidation; otherwise disable recovery issuance and confirmation entirely.
   Preserve original private audit evidence and record a minimal operational
   reconciliation event. This revocation/reconciliation tool is not implemented;
   do not edit JSON/SQL manually as a substitute or remove markers/history.
7. Revoke all website-player sessions before reopening identity writers so old
   credentials cannot retain authority. Preserve the separate staff realm unless
   its own artifact compatibility requires an authorized revocation. Session
   invalidation must cover all workers/stores, not only the current browser.
   Dedicated operational all-session revocation tooling is not implemented.
8. Rehearse the chosen artifact and compatible readers in a separately designated
   isolated environment using synthetic copies first. Verify private defaults,
   public opt-in/revocation, native-name collisions, underscore/hyphen ownership,
   banned accounts, old/reset/new password behavior, single-use token denial,
   stale sessions, journal/replay preservation and negative CSRF/Origin/throttle
   cases. Restore the rehearsal snapshot and verify recovery from a failed
   rollback. Require evidence for the actual driver/filesystem and serving layer.
9. Reopen only the verified compatible workflows under explicit operational
   authorization. Retain disabled endpoints/aliases and original histories until
   a reviewed migration provides compatibility. If checks fail, keep writers
   frozen and return to the current compatible artifact and its consistent state;
   do not overwrite newer committed data or permit both versions to write.

Current automated suites verify component behaviors, not these operational
steps. Deployment routing, backups, global freeze/drain, all-session revocation,
token reconciliation, retention and restoration tooling remain missing. This
document removes ambiguity about the intended rollback boundary; it does not
certify a runnable rollback or make old writers safe.
