# Feature derivative API

Decision 115 implements `feature.resolve@1.0.0`, the fourth utility derivative
and the first block that exists purely so crafts can be *optional*. It is a
focused Singularity operation governed by Order and proposable through Chaos,
not a fourth primary API. See [building blocks](BUILDING_BLOCKS.md) for the law it
implements.

`Kernel/Features/Domain/FeatureCatalog` owns validation and resolution;
`Bootstrap/Apis/WebsiteDefinitions` declares the reviewed switches and
`FeatureResolve` plus `UtilityApis` bridge them into the sealed Order registry.

```php
$response = WebsiteApis::order()->invoke('feature.resolve', '1.0.0', [
    'name' => 'webshop.storefront', 'version' => '1.0.0',
    'scopes' => [['scope' => 'craft:webshop']],
], WebsiteApis::publicContext());
// success.result: {name:webshop.storefront, version:1.0.0, enabled:true, source:scope}
```

## Optional, never authoritative

A reviewed declaration is a boolean default plus up to 32 named boolean scopes.
Resolution combines the requested bounded scopes with OR and falls back to the
declared default when no scope matches, and reports which of the two produced the
answer.

**A feature switch never grants authority.** It cannot create a permission, widen
a resource, reach MSSQL, execute a migration, enable a payment path or bypass the
game-write gateway. A switch is only meaningful next to a check that already
exists; a disabled feature removes an existing capability, it never adds one. This
is why every craft keeps its own server-side authorization, and why the switch is
a pure Singularity operation with `effects: none` and no required permission.

Declared switches are reviewed composition, not editable settings: there is no
administrator switch editor, no per-tenant overrides and no file fallback.
Operator-configurable switches would require authoritative website MSSQL storage,
revisions, authorization and audit, exactly like the other website configuration.

## Profile 1.0

- Up to 128 reviewed features in 64 KiB, exact semantic version, strict name
  grammar, boolean default only, and unique scope names per feature.
- Up to 8 requested scopes per call, bounded scope names, no nested or computed
  scopes and no wildcard matching.
- Unknown feature or version refuses without falling back to a default.
- Replacing a reviewed declaration changes resolved values without editing any
  consumer; an already composed catalog keeps its own validated snapshot.

See [URL](URL.md), [Math](MATH.md), [Style](STYLE.md),
[integration profile](WEBSITE_INTEGRATION.md) and [STATE](../../STATE.md).