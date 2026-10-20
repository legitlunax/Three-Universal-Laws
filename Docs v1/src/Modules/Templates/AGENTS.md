# Templates boundary

Read root STATE.md and AGENTS.md first. Templates provides canonical GrapesJS
JSON envelope persistence, revision management, optimistic concurrency, and safe
server-side component rendering.

The intended GrapesJS/Enjin-style builder is free/core. Free/default templates may
ship alongside paid premium predesigned template packs, with physically separate
free/premium bundle directories/packages (starter JSON, assets and metadata).
Free editing/templates must not depend on premium packs. Installed projects and
revisions still belong in website-owned MSSQL; distributed bundles are not a file
configuration fallback. Physical packaging/catalog/entitlements remain pending.
See TEMPLATE_SYSTEM.md, GRAPESJS_INTEGRATION.md and decision 085.

Domain and infrastructure rules:
- Canonical project envelope contains `schemaVersion` (1), `templateId`, `revision`,
  `status`, `componentVersions`, `requiredCapabilities`, and `projectData`.
- GrapesJS JSON in `projectData` is the single source of truth; HTML and CSS are
  derived views. Never permit arbitrary PHP or SQL in templates.
- `JsonTemplateStore` enforces optimistic concurrency (`expectedRevision` for drafts,
  `expectedPublished` for publishing), immutable revision files under `revisions/`,
  and retains previous published pointers for rollback safety.
- `DefaultComponentRenderer` implements pure read-only component rendering with
  strict escaping and safe tag/attribute allowlists. It supports dynamic `zenda.rankings`
  and `zenda.account` blocks, strictly preventing exposure of credentials or session secrets.
- `AssetUploadPolicy` enforces Tier 0 upload security: strict MIME allowlist (PNG, JPEG, WebP only;
  no SVG/HTML), extension allowlists, size caps (<= 2MB), magic byte validation, GD image decoding
  verification, and random 32-hex storage filenames.
- `SecureAssetUploader` implements `AssetUploader` infrastructure with server-side authorization checks,
  safe atomic writes, non-executable destination permissions (0644), script execution prevention via
  `.htaccess`, and path traversal rejection.
- `FilePublishedTemplateResolver` safely resolves published templates for public consumption without
  bypassing `TemplateStore.load`'s actor authorization boundary.
- Template publishing remains disabled in offline configuration (`templatePublishing: false`).

Database-mode Bootstrap supplies the authoritative bounded image byte limit from
the separate operational configuration record and rejects both HTTP upload entry
points when uploads are disabled. Existing assets remain readable. Keep template
publication and immutable MIME/decoding/storage guards independent of this switch;
see docs/OPERATIONAL_CONFIGURATION.md. Template JSON persistence still awaits its
own actual MSSQL verification/cutover; PdoTemplateStore and full-collection import
are prepared with owned SQLite evidence only. SQL null expectedRevision now means
create-if-absent; an existing draft requires its exact expected revision. Do not
restore null-as-wildcard behavior. Whole-collection imports validate all revisions/
current/previous pointers before writing and commit in one owning transaction,
with heads before revisions to satisfy DDL 005's foreign key. No partial seed,
fallback draft, pointer repair or silent source-directory omission is allowed.
Exact history is immutable; published projections overlay imported metadata.
Direct SQL publish refuses until a validated publication pipeline exists, even
for an authorized caller. TemplateStorageDocument is persistence validation, not
an HTML/CSS sanitizer or proof of authentic GrapesJS round-trip compatibility.


