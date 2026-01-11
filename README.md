# Analyzing-Broken-Access-Control-BAC-Vulnerabilities-in-Web-Applications
Broken Access Control occurs when an application fails to properly enforce restrictions on what authenticated users are allowed to do. In this project, I analyzed how a lack of server-side authorization checks can lead to unauthorized data access, privilege escalation, and sensitive data exposure.

# About Me
Information Systems graduate, currently transitioning to Cyber Security. Previously served as a Technician at Oil and Gas Industry (Pertamina Patra Niaga vendor), where I developed a highly disciplined approach to managing critical infrastructure compliance and 24/7 reliability. Currently formalizing expertise through an intensive cybersecurity bootcamp at Dibimbing, I am eager to apply this technical foundation and structured approach to a security operations role.

# 1. Background
In the modern business landscape, web based applications have become indispensable assets. As companies undergo digital transformation to digitize their products and services, the attack surface for cyber threats expands accordingly. While web development technologies have advanced rapidly, so too have the sophistication of cyber-attacks. For business owners, these threats represent a significant risk that can lead to:

- Sensitive Data Theft: Exposure of proprietary information and user data.
- Financial Loss: Direct costs associated with breaches and potential regulatory fines.
- Reputational Damage: Loss of trust from clients and stakeholders, which can be irreversible.

To mitigate these risks, Penetration Testing is employed as a proactive security measure. This method involves simulating real world cyber attacks in an ethical and controlled environment. The primary objective is to identify and document vulnerabilities before malicious actors can exploit them. By conducting these tests, organizations can implement remediation strategies to minimize their threat exposure to the lowest possible level. For this assessment, the target application is the Damn Vulnerable Web Application (DVWA), hosted locally on a Kali Linux Virtual Machine.

# 2. Methodology
This assessment was conducted using a Gray Box testing methodology. In this approach, the penetration tester is provided with limited information about the target system, specifically access credentials. This method simulates an attacker who may have gained internal access or a malicious insider, allowing for a more efficient evaluation of the application’s internal security controls and logic. The testing followed the OWASP (Open Web Application Security Project) Testing Guide. This ensures a comprehensive and systematic evaluation of common web vulnerabilities, specifically targeting A01:2021 – Broken Access Control. By adhering to this framework, the assessment ensures that all critical attack vectors are thoroughly examined. To provide a clear understanding of the severity of each finding, all vulnerabilities identified within DVWA were analyzed using the Common Vulnerability Scoring System (CVSS) v3.1.

# 3. Scope
The scope of this penetration testing engagement is strictly limited to the locally hosted instance of the Damn Vulnerable Web Application (DVWA). This assessment is a targeted engagement rather than a general audit. The testing activities are exclusively focused on identifying and exploiting vulnerabilities related to Broken Access Control (OWASP A01:2021).

# 4. Details Finding 
1. BAC (Low Level)
- Severity: Medium: CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:L/I:L/A:N
- Vulnerability Impact: Authorization Bypass - an attacker can access sensitive resources without needing an account.
- Host Affected: http://127.0.0.1/dvwa/vulnerabilities/authbypass/
- Endpoint Affected: /dvwa/vulnerabilities/authbypass/get_user_data.php
- POC: At a low security level, using a standard user account (non-admin), an attacker utilizes Burp Suite to inspect the page's URLs. Burp Suite identifies a GET method directing to an application resource. By accessing /dvwa/vulnerabilities/authbypass through the link as a regular user, the attacker successfully gains access to the admin page despite only having a user role.
 <p align="center">
  <img width="500" height="550" alt="image" src="https://github.com/user-attachments/assets/37248b47-626b-45f6-bcb9-2610775dedf3" />
  <br>
 </p>

- Recommendation: Implement Deny by Default, Role Based Access Control.

2. BAC (Medium Level) 
- Severity: Medium: CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:C/C:L/I:N/A:N
- Vulnerability Impact: Data Leakage - An attacker can retrieve sensitive database information.
- Host Affected: http://127.0.0.1/dvwa/vulnerabilities/authbypass/
- Endpoint Affected: /dvwa/vulnerabilities/authbypass/get_user_data.php
- POC: On DVWA level medium, the attacker attempts to replicate the attack used in the low level but receives an 'unauthorized' response. Using Burp Suite to inspect the URL content, the attacker tries changing the request method to POST, which also fails. Upon further inspection of the web resources, the attacker notices that user data is not present as it was in the low level. The attacker then attempts to append get_user_data.php to the resource path. As a result, the application responds with user data and passwords, which can be exploited to gain access to both regular and admin accounts.
   <p align="center">
  <img width="500" height="387" alt="image" src="https://github.com/user-attachments/assets/c4b41101-2349-47fd-b112-ec8dae25ec20" />
  <br>
 </p>

- Recommendation: Use strong hashing algorithms for data (Bcrypt/Argon2), Use a routing system to hide the file structure.

3. BAC (High Level)
-  Severity: High: CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:C/C:H/I:H/A:N
- Vulnerability Impact: Privilege Escalation & Session Hijacking - An attacker can steal user access rights and use them to access the application without valid authentication.
- Host Affected: http://127.0.0.1/dvwa/vulnerabilities/authbypass/
- Endpoint Affected: /dvwa/vulnerabilities/authbypass/get_user_data.php
- POC: At the high security level, DVWA implements robust security measures; attempts to use low and medium-level techniques result in 'fail' and 'access denied' responses. However, based on the provided DVWA documentation, an attacker can still attempt to analyze the web responses. The attacker first captures a data modification request at the medium level using Burp Suite. After increasing the security level to high, the attacker identifies the session cookies of a standard user (non-admin). By copying these cookies and injecting them into the previously captured medium-level request while setting the security level to high, the server responds with 'result:ok'. This allows the attacker to successfully execute the attack.
<p align="center">
  <img width="500" height="363" alt="image" src="https://github.com/user-attachments/assets/2ef42ddd-905c-461a-8b8a-4ae87038422f" />
  <br>
 </p>

-  Recommendation: Implement Server-Side Session Authorization Verification, Implement HttpOnly and Secure Attributes.

# 5. Mitigation Recommendations
- Implement the Principle of Least Privilege (PoLP)
- Enforce "Deny by Default" Policy
- Continuous Permission Validation
- Transition to Attribute and Relationship-Based Access Control (ABAC/ReBAC)
 
