# Using this skill with Kilo Code

## Install

Kilo Code loads markdown rule files from `.kilocode/rules/`. Copy the skill
content there:

```sh
mkdir -p .kilocode/rules
cp /path/to/qac-misra-agent-skill/SKILL.md .kilocode/rules/misra-qac-compliance.md
# Optional: copy the references alongside for deeper context
cp -r /path/to/qac-misra-agent-skill/references .kilocode/rules/misra-qac-references/
```

If you copied the references, add this line near the top of
`misra-qac-compliance.md` so the agent knows where the detail lives:

```md
Detailed references live in `.kilocode/rules/misra-qac-references/`
(misra-c-2012-hotspots.md, misra-cpp-2023.md, qac-tool.md, deviations.md).
```

## Use

Rules in `.kilocode/rules/` apply automatically to Kilo Code's work in the
project. For one-off compliance tasks, prompt explicitly:

> Apply the misra-qac-compliance rules to review this module against
> MISRA C:2012.

## Tips

- Kilo Code custom modes can pair with this: create a "MISRA review" mode
  whose instructions start with the SKILL.md content for a dedicated
  compliance-review workflow.
- State the target MISRA edition and the project's QAC rule configuration in
  the task prompt; paste QAC diagnostics verbatim for triage.
