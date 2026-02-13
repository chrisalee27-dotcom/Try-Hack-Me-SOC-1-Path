# TryHackMe - Detecting Web DDoS Room Write-Up

Hey everyone! I'm **Chris** (https://www.linkedin.com/in/christopher-lee-a5a76b182/ on linkedin), and I recently completed the **Detecting Web DDoS** room on TryHackMe as part of the **SOC Level 1** learning path.

This room gave me a solid hands-on introduction to **denial-of-service (DoS)** and **distributed denial-of-service (DDoS)** attacks — especially application-layer (Layer 7) attacks. It covered attack mechanics, attacker motives, log-based detection, SIEM-powered analysis with Splunk, and real mitigation strategies.

In this write-up I share my experience, key takeaways, and all the questions + answers from each task. I'll add screenshots of my work (logs, Splunk queries, completion badge, etc.) once I upload them.

## What I Learned

- **DoS vs DDoS basics**: Single-source vs botnet-amplified attacks
- **Layer 7 attack techniques**: HTTP floods, Slowloris, form abuse, cache-busting, oversized queries
- **Attack motives**: Financial extortion, hacktivism, reputational damage, distraction, denial-of-wallet, competition
- **Detection indicators in web logs**:
  - Extremely high request rates to one URI
  - Suspicious / scripted user-agents (e.g. `Java/1.8.0_181`, `curl/7.x`)
  - Clustered attacking IPs (botnet patterns)
  - Shift from 200 → **503 Service Unavailable** for legitimate users
- **SIEM power (Splunk)**: Field extraction, `stats`, `timechart`, filtering bot vs legit traffic, visualizing request spikes
- **Defenses**: CAPTCHA, rate limiting, WAF rules, CDNs with load-balancing and edge caching
- **Log limitations**: If the server fully crashes, logging may stop — highlighting why network-layer visibility (e.g. NetFlow, IDS) is also important

The room took me roughly **2–3 hours** and really helped bridge theory → practical SOC detection workflow.

## Task Breakdown

### Task 1: Introduction
Overview of room objectives — no questions.

### Task 2: DoS and DDoS Attacks

**Questions & Answers**

- **What class of attack relies on disrupting the availability of a web service?**  
  **denial-of-service**

- **What do we call the network of compromised machines that attackers use to launch DDoS attacks?**  
  **botnet**

### Task 3: Attack Motives

**Questions & Answers**

- **Which attacker motive aims to make customers lose confidence in a company?**  
  **Reputational Damage**

- **Which motive most likely drove the 2023 DDoS attack against Microsoft?**  
  **Hacktivism**

### Task 4: Log Analysis

Analyzed `access.log` manually with `grep`, `awk`, etc. Found flood to `/login`, attacker IP starting the wave, and 503 errors hitting legit users afterward.

**Questions & Answers**

- **What is the attacker’s IP address?**  
  **203.12.23.195**
  
<img width="1098" height="638" alt="4 1" src="https://github.com/user-attachments/assets/f92b24e9-0152-478a-86de-0a7679b8650d" />

- **Which page is repeatedly targeted by the attacker’s requests?**  
  **/login**
  
<img width="959" height="504" alt="4 2" src="https://github.com/user-attachments/assets/638f8e0c-72aa-4c59-bbd7-8fb27cb3cc15" />

- **After the attack, what error code do legitimate users receive?**  
  **503**
  
<img width="942" height="664" alt="4 3" src="https://github.com/user-attachments/assets/8877fc7e-a536-4499-9d24-607a18205838" />

### Task 5: Leveraging SIEMs

Switched to **Splunk** — huge time-saver. Used fields like `clientip`, `uri_path`, `useragent`, `status`. Ran queries like `timechart span=1s count`, filtered botnet IPs (`203.0.*`), and visualized the spike.

**Questions & Answers**

- **What was the most frequently requested uri?**  
  **/search**
  
<img width="968" height="392" alt="5 1" src="https://github.com/user-attachments/assets/eaf1c13d-e410-4f6f-8b61-91bd0e83971d" />

- **Which clientip made the most requests to the target uri?**  
  **203.0.113.7**
  
<img width="950" height="423" alt="5 2" src="https://github.com/user-attachments/assets/52451a8a-b07e-4d61-828d-4d84461a8d77" />

<img width="957" height="307" alt="5 3" src="https://github.com/user-attachments/assets/6ce11ba6-6cd1-4373-8c21-ddb88e8f8ca2" />
- **How many IP addresses were part of the botnet that attacked your website?**  
  **60**
  
  <img width="957" height="321" alt="5 4" src="https://github.com/user-attachments/assets/60b74113-925d-44d5-bf93-f83527c9605e" />

- **Which useragent was most commonly used by the attacking traffic?**  
  **Java/1.8.0_181**
  
<img width="958" height="323" alt="5 5" src="https://github.com/user-attachments/assets/26d71ab1-4a36-44a8-8a67-22bf8e08ba53" />

- **Use the timechart command to visualize the requests. What is the peak number of requests made per second during the attack?**  
  **207**
  
<img width="965" height="318" alt="5 6" src="https://github.com/user-attachments/assets/be2c58fd-edd0-419a-a732-f920a2da2165" />

- **Which legitimate (non-attacking) clientip received the first 503 response status post-attack?**  
  **10.10.0.27**
  
<img width="953" height="281" alt="5 7" src="https://github.com/user-attachments/assets/3cec5cf6-8c01-4d28-b2d7-db24c916a70b" />

### Task 6: Defense

Covered practical mitigations and their trade-offs.

**Questions & Answers**

- **What type of security challenge blocks bots by asking users to solve a simple puzzle?**  
  **CAPTCHA**

- **Which CDN feature spreads traffic across multiple servers to prevent overload?**  
  **Load-balancing**

### Task 7: Conclusion
Room summary — no questions.

## Final Thoughts

This room was excellent for building **log analysis** and **SIEM hunting** muscle memory. Seeing how quickly a simple `/login` or `/search` flood can take down a site — and how Splunk turns chaos into clear patterns — was super valuable.

If you're doing the **SOC Level 1** path, definitely knock this one out early. Next rooms on my list: more THM SOC modules + maybe some blue-team-focused content.

Feel free to comment with questions or your own tips!

Thanks for reading!  
**Chris**  
February 13, 2026  
Saint Augustine, FL 🚀
