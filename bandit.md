# OverTheWire Bandit

## Why I did Bandit

I used OverTheWire Bandit as hands-on practice after studying Linux and networking fundamentals.

The main goal was not to memorize commands or copy walkthroughs. I wanted to get comfortable opening a terminal, looking at an unfamiliar environment, and figuring out what was happening by myself.

I finished the Bandit levels and came out with a much better understanding of how Linux, networking, scripting, Git, and security concepts connect together.

---

## What I Practiced

### Linux & Shell

I worked heavily with:

```bash
ls
cd
pwd
cp
mv
cat
file
du
find
grep
strings
man
```

I also practiced using `--help`, pipes, Bash scripting, command substitution, loops, conditions, and exit codes.

The biggest improvement was becoming comfortable doing investigation from the terminal instead of relying on a GUI.

---

## Shell Escaping

Shell escaping was one of the concepts that was new to me.

I learned that the shell interprets characters such as:

```text
'
"
\
$
*
?
spaces
```

before the command is executed.

This became important when dealing with unusual filenames and input.

It also gave me a better foundation for understanding things like command injection and unsafe shell usage.

---

## SSH

SSH was a major part of Bandit.

```bash
ssh user@host
```

I practiced remote authentication, SSH ports, password-based access, key-based authentication, and working completely through a remote shell.

I also learned that SSH authentication is not limited to username + password.

For example:

```bash
ssh -i private_key user@host
```

can be used with an identity file.

---

## Pipes & Command Composition

I used pipes to connect commands:

```bash
command1 | command2
```

This made the command line much more powerful.

Instead of trying to find one command that does everything, I could combine smaller tools into a workflow.

---

## Bash Scripting

Some tasks were repetitive, so I started writing Bash scripts instead of doing everything manually.

I practiced things like:

```text
Variables
Loops
Conditions
Command substitution
Exit codes
```

One useful change in my mindset was:

> Can I automate this?

instead of just repeating the same commands manually.

---

## File Discovery & Searching

I used:

```bash
find
grep
strings
file
```

to investigate the environment.

For example:

```bash
find <path> -type f
```

and:

```bash
grep "pattern" file.txt
```

I learned to search based on properties and contents instead of trusting filenames.

A useful mental model became:

```text
Filename → clue
Contents/type → evidence
```

---

## Ports & Services

Bandit also connected the networking concepts I had already studied with real service interaction.

My mental model became:

```text
Host
 ↓
Open Port
 ↓
Service
 ↓
Protocol
 ↓
Possible Attack Surface
```

An open port is not automatically a vulnerability.

It is something to investigate.

---

## Nmap

I used Nmap for port scanning and service discovery.

```bash
nmap <target>
```

and when needed:

```bash
nmap -sV <target>
```

This helped me understand the workflow:

```text
Scan
 ↓
Find Open Ports
 ↓
Identify Services
 ↓
Understand the Protocol
 ↓
Interact With It
 ↓
Investigate Further
```

This made enumeration much more practical for me.

---

## Netcat

I used Netcat:

```bash
nc <host> <port>
```

to connect to and interact directly with services.

This helped me understand that a port is associated with an actual communication endpoint, not just a number shown by a scanner.

---

## TCP & TLS

I interacted with TCP services and also worked with TLS connections.

For TLS:

```bash
openssl s_client -connect <host>:<port>
```

This was especially useful because I had already studied networking.

It helped connect the theory to an actual interaction:

```text
TCP Connection
      ↓
TLS Handshake
      ↓
Encrypted Application Data
```

---

## DNS

I used:

```bash
nslookup <domain>
```

to perform DNS lookups.

This reinforced:

```text
Domain Name
     ↓
DNS Resolution
     ↓
IP Address
```

---

## UIDs, Users & Permissions

I worked with:

```text
UID
User
Group
Ownership
Permissions
SUID
```

One of the questions I started asking was:

> Who is actually executing this, and what privileges does that identity have?

This made Linux permissions much more meaningful than just memorizing permission syntax.

---

## Git

Git was one of the most interesting parts for me.

Before Bandit, I mostly thought of Git as:

```bash
git clone
git pull
git add
git commit
git push
```

Bandit made me look at Git differently.

A repository can contain information that is not immediately obvious from the current working tree.

I used:

```bash
git log
git show-ref
```

to investigate repository history and references.

This taught me to look beyond the files currently visible in a checkout and consider things like:

```text
Commit History
Branches
Tags
References
Git Metadata
```

From a security perspective, this also reinforced that removing something from the current version does not necessarily mean that it disappeared from repository history.

---

## Encoding & Data Representation

I worked with different representations of data, including:

```text
Base64
Hex
Binary Data
Encoded Strings
```

One important distinction I reinforced:

```text
Encoding ≠ Encryption
```

Encoding changes representation.

Encryption is intended to provide confidentiality through cryptographic mechanisms.

---

## Enumeration Mindset

This was probably the most important thing Bandit added to my learning.

Instead of randomly running commands, I started approaching problems with a process:

```text
Read the Objective
        ↓
Inspect the Environment
        ↓
Enumerate
        ↓
Identify Useful Clues
        ↓
Read the Documentation
        ↓
Form a Hypothesis
        ↓
Test It
        ↓
Automate Repetitive Work
        ↓
Verify
```

This is the mindset I want to carry into future security labs.

---

## Commands & Tools I Practiced

### Linux / Shell

```text
ls
cd
pwd
cp
mv
cat
file
du
find
grep
strings
man
--help
pipes
Bash scripting
shell escaping
```

### Networking / Security

```text
ssh
nmap
nc
openssl s_client
nslookup
```

### Git

```text
git log
git show-ref
Git references
Git history
Git repository metadata
```

### Security Concepts

```text
SSH authentication
File permissions
UIDs
SUID
Enumeration
Port scanning
Service discovery
TCP
TLS
DNS
Shell parsing
Encoding
Repository investigation
```

---

## What Bandit Actually Added

Before Bandit, many of these were concepts I had studied.

After Bandit, I had to use them to solve problems in an environment I did not control.

That changed my relationship with the tools.

Instead of:

```text
"I know what this command does."
```

I became more comfortable with:

```text
"I don't know yet.
Let's inspect what is happening and figure it out."
```

For me, that is the real value of the CTF.

---

## Where It Fits In My Roadmap

```text
Linux Fundamentals
        ↓
Networking Fundamentals
        ↓
OverTheWire Bandit
        ↓
Hands-on Linux + Networking Practice
        ↓
Web Security / Natas
        ↓
More Security Labs
```

Bandit was my bridge between learning the fundamentals and actually using them in security-focused problems.

---

## Notes

These are learning notes, not a level-by-level walkthrough.

I intentionally keep passwords, flags, and complete solutions out of this file.

The goal is to document the concepts, tools, and problem-solving habits I actually practiced.
