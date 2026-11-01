# Tasks

## 1. Read

- [x] 1.1 Read the eight crates, `fixtures/checks`, `docs/*.md`, ADRs 0001–0039 against Q1–Q10, ADRs 0040–0048, `adr-challenge.md`; verify: every crate and every amended ADR has a row in `design/keep-drop-crates.md`.

## 2. Write

- [x] 2.1 `design/keep-drop-crates.md` §1–§4: verdicts, `views → project` cut, `facts` cockpit cluster cut, amended ADRs confirmed or corrected; verify: cited line ranges match `tau-rs/arch@9e087ea`.
- [x] 2.2 §5: each code-vs-decision contradiction as a question with a recommendation; §6: issues filed on discovery; verify: no contradiction is resolved in the file.
- [x] 2.3 Commit `design/glossary.md` and `design/adr-challenge.md` as the record this review reads; verify: both files present and referenced from `openspec/config.yaml`.

## 3. Settle

- [ ] 3.1 Record Titouan's answers to Q-1, Q-2, Q-3 in §5 as decisions, with the date; verify: §5 has no open question left and Change 001's rename list is derivable from it.
- [ ] 3.2 `openspec validate 000-crate-review` passes; verify: exit 0.

## Workflow follow-up

- Archive the change once PR tau-rs/openground#2 is merged (`openspec archive 000-crate-review`).
