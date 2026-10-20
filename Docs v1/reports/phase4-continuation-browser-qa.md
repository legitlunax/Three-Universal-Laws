# Phase 4 continuation browser QA — 2026-10-18

The optional tests/browser-fixture.php harness created a new private fixture,
synthetic website/staff accounts and a loopback server. No personal/default state,
old rejected-cleanup fixture, native MU connection or legacy execution was used.

Verified through actual forms:

- Player sign-in, session-specific support creation, persisted conversation.
- Independent staff sign-in, ticket access, staff reply; player conversation masks
  the staff identity and preserves the reply.
- Owner close/reopen; revision 4 shows awaiting-staff and original history intact.
- Staff drop-guide creation at 1.25%, public search; edit to 2.5% appears publicly.
- Synthetic Castle Siege cycle, current UTC stage, history and Kayito unsupported state.
- Independent sign-out: player logout leaves staff access intact, then staff logout
  revokes staff inbox access.

At 1440×1000 and 390×844, all 13 checked routes had no horizontal overflow and no
inline style attributes: home, dashboard, player/staff support, public/admin drops,
public/admin Siege, webshop, VIP, guilds, events, template-editor presentation.
Final console warning/error collection across the session was empty. Existing
dashboard/editor overflow found during QA was fixed by wrapping diagnostics and
responsive grid/header rules; static view styling was moved to external CSS.

Screenshots inspected: support-player-desktop.png, support-staff-mobile.png,
drops-mobile.png; additional captures: siege-mobile.png, dashboard-mobile.png.
This is tool-assisted manual QA, not a committed automated visual regression suite
or authentic GrapesJS round-trip evidence. The final shop preview terminology was
verified by updated unit/view/HTTP tests after this browser session.

Browser tab closed. Sending stop to the fixture harness stopped its owned server
and removed the private fixture directory; subsequent Test-Path returned false.
Earlier rejected-cleanup residue documented in STATE.md remains untouched.
