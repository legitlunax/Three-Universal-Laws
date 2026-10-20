# GameTypeAPI: game drivers and normalized capabilities

**Derivation:** GameTypeAPI defines specialized Singularity domain-operation contracts, registered through derived MainAPI and invoked under Order policy. It is not a primary API. Game, engine and schema identities remain independent. See [three-API architecture](API_ARCHITECTURE.md).

**Proposed SDK v1, not implemented.** `GameTypeAPI` allows a folder plugin to add support for a *kind of game* without teaching the CMS core about MU Online, Minecraft, Metin2 or any other specific game's databases or wire protocols.

## 1. Three identifiers, never confuse them

- `gameTypeId`: stable game family, e.g. `mu.online`, `minecraft.java` (illustrative future game only).
- `gameProfileId` / `realmId`: website-admin configured server/realm with its own endpoint, account topology, game type, transport, adapters, capabilities and security policy.
- `schemaAdapterId`: independently installed provider mapping that game's actual layout, e.g. `mu.emulator.muemu.s6`. The game type does not automatically imply an emulator format.

Profiles and choices are stored authoritatively in website-owned MSSQL, **not** editable manifest files. Missing provider yields unavailable state; never auto-reassign a game profile to a different emulator/provider.

## 2. GameType provider interface (illustrative PHP 7.4 pseudocode)

```php
interface GameTypeProvider
{
    public function id(): string;                  // "mu.online"
    public function version(): string;             // semver
    public function describe(): GameDefinition;    // supported concepts/capabilities
    public function validateProfile(GameProfile $profile): ValidationReport;
    public function bindReads(GameProfile $profile, VerifiedSchema $schema): GameReadPorts;
}
```

Example registration when `/plugins/zenda.game.mu/` exists and is approved:

```php
final class MuPlugin implements PluginEntrypoint
{
    public function register(PluginContext $ctx): void
    {
        $ctx->gameTypes()->register('mu.online', '1', MuGameType::class);
    }
}
```

A `GameReadPorts` bundle is a set of independent typed, permission-filtered, bounded projections, **not** a raw generic SQL connection. Possible normalized ports:

| Port/capability | Purpose |
| --- | --- |
| `game.server.status.read@1` | server/realm status (may use API, RCON or DB adapter) |
| `game.players.read@1` | player directory and normalized account references |
| `game.characters.read@1` | names/classes/levels with provenance and realm IDs |
| `game.rankings.read@1` | server-authorized ranking page data |
| `game.guilds.read@1` | read guild, member and roster details where supported |
| `game.inventory.read@1` | read supported item/equipment projections for verified codec |
| `game.currency.read@1` | read named balances, no generalized assumption about currencies |
| `game.mutation.request@1` | only a **guarded command gateway** when full native execution is verified |

These capabilities are *optional per profile*: not every game, season, emulator or DB schema provides them. The kernel and independent plugins must not assume they exist. A template widget asks for a capability and a selected realm, not a path to native database tables.

## 3. Game-specific extensions

Use namespaced additional ports when genuinely necessary, such as `mu.chaos-mix.read@1` or `minecraft.server.whitelist.manage@1` (future example). Do not contaminate generic interfaces with MU inventory bytes, WCoin tables, GameID slots or Webzen seasons. Game-specific private implementation remains inside the GameType plugin/adapter; consumers use published ports only.

## 4. Consuming GameTypeAPI from a generic feature plugin

`zenda.rankings` asks for `game.rankings.read@1` for the selected realm. If no verified provider exists, it shows `UNAVAILABLE` for that widget/profile while other CMS pages remain usable. It must **not** query game tables directly. `zenda.webshop` additionally needs a supported item catalog and the protected native effect gateway before any game delivery; it may render a read-only preview without native writes.

## 5. Game writes

A game provider's presence or SQL connectivity **never** authorizes native game mutation. The existing protected mutation boundary must enforce: actor ownership, permission, online/offline state as required, game/realm identity, lock coordination, idempotency/replay, escrow/currency and item serial integrity, commit acknowledgements, audit and rollback/recovery. No plugin may directly grant itself write capabilities. Unsupported game writes return explicit `UNAVAILABLE` and cannot be silently approximated.

## 6. Acceptance tests

With only `zenda.game.mu`, report game type available but required DB/schema capabilities missing where applicable. Removing it blocks its dependent profiles, not kernel/News. Adding another game type does not require changing static core switch statements. A module consuming `game.rankings.read@1` works unchanged against two supported game types if their providers implement the same normalized contract. Verify profile selection, auth, multi-realm identity isolation and missing-provider renderer behavior.
