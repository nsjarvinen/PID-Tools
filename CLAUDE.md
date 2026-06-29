# Project Operating Charter — P&ID Authoring Tool
*(JavaScript / single-file HTML edition)*

This file governs how you (Claude Code) work in this repository. Read it
every session and follow it over any default behaviour. The human is the
decision-maker; you do the mechanical work and surface genuine decision
points. Bias toward autonomy within a single approved change, and toward
asking only at the two gates defined below.

---

## 0. Project context

- **What we are building:** a portable, single-file HTML application for
  authoring P&IDs for power generation plant.
- **Governing principle:** the data model is the single source of truth; the
  graphic is a *projection* of it. Never read state back out of the DOM or
  SVG — render from the model, not the other way around.
- **Controlling references** (authoritative for scope and shape):
  `PID-STD-REF` (standards), `PID-FRS` (functional requirements),
  `PID-SCHEMA` (data schema). Treat these as source of truth. If the work
  appears to conflict with them, flag it rather than guessing.

---

## 1. The loop

Every piece of work is one cycle. Never run two cycles at once.

1. **Frame.** State the change as a single one-line goal, then a short plan
   (3–6 steps). Do this in plan mode — read-only, no edits yet.
   → GATE 1: the human approves or adjusts the plan.
2. **Build.** On approval, implement autonomously: write code, run it, run
   the self-tests, and self-check (Section 4). Do not stop to ask about
   routine edits — that is what approval bought.
3. **Report.** Surface only three things: the diff, the test result, and any
   residual doubts you could not resolve yourself. Keep it short.
   → GATE 2: the human accepts or rejects.
4. **Settle.**
   - Accept → commit to git with a clear message. This is the durable
     checkpoint. Then start the next cycle from here.
   - Reject → the human gives a one-line reason. Rewind the code to the last
     committed state, record the reason in Section 7 as a standing
     constraint, then retry with that constraint in force.

---

## 2. One change at a time

A cycle changes exactly one thing. If a worse result appears, it must be
attributable to that one change. Never bundle a refactor with a feature, or
two fixes together. If the human asks for several things, split them into
separate cycles and confirm the order.

---

## 3. Hard rules (do not violate)

- Never present code you have not actually run. For this project, "run" means
  the self-test harness passes **and**, for any change to visible behaviour,
  the file has been opened and the behaviour observed.
- Never silently work around a failing test or check. Surface it.
- Never delete or rewrite working code to "tidy it up" unless tidying is the
  stated goal of the current cycle.
- Never re-propose anything listed in Section 7 (Rejection Log).
- **Never introduce an external dependency** — no CDN, web font, framework,
  bundler, or build step. The deliverable is one self-contained HTML file
  that runs offline from the filesystem. If a capability looks like it needs
  an outside library, raise it as a decision; do not just add it.
- Do not rely on `localStorage` / `sessionStorage` as the source of truth.
  The model lives in memory and is persisted as JSON the user saves and
  loads. (Browser storage is unreliable under `file://`.)
- Never modify tracked source with shell file operations (rm/mv/cp) when an
  edit tool would do — shell changes are not captured by checkpoints.
- Always commit to git on an accepted change before starting the next cycle.
- When unsure whether something is one change or two, treat it as two.

---

## 4. Self-check protocol (before every Report)

Run these in order. They are listed strongest-first; do not skip the early
ones because the later ones feel sufficient.

1. **Run it.** Execute the self-test harness; for any visible behaviour, open
   the file and observe it directly. This is ground truth, not opinion. A
   change is not done until the full self-test suite passes.
2. **Adversarial pass.** Re-read the change against the goal as if reviewing
   a stranger's work. Find up to three ways it is wrong, fragile, or
   suboptimal. Fix what is clearly wrong. For genuine judgment calls, do not
   decide silently — carry them to the Report.
3. **State residual doubt.** Explicitly name anything you are still unsure
   about. "I think X but I am not confident because Y." Honest uncertainty
   is more useful than false confidence.

Do not treat "are you sure?" self-review as a substitute for running the
code. Execution catches what self-review misses.

---

## 5. Acceptance checks (JavaScript)

- Vanilla JavaScript (current ES), no framework, no build, no transpile. One
  HTML file with inline CSS and JS.
- **Tests live in an in-file self-test harness** — assertions that run in the
  browser (e.g. opening the file with `#selftest`) and report pass/fail
  in-page, with zero external tooling, runnable from `file://`. The testable
  core (model, validation, serialisation) is written as pure functions with
  no DOM dependency, so it can also be exercised headlessly during
  development without making any tool part of the shipped artifact.
  *(Provisional mechanism — confirm before first build cycle.)*
- Every behavioural change needs at least one assertion that would fail
  without the change. No test, not done.
- Functional regression is objective: if a previously passing assertion
  fails, the change is rejected automatically — do not bring it to the human
  as a choice.
- Quality (readability, structure, naming) is the human's call at Gate 2, not
  yours. Optimise for being easy to follow, not for cleverness.

---

## 6. Checkpointing and rollback

Two layers, used for different things:

- **Checkpoints (`/rewind`)** — fast, in-session undo for a bad attempt
  inside a cycle. Cheap. Use freely.
- **Git commits** — the durable floor. One commit per accepted cycle, with a
  message stating the one-line goal. This is what survives sessions and what
  a hard reset returns to.

The rule of thumb: rewind to abandon an attempt; commit to lock in a result.
There must always be a clean git state to return to between cycles.

---

## 7. Rejection Log (grows over time)

When the human rejects something, record the reason here as a standing
constraint. Treat every entry as a permanent prohibition for this project.
Re-proposing a logged item is a Section 3 violation.

<!-- Format: - [date] rejected: <what> — because <why> -->

(empty — entries accrete as the project runs)

---

## 8. Conventions

- Australian English in comments, identifiers where natural, and all UI text.
- **Data is the source of truth; the graphic is rendered from it, never the
  reverse.** Hold this line even when a DOM shortcut looks easier.
- Prefer clear, boring code over clever code. Flag gaps and assumptions
  honestly rather than papering over them.
- Keep the single-file, zero-dependency constraint sacrosanct (Section 3).
- Keep functions small enough to hold in your head. If a reviewer would need
  the whole file to understand one function, it is too big.
- Component types register themselves into the registry; the core engine does
  not hard-code any specific symbol or tagging scheme.
- Commit messages: imperative mood, state the goal, e.g.
  "add port model to component record" or
  "render gate-valve symbol from component geometry".
