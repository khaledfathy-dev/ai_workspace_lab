---
name: review-user-story
description: "Optionally provide advisory feedback on a complete story's acceptance criteria and cross-Task behavior. Developer approval remains authoritative."
---

# Review User Story Skill

## 1. Objective

When explicitly requested, provide an advisory, story-wide assessment of whether the cumulative changes satisfy the approved story and plan, work coherently across Tasks, and preserve relevant existing behavior.

This review is optional and non-authoritative. It does not approve Tasks, change workflow status, replace developer review, or replace final verification.

## 2. When to Use

Use `/review-user-story` only when the developer requests an optional cumulative assessment:

- After `/implement-whole-story` completes, for an additional review of the cumulative result.
- After all planned tasks have been implemented through separate operations.
- When the user explicitly requests a review of the entire implemented user story.

The story-wide review may identify follow-up concerns at any point after implementation, but must not change Task status or act as a workflow gate.

## 3. Required Context

Before reviewing:

1. Read `AGENTS.md`, `README.md`, and `development-guidelines/ai-workflow.md`.
2. Read `ai/current-state.md`, `ai/project-context.md`, and relevant project-analysis documents.
3. Read applicable development guidelines.
4. Read the active story's `user-story.md` and approved `implementation-plan.md`.
5. Read the complete implementation record and implementation records for every planned task.
6. Read all available task review reports, fix reports, and the final report if one exists.
7. Inspect the cumulative source changes, relevant tests, configuration, and actual repository state. Do not rely only on reports or status labels.

## 4. Entry Conditions

Confirm that:

- The implementation plan is approved.
- Every planned Task has an implementation record and implementation work is complete; no Task remains `Not Started`, `In Progress`, or `Blocked`.
- The actual changes and their scope can be identified reliably.

The story may have pending task reviews or may already have completed final verification. Do not treat this review as permission to skip any remaining workflow gate.

If an entry condition is not met or the implementation cannot be inspected reliably, record the limitation and stop without approving the story or changing task statuses.

## 5. Review Scope

Review the complete story, not just a selected task. At minimum:

- Trace every acceptance criterion to the implementation and available verification evidence.
- Compare the cumulative changes with the approved scope and plan.
- Inspect interactions between tasks, including shared contracts, persistence, transactions, error handling, security boundaries, configuration, and compatibility where relevant.
- Consider regressions and unintended changes outside the story scope.
- Evaluate whether tests cover the combined behavior and important failure cases.
- Check reported verification against available evidence. Run focused, non-destructive checks when needed to resolve a concrete review question; record any check not run.

Use the project's applicable development guidelines and established conventions. Report only evidence-based, actionable concerns. Distinguish confirmed defects from risks, recommendations, and unresolved questions.

## 6. Findings and Outcome

For each actionable finding, record:

- **Finding ID:** Stable story-review identifier, such as `STORY-REV-001`.
- **Severity:** Critical, High, Medium, or Low.
- **Location:** File and relevant code section.
- **Category:** Such as correctness, security, data integrity, architecture, testing, or compatibility.
- **Evidence:** Specific code, behavior, or verification evidence.
- **Impact:** Why the issue matters to the story or existing behavior.
- **Required action:** A concrete corrective action.
- **Affected task(s):** Planned task identifiers when they can be determined.

Use one of these outcomes:

- **Story Review Passed:** No blocking story-wide findings remain. This does not itself approve any task or certify final verification.
- **Changes Required:** One or more actionable issues need correction.
- **Review Blocked:** Essential evidence or access is unavailable, preventing a reliable review.

Do not inflate severity or present style preferences as defects.

## 7. Save the Story Review Report

Save the report in the active story directory as:

`review-story.md`

Include:

1. Story identifier and title, review date, and outcome.
2. Review scope and implementation tasks covered.
3. Requirements and acceptance-criteria assessment.
4. Cumulative changes and cross-task interactions inspected.
5. Files, reports, and verification evidence inspected.
6. Findings with severity, evidence, impact, affected tasks, and required actions.
7. Checks run during review and checks not run, with reasons.
8. Remaining risks, limitations, and next permitted action.

Preserve review history. If `review-story.md` already exists, append a clearly dated review section rather than replacing earlier findings.

## 8. Update Workflow State

Update `ai/current-state.md` and relevant story records with the review date, outcome, report location, findings, and next permitted action.

- Leave Task and Story statuses unchanged. In particular, do not use this advisory review alone to mark Tasks `Developer Approved` or a Story `Final Verification Passed`.
- Record potential concerns and affected Tasks in the advisory report. The developer decides whether they require changes and records any status updates.
- If a concern appears to require a scope or plan change, document it and wait for developer direction. Do not change the approved plan or status.
- If evidence is missing, report the limitation; do not change approval statuses.

Do not automatically fix findings or start another workflow stage.

## 9. Restrictions

- Do not modify application source code or tests.
- Do not fix findings or implement additional work.
- Do not change the approved implementation plan.
- Do not replace or bypass developer review, requested fixes, or final verification.
- Do not approve Tasks or mark the Story complete based on this review.
- Do not claim a test or check passed without evidence.
- Do not commit, push, merge, deploy, or create a pull request.

## 10. Completion Report

Report:

- Story identifier and review outcome.
- Tasks and acceptance criteria covered.
- Findings by severity and affected tasks.
- Verification evidence, unrun checks, and limitations.
- Story review report location.
- Any suggested follow-up for the developer to consider.

Stop after reporting the review. Wait for the user's instruction.
