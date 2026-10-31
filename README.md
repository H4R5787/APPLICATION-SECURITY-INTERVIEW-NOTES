# APPLICATION SECURITY ENGINEER — COMPLETE INTERVIEW NOTES
Target experience: Around 3 years
Focus: Veracode DAST, Authenticated Scanning, Web Application Security, API Security, Python Automation, and DevSecOps

============================================================
HOW TO USE THESE NOTES
============================================================

1. Learn the concepts and understand why each security control is needed.
2. Practice explaining answers in simple English.
3. Use examples from your actual TCS work wherever relevant.
4. Do not claim that you performed tasks or achieved results that you have not actually done.
5. For scenario questions, explain your investigation, evidence, coordination, action, and verification.

============================================================
PART 1: CORE APPLICATION SECURITY INTERVIEW QUESTIONS
============================================================

1. TELL ME ABOUT YOURSELF

Answer:

"I'm working at TCS in the cybersecurity domain. My work includes supporting Veracode DAST activities, authenticated scan configuration, and coordinating with application or support teams on scan setup and access-related issues.

I'm strengthening my knowledge of web application security, API security, and Python automation. I want to grow into an Application Security Engineer role where I can contribute to security testing, automation, and integrating security into the software development lifecycle."

2. WHAT IS THE DIFFERENCE BETWEEN SAST AND DAST?

Answer:

SAST stands for Static Application Security Testing. It analyzes source code or another supported code representation without running the application.

DAST stands for Dynamic Application Security Testing. It tests a running application by sending requests and analyzing responses.

SAST can identify risky code patterns, while DAST can identify certain security weaknesses observable during runtime. Both approaches complement each other.

3. WHAT IS VERACODE DAST?

Answer:

Veracode DAST is used to test running web applications for security weaknesses. Depending on the configuration and application, it can crawl pages, send test requests, and analyze responses.

Correct target configuration, application reachability, authentication, and crawl coverage are important for a useful scan.

4. HOW DO YOU SET UP AN AUTHENTICATED DAST SCAN?

Answer:

First, I confirm that the target URL and test environment are correct and that testing is authorized.

Next, I identify the authentication method, such as a login form or supported SSO flow. I configure authentication using the approved process and securely handled test credentials.

Then, I validate that the scanner can authenticate and access pages behind the login. I start the scan and monitor its status and logs for errors.

5. WHAT IS THE DIFFERENCE BETWEEN AUTHENTICATED AND UNAUTHENTICATED SCANNING?

Answer:

An unauthenticated scan tests functionality accessible without logging in.

An authenticated scan uses a configured login or session to test pages that require authentication.

Authenticated scans can provide broader coverage, but only if the scanner can log in successfully and maintain its session.

6. THE SCAN FINISHES, BUT ONLY THE LOGIN PAGE IS DISCOVERED. WHAT DO YOU DO?

Answer:

I would review the scan results and logs to determine whether authentication succeeded.

I would check:
- Authentication configuration and login steps.
- Redirects and session cookies.
- Session expiration.
- Test-account permissions.
- SSO or MFA limitations.
- Starting URL and crawl scope.

I would coordinate with the relevant application or support team to address the confirmed issue. After the change, I would rerun the scan and verify that authenticated pages are discovered.

7. HOW WOULD YOU INVESTIGATE A FAILED AUTHENTICATED SCAN?

Answer:

I would identify the stage at which the scan failed.

I would check the application URL, network reachability, credentials, authentication configuration, session handling, redirects, and available logs.

I would also check whether the test account is locked or lacks permissions. I would coordinate with the application or support team, make only authorized changes, and rerun the scan to confirm the outcome.

8. THE SCANNER CANNOT CONNECT TO THE APPLICATION. WHAT WOULD YOU CHECK?

Answer:

I would confirm the URL, DNS resolution, TLS certificate behavior, and whether the application is reachable from the scanner's environment.

I would check approved firewall rules, proxy settings, allowlists, and network restrictions.

I would coordinate with the network or application team to investigate connectivity. I would not bypass security controls without authorization.

9. WHAT IS THE OWASP TOP 10?

Answer:

The OWASP Top 10 is an awareness resource describing major web application security risk categories.

It helps developers and security teams understand common risks, discuss them consistently, and prioritize prevention and testing.

Reference:
https://owasp.org/www-project-top-ten/

10. WHAT IS SQL INJECTION, AND HOW CAN IT BE PREVENTED?

Answer:

SQL injection occurs when untrusted input changes the meaning of a database query.

The primary prevention is to use parameterized queries or prepared statements rather than concatenating user input into SQL.

Other measures include avoiding unsafe dynamic SQL, using least-privilege database accounts, and testing safely in an authorized environment.

11. WHAT IS CROSS-SITE SCRIPTING (XSS)?

Answer:

Cross-site scripting occurs when an application causes untrusted content to execute as script in a user's browser.

Common types include:
- Reflected XSS.
- Stored XSS.
- DOM-based XSS.

Prevention includes context-aware output encoding, safe DOM APIs, careful handling of untrusted content, and an appropriate Content Security Policy as an additional layer of defense.

12. WHAT IS THE DIFFERENCE BETWEEN AUTHENTICATION AND AUTHORIZATION?

Answer:

Authentication verifies who a user is.

Authorization determines what that user is allowed to access or do.

For example, a user may log in successfully but should not be allowed to access an administrator-only page.

Applications must enforce authorization checks on the server side.

13. WHAT IS BROKEN ACCESS CONTROL?

Answer:

Broken access control occurs when an application does not correctly enforce what a user is allowed to access.

For example, a user might be able to view another user's record by changing an identifier in a request.

Prevention includes server-side authorization checks on every relevant request, least privilege, and tests using accounts with different roles.

14. A DEVELOPER SAYS A FINDING IS A FALSE POSITIVE. WHAT DO YOU DO?

Answer:

I would review the finding details, request and response evidence, affected endpoint, and reproduction steps.

In an authorized test environment, I would validate the behavior safely and determine whether the reported condition is actually exploitable.

I would document the evidence and discuss it with the developer. I would mark it as a false positive only when the evidence supports that conclusion and the required process is followed.

15. HOW WOULD YOU PRIORITIZE 100 SECURITY FINDINGS?

Answer:

I would consider severity, exploitability, whether the application is internet-facing, sensitivity of the affected data, business impact, known exploitation, and available mitigations.

I would validate high-impact findings and coordinate remediation priorities with the application owner.

I would track owners, due dates, exceptions, and retest results rather than prioritizing by severity score alone.

16. HOW CAN PYTHON HELP AUTOMATE SECURITY TESTING?

Answer:

Python can help call approved APIs, collect scan statuses, process reports, identify failed scans, and generate summaries.

A reliable script should include:
- Error handling.
- Timeouts.
- Secure credential handling.
- Useful logging.
- Access controls.

I would test automation against a non-production or approved environment before using it in a wider workflow.

17. HOW WOULD YOU INTEGRATE SECURITY TESTING INTO CI/CD?

Answer:

I would identify the right security checks for each stage of the pipeline.

SAST and dependency checks can run earlier in development, while DAST can test a deployed test environment.

I would define severity thresholds and failure criteria with the development and security teams, store credentials securely, and make results visible to the team.

The process should provide actionable feedback without creating unnecessary pipeline failures.

18. HOW WOULD YOU EXPLAIN A CRITICAL SECURITY FINDING TO A NONTECHNICAL PERSON?

Answer:

I would explain the business risk in plain language: what could happen, which system or data could be affected, and why the issue needs attention.

I would describe the recommended next step, urgency, and who needs to act.

I would avoid unnecessary jargon and distinguish confirmed impact from potential impact.

19. WHAT WOULD YOU DO IF MFA OR SSO BLOCKED THE SCAN?

Answer:

I would identify where the authentication flow fails and review the scanner's supported authentication options and the application's approved test process.

I would coordinate with the identity or application team to establish an authorized test approach, such as a suitable test account or supported flow.

I would not disable MFA or weaken production controls without explicit approval.

20. HOW DO YOU VERIFY THAT A FIX WORKED?

Answer:

I would confirm that the application owner deployed the change, rerun the relevant authorized test, and review the result and evidence.

I would verify whether the original issue is no longer reproducible and check for unintended effects where appropriate.

I would record the retest result and close the finding according to the team's process.

21. HOW DO YOU HANDLE A SECURITY ISSUE YOU CANNOT RESOLVE YOURSELF?

Answer:

I would gather clear evidence, describe the impact and steps already attempted, and raise it with the correct application, network, identity, or support team.

I would communicate the blocker promptly, track ownership and progress, and retest once a fix or configuration change is available.

I would not make unauthorized changes.

22. WHAT MAKES YOU READY FOR A THREE-YEAR-LEVEL ROLE?

Answer:

"I focus on understanding the reason behind a security issue, gathering evidence, communicating clearly, and verifying the outcome.

I am developing deeper knowledge of authenticated DAST, web and API security, and automation. I can explain the work I have actually performed and identify areas where I need more hands-on experience."

IMPORTANT:
Prepare one real example describing the problem, your role, investigation, action, and verified outcome. Do not invent metrics or claim ownership of work performed by others.

============================================================
PART 2: 30 ADDITIONAL SCENARIO-BASED QUESTIONS
============================================================

23. YOU DISCOVER A POTENTIAL XSS VULNERABILITY IN A CRITICAL WEB APPLICATION. HOW WOULD YOU MITIGATE IT?

Answer:

I would validate the finding safely in an authorized environment and identify the affected input and output context.

I would recommend context-aware output encoding, safe handling of untrusted input, and avoiding unsafe DOM operations.

A suitable Content Security Policy can provide an additional layer of defense, but it does not replace fixing the underlying issue.

I would retest after remediation.

24. DURING A CODE REVIEW, YOU IDENTIFY A RISK OF SQL INJECTION. HOW WOULD YOU ADDRESS IT?

Answer:

I would recommend parameterized queries or prepared statements so user input is treated as data rather than executable SQL.

I would also review unsafe dynamic SQL and database permissions, then test the fix in an authorized environment.

25. THE APPLICATION ALLOWS USERS TO UPLOAD FILES. HOW WOULD YOU SECURE FILE UPLOADS?

Answer:

I would recommend validating permitted file types and content, limiting file size, generating safe server-side filenames, and storing uploads in a controlled location.

I would enforce access controls and consider antivirus or malware scanning where appropriate.

I would also consider file-parsing risks and test the controls.

26. HOW WOULD YOU IMPLEMENT MFA IN AN EXISTING APPLICATION?

Answer:

I would work with the application's identity design to combine independent authentication factors, such as a password and an authenticator-generated one-time code or another approved factor.

I would consider secure enrollment and recovery, rate limiting, session handling, and protection against bypass.

The method should match the application's risk and supported identity platform.

27. A NEW FEATURE REQUIRES DIFFERENT LEVELS OF USER ACCESS. HOW WOULD YOU IMPLEMENT RBAC?

Answer:

I would define roles based on job responsibilities and map each role to the minimum permissions it needs.

I would assign users to appropriate roles and ensure the application enforces authorization on the server for every relevant operation.

I would test that users cannot perform actions outside their assigned roles.

28. THE TEAM IS CONSIDERING TOKEN-BASED AUTHENTICATION. WHAT ARE ITS ADVANTAGES AND SECURITY CONCERNS?

Answer:

Token-based authentication can support scalable, stateless interactions and integrations.

Risks include token theft or leakage, excessive token lifetime, insecure storage, and inadequate validation.

I would recommend secure transport, appropriate expiration and rotation, careful storage, and correct signature, issuer, and audience validation where applicable.

29. HOW WOULD YOU PROTECT AN APPLICATION AGAINST CSRF?

Answer:

I would determine whether the application relies on browser cookies that are automatically sent with requests.

Where applicable, I would use anti-CSRF tokens and validate them server-side, configure appropriate SameSite cookie settings, and verify origin or referer information as an additional control where suitable.

State-changing actions should not rely on simple GET requests.

30. HOW DOES CSP HELP SECURE A WEB APPLICATION?

Answer:

Content Security Policy lets the application define which sources the browser may load scripts, styles, and other resources from.

I would begin with a policy suited to the application, test it in report-only mode if appropriate, review violations, and then enforce it.

I would avoid overly broad directives and treat CSP as an additional defense rather than a substitute for secure coding.

31. WHY ARE SECURITY HEADERS SUCH AS HSTS IMPORTANT?

Answer:

Security headers instruct browsers to apply additional protections.

HTTP Strict Transport Security tells a browser to use HTTPS for a specified period after receiving the header over a secure connection.

I would confirm HTTPS is correctly deployed across the intended scope before enabling an appropriate HSTS policy.

32. YOU ARE ASKED TO PERFORM A SECURITY CODE REVIEW. WHAT WOULD YOU FOCUS ON?

Answer:

I would review input handling, output encoding, authentication and authorization, session management, secrets, cryptography, file handling, error handling, and database access.

I would combine automated tools with manual review and trace risky data flows.

I would document evidence and practical remediation guidance.

33. A VULNERABILITY SCAN REPORTS SEVERAL CRITICAL ISSUES. HOW WOULD YOU PRIORITIZE THEM?

Answer:

I would validate the findings and prioritize using severity, exploitability, exposure, business impact, affected data, and available mitigations.

I would coordinate with application owners to agree on remediation, track responsibilities and due dates, and retest the fixes.

I would not assume every scanner result is confirmed without reviewing its evidence.

34. YOU RECEIVE AN ALERT ABOUT A POTENTIAL SECURITY INCIDENT. WHAT STEPS WOULD YOU TAKE?

Answer:

I would follow the incident response plan by assessing and scoping the alert, preserving relevant evidence and logs, and notifying the appropriate responders.

I would support containment of affected systems when authorized, investigation, and recovery.

I would document actions and contribute to lessons learned. I would avoid actions that could destroy evidence or exceed my authority.

35. WHY IS ENCRYPTION IMPORTANT, AND HOW WOULD YOU PROTECT DATA IN TRANSIT?

Answer:

Encryption in transit protects confidentiality and helps protect data from interception or tampering while it moves between systems.

I would use properly configured TLS, validate certificates, disable obsolete protocols and weak configurations, and manage certificates and keys securely.

36. A DATABASE INTERACTION IS VULNERABLE TO SQL INJECTION. HOW WOULD YOU SECURE IT?

Answer:

I would replace unsafe query construction with parameterized queries or prepared statements.

I would review dynamic SQL and apply least-privilege permissions to the database account.

I would test the fix with safe, authorized cases and check that error handling does not expose sensitive details.

37. WHAT ARE THE KEY CONSIDERATIONS FOR SECURE SESSION MANAGEMENT?

Answer:

I would use unpredictable session identifiers, secure cookie attributes such as Secure, HttpOnly, and appropriate SameSite settings, and suitable idle and absolute timeouts.

I would rotate session identifiers after authentication or privilege changes, invalidate sessions on logout, and consider reauthentication for sensitive actions.

I would also protect against session fixation and session theft.

38. HOW WOULD YOU SECURE A REST API?

Answer:

I would use an appropriate authentication mechanism, such as OAuth 2.0 or an identity-provider-supported token flow.

I would validate tokens correctly and enforce authorization for each resource and action.

I would use TLS, validate input, apply rate limits, avoid exposing sensitive data, and use integrity controls appropriate to the API and threat model.

API keys alone should not be treated as a replacement for user authorization.

39. WHY IS API RATE LIMITING IMPORTANT?

Answer:

Rate limiting helps reduce brute-force attempts, abuse, and resource exhaustion while supporting fair use.

I would define limits based on the endpoint and risk, and apply tracking by account, token, client, or IP as appropriate.

I would return suitable responses when limits are exceeded and monitor suspicious patterns.

40. WHAT SECURITY CONSIDERATIONS APPLY WHEN INTEGRATING THIRD-PARTY APIS?

Answer:

I would verify the provider and required permissions, use secure communication, protect API credentials in an approved vault, validate returned data, and apply timeouts and error handling.

I would request least-privilege scopes, rotate credentials according to policy, monitor usage, and review the integration when requirements change.

41. AN APPLICATION IS MIGRATING TO THE CLOUD. WHAT SECURITY MEASURES WOULD YOU IMPLEMENT?

Answer:

I would review identity and access management, least privilege, encryption at rest and in transit, network segmentation, secure configuration, logging and monitoring, secrets management, backup and recovery, and vulnerability management.

I would use relevant cloud-native security services and validate configurations against organizational requirements.

42. EXPLAIN THE SHARED RESPONSIBILITY MODEL IN CLOUD SECURITY.

Answer:

The cloud provider secures the underlying cloud infrastructure, while the customer remains responsible for security tasks such as data, identities, applications, and configurations, depending on the service model.

I would identify the exact division of responsibility for the selected service instead of assuming the provider handles every security control.

43. HOW WOULD INCIDENT RESPONSE DIFFER FOR CLOUD RESOURCES?

Answer:

I would follow the same incident response principles but use cloud-specific identity, audit, network, workload, and control-plane logs.

I would coordinate with the cloud provider when needed, isolate or restrict affected resources through approved procedures, preserve evidence, review configuration changes, and restore services safely.

44. HOW CAN AI AND MACHINE LEARNING ENHANCE THREAT DETECTION?

Answer:

AI and machine learning can help analyze large volumes of events, identify unusual patterns, correlate signals, and prioritize alerts.

Their outputs still require validation because models can produce false positives or miss threats.

I would combine them with reliable telemetry, human review, and established incident response processes.

45. HOW CAN BLOCKCHAIN BE USED TO ENHANCE DISTRIBUTED APPLICATION SECURITY?

Answer:

A blockchain can provide a tamper-evident shared ledger for certain transaction records and help participants verify recorded changes.

Its suitability depends on the use case and threat model.

It does not automatically make an application secure. Access control, key management, smart-contract security, privacy, and surrounding systems still require protection.

46. AN APPLICATION INTEGRATES IOT DEVICES. WHAT SECURITY CONSIDERATIONS SHOULD BE ADDRESSED?

Answer:

I would consider unique device identity, secure provisioning, authenticated and encrypted communication, secure firmware updates, credential protection, device inventory, network segmentation, and vulnerability handling throughout the device lifecycle.

Protocols should be selected and configured according to the device's capabilities and threat model.

47. HOW WOULD YOU COMMUNICATE SECURITY RISKS TO EXECUTIVES OR NONTECHNICAL STAKEHOLDERS?

Answer:

I would explain what could happen, which business process or data could be affected, how serious the impact may be, and what action is recommended.

I would distinguish confirmed facts from potential impact, avoid unnecessary jargon, and provide clear owners, urgency, and next steps.

48. HOW WOULD YOU FOSTER COLLABORATION WITH DEVELOPMENT TEAMS?

Answer:

I would involve developers early, communicate findings with clear evidence and actionable remediation advice, and integrate appropriate security checks into the development lifecycle.

Regular discussions, knowledge sharing, and practical guidance help make security a shared responsibility rather than a last-minute obstacle.

49. HOW WOULD YOU CONDUCT AN INCIDENT-RESPONSE TABLETOP EXERCISE?

Answer:

I would define a realistic scenario and objectives, bring together relevant security, IT, application, legal, communications, and management representatives, and walk through decisions using the incident response plan.

I would record gaps, unclear ownership, and communication issues, then assign actions and follow up on improvements.

50. HOW DO YOU EXPECT AUTOMATION TO EVOLVE IN APPLICATION SECURITY?

Answer:

Automation can make recurring scans, status checks, report processing, alert triage, and evidence collection more consistent and efficient.

It can shorten feedback cycles and free people to focus on investigation and risk decisions.

Automation still needs secure credentials, error handling, monitoring, testing, and human oversight.

51. WHAT SECURITY MEASURES MATTER FOR CLOUD-NATIVE ARCHITECTURES?

Answer:

I would emphasize identity and access management, secure container images, dependency and image scanning, secrets management, workload isolation, Kubernetes or serverless configuration security where relevant, infrastructure-as-code checks, runtime monitoring, and centralized logging.

Controls should match the architecture and be continuously reviewed.

52. HOW DO YOU STAY UPDATED ON APPLICATION SECURITY AND ENCOURAGE CONTINUOUS LEARNING?

Answer:

I would follow trusted security resources, advisories, OWASP materials, vendor documentation, technical communities, webinars, and relevant training.

Within a team, I would encourage knowledge sharing, practical demonstrations, retrospectives, and learning opportunities.

I would verify advice against reliable sources before applying it.

============================================================
PART 3: QUICK REVISION — KEY TERMS
============================================================

SAST:
Tests source code or supported code representations without running the application.

DAST:
Tests a running application from the outside.

Authenticated Scan:
Uses a configured login or session to test protected functionality.

False Positive:
A reported issue that evidence shows is not a real vulnerability in the stated context.

SQL Injection:
Untrusted input changes the meaning of a database query.

XSS:
Untrusted content executes as script in a user's browser.

Authentication:
Verifies identity.

Authorization:
Determines permitted actions and resources.

Least Privilege:
Give each account only the permissions it needs.

Retest:
Test again after a fix to verify the outcome.

CI/CD:
Automated build, test, and delivery/deployment workflow.

MFA:
Multi-factor authentication.

SSO:
Single sign-on.

CSRF:
An attack that can cause a user's browser to submit an unwanted action to an application where the user is authenticated.

CSP:
A browser-enforced policy that restricts the sources from which resources may be loaded.

HSTS:
A policy that tells browsers to use HTTPS for a specified period.

RBAC:
Role-based access control. Permissions are assigned to roles, and users are assigned appropriate roles.

TLS:
A protocol used to protect data in transit.

============================================================
PART 4: MOCK INTERVIEW PRACTICE
============================================================

QUESTION 1:

A Veracode DAST authenticated scan starts successfully, but the report shows only the login page. How would you investigate the problem and verify that it is fixed?

Structure your answer:

1. What would you check first?
2. How would you identify the root cause?
3. Who would you coordinate with?
4. How would you verify the fix?

QUESTION 2:

The scanner cannot authenticate to an application protected by MFA. How would you investigate the issue without weakening security controls?

QUESTION 3:

A developer claims that a DAST finding is a false positive. How would you validate the claim?

QUESTION 4:

A scan fails because the scanner cannot connect to the target application. What technical checks would you perform?

QUESTION 5:

You have multiple security findings with different severity levels. How would you prioritize remediation?

QUESTION 6:

How would you explain the difference between SAST and DAST to a nontechnical stakeholder?

QUESTION 7:

How could Python help improve the reliability and efficiency of recurring security scans?

QUESTION 8:

A developer fixes a vulnerability and asks you to close the finding. What would you do before closing it?

QUESTION 9:

You cannot resolve an access-related blocker yourself. How would you communicate and track it?

QUESTION 10:

Describe one real situation from your work using this structure:

- Situation: What was the problem?
- Responsibility: What was your role?
- Investigation: What did you check?
- Action: What did you do, and whom did you coordinate with?
- Result: What outcome did you personally verify?
- Learning: What would you do differently next time?

============================================================
FINAL INTERVIEW REMINDERS
============================================================

1. Understand the concept instead of memorizing every word.
2. Explain technical issues in simple, clear English.
3. Never claim you personally fixed a vulnerability if your role was only scan configuration or coordination.
4. Distinguish scan setup and execution from vulnerability assessment and remediation.
5. Do not invent project metrics, security findings, or achievements.
6. Explain why you would take an action, not just list tools.
7. For real-world scenarios, mention authorization, evidence, collaboration, and verification.
8. If you do not know an answer, explain what you know and how you would investigate safely.

END OF APPLICATION SECURITY ENGINEER INTERVIEW NOTES
