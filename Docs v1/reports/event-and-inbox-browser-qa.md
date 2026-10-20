# Manual QA: website timetable, calendar and support inbox

Owned `tests/browser-fixture.php` loopback fixture only, with synthetic identities.
No default/personal state, legacy runtime, native game data or external service.

- Staff login; creation of a fixed-time weekday schedule; public canonical UTC
  output; edit to a 100-minute day-anchored interval; removal; journal observation.
- Browser identifier validation accepts valid IDs and rejects invalid ones.
- Actual link download succeeded as `zenda-events.ics` with 402 default-example
  occurrences and complete CRLF envelope (171866 bytes at the observed revision).
- Player login and private ticket creation; subject filter; actual next-page link
  over 22 closed owned fixtures; two remaining records; conversation link retains
  category/status/query/page. Staff filtered inbox shows the authorized same view.
- Event/public/editor routes: three at each of 1440x1000 and 390x844; no horizontal
  overflow or inline style attributes. Support list/details in player/staff modes:
  four routes at each width, likewise no horizontal overflow or inline styles.
- Final fresh browser session warnings/errors: zero. Screenshots were visually
  inspected, including events-editor-mobile.png and support-pagination-mobile.png.

Early QA found an invalid HTML pattern, dotted-route behavior in the PHP development
server and query parameters silently falling back. These were corrected and the
relevant browser/HTTP checks rerun. A temporary QA seeding helper initially called
the wrong account lookup method; it failed before writes, was corrected and then
seeded only the exact owned fixture. These were tool-assisted manual checks, not
an automated browser regression suite or calendar-client import certification.

Canonical final verification and fixture-cleanup evidence belong in STATE.md.
