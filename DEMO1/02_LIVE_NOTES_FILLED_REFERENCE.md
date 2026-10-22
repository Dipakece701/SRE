# LIVE NOTES REFERENCE — Fully Filled Example

> **HOW TO USE THIS FILE:**
> Keep this open on your side (private screen) as a cheat sheet while you type notes on your shared screen.
> It shows you the exact words, timestamps, and numbers to type into `02_LIVE_NOTES_TEMPLATE.md` during the call.
> It is aligned with your swapped roles: **Maurice = Incident Commander**, **Constantin = Backup Responder**.

---

# SRE Tabletop Exercise — Live Meeting Notes

- **Exercise Date:** Thursday, October 22, 2026
- **Time:** 10:15 AM to 11:15 AM (60 minutes)
- **Governing Procedure:** Incident Response Procedure v1.0
- **Target Environment:** DEMO (dm0)
- **Work Item Reference:** AB#34970 (SOC 2 CC7 Audit Evidence)

---

## 1. Attendees and Roles

| Role | Name | Contact |
|---|---|---|
| **Incident Commander** | Maurice Kennedy | Slack: `@Maurice Kennedy` |
| **Primary Responder (Facilitator)** | Dipak Singh | Slack: `@Dipak Singh` |
| **Backup Responder** | Constantin Pricochi | Slack: `@Constantin Pricochi` |
| **Investigation Engineer** | Aditya Mehta | Slack: `@Aditya Mehta` |
| **Security Escalation Lead** | Maurice Kennedy & Constantin Pricochi | Slack: `#security-ops` |

---

## 2. Scenario A: App Gateway Error Surge (Reliability Incident)

**Incident ID:** INC-20261022-DM0-APIM  
**Incident Record (Azure DevOps ticket):** [New Child Task under AB#34970 - Created live during meeting]  
**Severity Level:** Sev 2 (Major Service Degradation)  

### Who responded to the alert?

- **Responded by:** Dipak Singh (Primary Responder)
- **Time of response:** 10:20 UTC (2 minutes after detection)
- **How they responded:** Added 👀 emoji reaction and posted acknowledgment in `#helios-demo-alerts` Slack channel
- **Backup responder (if Primary did not respond):** Constantin Pricochi (would step in if no ack within 15 mins)

### Timeline of Events:

| Time (UTC) | What Happened | Who Did It |
|---|---|---|
| 10:18 | Alert fired: `helios-dm0-gateway-failure-rate` (42% failure rate over 15m) | System (automated) |
| 10:20 | Alert acknowledged in Slack; triage bridge opened | Dipak Singh |
| 10:22 | Incident record AB#34974 created in Azure DevOps under `Helios\QRE` | Dipak Singh |
| 10:32 | Diagnostic KQL query run in `helios-dm0-logs`; isolated issue | Aditya Mehta |
| 10:45 | Incident Commander authorized rollback of `ontology-event-processor` | Maurice Kennedy |
| 10:47 | Rollback executed to previous known-good deployment digest via GitOps | Constantin Pricochi |
| 10:50 | 4-point recovery checklist executed and verified | Dipak Singh |

### Root Cause Found:

- **Which service failed?** `ontology-event-processor` container
- **What type of error?** Upstream HTTP 504 gateway timeout (requests exceeding 30 seconds); `plan-narration` remained healthy
- **Was the failure isolated to one service or widespread?** Isolated to single service (`ontology-event-processor`)

### Recovery Verification (4-Point Checklist):

- [x] **Metric check:** Error rate dropped from 42% to 0.08% and stayed stable for > 15 minutes.
- [x] **Health check:** Health endpoint `GET /ontology-event-processor/health` returned HTTP 200 OK in 48ms.
- [x] **Log check:** Diagnostic query confirmed 0 gateway 504 timeout errors in the last 10 minutes.
- [x] **Sign-off:** Recovery evidence posted to Slack. Incident record marked as Resolved on the new child task.

---

## 3. Scenario B: Key Vault Security Alert (Security Incident)

**Incident ID:** INC-20261022-DM0-BACKEND-KV-SEC  
**Incident Record (Azure DevOps ticket):** [New Child Task under AB#34970 - Created live during meeting]  
**Severity Level:** Sev 1 (Critical Security / Credential Compromise)  

### Who responded to the alert?

- **Responded by:** Dipak Singh (Primary Responder)
- **Time of response:** 10:27 UTC (2 minutes after alert)
- **Escalated to:** Maurice Kennedy and Constantin Pricochi
- **Reason for escalation:** Unauthorized Key Vault secret access detected; mandatory joint escalation under Procedure v1.0

### Timeline of Events:

| Time (UTC) | What Happened | Who Did It |
|---|---|---|
| 10:25 | Security alert fired from Microsoft Defender for Cloud | System (automated) |
| 10:27 | Alert acknowledged in Slack; security incident bridge activated | Dipak Singh |
| 10:28 | Joint security escalation triggered to IC and Infra Lead | Dipak Singh |
| 10:29 | Incident record created in Azure DevOps tagged `security`, `sev-1` as Child Task | Dipak Singh |
| 10:35 | Diagnostic query in `AzureDiagnostics` identified leaked Service Principal | Aditya Mehta |
| 10:42 | Compromised Service Principal credential disabled and deleted in Entra ID | Constantin Pricochi |
| 10:43 | Key Vault firewall updated to restrict public access to trusted VNets | Dipak Singh |
| 10:44 | Secret `redis-cache-connection` rotated; dependent workloads restarted | Dipak Singh |
| 10:52 | 4-point security recovery checklist executed and verified | Dipak Singh |
| 10:55 | Formal security sign-off and closure approved | Maurice Kennedy |

### Attacker Details:

- **Attacker IP address:** `198.51.100.44`
- **Number of unauthorized attempts:** 18 attempts (HTTP 403 Forbidden) within 5 minutes
- **Targeted secret name:** `redis-cache-connection`
- **How the credential was compromised:** Leaked/expired Service Principal credential attempted from untrusted external IP

### Containment Actions Taken:

- [x] Compromised Service Principal credential disabled and deleted in Entra ID.
- [x] Key Vault firewall restricted to trusted internal networks only.
- [x] Targeted secret rotated (new password generated).
- [x] Dependent services restarted with the new secret.

### Recovery Verification (4-Point Checklist):

- [x] **Audit log check:** Confirmed 0 requests from attacker IP (`198.51.100.44`) in the last 30 minutes.
- [x] **Service health check:** Legitimate services authenticated with new secret via Workload Identity (HTTP 200 OK).
- [x] **Defender status:** Microsoft Defender marked incident as *Mitigated — Credential Rotated & Network Isolated*.
- [x] **Security sign-off:** Maurice Kennedy reviewed audit evidence and approved closure at 10:55 UTC.

### Additional Security Checks (if discussed):

- [x] Checked if the attacker IP hit any other Azure resources: Zero attempts found in NSG/Firewall logs.
- [x] Checked Entra ID sign-in logs for the compromised Service Principal: No access to other enterprise apps found.
- [x] Checked threat intelligence feeds for the attacker IP: IP noted in threat feeds; blocked at perimeter.
- [x] CISO notification required? Evaluated with Incident Commander; DEMO environment isolation confirmed, internal security escalation logged.

---

## 4. What Went Well

1. Fast initial response time: both alerts were acknowledged in Slack within 2 minutes of firing.
2. Clear team role separation: no ambiguity between Incident Commander (Maurice), Backup (Constantin), and Primary Responder (Dipak).
3. Evidence-based recovery: resolution was validated with KQL diagnostic queries and synthetic health checks rather than assumptions.
4. Seamless security containment: rogue credential disabled and Key Vault firewall isolated in under 15 minutes from alert.

---

## 5. Gaps Found and Action Items

| # | Action Item | Who is responsible? | Target date |
|---|---|---|---|
| 1 | Document SRE on-call rotation, backup rules, and escalation matrix in team Wiki | Maurice Kennedy | Sprint 2 (Nov 06, 2026) |
| 2 | Publish formal Incident Severity Classification Rubric with quantitative Sev 1/2 thresholds | Dipak Singh | Sprint 2 (Nov 06, 2026) |
| 3 | Embed runbook deep links and pre-populated KQL query links into Slack alert cards | Aditya Mehta | Sprint 2 (Nov 06, 2026) |
| 4 | Automate Key Vault secret rotation via Event Grid + Functions and add public network deny alert | Constantin Pricochi | Sprint 2 (Nov 06, 2026) |
| 5 | Establish external customer communication and status page notification protocol for Sev 1 incidents | Dipak Singh | Sprint 2 (Nov 06, 2026) |

---

## 6. Customer and Stakeholder Communication

- **Was customer communication needed for either scenario?** No, incidents occurred on DEMO environment (`dm0`). In a production equivalent, Sev 1 would trigger customer notification.
- **Who is responsible for customer notifications?** Incident Commander (Maurice Kennedy) coordinates with Product/Support lead.
- **Do we have a status page?** Identified as a gap to address in Action Item 5.
- **Action item to improve this?** Logged as Action Item #5 above.

---

## 7. Sign-Off

By signing below, participants confirm they attended and participated in the
SRE Tabletop Exercise for SOC 2 CC7 audit compliance.

| Name | Role | Confirmed (Yes/No) |
|---|---|---|
| Maurice Kennedy | Incident Commander | Yes |
| Constantin Pricochi | Backup Responder | Yes |
| Aditya Mehta | Investigation Engineer | Yes |
| Dipak Singh | Primary Responder / Facilitator | Yes |

---

> **After the meeting:** Copy this completed document and attach it to
> Azure DevOps work item AB#34970 as audit evidence.
