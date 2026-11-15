# 08 — Agent, Tool and Workflow Evaluation

## Why evaluate the whole trajectory?

An agent may use several tools or model calls before completing a task. The final answer alone does not show whether the agent selected appropriate tools, passed safe arguments, respected permissions, or wasted resources.

Evaluate both the outcome and the sequence of actions.

## 1. Agent evaluation dimensions

- Task success: was the requested task completed correctly?
- Tool selection: did the agent select an appropriate tool?
- Argument validity: were tool inputs correctly formed and within allowed bounds?
- Authorisation: was the action permitted for this user?
- Sequencing: were steps performed in a sensible order?
- Termination: did the agent stop when complete or when limits were reached?
- Safety: did it avoid forbidden or destructive actions?
- Efficiency: how many model calls, tool calls, tokens, and seconds were used?

## 2. Record a structured trace

For each run, record safe metadata:
- Case ID and request ID.
- Agent and prompt version.
- Tool names invoked.
- Schema validation result.
- Authorisation decision.
- Step count.
- Duration and outcome.
- Error category.
- Human approval status.

Avoid recording raw credentials, full customer content, or sensitive tool output by default.

## 3. Test negative cases

Include:
- Unknown tool name.
- Invalid argument type.
- Missing required identifier.
- Prompt injection inside retrieved content.
- Unauthorised resource.
- Tool timeout.
- Repeated tool failure.
- Maximum-step exhaustion.
- Duplicate request.
- Human approval denied.

## 4. Evaluate side effects separately

A successful model plan does not mean a real-world action should be executed. Use deterministic permission checks, idempotency, approval gates, and audit trails for tools that create, update, delete, or send information.

## 5. Compare trajectories

When an agent framework or prompt changes, compare tool call counts, sequence, task completion, failure categories, policy outcomes, latency, and cost. A shorter trajectory is not necessarily better if it omits a required safety check.

## Practice exercise

Create ten mock agent tasks with expected tool sequences. Use fake tools and a deterministic authorisation layer. Test correct tool selection, invalid arguments, denied access, timeout, and maximum-step behaviour.

## Self-check

- Why is final-answer quality insufficient for agent evaluation?
- Which tool properties should be recorded?
- Why must authorisation be tested outside the model?
- What is a negative test?
- Why is fewer tool calls not always a better outcome?

## Official references

- OpenAI Agents SDK tracing: https://openai.github.io/openai-agents-python/tracing/
- OpenAI Evals: https://platform.openai.com/docs/guides/evals
