# Dynamic component development

## New source of dynamic components: removable plugin packages

Ultimately a dynamic component belongs to an approved `plugins/<id>` package
and registers through the versioned `PluginAPI`/`WidgetRegistry`, not a hardcoded
core component switch. Missing plugin removes palette block and provider
handlers, but saved project JSON and component ID/version/properties survive
with a safe placeholder. No cross-package private class imports. Refer to
`PLUGIN_SYSTEM.md` and `PLUGIN_DEVELOPMENT.md`; this is planned behavior and
does not imply that the current renderer/editor is complete.

Before adding a visual component, read STATE, CONTRACTS and TEMPLATE_SYSTEM.
Current registry entries are planned; there is no executable renderer/editor yet.

1. Implement and verify the owning application's read capability first.
2. Allocate a stable `zenda.<name>` ID/version and declare provider, property
   schema, capabilities, data requirements, permissions, rendering, preview and
   compatibility in the registry. Do not rename a published component casually.
3. Keep properties presentation-only. Create actor/permissions/projections on the
   server; an editable property never grants authority or chooses arbitrary SQL.
4. Register the GrapesJS model/view and traits using native component APIs. Keep
   preview synthetic/read-only and render escaped markup through the server port.
5. Add representative saved JSON plus rendering/security/browser regression
   tests. Validate old projects. Use a new version and copy-based migration when
   properties change; retain old revisions and reject ambiguous input.
6. Run impact analysis, complete/migration checks and contract review. Document
   the exact old/new behavior and rollout/recovery path before activation.

The phase-3 fixture validator supports an intentionally limited static DOM/CSS
subset and two proposed dynamic schemas. Extending that subset requires tests;
passing its checks is not proof of runtime sanitization or visual equivalence.
