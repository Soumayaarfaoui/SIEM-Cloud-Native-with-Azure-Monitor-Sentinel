<h1>Cloud-Native SIEM — Azure Monitor & Microsoft Sentinel</h1>

<h2>Description</h2>
Internship project (Smartovate Ltd) to deploy a cloud-native SIEM on Microsoft Azure. The project centralizes security event logs (identity, activity, endpoint) into a Log Analytics Workspace and layers Microsoft Sentinel on top for detection, correlation, and incident response — built and documented across four agile sprints.
<br />

<h2>Languages and Utilities Used</h2>
- <b>Microsoft Azure Portal</b>
- <b>Microsoft Sentinel</b>
- <b>Azure Monitor / Log Analytics Workspace</b>
- <b>KQL</b> (Kusto Query Language)
- <b>Microsoft Entra ID</b>
- <b>Azure Monitor Agent (AMA) & Data Collection Rules</b>

<h2>Environments Used</h2>
- <b>Azure subscription:</b> Smartovate (shared class tenant)
- <b>Region:</b> East US
- <b>Resource group:</b> SoumayaArfaoui

<h2>Sprint Division</h2>

| Sprint | Epic | Focus | Status |
|---|---|---|---|
| [Sprint 1](#sprint-1--base-infrastructure) | Epic 1 — Base Infrastructure | Log Analytics Workspace + Sentinel activation | ✅ Complete |
| [Sprint 2](#sprint-2--data-source-integration) | Epic 2 — Data Source Integration | Entra ID logs, Azure Activity logs, AMA on VMs | ✅ Complete |
| Sprint 3 | Epic 3 — TBD | TBD | ⬜ Not started |
| Sprint 4 | Epic 4 — TBD | TBD | ⬜ Not started |

---

<h2 id="sprint-1--base-infrastructure">Sprint 1 — Base Infrastructure</h2>

<b>Epic 1:</b> Deployment and Configuration of the Base Infrastructure
<br />
<b>Status:</b> ✅ Complete

<h3>Description</h3>
Deploy the foundational logging backbone for the SIEM: a centralized Log Analytics Workspace, and Microsoft Sentinel activated on top of it with correct RBAC access.

<h3>US 1.1 — Creation of the Log Analytics Workspace</h3>

*As a security engineer, I want to deploy a Log Analytics Workspace, in order to centralize the storage of all event logs.*
**Priority:** High

| Acceptance criteria | Status | Evidence |
|---|---|---|
| Workspace created in the appropriate Azure region | ✅ Done | `law-smartovate-siem`, region East US, resource group `SoumayaArfaoui` |
| Data retention configured to a minimum of 90 days | ✅ Done | 90-day retention (free tier while Sentinel is active) |
| Billing and environment tags applied | ✅ Done | Tag `project: SIEM-Sentinel` |

<h3>US 1.2 — Activation of Microsoft Sentinel</h3>

*As a security engineer, I want to activate Microsoft Sentinel on the Log Analytics Workspace, in order to benefit from SIEM capabilities.*
**Priority:** High

| Acceptance criteria | Status | Evidence |
|---|---|---|
| Sentinel successfully activated on the target workspace | ✅ Done | Activated on `law-smartovate-siem`, 31-day free trial (10 GB/day, until 17/08/2026) |
| RBAC roles (Sentinel Contributor, Sentinel Reader) defined and assigned | ✅ Done | Confirmed via Access control (IAM) → Check access: Sentinel Contributor, Sentinel Reader, Sentinel Automation Contributor |

<h3>Issue Encountered & Resolved</h3>
**Problem:** Guest account (Gmail-based) could not be found via the standard "Select members" search when assigning Sentinel roles — an Entra ID guest-enumeration restriction.
<br />
**Resolution:** Subscription Owner (Abdelhalek Bakkari) assigned the roles directly, bypassing the search.
<br />
**Verification:** Confirmed via Access control (IAM) → Check access.

<h3>Program walk-through</h3>
<p align="center">

Search for and create the Log Analytics Workspace: <br/>
<img src="docs/images/3.png" height="80%" width="80%" alt="Search Log Analytics workspaces"/>
<br />
<br />



Confirm 90-day retention setting: <br/>
<img src="docs/images/datarention.png" height="80%" width="80%" alt="Data retention 90 days"/>
<br />
<br />

Confirm tags applied: <br/>
<img src="docs/images/4.png" height="80%" width="80%" alt="Tags applied"/>
<br />
<br />

Add Microsoft Sentinel to the workspace: <br/>
<img src="docs/images/sentinel.png" height="80%" width="80%" alt="Add Microsoft Sentinel"/>
<br />
<br />
<img src="docs/images/sentinel2.png" height="80%" width="80%" alt="Add Microsoft Sentinel"/>
<br />
<br />


<br />
<br />

Confirm RBAC roles assigned (Check access): <br/>
<img src="docs/images/rbac.png" height="80%" width="80%" alt="Check access RBAC roles"/>

</p>

<h3>Sprint 1 Summary</h3>

| User Story | Completion |
|---|---|
| US 1.1 — Log Analytics Workspace | ✅ 100% |
| US 1.2 — Microsoft Sentinel activation | ✅ 100% |

**Sprint 1 status: fully complete, no open blockers.**

---

<h2 id="sprint-2--data-source-integration">Sprint 2 — Data Source Integration</h2>

<b>Epic 2:</b> Data Source Integration
<br />
<b>Status:</b> ✅ Complete

<h3>Description</h3>
Connect the SIEM's core data sources: Microsoft Entra ID identity logs, Azure Activity (resource-level) logs, and endpoint telemetry from test VMs via the Azure Monitor Agent — so Sentinel has real data to detect against in Sprint 3.

<h3>US 2.1 — Connecting Azure Active Directory logs</h3>

*As a cloud administrator, I want to connect Azure AD sign-in and audit logs to Sentinel, in order to monitor authentications and identity changes.*
**Priority:** High

| Acceptance criteria | Status | Evidence |
|---|---|---|
| The Azure Active Directory (Microsoft Entra ID) data connector is enabled | ✅ Done | Data connectors → Microsoft Entra ID shows **Connected** status, live ingestion graph, last log ~8 min ago |
| The "SigninLogs" and "AuditLogs" tables are visible in the KQL query interface | ✅ Done | `SigninLogs \| take 10` and `AuditLogs \| take 10` both return real rows (sign-in activity, user/password/security-info updates) |
| Maximum delay of 15 minutes between an event and its appearance in Sentinel | ✅ Done | `SigninLogs \| extend delay = ingestion_time() - TimeGenerated \| project TimeGenerated, ingestion_time(), delay \| take 5` — tested over Last 24 hours and re-tested over Last 7 days: delay consistently between ~45 seconds and ~1.5 minutes, well within the 15-minute requirement |

<h4>Issue Encountered & Resolved</h4>
**Problem:** Earlier testing (Sprint 2 start) showed an ingestion delay of roughly 30 minutes for SigninLogs, exceeding the 15-minute acceptance criterion.
<br />
**Resolution:** No manual fix applied — re-tested later in the sprint once the Entra ID connector and ingestion pipeline had stabilized. A 7-day sample confirmed consistent delay under 2 minutes.
<br />
**Verification:** KQL delay query re-run across a 24-hour and a 7-day window, both showing sub-2-minute delay.

<h3>US 2.2 — Collecting Azure Activity logs</h3>

*As a security analyst, I want to ingest Azure Activity logs, in order to trace changes made to infrastructure resources.*
**Priority:** Medium

| Acceptance criteria | Status | Evidence |
|---|---|---|
| The Azure Activity connector is configured via diagnostic settings at the subscription level | ✅ Done | Diagnostic setting created under Subscriptions → Activity log → Diagnostic settings, destination = `law-smartovate-siem` |
| Resource creation, modification, and deletion events flow into the AzureActivity table | ✅ Done | `AzureActivity \| take 10` returns real events (e.g., VIRTUALMACHINES/START/ACTION, WORKSPACES/WRITE) across multiple resource groups |

<h4>Issue Encountered & Resolved</h4>
**Problem:** The standard Content Hub "Azure Activity" connector requires configuration via Azure Policy, which requires the Owner role at the subscription level — held only by Contributor, which explicitly excludes `Microsoft.Authorization/PolicyAssignments/write`.
<br />
**Resolution:** Bypassed the Policy-based connector by creating a direct diagnostic setting on the subscription's Activity log, pointing to the Log Analytics workspace — achieves the same data flow without requiring Owner-level Policy permissions.
<br />
**Verification:** `AzureActivity` table populated with live events; diagnostic setting visible and enabled in the portal.

<h3>US 2.3 — Deployment of the Azure Monitor Agent on VMs</h3>

*As a systems engineer, I want to deploy the Azure Monitor Agent (AMA) on a sample of virtual machines, in order to collect system events (Windows Event Logs / Syslog).*
**Priority:** High

| Acceptance criteria | Status | Evidence |
|---|---|---|
| A Data Collection Rule (DCR) is created to specify which events to collect | ✅ Done | DCR created and associated with both `vm-windows` and the Linux VM, specifying Windows Security Events and Syslog facilities |
| AMA agent installed on at least one Windows VM and one Linux VM | ✅ Done | Extensions on `vm-windows` show AzureMonitorWindowsAgent Running; Linux VM shows AzureMonitorLinuxAgent Running |
| Security events (e.g., Event ID 4624) ingested into the SecurityEvent table | ✅ Done | `SecurityEvent \| where EventID == 4624 \| take 10` returns real sign-in events from `vm-windows` |

<h4>Issue Encountered & Resolved</h4>
**Problem:** VM quota blocker — the B-series VM family was capped at 4 vCPUs subscription-wide, fully used, blocking creation of the second (Linux) test VM.
<br />
**Resolution:** Self-service quota increase request submitted via Azure "Manage Quota" — approved instantly, raising the limit from 4 to 8 vCPUs.
<br />
**Verification:** Both `vm-windows` and the Linux VM created and running without further quota errors.

<h3>Program walk-through</h3>
<p align="center">

Microsoft Entra ID connector — connected status and ingestion graph: <br/>
<img src="docs/images/entra-connector.png" height="80%" width="80%" alt="Microsoft Entra ID connector connected"/>
<br />
<br />

AzureActivity events flowing (deletions, creations, modifications): <br/>
<img src="docs/images/azureactivity-logs.png" height="80%" width="80%" alt="AzureActivity query results"/>
<br />
<br />

AuditLogs visible in KQL: <br/>
<img src="docs/images/auditlogs.png" height="80%" width="80%" alt="AuditLogs query results"/>
<br />
<br />

SigninLogs visible in KQL: <br/>
<img src="docs/images/signinlogs.png" height="80%" width="80%" alt="SigninLogs query results"/>
<br />
<br />

Ingestion delay confirmed under 2 minutes (7-day sample): <br/>
<img src="docs/images/ingestion-delay.png" height="80%" width="80%" alt="Ingestion delay KQL query"/>
<br />
<br />

Diagnostic setting configured at subscription level: <br/>
<img src="docs/images/diagnostic-settings.png" height="80%" width="80%" alt="Diagnostic settings Activity log"/>
<br />
<br />

Data Collection Rule (DCR) configuration: <br/>
<img src="docs/images/dcr.png" height="80%" width="80%" alt="Data Collection Rule"/>
<br />
<br />

AMA agent status on Windows and Linux VMs: <br/>
<img src="docs/images/ama-windows.png" height="80%" width="80%" alt="AMA on Windows VM"/>
<br />
<br />
<img src="docs/images/ama-linux.png" height="80%" width="80%" alt="AMA on Linux VM"/>
<br />
<br />

SecurityEvent Event ID 4624 ingested: <br/>
<img src="docs/images/securityevent-4624.png" height="80%" width="80%" alt="SecurityEvent EventID 4624"/>

</p>

<h3>Sprint 2 Summary</h3>

| User Story | Completion |
|---|---|
| US 2.1 — Connect Azure AD (Entra ID) logs | ✅ 100% |
| US 2.2 — Collect Azure Activity logs | ✅ 100% |
| US 2.3 — Deploy AMA on Windows + Linux VMs | ✅ 100% |

**Sprint 2 status: fully complete. Two workarounds documented (diagnostic-setting bypass for Azure Activity, VM quota self-service increase); ingestion delay re-tested and confirmed within acceptance criteria after an initial ~30-minute observation.**

---

<h2 id="sprint-3--threat-detection">Sprint 3 — Threat Detection and Alerting</h2>

*⬜ Not started — this section will be completed once Sprint 3 requirements are shared and testing is finalized.*
