# Webshop preview package

This separate development package owns synthetic item catalogs/pricing, display
projections, the legacy prototype order value object and catalog/configurator
presentation. It consumes free V2.PageLayout and provides V3.WebshopPreview.
Only the explicit local file sandbox loads the fixed Bootstrap composition.

Calculations take synthetic balances by value and never accept account identities,
readers, writers or warehouses. They generate no native bytes, serials, slots,
orders, debits or delivery. Missing/broken packages refuse privately; core routes
and the separate VIP package remain independent. Database mode does not load it.

Production activation, licensing/encryption, runtime SQL catalog binding, protected
fulfillment and full distribution/test manifests remain pending. See root AGENTS.md,
STATE.md, CONTRACTS.md and docs/WEBSHOP_PREVIEW.md (decision 096).

Decision 097 prepares this package's strict immutable draft catalog, internal read
port, SQL store, package-owned database/mssql/001-catalog.sql and guarded V4 CLI
provisioning. See docs/PREMIUM_CATALOGS.md. No database storefront or native/licensing
activation is provided; actual SQL Server remains unverified.
