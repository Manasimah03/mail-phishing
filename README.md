# 🛡️ MailGuard

**Rule-based phishing detection engine that scores emails 0–100 and maps findings to MITRE ATT&CK.**

Built with Python + Flask · Jan 2026 – May 2026

---

## What it does

MailGuard takes an email (sender, subject, body), runs it through three detection layers, and returns a structured JSON verdict that a SOC analyst can triage quickly.

| Score | Verdict |
|-------|---------|
| 0–29 | ✅ Safe |
| 30–59 | ⚠️ Suspicious |
| 60–100 | 🚨 Critical Phishing Threat |

## Detection layers

| # | Layer | What it checks | Score |
|---|-------|----------------|-------|
| 1 | **Sender analysis** | Brand-impersonating sender (paypal, apple, bank, support) from a non-trusted domain | +45 |
| 2 | **Content analysis** | 7 urgency/fear trigger phrases (e.g. "verify your account", "act now") | +20 per phrase |
| 3 | **Link inspection** | URLs with credential-harvesting patterns (login, update, secure, hyphenated domains) | +40 |

Final score is capped at 100.

## MITRE ATT&CK mapping

| Technique | ID | Triggered by |
|-----------|----|--------------|
| Phishing: Spearphishing Link | T1566.002 | Spoofed sender |
| User Execution: Malicious Link | T1204.001 | Urgency language, malicious URL |

## Example output

```json
{
  "score": 100,
  "status": "Critical Phishing Threat",
  "threats": [
    "Suspicious Sender Domain (Spoofing Attempt): support@paypal-alerts.net",
    "Urgency/Fear framing detected: urgent, suspended, verify your account, act now",
    "Malicious URL Detected: http://paypal-login.net/verify"
  ],
  "mitre_mappings": [
    { "id": "T1566.002", "name": "Spearphishing Link", "desc": "..." },
    { "id": "T1204.001", "name": "User Execution: Malicious Link", "desc": "..." }
  ],
  "analyzed_components": {
    "urls_checked": 1,
    "nlp_severity": "High",
    "domains_verified": "Fail"
  }
}
```

## Quick start

```bash
git clone https://github.com/Manasimah03/<repo-name>.git
cd <repo-name>
pip install -r requirements.txt
python app.py
```

Then open `http://localhost:5000`.

## Project structure

```
├── app.py          # Flask app / routes
├── analyzer.py     # analyze_email() detection engine
├── templates/      # UI
└── requirements.txt
```

## Limitations & roadmap

Being upfront about what this is: a heuristic engine, not a production filter.

- [ ] Replace hardcoded `trusted.com` with a configurable allowlist
- [ ] Real SPF/DKIM/DMARC header validation (currently simulated by sender-domain rules)
- [ ] URL reputation via VirusTotal / PhishTank instead of string patterns
- [ ] Tighten URL rule (hyphens alone cause false positives)
- [ ] Deterministic scoring + unit tests with a labelled phishing/ham sample set
- [ ] Parse raw `.eml` files

## Author

**Manasi** · [GitHub](https://github.com/Manasimah03) · [LinkedIn](https://linkedin.com/in/manasi-mahesh-b9a217253)
