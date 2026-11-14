# Hands-on Linux Labs

Run these only on a disposable Linux VM or approved non-production environment. Do not use production systems to practise account changes, permission changes, process termination, disk filling, or service restarts.

## Lab 1 — Files and logs

```bash
mkdir -p ~/prod-support-lab/logs ~/prod-support-lab/config
cat > ~/prod-support-lab/logs/app.log <<'LOG'
2026-11-14T10:00:00Z INFO Application started
2026-11-14T10:01:00Z WARN Retry attempt 1
2026-11-14T10:02:00Z ERROR Database timeout requestId=abc-001
2026-11-14T10:03:00Z ERROR Database timeout requestId=abc-002
2026-11-14T10:04:00Z INFO Health check passed
LOG
printf '{"service":"orders","environment":"lab","enabled":true}\n' > ~/prod-support-lab/config/app.json
```

Tasks:

1. Use `file`, `stat`, `less`, and `tail` on the log.
2. Find lines containing `ERROR` and count them.
3. Use `jq` to read the `service` and `enabled` values if `jq` is installed.
4. Archive the lab directory and list the archive contents without extracting.
5. Explain why none of these commands required `sudo`.

## Lab 2 — Permissions and ACLs

```bash
mkdir -p ~/prod-support-lab/shared
printf 'internal lab data\n' > ~/prod-support-lab/shared/sample.txt
chmod 640 ~/prod-support-lab/shared/sample.txt
chmod 750 ~/prod-support-lab/shared
ls -ld ~/prod-support-lab/shared
ls -l ~/prod-support-lab/shared/sample.txt
namei -l ~/prod-support-lab/shared/sample.txt
```

Tasks:

1. Interpret owner, group, and other permissions.
2. Explain whether a user outside the owner/group can list the directory or read the file.
3. If ACL tools are installed, inspect with `getfacl`.
4. Do not change permissions to `777`. Write down the minimum permission a service identity would need for the intended operation.

## Lab 3 — Users and groups (optional; requires a disposable VM and admin rights)

Only perform this lab in a disposable VM where you are authorized to administer local accounts.

```bash
sudo groupadd supportlab
sudo useradd -m -s /bin/bash supportuser
sudo usermod -aG supportlab supportuser
id supportuser
getent group supportlab
```

Tasks:

1. Explain the output of `id`.
2. Explain why `-aG` is used.
3. Log in as the test user (or start a fresh login session) to verify group membership.
4. Clean up only after confirming this is the test VM and the test account owns no needed files. Account deletion is intentionally not shown here; follow your distro's man page and lab procedure.

## Lab 4 — Process and service investigation

Use an existing lab service or start a temporary HTTP server in the lab:

```bash
python3 -m http.server 8088 --bind 127.0.0.1
```

In another terminal:

```bash
pgrep -af 'http.server'
ss -lntup | grep ':8088'
curl -I --max-time 5 http://127.0.0.1:8088/
```

Tasks:

1. Identify the process ID and listening address.
2. Explain why binding to `127.0.0.1` makes it local to the host.
3. Stop the test server with Ctrl+C in the terminal where it is running.

## Lab 5 — Networking layers

Use a public endpoint only if permitted by your network policy, or use an internal lab endpoint.

```bash
getent hosts example.com
nc -vz example.com 443
curl -I --connect-timeout 5 --max-time 10 https://example.com
```

Tasks:

1. Record DNS, TCP, TLS/HTTP observations separately.
2. Repeat with an intentionally invalid hostname and compare the failure.
3. Explain why a successful DNS lookup does not prove the web application is healthy.

## Lab 6 — Disk and inode investigation

Tasks:

1. Inspect `df -hT` and `df -ih` on the lab VM.
2. Compare `du -xhd1 /var` with the filesystem usage shown by `df` (if you have permission).
3. Learn the meaning of a deleted-but-open file using documentation or a controlled lab process.
4. Do not deliberately fill the VM disk unless it is a disposable VM with a dedicated small test volume.

## Lab 7 — Write a mini incident report

Scenario: A web application health check is returning 503, but the host is reachable.

Write a report using the template in `03-production-incident-runbook.md`. Include:

- What you would inspect first
- Commands you would use and why
- Evidence that would distinguish host, process, port, TLS, application, and dependency issues
- Which actions require approval
- How you would verify recovery

## Lab completion checklist

- [ ] I can confirm the host, user, OS, time, and working directory.
- [ ] I can find files and extract evidence from logs without editing them.
- [ ] I can explain owner/group/other permissions and directory traversal.
- [ ] I can inspect users, groups, ACLs, and sudo rights.
- [ ] I can inspect a process, systemd service, listening port, and journal logs.
- [ ] I can isolate DNS, TCP, TLS, and HTTP failures.
- [ ] I can diagnose disk bytes vs inode exhaustion.
- [ ] I can write a clear incident update with facts, hypotheses, actions, and next steps.

## Free online environments for Linux practice

You do not need to buy a course or install Linux to begin. These websites offer free learning material or free practice environments. Some platforms may have optional paid courses or usage limits, so check the terms shown on the site before starting a lab.

### 1. WebTerm — guided browser-based practice

Website: https://webterm.app/en

Use this when you are new to the terminal and want to learn commands with guidance. Work through the command examples and notice how the terminal responds. It is a good place to get comfortable before attempting the more challenging labs below.

**Try these first:**
- `pwd` — see the directory you are currently working in.
- `ls -la` — list files, including hidden files, with details.
- `mkdir practice` — create a practice directory.
- `cd practice` — move into that directory.
- `touch notes.txt` — create an empty file.
- `echo "Linux practice" > notes.txt` — write a line into the file.
- `cat notes.txt` — display the file contents.

### 2. OverTheWire: Bandit — free Linux command-line challenges

Website: https://overthewire.org/wargames/bandit/

Bandit is a challenge-based learning game. Each level gives you a task to solve using Linux commands. It is especially useful after you know basic navigation and file commands. Read the level instructions carefully and try to solve each task yourself before looking for hints.

**Practise:** `ssh`, `ls`, `cd`, `cat`, `file`, `find`, `grep`, pipes, redirection, and file permissions.

**Beginner tip:** Start at Level 0. Keep notes about the command you used, what it did, and why it solved the level. Bandit is a challenge environment, not a replacement for learning the meaning of each command.

### 3. Killercoda — free interactive Linux and DevOps scenarios

Website: https://killercoda.com/

Use Killercoda when you want a browser-based terminal in a temporary practice environment. Browse its Linux and DevOps scenarios and choose beginner-friendly exercises first. It is also useful later for Docker and Kubernetes practice.

**Practise:** navigating a Linux shell, inspecting files and processes, and following guided infrastructure scenarios.

**Important:** A lab environment is temporary. Save your notes and any commands you want to keep before closing the session.

### 4. Webminal — online Linux terminal

Website: https://www.webminal.org/

Webminal provides an online shell for practising Linux commands. Use it for basic terminal exercises when you want to type commands rather than only read about them. Account requirements and current free usage limits may change; check the website before relying on it for a long session.

### 5. Linux Journey — free lessons and explanations

Website: https://linuxjourney.com/

Linux Journey is best used as a learning reference alongside a terminal. It explains Linux topics such as the command line, permissions, processes, packages, and filesystems. Read a lesson, then run the related commands in WebTerm, Killercoda, or your own disposable VM.

## A simple free practice path

| Stage | Use | What to do |
|---|---|---|
| First session | WebTerm | Practise `pwd`, `ls`, `cd`, `mkdir`, `touch`, `cat`, and `cp`. |
| Learn the concepts | Linux Journey | Read about files, permissions, processes, and networking; try each example yourself. |
| Build confidence | Webminal or Killercoda | Repeat the commands in a real shell environment. |
| Test your skills | OverTheWire Bandit | Complete Level 0 onward and write down how you solved each challenge. |
| DevOps practice | Killercoda | Move on to Linux, Docker, and Kubernetes scenarios when the basics feel comfortable. |

## How to practise each command

Do not memorise a command without understanding it. For every exercise, write down:

1. **What does the command do?** Explain it in your own words.
2. **Why would an engineer use it?** Describe the problem it helps solve.
3. **What does each option mean?** For example, in `ls -la`, understand both `-l` and `-a`.
4. **What output did you get?** Record the important part, not just “it worked”.
5. **What could go wrong?** Try a harmless example with a missing file or invalid path and understand the error.
6. **Where might this be useful in DevOps?** Connect the command to a real task such as checking logs, investigating a service, or troubleshooting disk usage.

Example — `grep`:

- **What:** Searches text for lines that match a pattern.
- **Why:** An engineer may need to find errors in a large application log quickly.
- **Try:** `grep "ERROR" ~/prod-support-lab/logs/app.log`
- **DevOps use case:** Find error messages before investigating a production incident. Practise on sample logs or an approved non-production system only.

## Safety rules for online labs

- Use only the terminal and resources provided by the practice platform, or a disposable VM you control.
- Do not enter work credentials, access tokens, passwords, private keys, customer data, or company files into a public practice website.
- Do not run commands copied from the internet without understanding what they do.
- Avoid destructive commands such as recursive deletion, disk-filling commands, or broad permission changes. Use the safe lab exercises in this repository instead.
- Never test against production systems or public systems without explicit authorization.
- Free plans, availability, and platform features can change. Check each website's current terms and limits.
