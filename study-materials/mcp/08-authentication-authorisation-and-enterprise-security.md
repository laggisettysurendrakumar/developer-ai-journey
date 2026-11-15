# 08 — Authentication, Authorisation, and Enterprise Security

## The security mindset

An MCP server is an API boundary. The fact that an AI host is calling it does not reduce the usual security requirements. I need to know who is calling, what they may access, what the tool can change, and how the action is audited.

## 1. Authentication versus authorisation

- **Authentication:** Who or what is making the request?
- **Authorisation:** Is that identity allowed to perform this operation on this particular object?

A valid token may identify a user but still not permit access to every project, repository, document, or tenant.

## 2. Local and remote trust boundaries

For a local stdio server, the operating-system user and process environment form a significant part of the trust boundary. Avoid running the server with elevated privileges. Restrict its filesystem access and environment variables.

For remote Streamable HTTP, use the security model required by the current MCP specification and your deployment. Validate tokens, issuer, audience, expiry, and relevant scopes. Do not invent a custom token flow or pass arbitrary client credentials through to downstream services.

## 3. Authorise every operation

A secure request path should:
1. Establish the authenticated principal using a trusted mechanism.
2. Validate the requested operation and arguments.
3. Check action-level permission.
4. Check object-level permission for every requested record.
5. Apply tenant and data filters in the backend query.
6. Return only fields the principal may see.
7. Emit a redacted audit event.

Do not accept a caller-supplied `user_id` as proof of identity. Derive the principal from the authenticated context.

## 4. Confused deputy risk

A server may have broad backend credentials. A user may ask it to read or change a resource they themselves cannot access. If the server uses its own privilege without checking the user's authority, it becomes a confused deputy.

Mitigations:
- pass trusted identity/claims into the operation,
- enforce user and tenant permissions in the backend,
- use delegated/on-behalf-of access where the architecture requires it,
- scope service credentials narrowly,
- audit the acting user and the server identity,
- avoid exposing arbitrary resource IDs without object-level checks.

## 5. Prompt injection and untrusted content

Tool descriptions, resource contents, issue comments, repository files, and search results are data. They may contain instructions such as “ignore the previous rules” or requests to exfiltrate secrets.

Defences:
- keep system/developer policy outside retrieved content,
- label returned material as untrusted data,
- never allow retrieved text to grant permissions,
- constrain tool arguments and operations,
- separate reading from writing,
- use confirmation for consequential actions,
- test malicious content in resources and tool outputs.

## 6. High-risk tools

Avoid exposing unrestricted:
- shell execution,
- arbitrary SQL,
- filesystem traversal,
- cloud administration,
- identity and access management changes,
- production deployment,
- secret retrieval.

When a valid business need exists, expose a narrowly scoped operation with allowlists, approvals, audit, rate limits, and rollback planning.

## 7. Secrets and data protection

- Store secrets in an approved secret manager.
- Do not embed secrets in prompts, source code, resource URIs, or error responses.
- Redact access tokens and personal data in logs.
- Set retention rules for prompts, outputs, and audit events.
- Use synthetic data for learning and tests.
- Review data residency and provider retention terms before sending content to an external model.

## Practice exercise

Threat-model a remote MCP server that can read deployment metadata. List assets, identities, trust boundaries, abuse cases, and mitigations. Include a confused-deputy scenario and a prompt-injection scenario. Define what must be logged without storing secrets.

## Self-check

1. What is authentication and what is authorisation?
2. What is a confused deputy?
3. Why must object-level access checks happen in the backend?
4. Why are resource contents untrusted?
5. Which controls are needed before exposing a write-capable tool?

## Further reading

- [MCP security best practices](https://modelcontextprotocol.io/docs/tutorials/security/security_best_practices)
- [MCP authorisation specification](https://modelcontextprotocol.io/specification/)
