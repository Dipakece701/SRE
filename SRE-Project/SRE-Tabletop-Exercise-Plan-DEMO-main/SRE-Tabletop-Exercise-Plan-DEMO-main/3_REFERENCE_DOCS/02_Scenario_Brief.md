# 02 — SRE Tabletop Scenario Brief

**Work Item:** AB#34970  
**Parent Feature:** AB#35261 (*Observability Phase 1: PROD ready for 11 Nov*)  
**Standard Operating Procedure:** `00_Incident_Procedure_v1.md` (AB#34970 Comment #10615713)  
**Compliance Standard:** SOC 2 Type I — CC7.1, CC7.2, CC7.3 (Incident Detection, Management & Response)  
**Scheduled Date & Time:** Thursday, October 22, 2026 | 10:30 AM – 11:30 AM PST (12:30 PM – 1:30 PM CDT)  
**Target Environment:** DEMO (`dm0`)  
**Facilitator & Scribe:** Dipak Singh  
**Participants:** Maurice Kennedy, Constantin Pricochi, Aditya Mehta, Dipak Singh  

---

## 1. On-Call & Escalation Roster (Pre-PagerDuty Interim)

Per **Incident Procedure v1.0**, the current team escalation path using the named participants in the room is:

| Incident Role | Primary Responder | Backup Responder | Escalation Contact |
|---|---|---|---|
| **Incident Commander (IC)** | **Maurice Kennedy** | **Constantin Pricochi** | Maurice Kennedy |
| **Primary Responder (First Ack)** | **Dipak Singh** | **Aditya Mehta** | Constantin Pricochi |
| **Backup Responder (Secondary)** | **Constantin Pricochi** | **Dipak Singh** | Maurice Kennedy |
| **Security Escalation** | **Maurice Kennedy** | **Constantin Pricochi** | Maurice Kennedy & Constantin Pricochi |

* **Escalation Rules:**
  * If Primary does not acknowledge within **15 minutes** (Sev 1) or **30 minutes** (Sev 2), Backup Responder (**Constantin Pricochi**) takes over immediately.
  * If unresolved/uncontained after **30 minutes**, escalate directly to Incident Commander (**Maurice Kennedy**).
  * Any security/data compromise triggers immediate joint escalation to **Maurice Kennedy** and **Constantin Pricochi**.

---

## 2. Tabletop Structure: Dual-Scenario Simulation

To test both **Operational Availability** and **Information Security** under SOC 2 Type I (CC7), this exercise walks through two paired incident tracks:
1. **Scenario A (Reliability):** APIM Gateway 5xx Failure Rate Surge (`helios-dm0-apim`) — live alert rule on `dm0`.
2. **Scenario B (Security — Detection Gap):** Key Vault Unauthorized Access Spike & Credential Compromise (`helios-dm0-backend-kv`) — exercised as a **detection gap** (`dm0` currently has no Key Vault unauthorized-access alert rule and Defender for Key Vault is on the Free tier).

Both scenarios leave **Who Acked**, **Timestamps**, **New ADO Work Item IDs**, and **Sign-offs** blank in advance so they are filled in live during the tabletop:
* **Who Acked** (responder name, live timestamp, and `#helios-demo-alerts` channel acknowledgment).
* **The Incident Record** (new Azure DevOps work item created live from the tabletop, severity, and tags).
* **How We Checked Recovery** (4-point technical verification checklist before resolution).

---

## 3. Scenario A (Reliability): APIM 5xx Surge

### Baseline & Ingestion
* **Resource:** `helios-dm0-apim` in `helios-dm0-us-west3-rg` (Subscription: `Helios - Demo`).
* **Alert Rule:** `helios-dm0-gateway-failure-rate` (`Category == "GatewayLogs"`, 15-min window, threshold $> 10\%$).
* **Notification Route:** Action Group `ag-helios-dm0-ops` $\rightarrow$ Slack `#helios-demo-alerts`.

### Simulation Timeline & Injects
1. **Detection (T+00m):** Alert fires to `#helios-demo-alerts` reporting 42% 5xx error rate on `helios-dm0-apim` [Time: `_______ UTC` - to be recorded live].
2. **Who Acked (T+02m):**
   * **Acknowledged by:** `_______________` [Time: `_______ UTC` - to be recorded live] via 👀 reaction + Slack thread post in `#helios-demo-alerts`:
     > *"ACKNOWLEDGED by @[Name] at [Time] UTC. Opening triage bridge. Declaring Sev 2 incident."*
3. **The Incident Record:**
   * Created live in Azure DevOps under `Helios\QRE` as a new Child Task of **AB#34970** (Title: `[INCIDENT - Sev 2] APIM Gateway 5xx Spike on helios-dm0-apim`).
   * **Work Item ID:** `AB#_______` *(Leave blank; record new ticket number live upon creation)*.
4. **Investigation (T+12m):**
   * KQL in `helios-dm0-logs` (`AzureDiagnostics | where Category == "GatewayLogs"`) confirms error isolated to `ontology-event-processor` with upstream HTTP 504 (timeout > 30s).
5. **Mitigation (T+25m):**
   * Incident Commander (**Maurice Kennedy**) authorizes pod restart/rollback of `ontology-event-processor`. **Constantin Pricochi** executes container rollback in GitOps.
6. **How We Checked Recovery (T+30m):**
   * [ ] **Criterion 1 (Metric):** APIM 5xx failure rate drops from 42% to 0.08% and remains stable for > 15 minutes.
   * [ ] **Criterion 2 (Synthetic Probe):** `curl -i -X GET https://helios-dm0-apim.azure-api.net/ontology-event-processor/health` returns `HTTP 200 OK` in 48ms.
   * [ ] **Criterion 3 (Diagnostic KQL):** Log Analytics confirms 0 gateway 504 errors in last 10 minutes.
   * [ ] **Criterion 4 (Sign-off):** Recovery proof posted to `#helios-demo-alerts`; status updated to Resolved on the newly created incident work item [Signed off by: `_______________` at `_______ UTC`].

---

## 4. Scenario B (Security — Detection Gap): Key Vault Unauthorized Access Spike

### Baseline & Detection Gap Context
* **Resource:** `helios-dm0-backend-kv` (Azure Key Vault in `helios-dm0-us-west3-rg`).
* **Detection Gap on `dm0`:** There is currently **no** Key Vault unauthorized-access alert rule on `dm0`, and Microsoft Defender for Key Vault is on the **Free** tier (so no automated alert fires to `#helios-demo-alerts`).
* **Tabletop Approach:** We run Track B as a **detection gap** scenario—injecting a simulated finding of 18 unauthorized `SecretGet` (HTTP 403 Forbidden) attempts in `AzureDiagnostics` from external IP `198.51.100.44` targeting secret `redis-cache-connection`, walking through our containment and recovery procedure, and logging the missing alert rule / Defender coverage as a new follow-up ticket.

### Simulation Timeline & Injects
1. **Detection / Inject (T+00m):** Simulated security inject / log review identifies [Time: `_______ UTC` - to be recorded live]:
   > 🚨 *[SECURITY Sev 1 — Detection Gap Inject] Key Vault `helios-dm0-backend-kv`: 18 unauthorized secret retrieval attempts (HTTP 403 Forbidden) observed in `AzureDiagnostics` within 5 minutes from external IP `198.51.100.44` targeting secret `redis-cache-connection`. (No automated alert fired in `#helios-demo-alerts` — logged as detection gap).*
2. **Who Acked (T+02m):**
   * **Acknowledged by:** `_______________` [Time: `_______ UTC` - to be recorded live] in `#helios-demo-alerts`:
     > *"ACKNOWLEDGED by @[Name] at [Time] UTC. Paging @Maurice Kennedy and @Constantin Pricochi. Initiating immediate security lockdown."*
   * Escalated immediately to **Maurice Kennedy** and **Constantin Pricochi**.
3. **The Incident Record:**
   * Created live in Azure DevOps under `Helios\QRE` as a new Child Task of **AB#34970** (Title: `[INCIDENT - Sev 1 Security] Anomalous Key Vault access attempts on helios-dm0-backend-kv`). Tags: `security`, `sev-1`, `soc2-cc7`, `detection-gap`.
   * **Work Item ID:** `AB#_______` *(Leave blank; record new ticket number live upon creation)*.
4. **Diagnostic Investigation (T+10m):**
   * Audit query in `AzureDiagnostics | where ResourceProvider == "MICROSOFT.KEYVAULT" and Resource == "HELIOS-DM0-BACKEND-KV"` reveals an expired or leaked service principal credential was queried from an untrusted public IP.
5. **Containment & Mitigation (T+17m):**
   * **Immediate Revocation:** **Constantin Pricochi** checks Entra ID sign-in logs and revokes/deletes the compromised service principal credential in Microsoft Entra ID.
   * **Network Hardening:** **Dipak Singh** restricts `helios-dm0-backend-kv` firewall rules to enforce private endpoint / trusted Azure VNet subnets only.
   * **Secret Rotation:** **Dipak Singh** rotates `redis-cache-connection` secret in `helios-dm0-backend-kv` and restarts dependent container workloads with the updated version.
6. **How We Checked Recovery (T+27m):**
   * [ ] **Criterion 1 (Audit Log Zero):** Key Vault audit log query in `helios-dm0-logs` shows 0 requests (401/403 or 200) from IP `198.51.100.44` post-containment:
     ```kql
     AzureDiagnostics
     | where ResourceProvider == "MICROSOFT.KEYVAULT" and Resource == "HELIOS-DM0-BACKEND-KV"
     | where CallerIPAddress == "198.51.100.44"
     | where TimeGenerated > ago(30m)
     ```
   * [ ] **Criterion 2 (Workload Health):** Legitimate backend workloads (`ontology-event-processor`, `cost-ingestion`) verified pulling new secret version via Workload Identity / Managed Identity with HTTP 200 OK.
   * [ ] **Criterion 3 (Detection Gap Logged):** New follow-up work item created in ADO to add a `helios-dm0-backend-kv` unauthorized-access log alert rule routed to `#helios-demo-alerts` (and evaluate Defender for Key Vault tier).
   * [ ] **Criterion 4 (Sign-off):** **Maurice Kennedy** reviews audit evidence and signs off on incident closure [Signed off by: `_______________` at `_______ UTC`].

---

## 5. Expected Audit Artifacts for AB#34970

1. **Incident Procedure v1.0:** Confirmed on AB#34970 and in `00_Incident_Procedure_v1.md`.
2. **Dual Incident Retrospectives:** Live notes completed during the call (`02_LIVE_NOTES_TEMPLATE.md`) covering both APIM 5xx and `helios-dm0-backend-kv` detection-gap scenarios.
3. **New Incident & Follow-up Work Items (Created live from the tabletop under AB#34970 — no pre-existing ticket numbers reused):**
   * `AB#_______`: Track A Incident Record (`[INCIDENT - Sev 2] APIM Gateway 5xx Spike on helios-dm0-apim`)
   * `AB#_______`: Track B Incident Record (`[INCIDENT - Sev 1 Security] Anomalous Key Vault access attempts on helios-dm0-backend-kv`)
   * `AB#_______`: Document SRE on-call rotation and escalation matrix in Wiki (Assignee: Maurice Kennedy)
   * `AB#_______`: Publish formal Incident Severity Classification Rubric (Assignee: Dipak Singh)
   * `AB#_______`: Embed runbook and KQL deep links into `#helios-demo-alerts` Slack alert cards (Assignee: Aditya Mehta)
   * `AB#_______`: Add Key Vault unauthorized-access / firewall deny alert rule on `helios-dm0-backend-kv` routed to `#helios-demo-alerts` & automate secret rotation (Assignee: Constantin Pricochi)
4. **Sign-off Sheet:** Signed live by Maurice Kennedy, Constantin Pricochi, Aditya Mehta, and Dipak Singh.
