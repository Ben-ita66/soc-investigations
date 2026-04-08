# Phishing Email Analysis — Unusual Login Activity

**Type:** Incident Response Practice Task  
**Analyst:** Nwabueze Benita  
**Date:** 8th April 2026  
**Verdict:** Phishing Confirmed

---

## Scenario

A suspicious email was submitted for analysis. The email claimed to be from the **Microsoft Security Team**, alerting the recipient of unusual login activity and requesting immediate account verification via an embedded link.

| Field | Details |
|-------|---------|
| Subject | Unusual Login Activity Detected |
| Sender | info@libreriacies.es |
| Claimed Identity | Microsoft Security Team |
| Recipient | sonyundefinedralph@gmail.com |
| Embedded URL | `https://tinyurl[.]com/ypu5kfts` |
| Verdict | **PHISHING — Confirmed** |

---

## 1. Indicators of Compromise (IOCs)

| IOC Type | Value | Verdict |
|----------|-------|---------|
| Sender Email | info@libreriacies.es | Suspicious — unrelated to Microsoft |
| Sender Domain | libreriacies.es | Misconfigured SPF, no DMARC |
| Recipient Email | sonyundefinedralph@gmail.com | No known breaches |
| Embedded URL | https://tinyurl[.]com/ypu5kfts | Terminated for ToS violation |
| IP Address | 217.18.161.43 | Clean on VirusTotal |
| Hostname | mail.libreriacies.es | Associated with sender domain |
| ASN | AS42220 — Trevenque Sistemas De Informacion S.L. | Spain-based hosting provider |

---

## 2. Email Address Investigation

### Sender — info@libreriacies.es
The email claims to be from the Microsoft Security Team. Legitimate security emails from Microsoft arrive from the domain `@accountprotection.microsoft.com`. The domain `libreriacies.es` has no affiliation with Microsoft Corporation — this mismatch is a primary indicator of impersonation.

### Recipient — sonyundefinedralph@gmail.com
- Investigated using **HaveIBeenPwned**
- Result: No data breaches found
- The recipient appears to be a genuine user targeted by this phishing campaign

---

## 3. Sender Domain Analysis

**Tool used:** MXToolbox (mxtoolbox.com) — MX Lookup + SPF Lookup

| Check | Result | Implication |
|-------|--------|-------------|
| DNS Record | Found | Domain is active and registered |
| DMARC Record | Not Found | No email authentication policy — spoofing is possible |
| SPF Record | Found but Misconfigured | Contains characters after `-all` terminator. References unrelated domain `serlogal.arnoia.com` |
| VirusTotal | 0 Detections | No known malicious history — not conclusive on its own |

**SPF Record:**
```
v=spf1 +a +mx -all +a:serlogal.arnoia.com
```

**Issues identified:**
- The `-all` directive should terminate the record. Any entries after it are ignored by mail servers
- `+a:serlogal.arnoia.com` appears after `-all` — invalid and suspicious
- References an unrelated third-party domain
- Inconsistent with a legitimate, professionally managed domain

---

## 4. Embedded URL Analysis

**Tool used:** CheckShortURL (checkshorturl.com)  
**URL analyzed:** `https[:]//tinyurl[.]com/ypu5kfts`

| Check | Finding |
|-------|---------|
| HTTP Response Code | 301 — Moved Permanently (active redirect detected) |
| TinyURL Status | URL Terminated |
| Reason for Termination | Violation of TinyURL Terms of Use — flagged as abusive |
| Redirect Technique | Short URL used to conceal true malicious destination |

Using a URL shortener to hide a redirect destination is a well-documented phishing technique. TinyURL independently identified and terminated this link for abuse, confirming malicious intent.

---

## 5. Social Engineering Techniques Identified

| Technique | Evidence in Email |
|-----------|------------------|
| Urgency / Pressure | *"If you do not verify your account within 24 hours, access to your mailbox may be restricted"* |
| Authority Impersonation | Claims to be Microsoft Security Team and IT Support Desk to establish false credibility |

---

## 6. Conclusion

**VERDICT: PHISHING INCIDENT CONFIRMED**

| # | Evidence |
|---|----------|
| 1 | Sender domain `libreriacies.es` has no affiliation with Microsoft |
| 2 | No DMARC record — sender authentication cannot be verified |
| 3 | SPF record misconfigured with entries after `-all` and references unrelated domain |
| 4 | Embedded TinyURL returned 301 redirect and was terminated by TinyURL for Terms of Use violation |
| 5 | Email employs urgency and authority impersonation — hallmark social engineering techniques |
| 6 | Legitimate Microsoft security emails originate from `@accountprotection.microsoft.com` |

---

## 7. Tools Used

| Tool | URL | Purpose |
|------|-----|---------|
| MXToolbox | mxtoolbox.com | MX Lookup, SPF and DMARC record analysis |
| VirusTotal | virustotal.com | Domain and IP reputation check |
| HaveIBeenPwned | haveibeenpwned.com | Recipient email breach check |
| CheckShortURL | checkshorturl.com | Safe URL expansion and redirect analysis |

---

*Prepared by Nwabueze Benita | TS Academy SOC Practice Task*
