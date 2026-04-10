# SOC Ticket — Phishing Analysis

---

## Ticket Information

| Field | Detail |
|---|---|
| Ticket ID | PHI-<!-- number --> |
| Date Opened | <!-- YYYY-MM-DD HH:MM --> |
| Analyst | Christopher Rice |
| Priority | <!-- Critical / High / Medium / Low --> |
| Status | <!-- Open / In Progress / Escalated / Closed --> |
| Classification | <!-- Confirmed Phishing / Suspected Phishing / False Positive --> |
| Source | <!-- User Report / Email Gateway Alert / SIEM Alert --> |

---

## 1. Reporter Information

| Field | Detail |
|---|---|
| Reported By | <!-- Name of employee who reported --> |
| Department | <!-- Finance / HR / IT / etc. --> |
| Date Reported | <!-- YYYY-MM-DD HH:MM --> |
| Reporting Method | <!-- Email to SOC / Phishing button / Phone / Help desk ticket --> |
| Did user click any links? | <!-- Yes / No / Unknown --> |
| Did user open any attachments? | <!-- Yes / No / Unknown --> |
| Did user enter credentials? | <!-- Yes / No / Unknown --> |

---

## 2. Email Details

| Field | Detail |
|---|---|
| Sender Display Name | <!-- e.g. "IT Support" --> |
| Sender Email Address | <!-- e.g. support@evil[.]com --> |
| Reply-To Address (if different) | <!-- e.g. harvester@evil[.]com --> |
| Recipient(s) | <!-- Who received the email --> |
| Subject Line | <!-- Email subject --> |
| Date/Time Sent | <!-- YYYY-MM-DD HH:MM --> |
| Mail Relay / Sending Server | <!-- IP or hostname from email headers --> |
| DKIM | <!-- Pass / Fail / None --> |
| SPF | <!-- Pass / Fail / None --> |
| DMARC | <!-- Pass / Fail / None --> |

---

## 3. Email Content Analysis

**Social Engineering Tactic:**
<!-- Check all that apply -->
- [ ] Urgency / Threats (account suspended, invoice overdue, etc.)
- [ ] Impersonation of internal staff or IT
- [ ] Impersonation of trusted third party (bank, vendor, Microsoft, etc.)
- [ ] Lure relevant to recipient's role (invoice for Finance, resume for HR, etc.)
- [ ] Reward / Prize notification
- [ ] COVID / Current events lure
- [ ] Other: <!-- describe -->

**Indicators of Phishing:**
- [ ] Sender domain does not match display name
- [ ] Misspelled domain (typosquatting)
- [ ] Failed DKIM / SPF / DMARC
- [ ] Suspicious or mismatched hyperlinks
- [ ] Attachment with macro-enabled / executable / archive extension
- [ ] Unusual urgency or pressure language
- [ ] Generic greeting (Dear Customer, Dear User)
- [ ] Poor grammar or formatting inconsistent with impersonated brand

---

## 4. Attachment Analysis

*Complete this section if the email contained an attachment.*

| Field | Detail |
|---|---|
| Filename | <!-- e.g. Invoice_2024.zip --> |
| File Type | <!-- .doc / .lnk / .zip / .iso / .pdf / etc. --> |
| MD5 Hash | <!-- hash --> |
| SHA256 Hash | <!-- hash --> |
| VirusTotal Result | <!-- X/72 detections — link --> |
| Password Protected? | <!-- Yes (password: xxxxxx) / No --> |
| Macro Enabled? | <!-- Yes / No / N/A --> |
| Sandbox Detonation | <!-- Clean / Malicious / Suspicious / Not performed --> |
| Sandbox Platform | <!-- Any.run / Joe Sandbox / Hybrid Analysis / etc. --> |

**Payload / Macro Behaviour (if detonated):**
```
<!-- Describe observed behaviour:
     - Processes spawned
     - Network connections made
     - Files dropped
     - Registry modifications -->
```

---

## 5. URL / Link Analysis

*Complete this section if the email contained links.*

| URL (defanged) | Destination | VirusTotal | Category |
|---|---|---|---|
| <!-- hxxps://evil[.]com/login --> | <!-- Credential harvester / Payload download / Redirect --> | <!-- X/90 --> | <!-- Phishing / Malware / Clean --> |

**URL Analysis Notes:**
```
<!-- Was the URL live at time of analysis?
     Did it redirect? To where?
     Was it behind a URL shortener?
     Was a login page presented — which brand was impersonated? -->
```

---

## 6. Infrastructure IOCs

| Type | Value (defanged) | Notes |
|---|---|---|
| Sender IP | <!-- 192[.]168[.]1[.]1 --> | <!-- Geolocation, ASN, reputation --> |
| Malicious Domain | <!-- evil[.]com --> | <!-- Registration date, registrar, hosting --> |
| Malicious URL | <!-- hxxps://evil[.]com/payload --> | <!-- Purpose --> |
| C2 IP (if applicable) | <!-- --> | <!-- --> |
| File Hash (MD5) | <!-- --> | <!-- --> |
| File Hash (SHA256) | <!-- --> | <!-- --> |

---

## 7. Affected User / Host Assessment

*Complete this section if the user interacted with the email.*

| Field | Detail |
|---|---|
| Hostname | <!-- Workstation name --> |
| IP Address | <!-- Internal IP --> |
| OS | <!-- Windows 10 / 11 etc. --> |
| User Account | <!-- domain\username --> |
| Interaction Type | <!-- Opened attachment / Clicked link / Entered credentials / None --> |
| Time of Interaction | <!-- YYYY-MM-DD HH:MM --> |
| Endpoint Isolation Required? | <!-- Yes / No / Already isolated --> |

**Endpoint Investigation (if interaction confirmed):**

| Check | Finding |
|---|---|
| Suspicious processes running? | <!-- Yes / No — detail if yes --> |
| Unusual network connections? | <!-- Yes / No — detail if yes --> |
| Files dropped on disk? | <!-- Yes / No — paths if yes --> |
| Persistence mechanisms found? | <!-- Yes / No — detail if yes --> |
| Credentials potentially exposed? | <!-- Yes / No / Unknown --> |

---

## 8. Verdict

<!-- Select one -->
- [ ] **Confirmed Phishing** — Malicious intent confirmed, IOCs identified
- [ ] **Suspected Phishing** — Strong indicators but not fully confirmed
- [ ] **Spam** — Unsolicited but no malicious payload or credential harvesting intent
- [ ] **False Positive** — Legitimate email, no threat identified

**Verdict Justification:**
```
<!-- 2-3 sentences explaining the basis for your verdict.
     Example: "Sender domain bpakcaging[.]xyz is a typosquat of bpackaging.com.
     The attachment contained an LNK file with an encoded PowerShell payload
     confirmed malicious by sandbox detonation (22/72 VirusTotal detections).
     Classified as Confirmed Phishing." -->
```

---

## 9. Actions Taken

| Time | Action | Result |
|---|---|---|
| <!-- HH:MM --> | <!-- e.g. Quarantined email from all mailboxes --> | <!-- Complete / Pending --> |
| <!-- HH:MM --> | <!-- e.g. Blocked sender domain at email gateway --> | <!-- Complete / Pending --> |
| <!-- HH:MM --> | <!-- e.g. Blocked malicious URL at proxy/firewall --> | <!-- Complete / Pending --> |
| <!-- HH:MM --> | <!-- e.g. Submitted IOCs to threat intel platform --> | <!-- Complete / Pending --> |
| <!-- HH:MM --> | <!-- e.g. Notified reporter of outcome --> | <!-- Complete / Pending --> |
| <!-- HH:MM --> | <!-- e.g. Isolated endpoint for further investigation --> | <!-- Complete / Pending --> |
| <!-- HH:MM --> | <!-- e.g. Escalated to IR team — user clicked link --> | <!-- Complete / Pending --> |
| <!-- HH:MM --> | <!-- e.g. Forced password reset for exposed account --> | <!-- Complete / Pending --> |

---

## 10. Escalation

| Field | Detail |
|---|---|
| Escalation Required? | <!-- Yes / No --> |
| Escalation Reason | <!-- User clicked link / Credentials entered / Malware executed / etc. --> |
| Escalated To | <!-- IR Team / Tier 2 / Management / Legal / HR --> |
| Escalation Time | <!-- YYYY-MM-DD HH:MM --> |
| Linked IR Report | <!-- IR-XXX (if full incident response initiated) --> |

---

## 11. Analyst Notes

```
<!-- Free-text field for anything not captured above.
     Investigation steps taken, ambiguous findings,
     context from the reporter, follow-up items, etc. -->
```

---

## 12. Resolution

| Field | Detail |
|---|---|
| Closed By | Christopher Rice |
| Date Closed | <!-- YYYY-MM-DD HH:MM --> |
| Time to Close | <!-- e.g. 45 minutes --> |
| Final Classification | <!-- Confirmed Phishing / Suspected Phishing / Spam / False Positive --> |
| User Notified? | <!-- Yes / No --> |
| Awareness Training Recommended? | <!-- Yes / No --> |

---

## References

- [VirusTotal](https://www.virustotal.com)
- [MXToolbox Header Analyzer](https://mxtoolbox.com/EmailHeaders.aspx)
- [Any.run Sandbox](https://any.run)
- [URLScan.io](https://urlscan.io)
- [MITRE ATT&CK — Phishing T1566](https://attack.mitre.org/techniques/T1566/)
