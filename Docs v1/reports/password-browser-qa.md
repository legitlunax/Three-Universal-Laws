# Manual website-password QA ? 2026-10-18

Owned synthetic harness: tests/browser-fixture.php, loopback port 51408.
No personal state, native MU account, legacy file or external service used.

- Player login and account navigation reached /account/password.
- Wrong current password produced 422 and three empty password fields.
- Valid change redirected to /login?notice=password-changed and displayed the notice.
- New-password sign-in succeeded; owner history displayed Revision 1 and UTC time.
- 1440x1000 and 390x844 form screenshots were visually inspected; no horizontal overflow.
- Final mobile DOM had zero inline styles/scripts; final console had zero warnings/errors.
- The deliberate invalid submission produced the expected 422 resource message.
- An intermediate DOM count immediately after screenshot reported six transient style
  attributes; a fresh error/history document had none. No application inline styles
  or scripts were present in the inspected final documents. Cause not established.
- Desktop/mobile/history screenshots: password-1440.png, password-390.png,
  password-history-mobile.png; all visually inspected.
- Browser tab closed. Harness received stop and reported server shutdown/state removal.
- Test-Path for zenda-browser-fixture-154d255cbce6923fdca194f2 returned False.

This is manual QA, not a committed browser regression suite or production integration.
