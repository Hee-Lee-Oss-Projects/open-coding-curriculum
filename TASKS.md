# TASKS.md — open-coding-curriculum

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer — TO BE SECURED; must be named before M0 exit, target 2026-07-12) · Lane: donated

## How these tasks map to Elyos

Each row below becomes one **Task JSON** validated against
`packages/schema/src/schemas.ts`. Field mapping:

- **id** → stable `occ-<area>-NNN` (the table's ID column).
- **title** → the task title.
- **project** → `"open-coding-curriculum"`.
- **type** → one of `code | research | writing | data | design-spec | maintenance`
  (lesson/exercise authoring = `writing`; schema/guide/spike = `design-spec`; runner/SSG/CI =
  `code`; dataset/glossary/graph = `data`; partner outreach + source verification = `research`;
  re-review/runtime-drift recheck = `maintenance`).
- **lane** → `donated` (this project has **no funded tasks**; if one were added it would require
  `fundedBudgetUsd`).
- **priority** → `high | medium | low` (set per task in the JSON; the table's Size column is the
  *token estimate*, not priority).
- **domain** → array, e.g. `["education","programming","computer-science","open-education","accessibility"]`.
- **riskTier** → `low` baseline. Authoring still carries the **correctness + pedagogy review
  discipline** (see quality floor) even at `low`. No `high` tasks exist (no high-stakes advice).
- **urgent** → boolean (default `false`).
- **deliverable** → `pr | dataset | document | translation` (Deliverable column).
- **tokenEstimate** → `small | medium | large` (Size column).
- **status** → `open` for all (new backlog).
- **context / objective / acceptanceCriteria[] / output** → from the task detail.
- **resources[]** → links to the schema, pedagogy/style guide, runner contract, source list.
- **requestor** → partner org (**TO BE SECURED**) — omitted/empty until confirmed.
- **verifiedNeed** → **`false`** on all tasks until an adopting partner is secured (honest).
- **outputLicense** → prose `CC-BY-4.0`; **code/exercise solutions `MIT`** (or `CC0`); datasets
  `CC-BY-4.0`.

Reviewer column: **Tech** = technical/correctness reviewer (author ≠ reviewer); **Ped** = pedagogy
reviewer (never the author); **Maint** = maintainer; **Steward** = last-mile owner.

**Per-lesson quality floor (applies to every `writing` authoring task below).** Beyond
schema-validity: (a) the **automated correctness gate** passes — reference solution → tests →
`solutionVerified:true`, and **starter code must NOT already pass**; (b) **≥ 2 authoritative
independent citations** (official docs/spec preferred); (c) a **signed-off pedagogy checklist**
(single clear objective, honest prerequisites, plain language, worked example before exercise,
hints scaffold not spoil, neutral examples); (d) inclusion in the maintainer's **rolling ≥ 10%
audit sample**; (e) **technical + pedagogy review, author ≠ reviewer**. Non-original media/code
additionally requires a **verified source URL**. See PLAN.md → Quality gates.

---

## Milestone M0 — Foundation & cold-start

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| occ-decide-000 | Decide authoring format (JSON vs Markdown+front-matter) — schema/tooling/runner depend on it | design-spec | small | low | document | — | Maint |
| occ-guide-001 | Write pedagogy + style guide (lesson/exercise templates, hint convention, refusal/defensive-only rules) | design-spec | medium | low | document | — | Ped, Maint |
| occ-schema-002 | Define lesson + exercise JSON Schemas (incl. `solutionVerified`, closed enums, license allow-list) + AJV CI validation | design-spec | medium | low | pr | decide-000, guide-001 | Maint |
| occ-runner-003 | Define autograder runner contract + build the sandboxed **CI correctness backend** (no network, FS read-only, CPU/mem/time caps, non-root) | code | medium | low | pr | schema-002 | Tech, Maint |
| occ-repo-004 | Scaffold repo (TS/ESM/pnpm, lint, validate, CI wiring) | code | small | low | pr | — | Maint |
| occ-lesson-005 | Reference Python lesson + graded exercise: "variables & printing" (low-risk exemplar, end-to-end) | writing | small | low | document | schema-002, runner-003, guide-001 | Tech, Ped |
| occ-spike-006 | WASM runner spike (Pyodide load size/perf, lazy-load, cache, offline fallback) | design-spec | small | low | document | — | Maint |
| occ-spike-007 | SSG selection spike (Astro/Eleventy/other): WCAG support, runner-embedding ease, build determinism — decide **before M2** | design-spec | small | low | document | — | Maint |

**Acceptance criteria — key tasks**

- **occ-decide-000** (predecessor of schema-002)
  - Authoring format (JSON vs Markdown+front-matter) chosen and recorded with rationale before
    schema/runner/seed authoring begins; schema, validation, and runner target that format.
- **occ-schema-002** (depends on decide-000 + guide-001)
  - Schemas cover Lesson (id, slug, title, language, level, objectives, prerequisites, concepts,
    body, runnableExamples, exercises, license, version, lastReviewed, authors, reviewers, sources)
    and Exercise (id, lessonId, language, prompt, starterCode, referenceSolution, tests, hints,
    rubric, difficulty, expectedRuntimeMs, resourceLimits, solutionVerified, isOriginal, license,
    version, lastReviewed, authors, reviewers).
  - `language`, `level`, `difficulty` are **closed enums**; `license` constrained to a **closed
    allow-list** (`original`, `CC0`, `CC-BY-4.0`, `CC-BY-SA-4.0`, `public-domain`, plus `MIT` for
    code); off-list/blank fails validation.
  - `solutionVerified` is documented as **CI-set, never hand-edited**; schema marks an exercise
    invalid if it lacks `tests` or a `referenceSolution`.
  - Non-original media/code (`isOriginal:false`) requires a verified source URL.
  - AJV validation runs in CI and fails on any invalid lesson/exercise.
- **occ-runner-003** (the correctness gate's engine)
  - Defines `run(code, tests, limits) → { passed, failures[], stdout, durationMs, resourceUse }`
    as a language-agnostic contract with a Python adapter first.
  - CI backend executes reference solutions in a sandbox: **no network, read-only FS, CPU/memory/
    wall-clock limits, non-root**; a solution that hangs, exceeds limits, or touches the network
    fails.
  - CI sets `solutionVerified:true` only when the reference solution passes its tests; sets the
    "starter must fail" check (starter run must NOT pass).
  - `pnpm build && pnpm test && pnpm lint` pass.
- **occ-lesson-005** (end-to-end exemplar)
  - Original prose; ≥ 2 authoritative cited sources (Python docs preferred), nothing copied.
  - Includes ≥ 1 runnable example and ≥ 1 graded exercise; reference solution `solutionVerified:true`;
    starter code fails the tests.
  - Schema-valid; passes technical + pedagogy review by reviewers other than the author.

**Definition of Done (M0):** a **named Maintainer/Owner is in place** (hard gate — M0 cannot be
declared done with the Owner still TBD; target 2026-07-12); authoring format decided; pedagogy/
style guide merged; lesson+exercise schemas + AJV CI validation merged; the **runner contract +
sandboxed CI correctness backend** working and gating publication; repo scaffolding green
(`pnpm build && pnpm test && pnpm lint`); one schema-valid, correctness-verified, technically and
pedagogically reviewed Python lesson+exercise published; WASM-runner and SSG spikes concluded with
decisions recorded before M2. End-to-end authoring of a correct, runnable, auto-graded lesson is
proven.

---

## Milestone M1 — Seed Python module & partner outreach

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| occ-lesson-101 | Python module batch A: input/output, variables, types, arithmetic (4 lessons + exercises) | writing | large | low | document | lesson-005 | Tech, Ped |
| occ-lesson-102 | Python module batch B: conditionals, loops, strings, lists (4 lessons + exercises) | writing | large | low | document | lesson-005 | Tech, Ped |
| occ-lesson-103 | Python module batch C: functions, basic error handling (2 lessons + exercises) | writing | medium | low | document | lesson-005 | Tech, Ped |
| occ-runner-104 | In-browser **Pyodide runner**: run examples + grade exercises client-side (lazy-load, offline fallback) | code | large | low | pr | runner-003, spike-006 | Tech, Maint |
| occ-data-105 | Glossary + concept/prerequisite graph for the Python module | data | medium | low | dataset | lesson-101..103 | Maint |
| occ-research-106 | Partner outreach packet + log first conversations (library/nonprofit/school/Carpentries) | research | small | low | document | lesson-005 | Steward |

**Acceptance criteria — key tasks**

- **occ-lesson-102 (conditionals/loops/strings/lists)**
  - Each lesson: one clear objective, worked example before the exercise; ≥ 1 graded exercise with
    `solutionVerified:true` and a failing starter.
  - Hints scaffold toward the solution without revealing it; examples are neutral/inclusive.
  - Schema-valid; technical + pedagogy review by non-authors; ≥ 2 cited authoritative sources.
- **occ-runner-104 (in-browser Pyodide runner)**
  - Learner runs examples and submits exercises **with zero install and zero account**; grading
    happens client-side against the exercise tests; learner code never leaves the device.
  - Runtime lazy-loads/caches; a graceful "download to run locally" fallback exists where WASM is
    unavailable.
  - Runner UI is keyboard- and assistive-tech-usable (accessibility precondition for M2).
- **occ-research-106 (partner outreach)**
  - A reusable outreach packet describing the curriculum, licenses, zero-install learner model, and
    the adoption ask.
  - ≥ 1 outreach packet sent; ≥ 1 partner conversation logged with outcome/next step.
  - Honest status recorded; `verifiedNeed` flips to `true` only on written partner confirmation.

**Definition of Done (M1):** a complete Python beginner module (≈ 8–10 lessons, each with ≥ 1
graded CI-verified exercise) published and schema-valid; the in-browser Pyodide runner executes
examples and grades exercises locally; glossary + concept/prerequisite graph merged; partner
outreach started with ≥ 1 logged conversation.

---

## Gate G1 — Post-M1 go/no-go (partner check, blocks M2 site build)

Before any M2 learner-site task (site-201+) starts, evaluate:

- **Go:** **≥ 1 partner is at least verbally committed** to adopting the curriculum → proceed to M2
  as written, and the SSG choice (spike-007) must already be decided.
- **No-go (no partner committed):** do **not** halt. Switch to the **fallback "delivered"
  definition** — ship the schema-valid corpus + the versioned **CC-BY dataset** (data-203) + a
  **self-contained offline runnable bundle** (lessons + Pyodide runner, openable without a server)
  as the delivered outcome, **defer the hosted learner site** (site-201/202/204) until a partner
  lands, and keep `occ-research-106` (outreach) as the active critical-path task.

This gate keeps "delivered, not merged" honest when partner adoption — the true outcome — has not
yet landed.

---

## Milestone M2 — Learner site & dataset release

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| occ-site-201 | Static-site generator: render lessons + embed in-browser runner (accessible, WCAG-AA aim) | code | large | low | pr | lesson-101..103, runner-104, spike-007, **Gate G1: Go** | Maint |
| occ-site-202 | Browse: concept/prerequisite navigation + search + graded-feedback UI | code | medium | low | pr | site-201, data-105 | Maint |
| occ-data-203 | Publish versioned CC-BY corpus (lessons + exercises + tests) + provenance manifest | data | small | low | dataset | site-201 | Maint |
| occ-site-204 | Deploy live learner site (static hosting) + canonical attribution | code | small | low | pr | site-202 | Maint |

**Acceptance criteria — key tasks**

- **occ-site-201**
  - Renders every schema-valid lesson; build **fails if any exercise has `solutionVerified:false`**
    or any lesson is invalid.
  - Embeds the in-browser runner so examples run and exercises grade in-page; no server-side code
    execution.
  - Accessibility checks pass for both prose and the runner/editor (keyboard nav, focus order, alt
    text, contrast — WCAG-AA target).
- **occ-data-203**
  - Full corpus exported as a single versioned dataset (prose CC-BY-4.0, code MIT/CC0) with
    per-asset provenance + license and a published attribution string.
  - Dataset includes `solutionVerified` status and the pinned runtime version per exercise.

**Definition of Done (M2):** live, accessible, searchable learner site at a public URL where a
learner completes an exercise with **zero install and zero account**; first versioned CC-BY corpus
published with provenance; concept/prerequisite navigation works.

---

## Milestone M3 — Second language & autograder hardening

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| occ-runner-301 | JavaScript runner adapter against the existing runner contract (reuse, not redesign) | code | medium | low | pr | runner-003, runner-104 | Tech, Maint |
| occ-lesson-302 | JavaScript beginner module (≈ 8–10 lessons + graded exercises) | writing | large | low | document | runner-301, guide-001 | Tech, Ped |
| occ-code-303 | CI rule: every exercise has tests + `solutionVerified:true` + starter-fails; block publish otherwise | code | small | low | pr | schema-002, runner-003 | Maint |
| occ-code-304 | Execution sandbox hardening + documentation (CI: no-net/RO-FS/limits/non-root; learner: WASM isolation, caps, graceful timeout) | code | medium | low | pr | runner-003, runner-104 | Tech, Maint |
| occ-feat-305 | Hint/feedback system: ordered hints + actionable failure diagnostics in the runner UI | code | medium | low | pr | site-202 | Ped, Maint |

**Acceptance criteria — key tasks**

- **occ-code-303**
  - CI fails any exercise lacking a test suite, lacking a reference solution, with
    `solutionVerified:false`, or whose starter code already passes the tests.
- **occ-lesson-302 (JavaScript module)**
  - Reuses the same schemas + runner contract; each lesson has ≥ 1 graded exercise with
    `solutionVerified:true` and a failing starter; technical + pedagogy reviewed by non-authors.
  - Demonstrates the language-agnostic model: adding JS required an adapter + content, not schema
    changes.
- **occ-code-304 (sandbox hardening)**
  - CI executor documented and verified: no network, read-only FS, CPU/memory/wall-clock caps,
    non-root, no secrets.
  - Learner runner: WASM isolation, resource caps, graceful timeout; a runaway exercise cannot hang
    or abuse the learner's browser.

**Definition of Done (M3):** a complete JavaScript module shipped through the same schema/runner;
the runner contract demonstrably reused via an adapter; the correctness CI rule enforced for every
exercise; execution sandbox hardened and documented for both backends; hint/feedback system live.

---

## Milestone M4 — Breadth & adoption

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| occ-lesson-401 | Third-language beginner module start (≥ 5 lessons + exercises; language partner-driven) | writing | large | low | document | runner-301, guide-001 | Tech, Ped |
| occ-research-402 | Confirm partner adoption + collect a learner use report | research | small | low | document | site-204, research-106 | Steward |
| occ-maint-403 | Runtime-drift re-check: re-run every `solutionVerified` against updated pinned runtimes | maintenance | small | low | pr | code-303 | Tech, Maint |
| occ-maint-404 | Re-review cadence: refresh lessons past their `lastReviewed` window | maintenance | small | low | document | lesson-005..302 | Tech, Ped |

**Acceptance criteria — key tasks**

- **occ-research-402 (adoption — the real outcome)**
  - ≥ 1 education partner confirms adoption in writing; a use report is collected (how it was used,
    learner-gain feedback).
  - `verifiedNeed` updated to `true` on confirmation; ≥ 1 external reuse of the CC-BY corpus
    documented.
- **occ-maint-403 (runtime-drift re-check)**
  - Every exercise's reference solution is re-run against the current pinned runtime; any newly
    failing exercise is flagged and fixed before it can stay published.
- **occ-lesson-401 (third language breadth)**
  - ≥ 5 lessons in a third language (chosen with partner input); each schema-valid with
    `solutionVerified:true` exercises; in-browser runner viability confirmed (spike) before commit.

**Definition of Done (M4):** a third-language module started (≥ 5 lessons); ≥ 1 partner adoption
confirmed in writing with a use report; ≥ 1 documented external reuse of the CC-BY corpus;
runtime-drift re-check and re-review cadence running.

---

## Backlog / future

| ID | Title | Type | Size | Risk | Deliverable | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| occ-i18n-501 | Localization framework + first translated lessons | code/writing | large | low | translation | Needs schema i18n decision (Open Q6); content i18n deferred |
| occ-feat-502 | Instructor mode: teacher guides + answer keys wrapping the corpus | writing | medium | low | document | For classroom partners; keeps learner site spoiler-free |
| occ-feat-503 | Local-only progress tracking (browser storage, opt-in, no server/PII) | code | medium | low | pr | Must preserve no-PII stance (PLAN §Security) |
| occ-contrib-504 | External contribution + review-scaling model (esp. correctness gate) | design-spec | medium | low | document | Opens UGC review burden (Open Q8) |
| occ-research-505 | Learner-gain survey instrument + outcome dashboard | research | small | low | document | **Prerequisite for the "≥75% learning-gain" metric**; design for ≥ 25 respondents over a rolling 90-day post-adoption window. Promote ahead of research-402 |
| occ-lesson-506 | Additional beginner modules (data/SQL basics, files, simple projects) | writing | large | low | document | Only after two languages are solid |
| occ-feat-507 | Public structured-export API over the corpus | code | medium | low | dataset | Only if downstream demand emerges |

---

---

## Generated task index

> Auto-generated by Elyos task-decomposition agent · 2026-06-29 · Branch: elyos/task-decomposition

All 34 `tasks/*.json` files listed below are schema-valid (validated against `packages/schema`).
33 were generated from backlog rows; 1 (`occ-schema-002`) is the pre-existing seed.

| File | Title | Type | Milestone | Priority | Deliverable |
| --- | --- | --- | --- | --- | --- |
| occ-decide-000.json | Decide authoring format (JSON vs Markdown+front-matter) | design-spec | M0 | high | document |
| occ-guide-001.json | Write pedagogy + style guide | design-spec | M0 | high | document |
| occ-schema-002.json | Define lesson + exercise JSON Schemas (+ CI validation) — **seed** | design-spec | M0 | high | pr |
| occ-runner-003.json | Autograder runner contract + sandboxed CI correctness backend | code | M0 | high | pr |
| occ-repo-004.json | Scaffold repo (TS/ESM/pnpm, lint, validate, CI wiring) | code | M0 | high | pr |
| occ-lesson-005.json | Reference Python lesson + graded exercise: "variables & printing" | writing | M0 | high | document |
| occ-spike-006.json | WASM runner spike (Pyodide load size/perf, lazy-load, cache) | design-spec | M0 | high | document |
| occ-spike-007.json | SSG selection spike (Astro/Eleventy/other) | design-spec | M0 | high | document |
| occ-lesson-101.json | Python module batch A: input/output, variables, types, arithmetic | writing | M1 | high | document |
| occ-lesson-102.json | Python module batch B: conditionals, loops, strings, lists | writing | M1 | high | document |
| occ-lesson-103.json | Python module batch C: functions, basic error handling | writing | M1 | high | document |
| occ-runner-104.json | In-browser Pyodide runner (lazy-load, offline fallback) | code | M1 | high | pr |
| occ-data-105.json | Glossary + concept/prerequisite graph for the Python module | data | M1 | medium | dataset |
| occ-research-106.json | Partner outreach packet + log first conversations | research | M1 | high | document |
| occ-site-201.json | Static-site generator: render lessons + embed in-browser runner | code | M2 | medium | pr |
| occ-site-202.json | Browse: concept/prerequisite navigation + search + graded-feedback UI | code | M2 | medium | pr |
| occ-data-203.json | Publish versioned CC-BY corpus + provenance manifest | data | M2 | medium | dataset |
| occ-site-204.json | Deploy live learner site (static hosting) + canonical attribution | code | M2 | medium | pr |
| occ-runner-301.json | JavaScript runner adapter against the existing runner contract | code | M3 | medium | pr |
| occ-lesson-302.json | JavaScript beginner module (≈ 8–10 lessons + graded exercises) | writing | M3 | medium | document |
| occ-code-303.json | CI rule: every exercise correctness-gated; block publish otherwise | code | M3 | medium | pr |
| occ-code-304.json | Execution sandbox hardening + documentation | code | M3 | medium | pr |
| occ-feat-305.json | Hint/feedback system: ordered hints + actionable failure diagnostics | code | M3 | medium | pr |
| occ-lesson-401.json | Third-language beginner module start (≥ 5 lessons + exercises) | writing | M4 | medium | document |
| occ-research-402.json | Confirm partner adoption + collect a learner use report | research | M4 | medium | document |
| occ-maint-403.json | Runtime-drift re-check: re-run every solutionVerified | maintenance | M4 | low | pr |
| occ-maint-404.json | Re-review cadence: refresh lessons past their lastReviewed window | maintenance | M4 | low | document |
| occ-i18n-501.json | Localization framework + first translated lessons | writing | backlog | low | translation |
| occ-feat-502.json | Instructor mode: teacher guides + answer keys | writing | backlog | low | document |
| occ-feat-503.json | Local-only progress tracking (browser storage, opt-in, no PII) | code | backlog | low | pr |
| occ-contrib-504.json | External contribution + review-scaling model | design-spec | backlog | low | document |
| occ-research-505.json | Learner-gain survey instrument + outcome dashboard | research | backlog | low | document |
| occ-lesson-506.json | Additional beginner modules (data/SQL basics, files, projects) | writing | backlog | low | document |
| occ-feat-507.json | Public structured-export API over the corpus | code | backlog | low | dataset |

**Fan-out applied:** `occ-i18n-501` spans `code/writing` in the backlog table; per TASKS.md mapping rules (`Translation: type:"writing"+deliverable:"translation"`), it is represented as a single `writing`/`translation` task covering both the localization framework and the translated content (the code-framework aspect is captured in the acceptance criteria).

---

## Example task JSON

```json
{
  "id": "occ-schema-002",
  "title": "Define lesson + exercise JSON Schemas (+ CI validation)",
  "project": "open-coding-curriculum",
  "type": "design-spec",
  "lane": "donated",
  "priority": "high",
  "domain": ["education", "programming", "computer-science", "open-education", "accessibility"],
  "riskTier": "low",
  "urgent": false,
  "deliverable": "pr",
  "tokenEstimate": "medium",
  "status": "open",
  "context": "open-coding-curriculum is an open CC-BY (prose) / MIT (code) curriculum of beginner programming lessons with graded, runnable exercises across languages. Every exercise must be uniformly machine-readable and provably solvable: the reference solution is executed against its tests in CI, which sets solutionVerified. Correctness must be enforceable as data, not by hand, because a broken or unsolvable exercise actively mis-teaches beginners.",
  "objective": "Define and publish versioned JSON Schemas for a lesson and an exercise, wired into CI via AJV, that capture all fields and enforce the correctness, license, and provenance invariants.",
  "acceptanceCriteria": [
    "Lesson schema covers id, slug, title, language, level, objectives, prerequisites, concepts, body, runnableExamples, exercises, license, version, lastReviewed, authors, reviewers, sources.",
    "Exercise schema covers id, lessonId, language, prompt, starterCode, referenceSolution, tests, hints, rubric, difficulty, expectedRuntimeMs, resourceLimits, solutionVerified, isOriginal, license, version, lastReviewed, authors, reviewers.",
    "language, level, and difficulty are closed enums; license is constrained to a closed allow-list (original, CC0, CC-BY-4.0, CC-BY-SA-4.0, public-domain, MIT); any other/blank value is invalid.",
    "solutionVerified is documented as CI-set (never hand-edited); an exercise missing tests or referenceSolution fails validation.",
    "Non-original media/code (isOriginal:false) requires a verified source URL.",
    "AJV validation runs in CI and fails on any invalid lesson/exercise.",
    "pnpm build && pnpm test && pnpm lint pass."
  ],
  "resources": [
    "C:\\code\\elyos\\packages\\schema\\src\\schemas.ts",
    "C:\\code\\elyos\\planning\\projects\\open-coding-curriculum\\PLAN.md",
    "C:\\code\\elyos\\docs\\good-deed-definition.md"
  ],
  "output": "A merged PR adding the lesson + exercise JSON Schemas, AJV-based validation script, and CI wiring, with the schemas documented in the repo.",
  "requestor": "",
  "verifiedNeed": false,
  "outputLicense": "MIT"
}
```
