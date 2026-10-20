# Dataflow engine (internal, Order-governed)

Decision 119 adds a contract-based dataflow engine: an **internal component
governed by Order**, not a fourth primary API. It exists so data can travel
between independent registered capabilities without consumers hardcoding
dependencies, implementation locations, URLs, calculation procedures or
presentation transformations.

The model is DATA -> TRANSFORMATION -> FORMULA -> RESULT -> PRESENTATION, and
every stage stays independently replaceable, testable and versioned. The engine
never renders and never becomes an HTML generator.

## Where it lives

| Component | Layer | Responsibility |
| --- | --- | --- |
| `Domain/DataflowGraph` | Domain | Structure only: steps, declared dependencies, references, a deterministic topological order and the bounded profile. |
| `Domain/DataflowReference` | Domain | The closed reference grammar: a step id, the reserved `result` segment and declared property names only. |
| `Application/DataflowValidator` | Application | Order-side validation: capability existence, effects, declared outputs, and type compatibility of every reference. |
| `Application/DataflowExecutor` | Application | Execution: substitution, invocation through Order, result collection, failure propagation and a bounded trace. |
| `OrderApi::executeGraph` | Application | The governed entry point. Direct invocation remains the lightweight path. |
| `ChaosApi::compose` | Application | Dependency-aware **proposals**. Still untrusted data until Order validates them. |

The engine is an internal component of the Order/Apis kernel owner, so it adds no
cross-owner edge, no new public contract and no plugin SDK.

## The graph contract

~~~json
{
  "plan_version": "1.0",
  "steps": [
    {"id": "s1", "capability": "fixture.read", "version": "1.0.0", "args": {"value": 5}, "depends_on": []},
    {"id": "s2", "capability": "fixture.double", "version": "1.0.0", "args": {"value": {"ref": "s1.result.value"}}, "depends_on": ["s1"]}
  ],
  "output_ref": "s2.result.value"
}
~~~

Bounds: at most 16 nodes, depth 16, fan-out 16 and 64 references per graph.
Step ids keep the existing plan grammar (`s1` to `s16`). A plan is
still a plan: the same envelope is used by Chaos proposals and by direct
composition, and Order validates it identically.

## Typed result references

A reference is an object with exactly one key, `ref`, whose value matches
`s<N>.result[.<property>...]`. It is never evaluated as PHP or JavaScript.
There is no arithmetic, no indexing, no wildcard and no free traversal: every
property must be declared in the producing capability output schema.

Types are checked, never coerced. A produced `string` cannot satisfy a
declared `integer` input, and the only widening allowed is an integer
where a number is declared. Incompatible connections refuse with
`CONTRACT_INCOMPATIBLE` before anything executes. Missing required values
and undeclared produced properties refuse with `INVALID_INPUT` and
`INVALID_OUTPUT_REFERENCE`.

## Execution, limits and safety

Every step is executed by `OrderApi::invoke`, so resolution, exact version
pinning, authorization, schema validation, effect restriction, deadlines, output
validation and the per-composition quota all keep working exactly as before. The
engine adds no privilege of its own and never accepts an identity from a graph:
the server-created `ExecutionContext` is used unchanged.

Only `none` and `read` effects may run. Write and external effects
stay refused, exactly as the documented implementation already refuses them.

A failed step never becomes a default value: its dependents are reported as
blocked and the graph fails with the first stable error code. The trace records
the step, the capability id@version, the status and the duration only, with no
arguments, results, actor identifiers or resources.

## Real website integration

The rankings workflow now runs as one composed graph. The synthetic game read
supplies the rows, `url.resolve` builds the management link from the
ranking identifier, `math.evaluate` derives the column width and
`locale.resolve` supplies the label. `OrderRankingReader` keeps the
exact V1 payload, so the frozen contract and every saved widget are unchanged;
`WebsiteApis::rankingWorkflow()` exposes the composed view model.

## Error codes

`INVALID_INPUT` (malformed graph, unresolved reference, missing value),
`CAPABILITY_UNAVAILABLE` (unknown id or version),
`DEPENDENCY_UNAVAILABLE` (an effect the runtime does not permit),
`CONTRACT_INCOMPATIBLE` (declared types disagree),
`INVALID_OUTPUT_REFERENCE` (an undeclared produced property),
`MISSING_DEPENDENCY` (a reference to an absent step),
`PERMISSION_DENIED` or `AUTHENTICATION_REQUIRED` (authorization),
`TIMEOUT`, `RATE_LIMITED`, `CONTRACT_VIOLATION`,
`INTERNAL_ERROR`, and the existing `DEPENDENCY_UNAVAILABLE` for a
refused effect.

## Known limitations

Concurrency is not implemented: the executor is deterministic and sequential,
which respects the graph and leaves a future concurrent executor possible.
Partial results are not exposed to consumers, write effects are not supported,
and the graph cannot reference a step that did not run. Plugin-contributed
workflows are not possible yet because the MainAPI lifecycle is still
unimplemented; nothing here bypasses approval.

See [WEBSITE_INTEGRATION](WEBSITE_INTEGRATION.md), [building blocks](BUILDING_BLOCKS.md),
[ORDER](ORDER.md), [CHAOS](CHAOS.md) and [STATE](../../STATE.md).