# IPA-TMS Project Overview

## Project identity and scope

The selected project is `code/IPA-TMS` (repository-relative). The solution is named Ethrai TMS and combines a training-management web application with backend services for user and program administration, enrollment, trainee workflows, reference data, surveys, content, certificates, notifications, and audit logging. These domains are evidenced by the service names, frontend routes, and project structure; detailed business rules and the expansion of “IPA” have not been established here.

## Technology stack

- Backend: C# / .NET 10; `back-end/global.json` pins SDK `10.0.303`.
- Backend architecture: service-oriented microservices with Clean Architecture project boundaries (`Core`, `Infrastructure`, `SharedKernel`, and `Web`) in the service tree. The legacy `back-end/src/Ethrai.TMS.API` is identified by project instructions as superseded.
- Frontend: Angular 22, strict TypeScript, standalone/lazy-loaded routes, SCSS, RxJS 7, and ngx-translate. The UI also uses the `@taif/design-system` package.
- Persistence: PostgreSQL with EF Core where relational persistence is used. The database documentation identifies service-owned databases and service EF migrations as the schema source of truth.
- Supporting infrastructure: RabbitMQ/MassTransit, Redis, .NET Aspire for local orchestration, and Kubernetes/Kustomize deployment manifests.
- Authentication: IdentityServer uses OpenIddict; the Angular SPA uses `angular-auth-oidc-client` with authorization code flow.
- Observability: Serilog is configured in inspected service hosts. Aspire `ServiceDefaults` configures OpenTelemetry metrics/traces and exports via OTLP when `OTEL_EXPORTER_OTLP_ENDPOINT` is configured.

## Repository layout

- `back-end/src/Services/`: 14 service directories, each with service code and, where present, tests.
- `back-end/src/Shared/`: shared backend libraries for infrastructure, messaging, caching, HTTP clients, logging, models, and multi-tenancy.
- `back-end/ServiceDefaults/` and `back-end/Ethrai.TMSi.Template.AppHost/`: Aspire defaults and local orchestration.
- `front-end/src/app/`: application shell, core services/interceptors, shared UI adapters, and feature areas.
- `front-end/design-system/` and `front-end/public/`: design-system assets and public content, including localization resources.
- `database/`: reviewed SQL deltas and seed/permission scripts. EF migrations inside services remain the schema source of truth.
- `k8s/`, per-service deployment directories, and `back-end/pipelines/`: Kubernetes manifests and CI/deployment support.

## Build and test entry points

- Backend solution: `dotnet build back-end/Ethrai.TMS.slnx`.
- Backend tests: run the relevant service test project; available test projects include unit, integration, and functional tests. Functional tests use `WebApplicationFactory` per project guidance.
- Frontend build: run `npm run build` from `code/IPA-TMS/front-end`.
- Frontend tests: run `npm test -- --watch=false` from `code/IPA-TMS/front-end`; the Angular configuration uses Karma and `ChromeHeadlessStable`.
- Build and test commands were inspected but not executed during this initialization.

## Verified constraints and unresolved context

- Preserve existing misspellings in project paths such as `AuditTrielService`, `TempleteService`, and frontend `adminstrations`; project instructions identify them as load-bearing names.
- The root workspace `AGENTS.md`, `README.md`, `ai/current-state.md`, and `ai/project-context.md` were absent at initialization. Project-level instructions are present in `code/IPA-TMS/AGENTS.md`.
- Deployment documentation describes Azure Pipelines as primary while also documenting GitLab and GCP paths. The intended strategic deployment target requires owner confirmation.
- `DEPLOYMENT.md` documents 13 backend services and omits CertificateService, while project instructions and the source tree identify 14 service directories. Verify deployment coverage before treating the catalog as exhaustive.
- The business owner, precise user roles, and any third-party partner integrations are not fully established by the inspected materials. Tenant isolation has been confirmed by the user; current enforcement remains unverified.

## Confirmed user decision

- On 2026-10-25, the user confirmed Azure remains the primary deployment target and GCP is an alternative.
- On 2026-10-25, the user confirmed tenant isolation is required across all customer-owned data and services. This is a product/security requirement, not confirmation that the current code enforces it.

## Evidence reviewed

Workspace and project instructions, backend solution and SDK settings, Aspire AppHost and ServiceDefaults, representative ReferenceData and IdentityServer hosts, frontend package/config/routes, database README, deployment catalog and pipeline README, and applicable architecture, API, database, integration, caching, messaging, HTTP-client, exception-handling, and observability guidelines.
