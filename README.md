# azure-zero-trust-portfolio
Practical implementation of Zero Trust Access Architecture using Microsoft Azure
# Zero Trust Access Architecture on Azure

**Duration:** 1 Week (6–7 Days)  
**Author:** Chavali Krishna Chandra Aakhyath

---

## Objective

Build a working Zero Trust model using Azure AD, Conditional Access, PIM, and Governance tools, then document it as a portfolio project.

---

## Tools and Services Used

- Azure Active Directory (Microsoft Entra ID)
- Conditional Access
- Azure AD Identity Protection
- Privileged Identity Management (PIM)
- Access Reviews
- Entitlement Management (Access Packages)
- Azure Monitor + Workbooks
- Log Analytics Workspace
- Azure Alerts

---

## Day 1 – Setup

- Created Azure Free Trial  
- Set up 3 users: `admin@`, `user1@`, `guestuser@`  
- Created 2 groups: `IT-Admins`, `App-Users`  
- Documented: Tenant name, region, domain name  
- Drew rough Zero Trust model (users, apps, policies)

**Tenant Info:**
- Tenant Name: Default Directory  
- Domain: techieguy361gmailcom.onmicrosoft.com  
- Region: India

---

## Day 2 – Conditional Access

- Enabled MFA for `admin`  
- Created CA Policy: "Admin Portal MFA"  
- Blocked legacy authentication    
- Tested difference in behavior between admin and user

**Result:**
- `admin` was required to complete MFA
- `user1` was not required unless Security Defaults were enabled
- CA policy reduced risk exposure for privileged accounts

---

## Day 3 – PIM

- Enabled Microsoft Entra PIM  
- Assigned `admin` as Eligible Global Administrator  
- Configured:
  - Activation Duration: 1 hour  
  - Required MFA for activation  
  - Justification enabled  
- Tested Just-in-Time activation with `admin`  
- Verified access was elevated only after PIM approval

---

## Day 4 – Identity Protection & Access Governance

**Objective:** Restrict access based on device compliance and identity risk

### CA Policy: Block Unmanaged Device Platforms

- Users: `user1`  
- Platform: Android, iOS, macOS, Linux  
- Result: iOS login was blocked

### Risk-Based Policies:

- **User Risk Policy:** Block access if risk = High  
- **Sign-in Risk Policy:** Require MFA if sign-in risk = Medium or higher

Tested by simulating:
- Risky logins (VPN, IP shift)  
- Outcome: MFA prompted or sign-in blocked  

### Access Package

- Created for `App-Users` group  
- Required approval by admin  
- Access expires after 14 days  
- Access Review enabled

---

## Day 5 – Monitoring & Alerting

**Objective:** Gain visibility into sign-in behavior using Azure Monitor

### Log Analytics Setup

- Created workspace: `ZT-Logs`  
- Enabled Diagnostic Settings for Sign-in Logs & Audit Logs

### Custom Workbook

- Tool: Azure Monitor → Workbooks  
- Name: `ZT Sign-in Activity Tracker`  
- Data Source: `ZT-Logs`

Query:
```kusto
SigninLogs
| where TimeGenerated > ago(7d)
| project TimeGenerated, UserPrincipalName, Location, DeviceDetail, Status, ConditionalAccessStatus, AuthenticationRequirement, MFA = tostring(AuthenticationDetails[0].authenticationMethod), AppDisplayName
| sort by TimeGenerated desc
