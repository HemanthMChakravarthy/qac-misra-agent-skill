# Helix QAC — tool reference for agents

Helix QAC (now branded "Perforce QAC") is Perforce's static analyzer for
embedded C and C++, formerly PRQA QA·C (C edition) and QA·C++ (C++ edition).
It is TÜV-SÜD certified for safety-related development (IEC 61508, ISO 26262,
EN 50716, IEC 60880, IEC 62304) — relevant when the project needs an ISO 26262
tool-qualification argument.

## Analysis scopes

- **Per-translation-unit analysis** (default): parse + dataflow per source file
  including its headers.
- **`--inter-tu-dataflow`**: extends dataflow to whole-program level. Requires
  the dataflow depth settings to be enabled (typically via a "Depth" preset);
  cannot be combined with CMA projects.
- **CMA (cross-module analysis)**: operates across translation-unit
  boundaries — duplicate definitions, incompatible declarations, unused
  variables.

Engine: abstract interpretation for sound interprocedural dataflow, hardened
with a commercial SMT solver, with pointer-alias and loop modeling.

## Message IDs, levels, severity — and the rule that overrides them

- Diagnostics carry **numeric message IDs** (QA-C, C edition) or **`C`-prefixed
  IDs** (QA-C++, C++ edition). IDs are **version-specific** — never hardcode a
  message-ID → rule mapping from memory or third-party tables.
- **Message levels 0–9** describe how a diagnostic relates to the ISO language
  standard: 9 = errors (parse/configuration), 8 = constraint violations,
  7 = undefined behavior, 6 = implementation-defined behavior, 5 = design
  issues including all dataflow messages, 3 = major (dangerous conversions,
  pointer usage, redundancy, declarations), 2 = minor (promotion, bracing,
  indentation).
- **Severity is independent of level**: set by Perforce language experts,
  customizable per project. Dataflow messages have certainty variants — e.g. a
  null-dereference "definite"/"apparent" variant can carry severity 7 while the
  "possible" variant carries severity 1.
- **In MISRA configurations, Rule Groups replace levels**: diagnostics surface
  grouped under Mandatory / Required / Advisory rather than 0–9 levels. This is
  the triage view to use.
- Enforcement coverage is published per standard by Perforce (e.g. 100% of
  statically enforceable rules for MISRA C:2023/C:2025 and MISRA C++:2023).
  Each rule is additionally marked Decidable/Undecidable and statically
  enforceable or not.

## Where to read the message → rule mapping (the only reliable sources)

1. The QAC desktop **Rule Groups view** ("Show Only" a rule's diagnostics).
2. The project's **rule configuration file**.
3. The **MISRA Compliance Report (MCR)** — the authoritative per-project
   mapping artifact.

## CLI reference

Canonical CI loop (confirm flag semantics with `qacli <cmd> --help` on the
installed version):

```sh
# Align the QAC project with the build (from a JSON compilation database)
qacli sync -P <project-dir> -t JSON <compile_commands.json>

# Analyze (file list or whole project; optional whole-program dataflow)
qacli analyze -P <project-dir> -f [--inter-tu-dataflow]

# Upload results to Validate (the CI/server flow)
qacli auth --validate --username <u> --password <p> --url <url>
qacli validate config --create -P <project-dir> …
qacli analyze -P <project-dir> -f
qacli validate build -P <project-dir> [-b <build-name> --tags "…"]
qacli validate config --push

# License server
qacli config license-server --add 5055@rlm-server.com
qacli config license-server --check
```

## CI integration patterns

- **Validate** is the dashboard of record: stores results, trends, and
  configurations; supports **delta analysis** on merge/pull requests and
  feature branches; review/assign/comment on diagnostics; track metrics
  (e.g. cyclomatic complexity) over time.
- **Baselines**: focus attention on new/changed findings in legacy code. A
  baseline is a noise filter, not a compliance claim.
- **Jenkins plugin**: File / Message / Project **compliance thresholds** act
  as quality gates — exceeding a threshold marks the build unstable.
  Thresholds can operate per message level so style issues pass while critical
  issues fail.
- **Standard reports**: MCR (MISRA Compliance Report — violations, deviations,
  disapplied rules in MISRA Compliance:2020 format; the audit-facing
  artifact), SCR, CRR, HMR (HIS metrics — the automotive ECU manufacturer
  metric set), MDR.

## Agent rules for tool output

1. Triage by MISRA category (Mandatory → Required → Advisory), not by raw
   message level.
2. Quote the diagnostic's message ID and the rule mapping **as the tool
   reports them** — never translate an ID to a rule number yourself.
3. When a finding looks like a false positive, say so explicitly and explain
   the reasoning (e.g. undecidable rule, tool limitation) rather than
   suppressing it silently. Suppression needs a deviation record — see
   `references/deviations.md`.
