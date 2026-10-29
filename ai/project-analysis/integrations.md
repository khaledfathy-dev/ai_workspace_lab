# IPA-TMS Integrations

## Internal identity and API clients

- **Identity**: IdentityServer is configured with OpenIddict. The Angular SPA uses `angular-auth-oidc-client`, authorization code flow, refresh tokens, and environment-provided authority/API URLs.
- **Service APIs**: the frontend calls backend APIs through shared Angular HTTP clients/interceptors. The frontend guide requires verifying endpoint response bodies and status/error behavior against the concrete backend contract.
- **Backend HTTP**: `ServiceDefaults` configures standard HTTP resilience and service discovery. Shared HTTP-client libraries exist; actual clients and service-to-service dependencies should be confirmed in the affected service.

## Messaging and cache

- **RabbitMQ/MassTransit**: a shared MassTransit extension configures a RabbitMQ host, correlation-ID publish filter, endpoint conventions, and optional EF Core outbox. Aspire AppHost starts RabbitMQ locally and connects several services. Not every service's use of messaging has been verified.
- **Redis**: shared caching infrastructure exists and Aspire AppHost starts Redis. Deployment documentation describes Redis as internal, non-persistent cache infrastructure in the dev environment; it is not a data store of record.

## Files and external content

FileMedia is a separate backend service and the Kubernetes deployment catalog describes a persistent media volume. External object-storage providers, third-party content APIs, and their credentials/configuration were not verified in this initialization.

## CI/CD and platform integrations

- Kubernetes/Kustomize manifests are present for the frontend and backend services.
- Azure Pipelines, GitLab CI, and GCP Cloud Build/deployment scripts are present. The user confirmed on 2026-10-25 that Azure remains primary and GCP is an alternative.
- Deployment docs describe registry, cluster, ingress, and secret prerequisites. Private credential files and secret values are intentionally excluded from this documentation.

## Observability

Aspire `ServiceDefaults` configures OpenTelemetry metrics and traces and enables OTLP export when an endpoint is configured. Service hosts use Serilog in inspected examples. The production collector, dashboards, alerting, and retention configuration were not verified.

## Unverified external integrations

The inspected architecture and configuration evidence did not establish a definitive list of third-party partner APIs. Do not assume one from integration guidelines alone; inspect service-specific clients/configuration when implementing an integration.
