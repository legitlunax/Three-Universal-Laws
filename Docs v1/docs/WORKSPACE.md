# VS Code, Cursor and continuation workspace

Open `C:\xampp\htdocs\ZendaWeb\ZendaMod-Next.code-workspace` using **File > Open Workspace from File**. The first root, `ZendaMod-Next`, is the new project. The second, `Legacy - READ ONLY`, points to `C:\xampp\htdocs\ZendaMod` for reference inspection. All configured tasks use the new project's explicitly named root as their working directory. No legacy configuration or user settings are changed.

The workspace and `.vscode/*.json` use strict JSON. VS Code 1.142.0 is available locally; Cursor support depends on its installed VS Code baseline and extensions and has not been verified in a live Cursor session. [VS Code documents named roots, relative folder paths, and task/debug discovery in multi-root workspaces](https://code.visualstudio.com/docs/editing/workspaces/multi-root-workspaces).

For Gemini or Google Antigravity, use [ANTIGRAVITY.md](ANTIGRAVITY.md). Parent and
application `GEMINI.md` files and always-on rules point to the same canonical
`STATE.md` and `AGENTS.md`. Actual Antigravity rule loading and workspace task
execution remain unverified. Keep the checkpoint current when switching agents.

For Minimax-hosted models (MiniMax-M3.1-Flash-Preview and related slugs), use
[MINIMAX.md](MINIMAX.md). The parent and application `MINIMAX.md` files point to the
same canonical `STATE.md` and `AGENTS.md`. Minimax provides no automatic model or
reasoning-effort selector, so effort is chosen per task as documented there, and
provider credentials stay in user-level agent configuration outside this repository.
The user approved phase 4 continuation on 2026-10-17; the phases 1–3 review is a
historical record, not an outstanding gate for this slice.

## Read-only protection and its limits

`files.readonlyInclude` covers the entire legacy root from workspace-level settings, including its root folder, and protects `src/Contracts/**`, `contracts/**`, `architecture/**`, `.github/workflows/**`, and `.github/CODEOWNERS`. New-project folder settings repeat the contract patterns so that opening the new folder alone retains those editor guards. Auto-save, automatic fixes, format on save, and automatic task detection are disabled at workspace level.

The legacy rules deliberately use the absolute local path. Relative patterns such as `../ZendaMod/**` are unreliable in multi-root read-only settings because matching is relative to each root; an [issue in the official VS Code repository documents this limitation](https://github.com/microsoft/vscode/issues/304130). If the workspace moves, update both copies of the absolute legacy rules and the absolute protected-new-project rules in the workspace file. Relative protected-contract rules continue to apply inside the new root.

This is an editor and Explorer guard. It is **not** an operating-system access control boundary and does not prevent a terminal, external script, extension using direct filesystem access, or AI shell tool from writing files. VS Code also permits a session command to override editor read-only status. Do not treat this setting as authorization to change the legacy source or protected contracts. The original project must remain untouched; protected new-project changes require the repository's documented contract evolution workflow. These limits follow [VS Code's read-only mode documentation](https://code.visualstudio.com/updates/v1_79#_readonly-mode).

Editor lock behavior has not been interactively tested. After opening the workspace, check that an existing legacy file and a protected new-project file show the read-only state without typing, deleting, renaming, or toggling them writable. Opening only the new folder does not mount or configure a separate legacy root; use the workspace file for reference work. Stronger filesystem restrictions would require a separately planned OS account/ACL or read-only mount; this setup does not change any ACLs.

## Verification tasks

Normative `AGENTS.md`, `ARCHITECTURE.md`, `CONTRACTS.md` and `INVARIANTS.md` also
have editor read-only guards. `architecture/policy.json` uses `protectedPaths` to
identify review-sensitive areas; that list also includes scripts/tests, which
remain editable for routine development but require explicit review of policy
and verification changes. Editor guards and review ownership are different controls.

Use **Terminal > Run Task** and select a `Verify:` task. **Run Build Task** runs the default centralized PHP verifier. The default and four explicit levels are available directly through `C:/xampp/php/php.exe`, so they do not require Composer:

| Task | Equivalent command from the new project root |
| --- | --- |
| Default | `C:/xampp/php/php.exe scripts/verify.php` |
| Fast | `C:/xampp/php/php.exe scripts/verify.php --level=fast` |
| Affected components | `C:/xampp/php/php.exe scripts/verify.php --level=affected --changed=src/SomeFile.php,contracts/some-contract.json` |
| Complete | `C:/xampp/php/php.exe scripts/verify.php --level=complete` |
| Migration preview | `C:/xampp/php/php.exe scripts/verify.php --level=migration` |

For affected checks, enter project-relative changed paths separated by commas when prompted. The default `src` input selects the source tree. Read the verifier's reported scope and escalation rules; selecting a task does not establish that a feature or full CMS has been implemented. Migration verification is offline validation, not permission to import or modify live data.

Equivalent `Composer:` tasks expose `composer verify`, `composer verify:fast`, `composer verify:affected -- --changed=...`, `composer verify:complete`, and `composer verify:migration`. Composer was not on PATH during setup; those task profiles require a separately installed Composer executable and PHP on its runtime PATH. The PHP tasks are the usable local entry point. The repository's verification documentation defines each level's actual coverage.

PHP task definitions use process execution and argument arrays; changed-path input is passed as one verifier argument rather than interpreted as a shell command. Composer aliases use shell tasks to support the usual Windows Composer launcher. [VS Code documents process tasks, arguments, and task working directories](https://code.visualstudio.com/docs/debugtest/tasks).

## Runtime and debugging prerequisites

The local configured PHP runtime is `C:/xampp/php/php.exe`, version 7.4.33. PHP validation and Intelephense target that version. PHP's local loaded modules do not include Xdebug, `pdo_sqlsrv`, or `sqlsrv`; no driver or debugger was installed. SQLTools is an optional inspection client and does not supply the PHP SQL Server extension. Keep connection credentials out of committed workspace files, and use a database account with SELECT-only permissions for legacy inspection.

The `PHP: listen for Xdebug` profile binds to `127.0.0.1:9003`. The fast-verification debug profile launches the verifier with debug flags. Both require the recommended PHP Debug editor extension and a PHP-7.4-compatible Xdebug 3 build loaded in the chosen local PHP runtime. Configuration such as `xdebug.mode=debug`, `xdebug.start_with_request=trigger`, `xdebug.client_host=127.0.0.1`, and `xdebug.client_port=9003` must be applied to that development runtime separately, then confirmed with `php --ri xdebug`. No shared XAMPP `php.ini` change was made. The CLI debug profile explicitly starts its own debugging request; a web request using trigger mode needs `XDEBUG_TRIGGER=1`. [The PHP Debug project's documentation explains Xdebug installation and port 9003](https://github.com/xdebug/vscode-php-debug).

The first offline application slice now has `public/index.php` and
`public/router.php`. Run **Development: PHP sandbox server** to serve only
`public/` on `127.0.0.1:8091`. Its entry-point prerequisite checks that the app
exists before launching. The prepared browser profile uses Chrome's built-in
JavaScript debugger and requires Chrome; it does not install PHP debugging.
Stop the server with **Tasks: Terminate Task** when finished. Follow
[SANDBOX.md](SANDBOX.md) for local administrator setup and private storage.
No Vite task is configured because a frontend package and build have not been
implemented. [VS Code requires a background problem matcher for a long-running pre-launch task](https://code.visualstudio.com/docs/debugtest/tasks#_can-a-background-task-be-used-as-a-prelaunchtask-in-launchjson).

## Optional extensions

Recommendations are recorded in the workspace and `.vscode/extensions.json`; nothing is installed automatically.

| Extension | Purpose and prerequisite |
| --- | --- |
| `bmewburn.vscode-intelephense-client` | PHP language analysis targeting 7.4.33. [Publisher installation instructions](https://github.com/bmewburn/intelephense-docs/blob/master/installation.md). |
| `xdebug.php-debug` | PHP breakpoint debugging; requires Xdebug in the development PHP runtime. [Project documentation](https://github.com/xdebug/vscode-php-debug). |
| `dbaeumer.vscode-eslint` | JavaScript/TypeScript linting when the approved frontend introduces ESLint dependencies and configuration. [Extension project](https://github.com/microsoft/vscode-eslint). |
| `swordev.phpstan` | Optional PHPStan diagnostics after a compatible PHPStan dependency and `phpstan.neon` configuration are approved. Automatic PHPStan analysis is disabled until those prerequisites exist; enable it only for the new project. It is not the repository's architecture enforcement engine. [Extension project and prerequisites](https://github.com/swordev/phpstan-vscode). |
| `mtxr.sqltools` and `mtxr.sqltools-driver-mssql` | Optional SQL Server inspection with separately configured development/read-only credentials. [SQLTools](https://marketplace.visualstudio.com/items?itemName=mtxr.sqltools), [SQL Server driver](https://marketplace.visualstudio.com/items?itemName=mtxr.sqltools-driver-mssql). |

Repository verification tasks remain the source of architectural enforcement. Installing extensions alone does not run or replace those checks. This setup changes no global editor preferences, installs no dependencies, and opens no database connection.
