# Linux Fundamentals for Absolute Beginners

I wrote this guide as the starting point for learning Linux from zero. The aim is to understand the computer, the command line, and the words used in Linux support before trying to memorise commands.

## 1. What is Linux?

Linux is an operating-system kernel. The kernel is the core software that manages hardware and provides services that programs need, such as memory, processes, files, and networking. In everyday conversation, people often say “Linux” to mean a complete operating system built around the Linux kernel.

Companies use Linux for web servers, cloud virtual machines, containers, databases, automation, and many infrastructure services. It is widely used because it is flexible, scriptable, stable, and available in many distributions.

## 2. Linux and Windows: a simple comparison

| Topic | Windows | Linux |
|---|---|---|
| Common interaction | Desktop, PowerShell, Command Prompt | Desktop or terminal/shell |
| Software packaging | Installers, Microsoft Store, package tools | Distribution package managers such as `apt` or `dnf` |
| File paths | Often use drive letters such as `C:\` | One directory tree starting at `/` |
| Permissions | Windows security descriptors and ACLs | Owner/group/other mode bits, ACLs, and other security controls |
| Server automation | PowerShell and other tools | Shell scripts and many automation tools |

Both operating systems can host applications and provide command-line tools. Learning Linux does not mean Windows knowledge is no longer useful.

## 3. What are Ubuntu, Debian, and RHEL?

A **Linux distribution** (often called a distro) combines the Linux kernel with system tools, package management, libraries, and a release/update model.

- **Ubuntu** is a popular Debian-based distribution used on desktops, servers, cloud VMs, and in learning environments.
- **Debian** is a community distribution and the base for several other distributions, including Ubuntu.
- **RHEL** means Red Hat Enterprise Linux. It is an enterprise distribution used in many business environments.
- **Rocky Linux** and **AlmaLinux** are examples of RHEL-compatible distributions.

Commands and file locations are often similar, but package managers and some service details differ. Identify the distribution before following instructions:

```bash
cat /etc/os-release
```

## 4. What are a terminal, shell, and Bash?

These words are related, but they do not mean the same thing.

- **Terminal:** The application or window where I type and see text commands.
- **Shell:** The program that reads my command, interprets it, and starts the requested program.
- **Bash:** A widely used shell (Bourne Again SHell) on Linux.
- **Command:** An instruction I ask the shell to run, such as `pwd` or `ls`.

A useful comparison: the terminal is like the keyboard and screen, while the shell is the interpreter that understands the instructions I type.

## 5. What is a Linux server?

A server is a computer (physical or virtual) that provides a service to other computers or users. A Linux server may run a web application, database, API, message broker, or background worker.

For example, an ASP.NET Core API may run as a process on a Linux virtual machine. A service manager may start it, a reverse proxy may accept external HTTPS requests, and the application may connect to a database.

## 6. Kernel, process, service, and port

| Term | Plain-English meaning | Example |
|---|---|---|
| Kernel | Core of the operating system; manages hardware and system resources | Linux kernel |
| Process | A running instance of a program | A running `dotnet` application |
| Service | A background program managed by the operating system or service manager | An ASP.NET Core systemd unit |
| Port | A numbered endpoint used by network communication | An API listening on TCP port 8080 |
| Log | A record of events produced by a system or application | Startup errors and request messages |

A service usually runs as one or more processes. A network application may listen on a port so clients or a proxy can connect to it.

## 7. What is the Linux filesystem?

Linux organises files under one directory tree. The top of that tree is `/` (called root). Unlike the common Windows drive-letter view, mounted disks and filesystems appear at directories within this tree.

| Path | Common purpose |
|---|---|
| `/` | Top of the filesystem tree |
| `/home` | Home directories for regular users |
| `/root` | Home directory for the root administrator account |
| `/etc` | System and service configuration |
| `/var` | Variable data such as logs, caches, and application state |
| `/var/log` | Common location for system logs |
| `/tmp` | Temporary files; cleanup behaviour depends on the system |
| `/opt` | Often used for optional or separately installed application software |
| `/usr` | Many user-space programs, libraries, and shared resources |
| `/bin` and `/sbin` | Essential command locations on many systems; may be links into `/usr` on modern distributions |

Do not assume every application uses the same path. Confirm the service's actual configuration.

## 8. Absolute paths, relative paths, and shortcuts

- An **absolute path** starts at `/`, for example `/var/log/syslog`.
- A **relative path** is interpreted from the current directory, for example `logs/app.log`.
- `.` means the current directory.
- `..` means the parent directory.
- `~` is shorthand for the current user's home directory.

Try these read-only commands:

```bash
pwd
ls -la
cd /var/log
pwd
cd ..
pwd
cd ~
pwd
```

`cd` changes the shell's current directory. `pwd` confirms where I am after moving.

## 9. Users, groups, root, and sudo

Linux uses user and group identities to decide who can access files and perform actions.

- A **user** is an account identity. An application can run under a dedicated service user.
- A **group** is a collection of users used to grant shared access.
- **root** is the special administrator account with broad system privileges.
- **sudo** lets an authorised user run a specific command with elevated privileges according to system policy.

Check the current identity:

```bash
whoami
id
```

Do not add `sudo` automatically when a command fails. First understand why access was denied and whether the action is authorised. Elevated privileges can change the whole system.

## 10. Files, directories, and permissions

Linux permissions commonly show three classes: owner, group, and others. Each class can have read (`r`), write (`w`), and execute (`x`) permissions.

For a regular file:
- `r` means read the file contents.
- `w` means modify the file contents.
- `x` means execute the file as a program/script, subject to other controls.

For a directory:
- `r` means list directory entries.
- `w` means create, remove, or rename entries (normally combined with execute permission).
- `x` means search/traverse the directory and access known entries if other permissions allow it.

Example permission string: `-rw-r-----` means a regular file where the owner can read/write, the group can read, and others have no permission. The first character identifies the file type; the remaining nine characters show the three permission classes.

Inspect permissions without changing them:

```bash
ls -l
stat <file>
namei -l <path>
```

## 11. What happens when an application receives a request?

A simplified web request path may look like this:

```text
Browser or client
      |
      v
DNS resolves the application hostname
      |
      v
Network connection to the server/load balancer
      |
      v
TLS handshake for HTTPS
      |
      v
Reverse proxy or load balancer
      |
      v
ASP.NET Core application process
      |
      v
Database / cache / external API
```

If a user sees an error, the problem could be at any layer. Linux support involves collecting evidence at each layer rather than assuming the server or application is always at fault.

## 12. Read-only commands for my first session

Run these in a practice terminal:

```bash
whoami
id
hostname
pwd
date -Is
uname -a
cat /etc/os-release
uptime
ls -lah
```

These commands identify the current account, host, location, time, kernel, distribution, uptime, and directory contents. They are intended for inspection and do not change system configuration.

## 13. Common words and errors

| Term or message | Meaning |
|---|---|
| `PATH` | A list of directories the shell searches for executable commands |
| Environment variable | A named value available to a process, such as configuration or runtime settings |
| `command not found` | The command may not be installed or may not be in `PATH` |
| `No such file or directory` | A file or part of the supplied path could not be found |
| `Permission denied` | The current identity or a security control does not allow the requested access |
| Exit status | A numeric result returned by a command; `0` commonly means success |

To check whether a command exists, use `command -v <command>`. To inspect the exit status, run `echo $?` immediately after the command you want to check.

## 14. Safe beginner rules

1. Practise in a browser lab, WSL2, or disposable VM—not a production server.
2. Never enter work passwords, access tokens, private keys, customer data, or company files into public practice sites.
3. Read commands before running them. Be especially careful with `sudo`, `rm`, `chmod`, `chown`, service controls, and shell redirection.
4. Start with commands that only display information.
5. Do not copy commands from a tutorial if you cannot explain each part.
6. If a command behaves differently from the guide, stop and inspect its help or manual page.

## 15. Check my understanding

Try to answer without looking back:

1. What is the difference between Linux and a Linux distribution?
2. What is the difference between a terminal and a shell?
3. What does the kernel do?
4. What is the difference between a process and a service?
5. What does `/var/log` commonly contain?
6. Why does a process need permission to traverse parent directories?
7. Why should I not use `sudo` for every command?
8. Why does a successful DNS lookup not prove an application is healthy?

**Ready to continue:** When I can explain these ideas and run the read-only commands above, I can move to [`01-seven-day-learning-plan.md`](01-seven-day-learning-plan.md).
