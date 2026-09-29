# Deviations, suppressions, and the compliance process

Based on MISRA Compliance:2020 (the process document for making a MISRA
compliance claim). Paraphrased — the authoritative process wording is in the
licensed document.

## The compliance claim and its artifacts

A compliance claim requires:

- **All Mandatory** guidelines met — no deviations possible.
- **All Required** guidelines either met or covered by a **formal deviation**.
- **Advisory** guidelines considered; they may be *Disapplied* via a
  recategorization plan, recorded in project documentation.

The four artifacts: **GEP** (Guideline Enforcement Plan — how each guideline
is checked), **GRP** (Guideline Re-categorization Plan — the contract between
acquirer and supplier on category changes), **GCS** (Guideline Compliance
Summary), and **deviation records**.

Recategorization is upward-only: Mandatory is fixed; Required → Mandatory;
Advisory → Disapplied / Required / Mandatory. Every recategorization needs a
rationale (especially Disapplied), review of existing deviations, and tool
reconfiguration.

Deploy automated checking **early** in the lifecycle — late-stage compliance
checking means expensive re-coding, re-review, and re-test.

## Deviation records — required fields

Every deviation record must contain:

1. The guideline(s) violated (rule/directive number).
2. The circumstances in which the violation is acceptable.
3. The reason the deviation is required.
4. Background information explaining the context and the language issues.
5. Requirements including relevant risk-assessment procedures and precautions.

Plus the approval workflow: developer → technical lead → safety manager →
configuration management, with periodic re-review (commonly every 6–12
months). **MISRA C:2012 Permits** (2021) can pre-approve recurring deviation
patterns instead of per-instance records.

## Agent rules for deviations

1. **Fix first.** A deviation is for violations that cannot reasonably be
   fixed (hardware constraints, generated code, proven-safe idioms) — not for
   convenience.
2. **Never draft a deviation for a Mandatory rule.** Refuse and explain why.
3. **Never quote normative rule text** in the record; reference the guideline
   by number and cite the licensed document.
4. Every record needs all five fields above plus an owner and a review date.
   A deviation without a review date is a liability.
5. Hardware-register casts (e.g. Rule 11.3 findings on memory-mapped I/O) are
   the classic legitimate deviation — document the address map, the access
   discipline, and why the cast is safe.

## QAC suppression mechanisms

- **In-code annotations**: the widely documented PRQA-era marker form is
  `/* PRQA S <message-id> ++ <deviation-id>: <justification> */`, pairing the
  diagnostic's message ID with a deviation record ID — the annotation *is* the
  traceability link. **Verify this syntax against the installed QAC version's
  documentation before using it**; it was not re-confirmed against the current
  Perforce manual.
- **Rule configuration**: rules/messages can be enabled, disabled, re-leveled,
  or re-severitized per project via the Rule Configuration File — the right
  place for project-wide policy (e.g. a recategorized Advisory rule).
- **Deviation tags**: QAC supports tagging suppressions with the *reason* for
  suppression — use them; untagged suppressions are an audit finding waiting
  to happen.
- **Scope**: suppressions are scope-configurable (single instance → whole
  project). Keep scope as narrow as the justification allows.

## Baselines vs deviations — do not conflate

| | Baseline | Deviation |
|---|---|---|
| Purpose | Focus CI on new/changed findings in legacy code | Formally accept a specific guideline violation |
| Justification | None required | Full record: circumstances, reason, background, risk assessment |
| Approval | Engineering decision | Developer → lead → safety manager → CM |
| Audit status | Noise filter | Compliance evidence |
| Review | When code is touched | Periodic (6–12 months) |

Presenting baselined code as compliant code is an audit risk. The skill must
never do it.
