# Project Agent Workflow

This project follows a "requirement-driven, verification-driven, convergent" Agent development process.

This document is a project-level execution ruleset, subordinate to the Codex global development principles. Where this document overlaps with Codex, Codex governs; this document does not redefine what Codex already covers, and only adds execution detail Codex does not.

Every user requirement goes through the following cycle:

Understand the requirement → Ask about missing requirements → Finalize the requirement → Check current state → Implement → Static checks → Automated tests → Verify the requirement is met → Report → Wait for the next requirement

The "Static Checks," "Automated Tests," and "Verification" nodes can each trigger a correction loop. "Static Checks" and "Automated Tests" share a single fix-progress/abort judgment (see below) when they fail. When "Verification" is not satisfied, the flow always returns to "6. Static Checks" and runs through the process again, without a separate progress judgment.

## Fix-Progress Judgment and Abort Criteria

Both "6. Static Checks" and "7. Automated Tests" apply this judgment on failure.

When an error is found, first classify its source per the Codex global rules: project error, tool error, operating-system error, or execution-environment error. Only after confirming it is a project error does the process below apply; errors that are not project errors are outside the scope of this document.

Once confirmed as a project error, after each failure first determine whether the fix is still moving toward a solution before deciding to continue fixing or to abort — do not loop indefinitely. The investigation and fix methodology itself (how to find the root cause, form and test a hypothesis, and implement the fix) is defined by a relevant debugging skill, if one is installed for this project; this section only governs the continue/abort decision, not how a fix is carried out.

**If no relevant debugging skill is installed or accessible:** this does not relax the requirement below. No fix may be proposed without first identifying the root cause of the failure — at minimum, read the full error/stack trace, confirm the failure is reproducible, and state a specific hypothesis of the cause before making any change. Note explicitly in the report that no debugging skill was available and this fallback minimum was used instead of a fuller methodology.

**Judged as still making progress — continue fixing** if:

- The scope of the error is shrinking;
- Verification results are improving;
- The root cause is clear;
- The fix remains within the current requirement and architecture scope.

**Judged as abort** if any of the following occur:

1. The problem keeps recurring and fixes are not moving toward a solution.
2. Continuing requires an ever-growing pile of exceptions, special cases, or patches.
3. The original technical assumption or architecture has proven unworkable and a new direction is needed. Concrete trigger: 3 or more fix attempts on the same issue have failed, especially where each attempt revealed a new symptom in a different place rather than converging — see the relevant debugging skill's fix-attempt guidance, if one is installed. This is not itself a fourth fix attempt; it is the signal to stop attempting fixes and report.
4. The problem lies in an environment, permission, hardware, external service, or other factor outside the Agent's control.
5. The cost of continuing clearly exceeds the reasonable benefit supportable by current information.
6. Resolving the problem requires the user to make a choice that has not yet been decided.

When judged as abort — whether in "6. Static Checks" or "7. Automated Tests" — do not proceed further; go directly to "9. Report," and:

- Do not keep consuming resources on further attempts.
- Preserve the current working state.
- Explain what has been completed, the reason for the current failure or blockage, what has and has not been verified, and what requires the user's decision or intervention.

## 1. Understand the Requirement

- First understand the problem, goal, and constraints the user actually wants solved.
- Do not translate the user's description directly into code.
- Distinguish clear requirements, known constraints, and undecided items.
- Do not add important assumptions on your own that would affect the design.

## 2. Ask About Missing Requirements

- Before starting implementation, proactively check for important missing requirements that would affect implementation or verification.
- Only ask questions that genuinely affect the outcome; do not ask unrelated questions just to be thorough.
- If the information is already sufficient, proceed directly to the next stage without asking for the sake of asking.
- If a significant ambiguity exists, ask the user first rather than guessing.
- Subjective, vague quality adjectives in the requirement (e.g., "more refined," "more polished," "smoother") are themselves a signal to trigger clarification, regardless of how well-defined the task type appears — such adjectives lack a verifiable concrete standard, and this directly affects whether "9. Report" and "8. Verification" can be established.

## 3. Finalize the Requirement

- Once the requirement information is sufficient to begin work, form explicit implementation goals and acceptance criteria.
- Subsequent implementation is based on the finalized requirement.
- Undecided future requirements are not included in the current implementation.
- If a fundamental contradiction or a new significant ambiguity in the original requirement is discovered during implementation, stop the related work and ask the user again.

## 4. Check Current State

- Before implementing, check the relevant files, code, scenes, resources, settings, tools, and current work state.
- Prioritize understanding and reusing existing structures.
- Do not pre-build architecture in anticipation of future requirements.
- Do not recreate functionality, rules, or definitions that already exist (the single-source-of-truth principle is defined in the Codex global rules; this document does not redefine it).
- Do not modify content unrelated to the current requirement.
- If the current state conflicts with a previously finalized decision or design, explicitly flag the conflict and raise it with the user; do not silently override an existing decision without notice.

## 5. Implement

- Before implementing, the Agent may proceed directly for simple, single-file, single-step tasks. This is not a hard requirement: when a task spans multiple files, involves multiple phases, has significant dependencies between steps, or is likely to lose context partway through, the Agent should surface the option of planning first to the user, rather than silently deciding either way on its own.
- When a plan is written, it must reduce uncertainty, not merely restate the cycle: at minimum it must state the scope of the change, the main steps, likely risks, and how the result will be verified. "Modify → test → done" does not meet this bar.
- Use the minimum implementation necessary to satisfy the requirement.
- Prefer modifying existing structures over unnecessarily adding new abstraction layers, frameworks, tools, or dependencies.
- The implementation should map directly to the finalized requirement and acceptance criteria.
- Do not expand the current scope of work because it "might be needed later."
- For high-impact changes (e.g., changing existing architecture, wide-ranging impact, hard to reverse), propose an implementation plan and get the user's approval before executing; ordinary changes with limited impact do not require individual approval. Reversibility is a factor in this judgment: a low-risk, easily-reversible decision should be executed directly without over-asking, while a hard-to-reverse or wide-impact change requires approval first regardless of how confident the Agent is in it. This is a project-level tightening of the Codex global rule that system-level, destructive, or externally-impactful operations require explaining their necessity first: changes meeting this project's high-impact definition require, in addition to explaining necessity per Codex, the user's explicit approval before execution — explanation alone is not sufficient to proceed.

## 6. Static Checks

After implementation, run checks that do not require fully running the game/application, for example:

- Code syntax and type issues
- Scene/Node structure
- Resource paths
- Project settings
- Obvious reference errors
- Git diff
- Other static issues directly related to this change

If issues are found, handle them per "Fix-Progress Judgment and Abort Criteria":

- Judged as still making progress → Fix, following the relevant debugging skill if one is installed → Re-run static checks.
- Judged as abort → Do not proceed to "7. Automated Tests"; go directly to "9. Report."

Once checks pass, proceed to "7. Automated Tests." This node is also the shared return point for the correction loops from both "7. Automated Tests" (when still making progress) and "8. Verification" (when not satisfied).

## 7. Automated Tests

Once static checks pass, run whatever automated tests the current environment supports.

Choose the appropriate verification method based on the requirement, for example:

- Project startup
- Headless execution
- Scene/resource loading
- Program logic
- Functional testing
- Unit testing
- Other automatically verifiable behavior

On failure, handle it per "Fix-Progress Judgment and Abort Criteria":

- Judged as still making progress → Fix, following the relevant debugging skill if one is installed → Return to "6. Static Checks," and go through static checks and automated tests again.
- Judged as abort → Do not proceed to "8. Verification"; go directly to "9. Report."

## 8. Verify the Requirement Is Met

Once automated tests pass, go back to the original requirement and acceptance criteria to confirm:

- Whether the original requirement has been fully realized.
- Whether all acceptance criteria have been met.
- Whether any requirement has only had its program logic completed but not its actual visual or interactive verification.
- Whether any part could not be automatically verified due to environment limitations.

Do not equate "tests passed" with "requirement complete"; automated verification and manual acceptance must be clearly distinguished, and content that has not actually been verified must not be claimed as complete.

If not satisfied:

> Fix → Return to "6. Static Checks," and go through static checks and automated tests again.

If satisfied, proceed to "9. Report."

## 9. Report

Whether completing normally or aborting at either "6. Static Checks" or "7. Automated Tests," report:

- What was completed.
- How it was verified.
- The verification results.
- What has not yet been verified.
- If aborted, the reason for the blockage and what requires the user's decision.

Conclusions such as "tests passed" or "verification complete" may only be claimed when backed by reproducible, concrete evidence (e.g., actual command output, logs, actual execution results); they must not be substituted with inference, past experience, or "should be fine" in place of actual verification — self-reporting is not inherently trustworthy on its own, so a distinction must be made between "what was instructed to be done" and "what has actually been verified as done."

Avoid reporting process details unrelated to the outcome.

## 10. Wait for the Next Requirement

After reporting, stop the current work.

Unless the user raises a new requirement, correction, or further instruction, do not expand the scope of work, optimize, or build unrequested functionality on your own.

Upon receiving the next requirement, start again from "1. Understand the Requirement."
