# Continue in Codex / OpenAI coding agents

`STATE.md` is the canonical, live checkpoint of the project, including completed
verification, missing work and the next authorized step. Read it first, then `AGENTS.md`.
These documents are shared equally between Codex, Cursor, ChatGPT, and Gemini/Antigravity;
this guide provides dedicated continuation guidance for Codex without maintaining a separate
progress ledger.

The user approved Phase 4 continuation after the Phase 1–3 review on 2026-10-17.
MU Online is the initial target; preserve extension points for future game adapters
without implementing Metin2 or Minecraft now. Always check `STATE.md` for the current
state and authorized scope before implementing changes.

## Open the project in Codex / Cursor / IDE

- **Multi-Root Workspace:** Open `../ZendaMod-Next.code-workspace` (or `C:/xampp/htdocs/ZendaWeb/ZendaMod-Next.code-workspace`).
  It mounts `ZendaMod-Next/` as the primary writable workspace root, and `C:/xampp/htdocs/ZendaMod` as
  a secondary read-only reference folder.
- **Application Folder Mode:** Alternatively, open `C:/xampp/htdocs/ZendaWeb/ZendaMod-Next` directly.
- **Legacy Protection (Tier 0 Non-Negotiable):** The original legacy site at `C:/xampp/htdocs/ZendaMod`
  is STRICTLY READ-ONLY. Never modify legacy files, and never execute its website, installer,
  scheduler, tests, or database tools. Reuse existing discovery evidence in `docs/discovery/`.

## Local runtime and verification

The local PHP runtime is:
`C:/xampp/php/php.exe` (PHP 7.4.33). PHP is not on PATH.

Run centralized verification using that binary from the application root (`C:/xampp/htdocs/ZendaWeb/ZendaMod-Next`):

| Purpose | Command |
| --- | --- |
| Fast checks (routine development) | `& 'C:/xampp/php/php.exe' scripts/verify.php --level=fast` |
| Affected components | `& 'C:/xampp/php/php.exe' scripts/verify.php --level=affected --changed=path1,path2` |
| Complete validation (before integration) | `& 'C:/xampp/php/php.exe' scripts/verify.php --level=complete` |
| Migration validation | `& 'C:/xampp/php/php.exe' scripts/verify.php --level=migration` |

If Composer is available on PATH, Composer task aliases (`composer verify`, `composer verify:fast`, etc.)
map to the same runner. All checks run completely offline without requiring live MSSQL instances or external services.

## Local sandbox server

To run the local sandbox web server:
```powershell
& 'C:/xampp/php/php.exe' -S 127.0.0.1:8091 -t public public/router.php
```

Setup a local website administrator:
```powershell
& 'C:/xampp/php/php.exe' scripts/setup-sandbox.php --username=youradmin
```
Follow [docs/SANDBOX.md](SANDBOX.md) for sandbox configuration and private storage details.

## Bidirectional multi-agent workflow (Codex <-> Gemini Antigravity)

1. **Single Source of Truth:** `STATE.md` is the single authoritative ledger for both Codex and
   Gemini Antigravity. When completing a turn, Codex must update `STATE.md` last with actual
   checks run, results, and the next authorized slice so Gemini Antigravity can continue if called.
2. **Mandatory 11-Step Process:**
   1. Understand the change and read `STATE.md`.
   2. Discover existing relevant functionality using focused searches.
   3. Identify affected contracts (`src/Contracts/V1` through `src/Contracts/V5`; see CONTRACTS.md).
   4. Analyze direct and indirect consumers with `scripts/impact.php`.
   5. Reuse existing infrastructure where appropriate.
   6. Implement through declared extension points.
   7. Update relevant tests and representative fixtures.
   8. Run architectural validation (`verify.php --level=fast`).
   9. Run appropriate functional tests (`verify.php --level=complete`).
   10. Verify consumer/template compatibility.
   11. Document evidence and update `STATE.md` last.
3. **Preserve Protected Surfaces:** Never casually modify frozen contracts (`src/Contracts/V1/`,
   `src/Contracts/V2/` through `src/Contracts/V5/` and their independent versioned baselines)
   or weaken test assertions. Follow the contract evolution process
   when contract changes are justified.

## Starter prompt for Codex

When initiating a new session with Codex or Cursor, use this prompt:

```text
Continue ZendaMod Next in this folder. Read STATE.md first, then AGENTS.md and SKILL.md.
Identify the current checkpoint, completed verification, missing work, and next
authorized step. Follow that scope, the documented 11-step development workflow,
and the canonical MU Online specifications in SKILL.md (.agents/skills/mu-online-web-dev/SKILL.md).
MU Online is the initial target; other games are future extension possibilities.
Keep C:/xampp/htdocs/ZendaMod strictly read-only and never execute its website,
installer, scheduler, or database tools. Reuse existing discovery evidence in docs/discovery/.
Implement the next authorized slice, run verification with C:/xampp/php/php.exe scripts/verify.php,
and update STATE.md last with actual results and remaining work.
```


