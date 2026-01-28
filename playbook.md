🛡️ SOC Playbook – OWASP A01: Broken Access Control

Playbook Type: Detection & Triage
Audience: SOC Tier 1 Analysts
OWASP Category: A01 – Broken Access Control
Last Updated: 01/27/26

📌 Purpose
<details> <summary><strong>Expand</strong></summary>
This playbook provides a standardized workflow for detecting, triaging, investigating, and escalating incidents related to Broken Access Control, where users or systems attempt to access resources, data, or functions outside their authorized permissions.
The goal is to ensure:
- Consistent Tier 1 response
- Accurate impact assessment
- Proper escalation to Tier 2 / AppSec
- Clear documentation and handoff
</details>

🎯 Scope
<details> <summary><strong>Expand</strong></summary>
AThis playbook applies to:
Public-facing web applications
Internal web portals
APIs (REST / GraphQL)
SaaS platforms
Cloud-hosted services
Out of Scope:
Authentication failures without authorization bypass
Password brute force (covered in separate playbook)
Insider misuse confirmed by HR or Legal
</details>
🚨 Alert Overview
<details> <summary><strong>Expand</strong></summary>
Common Alert Names
Unauthorized Resource Access
IDOR Attempt Detected
Privilege Escalation via Web App
Forbidden Endpoint Access
API Scope Violation
Severity Guidelines
Medium: Attempted or blocked access
High: Successful unauthorized access or repeated attempts
Related Frameworks
OWASP A01 – Broken Access Control
MITRE ATT&CK:
T1190 – Exploit Public-Facing Application
T1068 – Privilege Escalation (context dependent)

</details>
🧠 Common Access Control Failure Scenarios
<details> <summary><strong>Expand</strong></summary>
Insecure Direct Object Reference (IDOR)

User accesses /api/orders/12345

Modifies ID to /api/orders/12346

Gains access to another user’s data

Privilege Escalation

Standard user accessing admin-only endpoints

Access to user management, exports, or configuration

HTTP Method Abuse

DELETE or PUT requests allowed where only GET should exist

API Token Scope Abuse

Token accessing endpoints outside its defined scope

</details>
📥 Detection Sources
<details> <summary><strong>Expand</strong></summary>

Log Sources

Web server logs (Nginx, Apache, IIS)

Application audit logs

API gateway logs

Cloud provider logs (CloudTrail, Azure Activity Logs)

Security Controls

SIEM correlation rules

Web Application Firewall (WAF)

API security tooling

IAM monitoring alerts

</details>
⏱️ Initial Triage (First 5 Minutes)
<details> <summary><strong>Expand</strong></summary>
Key Questions

Is the request authenticated?

Is the user authorized for the resource?

Was access blocked or successful?

Is this a single event or part of a pattern?

Data Points to Capture

Timestamp

Username / User ID

Source IP

HTTP method

Target endpoint

Response status code (401 / 403 / 200)

</details>
🔍 Investigation Workflow
<details> <summary><strong>Expand</strong></summary>
Step 1: Validate the Alert

Confirm the event exists in logs

Verify response code

Identify whether access succeeded

⚠️ Success matters more than intent

Step 2: Identify the Actor

Human user vs service account

Internal vs external source

User role and privilege level

Known user behavior vs anomalous

Step 3: Scope the Activity

Search for:

Additional endpoints accessed

Sequential object ID requests

Repeated 403 → 200 patterns

Activity across multiple users or sessions

Step 4: Impact Assessment

Determine:

Was sensitive data accessed?

Were admin functions executed?

Was data modified or deleted?

Are multiple users or tenants affected?

</details>
🧭 Decision Logic
<details> <summary><strong>Expand</strong></summary>
Close as Benign / Expected if:

Access was blocked

User role legitimately changed

Known vulnerability scanner or QA activity

Single request with no follow-up

Escalate to Tier 2 / AppSec if:

Unauthorized access was successful

Another user’s data was accessed

Admin or privileged functions were reachable

Enumeration or automation detected

</details>
📤 Escalation Requirements
<details> <summary><strong>Expand</strong></summary>

When escalating, include:

User ID / account involved

Source IP + GeoIP

Affected endpoint(s)

HTTP methods used

Response codes

Timeline of activity

Relevant log samples

Analyst assessment (attempted vs successful)

This prevents Tier 2 from repeating Tier 1 work.

</details>
🛑 Containment & Response (Tier 1 Role)
<details> <summary><strong>Expand</strong></summary>

Tier 1 does not remediate, but may recommend:

Temporary account suspension

Token revocation

WAF blocking or rate limiting

AppSec code review

IAM permission review

All recommendations must be documented.

</details>
📝 Documentation Standards
<details> <summary><strong>Expand</strong></summary>

Incident Notes Must Include

Alert ID

Detection source

Summary of findings

Impact assessment

Escalation decision

Actions taken

Time spent

Clear documentation is mandatory for audit and handoff.

</details>
🔚 Closure Criteria
<details> <summary><strong>Expand</strong></summary>

An incident may be closed when:

Activity is confirmed benign or blocked

No unauthorized access occurred

Escalation was completed and acknowledged

Documentation is finalized

</details>
📚 References
<details> <summary><strong>Expand</strong></summary>

OWASP Top 10 – A01: Broken Access Control

MITRE ATT&CK Framework

NIST Incident Response Lifecycle

</details>
