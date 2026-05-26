# **Conditional Access Policy Design**

> Conditional Access policy framework implemented in the Carrie Cares Entra ID P2 sandbox — simulating a retail/ecommerce SMB building Zero Trust identity controls from the ground up.

---

## **Overview**

Conditional Access is the enforcement engine of Zero Trust. It evaluates signals — user identity, device state, location, risk level — and makes real-time access decisions. In an SMB context, it is the single most impactful identity control available in Entra ID.

| Principle | Application |
|---|---|
| **Verify explicitly** | Every sign-in evaluated against risk, device, and user signals |
| **Least privilege access** | MFA and compliant device required for privileged roles |
| **Assume breach** | Risk-based policies block or remediate compromised sessions automatically |

---

## **Policy Inventory — Carrie Cares Tenant**

| Policy Name | Scope | Condition | Grant Control | State |
|---|---|---|---|---|
| `CA-BlockLegacyAuth` | SG-All Users | Legacy auth clients | Block access | ✅ On |
| `CA-MFA-AllUsers` | SG-All Users | All cloud apps | Require MFA | ✅ On |
| `CA-MFA-AllAdmins` | Admin roles | All cloud apps | Require MFA | ✅ On |
| `CA-RequireCompliantDevice-Admins` | Admin roles | All cloud apps | Require compliant device | ✅ On |
| `CA-SignInFrequency` | SG-All Users | All cloud apps | Sign-in frequency control | ✅ On |
| `CA-SigninRisk` | SG-All Users | Sign-in risk: High, Medium | Block access | ✅ On |
| `CA-UserRisk` | SG-All Users | User risk: High, Medium | Require MFA + Risk remediation | ✅ On |

---

## **Policy Deep Dives**

### **CA-BlockLegacyAuth**

Legacy authentication protocols — Exchange ActiveSync, IMAP, POP, SMTP AUTH — do not support MFA. Any tenant that allows legacy auth has an MFA bypass built in by default.

| Setting | Value |
|---|---|
| **Users included** | SG-All Users |
| **Users excluded** | SG-EmergencyAccess-Admins |
| **Target resources** | All resources (All cloud apps) |
| **Condition** | Client apps: Exchange ActiveSync + Other clients |
| **Grant control** | Block access |
| **State** | On |

**Why this matters for SMBs:** Legacy authentication is one of the most exploited attack vectors in retail environments. Older email clients, point-of-sale integrations, and legacy line-of-business apps frequently use basic auth. This policy eliminates that attack surface entirely.

**SMB consideration:** Before enabling, audit your environment for legacy auth sign-ins via Entra sign-in logs filtered by client app type. Enabling without audit can break legitimate business workflows.

---

### **CA-SigninRisk**

Entra ID Protection evaluates every sign-in in real time and assigns a risk level based on signals like impossible travel, anonymous IP, leaked credentials, and atypical sign-in patterns.

| Setting | Value |
|---|---|
| **Users included** | SG-All Users |
| **Users excluded** | SG-EmergencyAccess-Admins |
| **Target resources** | All resources |
| **Condition** | Sign-in risk: High, Medium |
| **Grant control** | Block access |
| **State** | On |

**Why this matters for SMBs:** A risky sign-in from an anonymous IP at 3am is blocked automatically — no security team intervention required. For an SMB without a 24/7 SOC, this is automated incident response.

**Design decision:** Low risk sign-ins are not blocked — only Medium and High. This balances security with user experience, avoiding excessive friction for normal sign-in patterns.

---

### **CA-UserRisk**

User risk reflects the likelihood that a user account itself is compromised — based on signals like leaked credentials found on the dark web, suspicious activity patterns, or confirmed compromises.

| Setting | Value |
|---|---|
| **Users included** | SG-All Users |
| **Users excluded** | SG-EmergencyAccess-Admins |
| **Target resources** | All resources |
| **Condition** | User risk: High, Medium |
| **Grant controls** | Require MFA + Require risk remediation |
| **Session** | Sign-in frequency: Every time |
| **State** | On |

**Why this matters for SMBs:** Rather than simply blocking a potentially compromised user (which creates helpdesk tickets), this policy forces the user to prove their identity with MFA and complete a password reset — remediating the risk in real time.

**Design decision:** "Require risk remediation" forces a secure password change. Combined with "Sign-in frequency: Every time," the user cannot persist a session through a compromised credential.

---

## **Exclusion Strategy**

All policies exclude `SG-EmergencyAccess-Admins` — the security group containing the break-glass account. This is intentional and critical.

| Exclusion | Reason |
|---|---|
| **SG-EmergencyAccess-Admins** | Break-glass accounts must remain accessible if Conditional Access itself is misconfigured or if the identity provider experiences an outage |

**Governance controls on exclusions:**
- All exclusions are documented with business justification
- Exclusion group membership is reviewed quarterly
- Break-glass account sign-in activity is monitored via alert rules
- No individual user accounts are directly excluded — only named security groups

---

## **Naming Convention**

```
CA-[Function]-[Scope]

Examples:
CA-BlockLegacyAuth
CA-MFA-AllUsers
CA-MFA-AllAdmins
CA-RequireCompliantDevice-Admins
CA-SigninRisk
CA-UserRisk
CA-SignInFrequency
```

**Convention principles:**
- All caps prefix `CA-` for easy filtering in policy list
- Function describes what the policy does, not what it prevents
- Scope identifies who it applies to
- No spaces — use camelCase or hyphens only

---

## **Reference Screenshots**

![CA Policy List — All Policies Active](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/CA-Policies.png)

![CA-BlockLegacyAuth — Include Configuration](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/CA-BlockLegacyAuth-1.png)

![CA-BlockLegacyAuth — Exclude Configuration](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/CA-BlockLegacyAuth-2.png)

![CA-BlockLegacyAuth — Client Apps Condition](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/CA-BlockLegacyAuth-3.png)

![CA-SigninRisk — Sign-in Risk Levels](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/CA-SignInRisk-1.png)

![CA-SigninRisk — Conditions](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/CA-SignInRisk-2.png)

![CA-UserRisk — Include Configuration](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/CA-UserRisk-1.png)

![CA-UserRisk — User Risk Levels](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/CA-UserRisk-2.png)

![CA-UserRisk — Grant Controls](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/CA-UserRisk-3.png)

---

*Last updated: May 2026 — Billy Carrie*
