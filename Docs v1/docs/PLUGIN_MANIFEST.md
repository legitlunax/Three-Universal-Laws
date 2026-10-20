# Canonical distribution manifest v1

Decision 104 selects this format for data-only discovery. `Manifest::parse`
implements its structural rules; `Discovery::scan` checks files without executing
PHP. This is **not** a frozen MainAPI SDK or approval/activation interface.
The older `api`, string dependency and caret-version examples were design sketches;
no installed runtime format exists to migrate. Reject them, unknown fields and
unsupported manifest versions rather than guessing a conversion.

## Complete example

Every field shown is required. This example's digest corresponds to exactly
`<?php\nnamespace ZendaHello;\nfinal class Plugin {}\n` with LF bytes, where
`\n` denotes a newline. That class is a discovery example, not a runnable host
entrypoint; an eventual SDK implementation must also satisfy registration rules.

```json
{
  "manifestVersion": 1,
  "id": "zenda.hello",
  "name": "Hello Example",
  "publisher": "zenda",
  "version": "1.0.0",
  "contracts": {
    "mainApi": {"min": "1.0.0", "maxExclusive": "2.0.0"}
  },
  "requires": {
    "php": {"min": "7.4.0", "maxExclusive": "9.0.0"},
    "core": {"min": "1.0.0", "maxExclusive": "2.0.0"}
  },
  "entrypoint": "ZendaHello\\Plugin",
  "autoload": {"ZendaHello\\": "src/"},
  "dependencies": {"required": [], "optional": []},
  "providesCapabilities": [],
  "requiresCapabilities": [],
  "permissions": [],
  "contributions": ["routes"],
  "providers": [],
  "distribution": "free",
  "files": {
    "src/Plugin.php": "845c0f5d2a85d90039e390af2f0f1a16310f9e2297bfc4591ff8b2e24a81af04"
  }
}
```

## Exact shapes and constraints

| Field | Rules |
| --- | --- |
| `manifestVersion` | Integer `1`, not a numeric string. |
| `id`, `publisher` | ID equals the direct directory name; lowercase dotted segments, each starting with a letter, with internal digits/hyphens; at least two segments, at most 96 bytes. Publisher equals its first segment. This is a claim, never proof of provenance. |
| `name`, `version`, `distribution` | Name 1-120 UTF-8 bytes without control characters; release version is exactly `major.minor.patch`, each component 0-9999 with no leading zero; distribution `free` or `premium`. No prerelease/build syntax in v1. |
| `contracts` | Object requiring `mainApi`; optional independent `gameTypeApi`, `databaseTypeApi`, `schemaAdapterApi`. Values are ranges described below. Declaring a range does not establish a working SDK/provider. |
| `requires` | Exactly `php` and `core`, both explicit ranges. Compatibility evaluation belongs to activation, not parsing. |
| `entrypoint`, `autoload` | Canonical class name and exactly one top-level PascalCase namespace prefix mapped to `src/`. `Zenda` in any case is reserved. Entrypoint must be inside the prefix and its case-exact PSR-4 PHP file must appear in `files`. No class loading, constructor calls or PHP inspection during discovery. |
| `dependencies` | Exactly `required` and `optional` arrays, each at most 32 objects `{id,min,maxExclusive}`. No self dependency, repeated ID or overlap between lists. Missing/incompatible dependency handling belongs to the host graph. |
| `providesCapabilities` | Up to 64 unique `{id,version,scope}` objects; scope is `website` or `gameProfile`. Declarations cannot enable native effects. |
| `requiresCapabilities` | Up to 64 unique `{id,min,maxExclusive,scope,optional}` objects; `optional` is a boolean. Profile-scoped ports require a server-selected realm/provider binding, never first-match resolution. |
| `permissions` | Up to 64 unique dotted IDs; requested permissions are not grants. |
| `contributions` | Unique values from `routes`, `adminMenu`, `events`, `jobs`, `commands`, `assets`, `translations`, `widgets`, `services`, `settings`, `paymentGateways`, `gameTypes`, `databaseTypes`, `schemaAdapters`. These are declared surface types, not executable handlers. |
| `providers` | Up to 32 unique `{kind,id,version}` objects; kind is `gameType`, `databaseType` or `schemaAdapter`. A provider requires its corresponding API range and plural contribution type. IDs are dotted identities, distinct from a database driver's name such as `mssql`. Multiple kinds may be declared by one package. |
| `files` | Object of 1-512 package-relative paths to lowercase SHA-256 hex digests. Include every file, including bundled libraries, PHP, views, tests, assets and migrations, except root `plugin.json`. Its bytes are fingerprinted separately to avoid a self-hash. Empty object/list confusion, case-fold duplicates, and missing entrypoint inventory refuse. |

A range is exactly `{"min":"1.0.0","maxExclusive":"2.0.0"}` with increasing
release versions: inclusive minimum, exclusive maximum. There is one syntax;
no arbitrary Composer expressions or implicit version ranges. All nested records
have exact keys. Reject duplicate JSON object keys, including escaped equivalent
spellings, invalid UTF-8, BOM, excess depth and non-object envelopes.

Paths use forward slashes, at most 180 bytes and eight segments. Each segment
starts with an ASCII letter/digit and contains letters/digits/underscore/hyphen
plus at most one extension. Reject traversal, absolute/drive/UNC/stream paths,
dot files, reserved Windows device names, trailing dots/spaces and double
extensions. Case-exact inventory names must match the actual directory entries.
These deliberately bounded v1 distribution rules may require a reviewed format
extension for some existing vendor bundles; never bypass them silently.

## Completeness, identity and bounds

The server-owned project root determines the sole `plugins/` scan root. Missing
root and empty root produce no candidates. Enumerate at most 128 direct children;
scan at most 4,096 nested entries and 64 MiB of bytes including manifest rereads.
One package permits 1,024 entries, 512 inventoried files, 16 MiB total initial
content and eight MiB per resource; manifests are at most 64 KiB and JSON depth
32. A global limit discards the whole scan, never an arbitrary truncated set.
An invalid individual package produces a redacted diagnostic and does not hide
independent valid candidates. Both owners of a namespace collision refuse.

Reject canonical-path redirection and symbolic links/junctions before traversal;
reject hard-linked regular resources. Check sizes before reading and metadata
around reads. Inventory must match every actual resource digest; an incomplete
copy cannot execute. Discovery does not lint PHP: even syntax-broken PHP may be
a structurally valid **unapproved** candidate when its exact bytes are declared.

Package identity is SHA-256 over this exact concatenation:

```text
"zendamod-plugin-package-v1\n" + sha256(raw plugin.json bytes) + "\n" + JSON
```

`JSON` uses `JSON_UNESCAPED_SLASHES`, with keys in order `directories`, `files`.
Directories are a sorted list of relative paths; files are a path-sorted object
of verified SHA-256 values. Directory changes, raw manifest changes and any
resource changes alter identity. The digest is not a publisher signature or
approval. Previously stored identity cannot restore physically missing code.

No filesystem transaction or hostile-code sandbox is claimed. Discovery is a
bounded observation, not an execution authorization: the future loader must
revalidate current identity/presence against authoritative approval before
loading/dispatch and workers before claims. Deploy complete directories from
staging; do not edit active approved code in place. General deployment races,
OPcache behavior and HTTP/worker generation handling need separate integration
evidence. Ordinary PHP cannot undo requests already executing code.
