# QAC + MISRA Agent Skill

An [Agent Skills](https://agentskills.io)-format skill that teaches AI coding
agents to enforce **MISRA C / MISRA C++** coding guidelines with
**Helix QAC** (Perforce QAC, formerly PRQA QA-C / QA-C++) — the static-analysis
toolchain used across the automotive software industry for safety-critical
C/C++.

Works with **Claude Code, GitHub Copilot, Kilo Code, Kiro**, and any other
agent that supports the open Agent Skills (`SKILL.md`) format.

## What's inside

| Path | Contents |
|---|---|
| `SKILL.md` | The skill: when to use it, non-negotiable rules, review/fix workflows, QAC CLI loop |
| `references/misra-c-2012-hotspots.md` | Most-violated MISRA C rules in automotive code, paraphrased, with fix patterns |
| `references/misra-cpp-2023.md` | MISRA C++:2023 structure, key bans/permissions, migration notes |
| `references/qac-tool.md` | QAC message system, severity, `qacli` reference, CI integration, reports |
| `references/deviations.md` | MISRA Compliance:2020 deviation records, suppression mechanisms, baselines vs deviations |
| `references/agents/` | Install + usage notes per coding agent |

## Install

Pick your agent and follow the one-page guide in `references/agents/`:

- [Claude Code](references/agents/claude-code.md) — `.claude/skills/` or `~/.claude/skills/`
- [GitHub Copilot](references/agents/github-copilot.md) — `.github/skills/`
- [Kilo Code](references/agents/kilo-code.md) — `.kilocode/rules/`
- [Kiro](references/agents/kiro.md) — `.kiro/steering/`

## Design principles

1. **No invented diagnostics.** QAC message IDs are version-specific and no
   public source maps them to MISRA rule numbers — the skill instructs agents
   to read mappings from the tool itself, never from memory.
2. **No quoted standard.** MISRA documents are paid, single-user licensed.
   The skill contains rule numbers, categories, and own-words paraphrases
   only — never normative rule text, rationale, or examples.
3. **Fix first, deviate formally.** Required violations need a fix or a
   Compliance:2020 deviation record; Mandatory rules cannot be deviated;
   baselines are noise filters, not compliance claims.

## Sources

Researched from public Perforce Helix QAC documentation
([help.perforce.com](https://help.perforce.com/helix-qac/enforcement/doc/home.html)),
the SEI CERT wiki, MISRA Consortium public pages, and community automotive
skill projects. See the research notes for the full source list.

## Disclaimer

This is a workflow aid, not legal or safety advice. MISRA rule text,
rationale, and examples require the licensed MISRA documents. Always verify
tool behavior (message IDs, suppression syntax, CLI flags) against your
installed QAC version, and have a qualified safety engineer approve deviation
records.

## License

MIT — see [LICENSE](LICENSE).
