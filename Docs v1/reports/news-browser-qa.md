# Manual announcement browser QA - 2026-10-18

Owned synthetic harness on loopback port 55542. Prepared 25 synthetic public
announcements in zenda-browser-fixture-0e2a8e26870b8e1b31d6d149 only.

- Archive rendered 20 items, accurate total and page navigation.
- Actual Next link returned five items on page 2 of 2.
- Title search returned one result; no-match query rendered its empty state.
- Searching literal markup and opening the article preserved escaped title/text.
- Article retained the full long unbroken string, Unicode and second line;
  zero script elements and no horizontal overflow.
- Homepage rendered five linked announcements and the archive link.
- Archive checked at 1440x1000 and 390x844: no horizontal overflow or inline styles.
- Top-of-page desktop/mobile and full mobile article screenshots visually inspected:
  news-archive-top-1440.png, news-archive-top-390.png, news-article-mobile.png.
- Earlier viewport captures news-archive-1440.png/news-archive-390.png show scrolled
  content; top screenshots also cover header/search/pagination.
- Final console: zero warnings/errors. Browser tab closed.
- Harness received stop and reported server shutdown/private fixture removal.

Manual QA is not a committed visual regression suite or native MU integration.
