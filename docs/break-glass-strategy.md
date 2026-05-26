# **Break-Glass Strategy**

> Emergency access account design, protection standards, and monitoring requirements for the Carrie Cares Entra ID tenant.

---

## **What Is a Break-Glass Account?**

A break-glass account is a permanent, highly privileged emergency access account used only when normal administrative access fails — such as during a Conditional Access misconfiguration, MFA outage, or identity provider disruption.

Without a break-glass account, a misconfigured Conditional Access policy can lock every administrator — including the Global Administrator — out of the tenant entirely. For an SMB without a dedicated identity team, this is a critical risk.

---

## **Break-Glass Account — Carrie Cares**

| Field | Detail |
|---|---|
| **Display Name** | Drew Berry |
| **UPN** | Drew.Berry@CarrieCares.onmicrosoft.com |
| **Object ID** | c1067df5-3229-4b9c-a35a-3f26ff1e0647 |
| **User Type** | Member |
| **Role** | Global Administrator |
| **Assignment Type** | Active — Permanent |
| **Created** | March 17, 2026 |
| **MFA** | Excluded from MFA Conditional Access policies |
| **CA Exclusion Group** | SG-EmergencyAccess-Admins |

---

## **Design Principles**

### **1. Cloud-only account**
The break-glass account uses `@CarrieCares.onmicrosoft.com` — not a federated or synced identity. If the federation provider (e.g. AD FS, Entra Connect) fails, a federated account cannot authenticate. A cloud-only account always can.

### **2. Permanent Global Administrator assignment**
Break-glass accounts are assigned Global Administrator permanently — not as an eligible PIM assignment. In an emergency, there is no time to activate a role, wait for approval, or complete an MFA challenge.

### **3. Excluded from all Conditional Access policies**
The account is a member of `SG-EmergencyAccess-Admins`, which is excluded from every CA policy in the tenant. This ensures the account can always authenticate regardless of policy state.

### **4. Not used for day-to-day administration**
Break-glass accounts are not used for routine tasks. Any sign-in activity outside of a documented emergency is treated as a security incident.

### **5. Credential stored offline**
The password is complex, randomly generated, and stored offline in a physically secured location — not in a password manager or digital vault accessible via the tenant.

---

## **Conditional Access Exclusion**

The break-glass account is excluded from all CA policies via the `SG-EmergencyAccess-Admins` security group:

| Policy | Exclusion Applied |
|---|---|
| CA-BlockLegacyAuth | ✅ SG-EmergencyAccess-Admins excluded |
| CA-MFA-AllUsers | ✅ SG-EmergencyAccess-Admins excluded |
| CA-MFA-AllAdmins | ✅ SG-EmergencyAccess-Admins excluded |
| CA-RequireCompliantDevice-Admins | ✅ SG-EmergencyAccess-Admins excluded |
| CA-SignInFrequency | ✅ SG-EmergencyAccess-Admins excluded |
| CA-SigninRisk | ✅ SG-EmergencyAccess-Admins excluded |
| CA-UserRisk | ✅ SG-EmergencyAccess-Admins excluded |

**Why group-based exclusion matters:** Individual account exclusions are harder to audit and easier to miss during policy changes. A single named security group (`SG-EmergencyAccess-Admins`) creates a single point of governance for all break-glass exclusions.

---

## **Monitoring Requirements**

Break-glass account sign-in activity must be monitored continuously. Any authentication event should trigger an immediate alert.

| Alert | Trigger | Action |
|---|---|---|
| Break-glass sign-in detected | Any successful sign-in by Drew.Berry@CarrieCares.onmicrosoft.com | Investigate immediately — confirm emergency justification |
| Break-glass sign-in failure | Any failed authentication attempt | Investigate — potential credential testing or attack |
| Group membership change | Any addition/removal from SG-EmergencyAccess-Admins | Review and confirm authorized change |
| Role assignment change | Any modification to Global Administrator assignment | Escalate immediately |

**Recommended monitoring setup:**
```
Entra ID → Monitoring → Diagnostic settings
→ Send sign-in logs to Log Analytics workspace
→ Create alert rule: UPN = Drew.Berry@CarrieCares.onmicrosoft.com
→ Alert channel: Email + SMS to designated security contact
```

---

## **Break-Glass Usage Procedure**

```
Step 1 — Confirm normal admin access is unavailable
Step 2 — Document the incident and reason for break-glass use
Step 3 — Retrieve credential from offline secure storage
Step 4 — Authenticate and resolve the emergency
Step 5 — Sign out immediately after resolution
Step 6 — Document actions taken during the session
Step 7 — Rotate the break-glass credential
Step 8 — Review sign-in logs to confirm no unauthorized use
```

---

## **Quarterly Review Checklist**

| Check | Action if Failed |
|---|---|
| Break-glass account exists and is enabled | Re-create immediately |
| Account has no MFA registration | Remove any MFA registration — credential only |
| Account excluded from all CA policies | Re-add to SG-EmergencyAccess-Admins |
| Global Administrator role is active and permanent | Re-assign role |
| No sign-in activity since last review | Confirm — if activity exists, investigate |
| Credential stored in secure offline location | Verify with designated custodian |
| SG-EmergencyAccess-Admins membership is correct | Remove any non-break-glass accounts |

---

## **Reference Screenshots**

![Break-Glass Account Overview — Drew Berry](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/BreakGlassAccount.png)

![Break-Glass Account — Global Administrator Permanent Assignment](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/BreakGlassAccount-GA_Role.png)

![CA-BlockLegacyAuth — SG-EmergencyAccess-Admins Exclusion](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/CA-BlockLegacyAuth-2.png)

---

*Last updated: May 2026 — Billy Carrie*
