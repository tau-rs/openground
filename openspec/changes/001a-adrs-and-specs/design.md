# Design · 001a ADRs and capability spec skeletons

## Context

See proposal.md. Inputs: the handoff of 2026-11-01 (the drafts of ADRs 0040–0048), `design/adr-challenge.md` (keep / amend / drop / defer verdicts), `design/keep-drop-crates.md` §4 (each amendment checked against the code) and §5 (Q-1, Q-2, Q-3 decided), `design/glossary.md` (the only vocabulary), the 39 old ADRs now in `design/adr/`. The old ADR format is `# ADR NNNN · title` · Date · Status · Source · Context · Decision · Consequences; the new ones keep it.

## Goals / Non-Goals

**Goals:** every decision of the reset has a numbered, committed home; a reader can tell an amended ADR from an untouched one at a glance; the four capability specs exist so Change 002 writes deltas instead of first specs.

**Non-Goals:** rewriting the ten kept ADRs beyond nothing (their "trim" notes are recorded in ADR 0048, not applied); writing requirements the ADRs do not decide; any code or config under `crates/`.

## Decisions

- **Amended ADRs are rewritten in place, same file name, same number.** Why: every reference in the glossary, the challenge, the review and arch issues is by number, and a file rename would break the slugs for a cosmetic gain. One honest consequence: some slugs read stale (`0005-context-pack.md` holds the projection); the title line carries the new name and the superseded term appears in Context.
- **An amended ADR carries a second Status line: `Status: accepted · amended 2026-11-01 (ADR 0048; reset session 31 Oct – 1 Nov 2026)`, and its Context opens with one sentence on what the original decided.** Why: the record must stay honest about what changed without keeping two full texts. One honest consequence: the original wording is only in git history (`tau-rs/arch@9e087ea`) until 001b folds it in.
- **Kept, dropped and deferred ADRs stay byte-for-byte as copied; ADR 0048 is the only index of their status.** Why: the brief says copy them as they are, and one place to look (0048) beats 27 status edits. One honest consequence: a reader opening `0006-arch-init.md` alone does not see it is dropped; they must read 0048.
- **ADRs 0040–0048 are written from the handoff drafts, not re-derived from the glossary.** Why: the drafts carry the reasoning and the rejected options; the glossary carries only the result. One honest consequence: the change blocks until the handoff file is in hand; no gap is filled by inference.
- **The capability specs are new-capability deltas in this change, archived into `openspec/specs/` in the same PR.** Why: OpenSpec creates a main spec only from an archived delta, and the brief wants the four living specs on `main`. One honest consequence: the PR carries the archive commit, so `openspec/changes/001a-adrs-and-specs/` is already under `archive/` when the PR merges.
- **A spec requirement exists only where an ADR decides an observable behaviour; everything else is a heading with one line naming the ADR or change that will decide it.** Why: the config rule says one behaviour per requirement in glossary words, and inventing requirements to fill a skeleton would be the assumption-filling the brief forbids. One honest consequence: `loupe`, `map` and `devops` are mostly headings; `core` carries most of the requirements.
- **ADR 0010's amendment states both facts: two depths exist as a per-run option today; the sequencing "syntactic tier first, resolved tier supersedes it for the same worktree state" is built in Change 002.** Why: the review found the challenge's wording described something the code does not do. One honest consequence: 0010 names a future task inside an accepted ADR; Change 002's tasks must cite it.

## Risks / Trade-offs

- [The handoff drafts differ from the glossary or the §5 decisions] → the §5 decisions (dated 2026-11-01, later than the drafts) win; the ADR text records the draft's wording as rejected. Named in the ADR, not silently adapted.
- [`openspec validate --strict` warns on a requirement over 500 characters or a scenario without WHEN/THEN] → each requirement is one sentence; edge cases go into scenarios.
- [A glossary word is missing for something an ADR decides] → stop and say so; the glossary is amended in a separate commit of this PR, with Titouan's yes.

## Open Questions

None that change the artifacts. The handoff file is an input, not a question.
