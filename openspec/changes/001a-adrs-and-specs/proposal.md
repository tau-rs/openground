# Proposal · 001a ADRs and capability spec skeletons

## Why

The reset session (31 Oct – 1 Nov 2026) decided the core/products split in nine new ADRs and amended thirteen old ones, but the record of those decisions lives only in a handoff file and in `design/adr-challenge.md`'s one-line table. Change 001b (the fold and rename) and Change 002 (increment 1) cite ADR numbers that do not yet exist as files; this change writes them, so every later change can point at a committed decision instead of a chat.

## What Changes

- `design/adr/0001…0039.md` copied from `tau-rs/arch@9e087ea` as they are (done in the first commit; 001b folds the full history later).
- `design/adr/0040…0048.md` written from the handoff drafts: core and products · core contents · projection contract · loupe · method and repo · declaration kinds · audit · lenses · superseded ADRs.
- The thirteen amended ADRs (0004 0005 0010 0012 0016 0023 0025 0026 0032 0033 0034 0035 0037) rewritten per ADR 0048 and `design/keep-drop-crates.md` §4, folding in the three §5 decisions: Q-1 `Port` → `boundary`, `side` → `direction: inbound | outbound`; Q-2 `guard` is a core crate beside `project`, `api` holds no logic, keyed by actor; Q-3 a lens is turned on by `lenses = ["hexagonal"]` in `areas.toml`. ADR 0010's amendment says two depths exist as a per-run option and that "syntactic first, resolved in the background" is a sequencing Change 002 builds.
- The four capability specs created as living specs: `core`, `loupe`, `map`, `devops`. Headings where nothing is decided; a requirement only where an ADR decides a behaviour.
- No code. No crate moves. No rename.

## Assumption confronted

None: foundation. Pass criterion: a reader with `design/adr/` and `openspec/specs/` alone, and no access to the handoff or the chat, can state every decision Q1–Q8 and Q-1–Q-3 and name the ADR that holds it; `openspec validate --strict` passes; every ADR number cited in `design/glossary.md`, `design/adr-challenge.md`, `design/keep-drop-crates.md` and `openspec/config.yaml` resolves to a file.

## Out of scope · gates

Out of scope: archiving `arch`, `arch-app`, `sett`; the monorepo fold; the rename `arch → openground` in code (001b, 001c); the golden-facts change and `schema_version` bump the Q-1 and 0033 renames imply (001b step 3); closing arch#192 #182 #178 #95 #92 #76 (follow-on once ADR 0048 is on `main`). No gate is approached: this change writes under `design/` and `openspec/` only; it builds nothing of loupe, map, guard, MCP, lenses or declarations.

## Capabilities

### New Capabilities
- `core`: the headless engine. Facts, declarations, changes, actors, worktrees, projections, lenses, guard, check, notes, and the API over them (ADR 0040, 0041, 0042, 0045, 0046, 0047; 0012, 0023, 0034 amended).
- `loupe`: the client kit. The four behaviours select · climb · jump · refresh and the way a view is declared; reads only projections (ADR 0043).
- `map`: product #1. The structure view at three altitudes, lens layouts, map budgets (ADR 0040; 0026, 0037 amended).
- `devops`: the monorepo's build, CI, release and workspace tooling as a product (ADR 0044).

### Modified Capabilities
None: `openspec/specs/` is empty on `main`.

## Impact

- `design/adr/`: 39 files copied, 9 created, 13 rewritten.
- `openspec/specs/{core,loupe,map,devops}/spec.md`: created at archive from this change's deltas.
- `design/glossary.md`, `design/adr-challenge.md`, `design/keep-drop-crates.md`: unchanged; their ADR references become resolvable.
- Follow-on this change unlocks: arch#192 #182 #178 #95 #92 #76 close as superseded by ADR 0048; 001b reads the amended 0033 and Q-1 for its rename list.
