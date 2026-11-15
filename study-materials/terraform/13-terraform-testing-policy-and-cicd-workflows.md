# 13 — Terraform Testing, Policy, and CI/CD Workflows

## Why automate Terraform review?

Infrastructure changes can create cost, exposure, downtime, or data loss. CI should catch syntax and policy problems before an authorised identity applies a change.

## 1. A useful pipeline

```text
Pull request
    |
    v
terraform fmt -check
    |
    v
terraform init -backend=false
    |
    v
terraform validate
    |
    v
Static security / policy checks
    |
    v
Generate plan in controlled environment
    |
    v
Publish plan for review
    |
    v
Approval gate
    |
    v
Apply approved plan
    |
    v
Verify resources and application
```

The exact CI commands and backend strategy depend on your setup. A validation-only job can initialise providers without configuring a production backend. Plan jobs need carefully controlled credentials and state access.

## 2. Formatting and validation

```bash
terraform fmt -check -recursive
terraform init -backend=false
terraform validate
```

Formatting checks style; validation checks configuration structure and provider/module consistency. Neither proves that the deployed infrastructure is secure or will meet business requirements.

## 3. Static security and policy

Possible checks include:
- secret scanning,
- provider/module version review,
- vulnerability scanning,
- misconfiguration checks,
- policy-as-code,
- cost estimates,
- approved region and SKU rules.

Choose tools based on organisation policy and maintain their versions. Review findings rather than blindly suppressing alerts.

## 4. Plan artifacts and approval

A saved plan can contain sensitive data. Protect its storage and access. Ensure that the plan being applied is the exact reviewed artifact and that no unreviewed changes have altered its context.

Use protected environments or approval workflows for production. Do not put production credentials into pull-request jobs from untrusted forks.

## 5. Least-privilege pipeline identity

Use a dedicated deployment identity, scope it carefully, and separate non-production from production permissions. Prefer short-lived federation or managed identity patterns where supported. Store unavoidable credentials in an approved secret service.

## 6. Example pipeline stages

A provider-neutral CI pipeline can run:
1. formatting,
2. initialisation,
3. validation,
4. security/policy scans,
5. plan,
6. review and approval,
7. apply,
8. smoke tests,
9. deployment summary.

Use concurrency controls so two pipelines do not try to modify the same state at once.

## Practice exercise

Write a CI/CD design for dev and prod Terraform roots. Define the pull-request checks, plan artifact protection, approval owner, apply identity, concurrency handling, and rollback/recovery process. Explain which checks can run without cloud credentials.

## Self-check

1. What does `terraform fmt -check` verify?
2. Why is `terraform validate` not a security review?
3. Why protect plan artifacts?
4. Why should production apply use a dedicated identity?
5. How can CI avoid concurrent operations on one state?

## Further reading

- [Terraform CLI](https://developer.hashicorp.com/terraform/cli)
- [Terraform automation](https://developer.hashicorp.com/terraform/tutorials/automation)
- [Terraform security](https://developer.hashicorp.com/terraform/tutorials/security)
