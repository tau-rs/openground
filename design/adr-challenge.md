# ADR challenge · 39 ADRs against Q1–Q8 · second pass · project renamed openground

Verdicts: **keep** (true as is, cosmetic trim) · **amend** (true, rewritten for the core/products split) · **drop** (decides something no longer in the core; a dropping ADR records why) · **defer** (cockpit product; parked, not binding).

Decisions applied: Q1 core is the product, screens are products · Q2 core knows no session, plan, ask, gate, judge · Q3 core = facts, declarations, changes, actors, worktrees (observed); projections = node · link · mark · Q4 headless core, map is product #1 · Q5/Q6 projection contract, loupe, sett stays a design system · Q7 OpenSpec changes, monorepo, devops is a product · Q8 guard as a call; declaration kinds area · description · rule · flow; audit by recomputation + notes; lenses (structure built in, hexagonal offered).

## Keep · 10

| ADR | Why it stands |
|---|---|
| 0001 store: sqlite cache, plain text committed | Trim: `.openground/` no longer holds plan drafts or sessions; `refs/notes/openground` is the audit snapshot. |
| 0002 facts keyed by tree id, per-file deltas | The facts model; `incremental_equals_cold` is the core's main invariant. |
| 0007 unit = main bin or lib | A fact. |
| 0008 non-Rust ignored except `migrations/*.sql` | A fact. |
| 0009 confidence resolved · guessed · declared | Trim: "cannot gate a merge" is cockpit policy. |
| 0010 degrade to syntax-level facts | Trim: status bar, What's new, Checks tab are product words. |
| 0028 spawned worker is an entry kind | A fact. |
| 0030 inputs outside the tree in the package id | Core key. |
| 0031 items and ids | Node ids in projections. |
| 0036 engine binary stays `arch` | Trim: app search order moves to the map product. |

## Amend · 12

| ADR | What changes |
|---|---|
| 0004 areas derived, overrides only | An override is an **area** declaration. `side` is written only on accepting a hexagonal-lens suggestion; no `arch init`. |
| 0005 context pack | Becomes **the projection** (Q5). `check` = `project --lens intent`. Judge and Ask leave. |
| 0012 confinement hooks | Rewritten: **guard as a call** (`openground guard pre/post`, also on the API): stale-write refusal + write attribution by actor. No element veto, no driver, no hook installed by arch. |
| 0016 commits with trailers | `Openground-Element`/`Openground-Session` are opaque tags the core reads. "Through arch's tool", "denied", "one per element" are harness/cockpit rules. |
| 0023 settings, errors, secrets, packaging, telemetry | Keep: no telemetry, errors never modal. CLI = `serve · project · guard` (+ `check` alias). Packaging, secrets, Theia leave the core. |
| 0025 direction on call-family links | Direction is a fact. The grain is a finding under the hexagonal (or layers) lens, against declared sides. |
| 0026 budgets | Engine budgets stay. First paint, fold/expand, pan/zoom move to the map product. |
| 0032 rules and allows TOML | Rule = a declaration with `reason`; allow = a silence. Pattern grammar aligned on archaven/dependency-cruiser. No init template; the three template rules belong to the hexagonal lens. |
| 0033 link kinds | Rename `calls-port` → `calls-trait`, `depends-on-port` → `depends-on-trait`; `wires` unchanged. 21 kinds stay. |
| 0034 openground-api wire contract | Core API; methods are `project`, `guard`, `initialize`. Editor methods removed. |
| 0035 what the lints check | `god-module`, `cycle` in the structure lens; `leaky-port`, `speculative-abstraction`, `unresolved-dyn` in the hexagonal lens. |
| 0037 one row per top-level item | Fold rules become projection rules at altitude item. "Remembered per view", ⌘P go to loupe/map. |

## Drop · 8

| ADR | Why |
|---|---|
| 0006 `arch init` | §1: no init. A repo with zero declarations is complete. |
| 0011 worktree layout `<repo>-w<n>` | The core observes worktrees, never creates them. |
| 0020 plan drafts in cache | Plans are the cockpit's. |
| 0024 five repositories | Monorepo. |
| 0027 init sides rule | Not applied by the core. The heuristic lives on as the hexagonal lens's **suggestion**. |
| 0029 init order rule | No init. Field list folds into 0004; stable ordering is a projection guarantee. |
| 0038 workspace trust off | Theia-specific. One doc sentence survives. |
| 0039 editor hints from the engine | The core serves no editor. |

## Defer · 9 · to the cockpit product

0003 threads and records (the notes idea is reused by the audit ADR) · 0013 judge · 0014 concurrent sessions (overlap of two worktrees' deltas is a core projection, on request) · 0015 restart · 0017 agents never push · 0018 forge GitHub first · 0019 conflicts as a resolve element · 0021 element identity · 0022 review without a plan.

## New ADRs to write · 9

| # | Decision |
|---|---|
| 0040 | Core and products: the core is headless; map, cockpit and others are products on its API (Q1–Q4). |
| 0041 | Core contents: facts · declarations · changes · actors · worktrees; projections as node · link · mark with anchors (Q3). |
| 0042 | Projection contract: scope · altitude · lens · since; deterministic order; no pixels (Q5). |
| 0043 | loupe: behaviours (select, climb, jump, refresh) and view definitions; uses sett (Q6). |
| 0044 | Method and repo: OpenSpec changes, one living spec per capability; monorepo; devops is a product (Q7). |
| 0045 | Declaration kinds: area · description · rule · flow; rule and flow have a status; any kind may carry a reason; git provenance. |
| 0046 | Audit: status recomputed from facts at a commit; `--record` snapshots to `refs/notes/openground`; CI, pre-commit, cockpit are callers. |
| 0047 | Lenses: a bundle of suggested declarations and lints; `structure` built in; `hexagonal` offered; nothing applied without acceptance. |
| 0048 | Superseded ADRs: the 8 drops and the 9 deferrals, with reasons, so the record stays honest. |

## Net effect on the crates

`facts`, `analyze` are the core; `views` becomes `project` (projections, lenses, folding); `api`, `cli` survive trimmed; `session`, `driver`, `forge` leave the core and wait for the cockpit.
