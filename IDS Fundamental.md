# TryHackMe: IDS Fundamentals – Writeup & Learning Notes

**Room Link:** https://tryhackme.com/room/idsfundamentals  
**Path:** SOC Level 1 → Section 7: Network Security Monitoring (or related Security Solutions module)  
**Difficulty:** Easy to Medium (great intro to IDS concepts + hands-on Snort)  
**Estimated Time:** 1–2 hours  
**Completed:** February 2026  
**Author:** Chris (@chrisalee27)

## Overview
This room introduces **Intrusion Detection Systems (IDS)** as a core defensive tool that monitors network traffic for suspicious/malicious activity after it passes perimeter defenses like firewalls. It bridges theory (what IDS is, types, detection methods) with practical exposure to **Snort**, the popular open-source Network IDS (NIDS).

Key topics covered:
- Role of IDS vs. firewalls/IPS
- Signature-based, anomaly-based, and hybrid detection
- Snort architecture, modes (sniffer, logger, NIDS), rule structure
- Running Snort on PCAP files and live interfaces
- Basic rule writing/interpretation and alert generation

It fits perfectly after rooms like Network Security Essentials, firewalls intro, and prior NSM labs—prepares you for deeper Snort usage in the next room.

## Tasks & What I Did

### Task 1: What Is an IDS
- Read about IDS purpose: detect (not prevent) post-firewall malicious activity
- Compared IDS to surveillance cameras monitoring ongoing connections
- Answered theory questions on IDS capabilities and limitations

No hands-on here — solid foundational reading.

### Task 2: Types of IDS
- Studied deployment types: Network-based (NIDS) vs. Host-based (HIDS)
- Explored detection methods: signature-based (known patterns), anomaly-based (deviations from baseline), hybrid (combo)
- Answered questions identifying which type covers the whole network and which combines approaches

Key takeaway: NIDS is ideal for broad network visibility; HIDS catches host-specific threats.

### Task 3: IDS Example: Snort
- Learned Snort basics: open-source NIDS/IPS, rule-based engine
- Covered Snort modes: sniffer (live capture), logger (PCAP saving), NIDS (rule-based alerting)
- Understood components: rules files, preprocessors, output plugins
- Answered questions on modes and basic functionality

**What I learned:** Snort is lightweight, highly configurable, and widely used in production SOCs.

### Task 4: Snort Usage
- Explored Snort command-line syntax for different modes
- Ran Snort in sniffer/logger mode on interfaces/PCAPs
- Used NIDS mode with default rules to generate alerts
- Reviewed alert output formats (console, fast, full, unified2)

**Lab Questions**
- Where is the main directory of Snort that stores its files? /etc/snort
- Which field in the Snort rule indicates the revision number of the rule? rev
- Which protocol is defined in the sample rule created in the task? icmp
- What is the file name that contains custom rules for Snort? local.rules

<img width="200" height="100" alt="4 1 list the rules" src="https://github.com/user-attachments/assets/d57c3f28-9c22-4089-a201-d8260fed8aa9" />  

<img width="200" height="100" alt="4 2 Open local rules in text editor" src="https://github.com/user-attachments/assets/6e0bc0cf-09ca-4675-a0bd-6b6b6f77cac8" />

<img width="200" height="100" alt="4 3 add new rule" src="https://github.com/user-attachments/assets/6fdea8d7-fdd6-4256-bfb5-b1094a6ac21c" />

<img width="200" height="100" alt="4 4 start snort tool" src="https://github.com/user-attachments/assets/c57d0968-cb73-414f-a7d0-842575a32906" />

<img width="200" height="100" alt="4 5 test rule" src="https://github.com/user-attachments/assets/88f3100b-9459-4f3e-8b22-de045d306f1d" />

### Task 5: Practical Lab
- Deployed the lab machine
- Ran Snort against provided PCAP files using specific configs/rules
- Identified suspicious activity (e.g., protocol abuse, scans, or connections)
- Used filters/greps to extract attacker IPs, rule messages, or alert details from logs
- Correlated alerts to map malicious behavior

**What I learned:**
- Snort excels at rule-triggered alerts on known bad patterns
- Practical commands like `-r` (read PCAP), `-A console` (alerts to screen), `-c` (config file)
- Grepping logs for specific rule messages or IPs speeds up analysis

**Lab Questions**
- What is the IP address of the machine that tried to connect to the subject machine using SSH? 10.11.90.211
- What other rule message besides the SSH message is detected in the PCAP file? Ping Detected
- What is the sid of the rule that detects SSH? 1000002

<img width="934" height="458" alt="5 1 run pcap" src="https://github.com/user-attachments/assets/6b866308-1cf3-4f72-bb65-08cffbe220f9" />

## Key Takeaways & Skills Gained
- Understood IDS as detection-focused (vs. prevention-focused IPS)
- Differentiated NIDS/HIDS and signature/anomaly/hybrid methods
- Got hands-on with Snort: installation basics, modes, running on captures/live traffic
- Learned to interpret Snort alerts, rules, and output plugins
- Built comfort with CLI for IDS analysis — transferable to SIEM integrations
- MITRE alignment: Supports detection of various TTPs (e.g., initial access, execution, lateral movement)

## Tools & Commands I Used
- Snort CLI examples:
  - Sniffer mode: `sudo snort -i eth0`
  - Logger mode: `sudo snort -l /var/log/snort -r capture.pcap`
  - NIDS mode: `sudo snort -q -l /var/log/snort -r file.pcap -A console -c /etc/snort/snort.conf`
  - Common post-run: `grep "SSH" /var/log/snort/alert*` or `cat alert | grep "Detected"`
- Grep/awk/sort for parsing alerts (e.g., extract IPs or unique messages)

## Conclusion
Excellent bridge from theory to practice in the Network Security Monitoring section — demystifies IDS and gives real Snort exposure without overwhelming complexity. Feels like the perfect setup for the full Snort room next.

Strongly recommend for anyone building blue-team fundamentals. Wireshark + Snort combo is becoming second nature!

Next: Snort room or IDS/IPS deeper dives.

Chris  
February 2026
