# Language derivative API

Decision 118 implements `locale.resolve@1.0.0`, the fifth utility derivative and
the building block that makes the website multi-language. It is a focused
Singularity operation governed by Order and proposable through Chaos, not a
fourth primary API. See [building blocks](BUILDING_BLOCKS.md) for the law.

`Kernel/Locales/Domain/LocaleCatalog` owns validation and resolution;
`Bootstrap/Apis/WebsiteDefinitions` declares the reviewed locales and messages and
`LocaleResolve` plus `UtilityApis` bridge them into the sealed Order registry.

~~~php
$response = WebsiteApis::order()->invoke('locale.resolve', '1.0.0', [
    'locale' => 'es-AR', 'version' => '1.0.0', 'key' => 'nav.shop',
], WebsiteApis::publicContext());
// success.result.value: Tienda
~~~

## One key, one reviewed fallback

Each declared locale carries a text direction and exactly one fallback locale.
Resolution returns the stored value plus the locale it actually came from and
whether it was borrowed, so a craft can show a missing-translation notice,
select a font stack or set `dir` from the same response. An unknown locale, key
or version refuses; the block never returns raw source text as a fallback.

Text direction always follows the locale the text came from: a right-to-left
locale that borrows English copy reports `ltr`, because the English text really is
left-to-right. That is deliberate.

## Deliberately not included

The block returns **stored text only**. There is no interpolation, formatting,
pluralisation, markup rendering, HTML, URL, date/number/currency locale or
executable payload in this profile. Placeholder substitution and plural rules are
deliberately left out until two crafts need them, per the Rule of Two; they are
not smuggled in as craft-private logic either. Escaping on render stays the
consumer's responsibility, exactly as for every other reviewed string in the
website.

Reviewed locales today: `es-AR` (self-fallback), `es-MX` and `pt-BR` (fall back to
`es-AR`), `en-US` (self-fallback) and `ar-EG` (right-to-left, falls back to
`en-US`).

Declarations are reviewed composition, not editable settings: there is no
administrator translation editor, no per-tenant override and no disk fallback.
Operator-editable catalogues require authoritative website MSSQL storage with
revisions, authorization and audit. Replacing a reviewed message changes the
resolved copy without editing any consumer; a key version change requires an
explicit consumer migration.

See [URL](URL.md), [Math](MATH.md), [Style](STYLE.md), [Feature](FEATURE.md),
[integration profile](WEBSITE_INTEGRATION.md) and [STATE](../../STATE.md).