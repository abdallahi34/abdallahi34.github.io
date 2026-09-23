---
title: "{MACHINE NAME} — Writeup"
author: "abdallahi"
date: 2026-01-01 12:00:00 +0000
machine: "{MACHINE NAME}"
platform: "HackTheBox" # or "TryHackMe"
machine_url: "https://app.hackthebox.com/machines/{slug}" # or the TryHackMe room URL
difficulty: "Easy" # Easy | Medium | Hard | Insane
skills: [recon, web]
time_spent: "<1h"
categories: [writeup, ctf] # keep "writeup" so this appears on the /writeups/ page
tags: [hackthebox, writeup, ctf]
layout: post
render_with_liquid: false
---

> This machine is retired — check the platform before publishing if you're duplicating this template for a new post.
> {: .prompt-info }

<div class="no-print text-end mb-3">
  <button onclick="window.print()" class="btn btn-outline-secondary btn-sm">
    <i class="fas fa-file-pdf"></i> Download as PDF
  </button>
</div>

## TL;DR
One or two sentences: what was exploited, and the overall approach.

## Environment
- Attacking box: (e.g., Kali Linux)
- Tools: nmap, gobuster, curl, python3, etc.

## Enumeration
1. `nmap -sC -sV -p- <ip>`
2. Found service X on port Y — notes on what stood out.

## Exploitation
Step-by-step walkthrough with commands and short explanations of *why* each step is taken, not just what was typed.

## Privilege Escalation
If applicable: what was found, why it worked, and — importantly — why easier/more obvious approaches did *not* work first. That contrast is usually the most useful part of a write-up.

## Root Cause
Explain the underlying vulnerability class and why the fix (or lack of one) matters.

## Flags
<details><summary>User flag</summary>

`HTB{user_flag_here}`

</details>

<details><summary>Root flag</summary>

`HTB{root_flag_here}`

</details>

## Lessons & Detection
- What would catch this in a monitored environment (log source, detection rule, control)?
- Links to references, CVEs, and tools used.
