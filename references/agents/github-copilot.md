# Using this skill with GitHub Copilot

## Install

**Repo scope** (recommended — the skill travels with the repo):

```sh
mkdir -p .github/skills/qac-misra-compliance
cp -r /path/to/qac-misra-agent-skill/* .github/skills/qac-misra-compliance/
```

VS Code with Copilot's agent-skills support auto-discovers `SKILL.md` under
`.github/skills/`. Agent-skills support in Copilot is evolving — verify the
skill is picked up in your Copilot version (check the agent's available
skills/context in the Chat view).

**Fallback — repo instructions file.** If your Copilot setup does not yet
support agent skills, add a pointer in `.github/copilot-instructions.md`:

```md
## MISRA / QAC compliance

For any C/C++ compliance work, follow the skill at
`docs/skills/qac-misra-compliance/SKILL.md` (copy of
https://github.com/HemanthMChakravarthy/qac-misra-agent-skill).
Key rules: never invent QAC message-ID → rule mappings; never quote normative
MISRA text; fix first, deviate only with a formal record; Mandatory rules
cannot be deviated.
```

and copy the skill folder to `docs/skills/qac-misra-compliance/`.

## Use

- In Copilot Chat / agent mode, reference the skill explicitly:
  > Following `.github/skills/qac-misra-compliance/SKILL.md`, review
  > `can_driver.c` for MISRA C:2012 Required-rule violations.
- Paste QAC output (message IDs + the rule mapping as the tool reports it)
  into the chat for triage.

## Tips

- State the target MISRA edition and point at the project's QAC rule
  configuration file in your first message.
- Copilot tends to suggest the quickest fix; remind it that the skill requires
  re-checking a fix for newly introduced violations.
