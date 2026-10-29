# Project Context

## Selected project

- Path: `code/IPA-TMS` (relative to the workspace root).
- Identity: Ethrai TMS, an Angular SPA with .NET 10 backend services for training-management domains.

## Verified facts

- Backend solution: `back-end/Ethrai.TMS.slnx`; SDK pinned to `10.0.303`.
- Backend source has 14 service directories and shared libraries, using service boundaries and Clean Architecture-style projects; legacy `Ethrai.TMS.API` is superseded.
- Frontend: Angular 22, standalone/lazy routes, strict TypeScript, RxJS, ngx-translate, and the `@taif/design-system` package.
- PostgreSQL/EF Core are used in inspected services; project database documentation specifies database-per-service and EF migrations as schema source of truth.
- IdentityServer uses OpenIddict; frontend uses OIDC authorization code flow.
- RabbitMQ/MassTransit and Redis are represented in shared libraries and Aspire local orchestration.
- Kubernetes/Kustomize, Azure Pipelines, GitLab CI, and GCP deployment support are present.

## User decisions and unresolved questions

- Confirmed user decision (2026-10-25): Azure remains the strategic primary deployment target; GCP is an alternative.
- Confirmed user decision (2026-10-25): tenant isolation is required across all customer-owned data and services. The shared multi-tenancy library contains claim-based middleware/context, but service-wide enforcement was not verified and must not be assumed.
- Open: business owner, exact user roles, and definitive external partner integrations need owner confirmation or targeted source investigation.
- Documentation discrepancy: `DEPLOYMENT.md` catalogs 13 backend services, whereas source/project instructions identify 14, including CertificateService.

## Workspace context gaps

At initialization, workspace-root `AGENTS.md`, `README.md`, `ai/current-state.md`, and `ai/project-context.md` did not exist. Project-specific instructions are available in `code/IPA-TMS/AGENTS.md` and `code/IPA-TMS/front-end/AGENTS.md`.

## Workflow

Workspace initialization is complete. No application source, database schema, dependencies, or deployment configuration has been changed. Builds and tests have not been run.
