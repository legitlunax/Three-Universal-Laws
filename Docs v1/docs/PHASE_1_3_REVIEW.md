# Required review checkpoint: phases 1–3

**Historical snapshot. The user approved continuation on 2026-10-17.** Current
implementation, authorization and verification are maintained in [STATE](../STATE.md).
The original review content below records what existed at that checkpoint.

Prepared 2026-10-17 (America/Buenos_Aires). The user's specification §3.1 requires
this review before phase 4. No application implementation is authorized past
this checkpoint until that review. The full CMS acceptance checklist is not met.

## Review these artifacts

| Deliverable | Evidence and current status |
| --- | --- |
| Functional inventory and eight extraction artifacts | [FUNCTIONAL_INVENTORY](discovery/FUNCTIONAL_INVENTORY.md): 27 feature families, routes/API/AdminCP/tasks/assets/configuration, conditional and reference-only distinctions; static source analysis |
| Original-to-new migration matrix | [MIGRATION_MATRIX](discovery/MIGRATION_MATRIX.md): requirements, dependencies, acceptance evidence and all unimplemented reconstruction work |
| Data and integration knowledge | [DATA_AND_INTEGRATIONS](discovery/DATA_AND_INTEGRATIONS.md): source-cited profiles, mappings, item formats, payment behavior and observed consistency gaps; no live compatibility certification |
| Architecture and initial contracts | [ARCHITECTURE](../ARCHITECTURE.md), [CONTRACTS](../CONTRACTS.md), [INVARIANTS](../INVARIANTS.md): modular monolith; eight V1 interfaces and two planned visual component schemas frozen; missing specialized ports and runtime enforcement explicitly tracked |
| VS Code/Cursor workspace | [ZendaMod-Next.code-workspace](../../ZendaMod-Next.code-workspace), [.vscode](../.vscode/settings.json), [WORKSPACE](WORKSPACE.md): strict JSON, new root first, legacy read-only reference, tasks, debugging prerequisites and recommended extensions |
| AI memory and handoff | [AGENTS](../AGENTS.md), [STATE](../STATE.md): required workflow, protected boundaries, status, limitations and next step |
| Executable checks and CI configuration | [TESTING](../TESTING.md), [verify.php](../scripts/verify.php), [workflow](../.github/workflows/verify.yml): offline tooling tests; CI and ownership/branch rules not executed/configured remotely |

## Implemented and verified locally

The direct PHP verifier runs without Composer, SQL Server, network or legacy PHP.
It checks PHP syntax, strict JSON, declared modules/capabilities/configuration,
supported static architecture rules, frozen interface/component metadata and
synthetic project compatibility. Tool regression tests include forbidden layers,
cross-module access, aliases, contract/version mismatches, cycles, missing providers,
path traversal, executable template properties, unsafe config and copy-preserving
synthetic component migration. See STATE for final counts and commands.

Impact analysis generates the declared module graph, finds indirect consumers and
selects declared tests conservatively. A GameData change identifies Commerce,
Community, Templates and Admin. These are planned dependencies, not implemented
feature behavior. Existing source anchors were statically checked for existence
and line bounds; none of the legacy application was executed.

Final local results: fast passed (0 test assertions), affected passed (49), complete
passed (57), migration passed (57). Every level checked 16 PHP files, 15 JSON files
and two current project fixtures. All five IDE JSON files and their path/task
references also validated. The full legacy comparison passed: 39,846 files,
2,291 directories and 2,600,823,210 bytes, with zero additions, removals or changes.
The full manifest aggregate matched before/after; the local evidence is in
`reports/legacy-before.json` and `reports/legacy-comparison.json`.

## Implemented but unverified / external configuration pending

- VS Code/Cursor live editor lock, extension and debugger behavior. All five JSON
  files and task/profile references were checked; Xdebug is absent.
- Composer aliases (Composer absent from PATH); the exact underlying PHP commands
  are available locally. Only PHP 7.4.33 was executed; the PHP 8.2 CI matrix was not.
- Hosted CI execution, real reviewer identities/CODEOWNERS and mandatory branch
  review/check rules. No remote repository, publication or settings were changed.

## Not implemented

Frontend, AdminCP, shared application services, auth/session/recovery, production
repositories/adapters/schema discovery, runtime authorization/CSRF/rate limiting,
game synchronization/locking/idempotency/audit, payments, modules, real migrations,
asset copying/ingestion, GrapesJS/Vite installation, editor, renderer, persistence,
draft/private preview/publication/revision management and deployment.

There are no application/domain/MSSQL/payment/browser/visual/upload-security suites,
whole-program type analysis or JS lint/build checks yet. Template fixtures use a
proposed narrow schema and symbolic assets; passing them does not establish real
editor compatibility, sanitization or render correctness.

## Decisions for the gate and continuation

Review the PHP 7.4 baseline, modular responsibilities, game-write fail-closed policy,
initial V1 boundaries, website/game data separation and canonical JSON/revision
model. Specialized public-template/auth/payment/codec/currency ports must receive
contract review before activation; the current eight are not the complete CMS API.

After approval, begin phase 4 with a concrete isolated, offline end-to-end slice:
explicit composition, request/error/session/configuration boundaries, a read-only
fixture-backed public page and server-authorized AdminCP login boundary. Define
missing ports deliberately, implement real validation and conformance tests, then
introduce production integrations only with separately designated non-production
evidence. Do not start by enabling game writes or copying the legacy bootstrap.
