# AI development workflow

## New layered plugin contracts (specification only)

Agents must read `API_ARCHITECTURE.md` plus `MAIN_API.md`, `GAME_TYPE_API.md`, `DATABASE_TYPE_API.md`, `SCHEMA_ADAPTER_API.md` before touching any plugin/game/schema/database boundary. All providers are removable folders under `/plugins`; website MSSQL is kernel-owned and persists with empty `/plugins`. No runtime SDK implementation is implied by these docs.


## Priority for future agents: actual drop-in plugin mechanics

Follow `PLUGIN_SYSTEM.md` and `PLUGIN_IMPLEMENTATION_PROMPT.md`. A design that
requires changing central PHP switches, static `architecture/modules.json`,
Composer autoload registration or unrelated feature code whenever a new plugin
folder is added **does not satisfy the user's goal**. Prove that adding/removing
an approved complete `/plugins/<id>` folder toggles runtime contributions on
next HTTP request without taking down unrelated components or erasing MSSQL
state. Keep the existing eleven-step workflow, contract/version protections and
truthful `STATE.md` last-update rule. No general plugin loader exists yet.

AGENTS.md is the canonical agent instruction file and STATE.md is the live handoff.
Read them before editing. The user approved phase-4 continuation on 2026-10-17;
do not request that approval again. MU Online remains first; other games are a
future possibility. Gemini/Antigravity uses GEMINI.md and .agents/rules bootstrap
files pointing to the same state and instructions; see docs/ANTIGRAVITY.md.
Codex and Cursor use CODEX.md and docs/CODEX.md with the shared canonical ledger.
Minimax-hosted sessions use MINIMAX.md and docs/MINIMAX.md with that same shared
ledger, plus the documented per-task model and reasoning-effort choice; Minimax has
no automatic selector, and provider credentials stay in user-level agent config.
Do not repeat broad legacy discovery. Source facts and uncertainties are in
docs/discovery; cite inspected files rather than trusting old roadmap documents.

Follow the eleven steps in AGENTS: understand, discover, identify contracts,
analyze dependencies, reuse infrastructure, implement through extension points,
update tests, validate architecture, test behavior, verify compatibility and
document evidence. Use focused `rg` searches. Never log or copy installed secrets.

Start impact analysis with repository-relative changed paths. Policy/contract/test
changes are protected review surfaces, and unknown dynamic dependencies require
explicit catalog edges. A new implementation must not silently redefine the
architecture. Baseline changes need justification and a migration/version plan.

Use the smallest verification level appropriate while editing; run complete
before integration and migration before any contract/project/data migration.
Do not weaken checks. Report implemented-and-verified, implemented-but-unverified,
partial, not-implemented or unsupported accurately. Passing tooling tests cannot
be reported as passing game/payment/security/application tests.

Update STATE last with changed behavior, actual commands/results, stable
contracts, failing checks, unimplemented requirements, pending migrations and
the next authorized step. Keep it concise; do not paste a transcript into it.
