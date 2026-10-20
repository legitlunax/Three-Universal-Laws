# Prompt: independent architecture and engineering review

Copy the prompt below into an advanced AI that can inspect the attached
documents and, ideally, browse the web. Attach or paste the project documents
listed in the prompt. Bring the response back to the ZendaMod Next project so it
can be checked against source evidence and documented.

---

## Copy from here

```text
Act as an independent principal software architect and application-security /
reliability reviewer. Review ZendaMod Next as a real product-in-progress, not as
a hypothetical greenfield diagram. Be technically rigorous, practical, skeptical
of unsupported claims, and specific to the repository and its authorization
boundaries.

Your task is analysis and recommendations only. Do not edit code, generate
implementation patches, claim certification, or assert that a documented design
is already implemented. The developer will review and document your findings
afterward.

## Project purpose and product direction

ZendaMod Next is an independent PHP website/CMS intended eventually to reach the
useful capability parity of an existing MU Online website while not depending on
the legacy PHP application at runtime. MU Online is the first game target; future
other-game adapters should remain possible, but do not require implementing
Minecraft, Metin2, or other games now.

The long-term product includes:
- a secure website kernel and a separate website-administrator identity/AdminCP;
- player website accounts and public/private profiles;
- a guided installer, recovery flow, and website-only factory reset;
- MU Online server compatibility: account/game topology, schema/emulator adapters,
  normalized reads, inventories/warehouses, character progression, currencies,
  VIP, rankings, profiles, guilds, Castle Siege, events, drops, support, shops,
  player/character markets, and administrative tools;
- a free/open kernel and independently distributable free plugins; physically
  separate premium packages for paid capabilities; free PayPal/MercadoPago
  gateways must not depend on paid gateway internals; GrapesJS is intended as
  the free visual editor;
- a plugin-first experience: a complete approved folder under
  `plugins/<vendor>.<slug>/` can contribute features without editing kernel
  routes, Composer mappings, or other packages. Removing the folder revokes its
  contributions on the next request while retaining its website data; restoring
  a compatible trusted version may resume retained state.

The plugin design is a trusted-code model unless a separate process boundary is
built. PHP plugins loaded in the web process are not a sandbox for untrusted
code. An unknown or changed executable package must not run before trust and
administrator approval.

## Required architecture boundaries and safety requirements

- One trusted kernel exposes four distinct, independently versioned extension
  contracts: MainAPI/PluginHost, GameTypeAPI, DatabaseTypeAPI, and
  SchemaAdapterAPI. These are separate roles, not four CMS cores.
- MainAPI is the entry point for all plugins. GameType is game identity and
  normalized capabilities; DatabaseType is an optional external/game-data
  transport; SchemaAdapter interprets a particular verified game schema or
  emulator. Never conflate MU, MSSQL, and MUEMU/Louis.
- Website-owned MSSQL persistence is essential and cannot depend on a removable
  external-game DatabaseType provider. Website settings, administrator
  authority, plugin approvals/configuration/audit/migration state belong in
  website-owned MSSQL. Deployment secrets must not be stored in source or normal
  configuration records.
- Game/account/web data can share one SQL Server database or be split across
  databases and realms. A DB connection or schema probe does not prove game
  compatibility or authorize game writes.
- Features consume normalized, versioned read ports rather than another
  plugin's private code or raw game SQL. Cross-owner dependencies use declared
  public contracts. V1–V5 contract baselines are protected; evolve additively.
- Only Infrastructure performs SQL/external transport. Native game writes need
  verified ownership, authoritative offline checks, cross-worker/account lock,
  recheck under lock, idempotency, and immutable audit. Payment fulfillment
  needs verified callbacks, replay protection, immutable order values and an
  audited atomic transition.
- Website authentication/mutations require throttling and CSRF/Origin checks;
  authorization is server-side. Never infer AdminCP authority from a game
  account, same-name account, or in-game GM flag.
- GrapesJS JSON is canonical; templates cannot contain executable PHP/SQL/
  arbitrary scripts. Preview is synthetic/private until proven otherwise.
- Installer/reset affects website-owned configuration only; never erase MU
  account/game data, including a unified database topology. Do not infer that
  reset is currently available or authorized.
- The original website at `C:/xampp/htdocs/ZendaMod` is read-only. Do not run
  it, its tests, scheduler, installer, requests, database tools, or copy its
  secrets/player data/licenses/assets.

## Snapshot of actual progress (verify against attached STATE.md)

Snapshot date: 2026-10-19. STATE.md is authoritative if this summary conflicts.

- Current checkpoint is plugin implementation step 03: bounded migration
  operation model exists, but physical MSSQL schema inspection, verified
  installed-state persistence, explicit approval-bound execution, interruption
  reconciliation and guarded provisioning remain incomplete.
- Step 04 (first live MainAPI folder add/remove/readd HTTP slice) remains pending.
- Decisions 104–108 add data-only manifest/package discovery, approval,
  configuration/unverified attempt receipts, read-only migration review and a
  constrained logical schema-operation model. These do not make a working
  executable plugin CMS.
- There is no executable MainAPI/GameTypeAPI/DatabaseTypeAPI/SchemaAdapterAPI
  runtime, provider binding, live plugin HTTP/worker lifecycle, or full AdminCP
  extension registry yet.
- Existing `src/Modules/*`, `packages/premium/*`, static architecture catalogs,
  and frozen V1–V5 remain during gradual migration.
- Current runtime/comprehensive verification has used PHP 7.4.33. Its
  compatibility requirement must be reconciled with the official PHP support
  status as of the review date; do not silently assume old PHP is suitable for
  production.
- Several website features/storage adapters have offline/SQLite and bounded
  owned SQL lab evidence. These are not blanket SQL Server deployment,
  whole-site production, live MU integration, game-server synchronization,
  payment, or visual GrapesJS evidence.
- Native MU writes, payment fulfillment, template publication, reset, and
  universal Season 1–19+ schema support remain unimplemented/disabled.
- Existing project documentation contains detailed safety gates, migration
  steps, decision records, offline tests and known evidence limitations. Treat
  them as requirements, not proof of runtime behavior.

## Required review work

1. Read the supplied project documents before drawing conclusions. Identify
   conflicts, ambiguities, missing product decisions, and architecture
   requirements that are easy to overlook. Separate:
   - implemented and evidenced;
   - partially implemented;
   - designed/specification only;
   - not designed or not evidenced.
2. Browse current, primary sources (standards, official framework docs, original
   authors where suitable) on software architecture, quality attributes,
   plugin/package trust and supply chain, secure SDLC, database evolution,
   reliability/observability, and defect-reduction/testing methods. Give source
   title, publisher, URL, publication/version date when available, and the
   specific claim supported. If browsing is unavailable, say so; do not invent
   citations or pretend a link was checked.
3. Critically compare the product goals and future architecture diagram with the
   researched methods. Recommend an architecture that fits this project and its
   PHP hosting constraints. Discuss modular monolith vs microservices, trusted
   in-process plugins vs real isolation, contract/version governance, physical
   plugin lifecycle, website/game/account data boundaries, multi-realm topology,
   migration recovery, deployment consistency across workers/nodes, CI/release
   controls, security threat model, observability/SLOs, backups/RPO/RTO, and
   cross-system payment/game side effects.
4. Find the highest-risk missing decisions and engineering issues. Rank each by
   severity, likelihood/confidence, dependency/blockers, and production gate.
   Do not inflate speculative risks or duplicate controls already correctly
   specified. Explicitly call out when a rule is designed but not verified.
5. Recommend methods to reduce defects appropriate to this codebase: e.g.
   ports/adapters and bounded ownership, ADRs, architecture fitness functions,
   TDD/risk-based test layers, integration/contract/HTTP/browser tests,
   property-based/fuzz tests for parsers and binary codecs, static analysis,
   mutation testing, secure build/provenance, failure injection, and recovery
   exercises. Explain the best target surfaces and adoption order. Do not
   recommend adding every tool/pattern by default.
6. Produce a phased plan that respects the existing implementation sequence and
   contract freezes. State concrete acceptance evidence for each phase and what
   must remain disabled until the evidence exists. Include the current step 03
   and step 04 gates, not an alternative rewrite plan that skips them.
7. Identify missing architecture views. The current treeview is a hierarchy,
   not a deployment or runtime interaction diagram. Recommend only useful views
   (for example C4 Context/Container, selected Dynamic flows, Deployment and
   Trust Boundaries) and list what each should show.
8. Challenge ambitious claims such as universal autodetection, “bugless,”
   drop-in activation, plugin isolation and payment/game atomicity. Replace
   absolutes with measurable support matrices and evidence-based acceptance
   criteria.

## Required answer format

A. Executive conclusion (what is strong, what is not production-ready).
B. “Implemented vs specified vs missing evidence” table with repository
   references.
C. Ranked findings table with columns:
   `ID | Priority | Area | Evidence/state | Risk | Recommended action |
   Dependencies | Acceptance evidence`.
D. Recommended target architecture/design choices and alternatives considered,
   including tradeoffs and what not to add yet.
E. Defect-reduction engineering methods mapped to concrete ZendaMod files/
   surfaces and appropriate test types.
F. Ordered roadmap with exit criteria, preserving authorization and safety gates.
G. Useful architecture diagrams/views to add.
H. Sources: concise annotated list with verified URLs and dates.
I. Assumptions, uncertainties and questions that require the product owner.

Be concise enough to review, but detailed enough to turn into project
documentation. Prefer project-specific evidence and measurable criteria over
generic “enterprise best practices.” Do not claim a certification, production
readiness, universal game support, or passing tests without direct evidence.
Do not recommend changing frozen contracts or using the legacy project.

## Project files to attach or paste

At minimum:
- `STATE.md`
- `AGENTS.md`
- `ARCHITECTURE.md`
- `API_ARCHITECTURE.md`
- `PLUGIN_SYSTEM.md`
- `PLUGIN_DEVELOPMENT.md`
- `docs/IMPLEMENTATION_PLAN.md`
- `docs/PLUGIN_RUNTIME_MIGRATION.md`
- `docs/ARCHITECTURE_DIAGRAMS.md`
- `docs/ARCHITECTURE_GAP_ASSESSMENT.md`
- `docs/ENGINEERING_METHODS.md`
- `CONTRACTS.md`, `INVARIANTS.md`, `DATABASE.md`, `MODULES.md`
- `docs/INSTALLATION_LIFECYCLE.md`
- `architecture/runtime.json` and `architecture/modules.json`

If the AI can inspect the repository, ask it to read those exact files and
`STATE.md` first. If it cannot, paste the files or the relevant sections. Do not
paste credentials, player records, private settings, licenses, or production
connection strings.
```

## Cómo usar la respuesta

Cuando traigas la respuesta, adjunta el texto o archivo completo y, si el modelo
dio fuentes, conserva sus URLs y fechas. La revisión aquí debe verificar los
claims contra el estado canónico, distinguir recomendaciones de decisiones
aprobadas y documentar únicamente las conclusiones que tengan evidencia.
