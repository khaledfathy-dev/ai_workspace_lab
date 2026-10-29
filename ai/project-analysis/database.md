# IPA-TMS Database and Persistence

## Persistence model

The project database README documents a database-per-service model using PostgreSQL. EF Core migrations within each service are the schema source of truth; the SQL files under `database/` are reviewed deployment/audit deltas and must not be hand-edited or applied on top of a database already migrated by EF.

The inspected ReferenceData startup registers PostgreSQL-backed EF Core command and read contexts via Npgsql. IdentityServer registers its own Npgsql-backed `AuthDbContext` and applies pending migrations during host startup. The EnrichmentContent service has an `EnrichmentContentDbContext`. This confirms EF/PostgreSQL use in representative services but does not establish the complete schema or migration state of every service.

## Data ownership and service boundaries

Database ownership follows service boundaries. The database guideline states that services must not read another service's database; cross-service communication is through HTTP APIs or RabbitMQ events. The actual service-to-service calls and data ownership of each entity should be checked in that service's code before making changes.

## Schema management

- EF Core code-first migrations live in service infrastructure projects.
- `database/` contains versioned SQL deltas, seeds, and permission scripts.
- The database README explicitly cautions against applying reviewed SQL deltas to a database already updated by EF because both mechanisms maintain EF migration history.
- A repository script, `scripts/migrate-databases.ps1`, is documented for applying EF migrations and uses private runtime environment files; do not copy secret values into project documentation.

## File storage

Project deployment documentation identifies FileMedia as the service that mounts a persistent media volume in Kubernetes. The broader database guidance says large files should be stored outside PostgreSQL with metadata persisted relationally. Exact storage providers and metadata models are service-specific and remain to be confirmed in code.

## Known limits and open questions

- This summary does not enumerate per-service tables/entities or migration versions.
- The database README's listed SQL-delta coverage is a snapshot and does not prove the live database state.
- How the shared multi-tenancy library is used for data ownership/isolation is not confirmed.
- The shared multi-tenancy project contains claim-based tenant middleware and scoped tenant context. The service tree contains a direct `TenantContext` registration in NotificationService, but no use of the shared `AddMultiTenancy`/`UseMultiTenancy` extensions was found during initialization; end-to-end enforcement is therefore unverified.
- Environment-specific database provisioning and production backup/restore policy are not established by the inspected files.

## Confirmed user decision

On 2026-10-25, the user confirmed that tenant isolation is required across all customer-owned data and services. This is a security/data-isolation requirement; repository inspection did not verify that it is currently enforced. Treat the discrepancy as a high-priority open implementation question before making changes to customer data or authorization paths.
