# Implementation Plan

## Story Information

- **Story ID:** us-62941
- **Story file:** `user-story.md`
- **Project source path:** `code/IPA-TMS`
- **Plan version:** 1
- **Plan revision/re-review cycles in current approval attempt:** 0 of 2
- **Plan status:** Plan Ready
- **Approval status:** Approved
- **Last updated:** 2026-10-27

## 1. Scope and Approach

Deliver persistent Blog management in the existing Angular SPA and Enrichment Content service. Keep the management workflow backed by a dedicated Blog API and PostgreSQL aggregate, while retaining FileMedia as the byte store for the required cover image and uploaded image/video blocks. Saving creates or updates a `Draft`; a distinct submit action transitions a saved draft to `InReview`.

Use a distinct Blog aggregate within Enrichment Content rather than extending the existing Enrichment aggregate. The existing aggregate and use cases model enrichment publication/approval and do not contain Blog's bilingual descriptions, author, domain selections, ordered mixed content, or `Draft` to `InReview` lifecycle. A new microservice would add deployment and operational overhead without a demonstrated boundary need.

Protect management endpoints with the existing OpenIddict token-validation and permission-policy patterns. Register `Blogs.Manage` in UserManagement and grant it only to the Content Management Officer role. The role code is not present in the inspected repository data; seed logic must resolve the role unambiguously by its exact English display name, make no grant if zero or multiple roles match, and report that condition explicitly.

**Assumption requiring approval with this plan:** Blogs are platform-owned, globally managed content rather than tenant-owned records. The story has no tenant selector, and the feature is a public Blog; therefore the Blog aggregate will not implement tenant scoping. If the business intends tenant-specific blogs, stop before implementation and revise the data model, authorization, and API scope.

The existing public Blog experience also consumes `TheBlogService` mock state. Keep that public consumer and its behavior out of this management API replacement; use a management-specific API service so unrelated public content is not silently removed or changed. A review notification, reviewer assignment, or approval decision is not specified; submission in this story means the persisted status transition to `InReview` only.

## 2. Requirements Mapping

| Acceptance Criterion | Planned Tasks | Verification |
|---|---|---|
| AC-01 — An authorized content manager can open Blog management and start adding a Blog. | Task 01, Task 02 | Endpoint authorization tests verify that permission-bearing requests can use management APIs and authenticated requests without `Blogs.Manage` are denied; UI handles a 403 without exposing data. |
| AC-02 — Author is selected from users; main and one-or-more secondary domains are selected from domains. | Task 02 | Component/API tests verify user-management and reference-data calls populate the options, selection values are submitted, and loading/error states are visible. |
| AC-03 — Required Arabic and English names, cover image, author, domains, short and long descriptions, and at least one text/image/video content item are validated. | Task 01, Task 02 | API validation tests reject missing/invalid required values; UI tests verify required-field and content-list validation; valid bilingual values round-trip through PostgreSQL. |
| AC-04 — Saving a valid Blog persists it as a Draft and management views can reload it. | Task 01, Task 02 | Exercise the real request handlers through HTTP against PostgreSQL, apply the EF migration, then read the saved Blog back; UI tests verify Save calls the draft API and reflects the persisted response. |
| AC-05 — Submitting a saved Draft is a separate action that transitions it to InReview; Save alone never submits it. | Task 01, Task 02 | Handler/API tests prove Draft-to-InReview transition and reject invalid/repeated transitions; UI tests prove Save leaves Draft and only the separate submit action calls the transition endpoint. |
| AC-06 — Uploaded media is retained as FileMedia references, not browser data URLs, and the existing public Blog consumer is not regressed. | Task 01, Task 02 | Functional upload tests verify file IDs are persisted and reloadable; frontend regression tests preserve the public mock consumer and assert management code does not persist data URLs. |

## 3. Implementation Tasks

### Task 01 — Add persistent and authorized Blog API

- **Objective:** Provide the PostgreSQL-backed Blog capability, media handling, workflow transition, and `Blogs.Manage` authorization required by the management UI.
- **Scope:** Enrichment Content service, UserManagement permission registration/grant, local Aspire wiring, Enrichment Content runtime configuration, EF migration, and backend functional/security tests. Do not alter unrelated Enrichment behavior.
- **Requirements addressed:** AC-01, AC-03, AC-04, AC-05, AC-06.
- **Affected areas/components (files where known):**
  - `back-end/src/Services/EnrichmentContentService/src/Ethrai.TMS.EnrichmentContent.Core/` — Blog aggregate, validation, DTOs, use cases, and handler.
  - `back-end/src/Services/EnrichmentContentService/src/Ethrai.TMS.EnrichmentContent.Infrastructure/` — EF mapping, repositories, DbContext, and migration.
  - `back-end/src/Services/EnrichmentContentService/src/Ethrai.TMS.EnrichmentContent.Web/` — Blog controller, multipart request models, authorization, dependency registration, and configuration.
  - `back-end/src/Services/UserManagementService/src/Ethrai.TMS.UserManagement.Web/Seeding/` and its startup registration — idempotent permission and exact-role grant.
  - `back-end/src/Services/EnrichmentContentService/tests/` — new functional test project; no service-specific test project currently exists.
  - `back-end/Ethrai.TMSi.Template.AppHost/AppHost.cs` and `Ethrai.TMSi.Template.AppHost/Ethrai.TMS.AppHost.csproj`.
  - Enrichment Content Kubernetes base/overlays for IdentityServer, database, and FileMedia settings; existing ingress already routes this service.
- **Expected changes:**
  1. Add a separate Blog aggregate with bilingual names and short/long descriptions, author ID, main-domain ID, secondary-domain IDs, cover-file ID, ordered text/image/video blocks, and persisted `Draft`/`InReview` status. Store IDs for other services without cross-database foreign keys.
  2. Add create/update-draft, get/list-management, and submit-for-review use cases and API contracts. Validate required fields and at least one supported content block in the handler boundary as well as the UI. Save and submit must be distinct operations; submit may transition only a persisted Draft.
  3. Upload cover and image/video files as multipart data through the existing `IFileMediaClient` integration. Persist only returned FileMedia IDs and the metadata needed to reconstruct the ordered content. Reuse established file validation where applicable; do not introduce new size/type business rules unsupported by the story. Compensate for newly uploaded files if the database save fails, and do not remove replaced files until the database update succeeds.
  4. Add `Blogs.Manage` to the UserManagement permission catalog and idempotently associate it only with the unique role whose exact English display name is `Content Management Officer`. Do not grant to administrator or broad role sets. If the target role is absent or ambiguous, do not grant access and emit an explicit actionable error.
  5. Configure Enrichment Content token validation from `IdentityUrl`, shared permission-policy registration, and endpoint-level `Blogs.Manage` enforcement. Preserve anonymous public-read behavior for existing Enrichment endpoints. Add the required non-secret configuration to service deployment overlays.
  6. Add Enrichment Content and its PostgreSQL database to Aspire local orchestration, with IdentityServer and FileMedia endpoint configuration, so the API has its required runtime dependencies locally.
  7. Add a real PostgreSQL functional test project. Apply the actual EF migration to a PostgreSQL Testcontainer and exercise Blog HTTP handlers through `WebApplicationFactory`; include reload/save, field validation, upload/file-ID persistence, transition behavior, and 401/403/authorized cases. Test authorization with a controlled test authentication scheme while still invoking the actual endpoint and use case.
- **Relevant development guidelines:** `development-guidelines/ai-workflow.md`; `development-guidelines/architecture-guidelines.md`; `development-guidelines/api-development-guidelines.md`; `development-guidelines/database-guidelines.md`; `development-guidelines/exception-handling-guidelines.md`; `development-guidelines/integration-development-guidelines.md`; `development-guidelines/http-client-guidelines.md`; `code/IPA-TMS/AGENTS.md`.
- **Implementation approach:** Follow the service's Core/Infrastructure/Web boundaries and existing Result-to-HTTP contracts. Use EF Core migrations as the schema source of truth and the existing FileMedia client rather than storing bytes or data URLs in PostgreSQL. Register authentication middleware before authorization and use the shared singular `permission` claim policy handler. Keep permission data/grant in UserManagement, where roles and permissions are owned.
- **Execution order:** First.
- **Dependencies:** None.
- **Risks and considerations:**
  - Cross-service user/domain IDs are not relational foreign keys; the API should validate shape and requiredness, while option sources remain UserManagement and ReferenceData. Avoid new synchronous service-to-service validation unless existing service contracts establish it as required.
  - PostgreSQL and FileMedia cannot participate in one transaction. Compensating deletion of newly uploaded files is required on a failed DB write; deletion of old files must follow a successful update.
  - Token validation and permission policy registration are currently absent from Enrichment Content. The implementation must not leave the API with a fallback authentication scheme in deployed environments.
  - The exact CMO role display name must match exactly one persisted role; the safe failure behavior prevents accidental broad grants but requires deployment verification.
  - AppHost currently omits this service and has no active PostgreSQL resource for it; wiring must include valid local database, IdentityServer, and FileMedia endpoints.
  - Blog records are planned as non-tenant platform content under the stated assumption. A contrary business decision requires replanning before implementation.
- **Tests and verification steps:**
  - Build the Enrichment Content Web project and UserManagement Web project.
  - Run the new functional tests against PostgreSQL Testcontainers; apply the real EF migration and verify schema creation plus save/reload through the actual API request path.
  - Verify missing required fields and empty content are rejected; verify upload metadata and ordered blocks round-trip.
  - Verify Save creates/updates Draft, submit changes it to InReview, and invalid transitions fail without changing persisted state.
  - Verify unauthenticated requests receive 401, authenticated requests without `Blogs.Manage` receive 403, and permitted requests reach the real handler.
  - Build Aspire AppHost; render affected Kubernetes overlays and run `.\scripts\check-services-and-routes.ps1` from `code/IPA-TMS` because a controller is added.
- **Completion criteria:** Blog API and schema are ready for UI integration; a real PostgreSQL migration/save/reload test passes; media bytes remain in FileMedia; only the intended role receives `Blogs.Manage`; 401/403/authorized behavior is verified; runtime configuration is present for Aspire and deployment; no unrelated Enrichment endpoint behavior changes.
- **Effort estimate:** Not estimated.
- **Status:** Developer Approved
- **Developer decision/feedback:** Approved by developer on 2026-10-27
- **Optional advisory report(s):** None

### Task 02 — Connect Blog management UI to the API

- **Objective:** Replace management-only mock persistence with the authorized Blog API while preserving the public Blog consumer and the approved Draft-then-submit workflow.
- **Scope:** Blog management list/form, management API client/models, author/domain option loading, media upload UX, translations, and focused frontend tests. Do not change public Blog mock data or unrelated publication behavior.
- **Requirements addressed:** AC-01, AC-02, AC-03, AC-04, AC-05, AC-06.
- **Affected areas/components (files where known):**
  - `front-end/src/app/features/the-blog/management/the-blog-form/` including `the-blog-form.ts`, `the-blog-form.html`, `the-blog-form.config.ts`, and basic-info/content tabs.
  - `front-end/src/app/features/the-blog/management/the-blog-list/`.
  - A management-specific API service/model under `front-end/src/app/features/the-blog/management/` or the feature's established service location.
  - `front-end/src/app/features/the-blog/the-blog.models.ts` only as needed for shared model compatibility; keep `the-blog.service.ts` public mock behavior intact.
  - Existing Users and Main/Secondary Domains API clients; the authenticated Blog management routes.
  - `front-end/src/app/core/` or environment API configuration only if required by the established API client pattern.
  - `front-end/public/assets/i18n/ar.json` and `en.json`.
- **Expected changes:**
  1. Introduce a typed management-specific API client for create/update Draft, list/get, multipart media handling, and submit-for-review calls using the configured Enrichment Content base URL and existing API feedback/interceptor conventions.
  2. Load author choices from the existing UserManagement users API (including its pagination/search contract as needed); retain the existing ReferenceData main/secondary-domain calls. Display loading, empty, and recoverable error states rather than falling back silently to mock authors or domains.
  3. Load an existing Draft from the server for editing; populate bilingual fields and ordered content block references. Keep form controls consistent with the server DTO and validation rules.
  4. Make Save call only the draft endpoint and display the resulting persisted status. Add a distinct submit-for-review action for a successfully saved Draft; only a successful server response updates the UI to `InReview`.
  5. Send the cover image and content images/videos as multipart file data. Remove data-URL persistence and use FileMedia IDs/URLs returned by the API. Preserve the existing supported PDF behavior only if needed for existing management/public model compatibility; do not broaden the story's required block types.
  6. Keep the route within the authenticated application shell, handle API 403 responses as access denial, and rely on the backend `Blogs.Manage` policy as the authorization boundary; do not infer permissions from the frontend role claim. Do not replace the shared `TheBlogService` used by the public Blog components.
  7. Add matching Arabic and English translations for the new action, statuses, validation, loading, empty, and error states.
- **Relevant development guidelines:** `code/IPA-TMS/front-end/AGENTS.md` (especially API/feedback, translation, and auth guidance); `development-guidelines/ai-workflow.md`; `development-guidelines/api-development-guidelines.md`; `development-guidelines/integration-development-guidelines.md`.
- **Implementation approach:** Reuse `ApiClientService`, existing typed API-service conventions, `MainDomainsService`, and the UserManagement users endpoint. Keep server state authoritative after writes and avoid optimistic status changes. Preserve the shared public mock consumer by containing live API wiring to management components.
- **Execution order:** Second, after Task 01 fixes and verifies API DTOs, route, permission, and upload contract.
- **Dependencies:** Task 01.
- **Risks and considerations:**
  - The management and public Blog areas currently share an in-memory service; globally replacing it could regress public mock content. Keep the new API client scoped to management and add a regression test for the public consumer.
  - Existing form upload behavior reads files into data URLs. Ensure no form submission or shared model continues persisting those values after migration to multipart uploads.
  - The UI must not show `InReview` until the server confirms the transition; API errors must remain visible through existing interceptor/component feedback patterns without duplicate toasts.
  - A users endpoint may be paginated; the selection must use its real response contract and must not silently truncate the list.
- **Tests and verification steps:**
  - Add focused Angular component/service tests for author/domain loading, required validation, Draft save behavior, separate submit behavior, API failures, and server-reloaded content.
  - Add a regression test confirming public Blog components still receive their existing mock data and behavior.
  - Run the focused Blog tests, then `npm test -- --watch=false` and `npm run build` from `code/IPA-TMS/front-end`.
  - Verify both locale JSON files contain matching keys and a denied management API response is presented as an access error without leaking Blog data.
  - Perform a manual end-to-end check against the local Aspire/API environment: create, reload, edit Draft, upload cover and mixed content, save without submission, submit separately, and inspect persisted status.
- **Completion criteria:** Management users and domains are loaded from their real sources; valid bilingual Blog drafts and uploaded media persist and reload; Save leaves the Blog in Draft; a separate action transitions it to InReview; unauthorized users cannot use management endpoints; Arabic/English UI states are present; public Blog behavior is unchanged; frontend tests and build pass.
- **Effort estimate:** Not estimated.
- **Status:** Fixes Applied / Awaiting Developer Review
- **Developer decision/feedback:** On 2026-10-28, the user clarified that Task 02 is incomplete and authorized continuing under approved plan version 1. This supersedes the contradictory prior status entry; the earlier entry is preserved in the status history below.
- **Optional advisory review:** The 2026-10-28 advisory findings REV-001–REV-003 are addressed in the fix record below. The earlier persisted review report remains unchanged.

#### Task 02 Status History

- Previously recorded as `Developer Approved` with feedback `Awaiting implementation`, conflicting with `ai/current-state.md` and the implementation/review reports. On 2026-10-28, the user directed that Task 02 be treated as incomplete and authorized continuation under approved version 1.

#### Task 02 Implementation Record — 2026-10-28

- Replaced management-only mock list data with the paginated Blog API, Draft/InReview status display, Draft-only editing, and a distinct submit-for-review action that updates the list only from the server response.
- Added multipart draft creation/update with ordered upload-index mapping, retained FileMedia IDs for existing content, existing-draft reload/reset, required cover selection, and visible loading, lookup, API, and 403 access-denied states.
- Loaded author options from all UserManagement pages and main/secondary domains from ReferenceData; kept the public `TheBlogService` consumer unchanged. Added matching Arabic/English UI strings.
- Added focused API-service, form, list, lookup-pagination, and public-mock regression tests.
- **Verification:** Angular production build passed; 12 focused Blog tests passed using a temporary isolated test configuration, which was removed after the run. Both locale JSON files parsed and all new workflow keys were present in each locale.
- **Full frontend test suite:** `npm test -- --watch=false --browsers=ChromeHeadless` could not compile because existing unrelated test files have TypeScript errors in auth, profile, competences, regions/cities, and surveys. No unrelated tests were changed.
- **Manual end-to-end:** Not performed against a running Aspire/API environment. This remains a verification item after developer review.
- **Media cleanup follow-up (user-authorized 2026-10-28):** `SaveBlogDraftUseCase` now captures prior cover/content file IDs and, after a successful database write, deletes only IDs no longer referenced by the updated Blog. Per-file delete failures are logged and do not turn a persisted save into a reported failure. A failed database update still follows the existing staged-upload compensation path and does not delete the previous media. Cleanup retries/monitoring are not implemented.
- **Checkpoint:** Awaiting explicit developer review. Do not start final story verification until Task 02 is approved.

#### Task 02 Fix Record — 2026-10-28 (REV-001–REV-003)

- **REV-001 — Image upload contract:** Updated cover and content image selection to PNG/JPEG and a 2 MiB limit, matching the backend validator. Added a frontend regression assertion for both configured fields.
- **REV-002 — Direct edit route:** The form now refuses to display editable controls when a loaded Blog is not `Draft`, shows a translated explanation and Back to blogs action, and has a test for loading an `InReview` Blog directly.
- **REV-003 — Media cleanup coverage:** Added an Enrichment Content unit-test project with tests proving that a successful update removes obsolete media but retains referenced files, and a failed database update compensates a newly uploaded cover without deleting prior files.
- **Files changed:** Blog form configuration/component/template/spec, Arabic and English translations, new Enrichment Content unit-test project/spec, and `back-end/Ethrai.TMS.slnx`.
- **Verification:** `npm test -- --watch=false --browsers=ChromeHeadlessStable --ts-config=tsconfig.blog.spec.json --include='src/app/features/the-blog/**/*.spec.ts'` passed (14 tests); temporary test config removed. `npm run build` passed with existing bundle/style budget and CommonJS warnings. `dotnet test .\src\Services\EnrichmentContentService\tests\Ethrai.TMS.EnrichmentContent.UnitTests\Ethrai.TMS.EnrichmentContent.UnitTests.csproj` passed (2 tests). Enrichment Content Web `dotnet build` passed (0 warnings, 0 errors). Both locale JSON files parsed and include both new keys; `git diff --check` passed.
- **Outstanding:** Manual end-to-end testing against Aspire/API was not performed. The full frontend suite remains blocked by previously recorded unrelated TypeScript failures.
- **Status:** Fixes Applied / Awaiting Developer Review. No approval is implied; developer review is the next step.

## 4. Verification Strategy

- **Build checks (required):** Enrichment Content Web and UserManagement Web builds; Aspire AppHost build; frontend production build.
- **Automated tests (required):** New Enrichment Content functional tests against real PostgreSQL/Testcontainers; focused Blog Angular tests; full service/frontend test commands if targeted tests pass.
- **Persistence and migration (required):** Apply the generated EF migration to the functional-test PostgreSQL instance; verify a Blog survives save, a separate request reloads all localized fields/ordered blocks/status, and invalid updates do not partially change state.
- **API and authorization (required):** Exercise actual HTTP handlers/use cases. Verify 401 without authentication, 403 without `Blogs.Manage`, success for the intended permission claim, and the exact-role grant seed's idempotence/safe failure behavior.
- **Media integration (required):** Test multipart upload through the FileMedia client boundary, persisted file identifiers, and compensation behavior on database failure. Avoid asserting database persistence through EF InMemory.
- **Configuration/deployment checks (required):** Build Aspire AppHost; render changed Kustomize overlays; run `.\scripts\check-services-and-routes.ps1` from `code/IPA-TMS`. Confirm no secret values are added to source-controlled configuration.
- **UI regression (required):** Verify management behavior end-to-end and preserve public Blog mock behavior. Check Arabic and English translations and screen states.
- **Optional recommendation:** Capture a short manual Figma comparison for the Blog management screen after functional behavior is wired; visual fidelity is not a separate acceptance criterion in the saved story.

## 5. Risks and Open Questions

- **Tenant ownership assumption:** Plan treats Blogs as platform-owned because the story has no tenant selection and the feature is public. This assumption must be accepted with the plan; if Blogs are tenant-owned, do not implement this plan unchanged.
- **Role row availability:** No canonical role code or matching role seed was found in the repository. The grant must match exactly one existing role by the English name `Content Management Officer`; fail without granting when the match is absent or ambiguous.
- **Review routing:** No reviewer assignment, notification, or approval decision is specified by the story or current Blog UI. This plan implements the persisted `InReview` status transition only; expand scope only if the business later defines additional review routing.
- **File policy:** Reuse existing FileMedia file rules where compatible. The story does not define Blog-specific file sizes or extensions; any new limits require a separate business decision.
- **Deployment database:** The Enrichment Content service needs a real PostgreSQL connection in local Aspire and the deployed runtime. This plan wires the local resource and existing deployment configuration pattern; migration execution follows the established EF migration script/pipeline, not an ad hoc schema change.

## 6. Plan Approval

- **Approved by:** User
- **Approval date:** 2026-10-27
- **Approval reference:** User explicitly approved us-62941 plan version 1 as written, including the platform-owned Blog assumption and optional Figma visual comparison.

Plan approval does not authorize implementation by itself. Implementation must follow a separate explicit task instruction.

## 7. Change History

- 2026-10-27 — Initial version 1 plan. Recorded the approved Draft-then-submit workflow and `Blogs.Manage` assignment, and mapped repository evidence into backend/API/security/persistence and frontend integration Tasks.
- 2026-10-27 — User explicitly approved plan version 1 as written, including the platform-owned/non-tenant assumption and optional Figma visual comparison.
