# Linux Troubleshooting Scenarios for Application Developers

I use these scenarios to practise turning Linux commands into a structured investigation. The examples are deliberately generic; real services, ports, paths, and approval rules must come from the service owner and the organisation's runbooks.

## How to work through a scenario

1. Read the symptom and decide what evidence is missing.
2. State your first hypothesis, but do not treat it as a fact.
3. Choose the safest read-only command that can test that hypothesis.
4. Explain each command before running it.
5. Compare the output with the expected observations.
6. Identify what the evidence proves and what it does not prove.
7. Write the next step and say whether it needs approval.

Use a disposable Linux environment or approved non-production system. Replace placeholders only with values supplied for your lab.

## Scenario 1 — ASP.NET Core service does not start

**Symptom:** After deployment, the service is reported as unavailable.

**Questions to answer:** Is the service installed? Is it active or failed? Which account runs it? What error occurred first?

**Start with these checks:**

```bash
systemctl status <service> --no-pager
systemctl is-active <service>
systemctl show <service> -p User -p Group -p ExecStart
journalctl -u <service> --since '30 minutes ago' --no-pager
pgrep -af dotnet
```

**How to interpret:** A failed unit plus a journal error narrows the investigation. A missing process is not by itself the root cause. Look for configuration errors, missing files, permission failures, port conflicts, runtime problems, or unavailable dependencies.

**Do not do this automatically:** Do not restart the service or edit its unit/configuration just because it is down. Capture evidence and follow the change process.

## Scenario 2 — Application cannot read appsettings.json

**Symptom:** The application log contains `Permission denied` for a configuration path.

**Questions to answer:** Which user runs the process? Can that user traverse every parent directory? Can it read the file? Are ACLs or mounts involved?

```bash
systemctl show <service> -p User -p Group
namei -l /path/to/appsettings.json
stat /path/to/appsettings.json
getfacl /path/to/appsettings.json
id <service-user>
```

**How to interpret:** Directory execute/search permission is required along the path. The file's owner and mode are only part of the access decision; group membership, ACLs, mount options, symlinks, and security controls may also matter.

**Do not do this:** Never use `chmod 777` as a shortcut. Recommend the narrowest permission change and obtain approval.

## Scenario 3 — Users receive HTTP 503

**Symptom:** The host is reachable, but the application endpoint returns HTTP 503.

**Questions to answer:** Does DNS resolve? Can the client reach the port? Does TLS succeed? Does the proxy reach the app? Is the app healthy? Are dependencies available?

```bash
getent hosts <approved-host>
nc -vz <approved-host> 443
curl -I --connect-timeout 5 --max-time 10 https://<approved-host>/<health-path>
ss -lnt
journalctl -u <service> --since '30 minutes ago' --no-pager
```

**How to interpret:** DNS success does not prove TCP connectivity. TCP success does not prove TLS or HTTP health. A 503 may be returned by a gateway, proxy, application, or dependency. Correlate timestamps and request IDs across layers.

## Scenario 4 — Database connection times out

**Symptom:** The application starts, but requests fail while accessing a database.

**Questions to answer:** Is the database hostname resolving? Is the expected port reachable from the application host? Did a network, firewall, secret, or database change occur?

```bash
getent hosts <database-host>
nc -vz <database-host> <database-port>
```

Use only the approved database hostname and port. Do not put passwords or connection strings containing credentials into shell history, screenshots, or public issue reports.

**How to interpret:** A DNS failure points toward name resolution. A timeout may indicate filtering, routing, or an unresponsive destination. A successful TCP connection does not prove that database authentication or SQL queries work. Application logs and the database team's health evidence are still needed.

## Scenario 5 — Filesystem is nearly full

**Symptom:** Monitoring reports that a filesystem is above its usage threshold.

**Questions to answer:** Which mount is full? Is it bytes or inodes? Which directory is consuming space? Are deleted files still held open?

```bash
df -hT
df -ih
du -xhd1 <approved-mount-path> 2>/dev/null | sort -h
```

**How to interpret:** `df -hT` reports filesystem byte usage; `df -ih` reports inode usage. `du` helps identify directory usage but may not account for deleted-open files or all filesystem metadata.

**Do not do this:** Do not delete logs, container layers, or application files just to reduce the percentage. Check retention, ownership, backup, and cleanup procedures and obtain approval.

## Scenario 6 — High CPU usage

**Symptom:** The host is responding slowly and monitoring shows high CPU.

**Questions to answer:** Is the load sustained? Which processes are using CPU? Did traffic or a deployment change? Is a dependency causing retries or a busy loop?

```bash
uptime
top
ps -eo pid,ppid,stat,%cpu,%mem,etime,cmd --sort=-%cpu | head
```

**How to interpret:** `top` is a live view; the `ps` command gives a snapshot sorted by CPU usage. One snapshot does not establish a trend. Compare several observations and application metrics.

**Do not do this:** Do not kill a process based only on its CPU percentage. Identify ownership and impact, then follow the incident procedure.

## Scenario 7 — Docker container keeps restarting

**Symptom:** A container repeatedly exits and restarts.

**Questions to answer:** What is its state and exit code? What do its recent logs say? Was it killed for resource limits? Is its configuration or dependency failing?

```bash
docker ps -a
docker logs --since 30m <container>
docker inspect <container>
docker stats --no-stream
```

**How to interpret:** Container logs may reveal an application startup error. Inspect state, exit information, health status, mounts, environment configuration, and resource limits. Be careful: inspect output may contain secrets. Redact it before sharing.

Do not remove, restart, or recreate a production container without approval and the service's deployment procedure.

## Scenario 8 — Kubernetes pod is not becoming ready

**Symptom:** A workload's pod is Pending, CrashLoopBackOff, or not Ready.

**Questions to answer:** Am I in the correct cluster and namespace? What do pod events say? Did the previous container instance log an error?

```bash
kubectl config current-context
kubectl get pods -n <namespace> -o wide
kubectl describe pod <pod> -n <namespace>
kubectl logs <pod> -n <namespace> --since=30m
kubectl logs <pod> -n <namespace> --previous
kubectl get events -n <namespace> --sort-by=.lastTimestamp
```

**How to interpret:** Events can reveal scheduling, image-pull, probe, volume, or resource issues. Previous logs can help when a container has restarted. Confirm the context and namespace before every investigation.

Do not use `delete`, `apply`, `patch`, `scale`, `exec`, or rollout restart commands unless the action is authorised.

## Write your own incident note

```text
Scenario:
Impact and scope:
Environment and time (include timezone):
Facts observed:
Commands run and why:
Relevant output (redacted):
Hypotheses still being tested:
Likely failing layer:
Actions requiring approval:
Next step and owner:
How recovery will be verified:
```

## Final self-assessment

- [ ] I did not jump from a symptom to an unverified root cause.
- [ ] I used read-only checks first.
- [ ] I can explain every command and important option I ran.
- [ ] I checked the correct host, service, namespace, or endpoint.
- [ ] I separated DNS, TCP, TLS, HTTP, application, and dependency evidence.
- [ ] I redacted sensitive output.
- [ ] I identified actions that require approval.
- [ ] I can explain what would prove that the service recovered.
