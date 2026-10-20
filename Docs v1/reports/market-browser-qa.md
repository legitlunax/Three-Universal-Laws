# Milestone 6 browser QA — 2026-10-18

Owned loopback PHP server only; private synthetic credentials/state in a new
temporary directory. No legacy access or native MU connection.

- Desktop 1440×1000: administrator sign-in, item draft creation at 101 Zen;
  persisted preview displays fee 6 and seller proceeds 95.
- Mobile 390×844: character draft creation at 500 WCoinC; fee 25, proceeds 475;
  read-only transfer requirements report blocked/unreserved; AdminCP moderation
  persists cancellation and adds the market journal event.
- Owner cancellation removes the item preview; both character and item markets
  return to empty. Sign-out restores anonymous view without draft selectors.
- Both widths: no horizontal overflow on `/market`, `/market/characters`,
  `/admin/market`, `/`, `/shop`, `/vip`, `/guilds`.
- Visual inspection: desktop market and mobile manager PNGs inspected. Market
  pages have no final-navigation console warnings/errors or inline style attributes.
- Browser exposed an existing missing `}` in the max-width:480px media query;
  fixed it so later styles also apply on desktop. Existing unrelated dashboard,
  shop/VIP/guild inline styles still generate CSP errors; CSP was not weakened.

Screenshots: `market-desktop.png`, `market-mobile.png`,
`market-manager-mobile.png`. This is tool-assisted browser QA, not a committed
automated visual regression suite.

The browser tab and owned server are stopped. Automatic approval review rejected
both recursive and individually scoped deletion of private synthetic QA state,
reason: `blocked by policy`. Residue remains at
`C:/Users/gonza/AppData/Local/Temp/zenda-market-browser-37183ecc016e45c88ba4c2ae41a8e20d`.
Do not reuse these fixtures as personal sandbox credentials or start a server
using them. Routine automated suite fixtures cleaned up normally.
