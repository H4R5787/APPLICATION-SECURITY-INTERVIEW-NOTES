# DOCUMENT 8: HR & BEHAVIORAL INTERVIEW GUIDE (PERSONALIZED ANSWERS)

**Candidate:** Harsh Vishwakarma  
**Current Employer:** Tata Consultancy Services (TCS)  
**Target Roles:** Application Security Engineer / Product Security Engineer

---

## 1. INTRODUCTORY & CAREER TRANSITION QUESTIONS

### Q1: Tell me about yourself.
* **30-Second Elevator Pitch:**
  > "I am an Application Security Engineer currently working at TCS in the cybersecurity domain. My core focus is on Dynamic Application Security Testing (DAST) for enterprise banking applications, Veracode platform onboarding, authenticated scan orchestration, and REST API testing. I specialize in bridging the gap between automated scanning and development teams—troubleshooting authentication blockers, eliminating false positives through manual Burp Suite validation, and writing Python automation to streamline our workflows. Beyond enterprise work, I actively practice offensive security on TryHackMe and was ranked in the Top 100 nationwide in TCS HackQuest Season 9. I’m looking to take on an Application Security Engineer role where I can contribute to deeper vulnerability assessments, DevSecOps pipeline integration, and product security."

---

### Q2: Why are you looking to change jobs from a stable company like TCS?
* **Model Answer:**
  > "TCS has provided me with a fantastic foundation in enterprise-scale processes, compliance rigor, and large-scale banking application architectures. However, in my current scope, my role is primarily centered around DAST enablement, scan configuration, and vulnerability coordination. I am eager to transition into a dedicated Application Security / Product Security team where I can be more hands-on with end-to-end security architecture, integrate SAST/DAST directly into CI/CD pipelines, conduct manual verification, and work closely with product developers to build secure-by-design software."

---

### Q3: What is your official notice period at TCS, and can it be negotiated?
* **Model Answer:**
  > "The standard contractual notice period at TCS is 90 days. However, depending on project release cycles and transition of my scan onboarding responsibilities, buy-out options or early release can often be formally discussed with project leadership upon receiving a confirmed offer. I will maintain complete transparency and strive for the earliest feasible transition."

---

### Q4: What are your salary expectations?
* **Model Answer:**
  > "My current CTC is structured according to TCS standards for my role. For my next career step, I am looking for a compensation package that aligns with industry standards for an Application Security Engineer with hands-on enterprise DAST, API security, and Python automation capabilities. I am open to discussing a competitive offer based on the overall role responsibilities and growth opportunities within your team."

---

## 2. SITUATIONAL & BEHAVIORAL SCENARIOS

### Q5: How do you handle a situation where a developer strongly pushes back and refuses to fix a vulnerability?
* **Model Answer:**
  > "I approach this through collaboration and empirical evidence rather than authority. When a developer pushes back—typically because they believe a finding is theoretical or low risk—I first review the finding details and reproduce it in an authorized non-production environment. 
  > 
  > Using Burp Suite, I capture the exact request, payload, and response to demonstrate practical exploitability, such as showing how unvalidated input reflects in the browser or bypasses client-side checks. I schedule a brief 15-minute call with the developer, show the reproduction steps, explain the specific business impact on customer data, and provide concrete code-level remediation guidance (e.g., demonstrating a parameterized query or output encoding function). By focusing on evidence and making the fix easy to implement, developers almost always agree to prioritize the remediation."

---

### Q6: Tell me about a time you faced a major blocker on a project and how you resolved it.
* **Model Answer:**
  > "During an onboarding cycle for a critical banking portal in Veracode, the automated authenticated scan kept terminating after five minutes, reporting only the login page. The application had recently implemented a dynamic SSO redirect with an anti-CSRF token embedded in a dynamic DOM element that the standard crawler failed to execute.
  > 
  > Rather than waiting or giving up, I investigated the issue using browser developer tools and proxy logs, identifying the exact step where the session was dropped. I authored a custom Selenium WebDriver script with explicit waits to handle the dynamic element, successfully generated the authenticated session, and verified that the landing page was reached. I converted this into a Veracode autologin macro, re-ran the scan, and achieved 100% crawl coverage across the protected application modules on schedule."

---

### Q7: Where do you see yourself in 3 to 5 years?
* **Model Answer:**
  > "In the next 3 to 5 years, I aim to grow into a Senior Application Security Engineer or DevSecOps Lead. I want to achieve deep subject matter expertise in threat modeling, cloud-native application security (Kubernetes and container security), and automated security gating in CI/CD pipelines. Long term, I want to be someone who not only secures running applications but actively shapes secure software development architectures across an organization."
