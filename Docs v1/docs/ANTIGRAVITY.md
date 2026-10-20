# Continue in Gemini / Google Antigravity

`STATE.md` is the current checkpoint, including actual verification, missing work
and the next authorized step. Read it first, then `AGENTS.md`. They are shared by
Codex, Cursor and Antigravity; this guide is setup guidance, not another state log.

The user approved continuing after the phase 1-3 review on 2026-10-17.
MU Online is the initial game. Preserve extension points for possible future
games; Metin2, Minecraft and other adapters are outside the current implementation.
Check `STATE.md` for the latest authorized scope and evidence before continuing.

## Open the project

- **Antigravity app:** create a project and add
  `C:/xampp/htdocs/ZendaWeb/ZendaMod-Next` as its working folder. Choose Local to
  continue these files. Google's [getting-started guide](https://www.antigravity.google/docs/getting-started/)
  documents creating a project with Add Folder and Local mode.
- **Antigravity IDE or editor extension:** open
  `C:/xampp/htdocs/ZendaWeb/ZendaMod-Next` as a folder, then open its agent panel.
  Opening the parent `C:/xampp/htdocs/ZendaWeb` also has a bootstrap pointer.
- The existing `../ZendaMod-Next.code-workspace` is the VS Code/Cursor multi-root
  workspace. Its Antigravity workspace loading, task, debug and editor-guard
  behavior have not been tested. Opening the application folder alone is sufficient
  for continuation and does not mount the legacy project.

Do not add the legacy tree as a writable project. The original
`C:/xampp/htdocs/ZendaMod` is strictly read-only. Never execute its website,
bootstrap, installer, scheduler, tests or database tools. Reuse `docs/discovery/`
and inspect only affected source when necessary. Do not copy installed settings,
credentials, licenses, player data or third-party assets. Editor guards and agent
rules do not restrict shell writes or change filesystem permissions.

## Files configured

| Entry | Purpose |
| --- | --- |
| `../GEMINI.md` | Bootstrap when the parent folder is opened. |
| `GEMINI.md` | Bootstrap when the application folder is opened. |
| `../.agents/rules/zenda-continuity.md` | Always-on parent workspace pointer. |
| `.agents/rules/zenda-continuity.md` | Always-on application workspace pointer. |
| `STATE.md`, `AGENTS.md` | Canonical checkpoint and project instructions. |

Google's [Rules documentation](https://www.antigravity.google/docs/rules/)
describes directory discovery for `AGENTS.md`, `GEMINI.md` and
`.agents/rules/*.md`. The two bootstrap Markdown files need no frontmatter.
Each modular rule starts with `trigger: always_on` YAML frontmatter and uses
relative `@path` references. The documented older `.agent/rules/` location is
supported for compatibility; this project uses `.agents/rules/` only.

No Antigravity installation, extension, account, global `~/.gemini` configuration,
permission setting or external service was changed. Files and references are
configured locally; actual rule discovery and execution in Antigravity remain
unverified. In a new session, have the agent identify the loaded project files
and summarize the current checkpoint before making changes.

## Starter prompt

```text
Continue ZendaMod Next in this folder. Read STATE.md first, then AGENTS.md.
Identify the current checkpoint, completed verification, missing work and next
authorized step. Follow that scope and the documented development workflow.
MU Online is the initial target; other games are future extension possibilities.
Keep C:/xampp/htdocs/ZendaMod strictly read-only and never execute its website,
installer, scheduler or database tools. Reuse the existing discovery evidence.
Implement the next authorized slice, run the appropriate offline checks and
update STATE.md last with actual results and remaining work.
```

Run verification from the application folder with the documented local runtime,
for example `C:/xampp/php/php.exe scripts/verify.php --level=fast`.
Read `TESTING.md` for level selection and limits. Passing fixture/tool checks
does not establish live game, payment, database or deployment compatibility.
