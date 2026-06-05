# Tutorial: C# / .NET (ASP.NET Core)

> Goal: build a minimal REST API in C# with tests. You already have `.dotnet/` on the server, so the
> runtime is partly there. Framing: "ASP.NET Core is .NET's FastAPI/Express." Claim "I've built
> with," not "expert."

## Mental model
C#: statically typed, compiled, OOP + modern functional features, runs on **.NET** (cross-platform,
Linux-friendly). **ASP.NET Core** is the web framework; **Entity Framework Core (EF Core)** is the
ORM (≈ SQLAlchemy/JPA). **NuGet** is the package manager (≈ pip/npm). Async is first-class
(`async/await`, `Task`).

## Step 1 — install & scaffold
```bash
# install SDK if needed
sudo apt-get install -y dotnet-sdk-8.0
dotnet --version
dotnet new webapi -o PartsApi      # scaffold a Web API project
cd PartsApi
dotnet run                         # runs on http://localhost:5xxx
```

## Step 2 — a minimal API (modern, concise style)
`Program.cs`:
```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddDbContext<AppDb>(o => o.UseNpgsql(            // Postgres via Npgsql
    builder.Configuration.GetConnectionString("Default")));
var app = builder.Build();

app.MapGet("/api/parts/{id}", async (int id, AppDb db) =>
    await db.Parts.FindAsync(id) is Part p ? Results.Ok(p) : Results.NotFound());

app.MapPost("/api/parts", async (Part part, AppDb db) =>
{
    db.Parts.Add(part);
    await db.SaveChangesAsync();
    return Results.Created($"/api/parts/{part.Id}", part);
});

app.Run();
```

## Step 3 — model + DbContext (EF Core ORM)
```csharp
public class Part
{
    public int Id { get; set; }
    [Required] public string Sku { get; set; } = "";
    public int Quantity { get; set; }
}

public class AppDb : DbContext
{
    public AppDb(DbContextOptions<AppDb> o) : base(o) {}
    public DbSet<Part> Parts => Set<Part>();
}
```
```bash
dotnet ef migrations add Init     # EF Core migrations (like Alembic)
dotnet ef database update
```
Terms: **CLR, .NET runtime, NuGet, DI (built into ASP.NET Core), EF Core, DbContext, LINQ,
`async`/`await`/`Task`, records, properties, `IEnumerable`.**

## Step 4 — LINQ (C#'s killer feature — query collections like SQL)
```csharp
var lowStock = db.Parts
    .Where(p => p.Quantity < 5)
    .OrderBy(p => p.Sku)
    .Select(p => p.Sku)
    .ToList();
```
Definitely name-drop **LINQ** — it's the thing C# devs love.

## Step 5 — testing (xUnit + Moq)
```csharp
public class PartLogicTests
{
    [Fact]                                   // a test
    public void LowStock_Flags_Under_Threshold()
    {
        var part = new Part { Sku = "GEN-1", Quantity = 3 };
        Assert.True(part.Quantity < 5);
    }

    [Theory]                                 // parametrized (table-driven)
    [InlineData(3, true)]
    [InlineData(10, false)]
    public void Threshold(int qty, bool expected) =>
        Assert.Equal(expected, qty < 5);
}
```
```bash
dotnet test
```
- **xUnit** (most common; `[Fact]`, `[Theory]`), also **NUnit**, **MSTest**.
- **Moq** = mocking. **FluentAssertions** = readable asserts. **WebApplicationFactory** = in-memory
  integration testing of the whole API. **Testcontainers** for real Postgres.

## Common .NET libraries to name
| Need | Library |
|---|---|
| Web | **ASP.NET Core** (MVC + Minimal APIs) |
| ORM | **EF Core**, Dapper (micro-ORM) |
| DB driver | **Npgsql** (Postgres) |
| Testing | **xUnit**, Moq, FluentAssertions |
| JSON | **System.Text.Json**, Newtonsoft.Json |
| DI | built-in `Microsoft.Extensions.DependencyInjection` |
| Logging | Serilog, built-in `ILogger` |
| Background jobs | Hangfire, `IHostedService` |

## Interview soundbites
- "ASP.NET Core minimal APIs are very close to FastAPI — DI, async/await, EF Core for the ORM, LINQ
  for queries, xUnit + Moq for tests. I've built REST services with it; my production stack is
  Python/TypeScript."
- Know: **.NET runtime/CLR, ASP.NET Core, EF Core, LINQ, async/Task, NuGet, DI, xUnit.**
