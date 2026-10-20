# Live template layout editor

Decision 111 installs a free, self-hosted editor at
`/admin/templates/edit?id=synthetic-home`. Open **AdminCP → Visual templates →
Visual editor** using a separate website administrator account. Serve `public/`
as the document root; see [local setup](SANDBOX.md) and
[XAMPP preparation](installation/XAMPP_8_2.md). There are no default credentials.

## Libraries and distribution

| Library | Pinned version | Purpose | License / source |
| --- | --- | --- | --- |
| GrapesJS core | 0.23.8 | Canvas, models, pages, drag/drop and undo | [BSD-3-Clause](https://github.com/GrapesJS/grapesjs) |
| Material Color Utilities | 0.4.0 | Brand-color palettes and light/dark semantic colors | [Apache-2.0](https://github.com/material-foundation/material-color-utilities) |
| Moveable | 0.53.0 | Right-edge resize handle and snapping | [MIT](https://github.com/daybrush/moveable) |
| Floating UI DOM | 1.8.0 | Layout-help popover and viewport collision handling | [MIT](https://github.com/floating-ui/floating-ui) |

Grid, Flexbox, gap, padding, ResizeObserver and getBoundingClientRect are native
browser features. No paid Studio SDK or extra layout engine is required. Native
flow positions components; resizing changes width in 8-pixel steps bounded by
the parent. Arbitrary content can still overflow; the inspector reports it.

Decision 112 routes editor links, assets and saves through the
[URL derivative](api/URL.md). Sizing uses centrally declared named formulas from
the [Math derivative](api/MATH.md), including both parent padding edges for mouse
and keyboard controls. Other website URLs/calculations await migration.

`package-lock.json` pins versions and integrity. The build retains licenses and
copyright notices for production dependencies, including README/distribution
banners when npm omits a standalone license file. The artifact manifest records
direct versions and compiled hashes. These are code-distribution metadata, not
website configuration or trust approvals. GrapesJS includes deprecated upstream
`backbone-undo`; upgrades need the same undo/browser checks. npm audit is advisory
evidence at a point in time, not a security guarantee.

## Editing

- Drag Container, Columns, Stack, Card, Button Row or Text into the canvas.
  Add buttons offer a keyboard alternative, inserting into a selected container
  or at the root for a leaf selection.
- Select a component to measure dimensions, parent width, padding and gap.
  Adjust spacing, alignment, flow or width in the inspector. Columns adapt to
  available space; button rows wrap with explicit gaps.
- Edit plain text in the inspector. There is no rich HTML/source editor here.
- Choose a brand color and light/dark mode. Material semantic colors and theme
  identity live in canonical project models and participate in undo/reload.
  Existing legacy theme metadata remains unchanged until choosing Material colors.
  Frame creation and page switching paint that page's document so themes keep
  their background/text contrast across the existing pages.
- Switch existing pages and Desktop/Tablet/Mobile canvas widths. There is no
  page-creation UI yet. Use undo/redo for layout and theme changes.
- Save draft explicitly. Only a successful acknowledgement advances the expected
  revision. Edits during a pending save remain unsaved. Conflicts preserve the
  canvas for comparison. Saving never publishes.

Existing `zenda.rankings` and `zenda.account` types/properties remain intact with
synthetic canvas placeholders. No native account information is requested.
Pages, shared-section metadata, envelope fields and component IDs are retained;
transient GrapesJS node IDs are materialized before saving. Shared-section
editing, uploads in the editor and optional-provider registrations remain pending.
The existing asset manager stays separate.

## Server and security boundary

Bootstrap composes Templates-owned `BuilderProject` and `BuilderView` behind
existing administrator/session controls. GET validates stored data before
emitting escaped configuration. POST `/admin/templates/api/builder-save` requires
same-origin requests, CSRF, administrator `templates.edit`, matching template
identity and exact expected revision. Existing TemplateStore performs immutable
draft/CAS storage. Frozen V1-V5 signatures, component catalogs and both older
saved template fixtures remain unchanged.

The configured store remains authoritative: MSSQL in database mode; the existing
explicit offline file prototype only in offline mode. No new localStorage,
IndexedDB or JSON settings fallback was added. Actual MSSQL template cutover
remains unverified; browser tests use fresh private synthetic offline state.
This work changes no SQL schema, grants, deployment credentials or installation
record. Other static actions retain their current application boundaries;
Order's private API profile still does not execute writes.

The restricted profile bounds pages, depth, nodes and bytes. It permits known
static tags/types, local image/link attributes, checked CSS declarations/selectors
and the two existing dynamic placeholders. Scripts, event handlers, executable
components, raw HTML, external sources, arbitrary CSS and data sources refuse.
GrapesJS text nodes use `textContent` and escaped HTML export; literal `<` stays
text there. Other component content cannot contain HTML. Inline style attributes
are parsed through the same declaration checks as style objects. This is a
restricted format, not a general HTML/CSS sanitizer or publication validator.

Only the editor permits self-hosted JS, library inline styles, same-origin saves
and its iframe in CSP. No unsafe-eval or external CDN is enabled. The canvas is
sandboxed without script permission; template links/buttons do not navigate and
paste is disabled. The router and Apache rewrites expose three exact compiled
asset/notice paths. Production TLS/upload isolation, complete visual regression
and publication remain pending. Template publication stays disabled.

## Build and verify

Customers using compiled assets need no Node service. Developers need Node
22.12+ (tested with 24.21.0) and npm:

```powershell
npm.cmd ci --ignore-scripts
npm.cmd run build
npm.cmd run test:builder
npx.cmd playwright install chromium
npm.cmd run test:browser
& ./scripts/verify.ps1 -Level complete
```

Sources live in `frontend/builder/` and `src/Modules/Templates/`. Vite writes only
`public/assets/template-editor/`, with `publicDir: false` and automatic directory
clearing disabled. Never recursively copy public/ into its own descendant.

PHP profile/artifact checks run through `tests/templates.php`. JavaScript units
use browser-bundler resolution because Material 0.4.0 contains extensionless
imports that native Node ESM cannot resolve directly. Chromium tests create a
fresh marked private fixture, synthetic website admin and ephemeral loopback
server; they remove only their owned server/state. They exercise genuine canvas
actions, saved JSON, geometry and security checks. A captured real project lives
in `tests/fixtures/builder/`. Read [STATE](../STATE.md) for actual final evidence.
