---
layout: page
title: CloudGuard
permalink: /projects/cloudguard/
---

![CloudGuard architecture](/images/projects/cloudguard.png)
*Pipeline architecture , generated from the actual scan/detection code, not a mockup.*

## Cloud Security Detection Pipeline

A four-stage pipeline covering prevention, posture, detection, and response across the AWS control plane.

- Checkov scans Terraform/OpenTofu before deploy, wired into a CI gate that blocks merges on high-severity misconfigurations.
- A posture scanner checks 12 controls across S3, IAM, security groups, and CloudTrail, with every finding mapped to a MITRE ATT&CK technique.
- 8 Sigma detection rules watch CloudTrail-style activity, including 2 time-window/threshold rules for burst behavior (e.g. mass object reads).
- A remediation layer always dry-runs first and logs every applied change to an audit trail.
- Fully reproducible against a mocked AWS environment (Moto/boto3) , validated against a 189-event dataset (precision 1.0, recall 1.0), no live account needed.

**Stack:** Terraform, OpenTofu, Checkov, Python, boto3, Moto, pySigma, GitHub Actions

**Repo:** [github.com/abdallahi34/cloudguard](https://github.com/abdallahi34/cloudguard)

[&larr; Back to Projects](/projects/)
