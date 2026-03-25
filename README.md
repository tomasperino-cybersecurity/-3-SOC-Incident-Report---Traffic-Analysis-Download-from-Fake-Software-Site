🕵️‍♂️ # -3-SOC-Incident-Report---Traffic-Analysis-Download-from-Fake-Software-Site
You work as an analyst at a Security Operation Center (SOC). Someone contacts your team to report a coworker has downloaded a suspicious file after searching for Google Authenticator. 

🌎 BACKGROUND
You work as an analyst at a Security Operation Center (SOC). Someone contacts your team to report a coworker has downloaded a suspicious file after searching for Google Authenticator. The caller provides some information similar to social media posts at:

- https://www.linkedin.com/posts/unit42_2025-01-22-wednesday-a-malicious-ad-led-activity-7288213662329192450-ky3V/

- https://x.com/Unit42_Intel/status/1882448037030584611

Based on the caller's initial information, you confirm there was an infection.  You retrieve a packet capture (pcap) of the associated traffic.  Reviewing the traffic, you find several indicators matching details from a Github page referenced in the above social media posts.  After confirming an infection happened, you begin writing an incident report.

The program used to analyze the packets was Wireshark 🦈​

🌎​ Environment Details

- LAN segment range:  10.1.17[.]0/24   (10.1.17[.]0 through 10.1.17[.]255)
- Domain:  bluemoontuesday[.]com
- Active Directory (AD) domain controller:  10.1.17[.]2 - WIN-GSH54QLW48D
- AD environment name:  BLUEMOONTUESDAY
- LAN segment gateway:  10.1.17[.]1
- LAN segment broadcast address:  10.1.17[.]255

​🔎​ Findings:

- Infected IP Address: 10.1.17.215
- Infected MAC Address: 00:d0:b7:26:4a:74
- Infected Hostname: DESKTOP-L8C5GSJ
- Infected Windows user account name: shutchenson
- Fake Software Site for initial malware download: authenticatoor.org
- Suspicious IP Addresses: 5.252.153.241, 45.125.66.32, 45.125.66.252

⚠️​ Activity: A coworker accessed a malicious software website—in this case, a fake Google authenticator—confirming the infection. This malicious file attempts to perform a Command & Control (C2) attack. In other words, C2 is the infrastructure and set of techniques used by attackers to remotely communicate with and control infected systems within a target network. The downloaded file is a means to remotely control our systems.

🛜​ Network Behavior: First we can confirm access to a fake website "authenticatoor.org" which even has typographical errors. Second, initial connection to the fake site. Third, we noticed persistent connections with 2 IP addresses.

🧐​ Process & Filters:
<img width="359" height="505" alt="image" src="https://github.com/user-attachments/assets/842ede30-59ba-4971-b94f-cdad80064ce4" />

<img width="524" height="131" alt="image" src="https://github.com/user-attachments/assets/7b2cc027-65a5-497f-a1a4-6283024b8c31" />

With this information we can search for matches in the traffic to identify the malicious/suspicious packet.

<img width="776" height="271" alt="image" src="https://github.com/user-attachments/assets/dd66550b-71fa-453b-9174-83a8fe56707e" />

This packet gives us the infected IP address, the infected MAC address, and the first malicious IP.

<img width="685" height="91" alt="image" src="https://github.com/user-attachments/assets/87b38ca4-57e3-4968-8cdd-7840b64ec183" />

nbns && ip.addr == 10.1.17.215
- With this filter we can find out the infected hostname.

<img width="837" height="79" alt="image" src="https://github.com/user-attachments/assets/452e9142-b02d-4e92-94ef-2af7b64af713" />

Now we are looking for the malicious link, which we have identified as "authenticatoor.org"
- The attacker uses a ".org" domain that does not belong to the true purpose of the page, nor to the purpose it appears to be.
- The attacker seeks to disguise the link by duplicating a letter in the URL

<img width="1600" height="119" alt="image" src="https://github.com/user-attachments/assets/9c74f19b-7a28-448a-80ac-fc6b226795f0" />

<img width="1057" height="149" alt="image" src="https://github.com/user-attachments/assets/e95d97fb-0314-41f0-9bd8-35d247c28185" />

The traffic information, which identifies several IP addresses, is available in the GitHub repository. These IP addresses will then be searched for using Wireshark.

<img width="1336" height="427" alt="image" src="https://github.com/user-attachments/assets/8dae9952-35b1-410e-b209-5098938d1925" />

<img width="1253" height="395" alt="image" src="https://github.com/user-attachments/assets/c983b257-6ee6-4e20-84cf-298ae616e061" />

😎​ Conclusion: A coworker searched for the official Google authenticator, and without realizing it, entered a malicious URL and downloaded the unofficial, malicious file, which initiated the connection with malicious IPs seeking remote control of our systems.

🛡️​​ Recommendations as a Cybersecurity Professional:
1) Containment
- Preserve evidence. (example: .pcap files)
- Isolate the infected host.
- Block communication with the malicious IPs.

2) Investigation
- Perform full antivirus/EDR scan.
- Check for persistence mechanisms.
- Review the browser history and cookies on the host .215

3) Credential Security
- Reset user credentials.

4) Network Security
- Monitor for similar traffic patterns.
- Implement detection rules.

5) Hardening
- Apply outbound filtering policies.
- Implementation of a Proxy with SSL Inspection
- Network Segmentation (VLANs and Subnets)
- DNS Sinkhole
- Employee training
- Allow only official websites and web tools used by the company.
