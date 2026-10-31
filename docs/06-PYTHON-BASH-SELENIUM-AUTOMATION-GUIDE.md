# DOCUMENT 6: PYTHON, BASH, SELENIUM & SECURITY AUTOMATION GUIDE

**Focus:** Enterprise Security Automation, API Orchestration & Login Automation

---

## 1. PYTHON SECURITY AUTOMATION: VERACODE REST API INTEGRATION

In enterprise AppSec environments, engineers automate scan triggering, status checks, and report extraction using Python's `requests` library and HMAC authentication.

### Practical Script: Automated Scan Polling & Flaw Report Extraction
```python
import os
import sys
import time
import requests

# Load credentials securely from environment variables (Never hardcode secrets!)
API_BASE_URL = "https://api.veracode.com/was/v1"
API_ID = os.environ.get("VERACODE_API_KEY_ID")
API_SECRET = os.environ.get("VERACODE_API_KEY_SECRET")

def get_auth_headers():
    """Returns headers with token or HMAC authorization."""
    return {
        "Accept": "application/json",
        "User-Agent": "AppSec-Automation-Pipeline/1.0"
    }

def check_scan_status(analysis_id):
    """Polls Veracode DAST analysis status."""
    url = f"{API_BASE_URL}/analyses/{analysis_id}"
    try:
        response = requests.get(url, headers=get_auth_headers(), timeout=15)
        response.raise_for_status()
        data = response.json()
        return data.get("status")
    except requests.exceptions.RequestException as e:
        print(f"[!] Network or API error checking status: {e}", file=sys.stderr)
        return "ERROR"

def poll_scan_until_complete(analysis_id, interval_sec=60, max_retries=60):
    """Polls scan until terminal state (COMPLETED, FAILED, CANCELLED)."""
    print(f"[*] Monitoring DAST Analysis ID: {analysis_id}")
    for attempt in range(max_retries):
        status = check_scan_status(analysis_id)
        print(f"[{attempt + 1}/{max_retries}] Current Scan Status: {status}")
        
        if status in ["COMPLETED", "PUBLISHED"]:
            print("[+] Scan completed successfully. Proceeding to fetch report.")
            return True
        elif status in ["FAILED", "CANCELLED", "ERROR"]:
            print(f"[-] Scan terminated with non-success status: {status}")
            return False
            
        time.sleep(interval_sec)
    print("[-] Scan monitoring timed out.")
    return False

if __name__ == "__main__":
    test_analysis_id = "sample-analysis-uuid-1234"
    # poll_scan_until_complete(test_analysis_id)
```

---

## 2. SELENIUM WEBDRIVER: AUTOMATED LOGIN WORKFLOW (DAST AUTOLOGIN)

DAST scanners often fail on single-page applications (SPAs) or multi-step logins. A Selenium automation script simulates user interaction, handles dynamic DOM elements, and preserves session cookies.

### Practical Script: Headless Chrome Selenium Login Macro
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

def automate_appsec_login(target_login_url, username, password):
    """Executes an automated login and returns the authenticated session cookies."""
    chrome_options = Options()
    chrome_options.add_argument("--headless=new")
    chrome_options.add_argument("--disable-gpu")
    chrome_options.add_argument("--no-sandbox")
    chrome_options.add_argument("--window-size=1920,1080")
    
    driver = webdriver.Chrome(options=chrome_options)
    wait = WebDriverWait(driver, 15)
    
    try:
        print(f"[*] Navigating to {target_login_url}")
        driver.get(target_login_url)
        
        # Step 1: Wait for username input and enter credentials
        username_field = wait.until(EC.visibility_of_element_located((By.ID, "usernameInput")))
        username_field.clear()
        username_field.send_keys(username)
        
        # Step 2: Enter password
        password_field = wait.until(EC.visibility_of_element_located((By.ID, "passwordInput")))
        password_field.clear()
        password_field.send_keys(password)
        
        # Step 3: Click login button
        login_btn = wait.until(EC.element_to_be_clickable((By.XPATH, "//button[@type='submit']")))
        login_btn.click()
        
        # Step 4: Explicit wait for landing page element confirming successful login
        wait.until(EC.presence_of_element_located((By.ID, "userDashboardNavbar")))
        print("[+] Login assertion succeeded: Dashboard element discovered.")
        
        # Step 5: Extract session cookies for DAST scanner
        cookies = driver.get_cookies()
        session_cookie_dict = {c['name']: c['value'] for c in cookies}
        print(f"[+] Extracted {len(cookies)} cookies: {list(session_cookie_dict.keys())}")
        return session_cookie_dict
        
    except Exception as e:
        print(f"[-] Login automation failed: {e}")
        driver.save_screenshot("login_failure.png")
        return None
    finally:
        driver.quit()
```

---

## 3. BASH SHELL AUTOMATION: PRE-SCAN CONNECTIVITY & TLS VERIFICATION

Before kicking off a multi-hour DAST scan, running a pre-flight connectivity script prevents failed scans caused by DNS or firewall blockers.

### Practical Bash Script: `preflight_scan_check.sh`
```bash
#!/usr/bin/env bash
# Pre-Flight DAST Target Check
# Usage: ./preflight_scan_check.sh <target_url>

set -euo pipefail

TARGET_URL="${1:-}"

if [[ -z "$TARGET_URL" ]]; then
    echo "Usage: $0 https://target-app.internal.domain.com"
    exit 1
fi

echo "================================================="
echo " AppSec Pre-Flight Reachability & TLS Check"
echo " Target: $TARGET_URL"
echo "================================================="

DOMAIN=$(echo "$TARGET_URL" | awk -F[/:] '{print $4}')

# 1. DNS Resolution Check
echo -n "[*] Checking DNS resolution for $DOMAIN... "
if host "$DOMAIN" > /dev/null 2>&1; then
    echo "RESOLVED"
else
    echo "FAILED (DNS lookup failed. Check internal DNS / VPN settings)"
    exit 2
fi

# 2. Port 443 Reachability (TCP Connect)
echo -n "[*] Checking TCP reachability on port 443... "
if nc -z -w 5 "$DOMAIN" 443 > /dev/null 2>&1; then
    echo "REACHABLE"
else
    echo "BLOCKED (Port 443 unreachable. Check firewall / ISE agent routing)"
    exit 3
fi

# 3. HTTP Status and Redirect Evaluation
echo -n "[*] Checking HTTP response status and headers... "
HTTP_RESPONSE=$(curl -k -s -o /dev/null -w "%{http_code}" --connect-timeout 10 "$TARGET_URL")
echo "HTTP $HTTP_RESPONSE"

# 4. TLS Certificate Expiry Check
echo -n "[*] Checking TLS Certificate validity... "
EXPIRY_DATE=$(echo | openssl s_client -servername "$DOMAIN" -connect "$DOMAIN:443" 2>/dev/null | openssl x509 -noout -enddate 2>/dev/null | cut -d= -f2 || echo "UNKNOWN")
echo "Expires on: $EXPIRY_DATE"

echo "[+] Target verified. Ready for DAST scan onboarding."
exit 0
```

---

## 4. TOP PYTHON & SCRIPTING INTERVIEW QUESTIONS

### Q1: How do you handle authentication credentials securely in Python automation scripts?
* **Short Answer:** Never hardcode credentials in source code. Store them in enterprise secret vaults (e.g., HashiCorp Vault, AWS Secrets Manager, CyberArk) or pass them at runtime via strictly scoped environment variables.
* **Technical Detail:** Avoid logging secrets in stdout/stderr, ensure files containing environment variables (`.env`) are added to `.gitignore`, and use least-privilege service accounts with automated key rotation.

### Q2: What is the difference between `is` and `==` in Python?
* **Answer:** `==` checks for **equality of values** (do the two objects hold the same content?), whereas `is` checks for **identity** (do both variables reference the exact same memory location?). In security scripting, use `==` for comparing string tokens/passwords and `is` for comparing singleton constants like `is None` or `is True`.

### Q3: How do you parse and filter high/critical findings from a 50MB JSON/XML vulnerability report in Python?
* **Answer:** For JSON, use the `json` module (or `ijson` for streaming very large files without running out of memory). Iterate through the `findings` array and filter where `severity in ["High", "Very High", "Critical"]` and `status == "OPEN"`. For XML, use `defusedxml.ElementTree` to parse safely without exposing the parser to XML External Entity (XXE) injection attacks.
