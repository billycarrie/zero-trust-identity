# **Identity Risk Policies**

> Sign-in risk and user risk Conditional Access policy design for the Carrie Cares Entra ID P2 tenant — automated identity threat response without a 24/7 SOC.

---

## **Overview**

Entra ID Protection evaluates every sign-in and user account in real time using Microsoft's global threat intelligence. It assigns risk scores based on hundreds of signals — impossible travel, anonymous IP addresses, leaked credentials, atypical sign-in behavior, and more.

For an SMB without a dedicated security operations center, risk-based Conditional Access policies provide automated incident response at scale. When Entra detects a risky sign-in, the policy acts — no analyst required.

---

## **How Risk Scores Work**

| Risk Type | What It Measures | Examples |
|---|---|---|
| **Sign-in risk** | Likelihood that the specific authentication attempt is compromised | Anonymous IP, impossible travel, unfamiliar sign-in properties, malware-linked IP |
| **User risk** | Likelihood that the user account itself is compromised | Leaked credentials found on dark web, confirmed compromise, suspicious activity patterns |

| Risk Level | Threshold | Policy Action in Carrie Cares |
|---|---|---|
| **High** | Strong evidence of compromise | Block (sign-in) / Require MFA + Remediation (user) |
| **Medium** | Moderate evidence of compromise | Block (sign-in) / Require MFA + Remediation (user) |
| **Low** | Minor anomaly detected | No action — monitored only |
| **No risk** | Normal sign-in | No action |

---

## **CA-SigninRisk**

Responds to risky authentication attempts in real time — before access is granted.

| Setting | Value |
|---|---|
| **Policy name** | CA-SigninRisk |
| **Users included** | SG-All Users |
| **Users excluded** | SG-EmergencyAccess-Admins |
| **Target resources** | All resources (All cloud apps) |
| **Condition** | Sign-in risk: High, Medium |
| **Grant control** | Block access |
| **Session** | Not configured |
| **State** | On |

**Design rationale — why Block instead of Require MFA?**

For sign-in risk, blocking is appropriate because the authentication attempt itself is suspect. Requiring MFA on a high-risk sign-in from an attacker's device simply means the attacker needs to complete MFA — which they may already have access to via session hijacking or SIM swapping. Blocking stops the attempt entirely.

**What happens to legitimate users caught by this policy?**

A legitimate user triggering a false positive (e.g. traveling internationally) will be blocked and need to contact the helpdesk. This is an accepted trade-off — the friction of a helpdesk call for a legitimate user is significantly less damaging than an undetected compromise.

---

## **CA-UserRisk**

Responds to accounts that show signs of long-term compromise — forcing remediation rather than simply blocking.

| Setting | Value |
|---|---|
| **Policy name** | CA-UserRisk |
| **Users included** | SG-All Users |
| **Users excluded** | SG-EmergencyAccess-Admins |
| **Target resources** | All resources (All cloud apps) |
| **Condition** | User risk: High, Medium |
| **Grant controls** | Require MFA + Require risk remediation |
| **Session** | Sign-in frequency: Every time |
| **State** | On |

**Design rationale — why Require risk remediation instead of Block?**

For user risk, the account may be compromised but the user themselves may be legitimate. Blocking entirely prevents the real user from accessing their account to fix the problem. "Require risk remediation" forces the user to complete MFA and change their password — which clears the risk and restores normal access.

**Why Sign-in frequency: Every time?**

A compromised user with an active session could persist access even after the risk policy triggers. "Every time" forces re-authentication on every new resource access — eliminating the value of a stolen session token.

**The two grant controls work together:**

| Control | Purpose |
|---|---|
| Require MFA | Proves the person accessing the account is the legitimate user |
| Require risk remediation | Forces password reset — removes the compromised credential from the attacker's hands |

---

## **Risk Policy vs. Identity Protection Policy**

Entra ID offers two ways to configure risk-based controls — Conditional Access policies (used here) and the legacy Identity Protection risk policies. The Conditional Access approach is recommended:

| Dimension | CA Risk Policy (this repo) | Legacy ID Protection Policy |
|---|---|---|
| Control granularity | Full CA policy controls | Limited |
| Exclusion management | Group-based, governed | Limited |
| Audit trail | Full CA sign-in log integration | Separate log |
| Microsoft recommendation | ✅ Current best practice | Legacy — being deprecated |

---

## **Monitoring and Tuning**

Risk policies require ongoing monitoring, especially in the first 30-60 days after enablement.

```
Entra ID → Protection → Identity Protection → Risky sign-ins
→ Review flagged sign-ins weekly
→ Confirm or dismiss risk detections
→ Dismissed detections improve future model accuracy

Entra ID → Protection → Identity Protection → Risky users
→ Review flagged accounts
→ Confirm compromise or dismiss false positives
→ Reset passwords for confirmed compromised accounts
```

**Tuning indicators:**

| Signal | Action |
|---|---|
| High volume of false positives | Review sign-in patterns — consider named locations for known IPs |
| Legitimate users blocked frequently | Audit if specific use cases (VPN, travel) need accommodation |
| No risk detections ever | Verify policies are On and correctly scoped — not a normal outcome |
| Risk detections not resolving | Confirm risk remediation grant control is configured correctly |

---

## **SMB Considerations**

| Challenge | Approach |
|---|---|
| No helpdesk to handle false positives | Document a self-service remediation process for users — password reset portal |
| No SOC to review risk detections | Weekly review of risky sign-ins by IAM owner — calendar block recommended |
| Users traveling frequently | Configure named locations for known office IPs to reduce false positives |
| Risk policies blocking shared accounts | Shared accounts should not exist — this is a JML process finding, not a risk policy exception |

---

## **Reference Screenshots**

![CA-SigninRisk — Policy Overview and User Scope](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/CA-SignInRisk-1.png)

![CA-SigninRisk — Sign-in Risk Levels High and Medium](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/CA-SignInRisk-2.png)

![CA-UserRisk — Policy Overview and User Scope](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/CA-UserRisk-1.png)

![CA-UserRisk — User Risk Levels High and Medium](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/CA-UserRisk-2.png)

![CA-UserRisk — Grant Controls and Risk Remediation](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/CA-UserRisk-3.png)

---

*Last updated: May 2026 — Billy Carrie*
