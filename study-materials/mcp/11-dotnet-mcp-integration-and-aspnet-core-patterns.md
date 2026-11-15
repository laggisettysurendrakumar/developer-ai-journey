# 11 — .NET MCP Integration and ASP.NET Core Patterns

## Why this lesson matters

I work with ASP.NET Core and C#, so I want to understand how MCP fits into a .NET service. The important architecture principle is the same as Python: business logic belongs in normal application services, and the MCP layer should be a thin adapter.

## 1. Start with application services

A typical .NET solution might contain:
- `Application`: use cases and business rules,
- `Infrastructure`: database and external API adapters,
- `Web`: ASP.NET Core endpoints,
- `Mcp`: MCP server adapter.

Do not put SQL queries and business permissions directly inside tool methods. Inject application services and call the same tested use cases used by the API.

## 2. Illustrative service boundary

```csharp
public sealed record SearchDocumentsQuery(string Query, int Limit);

public sealed record DocumentSummary(string Id, string Title);

public interface IDocumentSearchService
{
    Task<IReadOnlyList<DocumentSummary>> SearchAsync(
        SearchDocumentsQuery query,
        ClaimsPrincipal user,
        CancellationToken cancellationToken);
}
```

The service receives the authenticated principal and cancellation token. Its implementation should validate the query, enforce object- and tenant-level access in the repository, and return only permitted summaries.

## 3. MCP adapter responsibilities

The exact .NET MCP SDK package, extension methods, registration, and tool attribute APIs can change. I check the current official C# SDK and package docs before copying setup code.

Conceptually, a tool adapter should:
- expose a narrow tool name and description,
- bind a strongly typed argument object,
- retrieve the trusted identity from the server's request/authentication context,
- call `IDocumentSearchService`,
- pass cancellation through,
- map expected failures to safe protocol errors.

Never trust a `userId` supplied as a tool argument to establish identity.

## 4. ASP.NET Core and HTTP hosting

For remote hosting, apply ASP.NET Core middleware and infrastructure consistently:
- HTTPS/TLS,
- authentication,
- authorisation,
- request size limits,
- rate limiting,
- structured logging and tracing,
- health checks,
- dependency timeouts,
- graceful shutdown.

Ensure the MCP SDK's HTTP transport and hosting mode are supported by the versions you deploy. Do not assume that mapping an ordinary MVC controller route is equivalent to implementing MCP.

## 5. Cancellation and dependency injection

Pass cancellation tokens to database and HTTP operations. Register application services with suitable lifetimes. Avoid capturing scoped services in singleton objects. Do not launch unbounded background work from a tool invocation.

## 6. Integration strategy for a .NET estate

A practical approach is to wrap existing service-layer use cases rather than expose the entire REST surface. Start with a read-only capability, for example service catalogue search. Add write tools only after threat modelling, approval design, and audit controls.

## Practice exercise

Create an architecture diagram for an ASP.NET Core 8 service exposing a read-only MCP search capability. Show authentication middleware, MCP adapter, application service, repository, SQL database, and audit telemetry. Define how the principal and cancellation token reach the repository. Check the official C# SDK for current code and implement one tool in a separate branch.

## Self-check

1. Why should an MCP adapter call an application service?
2. Why should identity not be a normal tool argument?
3. What does cancellation propagation achieve?
4. Which HTTP controls apply to a remote MCP server?
5. Why must the installed SDK version be checked before using sample APIs?

## Further reading

- [Official C# SDK](https://github.com/modelcontextprotocol/csharp-sdk)
- [ASP.NET Core documentation](https://learn.microsoft.com/aspnet/core/)
