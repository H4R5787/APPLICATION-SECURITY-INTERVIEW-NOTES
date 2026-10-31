# DOCUMENT 3: MASTER TECHNICAL INTERVIEW QUESTION BANK (CATEGORIES A–M)

**Candidate Focus:** Harsh Vishwakarma — Application Security Engineer / DAST Specialist  
**Coverage:** 100+ Deep Technical Questions with Short Answer, Detailed Answer, Real-World Example, Follow-ups, and Key Concepts.

---

## CATEGORY A: PERSONAL INTRODUCTION & CAREER

### Q1: Can you walk me through your career journey and how you arrived at Application Security?
* **Short Answer:** I graduated with a B.Sc. in Computer Science, began my security journey with practical labs on TryHackMe and an internship at Acmegrade, and joined TCS as a DevOps Engineer – Cyber Security. At TCS, I found my niche in Application Security—specifically orchestrating DAST scans, handling complex authentication, and working with development teams to remediate real-world vulnerabilities.
* **Detailed Answer:** During my degree, I developed software projects like an AI-powered malware analysis tool using Flask and VirusTotal APIs. This made me appreciate how critical defensive software engineering is. At TCS, I was placed into the cybersecurity domain where I took ownership of Veracode DAST onboarding, API security testing, and authenticated scan workflows for banking clients. I further honed my offensive assessment skills through TCS HackQuest, placing in the Top 100 nationwide.
* **Real-World Example:** Transitioning from manual CTF problem-solving to enterprise-scale DAST where 40+ banking portals require scheduled scans, zero business disruption, and strict SLA compliance.
* **Possible Follow-ups:** "What was the most surprising difference between CTFs and enterprise banking security?"
* **Important Concepts:** Black-box testing, enterprise governance, compliance SLAs, software development lifecycle (SDLC).

---

## CATEGORY B: TCS PROFESSIONAL EXPERIENCE

### Q2: What is your exact daily workflow at TCS?
* **Short Answer:** My daily responsibilities encompass reviewing incoming application onboarding requests, verifying network reachability, configuring Veracode DAST scan policies and authentication macros, monitoring active scan execution, analyzing and triaging reported vulnerabilities to eliminate false positives, and conducting walkthroughs with developers to guide remediation.
* **Detailed Answer:** Each morning begins with reviewing automated scan dashboards and status alerts from overnight runs. For failed scans, I immediately inspect execution logs, HTTP status codes, and crawl maps to identify the failure point (e.g., locked test accounts, expired TLS certs, or crawler session drops). For completed scans, I extract high and critical findings, replicate them in Burp Suite to confirm validity, and document developer advisories. I also attend sprint planning and security triage calls with development squads to track remediation against our 30-day compliance SLA for high-severity findings.
* **Real-World Example:** Rescuing a blocked banking portal scan where an updated login page broke the automated macro. I updated the Selenium script within 30 minutes, allowing the release gate to pass on time.
* **Possible Follow-ups:** "How do you prioritize between a blocked scan and a developer consultation meeting?"
* **Important Concepts:** Verification testing, scan logs, crawl depth, SLA tracking, developer consultation.

---

## CATEGORY C: VERACODE & DAST DEEP-DIVE

### Q3: Explain the difference between SAST, DAST, IAST, and SCA. Where does each fit in DevSecOps?
* **Short Answer:** SAST analyzes source code without running it (white-box, early in CI/CD). DAST tests the running application from the outside (black-box/gray-box, in UAT/staging). IAST runs inside the application runtime via an agent combining both. SCA scans third-party open-source libraries for known CVEs.
* **Detailed Answer:**
  * **SAST (Static Application Security Testing):** Identifies risky code constructs, insecure cryptography, and missing input validation directly in source repositories before build. High visibility but can produce false positives due to lack of runtime context.
  * **DAST (Dynamic Application Security Testing):** Tests the fully deployed application, including server configuration, authentication flows, and runtime data handling. Zero visibility into source code, but findings are highly actionable and confirm exploitability in a live environment.
  * **IAST (Interactive Application Security Testing):** Employs runtime sensors inside the JVM or CLR. Detects vulnerabilities as functional tests are run, with high accuracy.
  * **SCA (Software Composition Analysis):** Scans package manifests (`package.json`, `pom.xml`, `requirements.txt`) to flag public CVEs in third-party libraries.
* **Real-World Example:** SAST might flag an unvalidated SQL query in a code file, while DAST verifies whether the web application firewall (WAF) or an ORM parameterization actually neutralizes the payload at runtime.
* **Possible Follow-ups:** "Can DAST replace SAST in a mature DevSecOps pipeline?"
* **Important Concepts:** Shift-left, runtime context, false positive rates, dependency scanning.

### Q4: How do you configure and secure an Internal Scan Engine (ISE) in Veracode?
* **Short Answer:** The Veracode ISE is an on-premise scanner deployed as a virtual machine or Docker container within the internal network. It initiates outbound HTTPS connections (port 443) to the Veracode cloud to fetch scan jobs, scans internal target IPs directly, and uploads encrypted results.
* **Detailed Answer:** Since internal banking applications are hosted behind private enterprise firewalls and cannot be accessed from public internet scanner IPs, an ISE is required. The ISE requires no inbound firewall openings. Outbound access over TCP port 443 to `api.veracode.com` is configured through approved enterprise proxies. The internal scanner IP is allowlisted on the target web servers and internal load balancers so that automated security probes are not blocked by internal intrusion prevention systems (IPS).
* **Real-World Example:** Setting up internal scan profiles for core banking ledger services that have no internet exposure.
* **Possible Follow-ups:** "What happens if the ISE loses network connectivity mid-scan?"
* **Important Concepts:** Outbound polling, reverse proxy, fleet management, DMZ deployment.

---

## CATEGORY D: OWASP TOP 10 DEEP-DIVE

### Q5: What is Server-Side Request Forgery (SSRF) and how does it threaten cloud and enterprise applications?
* **Short Answer:** SSRF occurs when an attacker induces a server to send a crafted HTTP request to an unexpected destination, typically internal network resources, loopback interfaces (`127.0.0.1`), or cloud metadata endpoints (`169.254.169.254`).
* **Detailed Answer:** If an application features a capability to import profile pictures from a user-supplied URL or fetch remote webhooks (e.g., `POST /fetch?url=https://user-site.com/image.png`), an attacker can replace that URL with `http://169.254.169.254/latest/meta-data/` in AWS or `http://metadata.google.internal/` in GCP. The server, acting as an unintended proxy, fetches internal credentials (such as temporary IAM role tokens) and returns them in the response.
* **Real-World Example:** The Capital One breach of 2019, where an SSRF vulnerability in a misconfigured WAF allowed an attacker to access the AWS metadata service and exfiltrate credentials for S3 buckets containing over 100 million records.
* **Possible Follow-ups:** "How do you prevent SSRF when the business requirement demands fetching remote URLs?"
* **Important Concepts:** Cloud metadata IP, DNS rebinding, private IP range blocking (RFC 1918), URL allowlisting.

---

## CATEGORY E: REST API SECURITY

### Q6: What is BOLA (Broken Object Level Authorization) and how do you test for it?
* **Short Answer:** BOLA (formerly IDOR) is an authorization flaw where an API endpoint exposes an object identifier without verifying that the requesting user owns that object.
* **Detailed Answer:** In REST APIs, resource endpoints are frequently modeled as `/api/v1/accounts/{accountId}/transactions`. If the API relies solely on client input without validating whether `{accountId}` belongs to the `sub` (subject) of the caller's JWT token, any authenticated user can substitute another user's ID and access their private transaction records.
* **Real-World Example:** Testing a banking API where User A (Token A) swaps their `accountId` parameter with User B's `accountId` in Postman. If HTTP 200 is returned with User B's data, BOLA is confirmed.
* **Possible Follow-ups:** "How does BOLA differ from Mass Assignment?"
* **Important Concepts:** Object ownership validation, JWT claim verification, GUIDs vs sequential IDs, horizontal privilege escalation.

---

## CATEGORY F: AUTHENTICATION, MFA & SSO

### Q7: Explain OAuth 2.0 Authorization Code Flow with PKCE. Why is PKCE needed?
* **Short Answer:** OAuth 2.0 with PKCE (Proof Key for Code Exchange) is the secure standard for single-page applications (SPAs) and mobile apps where client secrets cannot be securely stored. PKCE dynamically generates a cryptographic challenge to prevent authorization code interception attacks.
* **Detailed Answer:**
  1. **Code Verifier & Challenge:** The client generates a random string (`code_verifier`) and hashes it (`code_challenge = SHA256(code_verifier)`).
  2. **Authorization Request:** Client sends user to IdP with `code_challenge` and `code_challenge_method=S256`.
  3. **User Authenticates:** IdP authenticates user and redirects back with an authorization code.
  4. **Token Exchange:** Client sends the authorization code along with the original plain `code_verifier`.
  5. **Verification:** The authorization server hashes the received `code_verifier` and verifies it matches the original `code_challenge`. If matched, tokens (Access, ID, Refresh) are issued.
* **Real-World Example:** Securing a modern React banking portal communicating with a microservices API gateway.
* **Possible Follow-ups:** "Why shouldn't single-page applications use the OAuth 2.0 Implicit Flow?"
* **Important Concepts:** PKCE, Code Verifier, Code Challenge, Implicit Flow deprecation, IdP (Identity Provider).

---

## CATEGORY G: PYTHON, BASH & SCRIPTING

### Q8: Write a Python script snippet that safely validates whether a user-submitted file upload is an image and prevents Path Traversal.
* **Answer & Code:**
```python
import os
import uuid
import magic  # python-magic library for MIME inspection
from werkzeug.utils import secure_filename

ALLOWED_MIME_TYPES = {"image/jpeg", "image/png", "image/gif"}
UPLOAD_DIR = "/var/app/uploads"

def handle_secure_upload(file_storage_obj):
    # 1. Sanitize the original filename to eliminate ../ or path traversal
    safe_name = secure_filename(file_storage_obj.filename)
    if not safe_name:
        raise ValueError("Invalid filename.")
        
    # 2. Inspect the file content header (magic bytes), not just the file extension
    file_bytes = file_storage_obj.read(2048)
    file_storage_obj.seek(0)  # Reset pointer
    
    mime_type = magic.from_buffer(file_bytes, mime=True)
    if mime_type not in ALLOWED_MIME_TYPES:
        raise ValueError(f"Prohibited file type: {mime_type}")
        
    # 3. Save with a randomized UUID filename to prevent overwriting and path traversal
    extension = os.path.splitext(safe_name)[1].lower()
    randomized_filename = f"{uuid.uuid4().hex}{extension}"
    destination_path = os.path.join(UPLOAD_DIR, randomized_filename)
    
    # 4. Save file to non-executable directory
    file_storage_obj.save(destination_path)
    return destination_path
```
* **Key Concepts:** Magic bytes, `secure_filename`, UUID generation, non-executable storage directories.

---

## CATEGORY H: SECURITY TESTING TOOLS

### Q9: How do you configure Burp Suite to intercept traffic from an application that uses SSL Pinning?
* **Short Answer:** SSL pinning forces the client application to trust only a hardcoded certificate or public key. To intercept, the pinning check must be hooked and bypassed dynamically using tools like Frida or Objection on a jailbroken/rooted device or test emulator.
* **Detailed Answer:** When a mobile or thick client app enforces certificate pinning, it rejects Burp Suite's CA certificate during the TLS handshake, causing immediate connection termination. In authorized security testing, we attach Frida to the application process at runtime, hook the SSL validation APIs (e.g., `TrustManager` in Android or `SecTrustEvaluate` in iOS), and force them to return `true`.
* **Important Concepts:** TLS certificate chain, Public Key Pinning (HPKP), Frida dynamic instrumentation, reverse engineering.

---

## CATEGORY I: DEVSECOPS & CI/CD GATING

### Q10: How do you define automated security gates in a Jenkins pipeline without blocking developer deployments unnecessarily?
* **Short Answer:** Implement tiered thresholds: Fail the build strictly for confirmed **Critical/High** vulnerabilities with known exploits in production code, while generating automated Jira tickets or warnings for **Medium/Low** findings without breaking the build.
* **Detailed Answer:** A robust DevSecOps pipeline must balance security compliance with delivery velocity. We configure policy rules in the CI/CD plugin:
  1. Fast security checks (SAST linting, secret detection like GitGuardian/TruffleHog, SCA) execute in the Pull Request phase in under 3 minutes.
  2. DAST scans run asynchronously post-deployment in the UAT/Staging environment.
  3. The pipeline gate evaluates findings against the enterprise policy: if an unmitigated Critical flaw (CVSS > 9.0) is detected, the deployment to Production is automatically halted, and the security team is alerted.
* **Important Concepts:** Quality gates, asynchronous DAST, shift-left, CVSS thresholding, false positive dampening.

---

## CATEGORY J: INTERNSHIP EXPERIENCE (ACMEGRADE)

### Q11: Explain the stages of the Lockheed Martin Cyber Kill Chain and how an Application Security Engineer uses them defensively.
* **Short Answer:** The 7 stages are: Reconnaissance, Weaponization, Delivery, Exploitation, Installation, Command and Control (C2), and Actions on Objectives. AppSec primarily disrupts the **Exploitation** stage by securing code and patching vulnerabilities.
* **Detailed Answer:**
  * **Reconnaissance:** Enumerating web endpoints, technologies, and subdomains (thwarted by disabling verbose error messages and removing version headers).
  * **Weaponization & Delivery:** Crafting and sending malicious input (XSS payloads, SQLi queries) via HTTP requests.
  * **Exploitation:** The vulnerability triggers in the web application. AppSec controls (input validation, output encoding, parameterization, WAF) break the kill chain here, preventing the adversary from ever achieving code execution or data access.
* **Important Concepts:** Threat modeling, defense-in-depth, indicators of compromise (IoCs), kill chain interruption.

---

## CATEGORY K: PERSONAL PROJECTS DEEP-DIVE

### Q12: Walk me through the architecture and security controls of your Virus Detection Web Application.
* **Short Answer:** It is a Flask-based web application backed by MySQL that orchestrates malware analysis by integrating the VirusTotal REST API and Meta's Llama-3 AI model for automated risk summaries.
* **Detailed Answer:**
  * **Architecture:** Users submit files or URLs via an authenticated dashboard. The backend computes the file's SHA-256 hash. If previously scanned, it retrieves cached results from MySQL; otherwise, it uploads the file to VirusTotal's API via secure HTTPS requests. Llama-3 parses the detection engine heuristics and translates them into an executive summary.
  * **Security Controls Implemented:**
    1. Input sanitization on all text fields using bleach and parameterized queries via SQLAlchemy.
    2. Upload restrictions: Files are limited to 25MB, validated for magic bytes, stored with random UUID filenames, and placed in a dedicated directory with execution permissions revoked (`chmod 600`).
    3. Rate limiting enforced via Flask-Limiter to protect API quotas.
* **Possible Follow-ups:** "How did you prevent users from uploading malicious PHP or shell scripts that execute on the server?"
* **Important Concepts:** Defense-in-depth, file execution prevention, API key protection, AI output parsing.

---

## CATEGORY L: CERTIFICATIONS & ACHIEVEMENTS

### Q13: Tell me about your experience in TCS HackQuest Season 9. What challenges did you solve to reach the Top 100?
* **Short Answer:** TCS HackQuest Season 9 was a 24-hour national competitive CTF. I competed against thousands of candidates across the country, solving practical challenges in web application security, IDOR exploitation, SQL injection, and cryptographic analysis to rank in the Top 100.
* **Detailed Answer:** The competition simulated real-world vulnerable banking portals and enterprise systems. I demonstrated hands-on technical proficiency by:
  1. Discovering and exploiting an IDOR vulnerability in an API endpoint to access unauthorized administrative tokens.
  2. Bypassing client-side form validation and executing second-order SQL injection to retrieve sensitive database flags.
  3. Analyzing intercepted network packets in Wireshark to reconstruct credentials transmitted over insecure channels.
* **Important Concepts:** Capture-the-Flag (CTF), offensive security, real-time problem solving, competitive benchmarking.

---

## CATEGORY M: HR & CAREER DECISION QUESTIONS

### Q14: How do you handle working under tight deadlines when multiple scan onboarding requests arrive simultaneously?
* **Short Answer:** I use systematic prioritization based on business criticality, release schedules, and risk exposure, while communicating clear expectations with stakeholders.
* **Detailed Answer:** When multiple requests arrive at once, I evaluate:
  1. Is the application scheduled for a production release within the current sprint?
  2. Is the application customer-facing and handling sensitive banking data, or is it an internal staging portal?
  3. Pre-flight verification: I quickly run automated reachability and authentication checks so that applications with missing prerequisites are sent back for correction immediately without blocking ready applications.
* **Important Concepts:** Triage, stakeholder communication, pipeline efficiency, risk-based prioritization.
