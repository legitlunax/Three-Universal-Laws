# Canonical project and publishing design

## Template dependency on removable plugins (planned)

Filesystem plugin installation and GrapesJS canvas drag-and-drop are separate.
Each approved plugin may contribute its own namespaced, versioned visual blocks
through `PluginAPI`; the free core GrapesJS builder itself must run without any
optional plugin. When a plugin folder disappears, its block is removed from the
editor palette immediately on next request, but saved canonical project JSON,
immutable revision history and publication pointers remain untouched. Public
rendering omits the missing dynamic portion or uses a safe escaped placeholder;
admin editing displays an explicit missing-provider warning and preserves its
original settings/schema/ID for a later compatible re-add. No arbitrary PHP/SQL
or editable privileges in project JSON. See `PLUGIN_SYSTEM.md`. This behavior
has not been implemented or verified.

Status: V1 component contracts, two synthetic project fixtures and offline
store/render/preview/guarded-media foundations exist. Decision 111 adds a
restricted real GrapesJS layout editor, actual browser round-trip tests and a
separate captured project. Full editor/publication/provider lifecycle and MSSQL
template cutover remain incomplete; template publication stays disabled.
See [live builder implementation](docs/LIVE_BUILDER.md).

SQL canonical storage/import is prepared with FK-enforced owned SQLite evidence;
see docs/TEMPLATE_STORAGE.md. Draft CAS and one-transaction full-collection imports
retain immutable revisions and all pointers. SQL publication refuses directly
until the full validation pipeline exists. Persistence checks are not a sanitizer.

GrapesJS is intended as a live in-browser visual template editor and website
builder, with the user's old Enjin.com Minecraft website-building experience as
the reference. Owners should compose pages/shared sections, edit blocks/styles
and see changes on the canvas, retaining editable projects across save/reload.
Live templates must work with drag-and-drop building, allowing administrators and
designers to drag visual blocks, widgets, and layout sections directly into any page.
Templates must be universally compatible with any feature, module, or page in the
system (including news, rankings, player profiles, account management, visual webshop,
P2P marketplace, guilds, castle siege, event timetables, drop guides, support, and custom pages).
See GRAPESJS_INTEGRATION.md; live editing preserves separate drafts/publication.

Distribution requirement (decision 085): the GrapesJS builder itself is free/core.
Free/default templates may be shipped, while premium predesigned templates are
paid. Distribute free and premium template bundles in physically separate
directories/packages, including starter project JSON, assets and bundle metadata.
The free builder and free templates must run without any premium template pack.
This separates the editor's free functionality from the price of a prepared design.

Physical bundle packaging is distinct from installed website state. Editable
projects, drafts, revisions and publication settings remain authoritative in
website-owned MSSQL; bundle files do not become a configuration fallback. All
template tiers retain the same validation, security, permission and publication
boundaries. Physical bundle separation/catalog/entitlements remain unimplemented;
this note does not move existing assets or enable the live editor/publication.

The application envelope contains schemaVersion, templateId, revision, status,
componentVersions, requiredCapabilities, asset references, theme/design tokens,
sharedSections and `projectData`. projectData is canonical editable GrapesJS JSON,
including pages/frames/components/styles. Generated HTML/CSS are derived caches
only. Keep server metadata outside GrapesJS project data and preserve unknown
editor-owned fields through compatible round trips.

The fixture subset covers two pages, nested static/dynamic components, a shared
header/footer, dark/light theme tokens and responsive styles. It is a proposed
schema fixture, not a captured editor project. Asset paths are symbolic, so these
checks do not prove asset existence or real GrapesJS round-trip compatibility.

Persistence design: append-only revision records with project JSON, schema and
component versions, asset references, author and time; separate current-draft and
current/previous-published pointers. Autosave uses optimistic revision checks.
Public rendering resolves only the published pointer; authorized previews resolve
an exact draft revision using synthetic/read-only projections. Saving never
publishes. Restore and duplicate create new revision identities rather than
editing historical records. Export/import preserve canonical data and metadata,
with explicit size/schema/content checks and revalidated asset references.

Before publishing, validate registered component/version/property contracts,
capabilities, route and render compatibility, theme/shared-section references,
asset existence and security restrictions. Permissions, data and commerce rules
are server-owned. Rendering uses an allowlisted DOM/CSS representation with proper
escaping; do not execute arbitrary PHP/SQL/scripts from a template. A real HTML/CSS
sanitizer and browser/CSP tests remain required; the fixture checker is not one.

The synthetic `displayCount` v0 to `limit` v1 adapter in Checks::migrateRankings
demonstrates copy-based validation and rejects ambiguous properties. It does not
migrate live data, publish content or claim compatibility with an existing legacy
builder. Production migrations must keep backups, create new revisions, render
and compare fixtures, support rollback and require explicit review.

The internal PublishedTemplateResolver and its file adapter keep public
published-only resolution separate from actor-authorized exact draft access.
A future cross-module resolver contract requires the normal version/review
process. TemplateStore.load must never become an anonymous draft-access shortcut.
