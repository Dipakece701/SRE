# SRE Tabletop Exercise — Live Meeting Notes & PIR Template

> *All fields, timestamps, new ADO work item IDs, and sign-offs are left blank in advance and will be filled in live during the tabletop exercise.*

- **Exercise Date:** Thursday, October 22, 2026
- **Time:** 10:30 AM to 11:30 AM PST / 12:30 PM to 1:30 PM CDT (60 minutes)
- **Governing Procedure:** Incident Response Procedure v1.0 (AB#34970)
- **Target Environment:** DEMO (`dm0`)
- **Work Item Reference:** AB#34970 | **Parent Feature:** AB#35261
- **Compliance Standard:** SOC 2 Type I (CC7 Audit Evidence)

---

## 1. Attendees and Roles

| Role | Name | Contact |
|---|---|---|
| **Incident Commander** | _______________ | Slack: _______________ |
| **Primary Responder (Facilitator)** | _______________ | Slack: _______________ |
| **Backup Responder** | _______________ | Slack: _______________ |
| **Investigation Engineer** | _______________ | Slack: _______________ |
| **Security Escalation Lead** | _______________ | Slack: _______________ |

---

## 2. Scenario A: App Gateway Error Surge (Reliability Incident)

**Resource:** `helios-dm0-apim` | **Alert Channel:** `#helios-demo-alerts`  
**Incident Record (New ADO Child Task under AB#34970):** `AB#_______________`  
**Severity Level:** _______________

### Who responded to the alert?

- **Responded by:** _______________
- **Time of response:** _______________ UTC
- **How they responded (in `#helios-demo-alerts`):** _______________
- **Backup responder (if Primary did not respond within 15m/30m):** _______________

### Timeline of Events:

| Time (UTC) | What Happened | Who Did It |
|---|---|---|
| _____ | Alert fired in `#helios-demo-alerts`: `helios-dm0-gateway-failure-rate` | System (automated) |
| _____ | Alert acknowledged in `#helios-demo-alerts` | _______________ |
| _____ | New incident record created in Azure DevOps under `AB#34970` | _______________ |
| _____ | Diagnostic investigation completed in `helios-dm0-logs` | _______________ |
| _____ | Incident Commander authorized rollback | _______________ |
| _____ | Rollback executed in GitOps | _______________ |

### Root Cause Found:

- **Which service failed?** _______________
- **What type of error?** _______________
- **Was the failure isolated to one service or widespread?** _______________

### Recovery Verification (4-Point Checklist):

- [ ] **Metric check:** Error rate dropped from ___% to ___% and stayed stable for ___ minutes.
- [ ] **Health check:** Health endpoint returned HTTP ___ in ___ms.
- [ ] **Log check:** Diagnostic query confirmed ___ errors in the last ___ minutes.
- [ ] **Sign-off:** Recovery evidence posted to `#helios-demo-alerts`. Incident record `AB#_______` marked as _______________ by _______________ at _____ UTC.

---

## 3. Scenario B: Key Vault Unauthorized Access Spike (Security Detection Gap Track)

**Resource:** `helios-dm0-backend-kv` | **Alert Channel:** `#helios-demo-alerts`  
**Detection Gap Note:** `dm0` has no Key Vault unauthorized-access alert rule and Defender for Key Vault is on Free tier — run as a detection gap and log follow-up ticket.  
**Incident Record (New ADO Child Task under AB#34970):** `AB#_______________`  
**Severity Level:** _______________

### Who responded to the inject / detection?

- **Responded by:** _______________
- **Time of response:** _______________ UTC
- **Escalated to:** _______________
- **Reason for escalation:** _______________

### Timeline of Events:

| Time (UTC) | What Happened | Who Did It |
|---|---|---|
| _____ | Detection gap inject: 18 HTTP 403 `SecretGet` attempts on `helios-dm0-backend-kv` observed in `AzureDiagnostics` (no automated alert on `dm0`) | Tabletop Inject / Log Audit |
| _____ | Acknowledged in `#helios-demo-alerts` | _______________ |
| _____ | Joint security escalation triggered | _______________ |
| _____ | New incident record created in Azure DevOps under `AB#34970` | _______________ |
| _____ | Diagnostic investigation completed in `helios-dm0-logs` | _______________ |
| _____ | Compromised Service Principal credential disabled/deleted in Entra ID | _______________ |
| _____ | `helios-dm0-backend-kv` firewall locked down to trusted VNets | _______________ |
| _____ | Secret `redis-cache-connection` rotated and services restarted | _______________ |

### Attacker & Detection Gap Details:

- **Attacker IP address:** _______________
- **Number of unauthorized attempts:** _______________
- **Targeted secret name:** _______________
- **Why no alert fired on `dm0` (Detection Gap):** _______________

### Containment Actions Taken:

- [ ] Compromised Service Principal credential disabled and deleted in Entra ID.
- [ ] `helios-dm0-backend-kv` firewall restricted to trusted internal networks only.
- [ ] Targeted secret (`redis-cache-connection`) rotated.
- [ ] Dependent services restarted with the new secret.

### Recovery Verification (4-Point Checklist):

- [ ] **Audit log check:** Confirmed ___ requests from attacker IP in the last ___ minutes on `helios-dm0-backend-kv`.
- [ ] **Service health check:** Legitimate services authenticated with new secret via Workload Identity (HTTP ___).
- [ ] **Detection gap logged:** Follow-up ticket `AB#_______` created to add Key Vault unauthorized-access alert rule on `dm0` routed to `#helios-demo-alerts`.
- [ ] **Security sign-off:** _______________ reviewed audit evidence and approved closure at _____ UTC.

### Additional Security Checks (if discussed):

- [ ] Checked if the attacker IP hit any other Azure resources: _______________
- [ ] Checked Entra ID sign-in logs for the compromised Service Principal: _______________
- [ ] Checked threat intelligence feeds for the attacker IP: _______________

---

## 4. What Went Well

1. _______________________________________________________________
2. _______________________________________________________________
3. _______________________________________________________________
4. _______________________________________________________________

*(Add more rows as needed during the discussion.)*

---

## 5. Gaps Found and Action Items (New Child Tasks Created Under AB#34970)

> *(Create new tasks from the tabletop — do not reuse pre-existing ticket numbers)*

| # | New ADO Ticket ID | Action Item | Who is responsible? | Target date |
|---|---|---|---|---|
| 1 | `AB#_______` | _______________________________________ | _______________ | _______________ |
| 2 | `AB#_______` | _______________________________________ | _______________ | _______________ |
| 3 | `AB#_______` | _______________________________________ | _______________ | _______________ |
| 4 | `AB#_______` | _______________________________________ | _______________ | _______________ |
| 5 | `AB#_______` | _______________________________________ | _______________ | _______________ |

---

## 6. Customer and Stakeholder Communication

- **Was customer communication needed for either scenario?** _______________
- **Who is responsible for customer notifications?** _______________
- **Do we have a status page?** _______________
- **Action item to improve this?** _______________

---

## 7. Sign-Off (Filled in Live at End of Tabletop)

By signing below, participants confirm they attended and participated in the
SRE Tabletop Exercise for SOC 2 Type I (CC7) audit compliance.

| Name | Tabletop Role | Confirmed (Yes/No) | Timestamp (UTC) |
|---|---|---|---|
| Maurice Kennedy | Incident Commander | | |
| Constantin Pricochi | Backup Responder | | |
| Aditya Mehta | Investigation Engineer | | |
| Dipak Singh | Primary Responder / Facilitator | | |

---
