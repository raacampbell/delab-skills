# Changelog

## 2.1.0 — faster, cheaper review loop

The agentic workflow caught real mistakes but spent a lot of tokens and wall
time doing it. These changes keep the adversarial review and cut the fixed cost
around it. Nothing here weakens the correctness check; the principles in
`delab-coding-practices/SKILL.md` are unchanged for people and agents writing
code.

### One reviewer pass instead of two

- **Before:** the PM spawned a correctness reviewer and then a separate style
  reviewer. Each preloaded the principles, ran on the session's model, and read
  the same diff from scratch.
- **Now:** one `delab-reviewer` makes a single pass, correctness first, then
  style. Findings are labelled `[correctness]` or `[style]`.
- **Why:** the second spawn repeated most of the first's work. The labels give
  the PM the one distinction it needs to decide what to do next (see below).
- Also applied to `delab-interactive-workflow`, which asked for the same
  two-pass review before a push.

### Re-review only after a correctness fix

- **Before:** "re-review if the fixes are non-trivial", which agents applied
  liberally.
- **Now:** a fresh reviewer is spawned again only when a `[correctness]` finding
  was fixed, and is scoped to the changed lines. Style-only fixes are checked by
  the PM reading the fix diff.
- **Why:** a wrong result reaching a figure is the expensive failure; a
  mis-named variable is not.

### Less PM ceremony for simple items

- **Batch trivial items.** Related `simple` items that touch the same files go
  to one worker as one item with one review, because every item pays a worker
  cold start, a reviewer and an MR.
- **Short form for simple work items.** Title, a sentence or two of what and
  why, acceptance criteria, and type/complexity/status. The full form (inputs,
  outputs, interfaces) is for complex items and anything behind a figure.
- **Worklist only when something changes status.** The full worklist is still
  required whenever an item starts, goes to review, finishes, blocks, or needs
  the PI, but is no longer repeated on replies that move nothing.
- **Shorter MR review record** for simple items: two or three lines.
- **Why:** the process cost was the same for a one-line fix and a new pipeline.

### Style review narrowed

- The reviewer no longer checks principles 4 (map/vectorise), 5 (small
  functions), 11 (optimise only when needed) and 12 (branch naming). They still
  guide writers; they are no longer review findings, because whether a loop
  "should" be a `map` is mostly taste.
- Principles 2 and 5 gained notes (in your working tree: OO is fine, and do not
  over-split into single-use sub-functions), and `languages/matlab.md` was
  updated to match.
- `languages/python.md` got the same two notes: classes (e.g. a `dataclass`) are
  fine alongside pure functions, with module-level mutable globals the thing to
  avoid; and a helper used from one place stays inline unless it has a name
  worth giving, is reused, or needs its own test. Julia and R are unchanged.

### Install

- The README now points `/plugin marketplace add` at
  `raacampbell/delab-skills`, and explains how to swap over from the upstream
  marketplace, which uses the same `delab` name.
- Plugin version bumped to 2.1.0 so the release workflow tags it.

### Not changed, but worth considering next

- Per-item model choice (Sonnet reviewer by default, Opus for data-science or
  complex items). Reviewers still inherit the session model.
- Skipping `isolation: worktree` when only one worker is running.
- A condensed principles checklist in the agent definitions instead of
  preloading the full skill on every spawn.
