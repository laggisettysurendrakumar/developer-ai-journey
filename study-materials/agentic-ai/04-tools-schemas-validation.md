# Lesson 04 — Agent Tools, Schemas, Validation, and Permissions

## Learning goals

Learn how to design tools that are easy for a model to choose, easy for Python to validate, and safe for an application to execute.

## 1. What is a tool?

A tool is a deliberately exposed function or API that performs one specific operation. Examples:
- Search an approved knowledge base.
- Calculate a numeric value.
- Read a synthetic service status.
- Look up a ticket that the signed-in user may access.

A good tool has one responsibility, clear parameters, predictable output, and a controlled failure mode.

## 2. Bad versus good tool design

Bad: `do_anything(command: str)`.

Why bad:
- The purpose is unclear.
- The input may represent arbitrary code or shell.
- It is hard to validate and authorise.
- It can create dangerous side effects.

Better: `get_ticket(ticket_id: str)`.

Why better:
- One clear operation.
- A well-defined input.
- Access can be checked for that ticket.
- Output can be limited to approved fields.

## 3. Pydantic tool schemas

```python
from pydantic import BaseModel, Field

class TicketLookupArgs(BaseModel):
    ticket_id: str = Field(pattern=r"^INC-[0-9]{6}$")

class CalculatorArgs(BaseModel):
    left: float = Field(ge=-1_000_000, le=1_000_000)
    right: float = Field(ge=-1_000_000, le=1_000_000)
    operation: str = Field(pattern=r"^(add|subtract|multiply)$")
```

Schemas define what an acceptable request looks like. A schema can validate format and ranges, but it cannot prove the caller has access to a ticket.

## 4. Validate, authorise, execute

```python
SAMPLE_TICKETS = {
    "INC-123456": {
        "owner": "team-a",
        "summary": "Sample API timeout"
    }
}

def get_ticket(ticket_id: str, user_team: str) -> dict:
    args = TicketLookupArgs(ticket_id=ticket_id)

    ticket = SAMPLE_TICKETS.get(args.ticket_id)
    if ticket is None:
        raise LookupError("Ticket was not found")

    if ticket["owner"] != user_team:
        raise PermissionError("You are not allowed to access this ticket")

    return {
        "ticket_id": args.ticket_id,
        "summary": ticket["summary"]
    }

print(get_ticket("INC-123456", "team-a"))
```

**What it does:** Validates the ticket ID, finds the ticket, checks the user's team, and returns only selected fields.

**Why useful?** Every tool call should enforce application permissions even if the model was instructed to behave.

**Use case:** An incident assistant retrieves ticket details only for an authorised support team.

The `user_team` in this small example is passed by the application. Never trust a team name or user ID supplied only by the model. Obtain identity from authenticated server-side context.

## 5. Tool errors and outputs

Use clear, safe error categories:
- Invalid arguments.
- Not found.
- Not authorised.
- Temporary dependency failure.
- Timeout.
- Rate limit.

Do not return raw stack traces, access tokens, database connection strings, or internal secrets to the model. Return only enough context for safe recovery.

## 6. Tool design checklist

- One purpose per tool.
- Short, clear name.
- Description states when the tool should be used.
- Schema validates all inputs.
- Authorisation is independent of the model.
- Read-only by default.
- Output is small and structured.
- Timeout and rate limits are enforced.
- Side effects require idempotency and/or approval.
- Logs contain correlation IDs, not secrets.

## 7. Practice exercise

Design three tools for a Developer Support Agent:
1. `search_docs(query, category)`.
2. `get_service_status(service_name)`.
3. `calculate_percentage(value, total)`.

For each, write a Pydantic argument model, allowed value constraints, output shape, possible errors, and a test proving an unauthorised request is rejected.

## Self-check

1. What makes a tool description useful?
2. What can Pydantic validate?
3. Why is schema validation not the same as authorisation?
4. Why should tools return limited output?
5. What should happen when an unknown tool is requested?

**Completion check:** I can design a narrow, validated tool with server-side permission checks.
