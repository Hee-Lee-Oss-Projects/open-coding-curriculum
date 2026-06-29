# Competitive & Improvement Analysis — open-coding-curriculum

> Analyst review of `PLAN.md` (v0.1.0, 2026-06-28) and `TASKS.md`. Scope: an open,
> offline-capable, low-resource beginner programming/CS curriculum with executable, CI-verified
> exercises. Web-researched competitor claims are cited inline.

---

## 1. Correctness & completeness review of PLAN.md

**Overall.** The plan is unusually rigorous and internally consistent. Its central thesis —
*correctness is data, not vibes*: every exercise ships a reference solution + test suite, CI
executes the solution and sets `solutionVerified`, and `false` blocks publication — is the
strongest idea in the document and the right forcing function for accuracy. The "starter must
fail" assertion, the closed license allow-list, the MDN CC-BY-SA trap call-out, and the
client-side-WASM security thesis are all genuinely well-judged. The following are real gaps or
risks, ordered by importance.

**A. The offline/low-resource claim is in tension with the chosen runner (most important
finding).** The plan repeatedly promises "a self-learner with only a browser (no install)" and
markets to "students in under-resourced schools" and "learners without reliable internet," yet the
default Python runner is **Pyodide, whose first load is ~6.4 MB (core) and effectively 10–15 MB+
with packages, taking 4–5 s to initialize and running 3–5× slower than native Python**
([Pyodide first-load/perf](https://glinteco.com/en/post/beyond-the-server-running-high-performance-python-in-the-browser-with-pyodide-and-webassembly-2026-guide/),
[wasm constraints](https://pyodide.org/en/stable/usage/wasm-constraints.html)). On the
low-end Android phones and shared library PCs that the stated beneficiaries actually use, a
multi-megabyte WASM download over intermittent connectivity, plus the RAM cost of a browser-based
Python VM, directly undercuts the "low-resource, no reliable internet" promise. The plan
acknowledges a Pyodide spike (occ-spike-006) and an offline bundle fallback (G1), but treats
"browser = zero friction" as established. **Recommendation:** make the *downloadable offline
bundle* (lessons + cached runtime + runner, openable from local disk with no network) a
first-class M1/M2 deliverable, not just a no-partner fallback, and set explicit budgets (total
bundle MB, cold-load seconds on a low-end device, minimum RAM). This is the single biggest
correctness gap between the marketing and the architecture.

**B. "Zero-install in-browser grading" works cleanly for Python/JS but the language-agnostic claim
is over-promised for language #3.** The runner contract is elegant, but C, SQL, or a typed
teaching language each need a *usable* WASM toolchain that loads acceptably; the plan flags this as
an open question (Q5) but the M4 exit criterion still commits to "a third language module started."
Until the spike for #3 lands, the breadth promise is contingent. This is honestly scoped but worth
elevating from open-question to explicit gate.

**C. Pedagogy/assessment depth is thinner than correctness depth.** The plan's quality is heavily
weighted toward the *machine-checkable* gate. The pedagogy side is a checklist + author≠reviewer,
which is good, but: (i) there is **no learning-design spine** — no stated concept progression model
(e.g. spiral, worked-example-fading, misconception inventory) beyond a prerequisite graph; (ii)
assessment is binary pass/fail against hidden tests, which is the dominant failure mode of
auto-graders — it tells a beginner *that* they failed, not *why*, and can reward
test-gaming/overfitting. The hint system (M3) partly addresses this but is late. (iii) There is no
**diagnostic/formative layer** (predict-the-output, Parsons problems, fix-the-bug) — all proven
high-value for absolute beginners and cheaper to author than full free-coding exercises.
**Recommendation:** add lightweight non-coding item types and require each failing test to carry a
human-written, beginner-readable diagnostic message in the schema.

**D. Accessibility for a *code editor* is the hardest WCAG problem and is under-specified.** The
plan correctly requires the in-browser runner (not just prose) to be keyboard/AT-usable — better
than most competitors — but an accessible code editor + live grading output for screen-reader users
is a genuinely hard, specialized build (focus management, ARIA live regions for test results, no
reliance on color for pass/fail). It is named but not budgeted or de-risked with its own spike.

**E. Language/tool currency is sound but the "current but not chasing fads" line needs a written
policy.** Python-first and JS-second are defensible, mainstream beginner choices. The runtime-drift
re-verification cadence (re-running `solutionVerified` on pinned-but-updated runtimes) is excellent
and rare among competitors. Missing: an explicit, written rule for *what counts as a fad* vs. a
durable concept (e.g. "we teach language fundamentals and stdlib, not the framework of the month;
no AI-assistant-dependent workflows in beginner lessons"), so fan-out authors apply it consistently.

**F. Completeness/scope is appropriately bounded** (no accounts, no certs, no leaderboards, no
server-side execution, defensive-only security, beginner-only). The non-goals are crisp and the
refusal posture matches CLAUDE.md. One scope ambiguity: "≥ 30 lessons across Python + start of a
second language" as a 3-phase target is modest relative to the engineering investment (schema,
runner, sandbox, SSG, accessibility); the moat is explicitly the *structure + reviewed corpus*, so
30 lessons must be framed as a seed, not a product — which the plan mostly does.

**G. Not-duplicating-CS50/freeCodeCamp: defensible but the differentiation must be stated more
sharply** (see §3–4). The plan asserts "open, runnable, auto-graded, zero-install" as the wedge,
which is real, but several incumbents already do parts of this; the unique combination needs to be
foregrounded.

**Minor:** Owner/maintainer is TBD (hard M0 gate, honestly flagged); `verifiedNeed=false` until a
partner signs (honest); survey instrument (occ-research-505) correctly gated as prerequisite for
the learning-gain metric. No fabricated claims found. Internal cross-references resolve.

---

## 2. Competitive landscape

| Project | What it is | Strengths | Weaknesses (vs. this project's niche) |
| --- | --- | --- | --- |
| **Harvard CS50x** | The flagship intro-CS course (Malan), video + problem sets, 2026 edition adds AI | World-class production, huge brand, autograder (`check50`), teachers may adapt; 2026 refresh ([CS50x](https://cs50.harvard.edu/x/)) | **CC BY-NC-SA** — *non-commercial + share-alike*, so it **cannot be freely remixed into commercial courses or relicensed**, and is video-heavy (bad for low-bandwidth) ([CS50 license](https://cs50.harvard.edu/x/2023/license/)) |
| **freeCodeCamp** | Free self-paced full-stack + Python + math/CS, interactive in-browser challenges, free certs | 100% free, in-browser editor, massive scale, open codebase **BSD-3** so material is reusable ([fCC about](https://www.freecodecamp.org/news/about), [Python certs](https://www.freecodecamp.org/news/python-curriculum-is-live/)) | Web-dev/JS-centric; requires an account + reliable internet; not designed for offline/low-resource; content is tutorial-step-gated, not a remixable structured corpus |
| **The Odin Project** | Open-source full-stack web curriculum, project-based | Free, no account to read, strong community, project-driven ([TOP about](https://www.theodinproject.com/about)) | **Curriculum is CC non-commercial** (commercial reuse needs permission); web-dev focus, not absolute-beginner CS; relies on linking out to third-party resources; online-only ([TOP curriculum repo](https://github.com/TheOdinProject/curriculum)) |
| **Exercism** | 70+ language tracks, exercises + free human mentorship | Excellent practice + feedback loop, in-browser editor or CLI, open ([Exercism](https://exercism.org/)) | Codebase is **AGPL-3.0** (copyleft, network clause) — friction for remixing into other apps; practice-oriented, **not a teaching curriculum** (assumes you can already learn the language); account required ([Exercism wiki](https://en.wikipedia.org/wiki/Exercism)) |
| **Khan Academy CS** | Intro programming (ProcessingJS/JS), in-browser | Free, beginner-friendly, in-browser runner | Content **CC BY-NC-SA** (non-commercial); aging ProcessingJS stack; account-oriented; not a downloadable corpus |
| **Code.org** | K-12 CS, "free curriculum commitment," includes **CS Unplugged** activities | Genuinely free to teach, strong K-12 pedagogy, **offline "unplugged" pencil-and-paper lessons** ([free-curriculum](https://code.org/en-US/about/code-org-free-curriculum-commitment), [unplugged](https://code.org/curriculum/unplugged)) | Aimed at school classrooms/teachers, block-based (Blockly), not self-taught text-programming for older beginners; platform-coupled |
| **CS Unplugged** | Free games/puzzles teaching CS concepts **with no computer** | Pure offline, low-resource, CC-licensed, beloved ([csunplugged.org](https://www.csunplugged.org/en/)) | Concepts only — **does not teach you to write running code**; complementary, not competitive |
| **Scratch / MIT** | Block-based creative coding from the MIT Media Lab | Free, **offline desktop install available**, huge reach, great for kids ([RPi Scratch](https://projects.raspberrypi.org/en/projects/getting-started-scratch/0)) | Block-based, young-learner focus; not text programming / Python-JS path |
| **Raspberry Pi Foundation** | 250+ free coding projects + learning paths + Code Club | Free, project-based, some offline (downloadable Scratch), global clubs ([RPi resources](https://www.raspberrypi.com/resources/learn/)) | Project-recipe style, not a graded, correctness-gated, structured corpus; uneven assessment |
| **Open Source Society University (OSSU)** | A GitHub *meta-curriculum* linking free MOOCs to a CS degree path | Free, comprehensive, community-curated ([ossu/computer-science](https://github.com/ossu/computer-science)) | A **reading list of links**, not authored content; depends on third-party MOOCs that paywall grading; not beginner-zero, not offline, not self-graded |
| **MOOCs (edX/Coursera intro CS)** | University intro courses | High quality, structured | Grading/certs usually **paywalled**; video-heavy; not openly licensed; online-only |

**Synthesis.** The incumbents cluster into: (a) *high-quality but non-commercially-licensed*
(CS50, Khan, Odin, Code.org content) — cannot be freely remixed/relicensed/sold-into offline packs;
(b) *practice-not-teaching* (Exercism) or *concepts-not-code* (CS Unplugged); (c)
*account-and-internet-required* (freeCodeCamp, Khan, Exercism); (d) *link aggregators* (OSSU). **No
incumbent simultaneously offers: original truly-open (CC-BY + MIT/CC0, commercially remixable)
authored beginner lessons, machine-verified-correct graded exercises, AND an offline/low-resource
delivery story.** That four-way intersection is the defensible gap.

---

## 3. Gaps we can fill

1. **Truly-open (remixable & commercial-OK) authored content.** The best free material (CS50,
   Khan, Odin curriculum) is **non-commercial / share-alike**, which blocks libraries, nonprofits,
   and other OER projects from packaging or building products on it. CC-BY prose + **MIT/CC0 code**
   is a materially freer license posture — that *is* the wedge and should be stated louder.
2. **Correctness as a published, machine-checkable invariant.** No competitor exposes a
   `solutionVerified`-style guarantee in the data itself, nor a runtime-drift re-verification
   cadence. "Every exercise's answer provably runs, and we re-prove it on runtime upgrades" is a
   trust claim none of them make.
3. **Genuine offline / low-resource delivery.** Only CS Unplugged (no-code), Scratch (desktop
   install), and partial Code.org/RPi serve offline. **An offline-openable bundle that still grades
   real Python/JS** is unoccupied territory — *if* the Pyodide-weight problem (Finding A) is solved.
4. **A structured, downloadable, schema-validated corpus.** Competitors ship websites/courses;
   none ship a *dataset* (validated JSON lessons+exercises+tests+provenance) that third parties can
   build courses, apps, flashcards, or translations on.
5. **Teaching + graded practice in one loop for absolute beginners.** Exercism grades but doesn't
   teach; CS50/Khan teach but gate grading (videos/account). The lesson→worked-example→graded-
   exercise→hint loop with no account is a cleaner beginner on-ramp.
6. **Provenance/licensing rigor as a product feature** — per-asset source + license, auditable —
   which institutions and OER reusers actually need and incumbents rarely expose.

---

## 4. Differentiators to win

1. **Provably-correct exercises (the headline).** `solutionVerified:true` in CI + starter-must-fail
   + runtime-drift re-checks. Market it as "the curriculum whose answers are guaranteed to run."
2. **Maximally-open license stack** (CC-BY prose, MIT/CC0 code) — *commercially remixable*, unlike
   CS50/Khan/Odin. This is the legal moat that lets every other OER and library project build on us.
3. **Offline-first, low-resource-first delivery** (a downloadable, network-free, still-graded
   bundle with a stated MB/RAM/cold-load budget). Promote from fallback to flagship.
4. **Content-as-data / curriculum engine** — a validated corpus + reusable auto-graded runner that
   other Elyos projects and outsiders can fork (see §7).
5. **Accessibility of the *runner*, not just prose** — an AT-usable code editor + screen-reader-
   friendly grading output, which essentially no competitor does well.
6. **No-account, no-PII, near-zero-server-cost** — sustainability and privacy as differentiators,
   letting the project outlive any funder.

---

## 5. Claude API leverage — and the hard limits

**High-value, in-bounds uses (Claude drafts, humans/CI verify):**

- **Draft lesson prose** from an outline against cited official docs, in the house style/template —
  then human pedagogy + technical review. Big fan-out multiplier.
- **Draft exercises end-to-end**: prompt, starter code, **reference solution, and a test suite**,
  plus ordered scaffolding hints and a rubric. The reference solution is then **executed against
  the tests in the sandbox** — Claude proposes, CI disposes.
- **Generate runnable code examples** with expected stdout, which the runner then *verifies*.
- **Author beginner-readable diagnostic messages** for each failing test (closing the "why did I
  fail" gap from Finding C).
- **Write Parsons/predict-output/fix-the-bug variants** from an existing exercise (cheap formative
  items).
- **Provenance & license drafting**: assemble `sources[]`, flag likely license incompatibilities
  (e.g. "this phrasing tracks MDN — rewrite, MDN is CC-BY-SA").
- **Offline-packaging chores**: generate the manifest, the corpus export, alt-text drafts for
  diagrams, glossary entries, and concept/prerequisite-graph edges.
- **Accessibility & plain-language passes**: suggest alt text, flag reading-level, suggest ARIA —
  as *drafts* for an auditor.

**Where Claude must NOT be the decider (enforce in process/CI):**

- **Code correctness is decided by execution, never by the model.** `solutionVerified` is *only*
  ever set by the sandboxed CI run; an LLM "looks correct" is inadmissible. (Plan already mandates
  this — keep it absolute.)
- **No fabricated/hallucinated APIs, stdlib functions, or version behavior.** Every API used must
  be confirmed against pinned-runtime execution + official docs; treat unverified API claims as
  defects, not stylistic notes.
- **Pedagogy/beginner-appropriateness is decided by a human educator reviewer** (author ≠
  reviewer). Claude may draft to the checklist; it cannot sign it off.
- **Accessibility conformance is verified by audit/AT testing**, not by the model asserting "WCAG
  AA." Claude's alt text/ARIA are drafts requiring human + tool verification.
- **License/provenance verification** of any non-original asset requires **human source-URL
  confirmation** (`sourceVerified:true`); a model's claim that "this is CC0" never publishes.
- **Refusal calls** (security/abuse-adjacent lesson requests) follow the human-owned guardrail, not
  model discretion alone.

---

## 6. Ten concrete optimizations

1. **Promote the offline-openable, still-grading bundle to a first-class M1/M2 deliverable** with
   explicit budgets (total MB, cold-load seconds on a low-end Android, min RAM) — closes Finding A,
   the biggest gap between the offline promise and the Pyodide reality.
2. **Add a "diagnostic message" field to each test** (and require it), so a failing learner gets a
   human-written, beginner-readable explanation, not a bare red X. Claude drafts; reviewer approves.
3. **Add lightweight non-coding item types to the schema** (predict-the-output, Parsons/reorder,
   fix-the-bug). Cheaper to author, higher formative value for absolute beginners.
4. **Write an explicit "durable vs. fad" content policy** (teach fundamentals/stdlib; no
   framework-of-the-month, no AI-assistant-dependent beginner workflows) so fan-out authors apply
   "current but not chasing fads" consistently.
5. **Add a dedicated accessibility spike for the code editor/runner** (keyboard, ARIA live grading
   output, no color-only pass/fail) in M0/M1 — don't discover this is hard at M2.
6. **State a concept-progression model** (spiral + worked-example-fading + a per-module
   misconception inventory) in the pedagogy guide, giving the prerequisite graph a learning-design
   spine.
7. **Add an anti-overfitting check to the correctness gate**: require ≥ N tests and (where feasible)
   a hidden/held-out test set so a reference solution can't pass by coincidence and learners can't
   game visible tests.
8. **Pin and record the runtime version per exercise in the published dataset** (the plan pins
   internally — also *expose* it) so downstream reusers can reproduce grading exactly.
9. **Sharpen the public positioning to the four-way wedge** ("open + remixable + provably-correct +
   offline") on the landing page and proposal; don't let it read as "another free course."
10. **Ship a tiny "fork-an-exercise" template + CONTRIBUTING with the correctness gate baked in**,
    so external contributors and other Elyos projects can add verified exercises without bespoke
    onboarding (scales the bus factor and seeds the engine in §7).

---

## 7. Parallel & perpendicular spin-offs

- **Reusable auto-graded curriculum engine (the big one).** The schema + runner-contract +
  sandboxed CI correctness backend + offline bundler is a *general* "verified, offline-capable,
  graded-exercise" platform. Extract it as a standalone package so any subject — not just coding —
  can ship CI-verified graded content. This is the highest-leverage perpendicular outcome.
- **`oer-everything` / open-flashcards.** The CC-BY corpus + glossary + concept graph feed directly
  into a general OER library and spaced-repetition flashcards (auto-generate cards from lesson
  objectives + glossary). Shared license/provenance model.
- **`reproducibility-curriculum`.** Reuse the *exact* runner + sandbox to teach and *grade*
  reproducible-computing practices (deterministic envs, pinned deps, tests) — same engine, adjacent
  audience; the runtime-drift re-verification machinery is itself a reproducibility lesson.
- **`bioinformatics-from-zero`.** A domain "language adapter + content" on the same engine
  (Python/Pyodide + small datasets), proving the language-agnostic claim extends to *domain* tracks,
  not just languages. Strong tie to the broader oncology/bio portfolio in the repo.
- **An MCP server.** Expose the corpus + autograder over MCP: tools like
  `list_lessons`, `get_exercise`, `run_and_grade(code, exerciseId)`, `next_concept(prereqGraph)`.
  This lets any agent (a tutor bot, a partner's app, Claude itself) *query lessons and grade
  learner code through the verified sandbox* without re-implementing it — turning the curriculum
  into callable infrastructure and a clean integration point for the Elyos donated lane.
- **`decodable-readers` / `literacy-from-zero` synergy.** Shares the offline-bundle + accessibility
  + plain-language tooling; cross-pollinate the accessibility and offline-packaging work.

---

## 8. Open questions

1. **Offline runtime weight:** can a Pyodide-based bundle hit an acceptable MB/RAM/cold-load budget
   on a low-end shared device, or is a lighter interpreter (e.g. a smaller Python/MicroPython-style
   or a transpile-to-JS path) needed for the true low-resource tier? (Decides whether the headline
   promise holds.)
2. **Assessment philosophy:** how far beyond binary hidden-test grading do we go (diagnostics,
   formative item types, partial credit) before authoring cost outweighs learning gain?
3. **Partner:** which org adopts first, and does their device/connectivity reality match the
   offline budget we set? (Blocks Definition of Shipped; the partner should *validate*, not just
   bless.)
4. **Language #3:** does a usable WASM toolchain exist at acceptable load cost, and is the choice
   (typed teaching language vs. SQL vs. C) partner-driven?
5. **Engine extraction timing:** do we build the curriculum first and extract the reusable engine
   later, or design for extraction from M0 (cheaper long-term, slower to first lesson)?
6. **MCP/agent surface:** is the verified autograder exposed as infrastructure (MCP) in-scope for
   early phases or a later spin-off?
7. **Anti-gaming:** held-out tests improve trust but complicate the "see your tests" beginner
   experience — what's the right balance?
8. **Localization:** schema anticipates i18n, but which beneficiary languages, and who reviews
   translated *and still correct* content?

---

### Sources
- CS50x 2026 / license — https://cs50.harvard.edu/x/ , https://cs50.harvard.edu/x/2023/license/
- freeCodeCamp about / Python certs — https://www.freecodecamp.org/news/about , https://www.freecodecamp.org/news/python-curriculum-is-live/
- The Odin Project about / curriculum — https://www.theodinproject.com/about , https://github.com/TheOdinProject/curriculum
- Exercism — https://exercism.org/ , https://en.wikipedia.org/wiki/Exercism
- Khan Academy CS — https://www.khanacademy.org/computing/computer-science
- Code.org free curriculum / unplugged — https://code.org/en-US/about/code-org-free-curriculum-commitment , https://code.org/curriculum/unplugged
- CS Unplugged — https://www.csunplugged.org/en/
- Scratch / Raspberry Pi Foundation — https://projects.raspberrypi.org/en/projects/getting-started-scratch/0 , https://www.raspberrypi.com/resources/learn/
- OSSU — https://github.com/ossu/computer-science
- Pyodide load/perf/constraints — https://glinteco.com/en/post/beyond-the-server-running-high-performance-python-in-the-browser-with-pyodide-and-webassembly-2026-guide/ , https://pyodide.org/en/stable/usage/wasm-constraints.html
