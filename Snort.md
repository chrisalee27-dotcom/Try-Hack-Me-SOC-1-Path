# TryHackMe — Snort Room Walkthrough & Learning Notes (SOC Level 1 Path)

> **For educational purposes — my personal notes as Chris (@chrisalee27).**  
> I completed this room to build hands-on skills in network intrusion detection as part of SOC Level 1.  
> Room link: https://tryhackme.com/room/snort  
> Part of **SOC Level 1** → Network Security and Traffic Analysis module.

**Overall Room Goal:** Learn how to use **Snort** (open-source NIDS/NIPS) to detect real-time threats, analyze captured traffic (PCAPs), identify anomalies, and start writing basic detection rules.  
Snort helps SOC analysts monitor network traffic, spot malicious patterns, and generate alerts — core skills for Network Security Monitoring (NSM).

## Task 1: Introduction
**What I Learned:**  
Prerequisites + overview of Snort as an open-source, rule-based **Network Intrusion Detection/Prevention System (NIDS/NIPS)**. Importance of Linux CLI basics and network fundamentals (ports, protocols, traffic). Why Snort is widely used in SOCs for real-time and offline analysis.

## Task 2: Interactive Material and VM
**What I Learned:**  
How to interact with the provided TryHackMe VM, navigate to exercise folders, and run simple setup scripts. Gets you comfortable with the lab environment.

- **Question:** Navigate to Task-Exercises folder and run `./.easy.sh`. What is the output?
- **Answer:** `Too Easy!`
- 
<img width="729" height="402" alt="1 1 open traffic generator file" src="https://github.com/user-attachments/assets/f1005db6-af00-4bb3-9b79-166836a0ed45" />

<img width="749" height="239" alt="1 2" src="https://github.com/user-attachments/assets/90760758-1e2f-495a-9313-a485c4a526ab" />


## Task 3: Introduction to IDS/IPS
**What I Leared:**  
Core concepts of **Intrusion Detection Systems (IDS)** vs **Intrusion Prevention Systems (IPS)**. Differences between host-based vs network-based, detection vs prevention modes. Snort's flexibility (can operate as HIDS/HIPS/NIDS/NIPS/NBA). Terms like baselining (NBA training period) and "full-blown" NIPS.

| #  | Question                                                                 | Answer       |
|----|--------------------------------------------------------------------------|--------------|
| 1  | Snort mode to **stop** threats on a local machine?                       | `HIPS`       |
| 2  | Snort mode to **detect** threats on a local network?                     | `NIDS`       |
| 3  | Snort mode to **detect** threats on a local machine?                     | `HIDS`       |
| 4  | Snort mode to **stop** threats on a local network?                       | `NIPS`       |
| 5  | Mode similar to NIPS?                                                    | `NBA`        |
| 6  | Official description: what kind of NIPS is Snort?                        | `full-blown` |
| 7  | NBA training period is also known as…                                    | `baselining` |

## Task 4: First Interaction with Snort
**What I Learned:**  
Basic Snort installation check, version/build info, and how configuration files (`snort.conf`) load rules. Difference in rule counts shows how configs affect detection capabilities.

- **Q1:** Run Snort → build number?  
  **A1:** `149`

- **Q2:** Test with `/etc/snort/snort.conf` → rules loaded?  
  **A2:** `4151`

- **Q3:** Test with `/etc/snort/snortv2.conf` → rules loaded?  
  **A3:** `1`
  
<img width="752" height="181" alt="4 1 Snort Version Snort -V" src="https://github.com/user-attachments/assets/93ec6141-bb21-4a6b-8e23-8e666c0c8386" />

<img width="937" height="394" alt="4 2 config check" src="https://github.com/user-attachments/assets/4d37ac87-81c2-4a81-abfa-f9de7e0e283a" />

<img width="861" height="220" alt="4 3 rules count" src="https://github.com/user-attachments/assets/501dae62-7d30-4b0c-ac04-37a4ef8c39b2" />

<img width="957" height="377" alt="4 4 snortv2 test" src="https://github.com/user-attachments/assets/d8702043-f2b4-4c91-b4c4-b6fd6bf3dc6b" />

<img width="531" height="164" alt="4 5 1 rule" src="https://github.com/user-attachments/assets/d11aafa5-e291-44f5-8a35-f585e5b0c837" />


## Task 5: Operation Mode 1 — Sniffer Mode
**What I Learned:**  
Snort as a real-time packet sniffer (like a lightweight tcpdump). Command-line flags (`-v`, `-d`, `-e`, etc.) to control verbosity, display data link layer, etc. Great for quick traffic inspection.

## Task 6: Operation Mode 2 — Packet Logger Mode
**What I Learned:**  
Logging captured packets to disk for later analysis. Reading binary logs (`snort.log.*`) with Snort (`-r`), extracting fields like source ports, IP IDs, TCP ACKs, referers, and protocol stats. Builds foundation for offline forensics.

| #  | Question                                                                                  | Answer                              |
|----|-------------------------------------------------------------------------------------------|-------------------------------------|
| 1  | Folder `145.254.160.237` — source port to port 53?                                        | `3009`                              |
| 2  | `snort.log` — IP ID of 10th packet?                                                       | `49313`                             |
| 3  | `snort.log.1640048004` — referrer of 4th packet?                                          | `http://www.ethereal.com/development.htm` |
| 4  | `snort.log.1640048004` — Ack number of 8th packet?                                        | `0x38AFFFF3`                        |
| 5  | `snort.log.1640048004` — TCP port 80 packets?                                             | `41`                                |

<img width="823" height="505" alt="6 5 folders creation and unlocked" src="https://github.com/user-attachments/assets/cfea2b94-8e4d-419b-a7dc-53e6dcbd29e5" />

<img width="823" height="313" alt="6 6" src="https://github.com/user-attachments/assets/acc6f4a9-8ba3-4ade-a0f5-e88dcf45af11" />

<img width="920" height="518" alt="6 7" src="https://github.com/user-attachments/assets/c35d6e2f-1d0b-443d-873a-a5d1958c6979" />

<img width="907" height="741" alt="6 8" src="https://github.com/user-attachments/assets/6ad6a327-f278-45bb-b146-a59cccbb06fe" />

<img width="721" height="143" alt="6 9" src="https://github.com/user-attachments/assets/de74e59d-b3c1-4a1c-9997-5f49309069c5" />

<img width="930" height="876" alt="6 99" src="https://github.com/user-attachments/assets/d56cf679-178e-4e1f-a11c-96187f03cd7b" />


## Task 7: Operation Mode 3 — IDS/IPS
**What I Learned:**  
Running Snort in detection/prevention mode with rules. How it alerts on matches (e.g., HTTP GET methods). Combining with traffic generation for testing.

- **Q1:** Detected HTTP GET methods?  
  **A1:** `2`
  
<img width="886" height="538" alt="7 1 2 HTTP GET METHODS" src="https://github.com/user-attachments/assets/83de9a45-aa5d-4564-b34e-cd96dae7956e" />


## Task 8: Operation Mode 4 — PCAP Investigation
**What I Leared:**  
Offline analysis of PCAP files with Snort (`-r`). Reading alerts, stats (queued TCP segments, extracted HTTP headers), and aggregating results across multiple captures. Key for incident response / forensics when replaying evidence.

| #  | Question                                              | Answer |
|----|-------------------------------------------------------|--------|
| 1  | Alerts for `mx-1.pcap` (default)?                     | `170`  |
| 2  | TCP Segments Queued?                                  | `18`   |
| 3  | HTTP response headers extracted?                      | `3`    |
| 4  | Alerts for `mx-2.pcap` (default)?                     | `340`  |
| 5  | Detected TCP packets?                                 | `82`   |
| 6  | Total alerts `mx-2.pcap` + `mx-3.pcap`?               | `1020` |

<img width="884" height="42" alt="8 1" src="https://github.com/user-attachments/assets/370a173f-c972-494a-8f12-b7a440002fea" />

<img width="883" height="533" alt="8 2" src="https://github.com/user-attachments/assets/c73ef7a7-39de-4455-84f6-89dcb47ea2dc" />

<img width="886" height="531" alt="8 3" src="https://github.com/user-attachments/assets/322e42dc-31ec-4fd3-a11d-2ac4be1a3950" />

<img width="881" height="535" alt="8 4" src="https://github.com/user-attachments/assets/041b607c-b92b-434b-b601-ba0d5a7e71e4" />

<img width="872" height="39" alt="8 5" src="https://github.com/user-attachments/assets/768fb68b-8aa3-4900-a9e3-8b68150a37a7" />

<img width="864" height="247" alt="8 6" src="https://github.com/user-attachments/assets/eccdfadf-5666-4b9c-adf5-798a8f20483d" />

<img width="885" height="40" alt="8 7" src="https://github.com/user-attachments/assets/be1e2f0a-de34-4b2c-800c-c6ae1016f313" />

<img width="591" height="567" alt="8 8" src="https://github.com/user-attachments/assets/e566d40a-939b-4943-94bf-dc9a51b45b2d" />

<img width="775" height="527" alt="8 8b" src="https://github.com/user-attachments/assets/a457ca2a-1ef6-4e55-8113-67d6b4107d5e" />

<img width="882" height="49" alt="8 9" src="https://github.com/user-attachments/assets/fb2f4b92-271f-477b-a1b2-4a7ee957496e" />

<img width="588" height="510" alt="8 99" src="https://github.com/user-attachments/assets/fcef7d92-54a4-4c63-ae88-c65e64ca64cd" />


## Task 9: Snort Rule Structure
**What I Learned:**  
Core Snort rule anatomy: `action protocol src_ip src_port -> dst_ip dst_port (options)`. Writing simple rules (filters by IP ID, flags like SYN/PUSH-ACK, same src/dst IP). Testing rules against PCAPs and clearing logs. Essential skill for custom detection.

| #  | Question                                                                                   | Answer              |
|----|--------------------------------------------------------------------------------------------|---------------------|
| 1  | `task9.pcap` — rule for IP ID `35369`. Request name?                                       | `TIMESTAMP REQUEST` |
| 2  | Rule for SYN flag packets. Detected?                                                       | `1`                 |
| 3  | Clear logs → rule for Push-Ack. Detected?                                                  | `216`               |
| 4  | Clear logs → same src & dst IP. Detected?                                                  | `7`                 |

<img width="888" height="191" alt="9 1" src="https://github.com/user-attachments/assets/3600e1f1-87d0-46cf-85b4-6ae9f38cb7b2" />

<img width="901" height="199" alt="9 2" src="https://github.com/user-attachments/assets/9678ea6b-1805-4904-ad18-6b7f63e4d055" />

<img width="882" height="197" alt="9 3" src="https://github.com/user-attachments/assets/e37d1755-b82c-4acd-a176-7701971ba5ab" />

<img width="899" height="379" alt="9 4" src="https://github.com/user-attachments/assets/8223da69-463d-4549-8633-3b9b1919ae4b" />

<img width="899" height="194" alt="9 5" src="https://github.com/user-attachments/assets/f832e316-7b3a-4daf-b99b-832296e6541c" />

<img width="901" height="249" alt="9 6" src="https://github.com/user-attachments/assets/a98b9968-c2e3-4a92-8b90-ce9567e46f23" />

<img width="916" height="235" alt="9 7" src="https://github.com/user-attachments/assets/fc8da6a3-daa6-4a83-9b72-57f56e2d8457" />

<img width="885" height="262" alt="9 8" src="https://github.com/user-attachments/assets/65e9ffe8-b87c-4ee6-8bcb-975dbbcb0cdd" />

## Task 10: Snort2 Operation Logic — Points to Remember
**What I Learned:**  
Key operational reminders, rule logic, common pitfalls, best practices for Snort configs and rule management.


## Task 11: Conclusion
**What I Learned:**  
Recap of Snort's power in SOC workflows: real-time detection, offline analysis, custom rules. You're now ready to apply this in alerting triage, NSM, and incident investigations.

--- 
Snort is a foundational tool for any SOC analyst — understanding its modes and rule writing makes a big difference in traffic analysis.
