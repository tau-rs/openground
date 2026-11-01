# Crate review · keep / split / move out

- Date: 2026-11-01
- Change: 000 of the reset handoff (tau-rs/openground#1)
- Read: `tau-rs/arch@9e087ea` (`main`, 2026-10-23), the eight crates under `crates/`, `fixtures/checks`, `docs/*.md`, ADRs 0002–0039, against decisions Q1–Q10, the nine new ADRs 0040–0048, `adr-challenge.md` and `glossary.md`.
- Nothing moved. This file says where each piece goes and what the code disagrees with.

## In plain words

The old `arch` engine is eight Rust crates. Two of them, `facts` and `analyze`, are the surveyor that reads a repository and writes down what is in it; they are the core and stay. One, `views`, computes what is shown and checked; it becomes `project`, the core's one output. Two, `api` and `cli`, are the doors into the engine; they stay, but half of each is doors into things the core no longer has. Three, `session`, `driver`, `forge`, run agents, judge their work and talk to GitHub; they are the cockpit the reset decided the core must not contain, and they leave the workspace whole.

The surprise is not in the three cockpit crates. It is that cockpit words leaked into the two core crates: `facts` stores sessions, plans, records and the tool layer's state next to the fact model; `analyze`'s watcher reads the tool layer's files to tell an agent's write from yours. Those pieces are listed file by file below so the cut is mechanical. One analogy: the surveyor's notebook had the foreman's shift roster stapled into it; the roster comes out, the notebook stays.

Two things the code says that the decisions did not expect, and must be settled before Change 001's rename or Change 002's `project`: the fact model carries `Port` and `Side` as computed facts (the glossary drops "side as computed" and "port"), and the stale-write guard (Q8: a core call) lives today inside `driver`, the crate that leaves. Both are in §5.

```
                       today (arch)                          openground
  arch-facts    ──────────────────────────────▶  facts        (keep, minus a session cluster)
  arch-analyze  ──────────────────────────────▶  analyze      (keep, minus watcher attribution)
  arch-views    ──────────────────────────────▶  project      (rename + split: pack leaves)
  arch-api      ──────────────────────────────▶  api          (keep: serve · project · check; MCP added)
  arch-cli      ──────────────────────────────▶  cli          (keep: serve · project · guard · check)
  arch-driver   ─┬─ tool_layer/guard.rs stale-write ─────────▶  guard (core, new home: api or facts)
                 └─ everything else ─────────────────────────▶  out of the workspace
  arch-session  ──────────────────────────────▶  out of the workspace (cockpit)
  arch-forge    ──────────────────────────────▶  out of the workspace (cockpit)
```

## 1. Verdict per crate

| crate | lines | verdict | why (decision) |
|---|---:|---|---|
| `arch-facts` | 5 014 | **keep, split** | the fact model, store, hashes and `.arch/` readers are Q3's facts and declarations (ADR 0001, 0002, 0009, 0031 kept). `session/`, `arch_dir/{sessions,archive,tool_layer}.rs`, the session half of `event.rs` and the `plan_drafts` table are cockpit (Q2, ADR 0041). |
| `arch-analyze` | 10 917 | **keep, trim** | two depths exist and `incremental_equals_cold` is green; this is Q9's two-tier facts. `watch/tag.rs` (attribution through tool-layer files) and `git.rs`'s `Arch-Element` reading move or rename (ADR 0041: tags are opaque; the core never reads a session's state). |
| `arch-views` | 2 526 | **split → `project`** | `findings`, `placement` and the map half of `pack` are the projection (ADR 0042); `propose` is the hexagonal lens's suggestion (ADR 0047); the plan/element half of `pack` is the context pack, superseded (ADR 0005 → 0042). File cut in §2. |
| `arch-api` | 2 238 | **keep, trim** | `rpc`, `serve`, `check` are ADR 0034 amended (`initialize · project · guard · check`). `init.rs` is dropped (ADR 0006 → 0048), `session.rs` (753 lines) leaves, `tool_layer.rs` is the port the driver needed and goes with it. |
| `arch-cli` | 1 843 | **keep, trim** | one binary. `Init`, `Mcp` (the element-scoped server), `Hook`, `Session` and their tests (`tests/session.rs`, `tests/tool_layer.rs`) leave; `Serve`, `Check` stay; `Project`, `Guard` are new (ADR 0023 amended). |
| `arch-driver` | 4 172 | **move out, one file stays** | the `Driver` trait, claude-code adapter, replay, stream parser, MCP element server, commit tool, Bash allow-list and element-scope veto are the harness's confinement (Q2, ADR 0012 amended: no driver, no hook installed by openground). The stale-write decision in `tool_layer/guard.rs` is the one piece Q8 keeps as a core call: see §5 Q-2. |
| `arch-session` | 3 991 | **move out** | state machine, planner, judge, gate, review, accept: every word on ADR 0041's "nothing else" list. Deferred with ADRs 0003, 0013–0015, 0017–0019, 0021, 0022. |
| `arch-forge` | 1 780 | **move out** | GitHub PRs, reviews, checks, merge strategy: cockpit (ADR 0018 deferred). |
| `fixtures/checks` | — | **keep** | the invariant harness and budgets. MAP numbers that name the Theia app fall to the map product; `incremental_equals_cold` and the golden runner are Change 002's gate. |

"Move out" means: history kept, crate removed from `[workspace.members]`, path preserved under a non-member directory (`attic/` or a branch) until the cockpit product frames them. Not deleted.

## 2. The file-level cut · `arch-views` → `project`

The projection contract (ADR 0042) is scope · altitude · lens · since over nodes · links · marks. Nothing in `views` computes altitude or scope yet; what exists is the `item` altitude of one lens (`intent`) rendered as findings, and a text rendering of the unit. The cut:

| file | lines | goes to | as |
|---|---:|---|---|
| `src/findings.rs` | 331 | `project::marks` | `check_rules` is `project --lens intent` at altitude item: a `Finding` is a mark on a link. Keep the allow matching; `origin: core` stays. |
| `src/placement.rs` | 149 | `project::nodes` | which area an item sits in, from the `area` declaration (ADR 0004 amended). `Placement.side` stays optional and is read only when a lens asks. |
| `src/propose.rs`: the module walk in `propose_areas` (one area per top-level module of the root crate, a single file included), `placed`, `source_root` (lines 238–282) | ~70 | `project::nodes` (derivation) | areas derived from the module tree: the default placement with zero declarations (ADR 0004, 0041 "a repo with zero declarations has a complete map"). Today this walk is interleaved with the sides logic inside one 75-line function; it comes apart as its own `fn`. |
| `src/propose.rs`: `Groups::side` (the sides heuristic, lines 140–237), the **split** on mixed driving/driven children, `order`, `rule = hexagon \| layers` (the rest of `propose_areas`) | ~210 | `project::lenses::hexagonal` | the ADR 0027 sides heuristic survives only as a **suggestion** (ADR 0047). Split depends on sides, so it is lens-side too. `is_io` and `is_test_code` are shared helpers `findings.rs` imports today; they land in a `project` helper module both read. |
| `src/pack.rs` lines 75–254 (`header`, `map`, `port_line`, `links_between`) | ~180 | `project::render::text` | the text rendering of the unit at altitude area: areas, their links by kind, counts, ports. This is the `text` output of ADR 0042. Needs the `by side` grouping made optional. |
| `src/pack.rs` lines 255–304 (`rules`, `findings`) | ~50 | `project::render::text` | rules and open findings as text: the `intent` lens rendered. |
| `src/pack.rs` lines 1–74, 305–821 (`PackInput.plan/element`, `descriptions`, `element_section`, `reach_line`, `plan_section`, `element_line`, `label_line`, `position`, …) | ~560 | **out** (cockpit) | the planner's and judge's context pack (ADR 0005, superseded). `descriptions` (area descriptions as text) returns later as the `description` declaration kind (ADR 0045), not now. |
| `src/lib.rs` `Error::UnknownElement` | — | out | with the pack. |
| `tests/rules.rs`, `tests/smallsvc.rs` | 508 | `project` tests | unchanged. |
| `tests/propose.rs` | 397 | split like `propose.rs` | areas and split tests → `project`; `a_hexagon_like_smallsvc`, `the_module_that_registers_a_route_is_driving`, `a_unit_without_an_entry_is_layers_with_order_only` → the hexagonal lens's tests. |

What `project` does **not** have yet and Change 002 builds: altitude `unit`, scope (`unit · area · item+hops`, the four symbol scopes), stable ordering as a stated guarantee (today's "fixed order" in `pack.rs` is per list, not per document), folding at altitude item (ADR 0037; `Item.self_type` is still open, arch#98), JSON output of nodes · links · marks (today's `CheckOutput` is findings only), `since`.

## 3. The file-level cut · the cockpit cluster inside `arch-facts`

| file | lines | verdict | note |
|---|---:|---|---|
| `src/model.rs` | 792 | keep | see §5 Q-1 on `Port`, `Side`, `Section`. `Commit.element: Option<String>` becomes a generic trailer read (ADR 0041: `Openground-Element` is an opaque tag); the field can stay as `tags`. |
| `src/hash.rs`, `src/store/mod.rs` + `schema.sql` | 988 | keep, trim | drop `plan_drafts` (ADR 0020 dropped). `view_cache` is the projection cache; keep. `commits`, `tree_commits`, `branches`, `worktrees` are Q3's changes and worktrees (observed). |
| `src/arch_dir/{mod,areas,rules,allows}.rs` | 603 | keep | the declaration readers (ADR 0032 amended): `Rule` gains `reason`, `Rules::v1_template()` and `V1_LINTS` move to the hexagonal lens, `Allow` already has `by · reason · at`. `ColumnRule` (hexagon · layers in `areas.toml`): see §5 Q-3. `board_path` goes. |
| `src/arch_dir/sessions.rs`, `archive.rs` | 381 | out | `.arch/sessions/`, `refs/notes/arch` as a session archive (ADR 0003 deferred). ADR 0046 reuses the notes ref for **statuses**, a new writer; nothing of `Archive` is reusable beyond the `git notes` shell-out. |
| `src/arch_dir/tool_layer.rs` | 284 | out, one struct re-homed | `ToolLayerState.read{path: sha256}` is the "last read hash per actor" the guard needs (§5 Q-2); the rest (`expected`, `writes`, session · element ids) is harness state. |
| `src/session/*` | 665 | out | plans, records, threads, state. |
| `src/event.rs` | 123 | split | `FilesChanged`, `FactsUpdated`, `FindingsUpdated`, `Unplaced`, `Subsystem` stay (the engine's own events); `SessionState`, `Ask`, `Denied`, `WhatsNew` and `Attribution::Session` leave. `Attribution` collapses to an actor id (ADR 0041). |
| `src/session/mod.rs::{Timestamp, now}` | — | keep, move to `model` or `hash` | `allows.rs` and `tool_layer.rs` import them; they are not session-shaped. |
| `tests/arch_dir.rs` lines 85–426 (plan, session folder, archive, record tests) | ~340 | out | with the code. |

## 4. The amended ADRs against the code

| ADR | challenge says | code says | verdict |
|---|---|---|---|
| 0004 areas | override = an `area` declaration; `side` written only on lens acceptance; no init | `areas.toml` is overrides only, `side` optional, `propose_areas` computes sides for init | **confirmed**; `propose` splits (§2). |
| 0005 context pack → projection | `check` = `project --lens intent` | `check` calls `check_rules` directly; the pack calls `check_rules` too; same function, two renderers | **confirmed**; `pack.rs` splits (§2). |
| 0010 two tiers, syntactic first, resolved in the background | — | `Depth::{Syntax, Resolved}` is a per-run **option**, not a pipeline: `analyze()` at `Resolved` waits for rust-analyzer to load (3.7 s smallsvc) and only falls back on failure. No "syntactic now, resolved later" on one store. | **correct the amendment's wording**: the code has both tiers but not the sequencing. Wireflow 1's S1 → S2 needs `index(Syntax)` then `index(Resolved)` on the same worktree with the second replacing the first's deltas. The store's key includes "effective depth" so the two tiers get two tree keys today; the amendment should say the resolved tier **supersedes** the syntactic tree for the same worktree state. One Change 002 task, not a design gap. |
| 0012 guard as a call | `openground guard pre/post`: stale-write refusal + attribution; no veto, no driver, no installed hook | the guard is `driver::tool_layer::guard::pre`: stale-write **then** element-scope veto **then** expected-content; `post` records reads and confirms writes. Bash allow-list too. | **confirmed with a cut**: stale-write (`pre` lines 112–130) and `post`'s `record_read`/`confirm` are the core call; the veto, the Bash list and `expected` (watcher attribution) are harness-side. §5 Q-2 asks where the core half lives. |
| 0016 trailers | opaque tags the core reads | `git.rs` reads `Arch-Element` into `Commit.element`; `commit.rs` (driver) writes it | **confirmed**; rename, make generic (`Commit.trailers` already holds every trailer). |
| 0023 CLI | `serve · project · guard` (+ `check`) | `init · serve · check · mcp · hook · session` | **confirmed**; `init`, `mcp` (element-scoped), `hook`, `session` leave; `project`, `guard` new. Note: the amendment lists MCP "in `api` day one" (Q9) while `arch mcp` today is the **harness** server (tools `read · check · commit · ask`, one element). The core's MCP is a different server (`project · guard · check`); none of today's `mcp.rs` carries over except the JSON-RPC line loop, which `rpc.rs` already has. |
| 0025 direction | direction is a fact; the grain is a lens finding | `LinkKind::family()` exists; no direction smell is computed anywhere yet | **confirmed**, nothing to move. |
| 0026 budgets | engine budgets stay; first paint, fold, pan move to the map | `budgets.toml` already splits `runs_in = arch` vs `arch-app` | **confirmed**; rename `arch-app` rows to `map`. |
| 0032 rules/allows | rule = declaration with `reason`; no init template; template rules to the hexagonal lens | `Rule` has no `reason`; `Rules::v1_template()` writes the three rules and five lints | **confirmed**; add `reason`, move the template. `[lints]` levels in `rules`: under ADR 0047 lints belong to a lens, so `[lints]` becomes the lens-on switch. Flagged, not decided (§5 Q-3). |
| 0033 link kinds | `calls-port` → `calls-trait`, `depends-on-port` → `depends-on-trait` | `LinkKind::CallsPort`, `DependsOnPort`, serialized kebab-case; `schema_version = 1`; golden facts pinned | **confirmed**; the rename changes the golden and bumps `schema_version` (ADR 0033 "a renamed enum value bumps"). Part of Change 001 step 3. |
| 0034 wire contract | methods `initialize · project · guard · check`; editor methods removed | `rpc.rs` has `initialize` only; arch#192 (editor methods) is open, unbuilt | **confirmed**; close arch#192 as dropped. |
| 0035 lints | `god-module`, `cycle` → structure lens; the other three → hexagonal lens | none of the five is implemented (`V1_LINTS` are opaque strings) | **confirmed**; nothing to move, the ADR text changes. |
| 0037 folding | fold rules become projection rules at altitude item | nothing implemented; `Item.self_type` open (arch#98), `rows_fold_into_owner` gated (arch#190) | **confirmed**; both issues become Change 002 tasks. |

## 5. Where the code contradicts a decision · questions for Titouan

Reported, not adapted around. One at a time, each with a recommendation.

**Q-1 · `Port`, `Side`, `Section` are computed facts.** `facts.ports[]` carries `side: driving | driven` and `section` (platform-services · data-stores · …), computed by the analyzer from entries and I/O externals. The glossary drops "side (as computed)" and ADR 0047 says the core has no hexagonal mode. A port's `side` here is the I/O direction (inbound route vs outbound sql), not the area side; its three readers today (`propose.rs` sides heuristic, `findings.rs` externals rule, `pack.rs` port listing) are all lens-side or renderer-side.

*Decided 2026-11-01 (Titouan): option A.* Keep the fact. `Port` → `boundary`, `Port.side` → `direction: inbound | outbound`; `Section` unchanged. Rule, recorded in `openspec/config.yaml`: **facts use only words the compiler and the operating system know; every architecture style's words live in its lens.** Glossary gains `boundary`, `direction`, `external`, `table` as facts. Done in Change 001 step 3, in the same golden-facts change and `schema_version` bump as the 0033 link-kind rename. Rejected: stripping the field and letting each lens recompute it (same answer, every lens redoes the work, the analyzer forgets a true fact).

**Q-2 · The stale-write guard lives in the crate that leaves.** Q8 makes `guard` a core call; the code puts the decision in `arch-driver/src/tool_layer/guard.rs` and its state in `arch-facts/src/arch_dir/tool_layer.rs`, keyed by session · element. *Recommendation:* a `guard` module inside `api` (the three verbs' home), with per-actor read hashes under `.openground/cache/guard/<actor>.json`; `facts` keeps the state type stripped to `read{path: sha256}` and `writes[]`. Alternative: a fourth core crate `guard`; rejected for now, it is ~150 lines.

**Q-3 · `areas.toml` `rule = hexagon | layers` and `rules` `[lints]`.** Both are declarations that pick a lens. ADR 0047 says a lens is "turned on"; nothing says where that is written. *Recommendation:* one `lenses = ["hexagonal"]` line in `areas.toml` replaces `ColumnRule` and `[lints]`; `structure` is implicit. Alternative: keep `[lints]` per-lint levels (finer, but a person writes five lines to turn on one lens).

## 6. Not in this change, filed on discovery

- arch#192 (editor methods), arch#182 (CLI `session`), arch#178 (MCP `read · check · commit · ask`), arch#95, #92, #76 (session, judge, forge) decide things the core no longer has: close as superseded by ADR 0048 when Change 001 commits the ADRs.
- arch#98 (`self_type`), arch#190/#99 (fold rows), arch#85, #86, #84 (syntax pass on zed, incremental resolve): Change 002 tasks.
- arch#118, #119 (`for:sett`, `for:arch-app` pointer updates): moot after the monorepo fold.
