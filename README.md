# Cyber Security Challenge Greece 2026

Repository for documenting our team participation in **Cyber Security Challenge Greece 2026**, including solved challenge writeups, analysis notes, and reproducible technical findings.

## Event Summary

- Competition: **Cyber Security Challenge Greece 2026**
- Team score: **110**
- Solved challenges: **14**

This repository is organized to keep writeups concise, reproducible, and safe for public sharing. It focuses on methodology, technical reasoning, and challenge documentation without exposing personal or sensitive data.

## Team

- Konstantinos Panagiotaropoulos
- Ermis Efthimiou
- Alex Zagreba
- Giorgos Tsompanakis

## Repository Structure

```text
.
├── README.md
├── LICENSE
├── .gitignore
├── solutions/
│   ├── mobile/
│   ├── forensics/
│   ├── web/
│   ├── crypto/
│   ├── osint/
│   ├── reversing/
│   └── misc/
├── notes/
└── assets/
```

## Challenge Categories

- [Mobile](solutions/mobile/README.md)
- [Forensics](solutions/forensics/README.md)
- [Web](solutions/web/README.md)
- [Crypto](solutions/crypto/README.md)
- [OSINT](solutions/osint/README.md)
- [Reversing](solutions/reversing/README.md)
- [Misc](solutions/misc/README.md)

## Solved Challenges Overview

| Challenge | Category | Status | Points | Solved At | Documentation |
| --- | --- | --- | ---: | --- | --- |
| What is the path of the original implant | Forensics | Solved | 20 | March 13th, 7:10:05 PM | [solution](solutions/forensics/original-implant-path/README.md) |
| Is it called login or log in | Mobile | Solved | 30 | March 13th, 6:10:10 PM | [solution](solutions/mobile/is-it-called-login-or-log-in/README.md) |
| Message Received | Mobile | Solved | 20 | March 13th, 3:13:19 PM | [solution](solutions/mobile/message-received/README.md) |
| What is the MITRE ATT&CK ID of the technique | Forensics | Solved | 10 | March 13th, 2:59:37 PM | [pending](notes/pending-documentation.md#what-is-the-mitre-attck-id-of-the-technique) |
| Find the flag inside the email | Forensics | Solved | 10 | March 13th, 2:49:57 PM | [pending](notes/pending-documentation.md#find-the-flag-inside-the-email) |
| What is the email used by the adversaries | Forensics | Solved | 10 | March 13th, 2:47:31 PM | [pending](notes/pending-documentation.md#what-is-the-email-used-by-the-adversaries) |
| What is the email address of the victim | Forensics | Solved | 10 | March 13th, 2:46:37 PM | [pending](notes/pending-documentation.md#what-is-the-email-address-of-the-victim) |
| Mag1k | Mobile | Investigated, unresolved answer wording | 100 | - | [solution](solutions/mobile/mag1k/README.md) |

## Methodology

Our workflow favors fast triage first, then deeper verification only where needed:

1. Identify the artifact type and likely attack surface.
2. Preserve relevant evidence and avoid destructive actions.
3. Prefer static analysis before runtime interaction.
4. Use runtime validation only to confirm hypotheses or recover challenge-specific outputs.
5. Document commands, findings, and conclusions as the work progresses.
6. Separate proven results from unverified hypotheses.

## Tools and Techniques

Common tools and techniques used across this repo include:

- `adb` and emulator-based Android testing
- static APK triage (`strings`, unpacking, manifest/component review)
- JADX / decompilation-assisted reverse engineering
- lightweight Python and Ruby helpers for decoding and decryption
- hash recovery and credential analysis
- targeted broadcast / intent testing
- evidence-first markdown writeups

## Notes

- Shared tooling, helper projects, and archived drafts live under [notes/](notes/README.md).
- Safe screenshots or diagrams can be stored under [assets/](assets/README.md) when needed.
- Some solved challenges are still pending clean public documentation and will be filled in later.

## Ethical Use

This repository is published for educational and competition writeup purposes only. It documents challenge-solving techniques in a controlled CTF context and is not intended to support unauthorized access or misuse against real systems.

## Closing

The goal of this repository is not just to record outcomes, but to preserve the reasoning and workflow that produced them. As additional challenge writeups are finalized, they can be added cleanly within the existing category structure.
