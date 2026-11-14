# My 7-Day Linux Learning Plan for Production Support

I created this guide as a practical learning path for developers who are comfortable building applications but are new to working directly on Linux servers. I have kept the explanations simple because I want to understand what a command does, why I need it, and what its output tells me—not just memorise its name.

My goal for this week is to become comfortable investigating common production issues: an application that will not start, a file the application cannot read, a service that stopped, a server running out of disk space, or a network connection that is failing.

I do not expect to become a Linux administrator in seven days. I want to learn how to inspect a server safely, collect useful evidence, understand the likely cause, and know when to involve the infrastructure or security team.

---

## How I will study

- **Time:** Around 2 hours each day. I can spend more time on a topic that is new to me.
- **Approach:** Read the explanation, run the example in a Linux VM or approved lab, change one thing at a time, and write down what happened.
- **Practice environment:** Ubuntu in WSL2, a disposable Linux virtual machine, or a company-approved non-production server.
- **Safety rule:** I will not practise account changes, permission changes, deleting files, restarting services, firewall changes, or resource-exhaustion tests on a production server.

For every command, I will answer four questions:

1. What does it do?
2. Why would I need it while supporting an application?
3. When would I use it?
4. What should I look for in the output?

## Before Day 1: Understand the terminal

A **terminal** is the window where I type commands. The **shell** is the program that reads those commands and runs them. Bash is a common shell on Linux.

A command usually has this structure:

```bash
command [options] [arguments]
```

For example:

```bash
ls -lah /var/log
```

- `ls` is the command. It lists files and folders.
- `-l`, `-a`, and `-h` are options that change what is displayed.
- `/var/log` is the argument. It tells Linux which folder to list.

A few terms I will see often:

| Term | Meaning in plain English |
|---|---|
| Server / host | The computer where an application or service runs |
| Shell | The program that accepts and runs my commands |
| Directory | Another name for a folder |
| Path | The location of a file or folder |
| Process | A program that is currently running |
| Service | A background program managed by the operating system |
| Port | A numbered communication endpoint used by a network application |
| Log | A record of events, warnings, and errors |
| Root user | The most powerful Linux account; it can make system-wide changes |
| sudo | A way for an approved user to run a command with elevated permissions |
| Permission | A rule that says who can read, change, or enter a file or folder |

### Paths I should recognise

- `/` is the top of the Linux directory tree. All normal folders sit under it.
- `/home/surendra` is an example of a user's home folder.
- `/var/log` is a common location for system and application logs.
- `/etc` is commonly used for system and service configuration.
- `/tmp` is used for temporary files.
- `~` is shorthand for my current user's home folder.
- `.` means the current folder.
- `..` means the folder one level above the current folder.

### Three safety habits before running a command

1. Check which server I am connected to with `hostname`.
2. Check which account I am using with `whoami` and `id`.
3. Read a command before pressing Enter. Some commands only show information; others change or delete things.

I will start with commands that only inspect information. I will use commands that change the system only in my lab or after following the company's approval process.

---

# Day 1 — Get comfortable with Linux, folders, files, and logs

**Today's goal:** I should be able to find my way around a Linux server, locate a file, and read an application log without changing anything.

## 1. Identify the server and my account

### `hostname`

**What it does:** Displays the name of the Linux computer I am connected to.

**Why I use it:** Production teams may have development, test, staging, and production servers with similar names. I should confirm the target before doing anything.

**When I use it:** At the start of an SSH session or before investigating an incident.

```bash
hostname
```

### `uname`

**What it does:** Shows information about the Linux kernel, which is the core part of the operating system.

**Why I use it:** It helps me understand the system type and kernel version when investigating compatibility or driver issues.

**When I use it:** When a ticket mentions an operating-system or kernel issue.

```bash
uname -a
```

### `cat /etc/os-release`

**What it does:** Displays the Linux distribution and its version, such as Ubuntu or Red Hat Enterprise Linux.

**Why I use it:** Commands for installing packages and checking services can differ between distributions.

**When I use it:** Before following a Linux instruction from documentation or a runbook.

```bash
cat /etc/os-release
```

### `whoami` and `id`

**What they do:** `whoami` shows the account name I am currently using. `id` shows my user ID, primary group, and supplementary groups.

**Why I use them:** File access depends on the identity of the user running the command. An application may run as a service account rather than as my login account.

**When I use them:** When a command returns “Permission denied” or when I need to understand which permissions apply to my session.

```bash
whoami
id
```

### `uptime`

**What it does:** Shows how long the server has been running and gives a short view of system load.

**Why I use it:** It helps me notice whether a server has recently restarted and whether its load is unusually high.

**When I use it:** At the beginning of a server-health investigation.

```bash
uptime
```

### `date` and `timedatectl`

**What they do:** `date` shows the server's current date and time. `timedatectl` shows time and timezone configuration on systems using systemd.

**Why I use them:** Log timestamps are useful only when I understand the server's time and timezone. A time difference can make two logs appear out of order.

**When I use them:** When comparing an incident time with application logs, monitoring alerts, or another server.

```bash
date
date -Is
timedatectl
```

`timedatectl` may not be available on every Linux distribution.

## 2. Navigate folders

### `pwd`

**What it does:** Prints the full path of the folder I am currently in.

**Why I use it:** Before opening, copying, or deleting a file, I want to know which folder I am working in.

**When I use it:** Whenever I am unsure where I am in the directory structure.

```bash
pwd
```

### `ls`

**What it does:** Lists the files and folders in a location.

**Why I use it:** It gives me a quick view of what is available and helps me check whether a file exists.

**When I use it:** When looking for configuration files, log files, deployment folders, or scripts.

```bash
ls
ls -l
ls -lah
ls -lah /var/log
```

- `-l` shows details such as permissions, owner, size, and modification time.
- `-a` includes hidden files, whose names usually start with a dot.
- `-h` displays file sizes in readable units such as KB or MB.

### `cd`

**What it does:** Changes my current folder.

**Why I use it:** It lets me move to the folder where an application, configuration file, or log is stored.

**When I use it:** When I need to inspect several files in the same location.

```bash
cd /var/log
cd ..
cd ~
```

The first example moves to `/var/log`, the second moves one level up, and the third returns to my home folder.

### `tree`

**What it does:** Displays folders and their contents in a tree-like layout.

**Why I use it:** It makes a folder structure easier to understand than a long list of names.

**When I use it:** When exploring a project or deployment directory in a lab.

```bash
tree -L 2
```

`tree` may not be installed by default. I can use `ls -R` as an alternative, although its output can be harder to read. I should not install tools on a production host without approval.

## 3. Create and inspect files

### `mkdir`

**What it does:** Creates a directory.

**Why I use it:** I can create a separate folder for practice files or temporary investigation notes.

**When I use it:** In my lab, when preparing sample logs or configuration files.

```bash
mkdir linux-practice
mkdir -p linux-practice/logs/archive
```

The `-p` option creates missing parent folders too. It does not fail just because the folder already exists.

### `touch`

**What it does:** Creates an empty file if it does not exist. If the file already exists, it updates its timestamps.

**Why I use it:** It is a quick way to create a sample file for practice.

**When I use it:** In a lab, when I need a file to practise permissions or file commands.

```bash
touch sample.log
```

### `file`

**What it does:** Tries to identify the type of a file from its contents.

**Why I use it:** A file extension does not always tell me what a file really contains.

**When I use it:** When I receive an unfamiliar file from a deployment or support process.

```bash
file sample.log
file /path/to/unknown-file
```

### `stat`

**What it does:** Shows detailed metadata about a file, such as its size, owner, permissions, and timestamps.

**Why I use it:** It helps me check whether a file was recently changed and who owns it.

**When I use it:** When someone reports that a configuration file changed or an application cannot access it.

```bash
stat /path/to/appsettings.json
```

## 4. Read files and logs

### `cat`

**What it does:** Prints the content of a file to the terminal.

**Why I use it:** It is convenient for short files, such as a small configuration file.

**When I use it:** When I need to inspect a small file quickly.

```bash
cat /etc/os-release
```

I avoid using `cat` for very large log files because it can flood the terminal.

### `less`

**What it does:** Opens a file in a viewer that lets me move up and down without printing the entire file to the terminal.

**Why I use it:** Production logs can contain thousands or millions of lines. `less` makes them easier to inspect.

**When I use it:** When I need to read a large log or configuration file.

```bash
less application.log
```

Useful keys while inside `less`:

- `Space` moves down one screen.
- `b` moves up one screen.
- `/error` searches for the word `error`.
- `n` moves to the next match.
- `q` exits.

### `head`

**What it does:** Shows the first lines of a file.

**Why I use it:** It lets me inspect the beginning of a file without printing everything.

**When I use it:** When checking a file's header or the first few log entries.

```bash
head -n 20 application.log
```

### `tail`

**What it does:** Shows the last lines of a file.

**Why I use it:** The newest application events are often written at the end of a log.

**When I use it:** When checking what happened most recently.

```bash
tail -n 100 application.log
tail -f application.log
tail -F application.log
```

- `-n 100` displays the last 100 lines.
- `-f` keeps watching the same file for new lines.
- `-F` is useful when a log file may be rotated and recreated.

To stop following a log, I press `Ctrl+C`. Following a log only reads it; it does not change the application.

## 5. Copy, move, and find files

### `cp`

**What it does:** Copies a file or directory.

**Why I use it:** It can help me make a backup of a file before an approved change.

**When I use it:** In a lab, or during a change where the runbook requires a backup.

```bash
cp appsettings.json appsettings.json.backup
cp -a config-folder config-folder.backup
```

The `-a` option preserves useful file attributes while copying directories. I should confirm the destination and permissions before copying production configuration.

### `mv`

**What it does:** Moves a file or renames it.

**Why I use it:** It helps organize files or rename a file as part of an approved operation.

**When I use it:** In a lab, or when following a reviewed deployment or maintenance procedure.

```bash
mv old-name.txt new-name.txt
```

Unlike a copy, moving a file can remove it from its original location. I should be careful with the source and destination.

### `readlink`

**What it does:** Shows where a symbolic link points. A symbolic link is similar to a shortcut to another file or folder.

**Why I use it:** Applications sometimes use links such as `current` to point to the active deployment folder.

**When I use it:** When checking which release folder an application is using.

```bash
readlink -f /opt/myapp/current
```

### `find`

**What it does:** Searches for files and folders based on conditions such as name, type, size, or modification time.

**Why I use it:** I may know the name of a configuration file but not where it is stored.

**When I use it:** When locating a missing file or finding large or recently changed files.

```bash
find /opt/myapp -type f -name "appsettings.json"
find /var/log -type f -name "*.log"
find /opt/myapp -type f -mtime -1
```

- `-type f` means files.
- `-type d` means directories.
- `-name` matches a name.
- `-mtime -1` finds files modified within roughly the last day.

I start searching in a known application folder rather than searching the entire server. Some folders require extra permissions, so “Permission denied” messages do not necessarily mean the file is absent.

### `locate`

**What it does:** Searches a pre-built file-name database, which can make file searches very fast.

**Why I use it:** It can quickly find a file when its name is known.

**When I use it:** As a quick alternative to `find`, when the tool is installed and its database is current.

```bash
locate appsettings.json
```

The database may not include newly created files. If `locate` finds nothing, I can try `find`.

## 6. Search and filter text

### `grep`

**What it does:** Finds lines that contain a word or pattern.

**Why I use it:** Logs are large, and I usually need only lines related to an error, warning, request ID, or time.

**When I use it:** When investigating an error message or tracing a request through a log.

```bash
grep "ERROR" application.log
grep -i "timeout" application.log
grep -n "connection refused" application.log
grep -Rni "database" /opt/myapp/config
```

- `-i` ignores uppercase/lowercase differences.
- `-n` shows line numbers.
- `-R` searches inside folders recursively.
- `-l` can show only the names of matching files.

### `wc`

**What it does:** Counts lines, words, or bytes.

**Why I use it:** It helps me understand the size of a log or count how many matching lines were found.

**When I use it:** When estimating how many errors appear in a log.

```bash
wc -l application.log
grep -i "ERROR" application.log | wc -l
```

### `cut`

**What it does:** Extracts selected fields or character positions from each line.

**Why I use it:** Some simple log formats use a consistent separator, and I may need one field such as a date or status.

**When I use it:** When processing simple, consistently formatted text.

```bash
cut -d',' -f1,3 sample.csv
```

Here the comma is the delimiter and `-f1,3` selects the first and third fields. This is not a safe CSV parser for files that contain quoted commas; use a proper CSV tool for those files.

### `sort` and `uniq`

**What they do:** `sort` arranges lines. `uniq` removes or counts repeated adjacent lines.

**Why I use them:** Together they help me identify repeated errors or status codes.

**When I use them:** When I want to see which error message appears most often.

```bash
grep "ERROR" application.log | sort | uniq -c
```

`uniq` normally compares adjacent lines, which is why I sort first.

### `awk`

**What it does:** Reads text line by line and can select or print fields.

**Why I use it:** It is useful for extracting information from simple structured text, such as a space-separated report.

**When I use it:** When I need to print only one field from a consistent log or command output.

```bash
awk '{print $1, $2}' application.log
```

This example prints the first two whitespace-separated fields from each line. Real application logs can have different formats, so I check the format before relying on the result.

### `sed`

**What it does:** Processes text, often to select lines or replace text.

**Why I use it:** It can help inspect a small section of a file or demonstrate a text transformation.

**When I use it:** When I need to display a particular range of lines or test a replacement on sample data.

```bash
sed -n '1,20p' application.log
sed 's/ERROR/ISSUE/g' sample.log
```

These examples print transformed output to the terminal. I should avoid an in-place edit option on production configuration unless the change is reviewed and approved.

### `jq`

**What it does:** Reads and filters JSON data.

**Why I use it:** Many applications store settings and API responses as JSON. `jq` makes it easier to inspect a specific property.

**When I use it:** When checking a JSON configuration file or a saved API response.

```bash
jq '.' appsettings.json
jq '.Logging' appsettings.json
```

If `jq` is missing, I should not install it on a production system without approval. I can inspect a small JSON file with another approved tool.

## 7. Archive files and verify copies

### `tar`

**What it does:** Bundles files and folders into one archive. It can also extract an archive.

**Why I use it:** It is useful for packaging logs or transferring a group of files for an approved investigation.

**When I use it:** When I need to archive a lab folder or inspect a supplied archive.

```bash
tar -tf support-bundle.tar
tar -czf logs-backup.tar.gz logs/
tar -xzf logs-backup.tar.gz
```

`-t` lists archive contents, `-c` creates an archive, `-x` extracts, `-z` uses gzip compression, and `-f` specifies the archive file. Before extracting an unfamiliar archive, I inspect its contents and make sure the destination is appropriate.

### `gzip` and `zgrep`

**What they do:** `gzip` compresses files. `zgrep` searches text inside gzip-compressed files without requiring me to manually extract them first.

**Why I use them:** Old log files are often compressed to save disk space.

**When I use them:** When investigating a previous day's or week's logs.

```bash
gzip sample.log
zgrep -i "ERROR" application.log.gz
```

Compression changes the file being compressed, so I only practise it on sample files or use it when an approved process requires it.

### `sha256sum`

**What it does:** Calculates a checksum, which is a value based on the file's contents.

**Why I use it:** It helps compare two copies of a file and check whether their contents are identical.

**When I use it:** When verifying a downloaded deployment package or support bundle.

```bash
sha256sum package.tar.gz
```

Matching checksums are useful evidence that two files have the same contents, assuming the checksum itself came from a trusted source.

### `scp` and `rsync`

**What they do:** Both can transfer files between computers over a network. `scp` is a straightforward secure copy. `rsync` is designed to synchronize files efficiently.

**Why I use them:** They can help move an approved support bundle or copy files to a backup location.

**When I use them:** Only when the destination, account, network route, and data-handling rules are approved.

```bash
scp support-bundle.tar.gz user@lab-host:/tmp/
rsync -av --dry-run logs/ user@lab-host:/tmp/logs/
```

The `--dry-run` option shows what `rsync` would do without transferring files. I should understand the source and destination before removing `--dry-run`. I never copy secrets, private keys, or customer data to an unapproved location.

### Day 1 practice

1. Create a folder named `linux-lab` and a file named `application.log`.
2. Add a few lines containing INFO, WARN, and ERROR messages.
3. Use `less`, `head`, and `tail` to read the file in different ways.
4. Use `grep` to find errors and `wc -l` to count them.
5. Use `find` to locate the file.
6. Use `stat` and `file` to inspect it.
7. Create an archive of the lab folder and calculate its checksum.

**I should be able to explain:** where I am, which server I am on, where a file is located, how to read a large log, and how to find error messages without editing the file.

---

# Day 2 — Users, groups, ownership, and permissions

**Today's goal:** Understand why a user or application may be unable to read, write, or access a file.

Linux does not give every user unrestricted access to every file. Each file has an owner, a group, and permissions. This is an important part of production support because applications commonly run under dedicated service accounts.

## 1. Understand the current identity

### `groups`

**What it does:** Shows the groups that the current user belongs to.

**Why I use it:** Access can be granted to a group rather than to one individual user.

**When I use it:** When checking why a user can access one folder but not another.

```bash
groups
groups appuser
```

### `getent`

**What it does:** Looks up user or group information through the system's configured account databases.

**Why I use it:** It checks the account information Linux is actually using, which may come from local files or a central directory service.

**When I use it:** When checking whether a service account or group exists.

```bash
getent passwd appuser
getent group appgroup
```

A user entry normally includes the username, user ID, primary group ID, home directory, and login shell. Do not assume that a user is stored only in `/etc/passwd`; company systems may use centralized identity services.

### `last`

**What it does:** Shows recorded login sessions.

**Why I use it:** It can provide clues about recent logins and reboots.

**When I use it:** During an authorized access investigation, alongside the organization's official audit and identity tools.

```bash
last
```

This output is not a complete security audit. It should not be used on its own to prove who performed an action.

## 2. Read Linux permissions

### `ls -l` and `stat`

**What they do:** Show file permissions and ownership. `stat` provides more detailed file metadata.

**Why I use them:** They help me see whether the application account appears to have the required access.

**When I use them:** When an application reports that it cannot read a file or write to a folder.

```bash
ls -l appsettings.json
stat appsettings.json
```

An example permission display is:

```text
-rw-r----- 1 deploy appgroup 2048 Nov 14 10:30 appsettings.json
```

The first character is the file type. The next three characters are the owner's permissions, the next three are the group's permissions, and the final three are permissions for everyone else.

- `r` means read.
- `w` means write.
- `x` means execute for a file, or enter/traverse for a directory.
- `-` means that permission is not granted.

For a directory, execute permission is especially important. A user generally needs execute permission on each parent directory to reach a file inside it.

### `namei`

**What it does:** Displays the permissions of each folder in a path.

**Why I use it:** A file can have correct permissions while one of its parent folders blocks access.

**When I use it:** When I can see a file as an administrator but the application account receives “Permission denied”.

```bash
namei -l /opt/myapp/config/appsettings.json
```

This is one of the most useful commands for investigating path-related permission problems.

### `chmod`

**What it does:** Changes the permission bits of a file or folder.

**Why I use it:** It can grant or remove read, write, or execute permissions.

**When I use it:** Only in a lab or as part of an approved change after I understand the required access.

```bash
chmod 640 sample.conf
chmod 750 sample-script.sh
```

The number form uses:

- `4` for read.
- `2` for write.
- `1` for execute.

The three digits represent owner, group, and others. For example, `640` means the owner can read and write, the group can read, and others have no access.

I do not use `chmod -R 777` as a shortcut. It grants broad access and can create a security problem.

### `chown` and `chgrp`

**What they do:** `chown` changes a file's owner and optionally its group. `chgrp` changes the group.

**Why I use them:** Applications usually run as a specific service account. Files must be owned or grouped correctly for the intended access model.

**When I use them:** Only after confirming the expected service account and the ownership defined by the application deployment process.

```bash
chown appuser:appgroup sample.conf
chgrp appgroup shared-folder
```

Changing ownership can affect application behaviour. I do not run recursive ownership changes on production without a clear target and approval.

### `umask`

**What it does:** Shows or sets the default permission mask used when new files and folders are created.

**Why I use it:** It helps explain why a newly created file may not have the permissions I expected.

**When I use it:** When comparing files created by a shell session, service, or scheduled job.

```bash
umask
umask -S
```

The value shown is a mask, not the final permission itself. I should understand the process's default permissions before changing the mask.

## 3. Check and manage ACLs

### `getfacl`

**What it does:** Displays Access Control Lists, or ACLs, for a file or folder.

**Why I use it:** Normal owner/group/other permissions may not explain all access. ACLs can grant specific users or groups additional permissions.

**When I use it:** When the visible permission bits look correct but access still behaves unexpectedly.

```bash
getfacl appsettings.json
```

### `setfacl`

**What it does:** Adds, changes, or removes ACL entries.

**Why I use it:** It allows a specific user or group to receive access without changing the file's normal owner and group.

**When I use it:** In a lab, or during an approved access change.

```bash
setfacl -m u:appuser:r-- sample.conf
getfacl sample.conf
```

The first command grants the named user read permission on the sample file. ACL changes should be documented and checked against the team's access policy.

## 4. Check sudo and account status

### `sudo -l`

**What it does:** Lists the commands the current user is allowed to run with sudo.

**Why I use it:** It helps me understand whether I have the required administrative permission.

**When I use it:** Before a task that requires elevated access.

```bash
sudo -l
```

Having sudo access does not mean I should use it automatically. I use the least privilege needed for the task.

### `visudo`

**What it does:** Opens the sudoers configuration using a syntax-checking editor.

**Why I use it:** A broken sudoers file can prevent administrative access.

**When I use it:** Only when I am specifically authorized to change sudo rules.

```bash
sudo visudo
```

I never edit sudoers casually or use a normal text editor to bypass its validation.

### `passwd -S` and `chage -l`

**What they do:** `passwd -S` reports password status. `chage -l` shows password aging and account expiry information.

**Why I use them:** An expired or locked account may explain a login problem.

**When I use them:** When investigating an account issue with approval from the identity or security team.

```bash
sudo passwd -S appuser
sudo chage -l appuser
```

## 5. Account-management commands — lab only

These commands can create, modify, lock, or remove accounts. I learn their purpose in a disposable VM and never experiment with them on a production machine.

| Command | What it does | When it is used |
|---|---|---|
| `useradd` | Creates a user account | Provisioning an approved local service or test account |
| `usermod` | Changes an existing user | Updating a user's group membership or account settings |
| `userdel` | Deletes a user account | Removing an account through an approved deprovisioning process |
| `groupadd` | Creates a group | Creating a group for shared access |
| `gpasswd` | Manages group membership and group settings | Administering a local group |
| `passwd` | Sets or changes a password, subject to policy | Password administration |
| `usermod -L` | Locks password-based login for an account | An approved account-locking operation |

A detail worth remembering: `usermod -aG groupname username` adds a supplementary group while preserving existing supplementary groups. Using `usermod -G` without `-a` replaces the supplementary group list, which can accidentally remove access.

### Day 2 practice

1. In a disposable VM, inspect your own identity with `id` and `groups`.
2. Create two test users and one test group, following the VM's normal account setup process.
3. Create a shared folder and inspect it with `ls -ld`.
4. Use `namei -l` to inspect every folder in the path.
5. Use `getfacl` to see whether ACLs exist.
6. In the lab, practise one permission change and confirm the result.
7. Explain why a file's read permission alone may not be enough if a parent directory blocks traversal.

**I should be able to explain:** the difference between a user and a group, what `r`, `w`, and `x` mean, why directory execute permission matters, and how to investigate access denied without immediately changing permissions.

---

# Day 3 — Processes, ports, services, and application logs

**Today's goal:** Find out whether an application is running, which process owns a port, and why a service may have failed.

A **process** is a running program. A **service** is commonly a background program managed by the operating system. A service can be active while the application is still unhealthy, so I need to check more than one thing.

## 1. Find running processes

### `ps`

**What it does:** Shows information about processes running on the server.

**Why I use it:** It helps me check whether an application process exists and inspect its CPU and memory use.

**When I use it:** When an application is reported as stopped, slow, or consuming high resources.

```bash
ps aux
ps -ef
ps -ef | grep dotnet
```

The `ps aux` and `ps -ef` formats are common ways to list processes. A search using `grep` can also show the `grep` command itself, so I should not treat a matching line as proof that the application is running.

### `pgrep`

**What it does:** Searches for process IDs by process name or command line.

**Why I use it:** It gives me a quicker way to find a process than reading the full process list.

**When I use it:** When checking whether a known application process is present.

```bash
pgrep -af dotnet
```

### `top` and `htop`

**What they do:** Show processes and changing CPU and memory usage in a live view. `htop` is a more interactive alternative when installed.

**Why I use them:** They help me spot processes that are consuming unusual resources.

**When I use them:** When a server is slow or monitoring reports high CPU usage.

```bash
top
htop
```

Inside `top`, I can press `q` to exit. High CPU for a short period is not automatically a problem; I compare it with the incident time and the application's normal behaviour.

### `pstree` and `pidof`

**What they do:** `pstree` displays processes in a parent-child tree. `pidof` looks for the process ID of a program.

**Why I use them:** Some applications start child processes. Understanding the process tree can help explain how a service was launched.

**When I use them:** When investigating a service that starts helper processes or when I need a process ID for further inspection.

```bash
pstree -p
pidof dotnet
```

## 2. Understand process signals

### `kill`

**What it does:** Sends a signal to a process. Despite its name, it does not always force a process to stop.

**Why I use it:** A process may need to receive a signal to stop or reload safely.

**When I use it:** Only under an approved process-management procedure.

```bash
kill -TERM 1234
```

`TERM` asks the process to stop gracefully. A forceful signal such as `KILL` does not give the application a chance to clean up. I treat `kill -9` as a last resort, not as a normal troubleshooting step. For a systemd-managed service, I normally use the approved service-management procedure rather than killing an arbitrary process.

## 3. Find which process is using a port or file

### `ss`

**What it does:** Shows network sockets, including listening ports and active connections.

**Why I use it:** It helps me check whether an application is listening on the port that a load balancer or another service expects.

**When I use it:** When a health check fails or a connection cannot be established.

```bash
ss -lnt
ss -lntup
ss -tan
```

- `-l` shows listening sockets.
- `-n` shows numbers rather than resolving names.
- `-t` selects TCP.
- `-u` selects UDP.
- `-p` shows process information where permissions allow.

### `lsof`

**What it does:** Lists open files. On Linux, network sockets are also represented as file descriptors.

**Why I use it:** It can show which process has a file or network connection open.

**When I use it:** When a port appears occupied or a file cannot be replaced because a process still has it open.

```bash
lsof -i :5000
lsof -p 1234
```

### `fuser`

**What it does:** Identifies processes using a file, folder, or filesystem.

**Why I use it:** It can help identify which process is using a resource.

**When I use it:** When investigating a busy mount point or a port that appears to be in use.

```bash
fuser -v /path/to/file
```

Some `fuser` options can terminate processes. I use inspection-only forms and do not use kill options on production without explicit authorization.

## 4. Check services using systemd

Many Linux distributions use **systemd** to manage background services. `systemctl` is the command-line tool for communicating with it.

### `systemctl status`

**What it does:** Shows whether a service is active, failed, or stopped, along with recent status details.

**Why I use it:** It is usually my first check when an application is managed as a Linux service.

**When I use it:** When an application is unavailable or a monitoring alert says the service stopped.

```bash
systemctl status myapp.service
```

I look for the active state, main process ID, exit code, and recent log messages. The service name will be different in each environment.

### `systemctl is-active` and `is-enabled`

**What they do:** `is-active` checks whether a service is active now. `is-enabled` checks whether it is configured to start automatically.

**Why I use them:** A service can be stopped now but configured to start at boot, or active now but not enabled for future restarts.

**When I use them:** When checking service state during a support incident.

```bash
systemctl is-active myapp.service
systemctl is-enabled myapp.service
```

### `systemctl list-units` and `list-timers`

**What they do:** `list-units` lists loaded systemd units. `list-timers` lists scheduled systemd timers.

**Why I use them:** They help me discover services and scheduled work.

**When I use them:** When I know a service or job exists but need to confirm its registered name or schedule.

```bash
systemctl list-units --type=service
systemctl list-timers --all
```

### `systemctl cat` and `show`

**What they do:** `cat` displays a service's unit definition. `show` displays properties systemd knows about the unit.

**Why I use them:** They help me understand which executable starts, which account runs it, and which configuration paths are applied.

**When I use them:** When investigating differences between an interactive shell and the running service.

```bash
systemctl cat myapp.service
systemctl show myapp.service
```

I pay particular attention to the executable, working directory, environment configuration, and service user. These can explain why an application works manually but fails as a service.

### `systemctl start`, `stop`, `restart`, `reload`, and `daemon-reload`

These commands can change production state. I learn their meaning, but I use them only with the required approval.

| Command | Meaning | Important point |
|---|---|---|
| `systemctl start myapp` | Starts a stopped service | Changes service state |
| `systemctl stop myapp` | Stops a service | Can interrupt users |
| `systemctl restart myapp` | Stops and starts the service again | Can cause downtime |
| `systemctl reload myapp` | Asks the service to reload its configuration | Only works if the service supports reload |
| `systemctl daemon-reload` | Makes systemd reread unit definition files | It does not restart the application |

I should not confuse `daemon-reload` with an application restart. If a unit file changes, systemd may need to reload its unit definitions. The service may then need a separate approved restart for the change to take effect.

## 5. Read systemd logs with `journalctl`

**What it does:** Reads logs collected by the systemd journal.

**Why I use it:** It is a central place to investigate service startup messages, crashes, and system events.

**When I use it:** Immediately after checking a service that failed or restarted.

```bash
journalctl -u myapp.service
journalctl -u myapp.service --since "30 minutes ago"
journalctl -u myapp.service -p err
journalctl -f -u myapp.service
```

- `-u` filters by service.
- `--since` limits the time range.
- `-p err` shows error priority and higher-severity messages.
- `-f` follows new log entries.

The journal may contain sensitive information. I should redact secrets, tokens, and customer data before sharing output.

### Day 3 practice

1. Run `ps aux` and identify a familiar process.
2. Use `pgrep` to search for it.
3. Check whether a lab service is active with `systemctl status`.
4. Inspect its recent logs with `journalctl`.
5. Use `ss -lnt` to see listening TCP ports.
6. If a lab web server is available, identify its process and port.
7. Write down what evidence would be needed before restarting a service.

**I should be able to explain:** the difference between a process and a service, how to find a listening port, where systemd logs are stored, and why a running process does not always mean the application is healthy.

---

# Day 4 — Networking, DNS, HTTP, and TLS

**Today's goal:** Find out where a connection is failing instead of assuming every connection error is an application bug.

When one application calls another, several things must work: the destination name must resolve, the network route must be available, the destination port must accept the connection, TLS must complete when used, and the application must return a useful response.

## 1. Inspect network interfaces and routes

### `ip addr` and `ip -br addr`

**What they do:** Show IP addresses assigned to the server's network interfaces. The short form gives a compact view.

**Why I use them:** I need to understand which network addresses the server uses.

**When I use them:** When investigating a server that cannot reach another machine or is listening on an unexpected interface.

```bash
ip addr
ip -br addr
```

### `ip link`

**What it does:** Shows network interfaces and their link state.

**Why I use it:** It helps identify whether an interface is up or down.

**When I use it:** When investigating a network-interface issue.

```bash
ip link
```

### `ip route`

**What it does:** Shows the server's routing table, which helps Linux decide where to send network traffic.

**Why I use it:** A server may have an IP address but still lack a route to the destination network.

**When I use it:** When connectivity to a particular network fails.

```bash
ip route
ip route get 10.0.0.10
```

Use an address from the approved environment in place of the example address.

### `ip neigh`

**What it does:** Shows the neighbour table, often used for local network address resolution.

**Why I use it:** It can help investigate local network reachability between devices on the same network.

**When I use it:** When troubleshooting a local-network or address-resolution problem.

```bash
ip neigh
```

## 2. Check DNS

### `getent hosts`

**What it does:** Looks up a host using the operating system's configured name-resolution mechanism.

**Why I use it:** An application may use the same system resolver, so this is a useful first check for name resolution.

**When I use it:** When an application cannot connect using a hostname.

```bash
getent hosts example.internal
```

### `dig` and `nslookup`

**What they do:** Query DNS information. `dig` provides detailed DNS response information; `nslookup` is a simpler lookup tool.

**Why I use them:** They help me see whether a DNS record exists and which DNS server answered.

**When I use them:** When investigating hostnames that do not resolve or resolve to an unexpected address.

```bash
dig example.internal
nslookup example.internal
```

These tools may not be installed by default. DNS results can also vary by network and resolver configuration.

## 3. Test connectivity

### `ping`

**What it does:** Sends ICMP echo requests to check basic network reachability.

**Why I use it:** It can provide a quick indication that a host responds over the network.

**When I use it:** As one small test during a network investigation, where ICMP is allowed.

```bash
ping -c 4 example.internal
```

A failed ping does not prove that the application is unavailable. Firewalls often block ICMP while allowing application traffic.

### `nc`

**What it does:** Netcat can open or listen on network connections. The `-z` option can test whether a TCP port accepts a connection without sending application data.

**Why I use it:** It helps separate a TCP connectivity problem from an HTTP or application problem.

**When I use it:** When checking whether a remote service's port is reachable.

```bash
nc -vz example.internal 443
```

A successful connection means the TCP connection was accepted. It does not prove that the application behind the port is healthy.

### `traceroute` and `tracepath`

**What they do:** Show the network hops traffic may take toward a destination.

**Why I use them:** They can provide clues about where a route or network path is failing.

**When I use them:** During a network investigation when the approved network team asks for path information.

```bash
traceroute example.internal
tracepath example.internal
```

Some networks block or limit the packets these tools use. An incomplete trace does not always mean traffic is broken.

## 4. Check an HTTP endpoint with `curl`

### `curl -I`

**What it does:** Sends an HTTP request and displays response headers.

**Why I use it:** It gives me a quick view of the HTTP status code and response metadata without downloading the full response body.

**When I use it:** When checking whether a health endpoint responds.

```bash
curl -I https://example.internal/health
```

### `curl -v`

**What it does:** Shows detailed connection information, including DNS, connection, and TLS details.

**Why I use it:** It helps identify which stage of an HTTPS request fails.

**When I use it:** When a normal request only gives a short error message.

```bash
curl -v https://example.internal/health
```

Verbose output can expose internal hostnames, headers, or credentials. I review and redact it before sharing.

### `curl` with timeouts

**What it does:** Limits how long curl waits for a connection or the entire request.

**Why I use it:** A test should not hang for a long time during an incident.

**When I use it:** When comparing response time between a healthy and an unhealthy endpoint.

```bash
curl --connect-timeout 5 --max-time 15 -I https://example.internal/health
```

### `curl` with a short status summary

**What it does:** Writes the response body to a discard destination and prints selected request details.

**Why I use it:** It gives me a compact status and timing result for a health check.

**When I use it:** When I need to compare response times across repeated tests.

```bash
curl -sS -o /dev/null -w 'HTTP %{http_code} | time %{time_total}s\n' https://example.internal/health
```

A 200 response is common for a healthy endpoint, but the correct expected status depends on the application's health-check contract.

## 5. Understand TLS certificates

### `openssl s_client`

**What it does:** Opens a TLS connection and displays certificate and handshake details.

**Why I use it:** It helps diagnose certificate, hostname, and TLS negotiation problems.

**When I use it:** When an HTTPS request fails during the TLS handshake.

```bash
openssl s_client -connect example.internal:443 -servername example.internal
```

The `-servername` option sends the hostname through SNI, which matters when a server hosts multiple certificates.

### `openssl x509`

**What it does:** Reads certificate details from a certificate file.

**Why I use it:** It can show the subject, issuer, and validity dates of a certificate.

**When I use it:** When I have an approved certificate file and need to check its expiry.

```bash
openssl x509 -in server.crt -noout -subject -issuer -dates
```

### Environment proxy settings

Some servers use a proxy to reach external services.

```bash
env | grep -i proxy
printenv HTTPS_PROXY
```

I use these commands to inspect proxy-related environment variables. I must not paste the output into a ticket if a proxy URL contains a username, password, or token.

### Day 4 practice

For an approved test endpoint, perform these checks separately:

1. Resolve the hostname with `getent hosts` or `dig`.
2. Check the route with `ip route get`.
3. Test the TCP port with `nc -vz`.
4. Check the TLS handshake with `openssl s_client`.
5. Check the HTTP status with `curl -I`.
6. Compare the result with the application's own logs.

**I should be able to explain:** the difference between DNS failure, TCP connection refused, timeout, TLS certificate failure, and an HTTP 500 response.

---

# Day 5 — Disk, memory, CPU, and server health

**Today's goal:** Investigate whether a server is running out of disk space, inodes, memory, or CPU capacity.

A server can become unhealthy even when the application code has not changed. Resource checks help me understand whether the operating system is under pressure.

## 1. Check disk space

### `df`

**What it does:** Shows filesystem space usage.

**Why I use it:** It tells me whether a filesystem is nearly full.

**When I use it:** When an application cannot write logs, upload files, or create temporary files.

```bash
df -h
df -hT
```

`-h` uses readable units. `-T` also displays the filesystem type.

### `df -i`

**What it does:** Shows inode usage.

**Why I use it:** Linux uses inodes to keep track of files. A filesystem can run out of inodes even if it still has free disk bytes.

**When I use it:** When file creation fails but `df -h` appears to show free space.

```bash
df -ih
```

### `du`

**What it does:** Estimates how much space files and folders use.

**Why I use it:** After `df` identifies a full filesystem, `du` helps me find which folders are using the space.

**When I use it:** When investigating a full application or log partition.

```bash
du -sh /var/log
du -xhd1 /var
```

`-s` gives a summary, `-h` uses readable units, `-d1` limits the output to one folder level, and `-x` stays on one filesystem. The exact supported options can differ between implementations.

### `lsblk` and `findmnt`

**What they do:** `lsblk` lists block devices such as disks and partitions. `findmnt` shows mounted filesystems and their locations.

**Why I use them:** They help me understand which disk or filesystem backs a folder.

**When I use them:** When a mount is missing or a disk is not where I expected.

```bash
lsblk -f
findmnt
findmnt /var/log
```

## 2. Find large files safely

### `find` with size and filesystem limits

**What it does:** Searches for files matching a size or other condition.

**Why I use it:** One unexpectedly large log or dump file may be consuming a lot of disk space.

**When I use it:** After identifying the affected filesystem and choosing the correct search path.

```bash
find /var/log -xdev -type f -size +500M -print
```

`-xdev` tells the search not to cross into another filesystem. This example only lists matching files; it does not delete them.

### `lsof +L1`

**What it does:** Can list open files that have been deleted but are still held open by a process.

**Why I use it:** A file can disappear from its folder but continue using disk space while a process still has it open.

**When I use it:** When `df` says a filesystem is full but `du` cannot account for the space.

```bash
sudo lsof +L1
```

This is an inspection command, but `sudo` may be needed to see all processes. I do not kill the process just because a deleted file is open. I identify the owner and follow the service team's runbook.

## 3. Check CPU and load

### `uptime`

**What it does:** Shows uptime and load averages.

**Why I use it:** It gives a quick indication of system load over time.

**When I use it:** When the server is slow or alerts report high load.

```bash
uptime
```

Load average is not the same as CPU percentage. I compare it with the number of CPU cores and other metrics before drawing a conclusion.

### `top`

**What it does:** Shows CPU and memory activity in real time.

**Why I use it:** It helps me identify processes that are using significant resources.

**When I use it:** During a high-CPU or performance investigation.

```bash
top
```

### `ps` sorted by CPU or memory

**What it does:** Lists processes ordered by resource use.

**Why I use it:** It gives me a snapshot of the processes using the most CPU or memory.

**When I use it:** When I need a quick process-level view to attach to an investigation.

```bash
ps -eo pid,ppid,stat,%cpu,%mem,etime,cmd --sort=-%cpu
ps -eo pid,ppid,stat,%cpu,%mem,etime,cmd --sort=-%mem
```

Command lines can contain sensitive arguments. I redact secrets before sharing output.

## 4. Check memory and disk I/O

### `free`

**What it does:** Shows memory and swap usage.

**Why I use it:** It helps me check whether the server is under memory pressure.

**When I use it:** When an application is slow, gets terminated, or monitoring reports high memory usage.

```bash
free -h
```

Linux uses available memory for caching, so I do not assume that all memory shown as used is unavailable. I look at the available value and compare with historical monitoring.

### `vmstat`

**What it does:** Displays process, memory, swap, and CPU statistics at intervals.

**Why I use it:** It gives a short time-based view of resource pressure.

**When I use it:** When I want to see whether high load or swapping is continuing.

```bash
vmstat 1 5
```

This prints a report every second for five reports. The first report may reflect averages since boot, so I focus on later reports for current activity.

### `iostat`

**What it does:** Shows CPU and storage I/O statistics. It is commonly provided by the `sysstat` package.

**Why I use it:** Slow disk I/O can make an application appear slow even when CPU usage is low.

**When I use it:** When there are reports of slow reads, writes, or database operations.

```bash
iostat -xz 1 5
```

If the command is unavailable, I do not install packages on production without approval. I ask the infrastructure team for the approved metrics or tools.

### `dmesg`

**What it does:** Displays messages from the Linux kernel ring buffer.

**Why I use it:** Kernel messages may show disk, device, memory, or other operating-system problems.

**When I use it:** When investigating unexpected process termination, device issues, or filesystem errors.

```bash
dmesg -T
```

Some systems restrict access to kernel logs. I follow the server's security policy.

### `journalctl --disk-usage`

**What it does:** Shows how much disk space the systemd journal uses.

**Why I use it:** Journal logs can be one part of the disk usage on a server.

**When I use it:** When investigating a full filesystem that contains journal data.

```bash
journalctl --disk-usage
```

I do not vacuum or delete production logs just to free space. I follow log-retention and evidence-preservation rules.

### Day 5 practice

1. Run `df -hT` and identify the filesystem mounted at `/`.
2. Run `df -ih` and compare inode usage.
3. Use `du` on a lab folder and compare the result with `df`.
4. Use `lsblk -f` and `findmnt` to understand the disk layout.
5. Run `free -h`, `uptime`, and `top`.
6. Look at `vmstat 1 5`.
7. Write a short explanation of what you would investigate if disk usage reached 95%.

**I should be able to explain:** the difference between disk space and inodes, why `df` and `du` can disagree, and why memory usage should be interpreted with the available-memory metric.

---

# Day 6 — Scheduled jobs, packages, SSH, Docker, and Kubernetes

**Today's goal:** Understand how background jobs run, how to inspect installed software, and how to gather basic evidence from containers or a Kubernetes cluster.

## 1. Check scheduled jobs

Linux commonly uses cron or systemd timers to run work automatically.

### `crontab -l`

**What it does:** Lists the scheduled cron jobs for the current user.

**Why I use it:** A background task may not run because its schedule was removed or changed.

**When I use it:** When an overnight job, cleanup task, or scheduled script did not execute.

```bash
crontab -l
```

### `crontab -l -u`

**What it does:** Lists another user's cron jobs, if I have permission.

**Why I use it:** Scheduled jobs run under a user account. I may need to inspect the service account's schedule.

**When I use it:** During an approved investigation into a job owned by a service account.

```bash
sudo crontab -l -u appuser
```

This is a read operation, but access may require elevated permission. Do not change another user's schedule without authorization.

### `systemctl list-timers`

**What it does:** Lists scheduled systemd timers and their next or previous run information.

**Why I use it:** Not every scheduled task uses cron; some are managed by systemd.

**When I use it:** When a job is expected to run periodically but no cron entry is found.

```bash
systemctl list-timers --all
```

## 2. Check installed packages

Package commands differ by Linux distribution. I first check `/etc/os-release` so I use the correct family of commands.

| Distribution family | Command | What it helps me inspect |
|---|---|---|
| Ubuntu / Debian | `dpkg -l` | Installed package list |
| Ubuntu / Debian | `apt list --installed` | Installed packages through APT |
| RHEL family | `rpm -qa` | Installed RPM packages |
| RHEL family | `dnf list installed` | Installed packages through DNF |

**Why I use these commands:** They help check whether a required runtime, client, or system package is installed and which version is present.

**When I use them:** When a deployment fails because a runtime or native dependency may be missing.

I use package-list commands for inspection. Installing, upgrading, or removing packages changes the server and must follow the change process.

## 3. Understand SSH

SSH is the secure protocol commonly used to open a remote shell on a Linux server.

### `ssh -v`

**What it does:** Connects to a remote server and prints detailed connection diagnostics.

**Why I use it:** It can help identify whether a connection problem is related to network access, authentication, or SSH configuration.

**When I use it:** When an approved server connection fails.

```bash
ssh -v user@example.internal
```

Verbose SSH output can contain usernames, hostnames, and connection details. I redact it before sharing.

### `ssh-keygen -lf`

**What it does:** Displays the fingerprint of a public key.

**Why I use it:** A fingerprint helps compare a key with an approved record without sharing the private key.

**When I use it:** When verifying which public key is configured or presented.

```bash
ssh-keygen -lf ~/.ssh/id_ed25519.pub
```

A private key is secret. I never copy it into a ticket or repository.

## 4. Inspect environment and process limits

### `env` and `printenv`

**What they do:** `env` displays environment variables. `printenv` displays a selected variable.

**Why I use them:** Applications often receive configuration through environment variables, and a service can behave differently from my interactive shell.

**When I use them:** When checking whether a service received the expected configuration.

```bash
printenv PATH
printenv ASPNETCORE_ENVIRONMENT
```

I avoid dumping every environment variable into a ticket because some may contain passwords, tokens, or connection strings.

### `ulimit`

**What it does:** Shows resource limits applied to the current shell or process environment.

**Why I use it:** A process may be restricted in the number of open files or other resources it can use.

**When I use it:** When investigating “too many open files” or a process limit issue.

```bash
ulimit -a
```

The values for my shell may differ from those used by a systemd service. I check the actual service configuration and process limits when appropriate.

## 5. Docker basics — only if the environment uses Docker

A container packages an application and its runtime dependencies so it can run in an isolated environment. Docker is a common tool for working with containers.

### `docker ps`

**What it does:** Lists running containers.

**Why I use it:** It helps me check whether the expected application container is running.

**When I use it:** When a containerized application is unavailable.

```bash
docker ps
docker ps -a
```

The second command includes stopped containers too.

### `docker logs`

**What it does:** Displays logs produced by a container.

**Why I use it:** It helps me investigate startup errors and application exceptions.

**When I use it:** When the container exists but the application is not healthy.

```bash
docker logs --since 30m container_name
```

### `docker inspect`

**What it does:** Displays detailed configuration and runtime information about a container.

**Why I use it:** It can show the image, mounts, network settings, and configured environment. I must handle this output carefully because environment values can contain secrets.

**When I use it:** When comparing a container's runtime setup with its expected configuration.

```bash
docker inspect container_name
```

### `docker stats`

**What it does:** Displays live CPU, memory, and I/O statistics for containers.

**Why I use it:** It helps identify a container that is using more resources than expected.

**When I use it:** During a container performance investigation.

```bash
docker stats
```

`docker exec` can open a command inside a running container. It is useful for diagnosis, but it is an interactive action and can change the container. I use it only when permitted by the team's runbook.

## 6. Kubernetes basics — only if the environment uses Kubernetes

Kubernetes manages containerized applications across a cluster. Before running commands, I must know which cluster context and namespace I am using.

### `kubectl config current-context`

**What it does:** Displays the current Kubernetes context.

**Why I use it:** A context identifies the cluster and access configuration. Running a command against the wrong cluster can have serious consequences.

**When I use it:** Before any Kubernetes investigation.

```bash
kubectl config current-context
```

### `kubectl get pods`

**What it does:** Lists pods, which are the Kubernetes units that run one or more containers.

**Why I use it:** It shows whether application pods are running, pending, or restarting.

**When I use it:** When a deployed application is unavailable.

```bash
kubectl get pods -n my-namespace
```

### `kubectl describe pod`

**What it does:** Displays detailed information and recent events for a pod.

**Why I use it:** It can reveal image-pull errors, scheduling problems, failed probes, and restart information.

**When I use it:** When a pod is not starting or is repeatedly restarting.

```bash
kubectl describe pod my-pod -n my-namespace
```

### `kubectl logs`

**What it does:** Displays the output logs from a container in a pod.

**Why I use it:** It helps me find application startup errors and runtime exceptions.

**When I use it:** When the pod is running but the application is unhealthy.

```bash
kubectl logs my-pod -n my-namespace --since=30m
kubectl logs my-pod -n my-namespace --previous
```

`--previous` is useful when a container has restarted and I need the previous container's logs, if they are still available.

### `kubectl get events`

**What it does:** Lists recent Kubernetes events.

**Why I use it:** The events can explain why Kubernetes could not schedule, start, or keep a pod healthy.

**When I use it:** When the pod status alone does not explain the problem.

```bash
kubectl get events -n my-namespace --sort-by=.lastTimestamp
```

I treat commands that edit or delete Kubernetes resources as change operations. I do not run them during diagnosis unless the incident procedure explicitly authorizes them.

### Day 6 practice

1. List your own cron jobs with `crontab -l`.
2. Inspect systemd timers.
3. Identify the Linux distribution and check one installed package.
4. Inspect SSH connection diagnostics in a lab.
5. If Docker is available, inspect a test container and its logs.
6. If Kubernetes is available, verify the current context, list pods, and inspect one pod's events and logs.

**I should be able to explain:** why a scheduled job can behave differently from an interactive command, why package commands differ by distribution, and why I must verify the Kubernetes context before investigating a cluster.

---

# Day 7 — Production incident practice

**Today's goal:** Use the commands from the first six days in a structured investigation. I want to collect evidence before suggesting a fix.

I will use the same process for each incident:

1. Confirm the host, environment, account, and time.
2. Understand who is affected and what is failing.
3. Check whether there was a recent deployment or configuration change.
4. Inspect the relevant service, process, logs, filesystem, or network path.
5. Record exact errors and timestamps.
6. Decide what is known, what is only a possibility, and what still needs checking.
7. Make changes only with the required approval.
8. Verify the result and document the incident.

## Scenario 1 — ASP.NET Core application is unavailable

**Situation:** Users report that an application is not responding, and the health endpoint returns an error.

My first checks:

```bash
hostname
date -Is
systemctl status myapp.service
journalctl -u myapp.service --since "30 minutes ago"
ps -ef | grep dotnet
ss -lnt
curl -v --connect-timeout 5 --max-time 15 https://example.internal/health
```

**What I am trying to learn:**

- Is the service active or failed?
- Did the application exit during startup?
- Is a process running?
- Is the expected port listening?
- Does the health endpoint respond?
- Do the logs show a missing configuration, dependency failure, or unhandled exception?

A `503` response can come from a proxy, load balancer, or application. I should identify which component returned it before deciding what to change.

## Scenario 2 — Application cannot read a configuration file

**Situation:** The application reports that it cannot access a configuration file.

My first checks:

```bash
whoami
id
ls -l /opt/myapp/config/appsettings.json
stat /opt/myapp/config/appsettings.json
namei -l /opt/myapp/config/appsettings.json
getfacl /opt/myapp/config/appsettings.json
systemctl cat myapp.service
```

**What I am trying to learn:**

- Which account am I using?
- Which account runs the service?
- Who owns the file?
- Does the service account have read permission?
- Can it traverse each parent folder?
- Are ACLs granting or restricting access?
- Did a deployment change the owner or directory structure?

I do not immediately use `chmod 777`. First I identify the correct service identity and the intended access model.

## Scenario 3 — Disk space is almost full

**Situation:** The application cannot write files, and monitoring reports that a filesystem is nearly full.

My first checks:

```bash
df -hT
df -ih
findmnt
du -xhd1 /var
find /var/log -xdev -type f -size +500M -print
sudo lsof +L1
journalctl --disk-usage
```

**What I am trying to learn:**

- Which filesystem is full?
- Are bytes or inodes exhausted?
- Which folder uses the most space?
- Are large logs or dump files present?
- Are deleted files still open?
- How much space is used by the systemd journal?

I do not delete logs or dumps during investigation. They may be required for incident analysis, and log retention must follow company policy. I propose a cleanup or rotation plan and get approval first.

## Scenario 4 — One application cannot connect to another

**Situation:** An application reports a timeout while calling a dependency.

My first checks:

```bash
getent hosts dependency.internal
ip route get 10.0.0.10
nc -vz dependency.internal 443
curl -v --connect-timeout 5 --max-time 15 https://dependency.internal/health
```

**What I am trying to learn:**

- Does the hostname resolve?
- Is there a route to the destination?
- Can the server establish a TCP connection?
- Does TLS complete?
- Does the remote application return an HTTP response?

A timeout and a refused connection are different symptoms. I record which one occurred and share the exact timestamp with the network or dependency team.

## Scenario 5 — Scheduled job did not run

**Situation:** A background job was expected to run overnight but no output was generated.

My first checks:

```bash
date -Is
timedatectl
crontab -l
systemctl list-timers --all
journalctl --since "12 hours ago"
```

**What I am trying to learn:**

- Is the server using the expected timezone?
- Is the job scheduled through cron or a systemd timer?
- Which account should run it?
- Are there logs for the expected execution time?
- Did the script fail because of a missing file, permission, environment variable, or dependency?

Scheduled jobs often run with a smaller environment than an interactive shell. A script that works manually may fail when launched by cron or systemd.

---

# How I will write a production support update

When I report an incident, I should write what I checked and what the evidence shows. I should avoid saying that I found the root cause unless the evidence supports it.

I can use this simple structure:

```text
Issue:
Application health check is returning 503.

Impact:
Requests to the affected application instance are failing.
Impact scope is still being confirmed.

Environment:
Host:
Service:
Environment:
First observed time and timezone:

Checks performed:
1. Confirmed host and current account.
2. Checked service status.
3. Reviewed recent service logs.
4. Checked the listening port.
5. Tested the health endpoint.

Findings:
- Service state:
- Recent error:
- Listening port:
- Health endpoint result:

Current assessment:
The evidence suggests the issue may be in:
Additional checks required:

Action:
No production changes made yet.
Approval or assistance required:

Next update:
Time:
```

Before sharing command output, I remove passwords, access tokens, connection strings, private keys, and customer information.

---

# Linux commands I want to remember first

I do not need to memorise every Linux command at once. These are the commands I expect to use frequently when I start production support.

| What I need to check | Command |
|---|---|
| Which server am I on? | `hostname` |
| Which account am I using? | `whoami`, `id` |
| Which folder am I in? | `pwd` |
| What files are here? | `ls -lah` |
| Read the latest log entries | `tail -n 100`, `tail -F` |
| Search for an error | `grep -i "error" file.log` |
| Find a configuration file | `find /opt/myapp -name "appsettings.json"` |
| Check file permissions | `ls -l`, `stat`, `namei -l` |
| Check users and groups | `id`, `groups`, `getent` |
| Check service health | `systemctl status` |
| Read service logs | `journalctl -u` |
| Check running processes | `ps aux`, `top` |
| Check listening ports | `ss -lntup` |
| Check DNS | `getent hosts`, `dig` |
| Test an HTTP endpoint | `curl -v`, `curl -I` |
| Check disk space | `df -hT` |
| Check inode usage | `df -ih` |
| Find large directories | `du -xhd1` |
| Check memory | `free -h` |
| Check scheduled timers | `systemctl list-timers --all` |

## A few habits I want to keep after the first week

- I will confirm the host and environment before running commands.
- I will start with read-only commands and collect evidence first.
- I will understand every option in a command before running it.
- I will avoid broad recursive permission changes and destructive cleanup commands.
- I will use the team's approved runbooks for service restarts, account changes, firewall changes, and package updates.
- I will learn the Linux distribution used by my application because package and service details can differ.
- I will add examples to this repository only after testing them in a safe environment.
- I will never commit production credentials, private keys, access tokens, or customer data.

## Final checkpoint

At the end of these seven days, I want to be able to investigate a basic production issue in a calm and repeatable way. I should be able to identify the server, inspect files and permissions, check processes and services, read logs, test network connectivity, and understand basic resource usage.

I will keep improving this guide as I encounter new issues and learn the commands approved by my team. The objective is not to run commands quickly; it is to understand what they tell me and make safe decisions during production support.

---

*Notes: Command availability and options can vary between Ubuntu, Debian, RHEL, and other distributions. Before using an unfamiliar option, I will check the local manual with `man command-name` or use `command-name --help`. I will practise commands that change system state only in a disposable lab or through an approved change.*