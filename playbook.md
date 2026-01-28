# 🛡️ SOC Playbook – OWASP A01: Broken Access Control

**Playbook Type:** Detection & Triage  
**Audience:** SOC Tier 1 Analysts  
**OWASP Category:** A01 – Broken Access Control  
**Last Updated:** 2026-01-27  

---

## Purpose

<details>
<summary><strong>Expand</strong></summary>

This playbook provides a standardized workflow for detecting, triaging, investigating, and escalating incidents related to **Broken Access Control**, where users or systems attempt to access resources, data, or functions outside their authorized permissions.

The objectives are to ensure:
- Consistent Tier 1 response
- Accurate impact assessment
- Proper escalation to Tier 2 / AppSec
- Clear documentation and handoff

</details>

---

## Scope

<details>
<summary><strong>Expand</strong></summary>

**In Scope**
- Public-facing web applications
- Internal web portals
- APIs (REST / GraphQL)
- SaaS platforms
- Cloud-hosted services

**Out of Scope**
- Authentication failures without authorization bypass
- Password brute-force attempts
- Insider misuse confirmed by HR or Legal

</details>

---

## Alert Overview

<details>
<summary><strong>Expand</strong></summary>

**Common Alert Names**
- Unauthorized Resource Access
- Insecure Direct Object Reference (IDOR)
- Privilege Escalation via Web Application
- Forbidden Endpoint Access
- API Scope Violation

**Severity Guidelines**
- Medium: Attempted or blocked access
- High: Successful unauthorized access or repeated attempts

**Related Frameworks**
- OWASP Top 10 – A01: Broken Access Control  
- MITRE ATT&CK:
  - T1190 – Exploit Public-Facing Application
  - T1068 – Privilege Escalation (context dependent)

</details>

---

## Common Access Control Failure Scenarios

<details>
<summary><strong>Expand</strong></summary>

### Insecure Direct Object Reference (IDOR)
A user modifies an object identifier (e.g., record ID) in a request and gains access to another user’s data.

### Privilege Escalation
A standard user accesses functionality intended only for administrative or elevated roles.

### HTTP Method Abuse
Use of unauthorized HTTP methods (PUT, DELETE, PATCH) on endpoints intended to be read-only.

### API Token Scope Abuse
An API token accesses endpoints outside its assigned permission scope.

</details>

---

## Detection Sources

<details>
<summary><strong>Expand</strong></summary>

**Log Sources**
- Web server logs (Nginx, Apache, IIS)
- Application audit logs
- API gateway logs
- Cloud provider logs (AWS CloudTrail, Azure Activity Logs)

**Security Controls**
- SIEM correlation rules
- Web Application Firewall (WAF)
- API security tooling
- Identity and Access Management (IAM) monitoring

</details>

---

## Initial Triage (First 5 Minutes)

<details>
<summary><strong>Expand</strong></summary>

**Key Questions**
1. Is the request authenticated?
2. Is the user authorized for the resource?
3. Was access blocked or successful?
4. Is the activity isolated or repeated?

**Critical Data Points**
- Timestamp
- User ID / Username
- Source IP address
- HTTP method
- Target endpoint
- Response status code (401 / 403 / 200)

</details>

---

## Investigation Workflow

<details>
<summary><strong>Expand</strong></summary>

### Step 1: Validate the Alert
- Confirm the activity exists in logs
- Validate response codes
- Identify whether access succeeded

> Success matters more than intent.

---

### Step 2: Identify the Actor
- Human user vs service account
- Internal vs external origin
- Assigned role and permission level
- Historical behavior comparison

---

### Step 3: Scope the Activity
Investigate for:
- Additional endpoints accessed
- Sequential object ID enumeration
- Repeated access attempts across sessions
- Similar activity from other accounts

---

### Step 4: Impact Assessment
Determine whether:
- Sensitive data was accessed
- Administrative functions were reached
- Data was modified or deleted
- Multiple users or tenants were affected

</details>

---

## Decision Logic

<details>
<summary><strong>Expand</strong></summary>

### Close as Benign / Expected
- Access was blocked (403)
- Legitimate role or permission change
- Known vulnerability scanner or QA activity
- Single request with no follow-up behavior

---

### Escalate to Tier 2 / AppSec
- Unauthorized access was successful
- User accessed another user’s data
- Privileged or administrative functions were reachable
- Enumeration or automation behavior detected

</details>

---

## Escalation Requirements

<details>
<summary><strong>Expand</strong></summary>

Escalation packages must include:
- User ID / account details
- Source IP and GeoIP
- Affected endpoint(s)
- HTTP methods used
- Response codes
- Timeline of activity
- Relevant log excerpts
- Analyst assessment (attempted vs successful)

</details>

---

## Containment & Response (Tier 1 Responsibilities)

<details>
<summary><strong>Expand</strong></summary>

Tier 1 analysts do not remediate but may recommend:
- Temporary account suspension
- API token revocation
- WAF blocking or rate limiting
- IAM permission review
- Application security code review

All recommendations must be documented.

</details>

---

## Incident Documentation Standards

<details>
<summary><strong>Expand</strong></summary>

Accurate and complete documentation is mandatory for auditability, handoff to Tier 2, and post-incident review.  
All investigation steps must be traceable to **specific evidence sources**.

---

### Required Fields

#### Alert ID
- Unique alert or ticket identifier
- SIEM rule name or detection source
- Time the alert was generated

---

#### Detection Source
Document *exactly* where the alert originated:
- SIEM correlation rule
- WAF alert
- Application security alert
- API gateway alert
- Cloud provider alert (e.g., CloudTrail)
- Manual analyst detection

---

### Summary of Findings (Critical Section)

The **Summary of Findings** must clearly answer:
> *What happened, how we know, and where the evidence came from.*

This section should be written in **plain, factual language** and reference **multiple data sources** where applicable.

#### Include the Following Evidence Sources (as applicable):

##### 1. Application Logs
- Endpoint(s) accessed
- User ID / account involved
- Authorization checks performed
- Application response (allowed / denied)
- Error messages or stack traces (if any)

Example:
> Application logs show user `user123` accessed `/api/admin/export` and received HTTP 200 despite lacking admin role.

---

##### 2. Web Server / API Gateway Logs
- HTTP method used
- Request URI
- Response status codes
- Object identifiers or parameters modified
- Request frequency and timing

Example:
> API gateway logs confirm repeated GET requests to sequential object IDs within a 3-minute window.

---

##### 3. Authentication & Authorization Logs
- Authentication success or failure
- Role or permission assignments
- Token scopes or claims
- Recent permission changes

Example:
> IAM logs confirm the user authenticated successfully but did not have permissions for the requested resource.

---

##### 4. SIEM Correlation & Query Results
- Queries executed by analyst
- Time ranges searched
- Correlated events across sources
- Supporting evidence of pattern or repetition

Example:
> SIEM queries correlated application logs and API gateway logs showing unauthorized access across multiple endpoints.

---

##### 5. Network & Source Context
- Source IP address
- GeoIP information
- ASN or hosting provider
- Known corporate VPN or third-party service

Example:
> Requests originated from an external IP not associated with corporate infrastructure.

---

##### 6. WAF / Security Control Outcomes (if applicable)
- Whether the request was blocked or allowed
- Rule triggered
- Bypass or misconfiguration indicators

Example:
> WAF logs show the request was allowed due to missing authorization enforcement, not blocked by rule.

---

#### Summary Writing Guidance
- Avoid speculation
- Avoid assumptions about intent
- State **only what evidence confirms**
- Use neutral language

Good:
> Evidence indicates unauthorized access to another user’s data occurred.

Bad:
> The user was attempting to hack the system.

---

### Impact Assessment
Clearly document:
- Data accessed (type and sensitivity)
- Functions executed (read, modify, delete)
- Number of affected users or records
- Potential business or compliance impact

---

### Escalation Decision
- Escalated or closed
- Reason for decision
- Team notified (Tier 2, AppSec, IR)
- Timestamp of escalation

---

### Analyst Actions Taken
- Queries executed
- Evidence collected
- Stakeholders notified
- Recommendations made

---

### Time Tracking
- Investigation start time
- Investigation end time
- Total analyst time spent

This data is often used for metrics and staffing analysis.

</details>


---

## Closure Criteria

<details>
<summary><strong>Expand</strong></summary>

An incident may be closed when:
- Activity is confirmed benign or blocked
- No unauthorized access occurred
- Escalation was completed and acknowledged
- Documentation is finalized

</details>

---

## References

<details>
<summary><strong>Expand</strong></summary>

- OWASP Top 10 – A01: Broken Access Control  
- MITRE ATT&CK Framework  
- NIST Incident Response Lifecycle

</details>

---

## Analyst Notes

<details>
<summary><strong>Expand</strong></summary>

This playbook emphasizes authorization failures rather than authentication issues.  
Tier 1 focus should remain on **confirming access violations, assessing impact, and escalating with evidence**, not remediation.

</details>
