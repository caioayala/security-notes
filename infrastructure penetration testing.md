# Infrastructure Penetration Testing: Methodology and Technique Notes

## Methodology

Infrastructure penetration testing evaluates network-accessible devices such as servers, printers and firewalls, meaning any host reachable over a LAN or the internet. The engagement follows a five-stage process, and each stage depends on the completeness of the one before it.

1. **Enumeration.** Establish a baseline of what is known and what needs to be discovered about the target. This stage is repeated at every later point where new access is gained, since a foothold on a system exposes information that was invisible from the outside.
2. **Vulnerability analysis.** Correlate enumeration results with known weaknesses in the identified software and configuration.
3. **Initial access.** Select and execute an attack that establishes a foothold on the target.
4. **Privilege escalation.** Re-enumerate from inside the system and identify a path to a higher-privileged account.
5. **Reporting.** Communicate findings in a way that different audiences can act on. Without this stage, none of the technical work has organisational value.

## Exploit Research Workflow

Locating a working exploit is not a matter of guessing a category. Exploit-DB and `searchsploit` index entries by product name and version, not by network protocol, since almost every service runs over TCP and a protocol-based search returns nothing useful. The correct input is the software identified during enumeration, typically through service version detection (`nmap -sV`), for example:

```
searchsploit unrealircd
```

Once a matching entry is located, `searchsploit -p <EDB-ID>` resolves the on-disk path of the exploit file, following a consistent structure:

```
linux/remote/13853.pl
```

- **Platform** (`linux`): the operating system the exploit targets, not the system running the tool.
- **Class** (`remote`): how the exploit is delivered. `remote` requires no prior access and is used for initial access; `local` assumes existing low-privilege access and is used for privilege escalation; other classes include `webapps` and `dos`.
- **Identifier and language** (`13853.pl`): the Exploit-DB ID, followed by the scripting language of the proof of concept (`.py`, `.rb`, `.c`, `.sh`), which determines how it must be invoked.

## Initial Access via Known Backdoors

Not every exploited backdoor originates from an attacker exploiting a coding flaw after the fact. Backdoors reach production software through at least three distinct routes, and distinguishing between them matters for how a finding is classified in a report:

- **Supply chain compromise.** Malicious code is inserted into the distribution channel itself, before any user downloads the software. The UnrealIRCd 3.2.8.1 case (CVE-2010-2075) is a documented instance: between November 2009 and June 2010, mirror servers hosting the source archive were compromised and the legitimate tarball was replaced with a version containing a trojan in the `DEBUG3_DOLOG_SYSTEM` macro, allowing unauthenticated remote command execution. Anyone who downloaded the software during that window installed a backdoor without any vulnerability being exploited on their end.
- **Developer-introduced backdoors.** Debug or maintenance access left in the codebase intentionally or by oversight.
- **Post-exploitation persistence.** An attacker exploits an unrelated vulnerability, gains access, and installs a separate backdoor (webshell, hidden account, added SSH key) to retain access independently of the original flaw being patched.

The distinction is relevant because a supply chain finding and a persistence finding call for different remediation paths: verifying package integrity via checksums in the first case, and auditing for unauthorised accounts or scheduled tasks in the second.

## Reverse Shells

A payload's connection direction determines which network conditions it depends on:

- **Bind shell:** the target opens a listening port and waits for the attacker to connect inward. This depends on the target's firewall permitting unsolicited inbound connections, which is frequently blocked.
- **Reverse shell:** the target initiates the connection outward to the attacker. Outbound traffic is typically far less restricted than inbound traffic, since it resembles ordinary client behaviour such as browsing.

The listener parameters follow the same logic as the target parameters, reversed:

| Variable | Meaning | Points to |
|---|---|---|
| `RHOSTS` / `RPORT` | Remote host/port | The target |
| `LHOST` / `LPORT` | Local host/port | The attacker's own machine |

Port selection for `LPORT` is a matter of blending in rather than a technical requirement. Port 443 is the standard HTTPS port, and outbound traffic on it is rarely inspected or blocked, since doing so would interfere with normal internet access. A default Metasploit port such as 4444 is a known signature that a moderately configured IDS or firewall is more likely to flag.

## Post-Exploitation Enumeration for Privilege Escalation

Gaining a foothold does not conclude the assessment; the system must be enumerated again, this time from the inside. A common and effective check is searching for predictably named files that may contain credentials:

```
find / -name password* 2>/dev/null
```

`-name` filters by filename pattern, and `2>/dev/null` discards permission-denied noise generated while traversing directories the current user cannot access. This is a search pattern rather than an exploit: it succeeds when an administrator has stored a credential in plaintext at a predictable path, a class of finding categorised separately from a software vulnerability in a report, since the remediation (credential hygiene, use of a secrets manager) is unrelated to patching code.

Other filters serve related purposes during this phase:

| Filter | Purpose |
|---|---|
| `-name "*.conf"` | Configuration files, often containing credentials |
| `-perm -4000` | SUID binaries, a separate privilege escalation vector |
| `-newer /etc/passwd` | Recently modified files, useful for spotting tampering |

## Report Structure

A penetration test report is layered by audience, with technical depth increasing toward the end of the document:

1. **Cover page.** Title, author, contact, and version control. Version control matters because a report typically goes through drafts and a post-remediation retest, and without a version marker it is unclear which document is authoritative.
2. **Executive summary.** Written for the person who commissioned the engagement, in non-technical language, answering how exposed the organisation was.
3. **Technical summary** (optional). Written for an engineering manager who needs to prioritise but will not perform the remediation directly.
4. **Vulnerability table**, ordered by severity, used by both managers and engineers to decide what gets fixed first.
5. **Detailed exploitation section.** Written for the engineer performing remediation: technical description, impact, reproducible steps with evidence, and mitigation recommendations for each finding.

The guiding principle is that a manager reads the summary and an engineer reads the reproduction steps, and the document should let each stop where their role requires.
