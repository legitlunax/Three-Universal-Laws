# Developing drop-in ZendaMod plugins

Primary APIs are Chaos (planning), Order (governance) and Singularity (focused handlers). The plugin contracts below derive from them; future plugin operations register through Order's MainAPI specialization. Read [the implemented profile](docs/api/WEBSITE_INTEGRATION.md) before treating the private bridge as a public SDK.

## Which API does your plugin implement?

All plugin folders register through **MainAPI**. Depending on their declared provider type, they can additionally implement `GameTypeAPI` (`zenda.game.mu`), `DatabaseTypeAPI` (`zenda.db.mssql`, for external game transport only), `SchemaAdapterAPI` (`zenda.schema.muemu`), or simply *consume* typed normalized capabilities (`zenda.rankings`, `zenda.webshop`). See `API_ARCHITECTURE.md` and the four per-API manuals. Game type, database engine and emulator schema are independent choices and must never be inferred from one another as automatically compatible. Existing examples here remain conceptual until concrete SDK contracts and manifests are frozen.


**Proposed developer contract, not an implemented SDK.** Read `PLUGIN_SYSTEM.md`, `AGENTS.md`, `CONTRACTS.md`, `INVARIANTS.md` and `STATE.md` first. Plugin root: `plugins/<publisher>.<slug>/`. Folder absence must not be an error for the rest of the website.

## Minimal package

```text
plugins/zenda.hello/
  plugin.json
  src/Plugin.php
  src/Handlers/HelloPage.php
  assets/hello.css
  views/hello.php
  migrations/001_init.sql
  tests/HelloPluginTest.php
```

Use [the canonical manifest v1 example and rules](docs/PLUGIN_MANIFEST.md).
Decision 104 implements data-only parsing/discovery, including exact inventory
hashes and independent API/version ranges. Generate inventory digests when
packaging; no file may be implicitly trusted because it is inside `vendor/`.
Decision 105 adds the internal kernel approval ledger/service; the SDK/loader,
operator endpoints and executable lifecycle remain unimplemented.
The following registration pseudocode is still illustrative.

### Proposed API pseudocode

The following is **not runnable PHP against the current codebase** until the loader and public API are implemented:

```php
final class Plugin implements PluginEntrypoint
{
    public function register(PluginContext $ctx): void
    {
        $ctx->routes()->get(
            'zenda.hello.page', '/hello', HelloPage::class, ['public' => true]
        );
        $ctx->admin()->menu('zenda.hello.settings', '/admin/plugins/zenda.hello');
        $ctx->widgets()->register('zenda.hello.card', '1.0', HelloCard::class);
    }
}
```

The eventual interfaces must be versioned and testable, and routes must be bound through stable service factories rather than runtime closures loaded from untrusted manifests. A plugin never loads another plugin's classes directly. It consumes a stable capability port and tolerates the port being unavailable (if optional). Services/handlers must validate the actor/permission server-side, not based on an editable widget property.

## Developer checklist

- Keep code, classes, assets, migrations, translations, views, tests and namespaces within your own plugin directory. Do not edit core or other plugin files.
- Declare required and optional dependencies explicitly, with version intervals; use a scoped `PluginContext` and versioned service ports.
- Provide no side effects from PHP file load or `register`. Use authorized explicit install/migration steps; never manipulate game tables merely because plugin is present.
- Include a contract test: add -> activate -> GET works -> physically remove folder -> GET unavailable, menus/blocks/jobs absent -> core/other plugins still work -> re-add -> same persistent settings work.
- Add missing-dependency, stale-cache, route collision, bad-manifest, permission, restart and rollback tests. Test with PHP 7.4 until the hosting baseline is intentionally migrated.
- Respect core game write/payment/currency/identity boundaries. Real MU writes remain disabled unless the verified protected gateway specifically supports them.
- For GrapesJS components, persist stable component IDs/versions; saved documents with a missing provider must show a safe placeholder and be restorable without content loss.

**Distribution:** Each free or premium package is a **different physical folder** and independently removable. Commercial encryption can protect that folder but does not change the `PluginAPI`/version contract; optional PHP loaders/extensions must be disclosed as compatibility requirements and cannot silently become core requirements. A ZIP can be uploaded via an AdminCP installer only if it is validated and unpacked safely to staging outside `/plugins`, then atomically promoted after approval.
