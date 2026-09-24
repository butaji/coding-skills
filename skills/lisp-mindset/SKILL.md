---
name: lisp-mindset
description: >
  Write the smallest correct program by thinking like a Lisper: everything is
  data, one representation per fact, derived state, patterns over branches,
  a composable domain language, and effects at the edges. Use when writing,
  refactoring, or reviewing code, designing data models or architecture, or
  when code feels repetitive, branchy, or larger than the problem. Not for
  Common Lisp or Clojure syntax questions.
---

# Lisp Mindset Manifesto

Every line carries domain meaning. Code is a liability; data is leverage.
Model the data (1–3), derive the rest (4–6), speak the domain's language
(7–8), and keep the core honest (9–10).

1. **Everything is data.** Model the domain, its rules, and its config as plain
   data before writing any function. When code feels awkward, the model is
   wrong — fix the data.
2. **Unification is king.** Seek the one representation in which separate
   cases become the same case. Fewer shapes, fewer functions.
3. **One fact, one representation.** Every fact lives in exactly one place. No
   copies to sync, no flags that can drift.
4. **Derive everything else.** Views, flags, totals, and caches are functions
   of the facts. Build new values from old ones instead of mutating shared
   ones.
5. **State = memo(f(events)).** An identity is a sequence of values over time;
   its current state is the fold of its events, cached when needed.
6. **Behavior = state machines.** Name every state and every transition; make
   invalid states unrepresentable.
7. **Patterns over branches.** Dispatch on the shape of data — tables, maps,
   unions, pattern matching — instead of `if`/`switch` chains. Stable rules
   become data, prepared once and applied to changing inputs.
8. **Grow a domain language by composition.** Build small operators in the
   domain's vocabulary with uniform input, output, and failure shapes; compose
   them until the top level reads as the domain:
   `validate(required("email"), maxLen("name", 80))`. Prefer this to base
   classes and config-driven engines, and keep the laws callers rely on — an
   empty validator changes nothing; `parse(print(x)) == x`.
9. **Effects stay explicit and at the edges.** The core is pure; I/O, network,
   clock, and env live at the boundary. A function either computes or acts,
   never both.
10. **DRY knowledge, not syntax.** Each rule has one home. Code that looks
    alike but encodes different rules stays separate.

## Reduction pass

Once it works, before finishing, remove:

- helpers with one call site that don't name a domain concept → inline them
- parallel `if`/`switch` arms → a table (7)
- stored values derivable from others → compute them (4)
- defensive checks for states the model already excludes
- wrappers that only rename or re-export library calls
- comments that restate code; unused params, flags, and branches
- configurability nobody asked for

Stop when removing anything more would lose meaning.

## Example

Before — the knowledge is buried in control flow:

```ts
function shippingCost(order) {
  if (order.region === "US") {
    return order.express ? 25 : 5;
  } else if (order.region === "EU") {
    return order.express ? 30 : 8;
  } else {
    return order.express ? 50 : 15;
  }
}
```

After — the table is the spec, the logic is one line:

```ts
const SHIPPING = {
  US:    { standard: 5,  express: 25 },
  EU:    { standard: 8,  express: 30 },
  OTHER: { standard: 15, express: 50 },
};

const shippingCost = ({ region, express }) =>
  (SHIPPING[region] ?? SHIPPING.OTHER)[express ? "express" : "standard"];
```

## Guardrails

- Preserve the host language's idioms — the mindset, not Lisp syntax.
- Explicit structure, not theatrics: a plain loop or local mutation beats a
  clever pipeline or forced recursion when it reads better. Clarity wins ties;
  fewer lines win the rest.
- Keep compositions shallow and named; a 15-stage pipeline hides as much as a
  15-branch function.
- Grow the language from real domain patterns, not speculation. Every helper
  must earn its call site.

## Technical appendix

- **Functions ≤ 40 lines, cyclomatic complexity ≤ 10.** Split along a domain
  seam, never exempt.
- **Files ≤ 500 lines.** Split by domain concept.
- **No magic numbers.** Name domain limits, timeouts, and conversion factors
  with their units (`SESSION_TIMEOUT_MS`); `0`, `1`, and `""` stay inline.
