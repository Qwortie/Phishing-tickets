# SOC Ticket — Phishing Analysis

---

## Ticket Information

| Field | Detail |
|---|---|
| Ticket ID | PHI-002 |
| Date Opened | 2026-04-07 |
| Analyst | Christopher Rice |
| Priority | High |
| Status | Closed |
| Classification | Confirmed Phishing |
| Source | SOC Alert — Suspicious command execution flagged on workstation |

---

## 1. Reporter Information

| Field | Detail |
|---|---|
| Reported By | Maxine Beck |
| Department | Human Resources |
| Reporting Method | SOC alert triggered — analyst contacted user |
| Did user click any links? | Unknown |
| Did user open any attachments? | Yes |
| Did user enter credentials? | No |

---

## 2. Email Details

| Field | Detail |
|---|---|
| Sender Display Name | Wesley Taylor |
| Sender Email Address | westaylor23[@]outlook[.]com |
| Recipient | maxine.beck[@]quicklogisticsorg.onmicrosoft.com |
| Subject Line | Job Application |
| DKIM | Not verified |
| SPF | Not verified |
| DMARC | Not verified |

---

## 3. Email Content Analysis

**Social Engineering Tactic:**
- [x] Lure relevant to recipient's role (job application targeting HR Specialist)
- [x] Impersonation of legitimate job applicant

**Indicators of Phishing:**
- [x] Macro-enabled Word document as attachment
- [x] Free email provider (outlook.com) used for business correspondence
- [x] Document contained VBA macro fetching remote payload

---

## 4. Attachment Analysis

| Field | Detail |
|---|---|
| Filename | Resume_WesleyTaylor.doc |
| File Type | Microsoft Word Document (.doc) — macro-enabled |
| MD5 Hash | 52c4384a0b9e248b95804352ebec6c5b |
| Full Path on Disk | C:\Users\maxine.beck\AppData\Local\Microsoft\Windows\INetCache\Content.Outlook\WQHGZCFI\Resume_WesleyTaylor (002).doc |
| Macro Analysis Tool | Olevba |
| Sandbox Detonation | Malicious — macro executes multi-stage download chain |

**Macro Behaviour:**
```
Stage 1: VBA macro fetches update.png (disguised stage 2) from
         hxxps://files[.]boogeymanisback[.]lol/aa2a9c53cbb80416d3b47d85538d9971/update.png

Stage 2: update.png saved as C:\ProgramData\update.js
         Executed by wscript.exe (PID 4260, parent PID 1124)

Stage 3: update.js downloads update.exe from
         hxxps://files[.]boogeymanisback[.]lol/aa2a9c53cbb80416d3b47d85538d9971/update.exe
         Renamed to updater.exe — moved to C:\Windows\Tasks\
         Executed as PID 6216 — establishes C2 connection
```

---

## 5. URL / Link Analysis

| URL (defanged) | Destination | Category |
|---|---|---|
| `hxxps://files[.]boogeymanisback[.]lol/.../update.png` | Stage 2 payload disguised as image | Malware delivery |
| `hxxps://files[.]boogeymanisback[.]lol/.../update.exe` | Stage 3 C2 binary | Malware delivery |

---

## 6. Infrastructure IOCs

| Type | Value (defanged) | Notes |
|---|---|---|
| Malicious Domain | files[.]boogeymanisback[.]lol | Payload hosting server |
| C2 IP | 128[.]199[.]95[.]189 | C2 server |
| C2 Port | 8080 | C2 communication port |
| File | Resume_WesleyTaylor.doc | Initial malicious attachment |
| File | C:\ProgramData\update.js | Stage 2 JavaScript dropper |
| File | C:\Windows\Tasks\updater.exe | Stage 3 C2 implant |
| Registry Key | HKCU:\Software\Microsoft\Windows\CurrentVersion\debug | Fileless payload storage |
| Scheduled Task | Updater | Persistence mechanism |

---

## 7. Affected User / Host Assessment

| Field | Detail |
|---|---|
| User Account | maxine.beck |
| Interaction Type | Opened attachment — macro executed full payload chain |
| Endpoint Isolation Required? | Yes |

**Endpoint Investigation:**

| Check | Finding |
|---|---|
| Suspicious processes running? | Yes — wscript.exe, updater.exe (PID 6216) |
| Unusual network connections? | Yes — 128[.]199[.]95[.]189:8080 |
| Files dropped on disk? | Yes — update.js, updater.exe |
| Persistence mechanisms found? | Yes — scheduled task + registry-stored fileless payload |
| Credentials potentially exposed? | Unknown — investigation ongoing |

---

## 8. Verdict

- [x] **Confirmed Phishing**

**Justification:**
Attachment `Resume_WesleyTaylor.doc` contained a VBA macro confirmed malicious via Olevba analysis and sandbox detonation. The macro initiated a three-stage download chain resulting in a C2 implant (`updater.exe`) connecting to `128[.]199[.]95[.]189:8080`. A fileless persistence mechanism was implanted via registry and scheduled task. Confirmed spear-phishing targeting the HR department — consistent with Boogeyman threat group TTPs.

---

## 9. Actions Taken

| Time | Action | Result |
|---|---|---|
| T+00:05 | Quarantined email from all mailboxes | ✅ Complete |
| T+00:08 | Blocked boogeymanisback[.]lol at DNS | ✅ Complete |
| T+00:10 | Blocked 128[.]199[.]95[.]189 at firewall | ✅ Complete |
| T+00:12 | Isolated Maxine's workstation | ✅ Complete |
| T+00:15 | Removed scheduled task and registry persistence key | ✅ Complete |
| T+00:18 | Escalated to IR team — C2 established, persistent access confirmed | ✅ Complete |
| T+00:25 | Notified reporter of confirmed phishing verdict | ✅ Complete |

---

## 10. Escalation

| Field | Detail |
|---|---|
| Escalation Required? | Yes |
| Escalation Reason | C2 implant active — fileless persistence established |
| Escalated To | IR Team |
| Linked IR Report | [IR-002 — Operation Boogeyman](https://github.com/Qwortie/soc-home-lab/blob/main/docs/incident-reports/IR-002-boogeyman.md) |

---

## 11. Analyst Notes

```
Second targeted attack against Quick Logistics LLC — HR department this time.
Payload disguised as .png to evade file-type filtering on web proxies.
Fileless persistence via registry key is particularly evasive — no binary on disk
for the scheduled task to execute directly.
Memory forensics (Volatility) was required to recover full file paths and PIDs.
Part of the broader Boogeyman campaign documented in IR-003.
```

---

## 12. Resolution

| Field | Detail |
|---|---|
| Closed By | Christopher Rice |
| Final Classification | Confirmed Phishing |
| User Notified? | Yes |
| Awareness Training Recommended? | Yes — macro-enabled documents from unknown senders |
