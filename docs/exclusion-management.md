# **Exclusion Management**

> How to manage Conditional Access exclusions without creating governance gaps — framework and standards for the Carrie Cares Entra ID tenant.

---

## **Why Exclusions Are High Risk**

Every Conditional Access exclusion is a deliberate gap in your security policy. Exclusions are sometimes necessary — but they are also one of the most common sources of identity risk in SMB environments, where they are added quickly and never reviewed.

| Common SMB Pattern | Risk |
|---|---|
| Individual user excluded "temporarily" | Exclusion persists for years — becomes permanent by neglect |
| Shared service account excluded from MFA | Account becomes a persistent MFA bypass |
| No documentation on why exclusion was added | No one knows if it is still needed |
| Exclusions added directly to policies | Hard to audit — no central view of all exclusions |

---

## **Exclusion Governance Principles**

### **1. Group-based exclusions only**
No individual user accounts are directly excluded from any CA policy. All exclusions are managed through named security groups.

| Approach | Risk |
|---|---|
| Direct user exclusion | Invisible in policy list — easily missed in audit |
| Security group exclusion | Single group shows all excluded accounts — easy to audit and review |

### **2. Named exclusion groups with defined purpose**

| Group | Purpose | Members |
|---|---|---|
| `SG-EmergencyAccess-Admins` | Break-glass accounts excluded from all policies | Drew Berry (break-glass only) |

### **3. Every exclusion requires documentation**

Before any account or group is added to an exclusion group, the following must be documented:

| Field | Required |
|---|---|
| Account or group being excluded | Yes |
| Policy being excluded from | Yes |
| Business justification | Yes |
| Requestor | Yes |
| Approver | Yes |
| Review date | Yes — maximum 90 days |
| Decommission trigger | Yes |

### **4. Exclusions are temporary by default**
Every exclusion has a defined review date. If the exclusion is not reaffirmed at review, it is removed.

---

## **Current Exclusion Register**

| Excluded Group | Policy | Justification | Review Date | Owner |
|---|---|---|---|---|
| SG-EmergencyAccess-Admins | All CA policies | Break-glass emergency access — must be reachable if CA is misconfigured | Quarterly | Billy Carrie |

---

## **Exclusion Request Process**

```
Step 1 — Requestor submits exclusion request with justification
Step 2 — IAM Engineer reviews: Is the exclusion necessary?
          Could the requirement be met another way?
Step 3 — If approved: Add account to named exclusion group
          Document in exclusion register
          Set review date (maximum 90 days)
Step 4 — At review date: Reaffirm or remove exclusion
Step 5 — If removed: Remove from exclusion group
          Update exclusion register with removal date and reason
```

---

## **Quarterly Exclusion Review Checklist**

| Check | Action if Failed |
|---|---|
| All exclusions have documented justification | Document or remove immediately |
| All exclusion groups have named owners | Assign owner or remove group |
| No individual user accounts directly excluded | Move to group-based exclusion or remove |
| SG-EmergencyAccess-Admins contains only break-glass accounts | Remove any non-break-glass members |
| All exclusions still have a valid business need | Remove any that no longer apply |
| Review dates are current | Update or remove stale exclusions |

---

## **What Good Exclusion Management Looks Like**

A well-governed exclusion posture has three characteristics:

**1. Minimum exclusions** — the fewer exclusions that exist, the smaller the attack surface. Every exclusion request should be challenged: is there a way to meet the business need without excluding from the policy?

**2. Central visibility** — all exclusions are managed through named security groups, not individual user assignments. One group = one view of all excluded identities.

**3. Active review cadence** — exclusions are not permanent. Every exclusion has an expiry or review trigger. Exclusions that survive longer than 90 days without reaffirmation are removed automatically.

---

*Last updated: May 2026 — Billy Carrie*
