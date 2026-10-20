# Content boundary

The selected news backend is Drupal; see docs/DRUPAL_NEWS.md. Bootstrap's explicit
Drupal mode binds an anonymous PublishedNewsReader and denies local news writes.
The old local repository remains an offline compatibility adapter.
Database mode selects its connection through Admin's
operational record, composed only in Bootstrap; news environment variables are
standalone file-prototype inputs only. See docs/OPERATIONAL_CONFIGURATION.md.
Drupal owns publication/revisions; do not add a second local authoring/history system for
Drupal articles. All website configuration must ultimately live in MSSQL; file
and SQLite settings are development prototypes, and files may hold cache only.

Read root STATE.md and AGENTS.md. Public content and local administrative
announcements are implemented; full content management and visual templates are
not. Keep canonical template JSON/publication work in its own future service.

NewsService must enforce content.manage via the injected Authorization port
before every publish operation, including future CLI/API callers. The controller
cannot be the only authorization boundary. HTTP Origin and CSRF checks occur in
the request pipeline. Actor identity always comes from the server session.

Titles/bodies are bounded UTF-8 plaintext. Views escape every value and preserve
line breaks without interpreting HTML. Public projections omit publisher metadata.
Use the owning repository's stable flock + atomic replacement; corrupt data must
fail closed and remain recoverable. Private state is outside all webroots. This
file prototype is not production database/audit integration. Preserve concurrent
write, authorization, encoding, escaping and actual HTTP tests when evolving it.

Public archive/detail use explicit allowlists and validate stored text/dates.
Search only published title/body, before totals/pagination, with stable UTC/ID
ordering and bounded Unicode excerpts. Preserve strict query errors, encoded
page/article links and read-only content state. news/publicData/admin consumers
retain full-body lists; only the homepage view limits its list to five. Do not
turn findById's old nullable behavior into the new route's strict input policy.
See docs/ANNOUNCEMENTS.md and run news/content/views/HTTP compatibility checks.

Bootstrap's staff news POSTs share an announcements limiter after authorization/
CSRF. Retain cross-route client/actor budgeting and read independence. Existing
news writes still lack revision/replay and same-commit audit; do not describe
the separate audit append as atomic or refresh frozen baselines to add it.
