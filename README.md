# 🎫 SOC Phishing Ticket Log

![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=flat-square)
![Tickets](https://img.shields.io/badge/Tickets-4-blue?style=flat-square)
![Framework](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-B22222?style=flat-square)
![Technique](https://img.shields.io/badge/T1566-Phishing-orange?style=flat-square)

A structured log of phishing triage tickets produced from SOC investigations and simulations. Each ticket documents a reported phishing email from initial triage through verdict, containment, and escalation decision — following real-world SOC analyst workflow.

Tickets are sourced from TryHackMe SOC investigations and home lab simulations. All IOCs are defanged per standard threat intelligence practice.

---

## 🔁 Triage Workflow

```
Email Reported / Alert Triggered
            ↓
    Header Analysis
  (DKIM / SPF / DMARC)
            ↓
  Attachment + URL Analysis
 (VirusTotal / sandbox / olevba)
            ↓
         Verdict
            ↓
    ┌───────┴────────┐
    │                │
False Positive    Phishing Confirmed
    │                │
 Close ticket    Quarantine + Block IOCs
                     │
             User interaction?
                     │
            ┌────────┴────────┐
            │                 │
           No               Yes
            │                 │
      Close ticket      Isolate endpoint
                        Escalate to IR
                        Open IR-XXX report
```

---

## 📋 Ticket Index

| Ticket ID | Subject / Lure | Payload Type | Verdict | User Interaction | Escalated | Linked IR |
|---|---|---|---|---|---|---|
| [PHI-001](./tickets/PHI-001-invoice-lnk.md) | Unpaid Invoice — B Packaging Inc | LNK in encrypted ZIP | ✅ Confirmed Phishing | Yes — attachment opened | Yes | [IR-003](https://github.com/Qwortie/soc-home-lab/blob/main/docs/incident-reports/IR-003-boogeyman-campaign.md) |
| [PHI-002](./tickets/PHI-002-resume-macro.md) | Job Application — Wesley Taylor | VBA Macro .doc | ✅ Confirmed Phishing | Yes — attachment opened | Yes | [IR-003](https://github.com/Qwortie/soc-home-lab/blob/main/docs/incident-reports/IR-003-boogeyman-campaign.md) |
| [PHI-003](./tickets/PHI-003-ceo-iso.md) | Executive Targeting — ISO Payload | ISO container / rundll32 | ✅ Confirmed Phishing | Yes — attachment opened | Yes | [IR-003](https://github.com/Qwortie/soc-home-lab/blob/main/docs/incident-reports/IR-003-boogeyman-campaign.md) |
| [PHI-004](./tickets/PHI-004-follina-invoice.md) | Malicious Invoice — CVE-2022-30190 | .doc / MSDT Follina | ✅ Confirmed Phishing | Yes — document opened | Yes | [IR-002](https://github.com/Qwortie/soc-home-lab/blob/main/docs/incident-reports/IR-002-tempest-full-chain.md) |

---

## 📊 Ticket Statistics

| Metric | Value |
|---|---|
| Total Tickets | 4 |
| Confirmed Phishing | 4 |
| False Positives | 0 |
| Escalated to IR | 4 |
| User Interaction Confirmed | 4 |
| Unique Payload Types | LNK, VBA Macro, ISO/DLL, MSDT Exploit |
| Unique MITRE Techniques | T1566.001, T1059.001, T1059.005, T1218.011, T1203 |

---

## 📁 Repository Structure

```
phishing-tickets/
├── README.md                        # This file — ticket index and workflow
├── templates/
│   └── SOC-TICKET-template.md       # Blank reusable ticket template
└── tickets/
    ├── PHI-001-invoice-lnk.md
    ├── PHI-002-resume-macro.md
    ├── PHI-003-ceo-iso.md
    └── PHI-004-follina-invoice.md
```

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| Thunderbird | Email client for `.eml` header and attachment inspection |
| MXToolbox | Email header analysis — DKIM / SPF / DMARC |
| VirusTotal | File hash and URL reputation |
| Any.run | Dynamic sandbox detonation |
| olevba (Oletools) | VBA macro extraction and analysis |
| LNKParse3 | LNK file forensics — command line argument extraction |
| Wireshark / Tshark | Packet capture analysis — C2 traffic |
| URLScan.io | URL detonation and screenshot |

---

## 🔗 Related Repositories

- [soc-home-lab](https://github.com/Qwortie/soc-home-lab) — Lab environment
- [incident-response-reports](https://github.com/Qwortie/SOC-Home-Lab/blob/main/docs/incident-reports/README.md) - Full IR reports
- [splunk-detection-rules](https://github.com/Qwortie/splunk-detection-rules) — SPL rules used to detect phishing payloads
