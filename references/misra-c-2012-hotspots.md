# MISRA C:2012 — violation hotspots for automotive code

Rule numbers and categories are public metadata from the MISRA C:2012
amendment lineage (latest rollup: MISRA C:2025). All descriptions below are
paraphrases in our own words — **never treat them as normative text**; the
authoritative wording lives in the licensed MISRA document.

Confirm which MISRA C edition the project targets before citing a rule number:
amendments added rules (e.g. AMD1 security rules, AMD2 Rules 1.4/21.21, AMD3
C11/C18 rules, AMD4 threading rules 22.11–22.20).

Categories: **M** = Mandatory (no deviation permitted), **R** = Required
(formal deviation required), **A** = Advisory.

## The essential-type model (Rules 10.x) — highest-volume friction

MISRA's "essential type" model classifies expressions by their underlying
logical type (essentially signed/unsigned/boolean/character/float) rather than
the C promotion rules. Most 10.x findings are implicit conversions the C
compiler accepts silently.

| Rule | Cat | Paraphrase | Typical fix pattern |
|---|---|---|---|
| 10.1 | R | Operands of an operator must have an appropriate essential type for that operator | Cast operands explicitly, or choose types so the expression's essential type is already correct |
| 10.3 | R | Do not assign an expression into a narrower or different-category essential type without an explicit cast | Add an explicit cast at the assignment; better, keep variables in the destination's type throughout |
| 10.4 | R | Both operands of arithmetic subject to usual conversions should share the same essential-type category — no casual signed/unsigned mixing | Make both operands the same signedness (usually by widening the signed side or re-typing the unsigned side) |
| 7.2 | R | Integer literals that are logically unsigned must carry a `U` suffix | `uint32_t x = 42U;` — suffix every unsigned-intended literal |
| 12.1 | A | Do not rely on operator precedence being obvious — parenthesize | Add explicit parentheses around mixed operators |

## Pointers (Rules 11.x, 18.x)

| Rule | Cat | Paraphrase | Typical fix pattern |
|---|---|---|---|
| 11.3 | R | No casts between pointers to different object types | Use a union (where permitted), `memcpy`, or restructure so the cast is unnecessary; hardware-register access usually needs a formal deviation |
| 11.5 | A | Avoid converting `void *` to object pointers without an explicit cast | Add the explicit cast |
| 18.1 | R | Pointer arithmetic must stay within the array (or one past its end) | Bounds-check before advancing; prefer indexed access with explicit range checks |
| 18.6 | R | Do not let the address of a stack (automatic) object escape its lifetime | Copy the value out, or change the object's storage duration |
| 18.8 | R | No variable-length arrays | Use a fixed-size array with a documented maximum, or a static pool |

## Side effects and evaluation order (Rules 13.x)

| Rule | Cat | Paraphrase | Typical fix pattern |
|---|---|---|---|
| 13.5 | R | The right-hand side of `&&` / `\|\|` must not contain persistent side effects | Hoist the side-effecting call into its own statement before the condition |
| 13.6 | M | No expressions with side effects inside `sizeof` | Evaluate the expression into a variable first, then apply `sizeof` to the variable or the type |
| 1.3 | R | Avoid undefined or critical unspecified behavior — the catch-all | Remove the UB (sequence-point violations, out-of-bounds access, etc.); this rule fires behind many deeper findings |

## Control flow (Rules 14.x–17.x)

| Rule | Cat | Paraphrase | Typical fix pattern |
|---|---|---|---|
| 14.4 | R | `if` / `while` / `for` controlling expressions must be essentially Boolean — no implicit integer-to-bool tests | Write `if (x != 0)` instead of `if (x)` |
| 15.5 | A | Prefer a single point of exit at the end of a function | Restructure with a result variable; note many projects recategorize or deviate this one |
| 15.6 | R | Loop and selection bodies must be compound statements | Always use braces, even for single statements |
| 15.7 | R | Every `if … else if` chain must end with `else` | Add a final `else` (even if only a comment/assert/defensive action) |
| 16.3 | R | Every `switch` clause must end with an unconditional `break` | No fall-through; duplicate shared code or restructure |
| 16.4 | R | Every `switch` must have a `default` label | Add `default:` with defensive handling |
| 17.2 | R | No recursion, direct or indirect | Convert to iteration with an explicit stack if needed |
| 17.7 | R | Do not discard the return value of a non-void function | Use it, or cast to `(void)` deliberately with a comment explaining why |
| 2.1 | R | No unreachable code | Remove it, or restructure so it is reachable |
| 2.2 | R | No dead code (executed, but results never used) | Remove it |
| 2.7 | A | No unused function parameters | Remove the parameter, or `(void)param;` with justification |
| 9.1 | M | Never read an automatic (stack) variable before it has been assigned | Initialize at declaration |

## Declarations, macros, library (Rules 8.x, 20.x, 21.x)

| Rule | Cat | Paraphrase | Typical fix pattern |
|---|---|---|---|
| 8.4 | R | Every externally-linked function/object definition needs a visible compatible declaration (prototype) | Declare in the module header; include the header in the defining TU |
| 8.7 | A | Do not give external linkage to objects/functions used in only one TU | Mark them `static` |
| 20.7 | R | Parenthesize macro parameters and the whole macro body | `#define ADD(a, b) ((a) + (b))` — or prefer `static inline` functions |
| 21.3 | R | No dynamic heap allocation (`malloc`/`calloc`/`realloc`/`free`) in the safety-critical subset | Static allocation, fixed pools, or stack with bounded sizes; architectural constraint, decide at design time |

## How to use this file

1. When reviewing code, scan for these hotspots first — they account for the
   bulk of QAC findings in automotive C codebases.
2. When QAC reports a rule number listed here, the fix pattern is a starting
   point; verify it against the tool's diagnostic and the project's rule
   configuration before applying.
3. For rules not listed here, consult the licensed MISRA document — do not ask
   the agent to guess the rule's meaning from its number.
