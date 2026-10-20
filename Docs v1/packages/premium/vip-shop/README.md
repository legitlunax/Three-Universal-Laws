# VIP preview package

This separate development package owns the synthetic VIP catalog, pure calculation,
controller and view. It uses only its own module and public V2 contracts. The free
core supplies PageLayout and shared theme CSS. Bootstrap's fixed Composition.php
returns a factory accepting PageLayout and producing VipShopPreview.

Only the explicit local file sandbox selects this preview. Missing package returns
503 at VIP routes; the free site does not require its source. Database mode never
loads these hardcoded examples. Calculations take synthetic balances by value and
propose a tier/duration; they never read/write accounts or activate subscriptions.
There is no legacy class alias or currency-writer adapter.

Commercial licensing/encryption, production activation, runtime SQL catalog binding,
distribution/test manifests and native protected fulfillment are still pending.
The planned module declaration and source presence do not establish a paid license
or game capability. See root AGENTS.md, STATE.md, CONTRACTS.md and decision 095.

Decision 097 prepares this package's strict immutable draft catalog, internal read
port, SQL store, package-owned database/mssql/001-catalog.sql and guarded V4 CLI
provisioning. See docs/PREMIUM_CATALOGS.md. No database storefront or native/licensing
activation is provided; actual SQL Server remains unverified.
