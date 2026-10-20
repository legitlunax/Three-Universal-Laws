# URL derivative API

Decision 112 implements private `url.resolve@1.0.0`: a focused Singularity
operation governed by Order. It is a derivative of the three primary APIs.
It generates named same-origin paths; it neither performs HTTP requests nor
authorizes access to the generated destination. There is no public RPC endpoint.

`Kernel/Urls/Domain/UrlCatalog` owns validation and resolution.
`Bootstrap/Apis/WebsiteDefinitions` declares the initial six named routes:
`templates.index`, `templates.edit`, `templates.save`, `builder.script`,
`builder.style`, and `builder.notices`. `UtilityApis` and `UrlResolve` bridge
that owner into the sealed Order registry; neither URLs nor Math are absorbed
into Order's implementation.

```php
$response = WebsiteApis::order()->invoke('url.resolve', '1.0.0', [
    'name' => 'templates.edit', 'version' => '1.0.0',
    'parameters' => [], 'query' => [['name'=>'id', 'value'=>'synthetic-home']]
], WebsiteApis::publicContext());
// success.result.url: /admin/templates/edit?id=synthetic-home
```

The Bootstrap convenience bridge is
`UtilityApis::url('templates.edit', ['id'=>'synthetic-home'])`.
Views receive generated URLs through their model. The editor consumes injected
links, stylesheet/script URLs and a save endpoint; corresponding Application
route checks use the same names. Unknown names/versions fail without a fallback.
Other existing website views/controllers still contain literal URLs and await
consumer-by-consumer migration. Web-server asset rules remain explicit deployment
allowlists. This slice does not establish whole-site subdirectory deployment.

## Private profile 1.0

- Up to 128 reviewed routes, each with an exact release version, local path and
  at most 16 allowlisted query names. One active version per name in a catalog.
- Optional `{parameter}` segments accept bounded ASCII slugs only; all expected
  parameters must be supplied, with no extras. Query strings use sorted RFC3986
  encoding, bounded UTF-8 scalar strings and no control characters.
- External origins, protocol-relative paths, traversal, percent-encoded path
  tricks, backslashes, query/fragment syntax in declarations, duplicate pair
  names, unknown parameters and outputs over 2,048 bytes refuse.
- A directly composed catalog supports a validated trusted mount prefix. It
  never trusts Host, forwarded headers, request input or a caller-supplied origin.
- Order enforces closed bounded inputs, exact capability version, deadline and
  public execution context before handler construction. Resolving an admin URL
  grants no administrator permission; existing destination guards remain required.

Route declarations are reviewed application composition, not editable website
configuration. No file-backed redirect/settings system was introduced. Future
operator-configured origins or aliases require authoritative website MSSQL
storage and fresh validation; they must not silently fall back to these defaults.
Future drop-in plugin registrations require the approved MainAPI lifecycle,
which remains unimplemented. These classes are not a frozen public plugin SDK.

See [Math](MATH.md), [implemented profile](WEBSITE_INTEGRATION.md),
[builder](../LIVE_BUILDER.md) and [STATE](../../STATE.md).
