# Production Incident Runbook — Linux Host and Application

This is a generic investigation framework. Your team's service-specific runbooks, access model, severity definitions, and change process always take precedence.

## A. First five minutes

1. Read the incident description and confirm severity, customer impact, start time, and affected region/environment.
2. Confirm the target before doing anything:

   ```bash
   hostname -f
   whoami
   id
   pwd
   date -Is
   cat /etc/os-release
   ```

3. Check whether the issue affects one instance or many. Compare with a known-good instance only when authorized.
4. Check recent deployments, configuration changes, secrets/certificates, network changes, access changes, and dependency incidents.
5. Start an evidence timeline. Preserve timestamps and timezone; avoid changing the system before collecting initial evidence.

## B. Fast host snapshot (mostly read-only)

```bash
hostname -f
date -Is
uptime
free -h
df -hT
df -ih
ip -br addr
ip route
ss -s
systemctl list-units --type=service --state=failed
```

Use only commands available on that host. Some commands may require privilege for complete output. Do not elevate privileges unless necessary and authorized.

## C. Application appears down or health check fails

1. Confirm the exact URL, expected status, time, and request/correlation ID.
2. Inspect service state:

   ```bash
   systemctl status <service> --no-pager
   systemctl is-active <service>
   systemctl show <service> -p User -p Group -p ExecStart -p Restart
   ```

3. Inspect logs around the incident:

   ```bash
   journalctl -u <service> --since '30 minutes ago' --no-pager
   ```

4. Check process and listening port:

   ```bash
   pgrep -af '<process-pattern>'
   ss -lntup
   ```

5. Test the documented local health endpoint and the external endpoint separately.
6. Check downstream dependencies (database, cache, queue, API, identity provider) using approved health checks.
7. Review the last deployment/configuration/secret/certificate change.
8. Restart or roll back only through the approved runbook/change path. Capture pre-change evidence first.

## D. Permission denied / application cannot read or write a file

Investigate in this order:

1. Identify the actual process user and group from the service unit/process metadata.
2. Check the path and every parent directory:

   ```bash
   namei -l <path>
   stat <path>
   getfacl <path>
   ```

3. Check supplementary groups and account source:

   ```bash
   id <service-user>
   getent passwd <service-user>
   getent group <required-group>
   ```

4. Check whether the path is a mount, symlink, container volume, or network filesystem.
5. Compare owner, group, permissions, ACLs, and mount options against the approved configuration.
6. Check deployment scripts, file copy/creation steps, umask, and recent access changes.
7. Propose the narrowest corrective change. Do not use `chmod 777` or recursively change ownership to make the error disappear.

## E. Disk full / inode exhaustion

```bash
df -hT
df -ih
du -xhd1 <mount-or-parent-path> 2>/dev/null | sort -h
find <path> -xdev -type f -size +500M -print
journalctl --disk-usage
sudo lsof +L1
```

Check whether the issue is bytes, inodes, a deleted-open file, log growth, temporary data, container layers, or a backup/cache path. Confirm retention and backup policy before deleting anything. Use approved log rotation or cleanup procedures.

## F. High CPU / memory / load / I/O

```bash
uptime
free -h
vmstat 1 5
ps -eo pid,ppid,stat,%cpu,%mem,etime,cmd --sort=-%cpu | head
ps -eo pid,ppid,stat,%cpu,%mem,etime,cmd --sort=-%mem | head
iostat -xz 1 5
```

Look for a specific process, recent traffic/deployment change, memory growth, swapping, blocked I/O, or a dependency causing thread/connection buildup. Avoid repeated expensive diagnostics on an already overloaded host.

## G. Network / dependency problem

Run tests in layers:

1. **Name resolution:** `getent hosts <host>` or `dig <host>`.
2. **Routing/interface:** `ip -br addr`, `ip route`.
3. **TCP reachability:** `nc -vz <host> <port>`.
4. **TLS:** `openssl s_client -connect <host>:443 -servername <host>`.
5. **HTTP:** `curl -v --connect-timeout 5 --max-time 10 https://<host>/<path>` (review output for secrets before sharing).
6. **Application:** correlate gateway and application logs using timestamp and request ID.

Record whether the failure is DNS, timeout, refusal, TLS, authentication, authorization, HTTP 5xx, or application-level timeout.

## H. User, group, SSH, or sudo access issue

- Verify the user's identity and groups with `id`, `getent passwd`, and `getent group`.
- Check whether the account is local or directory-managed.
- Inspect account status/expiry with approved tools such as `passwd -S` and `chage -l` where supported.
- Check `sudo -l` for effective sudo rights.
- Inspect SSH/authentication logs according to the distribution and central logging setup.
- Use the corporate IAM/AD/SSO process for entitlement changes. Do not create emergency local accounts or edit sudoers outside the approved break-glass process.
- Never ask a user to send a password or private key.

## I. Scheduled job did not run

Check:

- Cron entry or systemd timer and its schedule/timezone
- The configured execution user and group
- Script executable bit, interpreter, working directory, and parent-directory permissions
- Environment variables (cron often has a smaller environment than an interactive shell)
- Log destination, exit code, and previous successful run
- Dependency availability, lock files, overlapping executions, and resource constraints

Commands to inspect include `crontab -l`, `systemctl list-timers --all`, `systemctl status <timer-or-service>`, and relevant logs.

## J. Container / Kubernetes incident (if applicable)

Docker:

```bash
docker ps -a
docker logs --since 30m <container>
docker inspect <container>
docker stats --no-stream
```

Kubernetes:

```bash
kubectl config current-context
kubectl get pods -n <namespace> -o wide
kubectl describe pod <pod> -n <namespace>
kubectl logs <pod> -n <namespace> --since=30m
kubectl logs <pod> -n <namespace> --previous
kubectl get events -n <namespace> --sort-by=.lastTimestamp
```

Confirm context, namespace, workload, and pod identity. Use `exec`, delete, rollout, scale, patch, and apply operations only under an approved procedure.

## K. Incident update template

```text
Current status: Investigating / Mitigating / Monitoring / Resolved
Impact: [who/what is affected; scope]
Environment/region: [approved identifier]
Start time: [timestamp + timezone]
Affected service/host: [service and approved host identifier]
Symptoms: [exact error/status]
Evidence collected: [logs, metrics, command outputs, request IDs]
Findings: [facts separated from hypotheses]
Likely failing layer: [DNS / TCP / TLS / gateway / app / dependency / host]
Actions taken: [approved action and timestamp, or none]
Risk/rollback: [impact and rollback plan]
Next step/owner: [action and responsible team]
Next update: [time]
```

## L. Handover checklist

- [ ] Current impact and scope are stated.
- [ ] Timestamps include timezone.
- [ ] Exact host/service/environment are identified without exposing restricted data.
- [ ] Evidence and commands are recorded; secrets and personal data are redacted.
- [ ] Recent changes and what has been ruled out are listed.
- [ ] Every state-changing action has approval/change reference.
- [ ] Current owner, next step, and next update time are clear.
- [ ] Recovery is verified from the user/request perspective.

## Beginner walkthroughs

The examples below show an investigation sequence. They are not instructions to make production changes. Use your organisation's service-specific runbook and approval process for any action that changes state.

### Walkthrough 1 — An ASP.NET Core application is not starting

**Situation:** A deployment completed, but the application health check fails.

**What I want to learn:** A service is a managed background process. I need to establish whether it is running, what account it uses, and what its logs say.

**Step 1: Confirm the target and time.**

```bash
hostname -f
whoami
date -Is
```

These commands help me avoid investigating the wrong host and give me a timestamp for the incident timeline.

**Step 2: Inspect the service.**

```bash
systemctl status <service> --no-pager
systemctl is-active <service>
systemctl show <service> -p User -p Group -p ExecStart
```

Look for the service state, recent exit information, configured executable/command, and service account. A service may be named differently from the application. Confirm the correct unit name with the team.

**Step 3: Read recent service logs.**

```bash
journalctl -u <service> --since '30 minutes ago' --no-pager
```

Look for the first meaningful error, not only the final failure message. Examples include a missing configuration value, an inaccessible file, a port already in use, or a failed dependency connection.

**Step 4: Check process and port evidence.**

```bash
pgrep -af dotnet
ss -lnt
```

`pgrep` can help identify a matching process; `ss -lnt` shows listening TCP sockets. Neither alone proves that the application is healthy.

**Interpretation:** If the service is failed, use the journal to identify the reason. If it is active but the health check fails, continue through the local endpoint, proxy, application logs, and dependencies. Do not restart it until the approved procedure says to do so.

### Walkthrough 2 — The application cannot read appsettings.json

**Situation:** The application reports `Permission denied` while opening a configuration file.

**What I want to learn:** Linux checks access using the process identity and the permissions on the file and every parent directory.

**Step 1: Find the service identity.**

```bash
systemctl show <service> -p User -p Group
```

Do not assume the service runs as the account I used to log in.

**Step 2: Inspect the complete path.**

```bash
namei -l /path/to/appsettings.json
stat /path/to/appsettings.json
```

`namei -l` displays permissions for each path component. A process needs search/traverse permission on parent directories as well as the appropriate permission on the file.

**Step 3: Compare identity and access.**

```bash
id <service-user>
getfacl /path/to/appsettings.json
```

Check owner, group, mode bits, ACL entries, and the service user's group membership. Also consider whether the path is a symlink, mount, container volume, or network filesystem.

**Interpretation:** Do not solve this by applying `chmod 777`. Propose the narrowest access change that matches the intended service identity and have it reviewed through the normal change process.

### Walkthrough 3 — The application returns HTTP 503

**Situation:** The host responds to network checks, but users receive HTTP 503.

**What I want to learn:** A reachable host does not guarantee a healthy application. I should test the request path in layers.

**Step 1: Confirm the endpoint and response.**

```bash
curl -I --connect-timeout 5 --max-time 10 https://<approved-host>/<health-path>
```

Record the HTTP status, timestamp, and any request/correlation ID. Avoid sharing verbose output if it contains tokens or sensitive headers.

**Step 2: Check name resolution and TCP reachability.**

```bash
getent hosts <approved-host>
nc -vz <approved-host> 443
```

Name resolution and TCP connection are separate checks. A successful DNS lookup does not prove that port 443 accepts connections.

**Step 3: Check TLS and the local application endpoint.**

```bash
openssl s_client -connect <approved-host>:443 -servername <approved-host> </dev/null
curl -I --connect-timeout 5 --max-time 10 http://127.0.0.1:<app-port>/<health-path>
```

Only test a local endpoint if the service documentation confirms its address, port, and health path. A local response can help distinguish proxy/gateway problems from application problems.

**Step 4: Correlate proxy and application logs.**

Use the relevant service's `journalctl` and application log commands. Match timestamps and request IDs across layers. Check recent deployments, configuration, certificates, and dependency health.

**Interpretation:** A 503 can originate from a proxy, load balancer, application, or dependency. Do not assume the application itself is the source until evidence identifies the failing layer.

## How to read sample command output

When documenting an investigation, include:
- The exact command and time it was run (with timezone).
- The relevant output lines, with secrets and sensitive identifiers removed.
- What the output proves.
- What it does not prove.
- The next check and why it is the safest useful step.

Example: `systemctl is-active <service>` returning `active` proves systemd considers the unit active at that moment. It does not prove that the application can serve requests successfully.

## Beginner escalation rule

Stop and ask the service owner or platform team before changing permissions, restarting or stopping a service, killing a process, editing configuration, changing network policy, installing packages, or modifying accounts. Escalation with clear evidence is a successful support outcome; I do not need to make an unauthorised change to complete an investigation.
