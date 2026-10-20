# MainAPI / PluginHost API: kernel contract

**Derivation:** MainAPI is the specialized PluginHost contract of Order API, not a fourth primary API. It retains independent versioning and canonical manifest keys. Executable registration remains pending; the private Order read gateway is not this public SDK. See [three-API architecture](API_ARCHITECTURE.md).

**Proposed SDK v1; not yet implemented.** This API is the stable host interface for every installed folder plugin. Think of this as the Paper/Spigot `PluginManager` plus ZendaMod website services. It is not a public Internet REST API. Separate REST endpoints can be registered through this interface and must enforce their own authentication.

## 1. Host ownership

Trusted kernel responsibilities: `PluginDiscovery`, `PluginTrust`, `PluginManager`, `PluginContext`, request-local `ContributionRegistry`, route and auth middleware, capability/service resolution, typed `EventBus`, `JobRegistry`, `AdminExtensionRegistry`, `WidgetRegistry`, secure `SettingsAPI`, versioned audit and installation/migration orchestration. Kernel/website-owned MSSQL is mandatory in production even if `/plugins` is empty. Admin credentials are a separate website authority from game player accounts.

`MainAPI` should be small and stable. It exposes *scoped registration handles*, **not** the entire dependency injection container, root PDO connection, filesystem write access or credentials.

## 2. Example plugin registration in PHP 7.4-compatible pseudocode

```php
interface PluginEntrypoint
{
    public function register(PluginContext $context): void;
}

final class RankingsPlugin implements PluginEntrypoint
{
    public function register(PluginContext $ctx): void
    {
        $ctx->routes()->get('zenda.rankings.list', '/rankings', RankingsController::class);
        $ctx->admin()->menu('zenda.rankings.settings', '/admin/plugins/zenda.rankings');
        $ctx->events()->listen('game.player.changed@1', RankingsInvalidator::class);
        $ctx->widgets()->register('zenda.rankings.table', '1.0', RankingsWidget::class);
        $ctx->requiresCapability('game.rankings.read@1', true);
    }
}
```

**Illustrative contract only**: exact namespaces/methods must be frozen additively when implemented. `register()` registers definitions, not DB reads or business actions. Never run install SQL, network calls, irreversible tasks or native game mutations during directory scanning or `register()`.

## 3. Manifest declarations

Decision 104 selects [canonical manifest v1](docs/PLUGIN_MANIFEST.md); its
data-only parser/discovery now exists, while MainAPI registration still does not.
Use explicit `{min,maxExclusive}` release-version objects for `contracts.mainApi`
and the independently versioned provider APIs. Required/optional plugin
dependencies are versioned objects; normalized read requirements also declare
`scope` and a boolean `optional`. Older string/caret-range examples are rejected,
not silently migrated. Requirements are requests, never trust or permissions.

## 4. Registry and lifecycle

`ABSENT` (folder not present) overrides stored approval; `DISCOVERED` -> `PENDING_TRUST` -> `APPROVED` -> `COMPATIBLE` -> `PENDING_MIGRATION` (only when required) -> `ACTIVE`, or `BLOCKED`/`DISABLED`. An unchanged previously approved plugin may resume automatically under policy after safe checks; unknown/modified executable files do not auto-execute. Activation does not install schema on GET requests. Optional dependency absence yields a typed unavailable result; required dependency absence blocks only dependents. Missing plugins leave saved MSSQL data untouched.

### Host API subservices

| Service | Expected behavior |
| --- | --- |
| `ctx->routes()` | Namespaced URL registration; no collisions, auth/CSRF checked before handlers |
| `ctx->admin()` | AdminCP pages/menus with separate staff authorization |
| `ctx->events()` | Typed event version, plugin ownership, safe dispatch and idempotency |
| `ctx->services()` | Resolve versioned required/optional ports for a selected profile, no cross-plugin internal imports |
| `ctx->jobs()` | Claimed only when provider is still active; retries/duplicate and cancellation rules |
| `ctx->widgets()` | Register schemas and read projections; no executable code in GrapesJS projects |
| `ctx->settings()` | Scoped website MSSQL config with revisions, no file fallback |
| `ctx->permissions()` | Check server-owned actor/action/resource grants at action boundary |
| `ctx->assets()` | Namespaced assets served through safe published mapping; absence invalidates URLs |
| `ctx->audit()` | Redacted immutable/append-only audit for sensitive actions |
| `ctx->capabilities()` | Declare/resolve typed API capabilities; profile-specific availability |
| `ctx->gameTypes()` | Register implementation of **GameTypeAPI** (if declared) |
| `ctx->databaseTypes()` | Register approved **external game DB transport** provider |
| `ctx->schemaAdapters()` | Register verified game schema/emulator adapter |

## 5. Failure and version rules

Every registration is immutable and scoped to the owning plugin for that request. No global mutable plugin variables or unconditional `require` of optional code. Reject duplicate route IDs, provider ID conflicts, unknown contract versions and dependency cycles. A plugin cannot override kernel auth/AdminCP/installer routes, even when activated. New registries must preserve older frozen contracts through adapters, not mutate them. Active requests are not retrospectively cancelled when files vanish; background workers revalidate before each claim.
