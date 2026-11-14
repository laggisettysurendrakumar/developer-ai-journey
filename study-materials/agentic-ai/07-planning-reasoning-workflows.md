# Lesson 07 — Planning, Reasoning, and Workflow Design

## Learning goals

Understand why an agent may break a goal into smaller steps, how to design a plan safely, and when a predictable workflow is a better design.

## 1. What is planning?

Planning is deciding which steps may help achieve a goal. A user might ask:

“Investigate why the demo API is failing and prepare a short report.”

A possible plan:
1. Confirm which environment is affected.
2. Read the supplied error.
3. Search the approved runbook.
4. Retrieve read-only service status.
5. Compare evidence.
6. Prepare a report with sources and unknowns.

A model can suggest a plan. The application must validate and execute permitted steps.

## 2. Plan first, execute second

Do not let a model jump directly into actions with side effects. Have the application inspect the plan:
- Is every step allowed?
- Does a step need a tool?
- Is the requested scope clear?
- Is the user authorised?
- Is approval required?
- Is there a maximum step budget?

## 3. Plan data structure

```python
from pydantic import BaseModel, Field

class PlanStep(BaseModel):
    description: str = Field(min_length=1, max_length=200)
    tool_name: str | None = None
    requires_approval: bool = False

class TaskPlan(BaseModel):
    goal: str
    steps: list[PlanStep] = Field(min_length=1, max_length=5)

plan = TaskPlan.model_validate({
    "goal": "Investigate a sample API timeout",
    "steps": [
        {"description": "Search the approved runbook", "tool_name": "search_docs"},
        {"description": "Read synthetic service health", "tool_name": "get_service_status"},
        {"description": "Prepare a report with sources"}
    ]
})

for number, step in enumerate(plan.steps, start=1):
    print(number, step.description)
```

The schema limits a plan to five steps. It does not prove the plan is correct or that the user may run each tool.

## 4. Plan validation is not plan approval

A plan can be syntactically valid and still be unsafe. For example:
- Read a document outside the user's access.
- Restart production when only diagnosis was requested.
- Send an email without approval.
- Repeat a costly search unnecessarily.

The application must check each step at execution time, not only when the plan is first created.

## 5. Planning versus deterministic workflow

Use a deterministic workflow when the process is known:
- Every request follows the same mandatory validation.
- Financial or production actions need strict approvals.
- Compliance requires predictable steps.

Use agent planning when a task genuinely has alternative paths and discovered information changes what is useful next.

A hybrid is common: a fixed workflow validates identity and scope, then an agent handles the bounded investigation, then a fixed approval process controls any action.

## 6. Avoid endless replanning

A planner can keep changing the plan. Control it with:
- Maximum planning revisions.
- Maximum execution steps.
- Time budgets.
- Tool-call budgets.
- Clear completion criteria.
- A safe stop path.

## 7. Practice exercise

Create a plan for investigating a fictional `502 Bad Gateway` error:
1. Keep the plan read-only.
2. Use no more than four steps.
3. Identify which evidence each step should return.
4. Mark any step that would change a system as requiring approval.
5. Write a validator that rejects more than four steps and unknown tool names.


## Practice exercise

Create a plan for investigating a fictional `502 Bad Gateway` error:
1. Keep the plan read-only.
2. Use no more than four steps.
3. Identify which evidence each step should return.
4. Mark any step that would change a system as requiring approval.
5. Write a validator that rejects more than four steps and unknown tool names.


## Self-check

1. Why should a plan be checked before execution?
2. Why does valid Pydantic data not prove a plan is safe?
3. When is a fixed workflow better?
4. How can an agent avoid endless replanning?
5. What is a useful completion condition?

**Completion check:** I can design a bounded plan and distinguish planning from permission to act.
