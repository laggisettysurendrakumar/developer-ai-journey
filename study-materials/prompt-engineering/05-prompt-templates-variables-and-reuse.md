# 05 — Prompt Templates, Variables, and Reuse

## Why reusable templates matter

A prompt template separates stable instructions from variable inputs. It makes changes easier to review and prevents every caller from constructing a slightly different prompt.

## 1. A template design

A prompt template normally contains:
- a stable task description,
- audience and scope,
- rules and constraints,
- named input variables,
- expected output format,
- optional examples.

Use meaningful variable names such as `{error_message}` and `{code_snippet}`, not `{x}` and `{y}`.

## 2. Simple Python template function

```python
def build_code_review_prompt(code: str, language: str) -> str:
    if not code.strip():
        raise ValueError("Code must not be empty.")
    if len(code) > 12_000:
        raise ValueError("Code input exceeds the example size limit.")

    return f"""Task: Review the supplied code.
Language: {language}
Review only correctness, security, maintainability, and performance.
Do not invent issues. If no issue is found, say so.
For each issue, provide severity, evidence, impact, and a minimal fix.

<untrusted_code>
{code}
</untrusted_code>
"""
```

**What it does:** checks simple input constraints and produces a repeatable task prompt.

**Limitation:** this function does not sanitise sensitive code, enforce permissions, or guarantee prompt-injection resistance. Apply those controls in the application.

## 3. Avoid unsafe template construction

- Validate variable type and length.
- Escape or encode data according to the target format.
- Keep trusted policy separate from user data.
- Do not allow users to change hidden policy variables.
- Do not place secrets into prompt templates.
- Use SDK message structures where supported instead of building complex raw message strings.

## 4. Keep templates small and focused

One prompt should have one primary job. Avoid creating a universal template that tries to classify, debug, summarise, generate code, and make business decisions in one call.

Split complex jobs into explicit, testable stages where appropriate.

## 5. Store prompts as assets

A repository can contain:
- prompt name and purpose,
- owner,
- version,
- variables and constraints,
- expected output schema,
- known limitations,
- evaluation dataset,
- change history.

Avoid committing secrets or private business data in prompt examples.

## Practice exercise

Create reusable templates for a code explainer and a bug-triage assistant. Add input validation, variable documentation, and sample test cases. Change the audience from beginner to senior engineer and compare whether the output changes appropriately.

## Self-check

1. Why use a prompt template?
2. What makes a variable name useful?
3. Why does length validation not equal security?
4. Why should one prompt have one primary job?
5. What metadata should be stored with a prompt?

## Further reading

- [OpenAI prompt engineering guide](https://platform.openai.com/docs/guides/prompt-engineering)
