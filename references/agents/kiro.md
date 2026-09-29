# Using this skill with Kiro

## Install

Kiro applies **steering files** from `.kiro/steering/` to the agent's work.
Add the skill as a steering file:

```sh
mkdir -p .kiro/steering
cp /path/to/qac-misra-agent-skill/SKILL.md .kiro/steering/misra-qac-compliance.md
# Optional: copy the references alongside for deeper context
cp -r /path/to/qac-misra-agent-skill/references .kiro/steering/misra-qac-references/
```

If you copied the references, add this line near the top of
`misra-qac-compliance.md`:

```md
Detailed references live in `.kiro/steering/misra-qac-references/`
(misra-c-2012-hotspots.md, misra-cpp-2023.md, qac-tool.md, deviations.md).
```

Steering-file inclusion behavior (always vs conditional) depends on your Kiro
version — check the Kiro docs for frontmatter options if you want this loaded
only for C/C++ work.

## Use

With the steering file in place, Kiro applies the compliance rules to C/C++
work automatically. For explicit tasks:

> Following the misra-qac-compliance steering, triage this QAC report and
> draft fixes for the Required violations.

## Tips

- State the target MISRA edition and point at the project's QAC rule
  configuration file in your first message of a compliance task.
- Paste QAC diagnostic output verbatim (message IDs + rule mappings as the
  tool reports them) rather than summarizing findings.
