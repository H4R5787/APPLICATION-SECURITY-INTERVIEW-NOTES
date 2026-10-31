# DOCUMENT 4: VERACODE DAST & TCS PROJECT INTERVIEW GUIDE

**Target Role:** Application Security Engineer / DAST Specialist  
**Domain Focus:** Enterprise Banking & Financial Applications

---

## 1. VERACODE ARCHITECTURE & DAST FUNDAMENTALS

### A. What is Veracode Dynamic Analysis (DAST)?
Veracode Dynamic Analysis (DAST) evaluates web applications and REST APIs in their running state from the outside in (black-box / gray-box). It simulates attacks by sending malicious or non-standard HTTP/HTTPS payloads to discover runtime vulnerabilities such as SQL Injection, Cross-Site Scripting (XSS), Server-Side Request Forgery (SSRF), and broken access controls.

### B. Internal Scan Engine (ISE) vs. Cloud Scanning
* **Veracode Cloud Scanner:** Scans external, internet-facing assets from Veracode's public IP ranges. Requires client firewalls to allowlist Veracode IP blocks.
* **Internal Scan Engine (ISE) / Dynamic FLI (Fleet Management):** A lightweight virtual appliance / Docker container deployed inside the enterprise network (e.g., TCS private banking intranet / DMZ). The ISE pulls scan jobs outbound from the Veracode cloud via HTTPS (port 443), executes the scan locally against internal URLs without opening inbound firewall ports, and uploads encrypted results back to the Veracode platform.

---

## 2. END-TO-END APPLICATION ONBOARDING WORKFLOW

```
1. Initiation & Scope Confirmation (Target URL, Environment: UAT/Staging)
             │
             ▼
2. Network Reachability & Allowlisting (Firewall/WAF rules for scanner IPs)
             │
             ▼
3. Authentication Planning (Test account, credentials, MFA handling)
             │
             ▼
4. Scan Configuration in Veracode (URL, exclusions, user-agent, crawl limits)
             │
             ▼
5. Pre-Scan / Verification Check (Confirm scanner logs in and crawls beyond login)
             │
             ▼
6. Scan Execution & Monitoring (Monitor crawl depth, request rates, error codes)
             │
             ▼
7. Results Triage & Validation (Filter false positives via Burp Suite)
             │
             ▼
8. Developer Consultation & Remediation Retest (Publish report, review fixes)
```

---

## 3. AUTHENTICATION CONFIGURATION IN VERACODE

### A. Standard Form-Based Login
* Scanner submits POST request with username and password parameters.
* Best for simple username + password forms with standard session cookie returned.

### B. Selenium-Based Login Scripts (Autologin)
* Used when the login form involves multi-step interactions, JavaScript rendering, CSRF tokens embedded in hidden fields, or complex redirects.
* A Python/Selenium script or Veracode Browser Recording `.side` file is recorded and uploaded to Veracode.
* **Flow:**
  1. Navigate to login page.
  2. Locate username element by ID/XPath and input credentials.
  3. Click `Next` or submit.
  4. Wait for password field to render dynamically (`Explicit Wait`).
  5. Input password and submit.
  6. Assert landing page element (e.g., `Welcome, User` or `/dashboard`) to confirm login success.

### C. Handling MFA in Automated Scans
* **Rule:** Never disable production MFA without authorization.
* **Enterprise Best Practices in UAT/Staging:**
  1. **Conditional Access / IP Whitelisting:** Identity provider (Azure AD / Okta / PingFederate) exempts MFA requests originating from the dedicated DAST Scanner IP.
  2. **Static OTP Test Bypass:** In UAT, developers configure a static, non-expiring OTP (e.g., `000000`) for the dedicated security test account.
  3. **TOTP Seed Injection:** Some modern scanners support passing the base32 TOTP secret seed to calculate 6-digit one-time codes on the fly.

---

## 4. DEEP-DIVE TROUBLESHOOTING SCENARIOS

### Scenario 1: Scan Finishes in 5 Minutes and Reports Only the Login Page
* **Root Cause:** Authentication failure or immediate session invalidation.
* **Investigation Steps:**
  1. Review Veracode scan logs and crawl map.
  2. Check HTTP status code on the authentication step:
     - `401 Unauthorized` / `403 Forbidden`: Invalid credentials or test account locked.
     - `302 Redirect` back to `/login`: Missing session cookie or failed session assertion.
  3. Verify test account status: Has the password expired? Is the account locked due to failed attempts?
  4. Test the login script manually in a browser or Burp Suite using the identical user-agent.
  5. Check for anti-CSRF token expiration or dynamic JavaScript requirements that the headless crawler failed to execute.
* **Remediation:** Update credentials, refresh the Selenium `.side` macro with updated element selectors, and verify login success using the Veracode Verification tool.

### Scenario 2: Scanner Cannot Connect to Application (`Connection Timed Out`)
* **Root Cause:** Network firewall, WAF, or routing block.
* **Investigation Steps:**
  1. Verify DNS resolution from scanner environment: `nslookup target.internal.bank.com`.
  2. Check port reachability: `nc -zv target.internal.bank.com 443` or `curl -Iv https://target.internal.bank.com`.
  3. Verify if the target requires Internal Scan Engine (ISE) execution rather than public cloud scanner.
  4. Check enterprise firewall / proxy allowlists.
  5. Coordinate with Network / Infrastructure team to confirm scanner egress and target ingress rules.

### Scenario 3: Scanner Trapped in an Infinite Loop (Scan Runs for Days)
* **Root Cause:** Dynamic calendar controls, infinite pagination, or unconstrained URL query parameters.
* **Remediation:**
  1. Configure **URL Exclusions** in Veracode: exclude `*/calendar/*`, `*?sort=*`, `*/logout*`.
  2. Configure max crawl depth and max page limits.
  3. Add logout URL to the exclusion list to prevent the scanner from killing its own authenticated session.

---

## 5. API SECURITY TESTING WITH VERACODE

### A. Onboarding REST APIs
1. **API Definition File:** Obtain the OpenAPI / Swagger JSON/YAML specification or Postman Collection (`v2.1`).
2. **Endpoint Configuration:** Define base URL, host, and port.
3. **Authentication Token Passing:**
   - Configure a custom HTTP Request Header: `Authorization: Bearer <token>` or `x-api-key: <key>`.
   - If tokens expire within 1 hour, configure a pre-scan authentication macro to request a fresh token via `/oauth/token` using client credentials grant before executing API tests.

---

## 6. FALSE POSITIVE TRIAGE & DEVELOPER COORDINATION

### A. How to Validate a Reported DAST Vulnerability
1. **Extract Evidence:** In Veracode report, inspect the raw HTTP request sent (with payload) and the raw HTTP response returned.
2. **Replay via Burp Suite Repeater:**
   - Copy request into Burp Suite Repeater.
   - Send original payload. Observe response headers, status code, and response body.
3. **Determine Exploitability:**
   - *Example: Reflected XSS.* If Veracode flags `alert(1)`, verify if the script tag actually executes in the browser context or if it is safely HTML-entity encoded (`&lt;script&gt;`) or blocked by a strict `Content-Security-Policy`.
   - *Example: SQL Injection.* If Veracode reports a time-based blind SQLi (`WAITFOR DELAY '0:0:5'`), test with multiple varying time delays (e.g., 2s, 8s, 12s) to ensure network latency did not trigger a false positive.
4. **Documenting False Positives:** Provide full request/response diff, explain why the condition cannot be exploited, submit through Veracode's formal mitigation/exception workflow, and secure manager sign-off.
