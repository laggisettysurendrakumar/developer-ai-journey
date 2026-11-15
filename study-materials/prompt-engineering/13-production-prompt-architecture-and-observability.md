# 13 — Production Prompt Architecture and Observability

## A prompt is one component of a system

In production, prompt quality depends on the model, input data, retrieval, tools, application policies, output parsing, and user experience. A prompt alone cannot make the entire feature reliable.

## 1. A practical architecture

```text
User Request
    |
Input Validation and Authentication
    |
Permission-Filtered Context Builder
    |
Prompt Template + Version
    |
Model API
    |
Output Parsing and Schema Validation
    |
Business Rules / Policy Checks
    |
Response to User
    |
Redacted Telemetry and Evaluation
```

Place permissions and business rules in trusted code, not in the prompt.

## 2. Observability

Track useful operational fields:
- prompt ID and version,
- model/provider identifier,
- request correlation ID,
- latency,
- input/output token counts,
- outcome category,
- validation failures,
- retry count,
- tool usage,
- evaluation result where policy allows.

Do not log full prompts and outputs by default if they contain sensitive business or personal data. Apply redaction, access controls, retention limits, and data minimisation.

## 3. Failure handling

Consider:
- provider timeout,
- rate limit,
- unavailable model,
- malformed structured output,
- context too large,
- refusal or policy block,
- empty/irrelevant retrieval,
- invalid tool arguments.

Return a safe, useful error. Retry only transient errors within a bounded deadline. Do not turn every failure into an unbounded second model request.

## 4. Prompt and model compatibility

A prompt can behave differently after:
- model version change,
- provider API change,
- tokenizer/context limit change,
- safety policy update,
- retrieval pipeline change,
- tool schema change.

Treat these as release inputs. Run a regression suite before upgrading a model or changing the context builder.

## 5. Production release checklist

- Is the prompt version controlled?
- Is its owner known?
- Are evaluation criteria documented?
- Is the context permission-filtered?
- Are structured outputs validated?
- Are tool actions independently authorised?
- Are sensitive logs minimised?
- Are timeouts and limits configured?
- Is a rollback version available?
- Are model/provider changes reviewed?

## Practice exercise

Create a production-readiness checklist for a .NET support assistant that uses prompts to summarise API failures. Include model timeout, redacted telemetry, prompt version, schema validation, regression tests, and rollback.

## Self-check

1. Why is the prompt only one part of the application?
2. Which telemetry helps diagnose prompt regressions?
3. Why should full prompts not automatically be logged?
4. What changes can affect prompt behaviour?
5. Why retain a rollback version?

## Further reading

- [OpenAI production best practices](https://platform.openai.com/docs/guides/production-best-practices)
- [OpenTelemetry](https://opentelemetry.io/docs/)
