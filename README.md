# **Zero Trust Identity**

> Conditional Access policies, PIM configurations, and identity-focused Zero Trust architecture designed and documented in a live Microsoft Entra ID P2 sandbox.

---

## **What This Covers**

| Area | Description |
|---|---
| [**Conditional Access Design**](docs/conditional-access-design.md) | Policy framework, named locations, device compliance integration | 
| [**Break-Glass Strategy**](docs/break-glass-strategy.md) | Emergency access accounts — design, protection, monitoring |
| [**PIM Configuration**](docs/pim-configuration-guide.md) | Just-in-time access, activation requirements, approval workflows | 
| [**Exclusion Management**](docs/exclusion-management.md) | How to manage CA exclusions without creating governance gaps | 
| [**Identity Risk Policies**](docs/identity-risk-policies.md) | ID Protection — sign-in risk, user risk, remediation flows | 

---

## **Repo Structure**

```
zero-trust-identity/
│
├── README.md
│
├── docs/
│   ├── conditional-access-design.md
│   ├── break-glass-strategy.md
│   ├── pim-configuration-guide.md
│   ├── exclusion-management.md
    └── identity-risk-policies.md
└── 
```

---

## **Core Principle**

Zero Trust is not a product, it is a strategy. Identity is the control plane. Every policy, configuration, and automation in this repo is designed around one principle: **never trust, always verify** — with explicit verification, least privilege access, and assumed breach as the baseline.

---

## **SMB Context**

Built in a live Entra ID P2 sandbox simulating **Carrie Cares** — Configurations reflect the reality of organizations encountered in M&A work — limited IT resources, inconsistent baseline controls, and the need to build Zero Trust incrementally without disrupting operations.

---

## **Related Repos**

| Repo | Focus | Status |
|---|---|---|
| [nhi-lifecycle-mgmt](https://github.com/billycarrie/nhi-lifecycle-mgmt) | Non-Human Identity governance | ✅ Complete |
| [entra-id-playbook](https://github.com/billycarrie/entra-id-playbook) | Entra ID operational runbooks | 🚧 In progress |
| [powershell-iam-toolkit](https://github.com/billycarrie/powershell-iam-toolkit) | IAM automation scripts | 📋 Planned |
| [iam-program-framework](https://github.com/billycarrie/iam-program-framework) | IAM program design templates | 📋 Planned |
| [zero-trust-identity](https://github.com/billycarrie/zero-trust-identity) | Zero Trust identity architecture | ✅ Complete |

---

## **Author**

**Billy Carrie** — IAM Engineer | Founding M&A Security Engineer

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/billycarrie/)
