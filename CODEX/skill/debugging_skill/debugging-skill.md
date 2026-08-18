---
name: debugging-skill
description: Technical methodology for investigating and fixing project errors. Invoked only from agent_workflow.md's fix loop ("6. Static Checks" / "7. Automated Tests", when a failure is classified as a project error and judged as still making progress). Not a standalone workflow.
---

# Debugging Skill

## Scope

This document defines HOW to investigate and fix a project error once agent_workflow.md's fix loop has been entered. It does not redefine:

- What counts as a project error vs. tool/OS/environment error — see AGENTS.md.
- Whether to continue fixing or abort — see agent_workflow.md, "Fix-Progress Judgment and Abort Criteria." The fix-attempt count produced by this document (Phase 4, step 4) is the input to that judgment, not a separate decision.

## Iron Law

No fix without root cause investigation first. A fix proposed before completing Phase 1 is a symptom fix and is not acceptable.

## Phase 1: Root Cause Investigation

1. Read the full error message and stack trace before forming any theory. Note exact file paths, line numbers, error codes.
2. Confirm the failure is consistently reproducible and identify the exact steps. If it is not reproducible, gather more data — do not guess at a fix for an unreproducible failure.
3. Check what changed recently that could plausibly cause this (diff, recent commits, new dependencies, config or environment changes).
4. If the system has multiple components or layers (e.g. build → sign, API → service → database), add diagnostic output at each component boundary — what data enters, what exits, whether config/environment propagates — and run once to locate which layer actually fails, before investigating that layer in detail.
5. If the error surfaces deep in a call stack, trace the bad value backward: what produced it, what called that with the bad input, continuing until the origin is found. Fix at the origin, not where the symptom surfaced.

## Phase 2: Pattern Analysis

1. Locate a working example in the same codebase that is structurally similar to the broken case.
2. If implementing or matching an existing pattern, read the reference implementation in full before adapting it — partial reading produces partial understanding and new bugs.
3. List every concrete difference between the working and broken cases, including ones that seem too small to matter.
4. Identify what the broken component depends on (other components, config, environment, implicit assumptions) that the working example satisfies.

## Phase 3: Hypothesis and Testing

1. State a single, specific hypothesis: "X is the root cause because Y." Vague hypotheses are not testable.
2. Test it with the smallest possible change, one variable at a time. Do not bundle multiple candidate fixes into one test.
3. If the test does not confirm the hypothesis, form a new hypothesis and return to Phase 1 with the new information — do not layer another fix on top of the failed one.
4. If the cause is genuinely not understood after this, say so explicitly rather than proceeding on a guess.

## Phase 4: Implementation

1. Before fixing, establish minimal reproducible evidence of the failure. The form depends on the nature of the problem — an automated test, a minimal one-off script, a browser/DOM reproduction, a captured console error, or a screenshot/visual comparison are all valid. When the project already has a suitable automated test framework for this kind of issue, prefer a failing automated test. The evidence must let the failure be confirmed before the fix and confirmed resolved after — not every issue can or should be forced into a test case (e.g. a layout shift, a hover animation glitch, or a browser-specific rendering issue is often better evidenced by a reproduction or visual comparison than by an automated test).
2. Implement exactly one fix addressing the identified root cause. No unrelated changes, no incidental refactoring bundled in.
3. Verify against the same evidence gathered in step 1: the failure is no longer present, and nothing that previously worked has regressed. Do not claim resolution without checking against that evidence.
4. Track fix attempts for this issue rather than losing count. This document does not decide when to stop: the count, and whether each attempt narrowed the problem or instead revealed a new symptom in a different place, is evidence for agent_workflow.md's Fix-Progress Judgment. Surface it explicitly when reporting or handing back — e.g. "attempt 3; the previous two attempts each surfaced a new, unrelated symptom" — rather than silently continuing or silently stopping on your own count. See agent_workflow.md's abort criteria for how this evidence is judged.

## When Investigation Finds No Root Cause

If Phase 1–3 are genuinely exhausted and the failure is environmental, timing-dependent, or external to the project (per AGENTS.md's error-classification distinction), that itself is the conclusion:

1. Document what was investigated and ruled out.
2. Implement the appropriate handling for that class of failure (retry, timeout, explicit error surface) rather than a code fix for a code problem that doesn't exist.
3. This is not a project error abort case under agent_workflow.md — report findings per "9. Report" as originally scoped.
