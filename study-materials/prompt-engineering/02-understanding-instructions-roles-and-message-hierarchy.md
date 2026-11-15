# 02 — Instructions, Roles, and Message Hierarchy

## Why message roles matter

Many model APIs accept messages with different roles. Roles help separate application instructions from user requests and prior assistant responses. The exact role names and precedence rules depend on the provider/API, so I check the current provider documentation before implementation.

## 1. Common message roles

| Role | Typical purpose |
|---|---|
| System or developer | Application-level behaviour and constraints |
| User | The user's request and supplied input |
| Assistant | The model's previous output |
| Tool | Result returned by an application tool |

Some APIs use a slightly different set of roles or combine system/developer concepts. Do not assume every provider has identical behaviour.

## 2. Keep application instructions separate from user input

A common mistake is concatenating all text into one large string without clear boundaries.

A conceptual message design:

```text
Developer instruction:
You are a support explanation assistant.
Use only the evidence supplied by the application.
Do not claim to have executed commands.

User message:
Please explain this error.

Untrusted error content:
<error>
[Redacted stack trace]
</error>
```

The labels clarify the intended structure. They are not a security barrier by themselves. The application still needs to prevent data leakage and enforce permissions outside the model.

## 3. Do not let user input rewrite policy

A user may ask the model to ignore previous instructions. A document may contain text asking for secrets. The application should keep trusted policy in the appropriate higher-level message and treat user/document content as data.

However, message hierarchy is not a substitute for:
- access control,
- input validation,
- tool allowlists,
- output filtering,
- human approval for sensitive actions.

## 4. Keep instructions stable and task inputs variable

A reusable prompt template has stable instructions and replaceable data.

```python
def build_prompt(issue_type: str, error_text: str) -> str:
    return f"""Task: Explain the software issue.
Audience: A developer with basic .NET experience.
Rules:
- Use only facts in the supplied error.
- Separate likely causes from confirmed facts.
- Give read-only diagnostics before remediation.
- Do not claim a command has been executed.

Issue type: {issue_type}

<untrusted_error>
{error_text}
</untrusted_error>
"""
```

In production, use provider-supported structured messages and safe template construction. Validate and bound inputs before inserting them. Avoid placing untrusted content into trusted instruction fields.

## 5. Manage conversation history deliberately

Conversation history helps preserve context, but blindly sending every previous message can:
- waste tokens,
- include stale information,
- repeat sensitive data,
- confuse the current task,
- exceed the context window.

Keep only relevant history, summarise older context carefully, and make the current task explicit.

## Practice exercise

Design message roles for a code-review assistant. Separate fixed application instructions, user request, source code, and any tool result. Add a test input containing “ignore previous instructions” inside a source-code comment. Explain why the code comment must remain data rather than policy.

## Self-check

1. Why separate application instructions from user input?
2. What is the purpose of message roles?
3. Why are labels and delimiters not complete security controls?
4. What problems can stale conversation history create?
5. Which protections must be enforced outside the prompt?

## Further reading

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
- [OpenAI API documentation](https://platform.openai.com/docs/)
