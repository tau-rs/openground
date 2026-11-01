# openground · glossary

Words that survived the reset session of 31 Oct – 1 Nov 2026. One line each; the choice behind a word where one was made. A word not here is not in the vocabulary of the core.

## Core

- **openground** — the project: the ground truth of a codebase, open source. Binary `openground`, alias `og`. Formerly `arch`; that word is now only a dropped name.
- **core** — the headless engine: facts, declarations, changes, actors, worktrees, projections, lenses, and the API over them. Has no screen, no host, no session.
- **product** — anything built on the core's API: the map, the cockpit, a tests view, the devops of the monorepo. A product never reaches past the API.
- **fact** — something derived from the code by the core: an item, a link, a hash, an entry. Carries a confidence. Never written by a person or an agent.
- **item** — what rust-analyzer calls an item (fn, struct, trait, impl, mod…), with an id `<crate>::<path>::<name>#<kind>`. The grain of altitude *item*.
- **area** — a group of items, derived from the module tree by default, overridable by an *area* declaration. The grain of altitude *area*.
- **unit** — the main bin or lib of the repo and its closure. One per repo in V1. The grain of altitude *unit*.
- **link** — a relation between two items: 21 kinds in four families (call, type, data, structure) plus `refers-to`. Five kinds carry direction. `calls-trait`, `depends-on-trait`, `wires` replace the former `*-port` names.
- **confidence** — `resolved` (from the compiler) · `guessed` (syntactic, by convention) · `declared` (by a person). Every fact carries one; a product shows it.
- **tier** — the two passes that produce facts: the syntactic tier (seconds, all guessed) and the resolved tier (rust-analyzer, in the background). Replaces "degraded" as the name of the first state.
- **anchor** — a file, line and content hash a node, link or mark points to. Every element on any screen has one; jumping to it is one move.
- **declaration** — something a person states about the code, stored in `.openground/` with git provenance. Four kinds: *area* (placement), *description* (free text on a node), *rule* (a checkable constraint), *flow* (a declared path through items). Any kind may carry a *reason*.
- **status** — what `check` says of a rule or flow at a commit: holds · violated (rule), holds · broken (flow). Area and description have no status.
- **finding** — a mark where a fact disagrees with a declaration. A finding on a guessed link warns, never blocks.
- **allow** — a deliberate silence of one finding, with who, why, and optionally until when. Replaces "silence" as the stored word; "silence" is the verb.
- **reason** — the free-text half of a declaration or an allow. Shown as declared; never checked.
- **change** — what differs between two trees, attributed to an actor and a worktree, carrying the opaque tags `Openground-Element` and `Openground-Session` if a commit has them. Read from git and from guard records.
- **actor** — an identity that writes: you, or an agent such as `agent-1`. Only used for attribution. The core knows nothing else about it.
- **worktree** — a git worktree, main checkout included. The core observes which one a change comes from; it never creates one.
- **guard** — the core call a harness makes before and after a write: refuses a stale write (file changed since the actor read it) and records who is writing. The only writer into *changes*.
- **projection** — the core's one output: a deterministic, stably ordered slice of nodes, links and marks with anchors, selected by *scope*, *altitude*, *lens*, *since*. Rendered as JSON, text or SARIF. Replaces "context pack" and "view model".
- **scope** — what a projection covers: the unit, an area, an item and its hops, a declared flow, or a symbol question: `callers(id)`, `callees(id)`, `references(id)`, `implementors(id)`.
- **altitude** — the grain of a projection: item · area · unit. "Climbing" is changing altitude.
- **lens** — a ready-made bundle of suggested declarations and lints a person turns on. `structure` is built in and always on (`cycle`, `god-module`); `hexagonal` is offered (sides suggestion, three template rules, `leaky-port`, `speculative-abstraction`, `unresolved-dyn`). A lens suggests; only an acceptance writes. Replaces "hexagonal is the law".
- **lint** — a generic check a lens contributes, producing findings without a per-repo rule.
- **check** — `project --lens intent` rendered as findings. An alias, not a second code path. `check --record` snapshots statuses into *notes*.
- **notes** — `refs/notes/openground`: recorded statuses per commit. A snapshot, never a source of truth; anything in it can be recomputed.
- **node · link · mark** — the three element kinds of a projection: a node is an item, area or unit; a link a relation; a mark something laid on a node or link (finding, changed-since, declaration).
- **api** — the three verbs `project · guard · check` over three transports: CLI, JSON-RPC socket, MCP. Never logic.

## Harness side (not arch)

- **harness** — whatever runs agents: Claude Code, openrig, a script. Owns sessions, plans, asks, gates, judge. Calls the core's API; the core never calls back.
- **hook** — a harness moment that runs a core command: session start pushes a projection, each write runs `guard`, session end runs `check`. No hook-only behaviour: every hook runs something that also exists as CLI and MCP.
- **MCP** — how an agent pulls from the core during a task. Day-one transport in `api`.

## Products

- **loupe** — the client kit: the four behaviours (select · climb · jump · refresh) and the way to declare a view (a rendering per element kind plus a layout). Reads only projections. Name chosen over scene, relief, prism: a loupe is what you look through; climbing is changing magnification.
- **view** — a product-side definition: projection parameters + rendering per element kind + layout. The map, a use-case flow, a tests view are views.
- **map** — product #1: the structure view at three altitudes, with lens layouts when sides are declared. Never talks to facts or declarations directly.
- **cockpit** — the later product for plan mode and supervision: task, plan, asks, gates, review. Owns `.openground/sessions/` if it needs one. Nothing of it in the core.
- **sett** — the tau-rs design system: tokens and components. Knows nothing of nodes.
- **devops** — the monorepo's build, CI, release and workspace tooling, as a product with its own capability spec.

## Method

- **capability** — an OpenSpec living spec: core · loupe · map · devops, more later.
- **change** (OpenSpec) — one increment: a proposal naming the assumption it confronts and its pass criterion, a spec delta, tasks. Archived into the living spec when done. Where the two meanings of "change" meet, say "OpenSpec change".
- **increment** — the unit of work; the same thing as an OpenSpec change, used when talking about assumptions rather than files.
- **assumption** — a belief the framing rests on, confronted by an increment: A (map worth opening with zero declarations), B (agent plans better with projections), C (git + tags tell what agents did), D (loupe makes a second view cheap), E (lens findings are useful).

## Dropped words

arch (as the project name) · witness · context pack · view model · session (in the core) · plan, ask, gate, judge (in the core) · init · side (as computed) · port (as a link-kind name) · degraded (as the name of the first tier) · scene, relief, plate, atlas, prism, easel.
