---
layout: page
title: SIEM/XDR Lab with Wazuh
permalink: /projects/wazuh-siem-lab/
---

![SIEM/XDR Lab with Wazuh architecture](/images/projects/wazuh-siem-lab.png)

## Full deployment and custom detection rules

- Wazuh Manager with Linux and Windows 10 agents, real-time File Integrity Monitoring with who-data, and centralized event correlation.
- 9 custom detection rules (XML, mapped to MITRE ATT&CK) covering SSH brute-force, privilege escalation, reverse shells, and Windows registry persistence.
- Each rule validated against simulated attacks (Hydra, Nmap), with an automated active response (firewall-drop) and a full post-simulation incident report.

**Stack:** Wazuh, ELK Stack, VirtualBox, Ubuntu Server, Windows 10, Hydra, Nmap

**Repo:** [github.com/abdallahi34/wazuh-siem-lab](https://github.com/abdallahi34/wazuh-siem-lab)

[&larr; Back to Projects](/projects/)
