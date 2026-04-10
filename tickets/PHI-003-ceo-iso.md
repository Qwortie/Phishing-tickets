# SOC Ticket — Phishing Analysis

---

## Ticket Information

| Field | Detail |
|---|---|
| Ticket ID | PHI-003 |
| Date Opened | 26-04-07 |
| Analyst | Christopher Rice |
| Priority | Critical |
| Status | Closed |
| Classification | Confirmed Phishing |
| Source | User Report — CEO reported suspicious email to security team |

---

## 1. Reporter Information

| Field | Detail |
|---|---|
| Reported By | Evan Hutchinson (CEO) |
| Department | Executive |
| Reporting Method | Email report to security team |
| Did user click any links? | Unknown |
| Did user open any attachments? | Yes — opened ISO attachment |
| Did user enter credentials? | No — credentials harvested via endpoint compromise |

---

## 2. Email Details

| Field | Detail |
|---|---|
| Sender Display Name | Unknown — spoofed internal sender |
| Recipient | evan.hutchinson (CEO) |
| Date Sent | 2023-08-29 |
| DKIM | Fail |
| SPF | Fail |
| DMARC | Fail |

---

## 3. Email Content Analysis

**Social Engineering Tactic:**
- [x] Executive targeting — CEO specifically selected
- [x] Document appeared legitimate — CEO noted "nothing happened" after opening

**Indicators of Phishing:**
- [x] ISO container attachment — used to bypass Mark-of-the-Web protections
- [x] Failed authentication headers
- [x] Payload concealed inside ISO — DLL executed via trusted Windows binary

---

## 4. Attachment Analysis

| Field | Detail |
|---|---|
| File Type | ISO disk image |
| Contents | review.dat (malicious DLL) |
| Execution Method | rundll32.exe — DllRegisterServer |
| Sandbox Detonation | Malicious — establishes C2, achieves SYSTEM via UAC bypass |

**Payload Execution Chain:**
```
ISO mounted by user
    ↓
Stage 1 (PID 6392) — xcopy copies review.dat to %TEMP%
    xcopy /s /i /e /h D:\review.dat C:\Users\EVAN~1.HUT\AppData\Local\Temp\review.dat

Stage 1 executes DLL
    "C:\Windows\System32\rundll32.exe" D:\review.dat,DllRegisterServer

Scheduled task "Review" created for persistence

C2 connection established → 165[.]232[.]170[.]151:80

UAC bypass via fodhelper.exe → local admin

Mimikatz downloaded from GitHub → LSASS dump
    hxxps://github[.]com/gentilkiwi/mimikatz/releases/.../mimikatz_trunk.zip

Credential harvested: itadmin:F84769D250EB95EB2D7D8B4A1C5613F2

Lateral movement to WKSTN-1327 via WinRM

DCSync against domain controller
    Accounts dumped: administrator, backupda

Ransomware staged:
    hxxp://ff[.]sillytechninja[.]io/ransomboogey.exe
```

---

## 5. URL / Link Analysis

| URL (defanged) | Destination | Category |
|---|---|---|
| `hxxp://165[.]232[.]170[.]151:80` | C2 server | C2 communication |
| `hxxp://ff[.]sillytechninja[.]io/ransomboogey.exe` | Ransomware binary | Malware delivery |

---

## 6. Infrastructure IOCs

| Type | Value (defanged) | Notes |
|---|---|---|
| C2 IP | 165[.]232[.]170[.]151 | C2 server — port 80 |
| Malicious Domain | ff[.]sillytechninja[.]io | Ransomware hosting |
| File | review.dat | Malicious DLL payload inside ISO |
| File | C:\ProgramData\final.exe | Elevated C2 implant |
| File | ransomboogey.exe | Ransomware binary — staged, not executed |
| Scheduled Task | Review | Persistence mechanism |
| Compromised Account | itadmin | NTLM hash: F84769D250EB95EB2D7D8B4A1C5613F2 |
| Compromised Account | administrator | DCSync target |
| Compromised Account | backupda | DCSync target |

---

## 7. Affected User / Host Assessment

| Field | Detail |
|---|---|
| Hostname | CEO Workstation, WKSTN-1327, Domain Controller |
| User Accounts | evan.hutchinson, itadmin, allan.smith, administrator, backupda |
| Interaction Type | Opened ISO attachment — DLL executed |
| Endpoint Isolation Required? | Yes — all affected hosts |

**Endpoint Investigation:**

| Check | Finding |
|---|---|
| Suspicious processes running? | Yes — rundll32, fodhelper, Mimikatz, final.exe |
| Unusual network connections? | Yes — 165[.]232[.]170[.]151:80 |
| Files dropped on disk? | Yes — review.dat, final.exe, mimikatz, ransomboogey.exe |
| Persistence mechanisms found? | Yes — scheduled task "Review" |
| Credentials potentially exposed? | Yes — full domain credential dump via DCSync |

---

## 8. Verdict

- [x] **Confirmed Phishing**

**Justification:**
ISO attachment contained a malicious DLL (`review.dat`) executed via `rundll32.exe`, bypassing Mark-of-the-Web protections. The payload established C2, performed UAC bypass via `fodhelper.exe`, dumped domain credentials via Mimikatz and DCSync, moved laterally to WKSTN-1327, and staged ransomware. Classified as Confirmed Phishing — critical severity, full domain compromise achieved.

---

## 9. Actions Taken

| Time | Action | Result |
|---|---|---|
| T+00:05 | Quarantined email from all mailboxes | ✅ Complete |
| T+00:08 | Blocked 165[.]232[.]170[.]151 and ff[.]sillytechninja[.]io at firewall | ✅ Complete |
| T+00:10 | Isolated CEO workstation, WKSTN-1327, and DC from network | ✅ Complete |
| T+00:15 | Disabled compromised accounts — itadmin, administrator, backupda | ✅ Complete |
| T+00:18 | Initiated double krbtgt password reset | ✅ Complete |
| T+00:20 | Removed scheduled task "Review" on all affected hosts | ✅ Complete |
| T+00:22 | Escalated to IR team — domain compromise, ransomware staged | ✅ Complete |
| T+00:30 | Notified CEO of confirmed phishing and scope of compromise | ✅ Complete |

---

## 10. Escalation

| Field | Detail |
|---|---|
| Escalation Required? | Yes — Critical |
| Escalation Reason | Full domain compromise — DCSync performed, ransomware staged |
| Escalated To | IR Team / Senior Management |
| Linked IR Report | [IR-002 — Operation Boogeyman](https://github.com/Qwortie/soc-home-lab/blob/main/docs/incident-reports/IR-002-boogeyman.md) |

---

## 11. Analyst Notes

```
Third wave of the Boogeyman campaign — this time targeting the CEO directly.
ISO container used specifically to bypass Mark-of-the-Web (MoTW) — files extracted
from ISOs do not inherit the Zone.Identifier ADS that triggers SmartScreen.
This is an increasingly common evasion technique replacing macro-based delivery.
Attack chain reached full domain compromise including DCSync and ransomware staging.
Most critical incident in the Boogeyman campaign series.
Full attack chain documented in IR-003.
```

---

## 12. Resolution

| Field | Detail |
|---|---|
| Closed By | Christopher Rice |
| Final Classification | Confirmed Phishing |
| User Notified? | Yes — CEO briefed directly |
| Awareness Training Recommended? | Yes — ISO attachment awareness, executive targeting awareness |
