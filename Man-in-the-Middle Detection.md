# TryHackMe: Man-in-the-Middle Detection – Writeup & Learning Notes

**Room Link:** https://tryhackme.com/room/mitmdetection  
**Path:** SOC Level 1 → Section 7: Network Security Monitoring  
**Difficulty:** Medium (solid Wireshark practice for juniors)  
**Estimated Time:** 1–2 hours  
**Completed:** February 2026  
**Author:** Chris (@chrisalee27)

## Overview
This room puts you in the shoes of a SOC analyst investigating a suspected **Man-in-the-Middle (MITM)** attack on a corporate network. Attackers insert themselves between legitimate parties to intercept, modify, or redirect traffic—often starting with local network tricks and escalating to protocol abuse.

Key MITM techniques covered:
- ARP spoofing/poisoning
- DNS spoofing
- SSL stripping / HTTPS downgrade attacks

You analyze a provided PCAP file and logs to spot the attack chain using **Wireshark** primarily (with some Splunk cross-reference). It builds perfectly on prior rooms like Network Security Essentials, Network Discovery Detection, and Data Exfiltration Detection.

## Tasks & What I Did

### Task 1: Introduction
- Read about MITM basics: why it's dangerous (interception without detection), common vectors, and real-world impact.
- Understood MITRE ATT&CK ties (e.g., T1557 – Adversary-in-the-Middle).

No questions — foundational theory.

### Task 2: Lab Connection
- Deployed the lab environment
- Opened Wireshark with the provided capture file (PCAP)
- Verified access to any Splunk instance if referenced
  
<img width="807" height="535" alt="network_traffic pcap" src="https://github.com/user-attachments/assets/bacec15d-efca-4cbe-b8b4-bea7eecde051" />

### Task 3: MITM Attacks – An Overview
- Studied the MITM attack chain: starts local (ARP), moves to redirection (DNS), ends with interception/downgrade (SSL stripping)
- Learned red flags like duplicate MACs, unexpected redirects, plaintext creds after HTTPS attempts

Key takeaway: MITM often chains techniques — detect early to stop escalation.

### Task 4: Detecting ARP Spoofing
- Filtered ARP traffic in Wireshark (e.g., `arp`)
- Identified gratuitous ARP replies and duplicate MAC usage
- Counted ARP packets from the legitimate gateway MAC
- Spotted the attacker's MAC impersonating the gateway
- Confirmed the attacker's IP/MAC pair

**What I learned:**
- ARP has no authentication — easy to poison ARP tables
- Look for duplicate responses or gratuitous ARPs claiming ownership
**Lab Questions
- How many ARP packets from the gateway MAC Address were observed?  10
- What MAC address was used by the attacker to impersonate the gateway?  02:fe:fe:fe:55:55
- How many Gratuitous ARP replies were observed for 192.168.10.1? 2
- How many unique MAC addresses claimed the same IP (192.168.10.1)? 2
  
<img width="779" height="294" alt="10 ARP from gateway MAC" src="https://github.com/user-attachments/assets/162903cd-4186-4d13-8369-96a4d0dde1e1" />

<img width="779" height="294" alt="10 ARP from gateway MAC" src="https://github.com/user-attachments/assets/bac00238-88e4-4ea6-a6ce-6dfffe418f1e" />

<img width="964" height="247" alt="2 grat for ip" src="https://github.com/user-attachments/assets/fc88f165-4452-418a-a93d-78f3d78e979f" />

<img width="951" height="685" alt="duplicate mac" src="https://github.com/user-attachments/assets/d744bb33-7bc6-43c5-9973-70bce9759ada" />

### Task 5: Unmasking DNS Spoofing
- Applied DNS filters (e.g., `dns`)
- Looked for anomalous responses (unexpected IPs for internal domains)
- Counted DNS responses for spoofed/corporate domains
- Traced poisoned resolutions back to the attacker's involvement

**What I learned:**
- DNS spoofing redirects victims to attacker-controlled hosts
- Compare expected vs. observed A records for internal resources
**Lab Questions
-How many DNS responses were observed for the domain corp-login.acme-corp.local? 211
How many DNS requests were observed from the IPs other than 8.8.8.8? 2
What IP did the attacker’s forged DNS response return for the domain? 192.168.10.55

<img width="890" height="590" alt="outlier ip" src="https://github.com/user-attachments/assets/21d2a9bc-00d7-4a25-8984-ddfd13ee5438" />

<img width="857" height="565" alt="our ip 8 8 8 8" src="https://github.com/user-attachments/assets/0f4a1f38-911e-42dc-9d97-67e28a51ec95" />

<img width="848" height="729" alt="corp login name" src="https://github.com/user-attachments/assets/c6cbf9ea-c032-4471-ba6e-c1a4756a1ec0" />

<img width="872" height="808" alt="domain of interest" src="https://github.com/user-attachments/assets/bf1084fb-2b51-4d9e-a6ab-dccf2ec0e50d" />

<img width="896" height="791" alt="!" src="https://github.com/user-attachments/assets/9f388351-0742-4217-bb1a-0b179adb537d" />

<img width="912" height="818" alt="211" src="https://github.com/user-attachments/assets/fcd6af26-6d0d-4c11-ae26-92427231e028" />

### Task 6: Detecting SSL Stripping / HTTPS Downgrade (and chained attack confirmation)
- Filtered HTTP/HTTPS traffic (e.g., `http` or `tls`)
- Identified downgrade attempts (HTTPS requests turning to HTTP)
- Followed streams to spot plaintext credentials or sensitive data exposure
- Correlated back to earlier ARP/DNS steps to map the full chain
- (If Splunk used) Cross-checked logs for related events

**What I learned:**
- SSL stripping forces downgrade by stripping HTTPS → exposes creds
- Follow TCP/HTTP streams to recover leaked data
- Full MITM detection requires piecing together protocol abuses
**Lab Questions
-How many POST requests were observed for our domain corp-login.acme-corp.local? 1
-What's the password of the victim found in the plaintext after successful ssl stripping attack? Secret123!

<img width="876" height="788" alt="TLS  SSL" src="https://github.com/user-attachments/assets/1bfb4321-057e-454a-88fa-49fe5d0aeb2b" />

<img width="898" height="760" alt="established tls handshake" src="https://github.com/user-attachments/assets/064c734f-6ce0-4f86-b9f7-1e8c797f60e7" />

<img width="869" height="790" alt="isolage dns responses from the attacker" src="https://github.com/user-attachments/assets/77173643-a1ff-4e41-87ed-f15ed66c1861" />

<img width="867" height="458" alt="ssl stripping login" src="https://github.com/user-attachments/assets/c0d5b93d-e0b5-4400-ad38-7124d3ceac82" />

## Key Takeaways & Skills Gained
- Mastered Wireshark for MITM hunting: ARP, DNS, HTTP/TLS filters + stream following
- Recognized attack chains: ARP poison → DNS spoof → SSL strip → credential theft
- Built intuition for anomalies: duplicate MACs, unexpected DNS IPs, plaintext after HTTPS
- Reinforced why encryption + certificate pinning matter in defense
- MITRE alignment: T1557 sub-techniques, network-based interception

## Tools & Commands/Filters I Used
- Wireshark filters examples:
  - `arp` / `arp.isgratuitous`
  - `dns` / `dns.qry.name contains "corp-login"`
  - `http.request.method == "POST"` or `tls.handshake.type == 1`
  - Right-click → Follow → TCP Stream / HTTP Stream
- (Optional Splunk) Queries for correlated events by IP/MAC

## Conclusion
Great progression in the Network Security Monitoring section — this room sharpens packet-level hunting for sneaky local attacks. Wireshark felt more natural after this one. Strongly recommend after the previous exfil room.

Next up: IDS Fundamentals or Snort!

Happy defending! 🔍🛡️

Chris  
February 2026

