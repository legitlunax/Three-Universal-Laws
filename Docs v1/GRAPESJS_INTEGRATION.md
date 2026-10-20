# GrapesJS integration plan

## Plugin-provided blocks versus installation by folder

The primary website extensibility goal is `plugins/<id>/`: add a **trusted
compatible plugin folder** to expose functionality via `PluginAPI`, remove that
folder and its contribution disappears without affecting the free core. This is
NOT GrapesJS page canvas drag-and-drop. GrapesJS will additionally discover
versioned, approved component registrations from those active plugins. Missing
plugin -> no palette block/new render handler; saved JSON/revisions remain with
an escaped missing-provider placeholder, retaining restoration compatibility.
See `PLUGIN_SYSTEM.md`. Decision 111 supplies the restricted browser editor;
the plugin-to-editor bridge and executable folder host remain unimplemented.

Status: decision 111 installs self-hosted GrapesJS 0.23.8, Material colors,
native layout blocks, Moveable sizing, Floating UI help and validated draft saves.
Actual Chromium editing/save/reload/undo and responsive tests exist. Full editor,
publication, MSSQL template cutover and provider lifecycle remain pending.
Template publication stays disabled. See [implementation and limits](docs/LIVE_BUILDER.md).

Product intent: GrapesJS is the live visual template editor and website builder.
The user's reference experience is old Enjin.com when it supported Minecraft
website building: owners build and customize their site in the browser through
drag-and-drop blocks, page layouts, shared headers/footers, styling and responsive
previews, then save and reload editable projects. The canvas updates as they edit;
draft saving and deliberate publication remain separate. Live templates must work
seamlessly with drag and drop, allowing administrators to position components and
design layouts visually. Furthermore, templates must be compatible with any feature,
module, or page of the website (e.g. news archives, competitive rankings, user
accounts, visual webshop, P2P marketplace, character armory, guilds, castle siege,
player & guild profiles, support system, event timetables, loot guides, and custom content).
This is the full intended experience; decision 111 implements the bounded layout editor described above.

The builder is free/core, including the intended Enjin-style visual editing
experience. Free/default templates may be offered; premium predesigned template
packs are paid. Keep free and premium template bundles/assets in physically
separate directories/packages. The editor and free templates must function without
premium template packages. Packaging/catalog/entitlement implementation remains
pending; see TEMPLATE_SYSTEM.md and decision 085.

Use open-source GrapesJS after review, self-host its compiled JS/CSS, and use
Vanilla JS plus Vite as build-time tooling. Pin and lock compatible package
versions after checking Node and browser support. Production PHP hosting should
not require a persistent Node server. Avoid loading untrusted remote editor plugins.

Use GrapesJS Storage Manager project JSON as the editable source, following the
[official storage documentation](https://grapesjs.com/docs/modules/Storage.html).
Register stable custom component types via the
[Component Manager](https://grapesjs.com/docs/modules/Components.html); use the
[Asset Manager](https://grapesjs.com/docs/modules/Assets.html) with a server-owned,
authorized and sanitized upload endpoint. These references were checked during
the 2026-10-17 design session; decision 111 pins the initial editor versions.

Expose native blocks, layers, traits, styles, text/image/background/typography
editing, device/responsive controls and undo/redo. Compose pages, themes and
shared sections through the template service. Add authenticated CSRF-protected
load/autosave/publish/revision/export/import endpoints with size limits and
optimistic concurrency. Draft edits and editor preview must not affect publication.

Dynamic blocks will invoke registered server read projections. The initial
rankings/account types in `contracts/v1/components.json` have offline renderer
foundations; the browser preserves their metadata with synthetic placeholders.
Optional provider registrations remain pending. Do not expose WebShop, Market, Inventory
or Workshop editing blocks until their backend capability is implemented and
verified. Presentation properties never carry SQL, executable PHP, authoritative
prices, actor identity or inventory decoding logic.

Acceptance after integration requires authentic editor save/reload/undo fixtures,
responsive screenshots, security/adversarial imports, authorization/CSRF tests,
private previews, asset availability, revision restoration and compatibility with
earlier saved projects. The two frozen structural fixtures still cover schema
design; a separate captured real-editor fixture and Chromium tests now cover
decision 111. Full publication/editor coverage remains incomplete.
