# TryHackMe SOC Level 1 - Detecting Web Shells Room Write-up

**Author:** Chris (@chrisalee27)  
**Date:** February 2026  
**Path:** SOC Level 1  
**Room Link:** [Detecting Web Shells](https://tryhackme.com/room/detectingwebshells)  
**Difficulty:** Easy  
**Time:** ~60 minutes  

Continuing right after **Detecting Web Attacks** in my **SOC Level 1** path on TryHackMe, I tackled the **Detecting Web Shells** room. This one builds perfectly on the previous — shifting from detecting attacks in flight to spotting the persistent backdoors (**web shells**) attackers leave behind after gaining access.

I learned what web shells actually are, how attackers deploy and use them for persistence, and — most importantly for blue team — practical ways to hunt them down using **web server logs**, **file system artifacts**, **audit logs** (like auditd), and **network traffic** (PCAPs). It emphasized that web shells are a super common post-exploitation technique (MITRE T1505.003), often hidden in web directories, and why early detection is key to stopping lateral movement or data exfil.

The room simulates investigating a compromised web server (likely the same TryBankMe scenario vibe), with access to logs, files, and a sample shell to interact with.

## What I Learned Overall

- Web shells are scripts (often PHP, ASPX, JSP) uploaded via vulnerabilities like file upload flaws, command injection, or RCE — giving attackers remote command execution via HTTP.
- They show up as persistence under **MITRE ATT&CK T1505.003 Server Software Component: Web Shell**.
- Detection relies on multiple sources: unusual HTTP methods/patterns in logs, suspicious files (odd extensions, names, locations), audit trails for file creation, and network anomalies like repeated GETs with cmd parameters.
- Common IOCs: weird User-Agents, POSTs to upload endpoints followed by GETs to the shell, files in /tmp/, /uploads/, web roots with .php/.aspx that look innocent but eval() code.
- Tools like **grep**, **find**, **auditd** queries, **Wireshark** (for HTTP streams), and basic file inspection are essential for SOC web shell hunts.
- Microsoft Exchange targets often use **.aspx** webshells (e.g., ProxyShell exploits).

## Task Breakdown & My Answers

### Task 1: Introduction
Overview of why detecting web shells matters for SOC/IR teams — they're a top persistence method after initial compromise. No questions, but it tied back to real-world breaches where webshells enabled long-term access.

### Task 2: Web Shell Overview

**Key takeaway:** Web shells provide attackers with a simple, HTTP-based backdoor. They're language-agnostic but tied to the web server's interpreter (PHP most common).

Questions:

- Which MITRE ATT&CK Persistence sub-technique are web shells associated with? **T1505.003**
- What file extension is commonly used for web shells targeting Microsoft Exchange? **.aspx**

<img width="1452" height="514" alt="1 1" src="https://github.com/user-attachments/assets/a852dedf-0d8f-4e11-9b91-a685a4252792" />

### Task 3: Anatomy of a Web Shell

Got hands-on with a sample shell — accessed it and ran basic commands to see how it works from the attacker's side.

**Key takeaway:** Even simple webshells let attackers run system commands, browse files, upload more tools — often as the web server user (www-data, etc.).

Question:

- Access the shell and determine which account you have access to by running the `whoami` command. **www-data**

<img width="618" height="231" alt="2 1" src="https://github.com/user-attachments/assets/f6265f9e-222b-4709-8c11-8b0a00956970" />

<img width="626" height="300" alt="2 2" src="https://github.com/user-attachments/assets/47e8bb13-da27-4d8d-8415-2348cdc3cc52" />

<img width="592" height="254" alt="2 3" src="https://github.com/user-attachments/assets/a72998a7-b10b-43a8-b6b3-ddb2b4306b5b" />

### Task 4: Log-Based Detection

Hunted through web server logs (Apache/Nginx style) for signs of upload + execution.

**What I looked for:**
- Suspicious POST requests to upload.php or similar (file creation)
- Follow-up GET requests to weird .php files with parameters like ?cmd=whoami or ?c=ls
- Repeated GETs in short time (interactive use)
- Unusual query strings or HTTP methods

Questions:

- What data in web logs often contains the command sent to a web shell? **Query Strings**
- What auditd syscall would confirm that a file was written to disk following a suspicious POST request to `/upload.php`? **creat**

### Task 5: File System Detection (or similar task)

Looked at common web shell hiding spots: web roots, temp dirs, CMS plugins, etc.

**Key takeaway:** File names like shell.php, up.php, error_log.php (masquerading), odd timestamps, or code containing eval(), system(), passthru().

-What command would you use to locate .php files in the /var/www/ directory? find /var/www/ -type f -name "*.php"
-Which Wireshark filter would you use to search specifically for PUT requests? http.request.method == "PUT"

### Task 6: Network-Based Detection

Analyzed PCAP for webshell traffic — often small, frequent HTTP GET/POST with base64 or encoded commands in params.

**Key takeaway:** Follow streams to see cmd output in responses; look for non-standard User-Agents or high-frequency requests to one endpoint.

Questions:

-Which IP address likely belongs to the attacker? 203.0.113.66

<img width="725" height="459" alt="3 1" src="https://github.com/user-attachments/assets/72e64908-9e58-4aa4-b7c5-919159b8002b" />

-What is the first directory that the attacker successfully identifies? /wordpress

<img width="725" height="459" alt="3 2" src="https://github.com/user-attachments/assets/cfd70d6c-e455-442a-9601-69c06067a94d" />

-What is the name of the .php file the attacker uses to upload the web shell? upload_form.php

<img width="971" height="195" alt="3 3" src="https://github.com/user-attachments/assets/fc1904d5-dce1-4adc-b8e0-7283154b5c71" />

-What is the first command run by the attacker using the newly uploaded web shell? whoami

<img width="946" height="387" alt="3 4" src="https://github.com/user-attachments/assets/9fe42d71-84b8-4aff-9b5a-5056e404cbc0" />

-After gaining access via the web shell, the attacker uses a command to download a second file onto the server. What is the name of this file? linpeas.sh

<img width="960" height="209" alt="3 5" src="https://github.com/user-attachments/assets/46eb7290-cd90-4f40-9cee-98a167bbfa57" />

-The attacker has hidden a secret within the web shell. Use cat to investigate the web shell code and find the flag. linpeas.sh

<img width="815" height="223" alt="3 6" src="https://github.com/user-attachments/assets/1eee777a-ca15-4816-bbcc-d856c6fecb13" />

## Final Thoughts

This room was a great follow-up — it connected the dots from attack detection to post-compromise persistence hunting. I’m now better at spotting the subtle signs of webshells in logs/files/network, which feels like a big step up in blue-team capability.

If you're on the SOC path, do **Detecting Web Attacks** first — then this one flows naturally. It’s beginner-friendly but packed with practical IOCs and hunt techniques.

Questions or different answers from the room? Drop them below!

**Tools I used:**
- **grep** / text editor for log hunting
- **find** / **ls** for file system checks
- **auditd** queries for syscalls
- **Wireshark** for network confirmation
- Basic shell commands (whoami, ls, cat) on the sample webshell
