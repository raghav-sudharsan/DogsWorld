# DogsWorld Datadog Sample

DogsWorld is a small ASP.NET Core 3.1 REST API for managing dog records. It is intended as a simple sample for observing an application with the [Datadog .NET tracer](https://docs.datadoghq.com/tracing/trace_collection/automatic_instrumentation/dd_libraries/dotnet-core/).

## What it demonstrates

- CRUD endpoints backed by Entity Framework Core and SQL Server
- OpenAPI/Swagger documentation
- Datadog request-span tags on `GET /api/Dogs/{id}`:
  - `username`
  - `dog_request_id` (a per-request GUID)
- A minimal logging helper for adding span tags and exceptions through `Datadog.Trace`

## Technology

- .NET Core 3.1 / ASP.NET Core Web API
- Entity Framework Core 3.1
- SQL Server
- Datadog.Trace 2.1.0
- Swashbuckle (Swagger)

## Prerequisites

- .NET Core 3.1 SDK
- A SQL Server instance
- Optional: a Datadog account and .NET tracer setup for sending traces

> .NET Core 3.1 is out of support. This sample keeps its original target framework; use a supported .NET version for production work.

## Configure the database

Copy `appsettings.example.json` to `appsettings.json`, then enter your local connection string. `appsettings.json` is intentionally ignored by Git, so your database details stay on your machine.

For local development, the easiest secure option is .NET User Secrets. This is stored outside the project and automatically loaded in the Development environment:

```powershell
dotnet user-secrets set "ConnectionStrings:DogsWorldContext" "Server=localhost;Database=Dogs;User Id=YOUR_USER;Password=YOUR_PASSWORD;Trusted_Connection=False;MultipleActiveResultSets=true;"
```

Alternatively, provide the connection string with the standard ASP.NET Core configuration environment variable:

```powershell
$env:ConnectionStrings__DogsWorldContext = "Server=localhost;Database=Dogs;User Id=YOUR_USER;Password=YOUR_PASSWORD;Trusted_Connection=False;MultipleActiveResultSets=true;"
```

Do not commit live database credentials to a GitHub repository.

Apply the included Entity Framework migration if needed:

```powershell
dotnet ef database update
```

## Run locally

```powershell
dotnet restore
dotnet run
```

When running locally, Swagger is available at `/swagger` (for example, `https://localhost:<port>/swagger`).

## API endpoints

| Method | Endpoint | Description |
| --- | --- | --- |
| GET | `/api/Dogs` | List all dogs |
| GET | `/api/Dogs/{id}` | Get a dog and attach Datadog span tags |
| POST | `/api/Dogs` | Create a dog |
| PUT | `/api/Dogs/{id}` | Update a dog |
| DELETE | `/api/Dogs/{id}` | Delete a dog |

Example request body:

```json
{
  "name": "Milo",
  "breed": "Beagle",
  "age": 3,
  "birthDate": "2023-01-01T00:00:00Z",
  "hasOwner": true
}
```

## Datadog tracing

Install and configure the Datadog .NET tracer in the environment where the API runs. The `DatadogLogger` helper writes tags to the active Datadog span; its `AddException` method is available for recording handled exceptions.

See the official Datadog documentation for runtime-specific tracer installation and configuration.

