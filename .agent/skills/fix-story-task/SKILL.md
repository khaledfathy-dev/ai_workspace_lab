---
name: fix-story-task
description: Apply developer-requested changes to one approved story Task, run relevant checks, record the fixes, and return it for another developer review.
---

# Fix Story Task Skill

## 1. Objective

Address the developer's review feedback for one implementation Task. Keep changes within the approved scope and return control to the developer for another review.

Changes must remain within the approved story scope and address the review evidence without introducing unrelated work.

This skill is reusable across projects, programming languages, frameworks, and AI agents.

## 2. Required Context

Before fixing findings:

1. Read `AGENTS.md` and `README.md`.
2. Read `development-guidelines/ai-workflow.md`.
3. Read `ai/current-state.md` and `ai/project-context.md`.
4. Read applicable development guidelines.
5. Read the active story's `user-story.md`.
6. Read its approved `implementation-plan.md`.
7. Read the selected task's implementation record.
8. Read the latest developer feedback and any optional advisory review relevant to that Task.
9. Inspect the actual source code and relevant changes.

Use the latest developer feedback as the primary work list. Preserve earlier feedback, implementation records, and optional AI advisory reports as history.

## 3. Authorization and Scope

Before modifying code, verify that:

- The implementation plan was approved.
- The selected task exists in the plan.
- The Task is awaiting developer review with requested changes, or the user explicitly selects a documented finding to fix.
- The requested fixes are within the approved scope.

If the findings are ambiguous, contradictory, or require a material scope change, ask the user for clarification.

Do not fix findings belonging to unrelated tasks unless explicitly authorized.

## 4. Analyze Each Finding

For every finding:

1. Identify its review ID.
2. Understand the reported evidence and impact.
3. Inspect the actual code to confirm the issue.
4. Determine the smallest appropriate correction.
5. Check whether the correction affects other components or requirements.
6. Identify relevant regression tests.

If the finding cannot be reproduced or the evidence appears incorrect, document the discrepancy rather than blindly changing code.

If a correction would require an architectural change, breaking API change, data migration, or other material scope expansion, stop and request approval before proceeding.

## 5. Apply Corrections

- Address only findings for the selected task.
- Follow the existing architecture and applicable development guidelines.
- Preserve unrelated user changes.
- Add or update tests where appropriate.
- Do not weaken security controls, validation, or tests to silence findings.
- Do not introduce unnecessary dependencies or unrelated refactoring.
- Do not implement another story Task.
- Do not automatically change the approved plan to accommodate an implementation deviation.

If additional defects are discovered, document them and ask whether they should be addressed separately unless they are necessary to resolve the authorized finding safely.

## 6. Verification

Run the appropriate checks for the corrections, including relevant tests and builds where practical.

For each check, record:

- The command or procedure used.
- Whether it was executed.
- The actual result.
- Any failure, skipped check, or environmental limitation.

Inspect the final diff for unintended changes.

Do not claim that a finding is resolved solely because code was changed. Record the supporting evidence and any remaining uncertainty.

## 7. Update Implementation Records

Update the selected task's implementation record with:

- Review finding IDs addressed.
- Corrections made.
- Files changed.
- Tests and verification performed.
- Actual results.
- Findings that remain unresolved.
- Any deviations, risks, or blockers.

Preserve earlier records and review findings.

When fixes have been applied and recorded, set the Task status to:

`Fixes Applied / Awaiting Developer Review`

If some findings remain unresolved, explain why and record the actual status and next action accurately. Do not imply that all findings are fixed when they are not.

## 8. Restrictions

- Do not implement unrelated features.
- Do not fix findings outside the selected task without authorization.
- Do not approve your own corrections.
- Do not mark the Task `Developer Approved`.
- Do not automatically proceed to another implementation task.
- Do not commit, push, merge, deploy, or create a pull request unless separately authorized.
- Do not overwrite or delete previous review reports.

## 9. Update Workflow State

Update `ai/current-state.md` with:

- The active story and task.
- Findings addressed and outstanding.
- The implementation record location.
- Verification results.
- Current task status.
- Next recommended action: developer review and an explicit approve-or-request-changes decision.

Preserve earlier developer feedback and optional advisory reports; record each developer decision in the implementation record or plan.

## 10. Completion Report

Report:

- Story and task identifiers.
- Review finding IDs addressed.
- Summary of corrections.
- Files changed.
- Verification performed and actual results.
- Findings still unresolved.
- Implementation record location.
- Final status: `Fixes Applied / Awaiting Developer Review`, or the accurately documented blocked outcome.

Then stop and return control to the developer. Do not approve the fix.