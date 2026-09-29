# MISRA C++:2023 — structure, key bans and permissions

MISRA C++:2023 (published late 2023, for C++17) **replaces MISRA C++:2008**
(228 rules for C++03) and **supersedes the AUTOSAR C++14 guidelines** — the two
documents were jointly merged into one. Do not mix 2008-era rule IDs
(e.g. `5-0-3`) with 2023 numbering; they are different schemes.

Structure: **177 guidelines — 5 Mandatory, 126 Required, 46 Advisory** — the
same three categories as MISRA C (Mandatory = no deviation; Required = formal
deviation; Advisory = review).

## What changed vs the old world

- Before 2023, AUTOSAR-following teams needed *two* documents (AUTOSAR C++14 +
  MISRA C++:2008, with AUTOSAR rejecting some 2008 rules). MISRA C++:2023 is a
  single document, and some legacy rules were re-evaluated as no longer
  safety-relevant.
- It is deliberately more modern-C++-friendly than its predecessors.

## Things it now permits (do not flag these as violations)

`constexpr`, lambdas, `auto` (with constraints), smart pointers,
`std::optional`, `std::variant`, `std::array`, `std::span`, structured
bindings. Prefer these over their banned counterparts — e.g. `std::array`
over C arrays, smart pointers over raw owning pointers.

## Things it still bans (paraphrased, own words)

- Exceptions and RTTI
- `goto`
- Unrestricted unions
- C-style casts (use C++ casts, and only where justified)
- `malloc` / `free` / `new` / `delete` (no dynamic allocation in the safety subset)
- `setjmp` / `longjmp`
- Recursion, direct or indirect

## Agent guidance for C++ reviews

1. Confirm the project targets MISRA C++:2023, not MISRA C++:2008 or AUTOSAR
   C++14 — the rule sets and numbering differ. If the project still cites
   AUTOSAR C++14 rule IDs, ask which document is authoritative.
2. Weight findings toward the same families as C: conversions, pointers and
   ownership, control-flow discipline, and resource management. In C++ the
   ownership questions (raw vs smart pointers, allocation bans) dominate.
3. When modernizing legacy C++ (e.g. C++03/11/14 codebases), prefer the
   permitted modern constructs above — they are the compliant path, not clever
   workarounds.
4. As with C: never quote normative rule text; use rule numbers + paraphrase,
   and point reviewers at the licensed document.
5. Perforce claims 100% enforcement of statically enforceable MISRA C++:2023
   rules in Helix QAC (QA-C++ edition) — see `references/qac-tool.md`.
