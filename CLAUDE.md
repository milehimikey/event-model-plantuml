# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A single-file PlantUML preprocessor library (`event-model-plantuml.iuml`, ~870 lines) that turns Event Modeling notation into PlantUML deployment diagrams. There is no build system, no test runner, and no source code in another language — the `.iuml` file *is* the deliverable, and the `examples/*.puml` files are the integration tests.

The library is a re-vocabulary fork of [chilit-nl/plantuml-event-modeling](https://github.com/chilit-nl/plantuml-event-modeling) aligned to Dimytruk's Event Modeling and Dilger's CQRS/event-sourcing patterns, plus machine-readable `$schema` annotations intended for AI-driven decomposition.

## Render examples (the only "build")

PlantUML is installed at `/opt/homebrew/bin/plantuml`. Examples are authored as `.puml` and committed alongside their rendered `.png` — when you change the library or an example, regenerate the PNG so the README screenshots match.

```bash
# Render one example
plantuml examples/00-quickstart.puml

# Render every example (run from repo root)
plantuml examples/*.puml
```

`.puml` files include the library via the GitHub raw URL on `main`. When iterating locally on `event-model-plantuml.iuml`, temporarily swap the `!include_once` to a relative path (`!include_once ../event-model-plantuml.iuml`) so renders pick up your edits, then revert before committing.

Only the **default GraphViz layout engine** is supported — do not introduce skinparams or directives that require `smetana` or `elk`.

## Architecture

### Public vs internal API

Convention enforced by naming, not by tooling: any procedure or function whose name starts with `$_` is internal (`$_addNode`, `$_renderLaneTypes`, `$_lookupLaneIndex`, etc.). Names without the underscore are the public surface (`$actor`, `$command`, `$event`, `$renderEventModelingDiagram`, …). Don't call `$_*` from example diagrams, and when adding a new public element, route it through the existing `$_addNode` / `$_addArrow` plumbing rather than emitting PlantUML directly.

### Pseudo data structures via variable name mangling

PlantUML's preprocessor has no arrays, maps, or objects. The library simulates them by computing variable *names* (e.g. `$event-2-items-3-name`) with helper functions like `$_node`, `$_heading`, `$_arrow`, then using `%set_variable_value` / `%get_variable_value` (wrapped in `$_set` / `$_get` / `$_exists`) to read and write them. Every "data structure" you see — lanes, nodes, arrows, alias storage, lane-index lookups — is just a naming convention layered on flat preprocessor variables. The technique is inherited from chilit-nl; preserve it when extending.

### Two-phase render pipeline

Element procedures (`$screen`, `$command`, `$event`, …) **do not emit PlantUML at call time**. They only record state into the pseudo structures. Nothing renders until `$renderEventModelingDiagram()` is invoked at the end, which calls `$_renderLaneTypes()` (lanes/nodes/auto-arrows) followed by `$_renderArrows()` (explicit user arrows). This is why `$renderEventModelingDiagram()` must be the **last statement before `@enduml`** — anything that writes state after it won't appear in the diagram.

### Lane types

Five lane types organize the diagram top-to-bottom: `wireframe` (actors/screens), `commandview` (commands and read models share a row), `event` (aggregates/events), `automation`, `policy`, `externalsystem`, `question`. The `commandview` collapsing is intentional — `$command` and `$readmodel` both call `$_addNode("commandview", 0, …)` so they share a single row. Aggregates with the same `$context` argument get visually grouped via `$_renderContextWrapper`.

### Auto-arrows and auto-layout

`$enableAutoLayout()` toggles automatic horizontal positioning based on definition order; `$enableAutoAlias()` makes duplicate names safe by appending `1`, `2`, …  When auto-layout is on, `$_autoAddArrow` infers arrows between sequentially defined elements only when the transition is semantically valid (command→event, event→readmodel, etc.) — pass `$arrow = 0` on an element to suppress its incoming auto-arrow, or use the explicit `$arrow($from, $to)` / `$commandarrow` / `$eventarrow` / `$automationarrow` / `$policyarrow` / `$integrationarrow` procedures.

### Schema annotations

`$schema = "field:type:constraint, …"` is the recommended way to attach data shapes. `$_schemaToFields` parses it into PlantUML creole for display *and* leaves the raw string in source for AI parsing. `required` renders as `•`; anything else renders unmarked. If both `$schema` and `$fields` are set, `$schema` is the machine-readable source and `$fields` overrides display.

### Backward-compatibility shims

The bottom of `event-model-plantuml.iuml` aliases the old chilit-nl names (`$wireframe`→`$screen`, `$view`→`$readmodel`, `$extra`→`$automation`, `$externalsystem`→`$integration`, `$configureWireframeLane`→`$actor`, `$configureEventLane`→`$aggregate`, `$enableAutoSpacing`→`$enableAutoLayout`, `$externalsystemarrow`→`$integrationarrow`). New examples should use the new names; the shims exist purely so pre-existing diagrams keep rendering.

## Authoring constraints (easy to trip on)

- **Single line per call.** PlantUML's preprocessor does not support multi-line procedure calls. Long `$schema` strings stay on one line, however unwieldy.
- **No hyphens in element names.** `$event(Order-Placed)` breaks rendering. Use spaces (`$event(Order Placed)`) — aliases are derived by stripping spaces.
- **Naming vocabulary is load-bearing.** Events past tense (`OrderPlaced`), commands imperative (`PlaceOrder`), read models nouns (`OrderList`), aggregates singular nouns (`Order`). The README, `ELEMENT_MAPPING.md`, and `EVENT_MODELING_PROCESS_QUICK_REF.md` all enforce this — don't "fix" example names that look stylistically off; the tense is the convention.
- **Process discipline in examples.** `GETTING_STARTED.md` and the `subscription-billing-*.puml` series demonstrate the canonical "base model first, slice later" workflow — the progressive examples (`-base` → `-with-policies` → `-with-rules` → `-sliced`) are pedagogical and should stay separated, not consolidated.

## Reference documents

- `README.md` — public API reference and element catalog
- `ELEMENT_MAPPING.md` — translation from generic Event Modeling vocabulary to this library's syntax
- `EVENT_MODELING_PROCESS_QUICK_REF.md` — 7-step workshop process
- `GETTING_STARTED.md` — first-diagram walkthrough
- `examples/event-modeling-phases-guide.md` — full process guide
- `examples/fintech-billing-system-README.md` — annotated walkthrough of the largest example
