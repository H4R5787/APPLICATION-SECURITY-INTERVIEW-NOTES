# DOCUMENT 10: LAST-MINUTE INTERVIEW REVISION CHEAT SHEET

**Time to read:** 10 minutes  
**Purpose:** Rapid concept refresh immediately before an interview.

---

## 1. CORE DEFINITIONS IN ONE SENTENCE

* **SAST:** Analyzes source code or binaries without executing the application (inside-out, white-box).
* **DAST:** Tests running applications by sending simulated attack payloads over HTTP/S (outside-in, black-box/gray-box).
* **SCA:** Analyzes open-source third-party dependencies for known CVEs.
* **IDOR / BOLA:** User accesses another user's private data by manipulating an object reference parameter.
* **SQL Injection:** Untrusted input alters SQL query logic; prevented using parameterized queries (prepared statements).
* **Cross-Site Scripting (XSS):** Malicious script executes in victim browser; prevented via context-aware output encoding and CSP.
* **CSRF:** Forces an authenticated victim browser to submit state-changing actions; prevented via anti-CSRF tokens and `SameSite` cookies.
* **SSRF:** Server coerced into making unauthorized network requests to internal resources (cloud metadata, internal APIs).
* **HSTS:** Tells browsers to enforce HTTPS connections exclusively for a designated duration (`includeSubDomains; preload`).
* **CSP:** Browser policy restricting domains from which scripts, styles, and frames can load.

---

## 2. VERACODE DAST INSTANT RECALL

* **Internal Scan Engine (ISE):** On-prem virtual machine/container that reaches internal enterprise URLs by pulling scan jobs outbound over port 443.
* **Authenticated Scan Blocker Checklist:**
  1. Expired credentials or locked test account.
  2. Missing element selector in Selenium login macro.
  3. Dynamic anti-CSRF token failed to generate.
  4. Crawler visited `/logout` and terminated session.
  5. Session cookie missing `SameSite` or dropped over HTTP redirect.
* **False Positive Validation Flow:**
  - Copy Request/Response into Burp Suite Repeater $\rightarrow$ Send payload manually $\rightarrow$ Check browser rendering/time delay $\rightarrow$ Document proof in mitigation workflow.

---

## 3. KEY PROTOCOLS & SECURITY HEADERS

| Header / Protocol | Value / Configuration | What It Protects |
| :--- | :--- | :--- |
| **Strict-Transport-Security** | `max-age=31536000; includeSubDomains; preload` | Downgrade attacks, SSL stripping. |
| **Content-Security-Policy** | `default-src 'self'; script-src 'self' cdn.example.com; object-src 'none'` | XSS, data injection, Clickjacking. |
| **X-Frame-Options** | `DENY` or `SAMEORIGIN` | Clickjacking (UI redressing). |
| **X-Content-Type-Options** | `nosniff` | MIME-type confusion attacks. |
| **Cookie Flags** | `Secure; HttpOnly; SameSite=Strict` | Prevents cookie theft via XSS, plain-text sniffing, and CSRF. |
| **OAuth 2.0 Grants** | Authorization Code Flow with PKCE | Mobile / SPA / Web API authentication without leaking client secret. |

---

## 4. TOP 5 SCENARIO QUESTIONS: MENTAL FRAMEWORKS

1. **"Developer claims false positive":**
   * *Formula:* Evidence $\rightarrow$ Non-prod reproduction $\rightarrow$ Burp Suite proof $\rightarrow$ Code-level fix suggestion $\rightarrow$ Retest.
2. **"DAST scan failed":**
   * *Formula:* Identify stage (DNS $\rightarrow$ TCP 443 $\rightarrow$ HTTP status $\rightarrow$ Auth $\rightarrow$ Crawl) $\rightarrow$ Coordinate with App/Net team $\rightarrow$ Retest.
3. **"MFA blocks automated scan":**
   * *Formula:* Never disable in prod $\rightarrow$ Request dedicated test account in UAT $\rightarrow$ Configure static OTP / IP bypass in IdP.
4. **"Prioritizing 100 vulnerabilities":**
   * *Formula:* Criticality = Exploitability $\times$ Internet Exposure $\times$ Data Sensitivity (PII/Financial) $\times$ Active Exploitation in Wild $\rightarrow$ SLA tracking.
5. **"Why hire you":**
   * *Formula:* Proven TCS enterprise DAST & API experience $+$ Practical Python automation skills $+$ Proven offensive mindset (HackQuest Top 100) $\rightarrow$ Ready to contribute from Day 1.
