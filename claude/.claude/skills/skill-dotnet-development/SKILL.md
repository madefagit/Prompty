---
name: skill-dotnet-development
description: "EF Core 9 + Npgsql Code-First, mandatory /healthz + /readyz probes, startup schema auto-upgrade, OpenTelemetry instrumentation, all config stored in database"
type: development
tags: ["dotnet", "csharp", "aspnet", "efcore", "npgsql", "postgresql"]
---

# .NET Development

## Database Access — EF Core 9 + Npgsql (Code-First)

- **MANDATORY**: all .NET applications use **EF Core 9** with the **Npgsql** provider for database access.
- Apply `UseSnakeCaseNamingConvention()` — all PostgreSQL columns and tables use snake_case automatically.
- **NEVER** use raw ADO.NET or Dapper directly in application code.
- Database: PostgreSQL 17. SQLite is permitted **only for unit/integration tests** (in-memory).
- Use `IRepository<T>` abstraction over direct `DbContext` in application and domain layers.
- Leverage PostgreSQL-specific types: `JSONB` for semi-structured data, `PostGIS` for geo queries, `TSVECTOR` generated columns for full-text search.

## Health Endpoints (Mandatory)

Every .NET service MUST expose:

- `/healthz` — liveness probe (returns 200 if process is alive)
- `/readyz` — readiness probe (checks all dependencies: DB connection, external services, message brokers)

Implementation:
```csharp
builder.Services.AddHealthChecks()
    .AddCheck("database", () => /* EF Core DbContext connection test */)
    .AddCheck("dependencies", () => /* external service checks */);
```

## Startup Schema Auto-Upgrade (Mandatory)

On every application startup, before serving traffic:

1. Connect to configured database via EF Core DbContext.
2. Read schema version from `__schema_version` table (create if missing, treat as v0).
3. Compare against required version in code.
4. **If behind**: apply pending migrations in order, each in its own transaction. Log: `migration_id`, `from_version`, `to_version`, `duration_ms`, `status`.
5. **If equal**: proceed normally.
6. **If ahead (downgrade)**: FAIL FAST with clear error. Never run against a newer schema.

Safety:
- Backup before migration (mandatory for SQLite, advisory for server engines).
- Abort on failure, roll back transaction, emit actionable error.
- Never partially start with inconsistent schema.

Observability:
- Expose schema version via `/readyz` endpoint.
- Log upgrade status at INFO level.

## Migrations

- Embedded in the application (not external SQL files).
- Each migration: idempotent where possible, wrapped in transaction.
- Naming: sequential number + description (e.g., `V003_AddUserPreferences`).
- Migration class pattern:

```csharp
public interface IMigration
{
    int Version { get; }
    string Description { get; }
    Task Up(AppDbContext db);
    Task Down(AppDbContext db); // optional, for rollback
}
```

## Build Targets

- **Containerized applications**: build for Linux x64 (`linux-x64`).
- **Exception**: LLMRouter — always build for macOS.
- **Console/desktop applications**: build 3 platforms (macOS, Windows x64, Linux x64), portable/self-contained.

## OpenTelemetry Instrumentation

- Metrics: request duration, error rate, active connections, custom business metrics.
- Traces: distributed tracing with correlation IDs across services.
- Logs: structured JSON format (Serilog with OpenTelemetry sink).
- Exporters: OTLP (configurable endpoint).

## Configuration Architecture

- All configuration stored in database (Rule 27). Never in appsettings.json for app-specific settings.
- Hot reload: periodic refresh from DB (configurable interval TTL). React to config save triggers immediately.
- Cache config in memory with TTL + on-write invalidation.
- REST API for all config, protected by token/OIDC.
- Admin dashboard for config management (if app has UI).

## REST API Standards

- All config options exposed via authenticated REST API.
- Write operations protected by secret token or OIDC.
- Every write endpoint: validate input (type, range, enum, format).
- Return clear validation error messages (field + reason + expected format).
- API versioning: URL path (e.g., `/api/v1/`).

## Project Structure

```
src/
  AppName/
    Program.cs
    Migrations/
    Services/
    Controllers/
    Models/
data/
  app.db              # Default SQLite database
tests/
  AppName.Tests/
Dockerfile
docker-compose.yml
```
