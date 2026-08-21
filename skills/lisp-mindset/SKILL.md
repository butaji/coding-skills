---
name: lisp-mindset
description: Apply a Lisp mindset to design and coding when facing decomposition, nested-data, recursion, or functions-as-values problems. Use to prefer data-first design, recursion over raw loops, small composable functions, and code-as-data reasoning across any language.
---

# Lisp Mindset

Think the way a Lisp programmer thinks. These are mental habits, not language
syntax — they apply to any language. Activated whenever a task involves
structured data, nested logic, transformations, or composable abstractions.

## Guiding principles

1. **Data first, code second.** Name and shape the data, then the functions
   that transform it follow naturally. If a structure is awkward, fix the data.
2. **Recursion over loops.** A loop accumulates state across iterations; a
   recursive step computes one value and asks "what's next?". Prefer the
   recursive shape when it makes the base case and the step explicit.
3. **Small, composable functions.** Each function does one thing and returns a
   value. Compose rather than branch. Avoid functions that are readable only in
   the middle of a larger loop.
4. **Code is data.** Lists, maps, and higher-order functions are building
   blocks. Express intent as transformations (`map`/`filter`/`fold`) instead of
   imperative mutation.
5. **Referential transparency.** Favor pure functions: same input, same output,
   no hidden state. Isolate side effects at the edges.

## How to apply it

- **Decomposition:** for any non-trivial task, split it into (a) the data
  shape, (b) a chain of pure transformations, (c) the side-effecting edges.
- **Nested data:** descend with structure-matching or indexed access in small
  steps; never flatten deeply without naming what each level means.
- **Repeated pattern in code:** extract the pattern into a higher-order helper
  (a function that takes functions) rather than duplicating the loop.
- **Choosing recursion vs. iteration:** use the explicit base case when it
  clarifies intent; fall back to iteration only when stack depth or
  performance is a real constraint — and say so.
- **Avoiding state:** replace "update this variable as I go" with "build a new
  value from the previous one". Prefer immutable updates and pure helpers.

## Guardrails

- Do not force recursion where a simple loop is clearer; the point is explicit
  structure, not theatrics.
- Keep compositions shallow and named; a 15-stage pipeline is as unreadable as a
  15-branch function.
- Preserve the host language's idioms — apply the mindset, not Clojure/Common
  Lisp syntax.
- Every abstraction must earn its place: if a higher-order helper has exactly
  one call site and doesn't clarify, inline it.

## Worked example

Task: sum the even elements of a nested list of integers.

1. **Data:** the input is already a list — name the recursive sub-shape
   ("a list of integers or lists of ...").
2. **Transform:** write one pure step — `sumEven(seq)` where an empty list
   yields 0, a number contributes itself if even, and a sub-list recurses.
3. **Edges:** no side effects here, so nothing moves to the boundary.

Result: a small recursive function whose base case and step are both explicit,
instead of a nested loop with mutable accumulators.