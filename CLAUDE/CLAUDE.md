Global Development Principles

Language policy
- Write rule and instruction documents for LLMs/Agents in English.
- Communicate with the user exclusively in Traditional Chinese, except for proper nouns and established shared terminology.

1. Define responsibility before implementation
First determine what the current task is responsible for, what problem it solves, and what is outside its scope.
Do not add capabilities, architecture, tools, or abstractions merely because they may be useful later.
2. Context before capability
Inspect the existing project, environment, tools, and relevant files before deciding what to change.
Prefer using existing capabilities and context over adding new dependencies or infrastructure.
3. Minimal necessary structure
Use the smallest structure that fully solves the current problem.
Before introducing an abstraction, framework, dependency, service, or new layer, identify the concrete problem it solves and verify that the existing structure cannot solve it.
4. Problem-driven development
Make changes in response to actual requirements, observed failures, usage experience, unclear boundaries, or maintenance costs.
Do not modify working systems merely because a different design seems theoretically better.
5. Single source of truth
Define each concept or rule in one authoritative place.
Reuse or reference existing definitions instead of creating duplicate rules, checks, or exceptions.
When a new case conflicts with an existing rule, reconsider the abstraction before adding a special case.
6. Validate before and after changes
Inspect the relevant state before modifying it.
After implementation, perform the smallest relevant automated validation available.
Distinguish project errors from tool, operating-system, or execution-environment errors before changing project code.
Do not claim visual or interactive validation when the execution environment cannot actually perform it.
7. Fix causes, not symptoms
Prefer correcting the underlying design or common cause over accumulating patches, exceptions, or workarounds.
When a fix fails, reassess the diagnosis instead of repeatedly modifying the same area.
8. Keep scope controlled
Do not modify unrelated files, systems, or configuration.
Do not install additional tools, SDKs, plugins, or services unless the current task actually requires them.
For system-level, destructive, or externally consequential actions, explain the necessity before proceeding.
9. Converge
When evidence is sufficient, converge on the supported conclusion and stop investigating resolved issues.
Do not continue optimizing a component after the actual task requirement has been satisfied unless a concrete problem remains.
