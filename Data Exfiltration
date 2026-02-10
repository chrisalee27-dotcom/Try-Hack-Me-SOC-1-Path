# TryHackMe: Data Exfiltration Detection – Writeup & Learning Notes

**Room Link:** https://tryhackme.com/room/dataexfildetection  
**Path:** SOC Level 1 → Section 7: Network Security Monitoring  
**Difficulty:** Medium (great for junior SOC analysts)  
**Estimated Time:** 1–2 hours  
**Completed:** February 2026  
**Author:** Chris (@chrisalee27)

## Overview
This room simulates real-world SOC analyst work: detecting **data exfiltration** attempts where attackers sneak sensitive data out of a network using legitimate-looking protocols.

Key exfiltration channels covered:
- DNS tunneling
- FTP uploads
- HTTP POST requests
- ICMP (ping) packets with hidden payloads

Tools used:
- **Splunk** → for log correlation and hunting suspicious patterns
- **Wireshark** → for deep packet inspection and following streams

The lab provides pre-captured logs and PCAPs so you can focus on analysis rather than generating traffic.

## Tasks & What I Did

### Task 1: Introduction
- Read background on data exfiltration
- Learned why it's hard to detect (blends into normal traffic)
- Understood MITRE ATT&CK mapping (primarily TA0010 – Exfiltration)

No questions here — just foundational reading.

### Task 2: Lab Connection
- Deployed the TryHackMe attack box / lab VM
- Accessed Splunk web interface and Wireshark via the provided machine
- Verified connectivity and opened sample logs/PCAP files

### Task 3: Data Exfil – Overview, Techniques, and Indicators
- Studied common exfiltration methods and their network signatures
- Answered theory question about classification of HTTP-based exfil
- Reviewed indicators like unusual volume, oversized payloads, odd domains, anonymous logins, etc.

Key takeaway: Most exfil happens over "trusted" protocols — baseline knowledge is crucial.

### Task 4: Detection – Data Exfil through DNS Tunneling
- Used Splunk to query DNS logs for anomalies
- Looked for high query volume to single external domain
- Identified the suspicious domain and originating internal IP
- Counted total suspicious events related to tunneling behavior

**What I learned:**
- DNS tunneling often shows many queries to the same unusual domain
- High entropy subdomains or long query names are red flags

<img width="745" height="587" alt="Susdomain" src="https://github.com/user-attachments/assets/a392fbc3-8e83-4b06-94aa-519b5551efc0" />

<img width="897" height="483" alt="IP most sus requests" src="https://github.com/user-attachments/assets/0317a080-a7aa-4bbc-b896-ba6cb9ceeb80" />


### Task 5: Detection – Data Exfil through FTP
- Filtered FTP traffic in Splunk and Wireshark
- Looked for anonymous/guest logins and file upload commands (STOR)
- Followed the FTP data stream in Wireshark to recover exfiltrated content
- Identified the compromised account and sensitive file name
- Extracted the hidden flag from the transferred data

**What I learned:**
- Anonymous FTP is still a favorite for quick exfil
- Following TCP streams in Wireshark reveals cleartext content
- 
<img width="813" height="208" alt="5 guest users" src="https://github.com/user-attachments/assets/9aac3c57-27fc-44b8-a07e-28dea92a6e0f" />

<img width="879" height="571" alt="follow tcp strea" src="https://github.com/user-attachments/assets/6c166307-f919-48eb-8181-3af679e5050a" />

<img width="798" height="603" alt="customer_data xlsx" src="https://github.com/user-attachments/assets/baef885c-cdf2-4410-9103-20bb94488595" />

<img width="905" height="296" alt="largest payload src ip" src="https://github.com/user-attachments/assets/e421f172-c56b-40ae-a179-7e1539a48b63" />

<img width="901" height="581" alt="follow tcp to flag" src="https://github.com/user-attachments/assets/312319b6-4c25-4425-abc5-bbd6748129db" />

<img width="787" height="601" alt="tcp flag" src="https://github.com/user-attachments/assets/c06b2ea9-3c96-41c8-ae8d-25ebee913739" />

### Task 6: Detection – Data Exfil through HTTP (and/or ICMP if present)
- Queried Splunk for large outbound HTTP POSTs or unusual user-agents
- In Wireshark: filtered http.request.method == "POST" and checked request sizes/bodies
- Looked for base64-like payloads or chunked transfers hiding data
- (If ICMP section existed) filtered icmp.type == 8 and inspected payload sizes (> normal ping)

**What I learned:**
- HTTP exfil often hides in POST bodies or headers
- ICMP can carry small chunks of data in echo requests/replies
  
<img width="904" height="173" alt="comromised host exfiltrating data" src="https://github.com/user-attachments/assets/9e86d664-6954-4c8e-a45e-c7113ac044f4" />

<img width="712" height="546" alt="follow http stream" src="https://github.com/user-attachments/assets/2182b4df-c94b-48be-802d-cc0e606cc232" />

<img width="828" height="505" alt="tryhackme flag http" src="https://github.com/user-attachments/assets/c269b182-0089-46b5-8afe-a9b290a8adf8" />

<img width="870" height="566" alt="last flag" src="https://github.com/user-attachments/assets/06d99df1-3aa5-4376-a801-f9139e6ea13f" />

## Key Takeaways & Skills Gained
- Built practical Splunk queries for hunting (e.g., counting events by src_ip, dest_host, protocol)
- Mastered Wireshark filters and Follow → TCP/UDP Stream for recovering exfil data
- Recognized protocol abuse patterns (high-volume DNS, guest FTP, bulky HTTP/ICMP)
- Understood why baselining normal traffic is essential for anomaly detection
- Reinforced MITRE ATT&CK TA0010 sub-techniques (T1048, T1567, etc.)

## Tools & Commands I Used
- Splunk examples:
  - `index=exfil sourcetype=dns | stats count by query`
  - `index=exfil sourcetype=ftp | search "STOR" OR "guest"`
- Wireshark filters:
  - `dns` / `ftp` / `http.request.method == "POST"` / `icmp.type == 8`
  - Right-click → Follow → TCP Stream to view payloads

## Conclusion
This room is an excellent hands-on intro to one of the hardest parts of SOC work — catching data leaving the building quietly. Highly recommend it to anyone on the SOC Level 1 path after completing Network Discovery Detection.

Completed and enjoyed — ready for Man-in-the-Middle Detection next!
