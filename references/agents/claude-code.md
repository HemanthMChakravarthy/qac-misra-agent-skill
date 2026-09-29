# Using this skill with Claude Code

## Install

**Project scope** (recommended — the skill travels with the repo):

```sh
mkdir -p .claude/skills/qac-misra-compliance
cp -r /path/to/qac-misra-agent-skill/* .claude/skills/qac-misra-compliance/
```

**Personal scope** (available in every project):

```sh
mkdir -p ~/.claude/skills/qac-misra-compliance
cp -r /path/to/qac-misra-agent-skill/* ~/.claude/skills/qac-misra-compliance/
```

Claude Code auto-discovers `SKILL.md` files in these locations. Verify the
skill loaded with `/skills` in a session.

## Use

Invoke it explicitly when starting compliance work:

> Use the qac-misra-compliance skill to review this driver for MISRA C:2012
> compliance.

Or let it trigger naturally — the `description` in the frontmatter tells
Claude Code when the skill applies (writing/reviewing/fixing C/C++ for MISRA,
interpreting QAC output, drafting deviations).

## Tips

- Point the agent at your project's rule configuration file and the MISRA
  edition you target (C:2012 vs C:2023 vs C:2025; C++:2023) in your first
  message — the skill asks for this, but stating it upfront saves a round
  trip.
- Paste QAC diagnostic output (message IDs + rule mappings as the tool
  reports them) rather than describing findings in prose.
- For deviation drafts, tell the agent the deviation ID scheme your project
  uses so the `PRQA S` annotations (see `references/deviations.md`) link up
  with your records.
