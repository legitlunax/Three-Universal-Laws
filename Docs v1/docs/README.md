# Documentation map

Read [STATE](../STATE.md), [AGENTS](../AGENTS.md) and [PROJECT](../PROJECT.md) first.
STATE is the only active progress ledger. Historical snapshots live in
[archive](archive/); their original relative links are interpreted from the
project root, and their old next actions do not override STATE.

| Area | Canonical entry / supporting detail |
| --- | --- |
| Three primary APIs | [Summary](../API%20Summary.md), [Chaos](api/CHAOS.md), [Order](api/ORDER.md), [Singularity](api/SINGULARITY.md), [dataflow engine](api/DATAFLOW.md) |
| Implemented API profile and consumer migration | [Website integration](api/WEBSITE_INTEGRATION.md), [derived contracts](../API_ARCHITECTURE.md) |
| URL, Math, Style, Feature and Language derivatives | [Named URLs](api/URL.md), [declarative formulas](api/MATH.md), [mobile-first design tokens](api/STYLE.md), [optional feature switches](api/FEATURE.md), [multi-language messages](api/LOCALE.md) |
| Execution sequence | [Implementation plan](IMPLEMENTATION_PLAN.md) |
| Folder plugins and package format | [System](../PLUGIN_SYSTEM.md), [authoring](../PLUGIN_DEVELOPMENT.md), [manifest](PLUGIN_MANIFEST.md), [consumer migration](PLUGIN_RUNTIME_MIGRATION.md) |
| Plugin trust / persistence | [Approvals](PLUGIN_APPROVAL_STORAGE.md), [metadata and inert migration review](PLUGIN_METADATA_STORAGE.md), [read-only physical schema inspection](PLUGIN_SCHEMA_INSPECTION.md) |
| Verified installed website schema | [Installed state adoption](PLUGIN_INSTALLATION_STORAGE.md) |
| Existing architecture / contracts / safety | [Architecture](../ARCHITECTURE.md), [contracts](../CONTRACTS.md), [invariants](../INVARIANTS.md), [modules](../MODULES.md) |
| Website MSSQL and topology | [Database boundaries](../DATABASE.md), [settings](WEBSITE_CONFIGURATION.md), [operational settings](OPERATIONAL_CONFIGURATION.md), [SQL lab](MSSQL_LAB.md), [SQL HTTP](MSSQL_HTTP.md) |
| Identity | [Administrator credentials](ADMIN_CREDENTIAL_STORAGE.md), [players](PLAYER_ACCOUNT_STORAGE.md), [passwords](WEBSITE_PASSWORDS.md), [recovery](WEBSITE_RECOVERY.md), [profiles](WEBSITE_PROFILES.md) |
| Templates / editor | [Live layout editor](LIVE_BUILDER.md), [template system](../TEMPLATE_SYSTEM.md), [GrapesJS](../GRAPESJS_INTEGRATION.md), [storage](TEMPLATE_STORAGE.md), [components](../COMPONENT_DEVELOPMENT.md) |
| Features | [Drupal news](DRUPAL_NEWS.md), [support](PLAYER_SUPPORT.md), [events](EVENT_TIMETABLE.md), [siege/loot](SIEGE_AND_LOOT.md), [market](MARKETPLACE.md), [premium catalogs](PREMIUM_CATALOGS.md) |
| Installation / recovery | [Lifecycle](INSTALLATION_LIFECYCLE.md), [rollback](WEBSITE_ROLLBACK.md) |
| Manual XAMPP 8.2 setup for operators/customers | [Extensions, GD, SQL Server drivers, CLI/Apache checks and troubleshooting](installation/XAMPP_8_2.md) |
| Development / verification | [Testing](../TESTING.md), [sandbox](SANDBOX.md), [workspace](WORKSPACE.md), [Codex](CODEX.md), [Antigravity](ANTIGRAVITY.md), [MiniMax](MINIMAX.md) |
| Legacy discovery (static reference) | [Inventory](discovery/FUNCTIONAL_INVENTORY.md), [migration](discovery/MIGRATION_MATRIX.md), [data](discovery/DATA_AND_INTEGRATIONS.md) |

Existing root filenames remain compatibility entry points. Source owners,
contracts, DDL and fixtures stay at their tested locations. The earlier project
overview and full state are retained as [project snapshot](archive/PROJECT-2026-10-19.md)
and [checkpoint snapshot](archive/STATE-2026-10-19.md); safety and residue restrictions
recorded there still apply.
