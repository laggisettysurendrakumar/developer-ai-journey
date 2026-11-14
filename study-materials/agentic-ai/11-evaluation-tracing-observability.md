# Lesson 11 — Evaluate and Observe an Agent

## Learning goals

Learn how to know whether an agent is doing the right thing, not simply producing fluent text. Learn to record traces and measure task quality, tool use, latency, reliability, and cost.

## 1. What should be evaluated?

For each task, define success criteria before running the agent:
- Did it understand the goal?
- Did it select the correct tool?
- Were arguments valid?
- Was the user allowed to access the data?
- Did the tool result support the answer?
- Did it stop at the right time?
- Did it avoid unauthorised actions?
- Was latency and cost acceptable?

## 2. Build a test set

Use representative synthetic inputs:
- Normal question.
- Ambiguous request.
- Missing required details.
- No matching document.
- Tool timeout.
- Unknown tool request.
- Prompt injection.
- Unauthorised document.
- Repeated tool result.
- Request requiring human approval.

Keep expected outcomes and source IDs alongside each test.

## 3. Deterministic tool test

```python
def safe_tool_name(name: str) -> bool:
    return name in {"search_docs", "calculator"}

def test_unknown_tool_is_rejected():
    assert safe_tool_name("run_shell") is False

def test_known_tool_is_allowed():
    assert safe_tool_name("search_docs") is True

test_unknown_tool_is_rejected()
test_known_tool_is_allowed()
print("Tests passed")
```

This is a code test. It does not evaluate natural-language answer quality.

## 4. Agent evaluation dimensions

| Dimension | Example measure |
|---|---|
| Task completion | Percentage of tasks completed correctly |
| Tool selection | Correct tool chosen for each scenario |
| Argument accuracy | Valid and relevant arguments |
| Groundedness | Claims supported by retrieved evidence |
| Safety | Disallowed action attempts blocked |
| Efficiency | Number of turns and tool calls |
| Latency | End-to-end time and dependency durations |
| Cost | Tokens, model calls, and infrastructure usage |

Do not optimise one metric while ignoring the others. Fewer tool calls are not useful if the answer becomes unsupported.

## 5. What is a trace?

A trace records the steps for one run. It can include:
- Application request received.
- Model call started/completed.
- Tool requested.
- Validation result.
- Permission decision.
- Tool completed or failed.
- Final response generated.

A trace helps answer “what happened?” It should include correlation IDs and timings, not secrets.

## 6. Simple timing example

```python
import time

started = time.perf_counter()

# Placeholder for one local operation.
sum(range(100_000))

duration_ms = (time.perf_counter() - started) * 1000
print(f"Operation took {duration_ms:.2f} ms")
```

For network operations, separately record model duration, retrieval duration, and tool duration so you can locate the bottleneck.

## 7. Human review

Human review is useful for:
- High-impact tasks.
- Safety or policy behaviour.
- Citation correctness.
- Ambiguous outputs.
- New model or prompt versions.

Reviewers should use a rubric rather than a vague “looks good” judgement.

## 8. Regression testing

Run the same dataset whenever you change:
- Model or model version.
- System/developer instructions.
- Tool descriptions or schemas.
- Retrieval settings or document chunking.
- SDK or orchestration framework.
- Permission logic.

Store the versions needed to reproduce a run.

## Practice exercise

Create 20 synthetic Agent Support Assistant tests. Include expected tool, expected source, allowed/denied status, and expected completion behaviour. Add a small report that counts passed and failed cases.

## Self-check

1. Why is fluent output not sufficient evidence of success?
2. How is tool accuracy measured?
3. What belongs in a trace?
4. Why must RAG retrieval and final answer be evaluated separately?
5. What changes should trigger regression tests?

**Completion check:** I can build a small evaluation set and explain what a trace should record.
