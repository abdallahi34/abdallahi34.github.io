---
layout: page
title: Zero-Knowledge Proof Authentication
permalink: /projects/zkp-auth/
---

![Zero-Knowledge Proof Authentication diagram](/images/projects/zkp-auth.png)

## Securing a smart IoT lock

- Audited a BLE smart lock: reproduced a relay attack (relaying the auth signal remotely) and an ARP spoofing attack to intercept and replay authentication exchanges.
- Designed a reinforced authentication protocol using Zero-Knowledge Proofs (Schnorr protocol), so the badge/smartphone can prove it holds the secret without ever transmitting it.
- Built a Python prototype of the ZKP exchange and validated it against the reproduced attack scenarios.

**Stack:** Bettercap, Scapy, ZKP protocols

**Team project — ENSIAS IoT Security module**

[&larr; Back to Projects](/projects/)
