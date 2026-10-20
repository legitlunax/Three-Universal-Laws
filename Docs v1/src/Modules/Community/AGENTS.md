# Community boundary

Read root STATE.md and AGENTS.md first. Current runtime Community work is private
website support, with no native game authority. Bootstrap supplies Authorization
and composes the module-local SupportStore. Do not import Identity internals here.

SupportService checks support.use for the server-authenticated owner and
support.manage for staff. Store commands are trusted application inputs, never
raw HTTP objects. Preserve ownership checks under the lock, expected revisions,
scoped replay identity and atomic state/audit persistence. A player role or
submitted staff flag cannot grant moderation authority.

Ticket text stays private and is escaped on output. Players never receive staff
actor IDs; staff replies use the Support team label. Audit projections omit ticket
subjects and messages. Preserve bounded categories, text, conversations, active
ticket counts and state size. Corrupt storage must not reset to empty defaults.

Search and pagination must operate on authorized summaries before counting or
slicing. Do not search private message bodies or expose other owners' totals.
Retain deterministic update-time/ID ordering, bounded pages and escaped filter
links. Invalid query parameters must not silently widen the inbox. These reads
have no mutation/audit authority; preserve existing listTickets consumers.

Canonical website owner IDs use signup's 4–10 ASCII letters/digits/_/- grammar,
lowercased after website authentication. Retain exact owner/message-author matches
and the distinct sandbox staff format. Older alphanumeric-only readers reject new
punctuated owner histories; preserve compatible readers or reconcile before rollback.

Run tests/support.php, tests/playeridentity.php and HTTP coverage via the central
verification runner; test concurrency with owned temporary fixtures only. The
file adapter is not proof of production SQL durability or retention controls.
