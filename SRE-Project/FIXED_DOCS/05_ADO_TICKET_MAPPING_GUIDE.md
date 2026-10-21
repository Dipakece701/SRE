# ADO TICKET CREATION & COPY-PASTE GUIDE

> **Parent Work Item:** [**AB#34970**](https://dev.azure.com/qcellsces/Helios/_workitems/edit/34970) *(SRE tabletop: one fake incident on DEMO, written up)*  
> **Area Path:** `Helios\QRE`  
> **Work Item Type:** `Task`  
> **Link Type:** `Child` (under AB#34970)

---

## 🖱️ How to Create Each Child Ticket in Azure DevOps (Click-by-Click)

1. Open parent story **AB#34970** in your browser.
2. On the right sidebar under **Related Work**, click:  
   👉 **Add link** $\rightarrow$ **New item**
3. In the popup window:
   * **Link type:** Select `Child`
   * **Work item type:** Select `Task`
   * **Title:** Paste the Title from the table below
4. Click **OK** to open the new task.
5. Fill in:
   * **Assigned To:** (See table below)
   * **Area Path:** `Helios\QRE`
   * **Tags:** (See table below)
   * **Description:** Open the corresponding file and copy-paste the whole description.
6. Click **Save & Close**.
7. ✍️ **Write the newly generated AB# ID into your Live Notes!**

---

## 📋 Complete Master Mapping Table (Which File to Copy for Each Ticket)

| # | WHEN to Create | Which File to Open in `2_INDIVIDUAL_TICKETS/` | Ticket Title to Paste | Assigned To | Tags to Paste |
|---|---|---|---|---|---|
| **1** | **Minute 10–12** *(During Scenario A)* | [`Ticket_1_APIM_5xx.md`](file:///C:/Users/Praveen/Documents/antigravity/SRE-Project/SRE-Tabletop-Exercise-Plan-DEMO-main/SRE-Tabletop-Exercise-Plan-DEMO-main/2_INDIVIDUAL_TICKETS/Ticket_1_APIM_5xx.md) | `[INCIDENT SIMULATION] APIM Gateway 5xx Failure Rate Surge on helios-dm0-apim (Track A)` | **Dipak Singh** | `incident, sev-2, tabletop, soc2-cc7, reliability` |
| **2** | **Minute 25–27** *(During Scenario B)* | [`Ticket_2_KeyVault_Security.md`](file:///C:/Users/Praveen/Documents/antigravity/SRE-Project/SRE-Tabletop-Exercise-Plan-DEMO-main/SRE-Tabletop-Exercise-Plan-DEMO-main/2_INDIVIDUAL_TICKETS/Ticket_2_KeyVault_Security.md) | `[INCIDENT SIMULATION] Key Vault Unauthorized Access Spike on helios-dm0-backend-kv (Track B)` | **Dipak Singh** | `incident, sev-1, security, tabletop, soc2-cc7` |
| **3** | **After Call** *(PIR Action Item 1)* | [`Ticket_3_ActionItem_OnCall_Wiki.md`](file:///C:/Users/Praveen/Documents/antigravity/SRE-Project/SRE-Tabletop-Exercise-Plan-DEMO-main/SRE-Tabletop-Exercise-Plan-DEMO-main/2_INDIVIDUAL_TICKETS/Ticket_3_ActionItem_OnCall_Wiki.md) | `[SOC 2 CC7 Action Item] Document SRE on-call rotation, backup rules, and escalation SLAs in Wiki` | **Maurice Kennedy** | `soc2-cc7, on-call, sop, table-top-pir` |
| **4** | **After Call** *(PIR Action Item 2)* | [`Ticket_4_ActionItem_Severity_Rubric.md`](file:///C:/Users/Praveen/Documents/antigravity/SRE-Project/SRE-Tabletop-Exercise-Plan-DEMO-main/SRE-Tabletop-Exercise-Plan-DEMO-main/2_INDIVIDUAL_TICKETS/Ticket_4_ActionItem_Severity_Rubric.md) | `[SOC 2 CC7 Action Item] Publish formal Incident Severity Classification Rubric with Sev 1/Sev 2 thresholds` | **Dipak Singh** | `soc2-cc7, sop, incident-management, table-top-pir` |
| **5** | **After Call** *(PIR Action Item 3)* | [`Ticket_5_ActionItem_Slack_Runbooks.md`](file:///C:/Users/Praveen/Documents/antigravity/SRE-Project/SRE-Tabletop-Exercise-Plan-DEMO-main/SRE-Tabletop-Exercise-Plan-DEMO-main/2_INDIVIDUAL_TICKETS/Ticket_5_ActionItem_Slack_Runbooks.md) | `[SOC 2 CC7 Action Item] Embed runbook and KQL deep links directly into Azure Monitor Slack alert cards` | **Aditya Mehta** | `soc2-cc7, monitoring, alerts, slack, table-top-pir` |
| **6** | **After Call** *(PIR Action Item 4)* | [`Ticket_6_ActionItem_KV_Rotation.md`](file:///C:/Users/Praveen/Documents/antigravity/SRE-Project/SRE-Tabletop-Exercise-Plan-DEMO-main/SRE-Tabletop-Exercise-Plan-DEMO-main/2_INDIVIDUAL_TICKETS/Ticket_6_ActionItem_KV_Rotation.md) | `[SOC 2 CC7 Action Item] Implement automated Key Vault credential rotation and network deny alerts` | **Constantin Pricochi** | `soc2-cc7, security, key-vault, automation, table-top-pir` |
| **7** | **After Call** *(PIR Action Item 5 - NEW)* | *(Copy description below)* | `[SOC 2 CC7 Action Item] Create customer and stakeholder communication protocol for Sev 1 incidents` | **Dipak Singh** | `soc2-cc7, customer-comms, sop, table-top-pir` |

---

## 📝 Description for Ticket 7 (New Action Item 5: Customer Communication Plan)

Copy and paste this into the description of Ticket 7:

```html
<p><strong>Action Item resulting from SRE Tabletop Exercise (AB#34970):</strong></p>
<p>Establish a formal customer and stakeholder communication protocol to fulfill SOC 2 Type II CC7.4 requirements (communication to affected parties during security and availability incidents).</p>

<p><strong>Deliverables:</strong></p>
<ul>
  <li>Define roles and responsibilities for external customer notifications during Sev 1 incidents.</li>
  <li>Evaluate and implement a company status page (e.g. Statuspage or Azure Status) with update cadences.</li>
  <li>Draft pre-approved communication templates for API gateway downtime and credential rotation events.</li>
</ul>
```

---

## 💡 Quick Tips During the Meeting

* **You do not need to create tickets 3, 4, 5, 6, 7 during the call!**  
  Only create **Ticket 1** (Scenario A) and **Ticket 2** (Scenario B) live if you want to record their new AB# numbers on your shared screen.
* Tickets 3 through 7 are Action Items resulting from the retrospective—you can create them **right after the call ends**.
