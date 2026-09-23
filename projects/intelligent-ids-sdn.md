---
layout: page
title: Intelligent IDS for SDN Networks
permalink: /projects/intelligent-ids-sdn/
---

![Intelligent IDS for SDN Networks architecture](/images/projects/ids-sdn.png)

## Real-time intrusion detection via Machine Learning

- Integrated with a Ryu controller (OpenFlow v1.3): packet capture via Packet-In, then aggregation of bidirectional flow statistics.
- ML inference every 5 seconds using models trained on the InSDN dataset (binary and multi-class classification).
- Compared Random Forest and Extra Trees on precision, recall, and F1-score to pick the deployed classifier.
- Inference wired directly into the SDN control loop for near real-time detection and response.

**Stack:** Python, Scapy, Mininet, Ryu, Snort, scikit-learn

**Repo:** [github.com/abdallahi34/intelligent-ids-sdn](https://github.com/abdallahi34/intelligent-ids-sdn)

[&larr; Back to Projects](/projects/)
