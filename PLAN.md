# PLAN.md — open-coding-curriculum

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated

> **Ownership gate.** The Owner/Maintainer is currently **TBD — TO BE SECURED**. A named
> maintainer **must be filled before M0 exit** (a hard M0 gate, not a nicety); target date to name
> the Owner is **2026-07-12**. Until then the curriculum schema, pedagogy/style guide, autograder
> contract, and Definition of Shipped have no accountable owner and M0 cannot be declared done.

> **Positioning (one line).** open-coding-curriculum is the *open, runnable, auto-graded* path from
> "never written a line of code" to "can read and write small programs" — original CC-BY lessons,
> MIT-licensed runnable examples, and graded exercises whose reference solutions are proven correct
> in CI, designed so a self-learner with only a browser (no install, no account, no payment) can
> actually learn. It is **not** a bootcamp, a certification mill, an exam-cram, or a leaderboard.

---

## Executive summary

**open-coding-curriculum** is an open library of beginner programming lessons paired with
**graded exercises and runnable examples**, spanning multiple languages (Python first, then
JavaScript, then a third), released as **machine-readable structured content** so it can power a
learner site, classroom courses, offline bundles, and downstream apps.

The core insight: the single biggest barrier to learning to code is not the absence of material —
it is that good material is fragmented, paywalled, ad-laden, often outdated or wrong, and almost
never gives a true beginner *immediate, correct feedback* on whether their code works. Tutorials
without exercises do not build skill; exercises without trustworthy auto-grading leave learners
stuck or, worse, learning the wrong thing. open-coding-curriculum closes that loop: every lesson
has runnable examples and at least one **graded exercise** whose **reference solution is executed
against its own test suite in CI**, so a learner gets correct, instant feedback and a maintainer
can guarantee the exercise is solvable and the answer is right.

Two properties make this an ideal fit for the Hee-Lee Oss donated lane. First, **each lesson + exercise
is a bounded, forkable unit** — near-unlimited fan-out with consistent quality. Second, the
**correctness gate is automatable**: "the reference solution passes its tests, in a sandbox, with
no network" is a machine-checkable invariant that catches the dominant failure mode (a broken or
unsolvable exercise) before any human reviews pedagogy.

Overall risk is **low**. The two headline gates are (1) a **license/provenance gate** — original
prose + original or verifiably openly-licensed media and code, with recorded provenance; no
copying from copyrighted books, courses, or problem banks — and (2) a **correctness + pedagogy
gate** — every exercise's reference solution proven correct in CI, plus a human review for
technical accuracy and beginner-appropriate teaching by someone other than the author. A third,
quieter gate is **execution safety**: learner code runs **in the learner's own browser via WASM**
(no server-side execution of untrusted code), and trusted reference solutions run only in a
locked-down CI sandbox.

This document specifies the problem, scope, architecture, licensing posture, review gates, a
phased roadmap (M0–M4), governance, risks, and the security/privacy stance. The itemized,
schema-mapped backlog is in **TASKS.md**.

## Problem & beneficiaries

**Who is helped.** Absolute and early beginners learning to program **without** money for a
bootcamp, a mentor to unstick them, or reliable internet for heavy IDEs and video — specifically:
self-taught learners, public-library and community-center patrons, students in under-resourced
schools, career-changers, refugees and displaced learners in coding-readiness programs, and the
instructors who serve them and need free, correct, reusable material they can teach from and remix.

**The need.** The need — that high-quality, *free*, *runnable*, *auto-graded* beginner programming
material is scarce relative to demand — is well supported in the abstract (it motivates Code.org,
freeCodeCamp, The Carpentries, university OER, and library code clubs worldwide). That is a
reasonable premise, **but a specific partner organization that will adopt this curriculum and
confirm it is fit for their learners is TO BE SECURED.** Until an education partner signs on,
`verifiedNeed` on individual tasks is honestly set to **`false`**, and the Definition of Shipped's
"adopted by at least one partner" clause remains an open dependency, not an assumption.

**Partner org.** TO BE SECURED — target profiles: a public-library digital-literacy/code-club
program, a nonprofit coding school for under-served learners, a Carpentries-style instructor
community, an under-resourced secondary-school CS department, or a refugee/displaced-learner
upskilling org. Early outreach is itself a milestone task (see Roadmap M1).

**Why not the existing web.** Existing beginner content is (a) mostly **not openly licensed**, so
it cannot be legally remixed into courses, offline packs, or apps; (b) **inconsistent on
correctness** — exercises with wrong or unsolvable "answers" actively mis-teach; (c) **not
runnable without setup**, which loses beginners at "install a toolchain"; and (d) optimized for
ads/engagement, not learning outcomes. open-coding-curriculum is the open, structured,
correctness-verified, zero-install alternative.

## Goals and non-goals

**Goals**
- Publish openly-licensed (CC-BY prose, MIT/CC0 code) beginner programming **lessons** with
  consistent quality and runnable examples.
- For every lesson, ship at least one **graded exercise** whose **reference solution is proven
  correct against its test suite in CI** before it can be published.
- Let a learner **run examples and submit exercises with zero install** — code executes in their
  own browser (WASM), giving instant, correct feedback with no account and no payment.
- Make all content **machine-readable** (validated JSON + Markdown) so third parties can build
  courses, offline bundles, and apps on it.
- Establish a **pedagogy + technical-correctness review standard** (author ≠ reviewer) so lessons
  are accurate *and* genuinely beginner-appropriate.
- Cover **multiple languages** through one language-agnostic content model (Python → JavaScript →
  a third), so the moat is the structure and the reviewed corpus, not any one runtime.
- Secure at least one education partner who adopts the curriculum and reports real learner use.

**Non-goals**
- **Not** a bootcamp, cohort program, or anything with enrollment, deadlines, or instructors-as-a-
  service.
- **Not** a certification, credential, or high-stakes **exam-cram** (e.g. AP CS, vendor certs).
  Where exam alignment is ever desired it is a separate project (`civics-exam-prep`-style) with its
  own accuracy-review bar; this project will not claim exam readiness it has not verified.
- **Not** a leaderboard, competitive-programming judge, or gamified streak engine. The goal is
  durable understanding, not engagement metrics.
- **Not** a host for video courses (still text, diagrams, and runnable code only — keeps provenance
  and accessibility tractable).
- **Not** a hosted IDE, cloud execution service, or a place that runs arbitrary learner code on
  **our** servers (execution is client-side by design — see Security).
- **Not** advanced/specialist tracks (ML, systems, security exploitation) in the planned phases —
  beginner fundamentals only.
- **Not** a teacher of offensive security, malware, scraping-for-abuse, or anything on the Hee-Lee Oss
  refusal list; security topics, if covered, are **defensive only**.
- **Not** an account system, social platform, or collector of learner personal data.

## Success metrics (outcomes)

Outcome-based and beneficiary-centric. Vanity metrics (raw pageviews, GitHub stars, "exercises
attempted") are explicitly **not** primary.

| Metric | Baseline (2026-06-28) | Target (first 3 phases) |
| --- | --- | --- |
| Published lessons with ≥ 1 graded, CI-verified exercise | 0 | ≥ 30 (one full Python module + start of a second language) |
| Exercises whose reference solution passes its tests in CI | n/a | **100% (hard gate — no exception)** |
| Education partners adopting the curriculum | 0 | ≥ 1 with written adoption + a use report |
| Beneficiary-reported learning gain (partner survey: "I could do something I couldn't before") | n/a | ≥ 75% agree, **≥ 25 respondents** over a **rolling 90-day** post-adoption window (target ≥ 30% response rate) |
| Lessons passing pedagogy + technical-correctness review (author ≠ reviewer) | 0 | 100% of published lessons |
| Content/media/code with recorded provenance + valid license | n/a | 100% (hard gate) |
| Learner runs an exercise with **zero install / zero account** | not possible | yes (in-browser runner shipped for ≥ 1 language) |
| Accessibility of the learner site | n/a | WCAG 2.1 AA target, audited |
| Documented external reuse (a course, offline pack, or app citing the CC-BY corpus) | 0 | ≥ 1 |

The partner-adoption and learning-gain rows are the **true outcome measures**; lesson count and
the dataset release are necessary enablers, not the goal. The learning-gain measure depends on a
survey instrument — backlogged as `occ-research-505` and a prerequisite for reporting that metric;
the ≥ 25 sample and 90-day window above are the floor it must be designed to hit.

## Scope

**In scope**
- A **curriculum schema** (JSON Schema) for a *lesson* (objectives, prose, runnable examples,
  prerequisites, language, concepts) and an *exercise* (prompt, starter code, reference solution,
  test suite, hints, rubric, expected difficulty, license).
- A **pedagogy + style guide** defining the beginner-appropriateness bar, the lesson template, the
  exercise template, the hint/feedback convention, and inclusive/plain-language requirements.
- An **autograder/runner contract**: a language-agnostic interface for "run code + tests, return
  pass/fail + diagnostics," with per-language adapters; **client-side (WASM) execution** for
  learners and a **sandboxed CI executor** for verifying reference solutions.
- **Seed content:** one complete Python beginner module (≈ 8–10 lessons), then a JavaScript module,
  then a third language, each lesson with ≥ 1 graded exercise.
- A **learner site** (static) that renders lessons, runs examples in-browser, accepts exercise
  attempts, and gives instant graded feedback — accessible (WCAG-AA aim), no account, no install.
- A **glossary** and a concept/prerequisite **graph** linking lessons.
- A downloadable, versioned **CC-BY corpus** (lessons + exercises + tests) plus per-asset
  provenance.
- **Editorial tooling** in CI: schema validation, the reference-solution correctness run, link/
  license-field checks, and accessibility linting.

**Out of scope (will NOT do)**
- Server-side execution of learner code; a hosted cloud IDE; any "submit your code to our backend."
- Accounts, logins, profiles, comments, forums, or user-generated content (deferred well beyond the
  planned phases).
- Certifications, credentials, exam-readiness claims, grades-of-record, or transcripts.
- Leaderboards, competitive judging, streaks, or engagement gamification.
- Advanced/specialist curricula (ML, distributed systems, cryptography implementation, offensive
  security).
- Lessons teaching malware, exploits, surveillance, scraping-for-abuse, or evasion of safety/abuse
  controls — explicitly refused.
- Copying or "paraphrase-laundering" exercises, prose, or solutions from copyrighted textbooks,
  paid courses, or proprietary problem banks (e.g. interview-prep sites).
- Localization in the first phases (the schema will *anticipate* i18n; translated content is a
  later phase).

## Solution approach & architecture

A **content-and-data project with a real but thin software layer.** The valuable artifact is the
structured, reviewed, correctness-verified corpus; the site and runner are renderers/executors
over it. The one piece of genuine engineering is the **autograder**, and the central design
decision is to make it *safe by construction*.

**Components**
1. **Schema package** — versioned JSON Schemas for `lesson` and `exercise`, validated in CI via
   AJV (reusing the project's TS/ESM conventions, mirroring `packages/schema`).
2. **Content corpus** — one directory per lesson: lesson Markdown + structured front-matter,
   plus one or more exercise files (prompt, starter, **reference solution**, **test suite**, hints,
   rubric). Authoring format (structured JSON vs Markdown-with-front-matter compiled to JSON) is
   **decided in M0** before any seed authoring, because schema shape, validation tooling, and the
   autograder all hang off it.
3. **Autograder / runner** — a language-agnostic **runner contract**
   (`run(code, tests, limits) → { passed, failures[], stdout, durationMs, resourceUse }`) with
   per-language adapters. Two execution backends behind the same contract:
   - **Learner backend (default): in-browser WASM** — Pyodide for Python, native JS engine for
     JavaScript, WASM-compiled toolchains for later languages. Learner code **never leaves the
     learner's device**; there is no server to attack and no learner code to store.
   - **CI backend: sandboxed executor** — verifies that every **reference solution** passes its
     tests, in a container with **no network, read-only FS, CPU/memory/time limits**, non-root.
4. **Learner site (SSG)** — renders validated lessons, embeds the in-browser runner, presents
   exercises, and shows graded feedback + hints. Static output; accessible; exports the corpus as a
   downloadable CC-BY dataset.
5. **Editorial/CI tooling** — schema validation, the **reference-solution correctness run** (the
   key gate), link checking, license-field presence/allow-list checks, and accessibility linting.

**Tech stack.** TypeScript, ESM, pnpm workspaces. AJV for schema validation. A lightweight,
well-supported SSG (Astro vs Eleventy — decided by an M0/M1 spike, before the M2 site build).
WASM runtimes: Pyodide (Python), the browser's own engine (JS); later languages via established
WASM toolchains. Code MIT-licensed; lesson prose CC-BY-4.0; **all learner-facing code (starter,
solutions, tests) MIT or CC0** so learners and downstream courses may copy it without attribution
friction.

**Data model (abridged).**
```
Lesson {
  id, slug, title, language, level: enum("intro"|"beginner"|"early-intermediate"),
  objectives[], prerequisites[], concepts[],
  body (Markdown), runnableExamples: [{ code, expectedStdout?, explanation }],
  exercises[]: <Exercise.id>,
  license, version, lastReviewed, authors[], reviewers[],
  sources: [{ title, url, accessed, note }]
}
Exercise {
  id, lessonId, language, prompt (Markdown),
  starterCode, referenceSolution, tests: [{ name, input?, assertion }],
  hints[]: ordered, rubric, difficulty: enum("warmup"|"core"|"stretch"),
  expectedRuntimeMs, resourceLimits,
  solutionVerified: boolean,   // CI-set: did referenceSolution pass tests?
  isOriginal: boolean,         // problem authored for this project, not lifted
  license, version, lastReviewed, authors[], reviewers[]
}
```
`level`, `difficulty`, and `language` are **closed enums**. `solutionVerified` is **set by CI,
never by hand**, and an exercise with `solutionVerified:false` (or missing tests, or a solution
that does not pass) **fails validation and cannot publish**. `license` is constrained to a closed
allow-list (see Licensing); any non-original asset additionally requires a verified source URL.

**Key decisions**
- **Correctness is data, not vibes.** Every exercise carries a reference solution + tests; CI runs
  them and sets `solutionVerified`. The dominant failure mode (broken/unsolvable exercise) is
  caught by a machine before pedagogy review.
- **Execution is safe by construction.** Learner code runs **client-side in WASM**; we never
  execute untrusted code server-side, so the worst-case "RCE on our infra" simply does not exist.
- **Language-agnostic core, per-language adapters.** Adding a language is an adapter + content, not
  a redesign — this is what makes "across languages" affordable.
- **Authoring format decided in M0**, before seed authoring, because schema/tooling/runner depend
  on it.
- **License is mandatory and allow-listed**; code is MIT/CC0 (freely copyable), prose is CC-BY-4.0.
- **Bounded tasks.** Each lesson+exercise is one task with a fixed template, enabling consistent
  fan-out.

## Data, licensing & compliance

**This section is a hard gate. Be conservative.**

- **Lesson prose:** 100% **original** writing authored for this project. Technical facts are
  verified against authoritative references — official language documentation (e.g. the Python
  docs under the PSF license; MDN under CC-BY-SA), language specs, and standard library docs —
  which are **cited, not copied**. **No paraphrase-laundering** of copyrighted books, paid courses,
  blog tutorials, or Stack Overflow answers.
- **Exercises:** **original problems** (`isOriginal:true`). **No lifting** of problems, prompts, or
  test cases from copyrighted textbooks, paid platforms, or proprietary interview/problem banks.
  Where a *classic* exercise is unavoidable (FizzBuzz, Fibonacci, temperature conversion — facts/
  ideas, not expression), the prompt and tests are written fresh and the lineage noted in `sources`.
- **Code (examples, starter, reference solutions, tests):** original, released **MIT or CC0** so it
  is freely copyable by learners and downstream courses without attribution friction.
- **Media (diagrams):** **original artwork is the default** (`isOriginal:true`). Any non-original
  asset must carry a `license` from a **closed allow-list enforced in the schema** — only
  `original`, `CC0`, `CC-BY-4.0`, `CC-BY-SA-4.0` (isolated + clearly attributed), `public-domain`;
  anything off-list or blank fails validation. Every non-original asset additionally requires
  **human verification of its source URL** (`sourceVerified:true`) — a CI presence check is not
  sufficient. **No "found on the web" images.**
- **Attention to license incompatibility:** MDN content is **CC-BY-SA** (share-alike); we therefore
  **do not copy MDN prose** into our CC-BY lessons (incompatible mixing) — we **cite** it and write
  our own. This is called out because it is a common, easy-to-miss provenance trap.
- **Output license:** prose **CC-BY-4.0**; code **MIT** (or CC0 for trivial snippets). Each lesson,
  exercise, and the dataset release states its license explicitly with attribution requirements.
- **Privacy / PII:** the project handles **no personal data** in the planned phases. No accounts,
  no learner-code upload (execution is client-side), no PII-collecting analytics. Contributors are
  credited by chosen handle only.
- **Provenance model:** a machine-readable `sources[]` per lesson/exercise and a provenance block
  per media asset, both validated and published with the dataset so reuse is auditable.
- **Refusal compliance:** any request to author malware/exploit/surveillance/abuse content, or
  offensive-security "how to attack" lessons, is refused and flagged per Hee-Lee Oss guardrails —
  regardless of framing.

## Quality, review & risk gates

**Risk tier.** Project baseline **low**. Although baseline is low, **exercise/lesson authoring
carries a domain-accuracy obligation** (a wrong exercise mis-teaches), so it is treated with a
medium-grade review discipline: a mandatory automated correctness gate **plus** human technical +
pedagogy review. Content that strays toward security/abuse topics escalates to a guardrail check
and is refused if offensive. No content in this project is **high** risk (no health/legal/safety
advice); if a proposed lesson ever implied high-stakes advice it would be out of scope.

**The correctness gate (automated, hard, non-negotiable).** For every exercise:
- A **reference solution** and a **test suite** exist; CI executes the solution against the tests
  in the sandboxed CI backend and sets `solutionVerified`. **`solutionVerified` must be `true` to
  publish.**
- The **starter code must *not* already pass** the tests (otherwise the exercise teaches nothing) —
  CI asserts starter ≠ passing.
- Tests run **with no network, under CPU/memory/time limits**; a solution that hangs or exceeds
  limits fails.

**Per-lesson quality floor (beyond schema-validity + correctness gate).**
- **Minimum-citation rule:** ≥ 2 authoritative, independent cited sources for any non-obvious
  technical claim (official docs/spec preferred).
- **Pedagogy checklist:** a written checklist (clear single objective, prerequisite honesty, plain
  language, worked example before exercise, hints scaffold rather than spoil, inclusive/neutral
  examples) that the reviewer signs item-by-item — not a free-form "looks good."
- **Audit/sampling rate:** the maintainer re-audits a **rolling ≥ 10% sample** of published
  lessons each cycle to catch drift across fan-out authors.

**Required review before "done":**
- **Every lesson + exercise:** (1) the **automated correctness gate** passes; (2) **technical
  review** for accuracy (idiomatic, correct, no bad habits) by someone other than the author; (3)
  **pedagogy review** against the checklist (may be the same reviewer if competent in both, but
  **never the author**).
- **License/provenance gate:** automated presence + allow-list checks + **mandatory** human
  source-URL verification of every non-original asset.
- **Accessibility gate (site):** WCAG-AA-aimed checks on the learner site (keyboard nav, contrast,
  alt text, the in-browser runner usable with assistive tech).

**Definition of Shipped (project).** A published, browsable curriculum with a growing,
peer-reviewed, correctness-verified set of lessons+exercises that a learner can run **with zero
install and zero account**, **adopted by at least one education partner**, with a downloadable
CC-BY corpus. Per Hee-Lee Oss' "delivered, not merged" bar, a lesson is *delivered* only when:
acceptance criteria met + schema-valid + **`solutionVerified:true`** + CI green + technical &
pedagogy review passed + published on the live site + reachable by the intended beneficiaries.

## Roadmap & milestones

**M0 — Foundation & cold-start (thin).**
Goal: make it possible to author **one** correct, runnable, auto-graded lesson+exercise
end-to-end, and prove the correctness gate works.
Exit criteria: a named **Maintainer/Owner** in place (hard gate, target 2026-07-12); authoring
format decided; lesson + exercise JSON Schemas published and CI-validated; pedagogy/style guide
merged; **autograder runner contract** defined and the **CI correctness backend** running (sandbox,
no network, limits); **one** complete Python lesson (e.g. "variables & printing") with ≥ 1 graded
exercise — reference solution `solutionVerified:true`, starter fails, technical + pedagogy reviewed;
repo scaffolding green (`pnpm build && pnpm test && pnpm lint`); SSG-selection spike scheduled to
conclude before M2.

**M1 — Seed Python module & partner outreach.**
Goal: a credible first module + a working learner runner + a real beneficiary conversation.
Exit criteria: one complete **Python beginner module** (≈ 8–10 lessons, each with ≥ 1 graded,
CI-verified exercise) published and schema-valid; the **in-browser (Pyodide) runner** executes
examples and grades exercises locally; glossary + initial concept/prerequisite graph in place; ≥ 1
partner outreach packet sent and ≥ 1 partner conversation logged.

**Gate G1 — Post-M1 go/no-go (partner check, blocks M2 site build).**
Before any M2 learner-site task starts: **≥ 1 partner must be at least verbally committed** to
adopting the curriculum, *and* the SSG choice must be decided. If met → proceed to M2. If **no
partner** is committed, do **not** halt: pivot to a **fallback "delivered" definition** — ship the
schema-valid corpus + the **downloadable CC-BY dataset** + a **self-contained runnable bundle**
(lessons + in-browser runner, openable offline) as the delivered outcome, defer the full hosted
learner site, and keep outreach as the active critical-path task. This keeps "delivered, not
merged" honest when partner adoption — the true outcome — has not yet landed.

**M2 — Learner site & dataset release.**
Goal: beneficiaries can actually learn from it. **Gated on G1** and on the SSG choice being decided.
Exit criteria: SSG renders all lessons into an accessible (WCAG-AA aim), searchable site with the
in-browser runner and graded feedback; concept/prerequisite navigation works; first versioned
**CC-BY corpus** published with provenance; live URL; learner can complete an exercise with zero
install and zero account.

**M3 — Second language & autograder hardening.**
Goal: prove the language-agnostic model and harden execution.
Exit criteria: a complete **JavaScript beginner module** through the same schema/runner; the runner
contract demonstrably reused (adapter, not redesign); **execution sandbox hardened and documented**
(CI: no network, FS read-only, CPU/mem/time caps, non-root; learner: WASM isolation, resource
caps, graceful timeout); hint/feedback system shipped; CI enforces "every exercise has tests +
`solutionVerified:true` + starter-fails."

**M4 — Breadth & adoption.**
Goal: breadth + confirmed real-world use.
Exit criteria: a **third language** beginner module started (≥ 5 lessons); ≥ 1 **partner adoption
confirmed in writing** with a use report; ≥ 1 documented **external reuse** of the CC-BY corpus;
re-review cadence running (`lastReviewed`-driven), including re-verification of `solutionVerified`
against current runtimes.

## Work breakdown

The itemized, schema-mapped backlog lives in **TASKS.md**, organized by the milestones above
(M0–M4), with a task table per milestone (ID, title, type, size, risk, deliverable, dependencies,
reviewer), acceptance criteria for the most important tasks, a per-milestone Definition of Done, a
backlog of sized-but-unscheduled work, and a complete, schema-valid example Task JSON for the first
M0 task.

## Governance, roles & stakeholders

- **Maintainer (Owner):** TBD — **TO BE SECURED**; owns the schemas, pedagogy/style guide,
  autograder contract, release cadence, and the Definition of Shipped. **Hard gate: filled before
  M0 exit; target 2026-07-12.**
- **Technical reviewers (rotation):** verify each exercise/lesson is correct and idiomatic for the
  language; confirm the correctness gate genuinely passed. **Author ≠ reviewer.**
- **Pedagogy reviewers:** confirm beginner-appropriateness against the checklist. May overlap with
  technical reviewers if competent in both, but **never the author**. Sourcing **TO BE SECURED**
  (ideally via the education partner — e.g. an instructor).
- **Steward (last-mile owner):** ensures published lessons actually reach learners and that partner
  adoption/feedback is collected. TO BE SECURED.
- **Partner / requestor:** education partner (library/nonprofit/school/Carpentries-style). TO BE
  SECURED.
- **Conflict-of-interest rule:** the author of a lesson/exercise may never be its own technical or
  pedagogy reviewer, even where the reviewer overlaps with partner staff.
- **Expert reviewers (risk tiers):** none expected in normal flow (no high-stakes content); the
  escalation path exists if a proposed lesson ever implied high-stakes advice (it would instead be
  ruled out of scope).

## Dependencies & integrations

- **Hee-Lee Oss pieces:** Task JSON schema (`packages/schema`), the CLI workspace-prep + PR flow (donated
  lane), the governance/review process, and the registry.
- **External tooling:** chosen SSG (Astro/Eleventy — TBD by spike); AJV for validation; **Pyodide**
  (Python WASM) and the browser JS engine for the runner; established WASM toolchains for later
  languages; a sandboxing/container mechanism for the CI correctness backend.
- **Authoritative sources (citation only, never copied):** official Python docs (PSF license),
  ECMAScript spec + MDN (cited, **not** copied — CC-BY-SA incompatibility), and language/stdlib
  reference docs.
- **Partner dependency:** the Definition of Shipped's adoption clause depends on securing a partner
  — a true external dependency, tracked as such.
- **Runner dependency:** the in-browser WASM runner (esp. Pyodide load size/performance) is on the
  critical path for the zero-install learner experience; a spike de-risks it in M0/M1.

## Risks & mitigations

| Risk | Likelihood | Impact | Mitigation | Owner |
| --- | --- | --- | --- | --- |
| A published exercise is unsolvable or has a wrong "answer" (mis-teaches) | Medium | High | **Automated correctness gate**: reference solution must pass tests in CI (`solutionVerified:true`) and starter must fail; no manual override | Maintainer |
| Untrusted learner code as an execution/RCE threat | Low | High | **Client-side WASM execution only** — we never run learner code server-side; no learner code stored; CI runs only trusted authored solutions in a no-network sandbox | Maintainer |
| Content/exercise lifted from copyrighted books/courses/problem banks | Medium | High | Original-only rule; `isOriginal` flag; reviewer checks lineage; classic problems re-authored fresh with `sources` lineage note | Tech reviewer |
| License-incompatible reuse (e.g. copying CC-BY-SA MDN into CC-BY) | Medium | High | Closed license allow-list in schema; explicit "cite, don't copy MDN" rule; provenance verification | Maintainer |
| No partner secured → Definition of Shipped not met | Medium | High | Start outreach in M1; **Gate G1** before M2; **fallback "delivered" = corpus + CC-BY dataset + offline runnable bundle**; `verifiedNeed=false` until confirmed | Steward |
| Pedagogy/technical review throughput bottleneck | Medium | Medium | Reviewer rotation; throttle authoring to reviewer capacity; the automated correctness gate removes the most time-consuming review burden | Maintainer |
| WASM runner too heavy/slow (Pyodide load) → poor beginner experience | Medium | Medium | M0/M1 runner spike; lazy-load runtime; cache; graceful fallback ("download to run locally") if WASM unavailable | Maintainer |
| Inconsistent quality across many fan-out authors | Medium | Medium | Strict schemas + pedagogy/style guide + templates; author≠reviewer; CI lint + correctness gate | Maintainer |
| Scope creep into bootcamp/certification/leaderboard | Medium | Medium | Explicit non-goals; review rejects out-of-scope work | Maintainer |
| Refusal-worthy content requested (malware/exploit/offensive-security lesson) | Low | High | Guardrail refusal + flag; documented in style guide; security topics defensive-only | All authors/reviewers |
| Runtime drift: a solution passes today but breaks on a future Python/JS version | Medium | Medium | Pin runtime versions; re-run `solutionVerified` on a cadence (M4 maintenance); record runtime version per exercise | Maintainer |
| Accessibility shortfall in site or in-browser runner | Medium | Medium | WCAG-AA target; runner keyboard/AT usability in site task acceptance | Maintainer |
| Maintainer bandwidth / bus factor | Medium | Medium | Document everything; reviewer rotation; small forkable tasks; CC-BY+MIT forkability | Maintainer |

## Security & privacy

- **Execution model is the headline security decision.** Learner code runs **only client-side in a
  WASM sandbox** (Pyodide / browser engine). There is **no server endpoint that executes untrusted
  code**, so the highest-severity threat (remote code execution on project infrastructure) is
  designed out, not merely mitigated.
- **CI correctness backend** runs only **trusted, authored** reference solutions, and still does so
  defensively: containerized, **no network**, **read-only filesystem**, **CPU/memory/wall-clock
  limits**, **non-root**, no secrets mounted. A solution that tries to reach the network or exceed
  limits fails the gate.
- **Threat surface otherwise small:** a static site + structured data; no accounts, no server-side
  learner state, no PII collection.
- **Secrets:** none required for content work; any CI/deploy tokens live in CI secrets only and are
  never written into lessons, logs, receipts, or commits (per Hee-Lee Oss rules).
- **PII:** none collected or stored. No learner-code upload (client-side execution). If
  progress-tracking is ever added it must be **local-only** (browser storage), opt-in, with no
  server collection.
- **Abuse/misuse prevention:** the primary misuse vector is the *content* being steered toward harm
  (malware/exploit/offensive-security lessons). Mitigated by refusal guardrails, the review gate,
  and explicit non-goals — enforced at authoring/review time, not just policy text. A secondary
  vector — the in-browser runner being abused to mine/attack from a learner's own browser — is
  bounded by WASM isolation and resource caps and the absence of network access in the exercise
  runtime.
- **Supply chain:** keep the SSG/runner dependency set minimal and pinned; pin the WASM runtime
  versions; static output reduces runtime attack surface.

## Sustainability & maintenance

- **After delivery:** the maintainer + reviewer rotation keep lessons current; `lastReviewed`
  drives a re-review cadence, and a **runtime-drift re-check** periodically re-runs every
  `solutionVerified` against pinned-but-updated runtimes so a language upgrade can't silently break
  exercises.
- **Outcome tracking:** the steward collects partner adoption and learning-gain feedback (the real
  success metrics) and feeds it into the backlog.
- **Low operating cost:** static hosting + client-side execution means **near-zero server cost** and
  no execution-infrastructure bill — a deliberate sustainability choice that lets the project
  outlive any single funder.
- **Forkability:** CC-BY prose + MIT/CC0 code + open schema mean the curriculum survives even if the
  original maintainers step away.

## Open questions

1. **Partner:** which education org (library/nonprofit/school/Carpentries-style) adopts first?
   (Blocks the Definition of Shipped; Gate G1.)
2. **SSG choice:** Astro vs Eleventy vs other — resolved by an M0/M1 spike (criteria, ranked: WCAG
   support, ease of embedding the WASM runner, build determinism/supply-chain footprint, maintenance
   burden). Decision deadline: before M2 site build.
3. **Authoring format:** structured JSON directly, or Markdown-with-front-matter compiled to JSON?
   **Decided in M0**, before seed authoring.
4. **First-language ordering:** Python first is assumed (most common beginner language, best WASM
   story via Pyodide) — confirm with partner needs; JS second; **third language TBD** (candidates:
   a typed teaching language, SQL for data beginners, or C basics) and partner-driven.
5. **In-browser runner viability** for the third language (does a usable WASM toolchain exist, and
   at what load cost?) — spike before committing in M4.
6. **Localization:** is multi-language UI/content a future phase, and does the schema need to
   anticipate it now? (Schema will leave room; content i18n deferred.)
7. **Pedagogy reviewer sourcing:** recruit independently or via the partner's instructors?
8. **Contribution model:** if/when external contributions open, what review scaling (esp. for the
   correctness gate) is needed?

## References

- `C:\code\hee-lee-oss\CLAUDE.md` — Hee-Lee Oss work rules, lanes, quality bar, refusal guardrails.
- `C:\code\hee-lee-oss\docs\good-deed-definition.md` — the 5 good-deed criteria + risk tiers.
- `C:\code\hee-lee-oss\packages\schema\src\schemas.ts` — Task JSON schema (TASKS.md maps to it).
- `C:\code\hee-lee-oss\planning\ROADMAP.md` — portfolio roadmap (this project: Track 3, low risk).
- `governance/proposals/open-coding-curriculum.md` — project proposal (**TO BE WRITTEN**).
- Pyodide — Python scientific stack compiled to WebAssembly (in-browser runner).
- Creative Commons Attribution 4.0 (CC-BY-4.0) — prose license. MIT / CC0 — code license.

---

## Appendix A — Improvements applied

The following 25 improvements were identified against the first draft and have been **applied**
to the PLAN above (and mirrored into TASKS.md where relevant). Each is concrete and incorporated,
not aspirational.

1. **Machine-set `solutionVerified` field.** Made the correctness result a schema field that **CI
   sets** (never hand-edited) and that **gates publication**, so the dominant failure mode (broken
   exercise) cannot ship. *(Architecture / Quality gates.)*
2. **"Starter must fail" assertion.** Added the rule that starter code must *not* already pass the
   tests, preventing empty exercises that teach nothing. *(Quality gates / M3 CI rule.)*
3. **Client-side WASM execution as the security thesis.** Elevated "learner code runs in the
   learner's browser" from an implementation note to the headline security decision that designs
   out server-side RCE. *(Security; Architecture.)*
4. **Hardened CI correctness backend.** Specified no-network, read-only FS, CPU/mem/time limits,
   non-root for the trusted-solution executor. *(Security; M3.)*
5. **MDN CC-BY-SA incompatibility called out.** Added an explicit "cite, don't copy MDN" rule
   because mixing CC-BY-SA into CC-BY is a common, easy-to-miss license trap. *(Licensing; Risks.)*
6. **Code licensed MIT/CC0, prose CC-BY.** Split the output license so learners and downstream
   courses can copy code without attribution friction while prose keeps attribution. *(Licensing.)*
7. **Runtime-drift re-verification.** Added a maintenance cadence that re-runs every
   `solutionVerified` against updated runtimes, so a Python/JS upgrade can't silently break
   exercises. *(Sustainability; Risks; M4.)*
8. **Pin runtime versions per exercise.** Record the runtime version so correctness is
   reproducible and drift is detectable. *(Architecture; Risks.)*
9. **Pedagogy review distinct from technical review.** Separated "is it correct" from "is it
   teachable," with author≠reviewer enforced for both. *(Quality gates; Governance.)*
10. **Beneficiary learning-gain metric with sample floor.** Replaced vanity metrics with a
    survey-based learning-gain target (≥ 75% agree, ≥ 25 respondents, rolling 90-day window) and
    backlogged the survey instrument as its prerequisite. *(Success metrics; Backlog.)*
11. **Gate G1 with a fallback "delivered" definition.** Added an explicit post-M1 partner go/no-go
    that, on no-partner, pivots to corpus + CC-BY dataset + **offline runnable bundle** rather than
    halting — keeping "delivered, not merged" honest. *(Roadmap; Risks; TASKS gate.)*
12. **Ownership gate before M0 exit.** Made a named Maintainer a hard M0 exit gate with a target
    date (2026-07-12), mirroring Hee-Lee Oss accountability practice. *(Header; Governance; M0 DoD.)*
13. **Refusal guardrail specialized to coding.** Spelled out that malware/exploit/offensive-
    security/abuse-scraping lessons are refused and security topics are defensive-only. *(Non-goals;
    Licensing; Risks.)*
14. **No-certification / no-exam-cram non-goal.** Explicitly disclaimed exam-readiness and
    credentials to avoid an unverified high-stakes accuracy claim. *(Non-goals.)*
15. **No-leaderboard / no-gamification non-goal.** Ruled out engagement mechanics that would pull
    the project toward vanity metrics. *(Non-goals; Success metrics framing.)*
16. **Language-agnostic runner contract.** Defined a single `run(code, tests, limits)` contract
    with per-language adapters so adding a language is an adapter + content, not a redesign.
    *(Architecture; M3 reuse proof.)*
17. **WASM runner spike to de-risk Pyodide weight.** Added an explicit spike + lazy-load/cache/
    fallback plan because Pyodide load size can wreck the beginner experience. *(Risks; Open Qs; M0/M1.)*
18. **Accessibility gate for the *runner*, not just the page.** Required the in-browser code editor/
    runner to be keyboard- and AT-usable, not just the static prose. *(Quality gates; Risks; M2.)*
19. **Authoring-format decision moved to M0.** Made JSON-vs-Markdown a blocking M0 decision because
    schema, tooling, and the autograder all depend on it. *(Architecture; Roadmap; Open Qs.)*
20. **Minimum-citation + ≥10% audit-sample rules.** Imported the per-lesson quality floor and
    rolling re-audit to control fan-out drift. *(Quality gates; TASKS quality-floor note.)*
21. **Concept/prerequisite graph, not just a flat list.** Modeled prerequisites/concepts so the
    site can sequence learning and flag missing prerequisites. *(Architecture; M1/M2.)*
22. **Local-only progress if ever added.** Pre-committed that any future progress-tracking is
    browser-local, opt-in, no server collection — protecting the no-PII stance. *(Security/Privacy.)*
23. **Honest `verifiedNeed=false` until a partner signs.** Stated plainly that need is reasonable in
    the abstract but unverified for a specific partner, and set the flag accordingly. *(Problem;
    TASKS mapping.)*
24. **Near-zero operating cost as a sustainability argument.** Tied the client-side-execution choice
    to no execution-infra bill, supporting longevity past any single funder. *(Sustainability.)*
25. **Classic-problem lineage handling.** Added the rule that unavoidable classic exercises
    (FizzBuzz etc.) are re-authored fresh with a `sources` lineage note, threading the
    facts-vs-expression line of copyright. *(Licensing; Risks.)*

## Review sign-off

**Reviewer pass (self-review against PLAN_SPEC, CLAUDE.md, good-deed-definition, and the Task
schema). Findings and fixes:**

- **Spec section completeness — PASS.** All 17 required H2 sections are present and in the
  prescribed order (Executive summary → References). Metadata header matches the global convention
  (Status/Version/Last updated/Owner/Lane).
- **Good-deed criteria — PASS.** (1) Tangible public benefit: free runnable beginner CS education.
  (2) Freely available: CC-BY prose + MIT/CC0 code, downloadable corpus. (3) Not primarily for a
  for-profit: no accounts, no ads, no upsell, near-zero-cost static delivery. (4) No harm/
  discrimination/misinformation: correctness gate + refusal guardrails + neutral examples. (5)
  Executable by AI sessions with review: bounded lesson+exercise tasks with automated + human gates.
- **Risk-tier handling — corrected & consistent.** Baseline `low`; authoring carries a
  medium-grade *review discipline* (automated correctness + technical + pedagogy) without
  overclaiming a `high` tier the content does not warrant. No high-stakes-advice content is in
  scope; the escalation path is noted but not expected.
- **License/provenance gate — strengthened.** Closed allow-list, MDN CC-BY-SA trap called out,
  code MIT/CC0 vs prose CC-BY split, classic-problem lineage rule, `isOriginal` + verified source
  URLs. This is the conservative posture the spec demands.
- **Security correctness — verified.** The claim "no server-side execution of untrusted code" is
  consistent throughout (client-side WASM for learners; trusted-only, sandboxed CI for reference
  solutions). No contradiction between the runner architecture and the privacy/PII stance.
- **Honesty of need — verified.** Partner is marked **TO BE SECURED** in every place it appears
  (header-adjacent, Problem, Governance, Roadmap Gate G1, Risks), and `verifiedNeed=false` is the
  honest default carried into TASKS.md.
- **Internal consistency — fixed.** Cross-references resolve: Gate G1 ↔ M2 dependency; the survey
  instrument (`occ-research-505`) is named as the prerequisite for the learning-gain metric; the
  SSG spike concludes before M2; authoring-format decision precedes the schema. Milestone exit
  criteria in PLAN match the Definition-of-Done statements in TASKS.
- **Schema mapping — verified against `schemas.ts`.** Every Task field used in TASKS.md is a real
  schema property with a valid enum value; the example Task JSON validates against the required
  fields and `additionalProperties:false` (no stray keys); funded-lane budget rule is noted as
  N/A (all tasks donated).
- **Residual risk acknowledged.** The two items most likely to bite are (a) no partner →
  mitigated by Gate G1 fallback, and (b) runtime drift breaking verified solutions → mitigated by
  pinned runtimes + the M4 re-verification cadence. Both are owned and tracked.

**Sign-off:** Plan is internally consistent, schema-aligned, and faithful to Hee-Lee Oss guardrails.
**Outstanding human decisions:** name the Maintainer (by 2026-07-12), secure an education partner,
confirm first/third language ordering, and write the formal `governance/proposals/` proposal.
