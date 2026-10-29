# Implementation Plan — Remaining Frontend Blog Management Integration

## Story Information
- **Story ID:** us-62941 (frontend remainder)
- **Plan version:** 2 (replan)
- **Plan status:** Plan Ready / Pending Approval
- **Approval status:** Pending

## 1. Scope
Complete the frontend Blog management integration that remains after partial Task 02:
- Load authors from UserManagement and domains from ReferenceData
- Wire edit/reload from server
- Multipart uploads (cover + content files)
- Submit-for-review action
- 403 handling
- AR/EN translations
- Tests and build verification

## 2. Implementation Tasks

### Task 03 — Wire management form to live API
- Load authors/domains from real sources
- Populate existing Draft on edit
- Save calls draft endpoint, submit calls submit endpoint
- Handle 403 as access denial
- Status: Not Started

### Task 04 — Multipart uploads and translations
- Convert data-URL to multipart FileMedia
- Add AR/EN translation keys
- Preserve public mock consumer
- Status: Not Started

### Task 05 — Tests and verification
- Component/service tests
- Regression test for public Blog
- npm test + npm build
- Status: Not Started

## 3. Verification
- npm test -- --watch=false
- npm run build
- Manual end-to-end against Aspire

## 4. Risks
- Large UI scope; incremental delivery recommended
