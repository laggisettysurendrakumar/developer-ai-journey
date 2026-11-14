# Linux Production Support Playbook

A beginner-friendly, hands-on path for learning Linux from the command line and applying it to application support and DevOps work. I created these notes to understand commands, practise them safely, and connect Linux concepts to the servers that run real applications.

> **Who this is for:** Developers and learners who are new to Linux or have used only a few terminal commands. No previous Linux administration experience is required.

## Start here

1. Read [`00-linux-fundamentals-for-absolute-beginners.md`](00-linux-fundamentals-for-absolute-beginners.md) first. It explains the basic ideas and vocabulary.
2. Follow [`01-seven-day-learning-plan.md`](01-seven-day-learning-plan.md). Spend about 1–2 hours each day; repeat a day if the concepts are new.
3. Keep [`02-command-cheatsheet.md`](02-command-cheatsheet.md) nearby when practising. It is a reference, not a document to memorise.
4. Complete [`04-hands-on-labs.md`](04-hands-on-labs.md) using a free browser lab or a disposable Linux environment.
5. Read [`03-production-incident-runbook.md`](03-production-incident-runbook.md) to learn a safe, repeatable investigation process.
6. Test yourself with [`05-linux-troubleshooting-scenarios.md`](05-linux-troubleshooting-scenarios.md). Try to investigate before reading the suggested approach.

## What I should be able to do

By the end of this learning path, I should be able to:
- Explain what Linux, a distribution, a kernel, a terminal, a shell, and Bash are.
- Navigate directories and create, inspect, search, and read files.
- Understand users, groups, ownership, permissions, and why `sudo` needs care.
- Inspect processes, services, ports, logs, CPU, memory, disk space, and inodes.
- Separate DNS, TCP, TLS, HTTP, application, and dependency failures.
- Collect evidence and write a clear incident update without making unapproved changes.
- Recognise where Docker and Kubernetes fit into the application runtime.

## Suggested practice setup

Choose one option:
- **No installation:** use the free online practice environments listed in `04-hands-on-labs.md`. Availability and free limits can change, so check each site's current terms.
- **Windows computer:** use Ubuntu on WSL2 if installation is permitted on your machine.
- **Virtual machine:** use a disposable Ubuntu VM for exercises that need more control.
- **Work environment:** use only a company-approved non-production host and follow access and change policies.

Start with read-only commands. Do not use a public browser terminal for company credentials, source code, private keys, tokens, customer data, or internal hostnames.

## How I will learn each command

For every command, answer these questions:
1. **What does it do?** Explain it in plain language.
2. **Why would I use it?** Identify the problem it helps solve.
3. **What do its options mean?** Understand flags such as `-l`, `-a`, and `-h`.
4. **What output should I expect?** Compare the result with the examples and note what differs.
5. **What could go wrong?** Understand errors such as `command not found`, `No such file or directory`, and `Permission denied`.
6. **Is it safe?** Decide whether it only reads information or changes the system.
7. **Where is it useful?** Connect it to an application or DevOps support task.

## Learning map

| Document | Purpose |
|---|---|
| [00 – Linux fundamentals](00-linux-fundamentals-for-absolute-beginners.md) | Understand the basic concepts before using commands |
| [01 – Seven-day plan](01-seven-day-learning-plan.md) | Learn the topics in a sensible order |
| [02 – Command cheat sheet](02-command-cheatsheet.md) | Look up syntax, purpose, examples, and safety |
| [03 – Incident runbook](03-production-incident-runbook.md) | Follow a structured production investigation |
| [04 – Hands-on labs](04-hands-on-labs.md) | Practise commands in safe environments |
| [05 – Troubleshooting scenarios](05-linux-troubleshooting-scenarios.md) | Apply the knowledge to realistic application problems |

## Production safety rules

- Confirm the host, account, environment, and time before investigating.
- Prefer read-only inspection before considering a change.
- Never run a command you do not understand, especially with `sudo`.
- Do not use `chmod -R 777`, broad recursive ownership changes, or destructive deletion as shortcuts.
- Do not restart services, kill processes, modify accounts, change firewall rules, or install packages without approval.
- Redact secrets and customer information from command output before sharing it.
- Follow your organisation's runbooks, access controls, and change process. This material does not replace them.

## A simple troubleshooting sequence

1. Define the symptom, impact, start time, and affected environment.
2. Confirm the host and identity.
3. Check recent deployments and configuration changes.
4. Follow the request path: DNS → network/TCP → TLS → proxy/load balancer → application → dependency.
5. Collect timestamps, logs, metrics, status, and request IDs.
6. Separate facts from assumptions and escalate when the next action needs approval.
7. Verify recovery from the user's point of view and document the outcome.

## Completion checklist

- [ ] I can explain the basic Linux terms without reading the notes.
- [ ] I can navigate and inspect files safely.
- [ ] I can explain file permissions and directory traversal.
- [ ] I can inspect a process, service, port, and log.
- [ ] I can distinguish DNS, TCP, TLS, and HTTP failures.
- [ ] I can investigate disk and resource usage without deleting data.
- [ ] I can complete the troubleshooting scenarios and explain my evidence.
