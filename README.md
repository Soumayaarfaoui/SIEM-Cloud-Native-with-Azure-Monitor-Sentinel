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
| [Sprint 2](#sprint-2--data-source-integration) | Epic 2 — Data Source Integration | Entra ID logs, Azure Activity logs, AMA on VMs | 🔶 In progress |
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

Fill in workspace details (name, region, resource group): <br/>
<img src="docs/images/sprint1-02-create-workspace-basics.png" height="80%" width="80%" alt="Create workspace basics tab"/>
<br />
<br />

Confirm the workspace overview (name, region): <br/>
<img src="docs/images/sprint1-03-workspace-overview.png" height="80%" width="80%" alt="Workspace overview"/>
<br />
<br />

Confirm 90-day retention setting: <br/>
<img src="docs/images/sprint1-04-retention-90days.png" height="80%" width="80%" alt="Data retention 90 days"/>
<br />
<br />

Confirm tags applied: <br/>
<img src="docs/images/sprint1-05-tags.png" height="80%" width="80%" alt="Tags applied"/>
<br />
<br />

Add Microsoft Sentinel to the workspace: <br/>
<img src="docs/images/sprint1-06-sentinel-create.png" height="80%" width="80%" alt="Add Microsoft Sentinel"/>
<br />
<br />

Confirm Sentinel is active on the workspace: <br/>
<img src="docs/images/sprint1-07-sentinel-overview.png" height="80%" width="80%" alt="Sentinel overview active"/>
<br />
<br />

Confirm RBAC roles assigned (Check access): <br/>
<img src="docs/images/sprint1-08-check-access.png" height="80%" width="80%" alt="Check access RBAC roles"/>

</p>

<h3>Sprint 1 Summary</h3>

| User Story | Completion |
|---|---|
| US 1.1 — Log Analytics Workspace | ✅ 100% |
| US 1.2 — Microsoft Sentinel activation | ✅ 100% |

**Sprint 1 status: fully complete, no open blockers.**

---

<h2 id="sprint-2--data-source-integration">Sprint 2 — Data Source Integration</h2>

*🔶 In progress — this section will be completed once Sprint 2 fixes and testing are finalized.*

