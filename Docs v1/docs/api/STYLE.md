# Style derivative API

Decision 114 adds a third utility derivative next to URL and Math:
`style.resolve@1.0.0`, a focused Singularity operation governed by Order and
proposable through Chaos. It resolves one reviewed, versioned design value for
a viewport width. It is a derivative of the three primary APIs, not a fourth
primary API, and it changes nothing about Order itself.

`Kernel/Styles/Domain/StyleCatalog` owns validation and resolution.
`Bootstrap/Apis/WebsiteDefinitions` declares the reviewed breakpoints and tokens;
`StyleResolve` and `UtilityApis` bridge that owner into the sealed Order
registry. Frozen V1-V5, static modules and the executable plugin lifecycle are
unchanged.

```php
$response = WebsiteApis::order()->invoke('style.resolve', '1.0.0', [
    'name' => 'space.gutter', 'version' => '1.0.0', 'viewport' => 375,
], WebsiteApis::publicContext());
// success.result: {name:space.gutter, version:1.0.0, breakpoint:null, minWidth:0, value:16, unit:px}
```

## Mobile-first resolution

Resolution is deliberately mobile-first, matching how responsive CSS is actually
authored: the token base value applies at every width, and each declared
overrides applies from its breakpoint minimum width upward until the next
overrides. The response always reports which breakpoint produced the value and its
`minWidth`, so a consumer can emit one base declaration plus deterministic
`min-width` media queries instead of maintaining its own breakpoint table.

Reviewed breakpoints (Bulma's published mobile-first convention): `mobile` 0,
`tablet` 769, `desktop` 1024, `widescreen` 1216, `fullhd` 1408. Reviewed tokens
cover block/inline/gutter spacing, column gap and full-width stacking, seven type
sizes, four corner radii, five font weights and a minimum touch-target size.

Only numeric values and the units `px`, `rem` and `%` are returned. There is no
CSS text, selector, property name, declaration string, `url()`, `calc()`,
expression, comment, animation or font payload in the profile, so a reviewed
token cannot smuggle authored CSS into a derived stylesheet.

## Provenance and boundaries

Bulma (<https://github.com/jgthms/bulma>, MIT) is the reference for the numeric
breakpoint and scale conventions this derivative adopts. **No Bulma stylesheet,
Sass, JavaScript, icon font or other third-party asset is vendored, imported,
copied or served by this project**, and no Bulma code appears in this repository.
What is new here is the bounded, mobile-first token resolution profile above.

Declarations are reviewed application composition, not editable website
configuration, and they are not a disk-backed settings source. Future
operator-configurable design tokens require authoritative website MSSQL storage
with revisions, explicit authorization and audit, and must not fall back to
these defaults. Token or breakpoint revisions must preserve declared behavior
or use a new version with explicit consumer migration; a catalog currently
selects one active version per token. There is no administrator token editor,
theme switcher or per-tenant customization UI.

Derived CSS remains the consumer's responsibility: this operation returns
numbers and units, and existing template/builder safety rules still forbid PHP,
SQL, arbitrary scripts and executable uploads in editable templates. Plugin
overrides of core tokens would need the approved MainAPI lifecycle, which remains
unimplemented.

See [URL](URL.md), [Math](MATH.md), [implemented profile](WEBSITE_INTEGRATION.md),
[builder](../LIVE_BUILDER.md) and [STATE](../../STATE.md).