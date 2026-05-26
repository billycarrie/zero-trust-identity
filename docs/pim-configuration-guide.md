# **Privileged Identity Management Configuration Guide**

> Just-in-time privileged access configuration for the Carrie Cares Entra ID P2 tenant — eliminating standing privilege and enforcing approval-based access to the Global Administrator role.

---

## **Why PIM Matters**

Standing privileged access — where an administrator has permanent, always-active elevated roles — is one of the highest risk configurations in any identity environment. If that account is compromised, the attacker immediately has full administrative access with no friction.

Privileged Identity Management (PIM) replaces standing access with **just-in-time** access — administrators request elevated roles when needed, complete MFA and provide justification, and the role expires automatically after a defined window.

| Without PIM | With PIM |
|---|---|
| Admin has Global Admin 24/7 | Admin requests Global Admin only when needed |
| Compromise = immediate full access | Compromise = attacker still needs MFA + approval |
| No audit trail on when privilege was used | Every activation logged with justification |
| No automatic expiry | Role expires after 1 hour — automatically |

---

## **Global Administrator — Role Settings**

| Setting | Configured Value | Rationale |
|---|---|---|
| **Activation maximum duration** | 1 hour | Limits the window of elevated access — forces re-activation for extended tasks |
| **On activation, require** | Azure MFA | Prevents activation from a compromised session without MFA re-challenge |
| **Require justification on activation** | Yes | Creates an audit trail — every activation has a documented business reason |
| **Require ticket information** | No | Ticket system not yet implemented in SMB context — justification text is sufficient |
| **Require approval to activate** | Yes | No administrator can self-approve Global Admin access |
| **Approvers** | SG-PIM-Approver | Designated approval group — prevents single point of failure in approval chain |

---

## **Break-Glass Exception**

The break-glass account (`Drew.Berry@CarrieCares.onmicrosoft.com`) is assigned Global Administrator as a **permanent active** assignment — not eligible. This is intentional.

In an emergency where PIM itself is unavailable, requiring an activation workflow would defeat the purpose of a break-glass account. The trade-off is accepted and mitigated through strict monitoring and offline credential storage.

| Account | Assignment Type | Reason |
|---|---|---|
| Billy Carrie (admin) | Eligible — requires activation | Normal privileged access pattern |
| Drew Berry (break-glass) | Active — permanent | Emergency access — PIM bypass by design |

---

## **Activation Workflow**

When an administrator needs Global Administrator access:

```
Step 1 — Navigate to PIM → My roles → Microsoft Entra roles
Step 2 — Find Global Administrator in Eligible assignments
Step 3 — Click Activate
Step 4 — Complete Azure MFA challenge
Step 5 — Enter justification (e.g. "Configuring new CA policy for security review")
Step 6 — Submit for approval
Step 7 — Approver in SG-PIM-Approver receives notification
Step 8 — Approver reviews justification and approves or denies
Step 9 — Role activates for 1 hour maximum
Step 10 — Role expires automatically — no manual deactivation required
```

---

## **Approval Group — SG-PIM-Approver**

| Field | Detail |
|---|---|
| **Group name** | SG-PIM-Approver |
| **Group type** | Security |
| **Purpose** | Designated approvers for Global Administrator PIM activation requests |
| **Membership** | Named individuals with authority to approve privileged access |
| **Review cadence** | Quarterly — ensure membership reflects current authorized approvers |

**Why a group, not an individual?** A single approver creates a bottleneck. If the approver is unavailable, legitimate activation requests are blocked. A group ensures continuity while maintaining accountability.

---

## **Audit and Monitoring**

Every PIM activation generates an audit log entry. Key events to monitor:

| Event | Action |
|---|---|
| Role activated | Review justification — confirm it matches expected activity |
| Activation denied | Confirm denial was intentional — investigate if unexpected |
| Role settings changed | Immediate investigation — settings changes should be rare and documented |
| Eligible assignment added/removed | Confirm authorized — all assignment changes require documented approval |
| Permanent active assignment added | Escalate immediately — permanent active assignments require explicit governance decision |

```
Entra ID → Identity Governance → Privileged Identity Management
→ Microsoft Entra roles → Audit history
→ Filter by role: Global Administrator
→ Review activation frequency, justifications, and approver actions
```

---

## **SMB Considerations**

PIM in an SMB context requires pragmatic design decisions:

| Challenge | Approach in Carrie Cares |
|---|---|
| Small team — approver may not be available | SG-PIM-Approver group with multiple members |
| No ticketing system | Justification text field is sufficient for audit purposes |
| Break-glass needs guaranteed access | Permanent active assignment — excluded from PIM requirement |
| Limited IT staff for monitoring | Focus monitoring on Global Administrator only — expand as program matures |

---

## **Reference Screenshots**

![PIM — Global Administrator Role Settings](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/PIMConfiguration-Global.png)

![Break-Glass — Permanent Active Global Administrator Assignment](https://raw.githubusercontent.com/billycarrie/zero-trust-identity/main/docs/screenshots/BreakGlassAccount-GA_Role.png)

---

*Last updated: May 2026 — Billy Carrie*
