# AGENTS.md

This repository is governed by `PURIA.md`.
Agents MUST read `PURIA.md` before any action.
`PURIA.md` is the single source of truth for agent behavior, engineering style, workflow, git rules, commits, testing, releases, and project-specific doctrine.
`.puria/design/DESIGN.md` is the design source referenced by `PURIA.md`; agents MUST read it before any task that affects UI, TUI, visual identity, layout, components, typography, or colors.
`.puria/agents/GITHUB_OPERATOR.md` defines the optional GitHub Operator sub-agent; agents MUST read it before any task that creates or manages GitHub labels, milestones, issues, projects, branches, or pull requests.
`.puria/agents/AGENT_EXECUTION_WORKFLOW.md` defines the optional issue/project/PR execution workflow; agents MUST read it before turning plans into tracked work.
`.puria/agents/INTEGRATOR.md` defines the optional final integration agent; agents MUST read it before merging sub-agent outputs into a roadmap or implementation plan.
Before doing anything, agents MUST read `PURIA.md`.
If `PURIA.md` is missing, unreadable, or unclear:

→ STOP  
→ DO NOT modify files  
→ report the problem

There is no fallback behavior.
`PURIA.md` is the only source of truth.

---

## Execution

- Do NOT infer conventions
- Do NOT adopt undocumented patterns
- Use ONLY rules defined in `PURIA.md`

If something looks like a convention but is not defined:

→ append it to `.puria/HITL.md`  
→ do NOT use it
