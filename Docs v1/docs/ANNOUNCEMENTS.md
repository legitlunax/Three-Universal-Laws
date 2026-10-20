# Public website announcements

Drupal is now the selected news authoring backend. The public read behavior below
is shared by local and Drupal adapters; the local CRUD/audit discussion applies
only to the older offline adapter. Drupal mode uses UUIDs, converts processed
body markup to plaintext and delegates editing/publication/revisions to Drupal.
See [Drupal setup and limits](DRUPAL_NEWS.md). Local atomic news commands are
deferred in favor of this integration. No phpBB or Superdesk integration exists.

`GET /news` lists published website announcements with literal, Unicode-caseless
title/body search and 20-item pages. Optional `q` is at most 120 Unicode characters
(480 bytes); invalid UTF-8 and controls are rejected before trimming outer spaces.
Optional `page` is a canonical decimal from 1 to 999999. Missing/empty values use
the default query/page; arrays, overlong values and malformed numbers return 422.
Search precedes totals and slicing. Ordering is newest UTC publication first,
then ascending stable ID for ties. Stale pages clamp to the last available page;
empty results remain page 1 of 1. Navigation preserves the encoded query.

Archive entries contain exactly ID, title, a UTF-8-safe 240-character body excerpt
(plus an ellipsis when truncated) and publication time. Search uses the complete
public article body. Publisher identity and internal fields are absent.
`GET /news/article?id=...` provides the complete plaintext article through a
separate exact allowlist. Existing 1–64-character identifiers containing ASCII
letters/digits/dot/underscore/hyphen and starting with a letter/digit remain valid.
Malformed/missing IDs return 422; unavailable/deleted articles return a redacted
404. Both endpoints are GET-only. HTML/title/attributes/links are escaped and
line breaks are preserved; article markup never becomes executable content.

The homepage shows five announcements with article links and a full-archive link.
The public navigation now targets `/news`; the existing homepage `#news` anchor
remains. Existing administrative publish/edit/delete actions share the same
storage, so edits retain article URLs and deletions remove search/detail output.
No archive or detail read changes announcement state. Existing session and
maintenance handling, private/no-store headers and strict CSP remain unchanged.

Existing staff publish/edit/delete POSTs now share a persistent `announcements`
limit of 30 attempts per client and scoped actor within 900 seconds, after
server authorization/CSRF and the common Origin check. Invalid authorized
submissions consume the same budget; switching routes cannot evade it. Editor
and public reads remain available. This closes a missing HTTP throttle on the
older mutation paths; it does not add revision/replay protection or make the
existing separate audit append atomic with news writes. Those mutation/audit
improvements remain pending.

## Boundaries and compatibility

NewsService adds archive and publishedArticle queries; ContentController forwards
them and Bootstrap composes/routes them. Existing news/findById/publicData/admin
methods keep signatures and full-body behavior. Only homepage rendering limits
its list. NewsRepository now extends an internal read-only port; its methods,
persistence formats, public V1 contracts, template
component properties and frozen fixtures are unchanged. No database migration,
native-game binding or GrapesJS editing functionality is introduced.

The new service reads validate stored public fields, text limits and canonical
UTC timestamps before projection; malformed state is unavailable rather than
partially exposed or silently skipped. The file adapter already validates the
entire bounded snapshot (1000 rows/16 MiB). Reads of seed-only file content do
not create news state. SQLite fixtures exercise the PDO adapter as well.
Pagination/search currently load the published set and operate in memory;
database-side pagination/indexing, production driver/performance verification,
full legacy route aliases, localized search and publication drafts remain future
work. These pages use the current published-only news model, with no scheduling
or draft-publication lifecycle added.

## Evidence

`tests/news.php` covers both existing adapters, ordering/ties, traversal, search
normalization/literals/Unicode, counts, clamping, projections, excerpts, malformed
queries/SQL fields, empty/corrupt state, read-only bytes and unchanged consumers.
Mounted/hostile view models exercise escaped document content and links.
`tests/http.php` follows actual pagination URLs and checks anonymous reads,
malformed query values, methods, content redaction, persisted state, edits and
deletions through the real server. Existing authorization/CSRF mutation coverage
remains in place. Manual browser QA covers desktop/mobile, pagination/search,
empty states, long article text and the five-entry homepage. See STATE.md for
actual checks; no production data, legacy runtime or external assets are used.
