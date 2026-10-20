# Math derivative API

Decision 112 implements private `math.evaluate@1.0.0`: a focused Singularity
operation governed by Order. Chaos can propose it as an independent pure step.
It evaluates a named, versioned, reviewed formula from declarative data.
It accepts no equation string, PHP, JavaScript, SQL or executable callback.

`Kernel/Math/Domain/FormulaCatalog` owns the arithmetic interpreter and validation.
`Bootstrap/Apis/WebsiteDefinitions` supplies initial formula data; `MathEvaluate`
and `UtilityApis` bridge it into the sealed Order registry. Order itself contains
no mathematical business rules. Frozen V1-V5 and public plugin SDKs are unchanged.

```php
$response = WebsiteApis::order()->invoke('math.evaluate', '1.0.0', [
    'name'=>'layout.columnWidth', 'version'=>'1.0.0',
    'inputs'=>[
        ['name'=>'available','value'=>1000],
        ['name'=>'columns','value'=>3],
        ['name'=>'gap','value'=>20]
    ]
], WebsiteApis::publicContext());
// success.result.value: 320
```

## First reusable formulas

| Name / version | Inputs | Behavior |
| --- | --- | --- |
| `layout.snapWidth / 1.0.0` | width, available | Snap to 8 pixels, prefer minimum 80, clamp to actual nonnegative integer available width |
| `layout.innerWidth / 1.0.0` | width, paddingStart, paddingEnd | Subtract both padding edges; clamp at zero |
| `layout.columnWidth / 1.0.0` | available, columns, gap | Equal column width after inter-column gaps; clamp at zero |

The real builder receives validated formula definitions from trusted server
composition and uses `frontend/builder/formulas.js` for immediate display/drag
feedback. Both mouse and keyboard sizing consume the named formulas and the
same measured padding. CSS Grid/Flexbox remain responsible for actual flow.
Changing approved formula data changes the calculation without editing its
consumer. Formula revisions must preserve declared behavior or use a new version
with explicit consumer migration; catalogs currently select one active version
per name. No administrator formula editor or persistence is implemented.

## Declarative expression profile 1.0

A node is a finite numeric literal, `{'var':'inputName'}`, or
`{'op':'operation','args':[nodes...]}`. Supported operations are binary add,
subtract, multiply, divide, min/max and unary floor, ceil, round, abs. Unknown
properties, operators and variables refuse. Round uses half ties toward positive
infinity in both runtimes; zero division refuses. There are no loops, references
to other formulas, recursion, random/time/network access, strings or functions.

Catalogs contain at most 32 formulas / 64 KiB, 16 input ranges per formula,
128 expression nodes and depth 16. Definitions require exact versions and closed
input names with numeric min/max. Numbers and intermediate results must be finite
and within +/-10^12; no implicit string/boolean coercion. Layout dimensions are
bounded to 10^6 and column count to 1-100. Invalid shape, duplicate pair names,
missing/extra inputs, stale versions, domain violations and overflow refuse.
Order applies its additional closed input/output, byte, context and call limits.

These initial formulas are reviewed source declarations, not authoritative
operator settings held in a file. Future editable formula catalogs must use
website-owned MSSQL, revisions, explicit authorization and audit with no disk
fallback. They also need domain-specific validation and versioned consumer tests.
No game, currency, payment or shop formula has been migrated or enabled here.
Floating-point layout arithmetic is not a monetary calculation contract.
Client results never authorize server changes; future business owners must
recompute and enforce their own protected application boundaries.

## Evidence

PHP tests cover named resolution, formula substitution, malformed/executable
definitions, range/depth bounds, nonfinite/coerced input, division/overflow and
Order/Chaos composition. JavaScript tests compare 74 actual PHP results and
exercise malformed inputs, rounding and independent immutable catalog snapshots.
Real Chromium exercises resizing and saving; an additional response-data fixture
changes named links/save query and the sizing expression while requests still
reach the real guarded PHP endpoint. It verifies consumer injection, not a
production formula-editing or route-registration UI.

See [URL](URL.md), [implemented profile](WEBSITE_INTEGRATION.md),
[builder](../LIVE_BUILDER.md) and [STATE](../../STATE.md).
