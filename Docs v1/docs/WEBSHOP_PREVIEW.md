# Extracted Webshop preview boundary

Decision 096 moves the synthetic catalog, pricing, display projection, legacy
prototype order value object, controller and view into `packages/premium/webshop`.
The package uses its own layers and public contracts only. Core supplies the free
V2.PageLayout shell. V3.WebshopPreview has an independent initial catalog/signature
freeze: V1 and V2 files, catalogs, components and baselines remain unchanged.

## Contract and compatibility

`render(model)` receives trusted server composition: mount path, staff display flag,
CSRF, notices, optional synthetic currency display, category, itemId and options.
The controller supplies its own catalog, selected item and configuration; caller
`items`, `selectedItem` and `config` cannot replace them. The layout staff flag
controls display and grants no authority. All text/form/path values remain escaped.

`preview(itemId, options, currency, balances)` takes no account, writer, transport,
clock, warehouse or audit dependency. Balances must have exactly `credits`, `wcoinC`
and `zen`, each a nonnegative PHP integer. Currency aliases credits/wcoinc/wcoin/zen
retain the old numerical pricing behavior; this is synthetic arithmetic, not a
currency conversion or payment quote. Unknown item/currency or insufficient balance
returns `{success:false,error:string}`. Malformed balances/options throw
InvalidArgumentException. Options may contain only these optional fields:

| Fields | Type and normalization |
| --- | --- |
| level, option, sockets | PHP integers clamped to the selected item's bounds, as before |
| skill, luck, ancient, harmony | booleans; unsupported item flags become false |
| excellent | sequential list of at most six strings of at most 96 bytes; unknown/duplicate selections normalize to allowed catalog order |

Success is `{success:true,sandboxPreview:true,nativeFulfilled:false,configuration,
balancesAfter}`. Configuration retains catalog/options/totalPrice/currency and a
display-only preview: group/index/name, normalized options, formattedName and
rarityClass. It contains no hex, byte length, serial or warehouse slot. No order,
reservation, custody, persistence, debit, audit or delivery occurs. Repeated calls
with identical input return identical proposals without modifying caller balances.
Synthetic catalogs allow at most 500 unique items; base prices are 0..1,000,000,000
to keep pricing arithmetic bounded. Excellent catalog labels are unique bounded
strings (at most six). These are offline policy limits, not native MU schema rules.

The former GameData classes and Content renderWebshop move without aliases.
The old unfrozen purchaseItem(writer) and buildMuItem APIs are removed explicitly.
Moved WebshopOrder remains an unused prototype value object, not an implemented
order subsystem. Tests of simulated debits, generated orders and fake binary/slot
output migrate to pure-proposal/no-effect and display coverage. Catalog/defaults,
option prices, categories, HTTP URLs, forms, redirects, escaping and shared theme
remain compatible. Direct options now require typed fields rather than coercion.
The HTTP boundary parses scalar controls and strictly rejects malformed excellent
lists before invoking the public port.

## Composition and limits

OptionalWebshop loads the fixed package-local Bootstrap closure only in explicitly
selected local file-sandbox mode, validates its factory/result and redacts failures.
Missing/broken packages return private 503; free content and separate staff login
remain available. POST retains authentication, Origin/CSRF and the existing shared
20-attempt/900-second commerce budget, including missing-package refusals. Database
mode refuses before loading either preview package, retaining the same protections.

Owned tests cover pricing/normalization, hostile rendering, repeat/no-effect
calculations, writer rejection, strict V3 identities/static bindings and actual
HTTP absence, throttling, malformed fields and independent package availability.
Saved template fixtures and V1/V2 consumers remain unchanged. Other paid
GameData responsibilities, complete distribution/test manifests, production
licensing/encryption, public premium SQL catalog bindings, native commerce and automated visual regression
remain pending. No SQL Server or game connection was used.

Decision 098 adds separate private V5 catalog inspection, not a change to this
V3 synthetic port. Each premium owner reads its stored draft only for authorized
website administrators; no stored catalog activates public database shops,
purchases or native authority. See docs/PREMIUM_CATALOGS.md.
