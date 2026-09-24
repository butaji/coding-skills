---
name: lisp-mindset
description: >
  Apply a Lisp mindset to software design and coding: model problems as data,
  grow small domain languages, derive state, and compose declarative operations
  with clear contracts. Use when designing architecture, refactoring, modeling
  nested data, or simplifying repeated logic. Not for language-specific Common
  Lisp or Clojure syntax advice.
---

# Lisp Mindset Manifesto

1. Represent the problem as data.
2. Keep one canonical representation of each fact.
3. Derive views from canonical facts instead of synchronizing copies.
4. Separate values, identities, and state. For event-backed state,
   `state = memo(f(events))`.
5. Make behavior and state transitions explicit.
6. Describe stable rules declaratively as data.
7. Dispatch on meaningful variants when repeated branches obscure the pattern.
8. Grow a small domain language from reusable operators.
9. Compose operators through clear input, output, and failure contracts.
10. Preserve the laws that make reusable operations compose.
11. Separate fixed inputs from changing inputs; specialize repeated work when it
    helps.
12. Keep effects visible and at boundaries.
13. Deduplicate knowledge, rather than merely similar syntax.

## How to apply

- **Model before implementing** (1–3): name the data shapes and their
  invariants. Store each fact once; compute dependent views from that source.
- **Model change deliberately** (4–5): distinguish an identity from its current
  value and name valid transitions. When replay or history matters, derive state
  from events and cache the result where useful. A state machine helps when
  transitions and invalid states need to be explicit.
- **Make rules readable** (6–7): express stable domain rules as data or small
  transformations. Use a table or dispatch function when cases share a shape;
  keep a direct conditional when it is clearer.
- **Grow the language bottom up** (8–9): build small domain operators that make
  the program read in its own vocabulary. Compose them through explicit inputs,
  outputs, and failure behavior; add syntax or a DSL when it clarifies repeated
  domain work.
- **State composition laws** (10): for reusable reducers, mappers, parsers, or
  serializers, identify relevant identities, associativity, or round trips.
  Check the laws that callers rely on; do not invent laws for unrelated APIs.
- **Stage recurring work** (11): prepare a stable rule set or schema once when
  it is reused with changing inputs and doing so improves clarity or cost.
- **Contain effects** (12): make I/O and shared-state changes easy to find;
  keep value transformations independent of them where practical.
- **Abstract knowledge** (13): give one domain rule one home. Similar-looking
  code can remain separate when it represents different knowledge.

## Worked example

Task: validate many records against the same set of domain rules.

- Represent the rules as data and name their invariants (1, 6).
- Build small validators that accept a value and return the same error shape;
  compose them into a domain-specific validator (8–9).
- Prepare the fixed rules once, then validate each changing record (11).
- Define an empty validator as the identity: adding it leaves the result
  unchanged. Specify how composed validators combine errors (10).
- Read records and report errors at the boundary; keep validation itself free
  of I/O (12).

## Practical standards

- **Zero magic numbers:** name domain limits, timeouts, and conversion factors
  so their purpose and units are clear. Self-evident values such as `0` for an
  empty sum or `1` for an increment can stay inline.
- Treat functions over 40 lines or complexity over 10, and files over 500
  lines, as prompts to review structure. Split when doing so improves clarity.

## Guardrails

- Preserve the host language's idioms; the mindset does not require Lisp syntax.
- Prefer a direct loop, conditional, or local mutation when it communicates the
  behavior more clearly than a pipeline or DSL.
- Keep compositions shallow and named. A long chain can hide the domain logic.
- A new operator should clarify its uses; avoid helpers that only rename one
  call site.
