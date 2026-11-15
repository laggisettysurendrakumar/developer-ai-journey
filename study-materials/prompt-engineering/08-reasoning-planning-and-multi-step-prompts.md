# 08 — Reasoning, Planning, and Multi-Step Prompts

## Make the work inspectable

For complex tasks, I ask for a concise plan, explicit assumptions, and verifiable conclusions. I do not need hidden internal reasoning. I need enough explanation to review the result and reproduce the important checks.

## 1. Plan before execution

For a multi-file change, ask for:
- requirement summary,
- files/components likely affected,
- assumptions and open questions,
- minimal implementation plan,
- risks,
- test plan.

Then review the plan before generating the implementation. In an automated application, enforce any approval pause in workflow code rather than relying on prompt wording alone.

## 2. Use intermediate outputs

A complex analysis can be split into:
1. classify the task,
2. extract relevant facts,
3. identify gaps,
4. compare options,
5. provide a recommendation,
6. validate against constraints.

This makes failures easier to locate than one giant prompt.

## 3. Ask for concise rationale

Useful request:

```text
Give the recommendation and the key evidence supporting it.
List assumptions and unresolved risks separately.
Do not provide speculative details as facts.
Provide the validation steps a developer can perform.
```

This asks for a reviewable explanation rather than private internal reasoning.

## 4. When to use separate model calls

Separate calls can help when:
- task stages need different context,
- an output must be reviewed before the next stage,
- one step transforms content and another checks it,
- a workflow needs a human approval gate,
- the task's token or latency budget requires controlled context.

But each extra call adds latency, cost, and another failure point. Use deterministic code for deterministic steps.

## 5. Avoid artificial complexity

Do not ask a model to “think step by step” for every task. For simple extraction or formatting, a clear instruction and schema may be better. The right workflow is the simplest one that meets the quality requirement.

## Practice exercise

Design a three-stage prompt workflow for investigating a synthetic API outage: evidence extraction, hypothesis ranking, and action recommendation. Make the final stage propose only safe, reversible actions and clearly mark approvals needed.

## Self-check

1. Why create intermediate outputs?
2. What kind of explanation is useful for reviewing a model result?
3. Why do more model calls add risk?
4. When should deterministic code replace a model step?
5. Why must approval be enforced outside the prompt?

## Further reading

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
