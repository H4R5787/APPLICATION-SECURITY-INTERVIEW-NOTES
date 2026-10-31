# DOCUMENT 5: OWASP TOP 10 & API SECURITY COMPREHENSIVE GUIDE

**Target Roles:** Application Security Engineer | Product Security Engineer  
**Coverage:** OWASP Web Top 10 & OWASP API Security Top 10

---

## PART 1: OWASP WEB TOP 10 (IN-DEPTH TECHNICAL BREAKDOWN)

### A01: Broken Access Control
* **Definition:** Failure to enforce permissions, allowing users to access unauthorized data or functions (horizontal or vertical privilege escalation).
* **Mechanism:** Applications trust client-side parameters, cookies, or hidden fields to determine user identity or authorization level without server-side verification.
* **Real-World Example:**
  - Insecure Direct Object Reference (IDOR): A logged-in customer views their invoice at `GET /api/invoices?id=1042`. By modifying the parameter to `id=1043`, they view another customer's financial statement.
* **Testing Methodology:**
  1. Capture request in Burp Suite with User A's session.
  2. Swap object ID to an object owned by User B.
  3. Inspect response: If HTTP 200 and data is returned, Broken Access Control exists.
  4. Test vertical escalation by accessing administrative endpoints (e.g., `/admin/users`) with a standard user token.
* **Prevention:**
  - Enforce server-side authorization checks on every request.
  - Implement role-based (RBAC) or attribute-based (ABAC) access control.
  - Deny by default. Use indirect reference maps or GUIDs instead of sequential IDs.

---

### A02: Cryptographic Failures (Sensitive Data Exposure)
* **Definition:** Inadequate protection of sensitive data in transit or at rest.
* **Mechanism:** Use of obsolete algorithms (MD5, SHA1, DES), plain HTTP, missing HSTS, or storing unhashed passwords.
* **Testing Methodology:**
  - Check TLS cipher suites using `testssl.sh` or `nmap --script ssl-enum-ciphers -p 443 <target>`.
  - Check cookies for `Secure` flag.
  - Inspect API responses for leaked PII, credit card numbers, or internal credentials.
* **Prevention:**
  - Enforce TLS 1.2/1.3 with strong cipher suites.
  - Mandate `Strict-Transport-Security: max-age=31536000; includeSubDomains; preload`.
  - Hash passwords using modern salted algorithms: Argon2id, bcrypt, or PBKDF2.

---

### A03: Injection (SQLi, Command Injection, LDAP)
* **Definition:** Untrusted input is interpreted by an interpreter as commands or query syntax.
* **Mechanisms:**
  - **SQL Injection (SQLi):** Attacker injects `' OR 1=1 --` into an input field, altering the logic of SQL queries.
    - *Types:* In-band (Classic/Union-based), Inferential (Blind boolean-based, Blind time-based), Out-of-band.
  - **OS Command Injection:** Untrusted input passed directly to `system()`, `exec()`, or `subprocess.Popen(shell=True)`.
* **Testing Methodology:**
  - Submit test characters: `'`, `"`, `';`, `--`, `/*`, `|| ping -c 4 127.0.0.1`.
  - In Burp Suite Repeater, monitor error messages (syntax errors), response time changes (time-based `SLEEP(5)`), or differential page content.
* **Prevention:**
  - Use parameterized queries / prepared statements with ORMs (e.g., SQLAlchemy, Hibernate).
  - Avoid dynamic query concatenation.
  - Use least-privilege database user accounts.

---

### A04: Insecure Design
* **Definition:** Architectural flaws that cannot be fixed by perfect implementation because the security design was flawed from the beginning.
* **Example:** Unlimited password reset attempts without rate limiting or CAPTCHA, allowing brute force of reset codes.
* **Prevention:** Threat modeling (STRIDE), secure architecture reviews, and defense-in-depth principles.

---

### A05: Security Misconfiguration
* **Definition:** Insecure default configurations, open cloud storage buckets, verbose error pages, or missing security headers.
* **Key Security Headers:**
  - `Content-Security-Policy (CSP)`: Mitigates XSS by specifying allowed resource sources.
  - `X-Frame-Options: DENY` or `SAMEORIGIN`: Prevents Clickjacking.
  - `X-Content-Type-Options: nosniff`: Prevents MIME-type sniffing.
  - `Referrer-Policy: strict-origin-when-cross-origin`: Restricts sensitive query strings in referrer headers.

---

### A06: Vulnerable and Outdated Components (Software Supply Chain)
* **Definition:** Using third-party libraries, frameworks, or dependencies with known public CVEs.
* **Prevention:** Integrate Software Composition Analysis (SCA) tools into CI/CD: OWASP Dependency-Check, Snyk, GitHub Dependabot.

---

### A07: Identification and Authentication Failures
* **Definition:** Vulnerabilities permitting credential stuffing, brute force, session fixation, or weak session token handling.
* **Key Defenses:** Multi-Factor Authentication (MFA), password complexity policies, session regeneration upon login, and secure cookie attributes (`HttpOnly`, `Secure`, `SameSite=Lax/Strict`).

---

### A08: Software and Data Integrity Failures
* **Definition:** Relying on unverified software updates, plugins, or serialized objects from untrusted sources.
* **Example:** Insecure Deserialization (Python `pickle.loads()`, Java `ObjectInputStream`). An attacker sends serialized bytecode executing arbitrary commands.
* **Prevention:** Avoid native deserialization of untrusted data; use safe data serialization formats like JSON.

---

### A09: Security Logging and Monitoring Failures
* **Definition:** Insufficient logging of authentication, authorization failures, or high-value transactions, delaying breach detection.
* **Requirement:** Centralized logging with SIEM (Splunk, ELK), logging user ID, timestamp, source IP, and action, while masking passwords and sensitive PII.

---

### A10: Server-Side Request Forgery (SSRF)
* **Definition:** The server fetches a remote resource without validating the user-supplied URL, allowing attackers to access internal systems (cloud metadata, internal APIs).
* **Example:** Attacker requests `GET /fetch?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/` to steal AWS IAM credentials.
* **Prevention:** Whitelist permitted hostnames/protocols, disable internal IP redirects, block loopback and private IP spaces (RFC 1918).

---

## PART 2: OWASP API SECURITY TOP 10 (CRITICAL FOCUS)

| Vulnerability | Explanation & Real-World Mechanism | Prevention |
| :--- | :--- | :--- |
| **API1: BOLA (Broken Object Level Authorization)** | The API equivalent of IDOR. Endpoint `/api/v1/accounts/{id}` lacks verification that `{id}` belongs to the requesting user. | Validate that the authenticated token subject (`sub`) owns the requested object ID on every DB query. |
| **API2: Broken Authentication** | Weak token generation, missing expiry validation, or accepting unsigned JWTs (`alg: none`). | Use standard OAuth 2.0 flows, validate JWT signatures, verify `exp`, `iss`, and `aud` claims. |
| **API3: Broken Object Property Level Authorization** | Mass Assignment: User submits `{"role": "admin"}` in JSON during profile update, and the backend binds it directly to the model. | Use Data Transfer Objects (DTOs) with strict schema binding; explicitly allowlist editable fields. |
| **API4: Unrestricted Resource Consumption** | Missing rate limiting, request size limits, or unconstrained page queries (`?limit=1000000`) leading to DoS. | Implement token-bucket / leaky-bucket rate limiting via API gateways; enforce maximum query pagination. |
| **API5: BFLA (Broken Function Level Authorization)** | Standard user accesses admin functions: `DELETE /api/v1/users/{id}` works with standard bearer token. | Enforce strict role/permission checks based on endpoint HTTP method and path at the controller/gateway level. |
| **API6: Unrestricted Access to Sensitive Business Flows** | Automated bots purchase inventory or abuse referral programs without human verification. | Implement CAPTCHA, device fingerprinting, and behavioral velocity checks on sensitive flows. |
| **API7: Server-Side Request Forgery (SSRF)** | API downloads external Webhooks or file URLs without validation. | Strict URL allowlisting, resolve domain to IP before connection, block private/loopback IP ranges. |
| **API8: Security Misconfiguration** | Unprotected CORS (`Access-Control-Allow-Origin: *` with credentials), verbose stack traces, open Swagger UI in production. | Restrict CORS to trusted origins, disable debug mode, restrict OpenAPI documentation to internal networks. |
| **API9: Improper Inventory Management** | Zombie or shadow APIs (`/api/v1/` left unpatched while `/api/v2/` is active). | Maintain an automated API catalog, deprecate legacy endpoints, and decommission retired routes. |
| **API10: Unsafe Consumption of APIs** | Backend trusts third-party API response without validation, leading to injection or deserialization issues. | Treat third-party API data as untrusted user input; validate schemas and sanitize data. |
