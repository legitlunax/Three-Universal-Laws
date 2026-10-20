---
name: mu-online-web-dev
description: Expert guide, system architect, and code generator for MU Online web development with full compatibility from Classic 0.97d up to Season 21. Always adapts dynamically to any MU Online database schema or emulator (IGCN, X-Team, MuDevs, Louis, SCF/TitansTech, ZTeam, MuEngine, OpenMU, and 97d-S21). Use when building websites, custom web engines, or CMS modules (WebEngine, DmN, MuCore), introspecting varying MSSQL/PostgreSQL schemas, handling 5-slot or 10-slot AccountCharacter tables (GameID1..GameID10), supporting all 16 classes (Dark Wizard through Season 20 Alchemist and Season 21 Crusader), parsing character inventory and warehouse binary/hex blobs (7B/10B, 16B, 20B, 32B, extended inventory, Pentagrams, Earrings, Muun, Artifacts, Gremory Case, multi-vault, and extended warehouse), managing VIP systems, currencies (Zen, WCoinC/P, Goblin Points, Ruud, Jewel Bank), character stats, resets, Master/Majestic/4th-5th class progressions, guild management with 8x8 mark decoding, Castle Siege cycles, competitive rankings, live event timers, WebShop, VIP Shop, Player Item and Character Market with escrow, web-based Chaos Machine workshops, Vote Rewards, and styling authentic Webzen dark-fantasy UI components, inventory grids, and item tooltips.
version: 1.2.0
---

# MU Online Web Development & Database Architecture (0.97d to Season 21)

A complete reference and code-generation guide for building MU Online websites, custom CMS platforms, player armories, and web commerce systems across every game era from **Classic 0.97d up to Season 21**. Follow these specifications when querying databases, adapting to different server emulators, manipulating binary item blobs, implementing game mechanics on the web, or designing classic Webzen dark-fantasy interfaces.

---

## 1. Era Compatibility Matrix (0.97d to Season 21)

Always identify or auto-detect the target server's era and emulator (IGCN, X-Team, MuDevs, Louis, SCF/TitansTech, ZTeam, MuEngine, OpenMU) so queries, parsers, and UI components scale seamlessly:

| Era / Version | Classes & Max Slots | Item Hex Size | Progression & Key Subsystems |
|---|---|---|---|
| **Classic (0.97d - Season 1)** | 4-5 Classes (DW, DK, Elf, MG, +DL in 0.99b), 5 Character Slots (`GameID1..5`) | **7B or 10B** (`14` or `20` hex chars) | Max Level 350/400, 8x8 Inventory, 8x15 Vault (120 slots), Standard & Excellent items, Ancient sets (0.99b+). |
| **Golden Era (Season 2 - Season 6 Ep 3)** | 6-7 Classes (+Summoner in S3, +Rage Fighter in S6), 5 Character Slots | **16B** (`32` hex chars) | 380-Level PvP options, Jewel of Harmony, 5-Socket Seed Spheres (S4+), 3rd Class & Master Skill Tree (`MasterSkillTree`), 2 Expanded Backpacks, Extended Vault (240 slots), Gens (Duprian/Vanert), CashShop (`WCoinC`/`P`/`GP`). |
| **Elemental & Mastery Era (Season 8 - Season 15)** | 8-10 Classes (+Grow Lancer, Rune Wizard, Slayer), **10 Character Slots** (`GameID1..10`) | **20B or 32B** (`40` or `64` hex chars) | Pentagram & Errtel Elemental system, Muun Inventory (`MuunInventory`), Gremory Case rewards, **Ruud** currency, Mastery Sets (Bloodangel to Manticore), 4th Class & Majestic Skill Tree (`MajesticLevel`), 4 Backpacks, 4th Wings. |
| **Modern & Legendary Era (Season 16 - Season 21)** | **All 16 Classes** (+Gun Crusher, White Wizard, Lemuria Mage, Illusion Knight, S20 Alchemist, **S21 Crusader**), 10 Character Slots | **32B** (`64` hex chars) | Max Level 1500-1800 (`cLevel` + `MasterLevel`/`MajesticLevel`), Ancestral/Compassion Earrings, Guardian Mounts, Spider Artifacts (`ArtifactInventory`), Ability Cards, 5th Wings & Wing Core, Mastery through Apocalypse/Lightning & **1st/2nd Legendary Gear (Ancestral Imperial)**, Elite Zones (Sanctuary of Aquilas). |

---

## 2. Dynamic Database Adaptation & Schema Introspection

**Never assume a single hardcoded schema.** Always use a **Schema Adapter / Config Mapping Layer** or auto-detect columns via `INFORMATION_SCHEMA.COLUMNS`.

### Three-Part Database Topology & Connection Configuration

MU Online web deployments frequently split their data stores across three distinct logical or physical databases. Systems must support configuring these independently or bound together:
1. **Game Data** (MSSQL `MuOnline` database):
   - Characters (`Character`), progression, inventory blobs (`Inventory`), vaults (`warehouse`, `ExtWarehouse`), guilds (`Guild`, `GuildMember`), Castle Siege (`MuCastle_DATA`), and game event schedules.
2. **Account Data** (MSSQL `Me_MuOnline` or `MuOnline` database):
   - Master accounts (`MEMB_INFO`), live server connection states (`MEMB_STAT`), character slot maps (`AccountCharacter`), and cash shop currencies (`T_InGameShop_Point`, `CashShopData`).
3. **Web Data** (SQLite offline prototype or MSSQL website database):
   - Website-owned settings, admin accounts, market listings & escrow records, visual templates (`zenda_template_head`, `zenda_template_revision`), timetables, drop guides, tickets, and audit logs.

**Topology Flexibility:**
- **Unified Single MSSQL Database**: Game, Account, and Web data all reside in a single database (e.g. `MuOnline`).
- **3-Way Split**: Separate Game DB (`MuOnline`), separate Account DB (`Me_MuOnline`), and an independent MSSQL database (`ZendaWeb`) or SQLite file for Web Data.
- **Shared Game/Account + Independent Web**: Game and Account data share `MuOnline`, while Web Data uses a dedicated DB.
- **Multi-Realm Architectures**: Multiple Game DBs (e.g. `MuOnline_Realm1`, `MuOnline_Realm2`) sharing a centralized `Me_MuOnline` and Web DB.
- Connection inputs (driver, host, port, database, user, password) must allow independent target specification or shared connection pooling.

### Open-Source Core & Commercial Encrypted Module Model
- **Open-Source Unencrypted Core**: Foundations, CMS, templates/theme system, player identity, basic views, rankings, drop guides, and news are open-source and unencrypted.
- **Commercial Encrypted Modules**: High-value gameplay and monetization modules (Visual WebShop, Player Item Market, Character Marketplace, Guilds & Alliances, Castle Siege, and Payment Gateways) may be distributed encrypted under individual commercial licenses required to activate each function for sale.
- **Implementation Rule**: Maintain strict decoupling. Code generators and agents must ensure module boundaries, declared ports, and capability gates isolate commercial features cleanly from open-source core contracts.

### Dynamic Schema Auto-Detection Rules

1. **Character Slots per Account (5 vs 10 Slots):**
   - Classic 0.97d through Season 8 use `GameID1` through `GameID5` (5 characters max) in `AccountCharacter`.
   - Season 9 through Season 21 (IGCN, X-Team, MuDevs) expand `AccountCharacter` to **10 character slots** (`GameID1` through `GameID10`).
   - Dynamically query `INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'AccountCharacter' AND COLUMN_NAME LIKE 'GameID[0-9]%'` so account dashboards, character switchers, and Character Market transfers work on 5-slot, 10-slot, or custom schemas.
2. **Level, Master Level & 4th Class (Majestic) Unified Display:**
   - In 97d-S2, total level is `Character.cLevel` (max 350/400).
   - In S3-S12, total level is `Character.cLevel + MasterSkillTree.MasterLevel`.
   - In S13-S21, total character level (up to Lv. 1750-1800) is either stored directly in `Character.cLevel` + `mLevel` + `MajesticLevel`, or split across `Character`, `MasterSkillTree` (`T_MasterLevelSystem`), and `T_EnhancementSkillTree`. Auto-detect and sum active level columns for rankings and reset requirements.
3. **Reset & Grand Reset Columns:**
   - **Resets:** Map dynamically across `Resets`, `ResetCount`, `ResetsCount`, or `Reset`.
   - **Grand Resets:** Map across `GrandResets`, `GResets`, `GrandResetCount`, `MasterReset`, or `MResets`.
4. **VIP & Account Status Columns:**
   - **MuEngine / Louis / X-Team / MuDevs (`MEMB_INFO`):** `AccountLevel` (`0`..`3`) and `AccountExpireDate` (`smalldatetime`).
   - **IGCN:** `T_VIPList` (`AccountID`, `Date`, `Type`).
   - **SCF / TitansTech (`MEMB_INFO`):** `SCFIsVip` (`tinyint`) and `SCFVipDays` (`int`).
5. **Cash Shop, Ruud & Currencies:**
   - Auto-detect coin tables across `CashShopData` (X-Team/Louis/MuDevs), `T_InGameShop_Point` (IGCN), `MEMB_CREDITS` (MuCore/DmN), and `Character.Ruud` (`RuudMoney` in IGCN).
6. **Binary Item Size Auto-Detection:**
   - Inspect `DATALENGTH(Items)` on `warehouse` or `CHARACTER_MAXIMUM_LENGTH` in `INFORMATION_SCHEMA.COLUMNS`:
     - `840` or `1200` bytes (120 slots) -> **7B or 10B/item** (Classic 0.97d-S2).
     - `1920` bytes (120 slots) or `3840` bytes (240 extended slots) -> **16B/item** (Season 3-S6E3, or S8+ with 16B setting).
     - `2400` bytes (120 slots) or `4800` bytes (240 slots) -> **20B/item** (IGCN S9-S12 format).
     - `3840` bytes (120 slots) or `7680` bytes (240 extended slots) -> **32B/item** (Season 8-S21 IGCN/X-Team/MuDevs with 64-bit serials, 5 sockets, Pentagrams, and Mastery/Legendary data).

### Core Database Tables (0.97d to Season 21)

| Table | Key Columns & Emulator Variants | Purpose |
|---|---|---|
| `MEMB_INFO` | `memb___id`, `memb__pwd`, `bloc_code`, `mail_addr`, `AccountLevel` / `SCFIsVip`, `AccountExpireDate` | Master account credentials, ban state, and VIP tier |
| `MEMB_STAT` | `memb___id`, `ConnectStat`, `ServerName`, `IP`, `ConnectTM`, `DisConnectTM` | Live connection state (`1` = Online, `0` = Offline) |
| `AccountCharacter` | `Id`, `GameID1`..`GameID5` (or `GameID1`..`GameID10`), `GameIDC`, `MoveCnt` | Maps 5 to 10 character slots and last active character (`GameIDC`) |
| `Character` | `AccountID`, `Name`, `cLevel`, `Class`, `LevelUpPoint`, `Strength`, `Dexterity`, `Vitality`, `Energy`, `Leadership`, `Money`, `PkLevel`, `PkCount`, `CtlCode`, `Inventory`, `MagicList`, `Resets`, `GrandResets`, `Ruud` | Character attributes, Zen, Ruud, inventory blob, and progression |
| `MasterSkillTree` / `T_MasterLevelSystem` | `Name`, `MasterLevel` (`ML_Level`), `MasterPoint` (`ML_Point`), `MasterSkill`, `MajesticLevel`, `MajesticPoint` | Season 4-21 Master, Majestic (4th Class), and Ability Card skill data |
| `warehouse` | `AccountID`, `Money`, `Items`, `DbVersion`, `pw`, `Items1`..`ItemsN`, `Expanded` | Main vault, vault Zen, lock password, and column-based multi-vaults |
| `ExtWarehouse` / `T_Warehouse_Ex` | `AccountID`, `Number` (`VaultID`), `Items`, `Money` | Row-based multi-warehouse and extended vault storage |
| `MuunInventory` / `T_Muun_Inventory` | `Name`, `Items` (`MuunItems`) | Season 8-21 Muun pet companion inventory blob |
| `ArtifactInventory` / `T_Artifact` | `Name`, `Items` (`ArtifactItems`) | Season 18-21 Spider Artifact inventory and enhancement state |
| `GremoryCase` / `T_GremoryCase` | `AccountID`, `Name`, `StorageType`, `ItemGUID`, `ItemInfo`, `ExpireDate` | Season 10-21 in-game reward delivery box (Hotkey `K`) for web/event rewards |
| `Guild` & `GuildMember` | `G_Name`, `G_Master`, `G_Mark`, `G_Score`, `G_Union`, `Name`, `G_Status` | Guild identity, 32-byte (64-hex) emblem, alliance and roster |
| `MuCastle_DATA` & `MuCastle_REG_SIEGE` | `CASTLE_OCCUPY`, `OWNER_GUILD`, `MONEY`, `TAX_RATE_CHAOS`, `TAX_RATE_STORE`, `REG_SIEGE_GUILD`, `REG_MARKS` | Castle Siege ownership, treasury Zen, tax rates, and Sign of Lord registrations |
| `CashShopData` / `T_InGameShop_Point` | `AccountID`, `WCoinC`, `WCoinP`, `GoblinPoint`, `Ruud` | In-game Cash Shop and mastery currency balances |
| `T_GensSystem` / `Gens_Rank` | `Name`, `Influence` (`Family`), `Contribution`, `Rank` | Gens Duprian (`1`) and Vanert (`2`) faction standings |

---

## 3. All 16 Character Classes & Evolutions (0.97d to Season 21)

Map `Character.Class` across all 5 evolution stages (1st Class -> 2nd Class -> 3rd Class [S3+] -> 4th Class [S13+] -> 5th Class / Renewal [S18-S21]):

| Class Family | Era Added | DB Class Codes (Typical `Character.Class` Range) | Evolutions (1st -> 2nd -> 3rd -> 4th -> 5th) | Primary Stats & Exclusive Gear |
|---|---|---|---|---|
| **Dark Wizard** | 0.97d | `0`, `1`, `2`/`3`, `7`, `15` | Dark Wizard -> Soul Master -> Grand Master -> Soul Wizard -> Darkness Wizard | ENE/AGI; Staffs, Orbs, Shields |
| **Dark Knight** | 0.97d | `16`, `17`, `18`/`19`, `23`, `31` | Dark Knight -> Blade Knight -> Blade Master -> Dragon Knight -> Ignition Knight | STR/AGI/VIT/ENE; Swords, Spears, Shields |
| **Fairy Elf** | 0.97d | `32`, `33`, `34`/`35`, `39`, `47` | Fairy Elf -> Muse Elf -> High Elf -> Noble Elf -> Royal Elf | AGI/STR or ENE; Bows, Crossbows, Quivers |
| **Magic Gladiator** | 0.97d | `48`, `50`/`51`, `54`, `62` | Magic Gladiator -> Duel Master -> Magic Knight -> Duple Knight | STR/AGI or ENE; Two-handed Swords, Staffs (No Helm) |
| **Dark Lord** | 0.99b | `64`, `66`/`67`, `70`, `78` | Dark Lord -> Lord Emperor -> Empire Lord -> Force Empire | STR/ENE/CMD (`Leadership`); Scepters, Dark Horse, Dark Raven |
| **Summoner** | Season 3 | `80`, `81`, `82`/`83`, `87`, `95` | Summoner -> Bloody Summoner -> Dimension Master -> Dimension Summoner -> Endless Summoner | ENE/AGI; Sticks, Spellbooks |
| **Rage Fighter** | Season 6 | `96`, `98`/`99`, `102`, `110` | Rage Fighter -> Fist Master -> Fist Blazer -> Bloody Fighter | STR/VIT or AGI/ENE; Claws/Gloves, Cloak (No Gloves armor slot) |
| **Grow Lancer** | Season 10 | `112`, `114`/`115`, `118`, `126` | Grow Lancer -> Mirage Lancer -> Shining Lancer -> Arcane Lancer | STR/AGI (Retaliation/Rage damage); Lances & Shields |
| **Rune Wizard** | Season 14 | `128`, `129`, `130`/`131`, `135`, `143` | Rune Wizard -> Rune Spell Master -> Grand Rune Master -> Majestic Rune Wizard -> Infinity Rune Wizard | ENE/AGI; Rune Maces & Shields |
| **Slayer** | Season 15 | `144`, `145`, `146`/`147`, `151`, `159` | Slayer -> Royal Slayer -> Master Slayer -> Slaughterer -> Rogue Slayer | STR/AGI; Dual Short Swords |
| **Gun Crusher** | Season 16 | `160`, `161`, `162`/`163`, `167`, `175` | Gun Crusher -> Gun Breaker -> Master Gun Breaker -> Heist Gun Crusher -> Magnus Gun Crusher | ENE/AGI; Dual Magic Guns |
| **White Wizard** | Season 16.2 | `176`, `177`, `178`/`179`, `183`, `191` | Kundun's Light -> Light Master -> Shine Wizard -> Luminous Wizard -> Glory Wizard | ENE/AGI; Light Staffs & Magic Books |
| **Lemuria Mage** | Season 16.2 | `192`, `193`, `194`/`195`, `199`, `207` | Lemuria Mage -> Warmage -> Archmage -> Mystic Mage -> Battle Mage | ENE/AGI; Orbs |
| **Illusion Knight** | Season 18 | `208`, `209`, `210`/`211`, `215`, `223` | Illusion Knight -> Mirage Knight -> Illusion Master -> Mystic Knight -> Phantom Pain Knight | AGI/STR; Curved Blades |
| **Alchemist** | Season 20 | `224`, `225`, `226`/`227`, `231`, `239` | Alchemist -> Alchemic Magician -> Alchemic Master -> Alchemic Force -> Creator | ENE/AGI; Magic Wands & Elixirs |
| **Crusader** | Season 21 | `240`, `241`, `242`/`243`, `247`, `255` | Crusader -> Impact Crusader -> Master Paladin -> Sacred Paladin -> Templar Commander | STR/AGI + ENE (Lugard's Blessing & **Holy Attack** stat); Hammers & Paladin Shields |

*Note: Some emulators (X-Team vs IGCN vs MuDevs) shift class bitmasks slightly in the lower 4 bits (`Class & 0xF0` always identifies the base class family `(0..15) << 4`). Always extract the class family using `(Class >> 4) & 0x0F`.*

---

## 4. Inventory, Warehouse & Binary Item Formats (7B/10B, 16B, 20B, 32B)

### Character Inventory Slot Map (0.97d to Season 21)

- **Core Equipped Gear (Slots 0 to 11 - All Versions):**
  - `0`: Left Hand (Primary Weapon)
  - `1`: Right Hand (Shield / Dual Weapon / Arrows / Quiver / Orb / Elixir / Paladin Shield)
  - `2`: Helm
  - `3`: Armor
  - `4`: Pants
  - `5`: Gloves
  - `6`: Boots
  - `7`: Wings / Cape / Cloak (1st to 5th Wings)
  - `8`: Helper / Guardian Pet (Imp, Angel, Dinorant, Dark Horse, Fenrir, Demon)
  - `9`: Pendant / Necklace
  - `10`: Left Ring
  - `11`: Right Ring
- **Main Inventory (Slots 12 to 75 - All Versions):** 64 slots (8 columns x 8 rows).
- **Expanded Backpacks (Season 6 to Season 21):**
  - Backpack 1 (Slots 76 to 107): 32 slots (8x4 grid).
  - Backpack 2 (Slots 108 to 139): 32 slots (8x4 grid).
  - Backpack 3 & 4 (Slots 140 to 203 in S8-S21): Two additional 8x4 grids (64 slots).
- **Personal Store (Slots 204 to 235):** 32 slots (8x4 grid).
- **Modern Equipped Slots (Slots 236 to 240+ in Season 8 to Season 21):**
  - `236`: Pentagram (Elemental Gear with 5 Errtel slots: Anger, Blessing, Integrity, Divinity, Gale).
  - `237`: Left Earring (Rage / Ancestral / Honor / Blood / Compassion Earring).
  - `238`: Right Earring.
  - `239`+: Guardian Mount (Ghost Horse, Ice Dragon, Pierce Lion, Shining Tail, Ur) / Muun slot.

### Multi-Format Binary Item Decoding

#### A. Classic 10-Byte (20-Hex) Format (0.97d - Season 2)
- `B[0]`: Item Index (`B[0] & 0x1F`) + Category low bit.
- `B[1]`: Skill (`bit 7`), Level (`bits 3-6`), Luck (`bit 2`), Option low (`bits 0-1`).
- `B[2]`: Durability (`0-255`).
- `B[3..6]`: 32-bit Item Serial.
- `B[7]`: Category high bits (`bits 5-7`), Option +16 (`bit 6`), Excellent bitmask (`bits 0-5`).
- `B[8]`: Ancient Set option (`0x05` / `0x0A`).
- `B[9]`: Item Group/Category (`(B[9] >> 4) & 0x0F`). *(7-byte 0.97d omits `B[7..9]` extended fields and encodes Category in `B[0]`/`B[7]`).*

#### B. Standard 16-Byte (32-Hex) Format (Season 3 - Season 6 Ep 3)
```text
Byte 00: Item Index Low (bits 0-7)
Byte 01: [Bit 7: Skill] [Bits 3-6: Level (0-15)] [Bit 2: Luck] [Bits 0-1: Life Option Low]
Byte 02: Durability (0-255)
Bytes 03-06: 32-bit Item Serial Number (Big-Endian)
Byte 07: [Bit 7: Item Index High] [Bit 6: Life Option High (+16)] [Bits 0-5: Excellent Option Bitmask]
Byte 08: Ancient Set Flag (Tier 1 / Tier 2 + Stamina)
Byte 09: [Bits 4-7: Item Category (0-15)] [Bit 3: 380-Level PvP Option] [Bits 0-2: Extended Index]
Byte 10: Jewel of Harmony [Bits 4-7: Harmony Option Type] [Bits 0-3: Harmony Option Level]
Bytes 11-15: Socket Slots 1 to 5 (0xFF = No Socket, 0xFE = Empty Socket, 0x00-0xFD = Seed Sphere ID)