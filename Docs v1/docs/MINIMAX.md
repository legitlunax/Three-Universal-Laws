# Continue with MiniMax models

`STATE.md` is the canonical, live checkpoint of the project, including completed
verification, missing work and the next authorized step. Read it first, then
`AGENTS.md`, then `SKILL.md`
([.agents/skills/mu-online-web-dev/SKILL.md](../.agents/skills/mu-online-web-dev/SKILL.md)).
This guide is setup and model-selection guidance only. It is deliberately **not**
a second progress ledger: Codex, Cursor, Gemini/Antigravity and Minimax sessions
read and update the same `STATE.md`.

## Provider setup

The Minimax models are served through an OpenAI-compatible `responses` endpoint, so
any client that supports a custom model provider can point at it. In Codex the
configuration lives in the user-level `~/.codex/config.toml` (a provider entry, a
default model and a local model catalog), not in this repository. Keep the API key
in that file only; **never** copy it into this project, its docs, fixtures,
`.code-workspace` files or any committed file.

| Concern | Where it belongs |
| --- | --- |
| API key / base URL | User-level agent config, outside the repository |
| Model list and effort levels | User-level model catalog, outside the repository |
| Project rules, state, workflow | This repository (`AGENTS.md`, `STATE.md`) |

## No automatic selector

Unlike OpenAI-hosted models in Codex, Minimax exposes no automatic model or
reasoning-effort selector. `MiniMax-M3.1-Flash-Preview` advertises the levels
`low`, `medium`, `high`, `xhigh` and `max`; `MiniMax-M3` offers `none` and `high`;
the M2.7 models are always-on `high`. Pick the level per task and change it
deliberately. Do not assume a cheaper level is safe for this repository, and never
describe an interface, fixture or tool run as verified functionality because a
weaker reasoning level produced a passing answer.

## Recommended effort for this project

| Level | Work |
| --- | --- |
| `xhigh` / `max` | Frozen-contract changes (`src/Contracts/V*`, `contracts/v*/baseline.json`), `PluginManager` and drop-in plugin lifecycle, mutation gateway, payment fulfillment, auth/CSRF/throttling, MSSQL-authoritative configuration, MSSQL schema or DDL changes, and any decision recorded in `DECISIONS.md` or `STATE.md`. |
| `high` (default) | Implementation inside an already-decided design: modules, adapters, controllers, migrations, tests, fixtures, `scripts/impact.php` analysis, architectural validation. |
| `medium` | Mechanical or repetitive edits: views, CSS, renames, bulk fixture and test-data expansion, doc synchronization where the plan already exists. |
| `low` / `none` | Trivial read-only inspection, formatting and lookups. Never for a contract, security gate or persistence rule. |

Model notes for this codebase: `MiniMax-M3.1-Flash-Preview` is the default choice and
supports image input, which helps with templates and visual regression work.
`MiniMax-M3` is a large-context alternative (`none` / `high` only). `MiniMax-M2.7`
and `MiniMax-M2.7-highspeed` are text-only and cheaper; reserve them for bulk
read-only discovery over legacy sources or repetitive fixture work, never for
contract or security reasoning.

## Delegated agents

There is no automatic selector, so set the effort explicitly per spawned agent
instead of relying on inheritance: `high` for exploration and design review,
`medium` for narrow file reading, and never below `medium` for an agent allowed to
edit `src/`, `contracts/` or `architecture/`.

## Verification

Runtime and commands are identical to the other agents; see [CODEX.md](CODEX.md) and
[TESTING.md](../TESTING.md). The local PHP binary is `C:/xampp/php/php.exe`:

```powershell
& 'C:/xampp/php/php.exe' scripts/verify.php --level=fast
& 'C:/xampp/php/php.exe' scripts/verify.php --level=complete
```

All checks run offline. Passing tool or fixture checks does not establish live game,
payment, database or deployment compatibility. The legacy tree at
`C:/xampp/htdocs/ZendaMod` is strictly read-only.

## Starter prompt

```text
Continue ZendaMod Next in this folder. Read STATE.md first, then AGENTS.md and SKILL.md.
Identify the current checkpoint, completed verification, missing work, and next
authorized step. Follow that scope and the documented 11-step development workflow.
MU Online is the initial target; other games are future extension possibilities.
Keep C:/xampp/htdocs/ZendaMod strictly read-only and never execute its website,
installer, scheduler, or database tools. Reuse the existing discovery evidence.
Implement the next authorized slice, run verification with
C:/xampp/php/php.exe scripts/verify.php, and update STATE.md last with actual
results and remaining work.
```
