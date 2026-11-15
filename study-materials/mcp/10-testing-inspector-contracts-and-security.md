# 10 — Testing MCP Servers: Inspector, Contracts, and Security

## Testing layers

I test the business operation, protocol adapter, transport, security boundary, and failure behaviour separately. A successful manual tool call is not enough to prove a server is ready.

## 1. Unit tests

Test normal and edge cases in the business function:
- valid input,
- empty and oversized strings,
- unexpected fields,
- unknown resource IDs,
- permission denied,
- empty result,
- backend exception.

Keep tests deterministic by using fake repositories and synthetic data.

## 2. Contract tests

Verify that the server advertises the expected names, descriptions, schemas, resources, and prompts. Test that an allowed request returns the expected shape and that invalid requests fail safely.

When protocol versions change, contract tests can reveal incompatibilities early.

## 3. Inspector testing

The official MCP Inspector helps developers connect to a server, inspect advertised capabilities, and try requests interactively. Use it against a local synthetic server before connecting real systems.

Record:
- SDK and protocol version,
- transport,
- server launch command,
- discovered capabilities,
- representative requests,
- error responses,
- process exit behaviour.

Do not assume that passing Inspector tests means every production host behaves the same.

## 4. Integration tests

Use a controlled test server and fake dependencies. Test client/server startup, discovery, invocation, response parsing, disconnect, and reconnect. Do not use production credentials in test fixtures.

## 5. Security tests

Test:
- missing/invalid/expired authentication,
- cross-user and cross-tenant access,
- resource ID enumeration,
- prompt injection inside resources,
- oversized inputs and outputs,
- rate limit behaviour,
- secret leakage in logs and errors,
- attempts to invoke unavailable tools,
- dangerous argument combinations.

## 6. Failure injection

Simulate:
- backend timeout,
- server restart during a request,
- client disconnect,
- malformed backend response,
- overload,
- partial dependency outage.

Verify that the server fails closed for security decisions and does not leave duplicate writes behind.

## 7. CI pipeline

A sensible sequence:
1. formatting and static checks,
2. unit tests,
3. schema/contract tests,
4. security tests,
5. integration tests with fake services,
6. dependency and secret scanning,
7. build and package,
8. deploy to a non-production environment,
9. smoke test from a compatible client.

Avoid publishing a server simply because it compiles.

## Practice exercise

Create a test matrix for your Lesson 05 server with at least ten cases. Include two malformed inputs, two expected results, one unknown resource, one disconnect, one dependency error, one prompt-injection payload, and one oversized response. Mark which tests run in CI.

## Self-check

1. What is the purpose of a contract test?
2. What can the Inspector verify?
3. Why are fake dependencies useful?
4. Which security tests should every multi-tenant server include?
5. Why is a successful manual call not sufficient for production readiness?

## Further reading

- [MCP Inspector](https://github.com/modelcontextprotocol/inspector)
- [Official Python SDK](https://github.com/modelcontextprotocol/python-sdk)
