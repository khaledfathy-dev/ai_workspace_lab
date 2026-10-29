---
name: finalize-story
description: Perform final verification after every planned Task has explicit developer approval; verify acceptance criteria and record actual results.
---

# Finalize Story Skill

## 1. Objective

Verify that a user story is complete, its approved requirements are satisfied, all implementation Tasks have explicit developer approval, and required final checks have been performed.

Produce an evidence-based final report and update the persistent workflow state.

This skill is reusable across projects, programming languages, frameworks, architectures, and AI agents.

## 2. Required Context

Before final verification:

1. Read `AGENTS.md` and `README.md`.
2. Read `development-guidelines/ai-workflow.md`.
3. Read `ai/current-state.md` and `ai/project-context.md`.
4. Read applicable development guidelines.
5. Read the active story's `user-story.md`.
6. Read its approved `implementation-plan.md`.
7. Read the implementation record for every Task, including developer decisions and feedback.
8. Read any optional advisory review reports relevant to the story.
9. Inspect relevant final source changes, tests, and configuration.
10. Review previous verification results and unresolved issues.

Do not rely solely on summaries or reported status values. Verify important claims against the available evidence.

## 3. Entry Conditions

Before starting final verification, confirm that:

- The story has an approved implementation plan.
- Every planned Task has status `Developer Approved`.
- Every Task has an implementation record with the developer's explicit approval.
- No unresolved blocking finding remains.
- The implementation remains within the approved scope.

If any condition is unmet, document the problem and stop finalization. Identify the required next action.

Do not mark a story complete while any Task lacks explicit developer approval.

## 4. Verify Acceptance Criteria

Evaluate every acceptance criterion in the original story.

For each criterion, record:

- Criterion ID or a concise identifier.
- Expected behavior.
- Verification method.
- Actual result.
- Evidence, such as a test result or inspected behavior.
- Status: Passed, Failed, or Not Verified.

Do not modify the original acceptance criteria to make the story appear complete.

If a requirement cannot be verified, record it as `Not Verified` and explain why.

## 5. Perform Final Verification

Select appropriate checks for the actual project and story, including where relevant:

- Full or targeted build.
- Unit tests.
- Integration tests.
- API or contract tests.
- UI and end-to-end tests.
- Static analysis and code quality checks.
- Security and authorization checks.
- Database migration validation.
- Configuration and integration checks.
- Regression testing.

Prefer the verification commands and procedures documented in the project.

Avoid destructive operations and production-impacting tests unless explicitly authorized.

For each check, record the command or procedure, execution status, actual result, and relevant evidence.

Distinguish clearly between:

- Passed.
- Failed.
- Not Executed.
- Blocked by an environmental or dependency issue.

A check that was not executed cannot be reported as passed.

## 6. Review Scope and Quality

Confirm that:

- The implemented changes match the approved plan.
- No unauthorized scope expansion occurred.
- Required security and data-integrity considerations were addressed.
- Relevant regression risks were evaluated.
- Known limitations and remaining technical debt are documented.
- The final source changes contain no obvious unintended modifications.

If new material defects are discovered, document them and return the story to the appropriate implementation and review stages. Do not silently ignore them.

## 7. Final Outcome

Use the following story status rules.

### `Final Verification Passed`

Use only when:

- All planned Tasks are explicitly approved by the developer.
- All acceptance criteria are verified as satisfied.
- All required final checks passed.
- No unresolved blocking issue remains.
- The final report and workflow records are complete.

### `Blocked`

Use when an unresolved obstacle prevents required verification or completion. Document the blocker and the action needed to resolve it.

If verification fails or an acceptance criterion is not satisfied, do not mark the story as passed. Record the failure and identify the appropriate corrective workflow stage.

## 8. Create the Final Report

Save the final report at:

`ai/stories/sprint-XX/user-story-XXX/final-report.md`

Use the actual story directory and preserve existing records.

Include:

1. Story identifier and title.
2. Approved scope and requirements.
3. Summary of implemented changes.
4. Implementation Tasks and their developer approval outcomes.
5. Acceptance criteria verification results.
6. Build, test, and other verification results.
7. Failed, skipped, or blocked checks.
8. Security, compatibility, and regression considerations.
9. Known limitations and remaining work.
10. Final outcome and supporting evidence.

The report must reflect what actually happened, not what was intended to happen.

## 9. Update Persistent State

Update `ai/current-state.md` and the story records to reflect:

- The final workflow stage.
- The actual story status.
- The final report location.
- Verification results.
- Remaining limitations or blockers.
- Any recommended follow-up work.

Preserve historical implementation and review records.

## 10. Restrictions

- Do not implement new features during final verification.
- Do not silently fix defects discovered during verification.
- Do not approve Tasks yourself or infer developer approval from silence, implementation completion, or AI advisory feedback.
- Do not claim success when required checks failed or remain unverified.
- Do not commit, push, merge, deploy, or create a pull request unless separately authorized.
- Do not automatically start another story.

If corrective work is required, report it and wait for the user's instruction.

## 11. Completion Report

Report:

- Story identifier and final status.
- Acceptance criteria results.
- Implementation Task developer approval outcomes.
- Final verification checks and actual results.
- Failed, skipped, or unverified checks.
- Remaining risks or limitations.
- Final report location.
- Recommended next action, if any.

Stop after reporting the outcome.