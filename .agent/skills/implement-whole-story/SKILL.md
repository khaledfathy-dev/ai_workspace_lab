---
name: implement-whole-story
description: Implement all remaining Tasks from an explicitly approved story plan in dependency order, pausing for developer review and approval at each Task checkpoint.
---

# Implement Whole Story Skill

## 1. Objective

Implement an entire approved user story in one orchestrated workflow, without requiring separate user invocations for each implementation task.

This skill coordinates implementation, developer review checkpoints, requested fixes, and final verification. It does not replace developer approval. The user may instead choose `/implement-story-task` to implement one Task and stop.

## 2. Required Context

Before changing application code:

1. Read `AGENTS.md`, `README.md`, and `development-guidelines/ai-workflow.md`.
2. Read `ai/current-state.md`, `ai/project-context.md`, relevant project-analysis documents, and applicable development guidelines.
3. Identify the target story from the user's request or unambiguous active workflow context. If ambiguous, ask which story to implement.
4. Read the story's `user-story.md`, approved `implementation-plan.md`, and implementation records.
5. Read relevant existing review, fix, and final reports.
6. Read the complete instructions for `implement-story-task`, `fix-story-task`, and `finalize-story`. Use `review-story-task` only if the developer explicitly requests optional advisory feedback.
7. Inspect the relevant source, configuration, tests, and current repository state.

Do not rely solely on summaries or recorded status values where the underlying evidence is available.

## 3. Authorization and Readiness Gate

Before changing application code, verify all of the following:

- The story and implementation plan exist and match.
- The plan's approval status is explicitly `Approved`, with approval recorded consistently under the workspace approval convention.
- The whole-story request authorizes implementation of the remaining Tasks; completed and developer-approved Tasks must be preserved and skipped.
- The approved scope, task dependencies, implementation tasks, and verification criteria are sufficiently clear to execute.
- Required runtime, database, test, and other prerequisites are available or have an approved, implementable resolution.

A plan marked `Plan Ready`, `Plan Ready / Pending Approval`, or otherwise awaiting approval is not approved. Do not infer approval from prior conversation, a task's implementation, or the story status.

If the plan is not explicitly approved, stop without changing application code. Report the missing approval and recommend `/approve-story-plan`.

If the approval record conflicts with the plan, story, or current workflow state, or if a critical prerequisite is unresolved, stop and report the inconsistency or blocker. Do not repair the plan, invent approval, or proceed based on an assumption.

## 4. Repository State and Safe Resumption

Before implementation:

1. Inspect the repository status and identify existing user changes relevant to the story.
2. Report pre-existing changes that overlap planned files or could affect verification.
3. Preserve all existing work. Do not discard, overwrite, reset, or reformat unrelated changes.
4. If a planned edit overlaps an unrecognized or conflicting user change and cannot be safely integrated, stop and ask the user how to proceed.
5. Compare every task's recorded status with its implementation, review, and fix evidence. Resume from the earliest unresolved task in dependency order; do not reimplement approved tasks.
6. Confirm that dependencies of each task are approved or otherwise complete before starting it.

Do not change the approved plan. If actual repository state reveals a material architectural decision, missing prerequisite, or scope change, stop before making that change and ask the user for direction.

## 5. Implement Tasks and Pause for Developer Checkpoints

Process planned Tasks in dependency order, one Task at a time. Resume from recorded evidence and do not repeat completed work unnecessarily.

### A. Implement

1. Follow `implement-story-task` for the selected Task's approved scope.
2. Mark it `In Progress` when work begins.
3. Implement the related production changes and tests together.
4. Run required verification and record commands and actual results.
5. Set it to `Implemented / Awaiting Developer Review` and report the changed files, behavior, checks, results, and remaining issues.
6. Update `ai/current-state.md` and stop for the developer's explicit decision.

If implementation or required verification fails, address the issue only when the correction is within approved scope. If it cannot be resolved safely, record the blocker and stop; do not proceed to dependent tasks.

### B. Developer review

1. Wait for the developer to review the implementation.
2. Continue only after an explicit approval or requested changes for the current Task.
3. Record an explicit approval as `Developer Approved`; record requested changes as `Changes Requested` in the plan, implementation record, and workflow state.
4. If optional AI advisory review is requested, it does not replace or determine the developer's decision.

### C. Fix and return

If the developer requests changes:

1. Apply `fix-story-task` to the requested changes for this Task only.
2. Keep corrections within the approved plan. If a request needs a material scope or architectural change, stop and ask the developer.
3. Run and record relevant checks, preserve earlier records, and set the Task to `Fixes Applied / Awaiting Developer Review`.
4. Return the result to the developer and wait for the next explicit decision.

Do not begin a dependent Task until its prerequisites are complete and the current Task is `Developer Approved`. Never reinterpret a failed or unexecuted required check as passed.

## 6. Story and Implementation Records

Keep the implementation plan, `implementation.md`, review reports, and `ai/current-state.md` consistent with actual progress:

- Use only the Task and Story status values defined in `development-guidelines/ai-workflow.md`.
- Set the story to `Implementation In Progress` when implementation begins.
- Preserve approved task statuses and all prior implementation, review, and fix history.
- Record each Task's changes, files, verification commands and actual results, developer decision, feedback, fixes, and blockers.
- Mark the story `Blocked` only when a documented unresolved obstacle prevents progress; record the action needed to unblock it.
- Do not mark a Task `Developer Approved` without the developer's explicit decision.

Never change the approved plan or rewrite its history. Document implementation deviations in the implementation record; if a deviation changes approved scope, stop and request authorization.

## 7. Final Verification

After every planned Task is `Developer Approved`:

1. Apply the complete `finalize-story` skill, including its entry conditions, acceptance-criteria verification, project-level checks, final report, and persistent-state updates.
2. Verify against the original story and approved plan. Do not add scope or silently change acceptance criteria.
3. Report final status according to the actual results. Do not mark `Final Verification Passed` if a required criterion or check failed, is blocked, or remains unverified.
4. If final verification identifies corrective work, record the actual outcome and stop for the appropriate implementation and developer-review cycle; do not silently fix defects during finalization.

## 8. Restrictions

- Never implement a story whose plan is not explicitly approved.
- Never change the approved plan or silently expand approved scope.
- Do not skip Task verification, developer review checkpoints, requested fixes, or final verification.
- Do not let the implementer approve its own implementation or fixes.
- Do not overwrite or discard existing user work or prior records.
- Do not claim checks passed unless they were executed and passed.
- Do not modify unrelated files or features.
- Do not commit, push, merge, create a pull request, or deploy without separate explicit authorization.

## 9. Completion Report

Report:

- Story identifier and plan location.
- Each implemented or resumed task, its final status, and review outcome.
- Files changed, including implementation and workflow records.
- Tests and checks executed, with actual results; identify checks not executed and why.
- Fixes performed and review findings resolved or remaining.
- Final verification outcome and report location, or the blocker that prevented completion.
- Relevant pre-existing changes that were preserved.
- If the story completed successfully, note that `/review-user-story` is available for an optional cumulative review; do not invoke it automatically.

Then stop and wait for the user's next instruction.
