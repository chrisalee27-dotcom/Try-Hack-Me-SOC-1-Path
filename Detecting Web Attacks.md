# TryHackMe SOC Level 1 - Detecting Web Attacks Room Write-up

**Author:** Chris (@chrisalee27)  
**Date:** February 2026  
**Path:** SOC Level 1  
**Room Link:** [Detecting Web Attacks](https://tryhackme.com/room/detectingwebattacks)  
**Difficulty:** Easy  
**Time:** ~60 minutes  

As part of my **SOC Level 1** path on TryHackMe, I completed the **Detecting Web Attacks** room. This was a really solid hands-on lab for learning how common web attacks look from the blue-team side — especially in logs and network traffic.

I learned how to tell the difference between **client-side** and **server-side** attacks, spot suspicious patterns in Apache access logs, dig through HTTP traffic in Wireshark/PCAP files, and understand how **Web Application Firewalls (WAFs)** block bad requests before they hit the app. It really drove home why monitoring web servers is such a big deal in a SOC — web apps are attacked constantly.

The room puts you in the shoes of investigating a breach at a fictional bank called **TryBankMe**, using the provided `access.log` and `traffic.pcap` files.

## What I Learned Overall

- **Client-side** attacks mostly happen in the victim's browser — very little shows up server-side.
- **Server-side** attacks leave obvious footprints: weird User-Agents, lots of 404s, repeated failed logins, suspicious parameters.
- Logs are great for finding reconnaissance (directory fuzzing), credential stuffing / brute-force, and injection attempts.
- PCAPs give you the full story — payloads, encoded data, full responses — that logs sometimes cut off or miss.
- WAFs act like a gatekeeper by checking headers, URIs, and bodies against attack signatures.
- Everyday tools like **grep**, **Wireshark**, **CyberChef** (for decoding), and basic filtering are must-haves for SOC web investigations.

## Task Breakdown & My Answers

### Task 1: Introduction
Quick overview — why web attacks are so common and where to look for evidence (logs, PCAPs, SIEMs, WAFs). No questions.

### Task 2: Client-Side Attacks

Client-side attacks abuse the **user’s browser**, **device**, or **behavior**. They’re tough to catch server-side because the malicious stuff often never reaches the server properly.

**Key takeaway:** Server logs give almost no visibility here — you need endpoint protection or client-side controls (like CSP) to stop these.

Questions:
- What class of attacks relies on exploiting the user’s behavior or device? **Client-Side**
- What is the most common client-side attack? **XSS**

### Task 3: Server-Side Attacks

These hit the server or application code directly — and they leave much clearer traces in requests and logs.

**Key takeaway:** Brute-force floods login pages, SQLi abuses input fields to dump databases, command injection sneaks OS commands through forms.

Questions:
- What class of attacks relies on exploiting vulnerabilities within web servers? **Server-Side**
- Which server-side attack lets attackers abuse forms to dump database contents? **SQLi**

### Task 4: Log-Based Detection

I worked with the Apache `access.log` (Common Log Format) and hunted for IOCs.

**What I looked for:**
- Weird User-Agents (e.g., scanner tools like FFUF)
- High volume of 404s (directory brute-forcing)
- Tons of POSTs to login/change forms with 401/302 responses
- Sketchy GET/POST parameters (SQL operators, encoded payloads, etc.)

Questions:
- What is the attacker’s User-Agent while performing the directory fuzz? **FFUF v2.1.0**
- What is the name of the page on which the attacker performs a brute-force attack? **/login.php**
- What is the complete, decoded SQLi payload the attacker uses on the /changeusername.php form? **' UNION SELECT 1,2,username,password FROM users --** (or the exact one you found — decoded via CyberChef if needed)

### Task 5: Network-Based Detection

Switched to **Wireshark** on `traffic.pcap` for full packet-level visibility.

**Key takeaway:** Follow HTTP streams → see full payloads, successful brute-force passwords, dumped database contents, and flags.

Questions:
- What password does the attacker successfully identify in the brute-force attack? **astrongpassword123**
- What is the flag the attacker found in the database using SQLi? **THM{dumped_the_db}** 

<img width="822" height="828" alt="5 1" src="https://github.com/user-attachments/assets/252188ab-3de6-4b3f-945c-71eecbda3c45" />

<img width="663" height="641" alt="5 2" src="https://github.com/user-attachments/assets/31f1a1d1-57cd-4101-b45c-47cf2160e6c7" />

<img width="839" height="547" alt="5 3" src="https://github.com/user-attachments/assets/ccfff53a-2c14-4426-8a63-eb388a821915" />

<img width="749" height="351" alt="5 4" src="https://github.com/user-attachments/assets/6779b2fb-c79c-4d7d-a188-10e7859e5f73" />


### Task 6: Web Application Firewall

Learned how WAFs inspect incoming traffic and block common attack patterns.

**Key takeaway:** WAFs catch a lot of low-hanging fruit (SQLi, XSS, command injection), but can be bypassed with encoding/obfuscation tricks.

Question:
- What do WAFs inspect and filter? **Web requests**

## Final Thoughts

This room was perfect for building real **blue-team** web hunting skills. I’m way more comfortable now grepping logs for weird patterns, filtering Wireshark for HTTP anomalies, and understanding why some attacks get through and others get blocked.

Super recommend it — especially early in the SOC path before jumping into SIEM or advanced detection rooms.

If you’re doing it too, feel free to comment with questions!

**Tools I used:**
- Text editor + **grep** for log hunting
- **Wireshark** for PCAP analysis
- **CyberChef** for decoding payloads
