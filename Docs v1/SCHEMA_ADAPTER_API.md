# SchemaAdapterAPI: Game emulator detection and verified mapping

**Derivation:** SchemaAdapterAPI defines specialized Singularity probe/mapping contracts governed by Order and registered through derived MainAPI. It is not a primary API; read-only probes confer no native-write authority. See [three-API architecture](API_ARCHITECTURE.md).

**Proposed SDK v1; not implemented.** This is deliberately separate from GameType and DatabaseType. It answers: *"I know this is MU Online, and the DB driver is MSSQL. Which particular emulator/schema/layout do these tables and inventory blobs actually use, and which functions can we safely support?"*

## 1. Folder packaging

```text
/plugins/
  zenda.game.mu/
  zenda.db.mssql/
  zenda.schema.muemu/
  zenda.schema.louis/
  zenda.schema.xteam/
```

Each adapter is an independent plugin package, potentially free or premium.
The canonical manifest in docs/PLUGIN_MANIFEST.md declares `providers` entries
with `kind: schemaAdapter`, its independent `schemaAdapterApi` range, required
dependencies and proposed versioned capabilities. Supported game/database types
are described through the future typed adapter API, not extra ad hoc manifest
fields. Removing an adapter deactivates only its bound profiles and required
consumers once runtime provider binding is implemented.

## 2. Interface pseudocode

```php
interface GameSchemaAdapter
{
    public function id(): string; // "mu.emulator.muemu.s6"
    public function supportedGameType(): string; // "mu.online"
    public function acceptedDatabaseTypes(): array; // ["mssql"]
    public function probe(ReadOnlySchemaInspector $inspector): SchemaProbeReport;
    public function validateMapping(
        SchemaProbeReport $probe,
        OperatorReview $review
    ): VerifiedSchemaProfile;
    public function readPorts(VerifiedSchemaProfile $profile): GameReadPorts;
}
```

Interfaces are aspirational contract designs. A `SchemaProbeReport` contains detected candidates, confidence/evidence, unknown columns, table/column encodings, warehouse/inventory codec candidates, expected PK/row shape, actor identity mapping, capability-level `verified | partial | unsupported` statuses, and redacted provenance. **Confidence is diagnostic, not authorization**. Approval requires deterministic validation against reviewed fixtures and actual designated sandbox reads when the use case needs them.

## 3. Detection pipeline (never arbitrary auto-guessing)

1. Operator selects a bounded game/account connection profile and an installed game type.
2. DatabaseType returns authorized bounded read-only schema metadata; no native writes or automatic DDL.
3. Available matching SchemaAdapter plugins inspect declared fingerprints and propose compatible candidates.
4. Reject no-match, conflicting candidates, mixed schemas, missing fields and unsupported blob formats rather than guessing.
5. Admin reviews specific detected candidates and binds one verified adapter+version to a game profile. Save mapping/history in website-owned MSSQL, not the plugin folder.
6. Publish only capabilities whose layout, query and codec are verified for that profile. Partial capabilities remain unavailable or read-only.
7. Re-evaluate after schema changes/version updates; never silently swap in another adapter or allow unsafe native write privileges.

## 4. Example: same engine, different MU Online versions

Two installations may both use SQL Server, yet one exposes MUEMU Season 6 `AccountCharacter` five-slot layout and 16-byte inventory, while another has a custom Season 21 layout and a different encoding. Both require `zenda.db.mssql`, but cannot share hardcoded query/codecs. The appropriate `zenda.schema.*` plugin establishes the mapping and decoders per verified profile. Any listed lengths or emulator names are candidates requiring verification, not unconditional claims of compatibility.

## 5. Interaction with other plugins

`zenda.rankings` uses a normalized `game.rankings.read@1` port; `zenda.webshop` requests a verified item read capability and guarded command gateway. Neither directly detects native schema or imports adapter code. `zenda.game.mu` owns game-specific domain meaning and mapping interfaces while database transport remains game-neutral. If the schema adapter folder vanishes, a saved GrapesJS ranking block displays a safe missing-data diagnostic and keeps its editable JSON.

## 6. Acceptance tests

Missing adapter; ambiguous two-adapter match; corrupt schema; wrong database type; wrong game type; modified table layout; unknown binary codec; partial read support; required vs optional capability; multi-realm isolation; selected provider removed and re-added; stale cache; no native game writes or unauthorized table access; no user/player secrets exposed in inspection reports. No universal emulator compatibility claim from schema metadata alone.
