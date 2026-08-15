## Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

- State your assumptions explicitly and proceed on them. Ask only when the
  readings lead to materially different work and guessing wrong is expensive.
- If multiple interpretations exist, name them — don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.

## Reporting Style

When reporting to me, be extremely concise and sacrifice grammar for concision.

## Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- Build what was asked: no extra features, no abstractions for single-use code,
  no unrequested flexibility, no error handling for impossible scenarios.
- Prefer decremental development — removing code that isn't needed beats keeping
  it "just in case". Dead code is technical debt.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## Surgical Changes

**Every changed line traces directly to my request.**

- Leave adjacent code, comments and formatting exactly as they are, even where
  the style differs from yours. Match the surrounding style in what you add.
- Remove imports/variables/functions that YOUR changes orphaned.
- Pre-existing dead code: mention it, leave it.

## Comments

Before writing a comment, ask what it would cost to omit it entirely — usually
nothing. Then ask whether a clearer identifier can carry it instead. What
survives both gets a sentence, not an essay. Three kinds are worth zero:
**history** ("this used to be X" — git keeps it), **restating the standard**,
and **justifying a duplication or workaround** — that last one is a load-bearing
excuse, it goes stale silently because nothing tests a justification, and it
ends up arguing against a fix that has become free.

## Goal-Driven Execution

**Define success criteria. Loop until verified.**

Requirements are best expressed as tests: executable, unambiguous, and unable to
go stale silently. Markdown is second best. Prose-only requirements are a last
resort for what genuinely cannot be tested (visual UX, hardware interactions).

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan of `[step] → verify: [check]` lines.
Strong criteria let you loop independently; "make it work" doesn't.

## TDD

Kent Beck's cycle, one test at a time: **Red → Green → Refactor**.

- Write the simplest failing test for the next small increment, named for the
  behaviour (`shouldSumTwoPositiveNumbers`).
- **Red before green** — watch it fail against the unfixed code. A test that
  looks right but was never seen red is unverified, and one written after the
  fix exists gets its assertion shaped by whatever the fix happens to produce.
  If the fix landed first, revert it (or monkeypatch the buggy function back),
  confirm red, restore, confirm green.
- Write just enough code to pass — no more.
- Refactor only on green, one named refactoring at a time, prioritising
  duplication removal and clarity.
- Run the whole suite (minus long-running tests) after each step.
- For a defect: an API-level failing test first, then the smallest test that
  replicates it, then make both pass.

## Test Discipline

- **Time is an input — pin it.** `datetime.now()` in a test undermines
  reproducibility. Production code that needs the clock takes an optional `now`
  parameter defaulting to the real one, so tests inject a fixed value.
- **Wait for the real condition, not a guessed duration.** `sleep(0.1); assert X`
  is slow on every run and flaky on a loaded machine. Poll the actual predicate,
  with a generous timeout as a safety net for genuine failure, not as the
  expected wait. Stronger still where the output has a deterministic terminator:
  wait on that and guess nothing. Only negative assertions ("nothing arrives")
  need a real bounded sleep.
- **Verify against reality, not just against assertions.** Whole classes of bug
  are structurally invisible to unit tests — how output branches *combine*, how
  a rendered frame actually looks, how real hardware reacts. Decode the artifact,
  capture the packets, measure against the real thing. A green suite is not the
  same as a correct one.

## Tidy First and Commit Discipline

Separate **structural** changes (renaming, extracting, moving — behaviour
identical) from **behavioural** ones. Never mix them in a commit; do the
structural ones first, with tests run before and after to prove they changed
nothing.

Commit when all tests pass, all warnings are resolved, and the change is one
logical unit. Say in the message which of the two kinds it is. Small and
frequent beats large and rare.

Commit each finished topic before starting the next. Unrelated changes piling up
in one working tree make a clean split expensive later — reconstructing each
slice by hand, with no intermediate history left to split from.
