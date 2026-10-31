# DOCUMENT 7: CYBERSECURITY TOOLS & PRACTICAL LAB EXERCISES

**Coverage:** Tool Mastery (Burp Suite, Nmap, ZAP, Wireshark, Metasploit, Postman) & Hands-on Verification Labs

---

## 1. ESSENTIAL APPSEC & NETWORK TOOLS

### A. Burp Suite (Community / Professional)
* **Core Function:** Intercepting HTTP/S proxy that operates as a man-in-the-middle between browser and server, allowing request/response inspection and tampering.
* **Key Modules:**
  * **Proxy:** Intercepts traffic, modifies parameters, headers, cookies in flight.
  * **Repeater:** Manually resends modified HTTP requests to analyze responses, test edge cases, and validate true/false positives.
  * **Intruder:** Automates customized attacks (Sniper, Battering Ram, Pitchfork, Cluster Bomb) for fuzzing parameters and rate-limit testing.
  * **Collaborator:** Detects out-of-band (OAST) interactions (blind SSRF, blind XXE, blind OS injection).
  * **Match and Replace:** Automatically injects custom headers (e.g., `Authorization: Bearer <token>` or custom User-Agent) on outgoing requests.

### B. OWASP ZAP (Zed Attack Proxy)
* **Core Function:** Free, open-source web application security scanner.
* **Key Capabilities:**
  * **Passive Scanning:** Inspects traffic flowing through ZAP without injecting attack payloads (identifies missing security headers, insecure cookies, sensitive info leaks).
  * **Active Scanning:** Generates targeted attack payloads against endpoints in scope.
  * **Context Configuration:** Defines include/exclude regex paths, authentication mechanisms, and session management.

### C. Nmap (Network Mapper)
* **Core Function:** Network discovery and vulnerability enumeration.
* **Critical Commands:**
  * `nmap -sS -p 1-65535 -T4 target.com`: SYN Stealth scan across all ports.
  * `nmap -sV -sC -p 80,443,8080 target.com`: Service version detection and default NSE scripts.
  * `nmap --script ssl-enum-ciphers -p 443 target.com`: Enumerates TLS protocols and weak cipher suites.
  * `nmap -sU --top-ports 100 target.com`: Scans top 100 UDP ports (DNS, SNMP, NTP).

### D. Wireshark
* **Core Function:** Deep packet inspection and network protocol analysis.
* **Essential Display Filters:**
  * `http.request.method == "POST"`: Isolates HTTP POST requests (logins, submissions).
  * `tcp.flags.reset == 1`: Identifies TCP reset packets indicating firewall drops or closed ports.
  * `tls.handshake.type == 1`: Captures TLS Client Hello packets.
  * `dns.flags.response == 1 && dns.qry.name contains "internal"`: Tracks internal DNS resolution.

### E. Metasploit Framework
* **Core Function:** Penetration testing and exploit execution platform.
* **Key Distinction:**
  * **Auxiliary Modules:** Used for scanning, fingerprinting, and fuzzing (e.g., `auxiliary/scanner/http/title`).
  * **Exploit Modules:** Deliver weaponized payloads against verified CVEs (e.g., `exploit/multi/http/apache_log4j2_rce`).
  * **Meterpreter Payload:** Advanced, memory-resident payload that operates entirely in RAM without touching disk, providing encrypted C2 communications.

### F. Postman for API Security Testing
* **Core Function:** API development and testing platform.
* **Security Applications:**
  * Ingest OpenAPI/Swagger definitions to populate all endpoints, query parameters, and JSON request bodies.
  * Configure environment variables for dynamic tokens (`{{bearer_token}}`).
  * Use Collection Runner for automated negative testing, fuzzing invalid data types, and boundary value testing.

---

## 2. HANDS-ON PRACTICAL LAB EXERCISES (AUTHORIZED LAB ENVIRONMENTS)

### Practical Exercise 1: Validating a Suspected SQL Injection (SQLi)
* **Objective:** Verify whether a DAST scanner alert for SQL Injection on an endpoint `GET /products?category=Gifts` is a true positive.
* **Step-by-Step Procedure:**
  1. Capture request in Burp Suite Proxy and send to Repeater.
  2. **Baseline Request:** Send `GET /products?category=Gifts`. Note response time (e.g., 85ms) and response length (e.g., 4200 bytes).
  3. **Syntax Error Test:** Inject single quote: `GET /products?category=Gifts'`. Observe if the server returns a 500 Internal Server Error or database error syntax (e.g., `You have an error in your SQL syntax`).
  4. **Boolean Differential Test:**
     - Inject `Gifts' AND '1'='1`: Content should render identical to the baseline.
     - Inject `Gifts' AND '1'='2`: Content should be empty or return zero products.
  5. **Time-Based Blind Test (Definitive Confirmation):**
     - Inject `Gifts' AND (SELECT 1 FROM (SELECT(SLEEP(5)))a)-- -` (MySQL) or `Gifts'; WAITFOR DELAY '0:0:5'--` (MSSQL).
     - Verify if server response takes approximately 5000ms. Repeat with 10s delay to rule out network lag.
* **Conclusion & Reporting:** If time delay executes predictably, confirm as True Positive (CWE-89, CVSS 8.5+ High). Provide remediation guidance: Replace string concatenation with parameterized SQL queries.

---

### Practical Exercise 2: Verifying Reflected Cross-Site Scripting (XSS)
* **Objective:** Test if user input in a search query parameter is executed in the browser.
* **Step-by-Step Procedure:**
  1. Enter a benign, unique canary string into the input field: `testcanary123`.
  2. Inspect the HTTP response body in Burp Repeater: Find where `testcanary123` is reflected.
  3. **Identify Context:**
     - *HTML Context:* `<div>Search results for testcanary123</div>`
     - *Attribute Context:* `<input value="testcanary123">`
     - *JavaScript Context:* `<script>var query = "testcanary123";</script>`
  4. **Context-Specific Injection:**
     - If HTML context: Inject `<script>alert(document.domain)</script>` or `<img src=x onerror=alert(1)>`.
     - Check response headers for `Content-Security-Policy (CSP)`. If CSP contains `script-src 'self'`, inline execution will be blocked by modern browsers.
  5. **Determination:** If payload is reflected unencoded (`<` and `>` not converted to `&lt;` and `&gt;`) and no restrictive CSP exists, confirm as True Positive.

---

### Practical Exercise 3: Validating an Insecure Direct Object Reference (IDOR / BOLA)
* **Objective:** Validate horizontal privilege escalation on an API endpoint.
* **Step-by-Step Procedure:**
  1. Authenticate as **Test User 1** (Account ID: `501`) and intercept `GET /api/v1/users/501/profile`.
  2. Copy Authorization header: `Bearer <Token_User_1>`.
  3. Authenticate as **Test User 2** (Account ID: `502`) in a second browser session to verify `502` exists.
  4. In Burp Repeater with **Token_User_1**, modify the URL path to `GET /api/v1/users/502/profile`.
  5. **Evaluation:**
     - If response is `403 Forbidden` or `401 Unauthorized`: Access control is securely enforced.
     - If response is `200 OK` and returns User 502's private email/phone/address: Confirm Critical True Positive (BOLA / Broken Object Level Authorization).
