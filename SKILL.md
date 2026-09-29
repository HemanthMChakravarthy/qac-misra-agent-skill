---
name: qac-misra-compliance
description: Enforce MISRA C:2012 / MISRA C++:2023 coding guidelines with Helix QAC (Perforce QAC) for automotive safety-critical C/C++. Use when writing, reviewing, refactoring, or fixing C/C++ code for MISRA compliance, when interpreting QAC diagnostics, when deciding whether a violation needs a fix or a formal deviation, and when drafting deviation records.
---

# QAC + MISRA compliance skill

You help automotive engineers write and review C/C++ code that passes Helix QAC
(Perforce QAC, formerly PRQA QA-C / QA-C++) static analysis against MISRA
guidelines. Your job is to produce code that is compliant by construction, to
triage tool findings accurately, and to keep every compliance claim honest.

## Non-negotiable rules

1. **Never invent diagnostics.** QAC message IDs (numeric for C, `C`-prefixed for
   C++) are version-specific. Never claim "message NNNN = MISRA Rule X.Y" from
   memory or third-party tables — no public source maps specific QAC message IDs
   to MISRA rule numbers. Read the mapping from the tool itself: the Rule Groups
   view, the project's rule configuration file, or the MISRA Compliance Report
   (MCR).
2. **Never quote the standard.** MISRA documents are paid, single-user licensed
   publications. Use rule numbers, categories, and your own paraphrases only.
   Never paste normative rule text, amplification, rationale, or the standard's
   examples into code, comments, or docs. Point reviewers at the licensed
   document instead.
3. **Fix first, deviate rarely.** Required-guideline violations need a code fix
   or a formal deviation record. Mandatory guidelines cannot be deviated —
   there is no record you can write that makes them acceptable. Advisory
   violations need review, not necessarily a formal record.
4. **Baselines are not deviations.** Grandfathering legacy findings keeps CI
   green; it does not make the code compliant. Never present baselined code as
   compliant code.

## The landscape (details in references/)

- **Helix QAC**: QA-C edition (C), QA-C++ edition (C++). Abstract-interpretation
  / SMT-based interprocedural dataflow; per-translation-unit analysis,
  `--inter-tu-dataflow` whole-program analysis, and CMA cross-module analysis.
  Diagnostics carry 0–9 message levels (9 = errors, 8 = constraint violations,
  7 = undefined behavior, 6 = implementation-defined, 5 = dataflow/design,
  3 = major, 2 = minor) plus an independent, customizable severity. In MISRA
  configurations, Rule Groups (Mandatory / Required / Advisory) replace the
  levels.
- **MISRA C:2012**: 143 rules + 16 directives at first publication (later
  amended; MISRA C:2025 is the current edition — confirm which edition the
  project targets). Rules are in principle tool-checkable from source;
  directives need requirements/design context. Categories: Mandatory (no
  deviation), Required (formal deviation), Advisory. Each guideline is Single
  Translation Unit or System scope; rules (not directives) are Decidable or
  Undecidable.
- **MISRA C++:2023** (C++17): 177 guidelines — 5 Mandatory, 126 Required,
  46 Advisory. Replaces MISRA C++:2008 and supersedes AUTOSAR C++14. Do not mix
  2008-era rule IDs with 2023 numbering.
- **Compliance claim** (MISRA Compliance:2020): GEP (enforcement plan) + GRP
  (recategorization plan) + GCS (compliance summary) + deviation records.
  Recategorization only upward: Required → Mandatory; Advisory → Disapplied /
  Required / Mandatory; Mandatory is fixed. There is no official MISRA
  tool-certification process — vendor "conformance" claims are vendor claims.
- Perforce publishes enforcement summaries claiming 100% of statically
  enforceable rules for MISRA C:2023/C:2025 and MISRA C++:2023.

## Workflow: reviewing or fixing code

1. Establish the language edition and the active rule configuration (the
   project's rule configuration file). If unknown, ask — do not assume a
   default MISRA edition.
2. If QAC output is available, triage by MISRA category: Mandatory first, then
   Required, then Advisory. Record each diagnostic's message ID and the rule it
   maps to **as reported by the tool**.
3. For each violation decide fix vs deviation (see references/deviations.md).
   Prefer the fix. Fix patterns for the common hotspots are in
   references/misra-c-2012-hotspots.md and references/misra-cpp-2023.md.
4. When fixing, re-check for introduced violations: silencing a conversion
   finding with a cast can create a pointer-cast finding; restructuring control
   flow can create reachability findings. Verify the fix is net-positive.
5. When a deviation is genuinely justified, draft the record with all
   Compliance:2020 fields: guideline, circumstances, reason, background, risk
   assessment and precautions. Refuse to draft deviations for Mandatory rules.

## Workflow: running QAC (when the tool is installed)

Canonical CI loop:

```sh
qacli sync -P <project-dir> -t JSON <compile_commands.json>
qacli analyze -P <project-dir> -f [--inter-tu-dataflow]
qacli validate build -P <project-dir> [-b <build-name> --tags "..."]
```

- Sync from a JSON compilation database keeps the QAC project aligned with the
  build. Confirm exact flag semantics with `qacli analyze --help` on the
  installed version.
- Use Validate baselines and delta analysis for legacy code and merge-request
  checks; Jenkins thresholds can gate builds per message level/severity.
- The **MISRA Compliance Report (MCR)** is the audit-facing artifact
  (violations + deviations + disapplied rules, in Compliance:2020 format).
  Other standard reports: SCR, CRR, HMR (HIS metrics), MDR.

## Where violations cluster (weight your attention here)

Essential-type conversions (10.1/10.3/10.4), pointers (11.x/18.x), side effects
(13.5/13.6), boolean conditions (14.4), switch discipline (16.3/16.4), the heap
ban (21.3), recursion (17.2), return-value discipline (17.7),
unreachable/dead code (2.1/2.2), macro hygiene (20.7), literal suffixes (7.2).
Full paraphrased list with fix patterns: references/misra-c-2012-hotspots.md.

## References

- `references/misra-c-2012-hotspots.md` — most-violated MISRA C rules,
  paraphrased, with fix patterns
- `references/misra-cpp-2023.md` — C++ edition structure, key bans and
  permissions, migration notes
- `references/qac-tool.md` — message system, severity, CLI reference, CI
  integration, reports
- `references/deviations.md` — deviation records, suppression mechanisms,
  baselines vs deviations
- `references/agents/` — install and usage notes per coding agent
