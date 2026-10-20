# DatabaseTypeAPI: external game-data transport, NOT website configuration persistence

**Derivation:** DatabaseTypeAPI defines specialized Singularity transport-operation contracts governed by Order and registered through derived MainAPI. It is not a primary API and cannot supply the mandatory kernel website MSSQL store. See [three-API architecture](API_ARCHITECTURE.md).

**Proposed SDK v1; not implemented.** `DatabaseTypeAPI` lets trusted plugins add support for different **game data transports** (Microsoft SQL Server, MySQL, PostgreSQL, or future non-SQL remote APIs). It is deliberately **not** a way to swap ZendaMod's mandatory website-owned MSSQL persistence. `DATABASE.md` and `INVARIANTS.md` keep website settings, approvals, histories, template revisions, separate administrator identities and plugin state authoritative in website MSSQL.

## 1. Critical distinction

```text
        ZendaMod trusted Kernel
          |
          +--> WebsiteStore (MSSQL; built into kernel, persists settings/admin/plugins)
          |
          +--> DatabaseTypeAPI (optional provider in /plugins; only external game data)
                    +--> zenda.db.mssql/ (MU game/account data transport)
                    +--> zenda.db.mysql/ (future game DB transport)
                    +--> zenda.db.pgsql/ (future game DB transport)
                    +--> zenda.db.http/  (future verified HTTP/REST integration)
```

The current game/account topology may use `MuOnline`, `Me_MuOnline` and a separate website database, or unified/split configurations, but even if physically co-located they are **different logical ownership and permission realms**. Removing an optional `zenda.db.mssql` plugin must not remove the kernel's mandatory MSSQL ability to load the website/AdminCP. The transport provider cannot write website installation state as a side effect of discovery.

## 2. Illustrative interfaces

```php
interface DatabaseTypeProvider
{
    public function driverId(): string;              // "mssql", "mysql", "pgsql"
    public function describe(): DatabaseDriverInfo;
    public function checkAvailability(): ValidationReport;
    public function openRestricted(
        ExternalConnectionProfile $profile,
        ConnectionPurpose $purpose
    ): ExternalReadSession;                          // scoped, bounded read facade
}

interface ExternalReadSession
{
    public function inspectSchema(SchemaInspectionRequest $request): SchemaMetadata;
    public function executeTypedRead(TypedQueryPlan $plan): BoundedResultSet;
    public function close(): void;
}
```

**Conceptual, not runnable**: the actual contract should avoid public arbitrary SQL strings and `PDO` exposure. A typed read plan must come only from an approved schema adapter or an approved server-side read port, with allowlisted entities, bounded result sizes/timeouts, parameterized statements, and source-scoped authorizations. Real SQL driver/runtime support must be verified for each supported PHP version and deployment environment; do not pretend PDO_ODBC, sqlsrv, pdo_mysql and pdo_pgsql are interchangeable without tests.

## 3. Capability and configuration separation

- `zenda.db.mssql/` declares external driver capability `database.external.mssql@1` and registers `DatabaseTypeProvider` in `MainAPI`.
- `zenda.game.mu/` declares the game family, not its underlying SQL vendor.
- `zenda.schema.muemu/` declares it can interpret specific verified MUEMU layouts after receiving restricted metadata/typed read access from the game-data transport.
- Website admin stores a `ConnectionProfile` with `id`, `purpose=game|account`, `driverId`, `realmId`, transport options and approved bindings; secrets must live in a suitable secret store/environment protected from normal plugin settings and responses.
- Only approved, server-owned profile associations can be opened. A template, webhook, route parameter or arbitrary plugin manifest cannot supply DSNs, passwords, raw table names or direct mutation flags.
- Driver supports introspection **read-only**, never DDL/UPDATE during passive detection. Installer/migrations have separate explicit guarded permissions and never touch native game tables on plugin discovery.
- A `GameType` may use an official HTTP API/RCON instead of a SQL driver where supported, but it must expose the same normalized feature capability contracts and verification requirements.

## 4. Schema != database engine

`mssql` means the wire protocol and SQL dialect, **not** that a server uses MUEMU, Louis, IGCN, MuDevs, X-Team or any particular Season's column/blob layout. Native schema mapping belongs solely to `SchemaAdapterAPI.md`. Do not put emulator-specific `SELECT` statements in generic database drivers.

## 5. Safety and failure

Even trusted in-process PHP can technically bypass APIs if it has OS-level access to secrets; the architecture imposes boundaries for cooperating reviewed plugins, not a hostile-code sandbox. Prefer separate least-privilege DB principals for website vs game reads, narrow allowlists, prepared params and explicit query budgets. Game mutation occurs only through a verified protected gateway outside ordinary `ExternalReadSession`, not by returning raw PDO or exposing generic `executeWrite`.

Removing `zenda.db.mssql/` makes only bound external game profiles unavailable next request. Stored profiles and game/website records remain unchanged. Inactive providers must never be resurrected from a stale cached registry. No automatic alternate-driver fallback or schema re-selection.

## 6. Test cases

- Core website login/AdminCP persists when all optional DatabaseType plugins are absent.
- Adding/removing one DB provider updates its external game profiles next request; unrelated providers unaffected.
- Wrong dialect, missing extensions, invalid TLS certificate, Unicode/binary round-trip, permission denial, timeouts and failed commits are explicitly tested per driver.
- Native read-only transport cannot run writes/DDL, including through malformed input or accidental reuse of installer roles.
- A verified schema adapter can use bounded schema metadata/typed read sessions without privileged access to unrelated databases.
- Profiles/configuration survive removal in website MSSQL and cannot leak secrets to events/widgets/client JSON.
