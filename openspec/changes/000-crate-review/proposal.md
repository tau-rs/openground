# Proposal · 000 crate review

## Why

The reset session (31 Oct – 1 Nov 2026) re-founded the project in ten decisions and nine new ADRs. Before any code moves, the eight crates of `tau-rs/arch` must be read against those decisions, or Change 001 (the monorepo fold and rename) moves cockpit code into the core without noticing.

## What Changes

- A written verdict per crate: keep · split · move out, each tied to a decision.
- The file-level cut `arch-views → project`, and the cockpit cluster inside `arch-facts`, file by file.
- Each amended ADR (0004 0005 0010 0012 0016 0023 0025 0026 0032 0033 0034 0035 0037) confirmed against the code, or corrected.
- Every contradiction between code and a decision reported as a question for Titouan, with a recommendation.
- No code moves. No rename. No ADR text rewritten.

## Assumption confronted

None: foundation. Pass criterion: Change 001 can be executed from `design/keep-drop-crates.md` alone, without re-reading the crates, and the three open questions in its §5 are answered before the rename.

## Out of scope · gates

Out of scope: archiving repos, creating the monorepo, renaming, committing ADRs 0040–0048 (Change 001); any crate code (Change 002). No gate is approached: this change writes under `design/` and `openspec/` only.

## Capabilities

### New Capabilities
None. This change decides where code goes; it changes no behaviour (`skip_specs: true`).

### Modified Capabilities
None.

## Impact

- `design/keep-drop-crates.md` (the deliverable), `design/glossary.md` and `design/adr-challenge.md` (inputs, committed as the record).
- Change 001's step list: the fold is three repos, not five (`arch-fixtures` and `arch-design` are already inside `arch`); the stale-write guard moves from `driver` into the core rather than leaving with it.
