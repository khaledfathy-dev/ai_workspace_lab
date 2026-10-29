---
name: approve-story-plan
description: Validate a Task-based user story plan against requirements and repository evidence, then record plan approval only after the developer explicitly confirms it.
---

# Approve Story Plan Skill

## 1. Objective

Validate the implementation plan for completeness and consistency, present it for developer approval, and record approval only after explicit confirmation. Validation is not approval.

This skill is reusable across projects, programming languages, frameworks, and AI agents.

## 2. Required Context

Before reviewing:

1. Read `AGENTS.md`, `README.md`, `development-guidelines/ai-workflow.md`, `ai/current-state.md`, and `ai/project-context.md`.
2. Read relevant project-analysis documents and applicable development guidelines.
3. Read the story's `user-story.md` and `implementation-plan.md`.
4. Read relevant story templates and any implementation, review, fix, or approval records needed to understand the current state.
5. Inspect repository evidence when needed to verify a material plan claim. Planning summaries are not proof of current source, configuration, test, or runtime state.

## 3. Identify the Story

Identify the target story using, in order:

1. An explicit story identifier or folder supplied by the user.
2. The active story clearly identified in the current workflow context.
3. The story plan selected by the user in the editor, if its identity is unambiguous.

If multiple stories are plausible or the target cannot be established, ask which story to approve. Do not choose a story based only on a stale selection or past conversation.

## 4. Check Approval State and Workflow Consistency

Before evaluating:

- Confirm the story directory contains the existing `user-story.md` and `implementation-plan.md`. Do not create a replacement or duplicate plan.
- Inspect the plan's `Plan status`, `Approval status`, and `Plan Approval` section, along with the story and `ai/current-state.md` statuses.
- If the plan is already approved, report that fact and stop without changing it or asking the user to approve it again.
- If approval fields conflict, approval evidence is ambiguous, or the story records implementation as started while plan approval remains pending, report the inconsistency and ask for resolution. Do not repair the history by assuming approval.
- Do not approve a plan that is still `Planning`, `Blocked`, or otherwise not presented as ready for approval. Recommend `/create-story-plan` to revise or complete it.

## 5. Evaluate the Plan

Review the plan against the user story, inspected repository evidence, project analysis, applicable guidelines, and the plan quality requirements. Verify that:

1. Every acceptance criterion maps to implementation tasks and meaningful verification.
2. Material defects, technical conflicts, missing dependencies, and runtime prerequisites have been investigated and have implementable proposed solutions.
3. Evidence and root causes support the decisions; facts, assumptions, recommendations, and unresolved questions are distinguished.
4. Technical assumptions are verified where repository inspection can establish them. Remaining assumptions and genuine business decisions are explicit and not represented as resolved.
5. Dependencies, runtime configuration, schema or other environment setup, deployment/migration prerequisites, and required test infrastructure are explicit.
6. Task boundaries are coherent rather than file-sized or arbitrary; scope, affected areas, risks, mitigations, tests, dependencies, completion criteria, and estimates (when evidence supports them) are clear enough to implement without redesign.
7. Shared infrastructure changes include assessment of other consumers and regression-test criteria.
8. Functional tests exercise the real use-case request handler unless a test explicitly exists only to verify an isolated HTTP boundary.
9. Persistence verification uses the intended provider and covers schema availability plus save/reload behavior; an in-memory test alone is insufficient evidence of provider persistence.
10. The plan follows the project's architecture and mandatory guidelines or documents a justified, implementable deviation.
11. No blocking finding, unresolved business decision, or runtime prerequisite is concealed by a limitation statement, confirmation request, or incomplete verification plan.

Inspect only as much source, configuration, tests, and documentation as needed to support the approval decision. Do not run implementation steps, migrations, package installation, or application code.

## 6. If the Plan Does Not Pass

Do not modify the plan or approval records. Report:

- The story identifier and plan path.
- Each specific blocking or material finding, with evidence and impact.
- Any non-blocking observations separately.
- The recommended next action: use `/create-story-plan` to revise the existing plan.

Do not ask the user to confirm a plan that has not passed review. Stop after reporting the findings.

### Bounded revision cycle

Track unsuccessful approval reviews in the plan's `Plan revision/re-review cycles in current approval attempt` field and `ai/current-state.md`. Allow at most two plan revisions and corresponding re-reviews for the same approval attempt. If the revised plan still fails after the second re-review, stop and ask the developer to resolve the remaining findings or choose a different approach. Never approve a failing plan or revise it silently from this skill.

## 7. Request Explicit Approval

If the plan passes:

1. Summarize the story, proposed scope, implementation tasks, material decisions, dependencies, and verification strategy.
2. State clearly that the plan is not yet approved and that no implementation will occur as part of this approval operation.
3. Ask the user for explicit approval, preferably with the ask-user tool and a clear choice between approving the named plan and not approving it.
4. Treat only an unambiguous affirmative confirmation that identifies the plan/story as approval. Silence, continuing the conversation, asking a question, or saying only that the plan looks good is not approval.
5. If the user declines, requests changes, or gives an ambiguous response, do not update approval fields. Report the outcome and recommend `/create-story-plan` if revisions are requested.

## 8. Record Confirmed Approval

Only after receiving explicit approval in the current approval process:

1. Reconfirm that the plan has not changed since it was reviewed. If it changed materially, review it again before recording approval.
2. Update the existing plan's `Plan Approval` section in place:
   - `Approved by: User`
   - `Approval date: YYYY-MM-DD`, using the actual current date at the time approval is recorded.
   - `Approval reference:` a concise, truthful reference to the explicit user confirmation, such as the confirmation text and the story identifier. Never fabricate a message id or quote.
3. Set `Plan status: Plan Ready` and `Approval status: Approved` in the existing story-information metadata. If the plan-status field already uses `Plan Ready / Pending Approval`, change only that field to `Plan Ready`; approval is tracked separately. Do not create a new plan or story status name.
4. Update `ai/current-state.md` with the recorded approval state, story status `Plan Approved / Pending Implementation`, and next permitted action. Use this status until implementation actually begins, then set the story to `Implementation In Progress`.
5. Preserve prior plan decisions and change history. Append a dated approval entry; do not replace existing history.
6. If the plan's approval section or status fields do not allow a truthful update without overwriting conflicting evidence, stop and report the problem instead of forcing a change.

## 9. Restrictions

- Starting `/approve-story-plan` begins review; it does not itself grant approval.
- Never infer or fabricate user approval.
- Never record approval before explicit confirmation.
- Do not modify application source code or implement any plan task.
- Do not install packages, change runtime configuration, or run database migrations.
- Do not commit, push, merge, create a pull request, or deploy.
- Do not silently rewrite an already approved plan.
- Do not create duplicate plans or erase approval/change history.
- Do not continue to implementation after recording approval.

## 10. Completion Report

Report:

- Story identifier and plan location.
- Review outcome: findings reported, awaiting confirmation, declined, or approved and recorded.
- For approval, the approval date, approver, approval reference, and resulting workflow state.
- Files changed.
- Confirmation that no implementation was performed.
- The next permitted action.

Then stop and wait for the user's next instruction.
