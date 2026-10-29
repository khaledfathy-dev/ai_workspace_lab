# IPA-TMS Architecture

## System shape

IPA-TMS is organized as an Angular single-page application and a collection of independently hosted .NET services. The backend tree uses per-service projects for core use cases, infrastructure, shared-kernel concerns, and web/API hosts, with exceptions such as IdentityServer contracts. The legacy monolithic `Ethrai.TMS.API` is marked superseded by project instructions and should not be extended for new functionality.

## Main components and boundaries

- **Angular frontend** (`front-end/`): standalone Angular application, lazy feature routes, core HTTP/auth/language services, and shared UI/design-system components. Frontend route guards protect the authenticated application shell.
- **Backend services** (`back-end/src/Services/`): service-specific web hosts and business capabilities. Backend controllers expose service APIs; the project guide says controllers generally map application `Result` values to direct HTTP response bodies rather than a common envelope.
- **Shared backend libraries** (`back-end/src/Shared/`): reusable infrastructure and cross-cutting concerns, including messaging, caching, HTTP clients, logging, and multi-tenancy.
- **IdentityServer**: OpenIddict-based identity host; the SPA is configured for OIDC authorization code flow with refresh-token renewal.
- **Persistence**: service-owned PostgreSQL databases, accessed through EF Core in infrastructure projects. EF migrations are documented as authoritative for schema.
- **Local orchestration**: Aspire AppHost starts a subset of services and RabbitMQ/Redis. The checked-in AppHost currently references ReferenceData, UserManagement, AuditTrail, IdentityServer, Survey, TrainingProgram, FileMedia, Notification, an event publisher, and a smoke-test project; it does not start every service.
- **Deployment**: service/frontend Kubernetes manifests use Kustomize bases and overlays. The dev ingress fronts the SPA and selected APIs; backend service-to-service URLs are configured for in-cluster use.
- **Tenant isolation requirement**: the user confirmed that all customer-owned data and services require tenant isolation. Shared claim-based tenant middleware/context exists, but its service-wide wiring and enforcement were not verified; do not assume the requirement is currently satisfied.

## Request and data flow

1. Users access the Angular SPA, which loads feature modules through lazy routes.
2. The SPA authenticates through IdentityServer using OIDC and sends API requests through its configured API clients/interceptors.
3. Requests reach a backend service through the configured ingress or local development host.
4. A service handles application logic within its own boundary and accesses its owned persistence through infrastructure code. The inspected ReferenceData host registers Npgsql-backed EF contexts.
5. Where asynchronous inter-service work is configured, shared MassTransit infrastructure connects to RabbitMQ and supports EF Core outbox setup. Redis is used as shared cache infrastructure where registered; it is not a system of record.
6. FileMedia provides file/media handling and is configured with persistent media storage in Kubernetes deployment documentation.

The flow above describes intended boundaries and inspected framework setup. It does not assert that every service uses every shared infrastructure component.

## Cross-cutting concerns

- Backend hosts use ASP.NET Core, Autofac, Serilog, API versioning, validation, and centralized exception handling in the documented/inspected patterns.
- Localization is supported in English and Arabic; backend request culture uses `Accept-Language`, and frontend translations are stored under `public/assets/i18n/`.
- Correlation identifiers are represented in shared messaging/HTTP client infrastructure and service middleware patterns.
- `ServiceDefaults` configures OpenTelemetry instrumentation and optional OTLP export. Deployment of a telemetry collector/backend is not verified.
- The frontend centrally configures HTTP interceptors for auth, language, routing, loading, error feedback, and API feedback.

## Deployment and environment caveats

- User confirmed on 2026-10-25 that Azure remains the primary deployment target and GCP is an alternative; GitLab and GCP-related paths remain present in the repository.
- User confirmed on 2026-10-25 that tenant isolation is required across all customer-owned data and services. Current end-to-end enforcement remains unverified.
- `DEPLOYMENT.md` reports only a dev ingress and identifies missing ingress routes for several services; it also reports infrastructure/resource gaps. Treat this as project-documented state and verify against manifests before deployment decisions.
- The deployment catalog lists 13 backend services while the source tree contains 14 service directories, including CertificateService. Its current completeness is unresolved.
- Aspire local orchestration starts only a subset of the backend services and does not establish full end-to-end local coverage.

## Evidence and limits

Evidence includes `AppHost.cs`, `ServiceDefaults/Extensions.cs`, representative ReferenceData and IdentityServer host code, Angular app configuration and routes, service tree/project guidance, and deployment/database documentation. This is a repository-level architecture summary, not a complete dependency graph or runtime deployment verification.
