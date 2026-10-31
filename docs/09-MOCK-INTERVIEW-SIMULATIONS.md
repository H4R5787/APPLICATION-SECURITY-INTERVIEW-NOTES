# DOCUMENT 9: MOCK INTERVIEW SIMULATIONS & EVALUATION

This document simulates five complete rounds of an Application Security Engineer hiring process at a premier technology company, complete with evaluator scorecards and feedback.

---

## SIMULATION 1: HR & BACKGROUND SCREENING ROUND

### Interviewer: "I see you're working at TCS as a DevOps Engineer – Cyber Security. Can you explain your primary responsibilities and why you're looking for an AppSec role?"
* **Candidate Answer:**
  > "At TCS, I work in application security focusing on DAST using Veracode for banking clients. I handle application onboarding, configure authenticated scanning with SSO/MFA test accounts, and troubleshoot connectivity or scan failures. I also validate high-severity findings with Burp Suite and help developers understand remediation. I'm looking for an AppSec role because I want to work more deeply on product security, manual testing, and CI/CD integration."
* **Interviewer Evaluation:**
  * *Strengths:* Clear, concise, and truthful to actual daily experience. Accurately links current scope to future goals.
  * *Areas to Improve:* Add quantifiable impact (e.g., number of apps onboarded) and mention independent offensive learning (HackQuest Top 100).
  * *Strong Candidate Upgrade:* Mention: *"Having onboarded and supported over 40 enterprise applications, I understand the friction between security scans and developer velocity, which is why I want to help teams shift security left."*

---

## SIMULATION 2: CORE APPLICATION SECURITY TECHNICAL INTERVIEW

### Interviewer: "Explain the difference between Reflected XSS, Stored XSS, and DOM-based XSS. How would you defend against each?"
* **Candidate Answer:**
  > "In Reflected XSS, the malicious script payload is delivered via the immediate request (like a URL search parameter) and reflected back in the immediate HTTP response without being saved on the server. The attacker tricks the victim into clicking a link.
  > 
  > In Stored XSS, the payload is persistently saved on the server's database (like in a comment field or profile name) and served to every user who visits that page. It has higher impact because it requires no user interaction beyond visiting the page.
  > 
  > In DOM-based XSS, the vulnerability exists entirely in client-side JavaScript. The server never sees the payload; client-side script takes data from an untrusted source (like `location.search` or `document.URL`) and passes it into an unsafe sink (like `element.innerHTML` or `eval()`).
  > 
  > To defend:
  > 1. Use context-aware output encoding (HTML, attribute, JavaScript encoding).
  > 2. Use safe DOM APIs like `textContent` instead of `innerHTML`.
  > 3. Enforce a strong Content Security Policy (`script-src 'self'`)."
* **Interviewer Evaluation:**
  * *Rating:* 10/10 Excellent. Covers definition, delivery mechanism, execution context, and tiered defenses.

---

## SIMULATION 3: VERACODE & DAST PRACTICAL DEEP-DIVE

### Interviewer: "You configure an authenticated scan on an enterprise banking app. The scan runs for 6 hours, consumes hundreds of requests, but reports 0 vulnerabilities and 0 pages discovered behind the dashboard. How do you triage this?"
* **Candidate Answer:**
  > "I would systematically triage this in four stages:
  > 1. **Scan Activity & Crawl Map:** First, I check the Veracode crawl logs and HTTP status codes. A 6-hour scan with 0 pages behind login usually indicates that the initial login failed or the crawler triggered a session termination on the second request.
  > 2. **Session Persistence:** I check whether the application invalidated the session due to a missing session cookie, an anti-CSRF token mismatch, or an idle session timeout.
  > 3. **Logout URL Crawling:** In many DAST setups, if the `/logout` endpoint is not explicitly excluded from the crawl scope, the crawler clicks the 'Logout' button during early exploration, immediately killing the session and spending the remaining 6 hours scanning the public login page.
  > 4. **Verification & Action:** I reproduce the login in Burp Suite using the same credentials and User-Agent. Once confirmed, I add the logout URL to the exclusion list, update the Selenium autologin script with explicit wait assertions, and re-run a pre-scan verification before executing the full scan."
* **Interviewer Evaluation:**
  * *Rating:* 10/10 Outstanding. Mentioning the crawler hitting the `/logout` button shows real, authentic enterprise DAST operational experience that cannot be faked.

---

## SIMULATION 4: PYTHON, API SECURITY & AUTOMATION ROUND

### Interviewer: "What is BOLA (Broken Object Level Authorization) in REST APIs, how does it differ from BFLA, and how would you test for it using Python?"
* **Candidate Answer:**
  > "BOLA (API1 in OWASP API Security Top 10) occurs when an API endpoint accepts an object identifier from the client (e.g., `GET /api/v1/orders/{order_id}`) without verifying that the authenticated user owns that specific order. It is horizontal privilege escalation.
  > 
  > BFLA (Broken Function Level Authorization), on the other hand, is vertical privilege escalation—where a regular user accesses administrative functions or endpoints (e.g., a standard user executing `DELETE /api/v1/users/{id}`).
  > 
  > To test for BOLA in Python:
  > I obtain two valid JWT tokens for two distinct test users (User A and User B). In Python using `requests`, I set the `Authorization: Bearer <Token_A>` header but request an object ID belonging to User B:
  > ```python
  > resp = requests.get('https://target.com/api/v1/orders/9999', headers={'Authorization': f'Bearer {token_a}'})
  > if resp.status_code == 200:
  >     print('[!] Critical BOLA vulnerability detected!')
  > elif resp.status_code in [401, 403, 404]:
  >     print('[+] Access securely denied.')
  > ```"
* **Interviewer Evaluation:**
  * *Rating:* 10/10. Precise explanation of authorization models and demonstrates immediate practical automation capability.

---

## SIMULATION 5: SENIOR-LEVEL SCENARIO & RISK MANAGEMENT ROUND

### Interviewer: "A critical 0-day vulnerability is announced in a third-party framework used across 20 of your banking applications. Management is demanding an immediate DAST scan on all production applications. What is your response and plan of action?"
* **Candidate Answer:**
  > "My priority is protecting application stability while rapidly assessing risk:
  > 1. **Explain DAST Limitations for 0-days:** I clarify to management that DAST is a dynamic behavioral test and scanner vendor signatures for a newly released 0-day often take days to be released and validated. Furthermore, running unconstrained aggressive scans directly against production banking databases during business hours risks service degradation or data corruption.
  > 2. **Immediate SCA / SBOM Identification:** The fastest and safest way to identify affected assets is Software Composition Analysis (SCA) and SBOM analysis—checking dependency manifests (e.g., `pom.xml`, `package.json`, `requirements.txt`) across source repositories to identify exactly which 20 applications include the vulnerable library version.
  > 3. **Targeted Non-Production Testing:** For applications confirmed to have the vulnerable component, we spin up targeted scans or manual PoC checks in UAT/Staging environments.
  > 4. **WAF & Compensating Controls:** While developers patch the underlying code, we work with the network/cloud security team to deploy emergency virtual patching rules at the Web Application Firewall (WAF) or API Gateway to block incoming exploit payloads.
  > 5. **Post-Fix Retesting:** Once developers deploy the patched library to staging, we verify the remediation and approve deployment to production."
* **Interviewer Evaluation:**
  * *Rating:* Exceptional. Demonstrates engineering maturity, risk management, and holistic DevSecOps thinking beyond just running a scanner.
