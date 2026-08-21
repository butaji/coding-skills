---
name: lisp-mindset
description: >
  Applies a Lisp mindset to software design and coding: data-first thinking,
  patterns over branches, unified single representations, derived state,
  explicit edge-confined effects, and composition over abstraction. Use when
  designing architecture, refactoring, modeling nested data, collapsing
  duplicate logic, or choosing between composition and abstraction. Not for
  language-specific Common Lisp or Clojure syntax advice.
---

# Lisp Mindset Manifesto

1. Everything is data.
2. Patterns over branches.
3. Unification is king.
4. One fact, one representation.
5. Derive everything else.
6. State = memo(f(events)).
7. Behavior = state machines.
8. Effects stay explicit and at the edges.
9. DRY knowledge, not syntax.
10. Prefer composition over abstraction.
11. Functions ≤ 40 lines, complexity ≤ 10.
12. Files ≤ 500 lines.

## How to apply

- **Model the data first**, before touching functions: name each shape and its
  invariants; awkward code usually means a wrong data model, so fix the data.
- **Replace deep `if`/`switch` chains with pattern dispatch** — a table, a
  discriminated union, or a dispatch map (manifesto 2 & 3) instead of nested
  branches.
- **Keep one canonical representation** of each fact (4); derive every other
  view from it rather than storing duplicates that can drift apart.
- **Derive, don't mutate**: compute a new value from the previous one (5).
  Model state as `state = memo(f(events))` — replay events, cache the reduce
  (6); encode behavior as explicit state machines with named transitions (7).
- **Push side effects to edges** (8): keep functions pure in the middle; I/O,
  network, and env live only at the boundary.
- **DRY knowledge, not syntax** (9): deduplicate the *concept*; repeat similar
  wording rather than fabricate a thin abstraction over it.
- **Compose over build frameworks** (10): reach for small combinable functions
  before inventing a base class or config-driven engine.
- **Enforce the size caps** (11 & 12): 40-line functions, complexity ≤ 10,
  500-line files — split instead of exempting.

## Worked example

Task: sum the even elements of a nested list.

- **Data first** (1): the input is already a list; name the recursive shape.
- **Pattern over branch** (2): one pure step — empty list → `0`; number → itself
  if even; sub-list → recurse.
- **Effects at edges** (8): no I/O, so nothing leaves the boundary.
- **Size caps** (11): a 6-line recursive function, complexity 1.

Result: an explicit base case + step, no loops, no mutable accumulator.

## Guardrails

- Don't force recursion where a plain loop is clearer — the goal is explicit
  structure, not theatrics.
- Keep compositions shallow and named; a 15-stage pipeline hides as much as a
  15-branch function.
- Preserve the host language's idioms — apply the *mindset*, not Clojure or
  Common Lisp syntax.
- Every helper must earn its only call site; if it doesn't clarify, inline it.