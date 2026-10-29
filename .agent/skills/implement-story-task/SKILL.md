---
name: implement-story-task
description: Implement one selected Task from an approved story plan, test and record the changes, then return control for developer review and approval.
---

# Implement Story Task Skill

## 1. Objective

Implement one selected implementation task from an approved user story plan.

Work must follow the existing project's architecture, coding conventions, development guidelines, and approved implementation plan.

This skill is reusable across projects, programming languages, frameworks, and AI agents.

## 2. Required Context

Before implementation:

1. Read `AGENTS.md` and `README.md`.
2. Read `development-guidelines/ai-workflow.md`.
3. Read `ai/current-state.md` and `ai/project-context.md`.
4. Read applicable development guidelines.
5. Read the selected story's `user-story.md`.
6. Read its `implementation-plan.md`.
7. Read relevant project-analysis documents.
8. Inspect the actual source code affected by the selected task.
9. Read prior implementation records and developer feedback relevant to that Task.

Do not rely solely on summaries when the actual source code is available.

## 3. Authorization Checks

Before changing source code, verify that:

- The implementation plan exists.
- The user has explicitly approved the plan.
- The selected Task exists in the approved plan.
- The selected Task is not already `Developer Approved`.
- Required dependencies are complete or otherwise explicitly resolved.
- The requested changes remain within the approved scope.

If authorization or a critical dependency is missing, stop and explain what is needed.

Approval of the plan does not authorize implementing every task in one operation.

## 4. Select One Task

Identify the exact Task selected by the user.

If the user requests implementation without identifying a task, ask which task to implement unless the current workflow state clearly identifies one that is already authorized.

Before implementation, confirm that the task's scope and dependencies are understood.

Update the Task status to `In Progress` when implementation begins.

## 5. Implement the Task

1. Follow the approved implementation plan.
2. Apply the relevant development guidelines.
3. Reuse existing project patterns where appropriate.
4. Make only the changes required for the selected task.
5. Add or update appropriate tests.
6. Preserve backward compatibility unless a breaking change is approved.
7. Follow applicable security, validation, error-handling, logging, and data-integrity requirements.
8. Keep changes focused and maintainable.

Inspect related components before making changes that could affect existing behavior.

Do not introduce a new architecture, framework, dependency, or design pattern without a justified need and the required approval.

## 6. Scope Restrictions

- Implement only one selected Task per operation.
- Do not implement another Task automatically.
- Do not silently expand the approved scope.
- Do not modify unrelated files or features.
- Do not weaken tests, validation, authorization, or security controls to make a check pass.
- Do not overwrite unrelated user changes.
- Do not execute destructive operations without explicit authorization.
- Do not commit, push, merge, deploy, or create a pull request unless separately authorized.

If the actual project differs materially from the plan, stop and report the discrepancy before making out-of-scope changes.

## 7. Verification

Run the relevant verification steps specified in the plan and required by the project.

Examples include:

- Targeted unit or integration tests.
- Build and compilation checks.
- Static analysis and formatting checks.
- API or contract validation.
- UI checks.
- Database migration validation.
- Security and authorization tests.

Use the project's actual tools and commands.

If a check cannot run because of a missing dependency, environment limitation, or other blocker, record it as not executed. Do not report an unexecuted check as passed.

Review the actual changes for unintended modifications.

## 8. Record Implementation Results

Update the active story's `implementation.md` with:

- Task ID and title.
- Implementation date.
- Files and components changed.
- Summary of changes.
- Design decisions and deviations from the plan.
- Tests and verification commands executed.
- Actual verification results.
- Known limitations and follow-up items.
- Any blockers.

Do not overwrite previous task records. Preserve history by adding a clearly identified section or using a consistent task-specific record.

After implementation, set the Task status to `Implemented / Awaiting Developer Review` when the changes are recorded and ready for the developer.

If implementation cannot be completed, record the actual outcome and mark the task `Blocked` when a genuine unresolved obstacle prevents progress.

## 9. Update Workflow State

Update `ai/current-state.md` with:

- Active story and selected task.
- Current workflow stage.
- Implementation outcome.
- Implementation report location.
- Verification status.
- Remaining blockers.
- Next recommended action: developer review and an explicit approve-or-request-changes decision for this Task.

Do not mark the Task `Developer Approved` yourself. An implementation report or AI self-check is not developer approval.

## 10. Completion Report

Report:

- The selected task.
- Summary of changes.
- Files changed.
- Verification performed and actual results.
- Checks not performed and why.
- Deviations, risks, or blockers.
- Implementation report location.
- Final Task status and the developer checkpoint awaiting.

Then stop. Return control to the developer and wait for explicit review feedback or approval.