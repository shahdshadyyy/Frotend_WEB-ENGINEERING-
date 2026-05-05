# Course Management (API + React SPA)

Monorepo layout:

| Path | Description |
|------|-------------|
| `CourseManagementApi.csproj` | ASP.NET Core 8 REST API (JWT + SQLite + EF Core) |
| `CourseManagement.sln` | Solution file (folder also contains `.csproj` → always pass **`--project`**) |
| `app/` | Vite + React + TypeScript SPA |

## Prerequisites

- [.NET 8 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
- [Node.js 20+](https://nodejs.org/)

No Docker or database server is required by default: the API uses **SQLite** file **`course_mgmt.db`** (created beside the API project on first run).

## Quick start (development)

### 1. Start the API

From this folder (same folder as `CourseManagementApi.csproj`):

```powershell
dotnet run --project CourseManagementApi.csproj --launch-profile http
```

URLs:

- API: `http://localhost:5288`
- Swagger: `http://localhost:5288/swagger`

> This repo contains **both** `CourseManagement.sln` and `CourseManagementApi.csproj`. Plain **`dotnet run`** may prompt MSB1011 — always use **`--project CourseManagementApi.csproj`** as above.

### 2. Start the SPA

In another terminal:

```powershell
cd app
npm install
npm run dev
```

Open **`http://localhost:5173`** (Vite defaults to port **5173**).

The SPA sends **`/api/*`** to the API via the Vite proxy (`vite.config.ts`), targeting **`http://localhost:5288`** unless you set **`VITE_PROXY_TARGET`**.

### 3. Sign in (seeded data)

After the first successful startup, **`course_mgmt.db`** exists and demo users are seeded:

| Email | Password | Role |
|-------|-----------|------|
| `admin@university.edu` | `Admin123!` | Admin |
| `alice.johnson@university.edu` | `Instructor123!` | Instructor |
| `charlie.brown@student.university.edu` | `Student123!` | Student |

*(More users in `Data/DbSeeder.cs`.)*

### Database / migrations

- Connection string: `appsettings.json` → `ConnectionStrings:DefaultConnection` (default `Data Source=course_mgmt.db`).
- Migrations live in **`Migrations/`**. After changing entities:  
  `dotnet ef migrations add <Name> --project CourseManagementApi.csproj`
- To reset locally: stop the API, delete **`course_mgmt.db`**, start again (migrations + seed rerun).

## Production build (SPA)

```powershell
cd app
npm install
npm run build
```

Output: **`app/dist/`**. Set **`VITE_API_BASE_URL`** to your public API URL at build time. See **`app/.env.example`**.

## Troubleshooting

- **`dotnet run` / `dotnet build` asks for a project file**: Use **`--project CourseManagementApi.csproj`** or **`dotnet build CourseManagement.sln`**.
- **SPA cannot reach API**: Ensure the API is on **5288** (http profile) or set **`VITE_PROXY_TARGET`** in **`app/.env.development`** to match **`launchSettings.json`**.
- **Stale schema**: Delete **`course_mgmt.db`** with the API stopped, then restart.
