# Design · 000 crate review

## Context

See proposal.md. The record read: `tau-rs/arch@9e087ea` (eight crates, `fixtures/checks`, `docs/`, ADRs 0001–0039), `design/adr-challenge.md`, `design/glossary.md`, the handoff of 2026-11-01.

## Goals / Non-Goals

**Goals:** a mechanical map for Change 001; every contradiction surfaced, none resolved by the reviewer.

**Non-Goals:** deciding the open questions; writing ADR text; moving code.

## Decisions

- **The review lives at `design/keep-drop-crates.md`, not in this change's `design.md`.** Why: Changes 001 and 002 consult it after this change is archived, and it sits beside the ADRs it amends. One honest consequence: this `design.md` is a pointer, and the archived change carries no copy.
- **"Move out" means out of `[workspace.members]`, history kept, path preserved under a non-member directory.** Why: the cockpit product will want `session`, `driver`, `forge` back and git history is cheap to keep. One honest consequence: dead code sits in the tree until the cockpit is framed; CI must not build it.
- **The stale-write guard is cut out of `driver` and kept in the core.** Why: Q8 makes `guard` a core call; the decision is ~20 lines of `guard::pre` plus the read-hash state. One honest consequence: the element-scope veto and the Bash allow-list, which share the function today, leave with the harness.
- **ADR 0010's amended wording is corrected, not confirmed.** Why: the code has both tiers as a per-run option; "syntactic first, resolved in the background" is a sequencing the store does not do yet. One honest consequence: wireflow 1's S1 → S2 is a Change 002 task, not a free property.

## Risks / Trade-offs

- [The three §5 questions are answered after the rename] → the rename pass touches the fact model twice (golden facts and `schema_version` bump twice). Mitigation: answer them before Change 001 step 3.
- [`propose.rs`'s module walk and sides heuristic are one function] → the `project` / lens cut needs a small refactor before the move. Mitigation: named in the cut table; a Change 002 task.

## Open Questions

Carried in `design/keep-drop-crates.md` §5 (Q-1 `Port.side` as a fact · Q-2 the guard's home · Q-3 where a lens is turned on). They change Change 001's rename list, not this change; asked one at a time.
