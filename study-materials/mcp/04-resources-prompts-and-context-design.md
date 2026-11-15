# 04 — Resources, Prompts, and Context Design

## Three different capability types

MCP has three important capability concepts. I should choose the one that matches the job instead of turning every piece of information into a tool call.

| Capability | Plain-English purpose | Example |
|---|---|---|
| Tool | Perform an operation | Search tickets |
| Resource | Expose addressable content | A service runbook |
| Prompt | Offer a reusable prompt template | Create an incident summary |

## 1. Resources

A resource is identified by a URI and represents content a client can read. It may represent a file, database record, generated report, or other application data.

Example URI design:
- `knowledge://runbooks/linux-service`
- `knowledge://services/catalog`
- `ticket://INC-12345/summary`

Choose stable, meaningful URI patterns. Do not put bearer tokens, passwords, or private secrets in resource URIs.

A resource may contain untrusted text. A runbook, ticket, or source file could include instructions intended to manipulate an AI model. The host must treat resource content as data, not as trusted system instructions.

## 2. Resource templates and dynamic content

Some resources are known in advance; others follow a template. A template can describe how a client may construct a resource URI for a specific item. Before reading a resource, the server must confirm the caller is allowed to access that item.

Do not assume that a hard-to-guess URI is a security control. Apply normal object-level authorisation.

## 3. Prompts

A prompt capability can provide a reusable, parameterised prompt template. For example, a `summarise_incident` prompt may accept an incident description and ask for impact, timeline, evidence, and next steps.

Prompts improve reuse and consistency, but they are not permissions, validation, or a guarantee of a correct answer.

## 4. Choose the right surface

- Use a **tool** when the client needs to ask the server to perform an operation.
- Use a **resource** when the client needs to read a known piece of context.
- Use a **prompt** when a reusable task template is useful.

One business workflow can use all three. Example: read an approved service runbook as a resource, call a read-only health-check tool, and use an incident-summary prompt to structure the final response.

## 5. Context design and data minimisation

Context is not free. Large resources increase latency, token usage, and the chance that irrelevant or malicious content influences the model.

I should:
- return only relevant fields,
- include stable source identifiers,
- keep snippets bounded,
- preserve timestamps and version information where useful,
- filter by permissions before content leaves the backend,
- avoid secrets and unnecessary personal data,
- make stale or partial data visible.

## Practice exercise

Design one tool, two resources, and one prompt for a developer support assistant. Write a URI convention, sample response, access rule, and maximum response size for each. Include a malicious instruction inside a sample runbook and explain how the host should treat it.

## Self-check

1. When is a resource better than a tool?
2. What does a prompt capability provide?
3. Why is an unpredictable resource URI not sufficient security?
4. Why should resource content be treated as untrusted?
5. How does data minimisation improve an AI integration?

## Further reading

- [MCP server concepts](https://modelcontextprotocol.io/docs/learn/server-concepts)
- [MCP specification](https://modelcontextprotocol.io/specification/)
