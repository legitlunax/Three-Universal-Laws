# ZendaMod Next

Start with [PROJECT.md](PROJECT.md) for the concise source/ownership map,
[API Summary.md](API%20Summary.md) for the three primary APIs, and
[docs/README.md](docs/README.md) for organized documentation.
Chaos plans, Order governs execution, and Singularity implements focused operations.
The private runtime bridge covers health, synthetic rankings and URL/Math derivatives;
the rest of the CMS and folder-plugin lifecycle continue through the staged plan.
Active XAMPP CLI: PHP 8.2.12; source compatibility target remains PHP 7.4.
See [STATE.md](STATE.md) for actual evidence and the next step.
For manual setup and future customers, follow the
[XAMPP 8.2 extension and SQL Server driver guide](docs/installation/XAMPP_8_2.md).

The [self-hosted live template editor](docs/LIVE_BUILDER.md) now includes
GrapesJS, Material colors, native responsive layouts, Moveable resizing and
Floating UI help. [Named URLs](docs/api/URL.md) and [declarative formulas](docs/api/MATH.md)
drive its first migrated consumers. Publication and actual MSSQL template cutover
remain pending; see STATE for evidence and remaining migrations.

Decision 106 adds separate kernel plugin configuration and retained, unverified
migration-attempt receipts, with offline and owned SQL lab evidence. Step 03
still needs verified installed migrations and guarded operator provisioning;
executable loading and HTTP/worker lifecycle remain pending.
See [metadata storage](docs/PLUGIN_METADATA_STORAGE.md),
[approval storage](docs/PLUGIN_APPROVAL_STORAGE.md) and
STATE.md for the current checkpoint.

Decision 104 implements bounded **data-only** plugin manifest/package discovery.
Decision 105 adds its approval ledger; executable loading, SDK and live HTTP
lifecycle remain pending.
See [canonical manifest rules](docs/PLUGIN_MANIFEST.md) and the
[runtime migration map](docs/PLUGIN_RUNTIME_MIGRATION.md); `STATE.md` remains
the implementation checkpoint.

## Derived extension contracts (planned)

The plugin ecosystem retains four specialized, independently versioned contracts:
**MainAPI** (Order's plugin host), **GameTypeAPI** (Singularity game contracts),
**DatabaseTypeAPI** (Singularity external transport contracts) and **SchemaAdapterAPI**
(Singularity schema contracts), all governed by Order. The kernel's website-owned
MSSQL persistence is mandatory even without plugins. See [API architecture](API_ARCHITECTURE.md).
The executable plugin SDK remains planned; the private read gateway is implemented.


## Plugin-first direction (new architectural goal)

**Goal, not yet implemented:** place a trusted, compatible folder at
`plugins/<plugin-id>/` and its routes, AdminCP integrations, event listeners,
services and GrapesJS widgets become available; physically remove that folder and
those contributions disappear on the next request **without breaking the core
or unrelated plugins**, while website-owned MSSQL state is retained. Unknown
code requires approval; removal is not data deletion. This is **filesystem plugin
drag-and-drop**, separate from the visual GrapesJS drag-and-drop builder.

Start with [PLUGIN_SYSTEM.md](PLUGIN_SYSTEM.md) (normative behavior),
[PLUGIN_DEVELOPMENT.md](PLUGIN_DEVELOPMENT.md) (package API), and
[PLUGIN_IMPLEMENTATION_PROMPT.md](PLUGIN_IMPLEMENTATION_PROMPT.md) (pasteable
coding-agent assignment). Existing static modules and partial PHP runtime have
not been converted just by updating these Markdown requirements.

Independent reconstruction of the legacy MU Online CMS. **Current milestone:
an offline phase 4 implementation**, including the public portal, separate
website-player/staff sessions, private support tickets, editable drop/event guides,
and synthetic MU market/Siege preparation. Native transfers and rewards remain
disabled. The user approved continuing
after the phases 1–3 review on 2026-10-17.

Open [the multi-root workspace](../ZendaMod-Next.code-workspace) in VS Code or
Cursor. Read [STATE.md](STATE.md) for actual status and the next step, then
[AGENTS.md](AGENTS.md) before changing code. For Gemini or Google Antigravity,
use [the continuation guide](docs/ANTIGRAVITY.md); all agents share the same
checkpoint and project instructions. The original `C:/xampp/htdocs/ZendaMod`
remains strictly read-only and must never be executed by this project.

When running Minimax models (MiniMax-M3.1-Flash-Preview, MiniMax-M3,
MiniMax-M2.7), read [the Minimax continuation guide](docs/MINIMAX.md). It documents
the same canonical `STATE.md`/`AGENTS.md` workflow plus the per-task model and
reasoning-effort choice, since Minimax exposes no automatic selector. Provider
credentials remain in user-level agent configuration and are never stored in this
repository.

To preview the website, run from this directory:

```powershell
& 'C:/xampp/php/php.exe' -S 127.0.0.1:8091 -t public public/router.php
```

Open `http://127.0.0.1:8091/`. Administration has no default credentials.
Follow [sandbox setup](docs/SANDBOX.md) to create a local administrator and
understand private storage, supported routes and current limits.

Register a website player at `/register`, sign in at `/login`, and use `/support`.
Staff use `/admin/support` and `/admin/drops`; public guide/Siege views are at
`/drops` and `/siege`. Read [support scope](docs/PLAYER_SUPPORT.md),
[Siege/drop scope](docs/SIEGE_AND_LOOT.md) and [market scope](docs/MARKETPLACE.md)
before treating any demonstration data as live integration.

Staff maintain UTC event schedules at `/admin/events`; `/events` publishes the
saved timetable and offers a bounded calendar download at `/events/calendar`.
Read [event timetable scope](docs/EVENT_TIMETABLE.md) for storage and calendar limits.

Players use `/account/profile` for private-by-default display/bio preferences and
`/account/profile/preview` to review saved fields. `/player` publishes opted-in
website profiles; matching names establish no native ownership. Read
[profile policy and compatibility](docs/WEBSITE_PROFILES.md) before rollback.

Verify from this directory:

```powershell
& ./scripts/verify.ps1 -Level complete
& 'C:/xampp/php/php.exe' scripts/impact.php --changed=src/Modules/GameData/Domain/Inventory.php
```

On hosts with PHP and Composer on PATH, `composer verify` runs the same command.
No Composer runtime packages are required. The browser editor ships compiled
assets; rebuilding requires the pinned npm dependencies. See
[TESTING.md](TESTING.md) for levels, evidence and coverage limits.

MU Online is the initial target. Preserve the possibility of future game
adapters; Metin2, Minecraft and other game integrations are not implemented.
Game accounts, game databases, email, payments and game writes remain disconnected.
The restricted GrapesJS editor is implemented; complete production modules remain planned. The implemented
partial slice is recorded in [architecture/runtime.json](architecture/runtime.json).

Reuse the [functional inventory](docs/discovery/FUNCTIONAL_INVENTORY.md),
[migration matrix](docs/discovery/MIGRATION_MATRIX.md),
[architecture](ARCHITECTURE.md), [contracts](CONTRACTS.md), [current and target
architecture diagrams](docs/ARCHITECTURE_DIAGRAMS.md) ([interactive HTML
treeview](docs/ARCHITECTURE_DIAGRAMS.html)), and the
[enterprise architecture gap assessment](docs/ARCHITECTURE_GAP_ASSESSMENT.md)
and [recommended engineering methods](docs/ENGINEERING_METHODS.md) before
planning production readiness. To request an independent advanced-AI review,
use the [architecture review prompt](docs/ARCHITECTURE_REVIEW_PROMPT.md); the
[fact-checked synthesis of the returned external review](docs/EXTERNAL_ARCHITECTURE_REVIEW.md)
records its validated findings. See also [workspace configuration](docs/WORKSPACE.md)
and the [phases 1–3 review](docs/PHASE_1_3_REVIEW.md), which records the historical checkpoint.

The webroot must be `public/`. Keep private state outside the source and webroot;
the loopback sandbox is not a production deployment. No asset/license copying,
database migration, live integration or deployment has been performed. CI
configuration belongs at the root of a repository containing this application's
files; hosting review rules remain unconfigured.
