# IPA-TMS Modules

## Backend services

The source tree and project instructions identify the following 14 service directories. Names below describe the evidenced service/domain label; detailed behavior must be confirmed from the relevant service implementation before changing it.

| Service directory | Evidenced responsibility |
|---|---|
| `AuditTrielService` | Audit trail capability; referenced as an audit sink by UserManagement in the Aspire AppHost. |
| `CertificateService` | Certificate and certificate-template capability. |
| `EnrichmentContentService` | Enrichment content capability; has its own infrastructure and EF context. |
| `EnrollmentService` | Enrollment capability. |
| `FileMediaService` | File/media and SCORM handling; configured with persistent media storage in deployment documentation. |
| `IdentityServer` | Identity and OpenID Connect endpoints, implemented with OpenIddict. |
| `NotificationService` | Notification capability; included in local Aspire orchestration. |
| `ReferenceDataService` | Reference data capability, including domain/lookup use cases. |
| `SurveyService` | Survey capability; included in local Aspire orchestration. |
| `TargetGroupService` | Target-group capability. |
| `TempleteService` | Template capability; retain the established directory spelling. |
| `TraineeService` | Trainee capability. |
| `TrainingProgramService` | Training-program capability; included in local Aspire orchestration. |
| `UserManagementService` | User and role management capability; included in local Aspire orchestration and connected to the audit sink. |

The module descriptions are intentionally high-level and derived from names and repository references; they are not a substitute for reviewing a service's use cases and API contracts.

## Shared backend modules

`back-end/src/Shared/` contains reusable projects for shared core/infrastructure/models, messaging, caching, HTTP clients, logging, and multi-tenancy. Individual service registration and actual use should be verified before relying on a shared capability.

## Frontend areas

The frontend is organized into core application services/layout, shared components and models, a design system, and feature routes. The visible route tree includes authentication, public portal/website pages, dashboard/profile, administration, basic/reference data, training programs/paths, certificate templates, surveys, enrichment content, trainee pages, and target audiences. Preserve the existing `adminstrations` spelling.

## Tests

Backend service trees include xUnit unit, integration, and functional test projects where present; not every service has every test type. Frontend tests use Jasmine/Karma. For any change, inspect and run the closest relevant test project/spec rather than assuming uniform coverage across modules.
