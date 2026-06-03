# Security Finding & Remediation
## Identity Privilege Escalation Path — Okta/Active Directory Integration

*Identified, Tested, and Remediated Phoenix Boisnier*

---

| | |
|---|---|
| **Finding Type** | Privilege Escalation via Identity Platform Misconfiguration |
| **Severity** | High — Potential Domain Admin escalation via chained identity controls |
| **Status** | Remediated |
| **Timeline** | March 9 – March 30, 2026 |

---

## Background

A proactive security assessment was initiated to evaluate whether a lower-privileged identity could traverse available access controls to achieve elevated permissions within the enterprise Active Directory and Okta environments. The assessment was conducted in an isolated test environment using purpose-built test accounts, with no impact to production systems or users.

## Finding

A viable privilege escalation path was identified through a chain of identity platform misconfigurations spanning Active Directory and Okta:

1. A helpdesk-tier AD account, when granted standard Helpdesk group membership, inherited membership in a group granting Okta Application Admin privileges.

2. The Okta Application Admin role was found to permit directory import operations — including the ability to import organizational units (OUs) containing sensitive and privileged accounts — without requiring Okta Super Admin access.

3. Once a privileged OU was imported into Okta, an account with Domain Admin privileges could reset account passwords through the Okta interface, bypassing the AD-level restriction that would otherwise prevent lower-tier accounts from modifying privileged identities.

4. This chain represented a realistic path from a helpdesk-tier compromise to Domain Admin-level access.

## Remediations Applied

- Delegated access to Okta administrative groups in Active Directory was restricted. Helpdesk and Service Center tiers can no longer self-add to these groups; additions now require escalated team involvement.

- The Okta Application Admin role was identified as the permissive vector enabling unauthorized OU imports. Access to the Application Access Active Directory resource was restricted, eliminating the OU import escalation path entirely.

- Okta SSO access groups previously residing in the general AD structure were renamed for clarity and relocated to a dedicated sub-OU, reducing the risk of accidental or unauthorized group membership changes.

- Active Directory groups that influence Okta administrative role membership were removed from automated provisioning pipelines and placed under manual, change-controlled administration, aligning them with the elevated account management process.

## Verification

Following remediation, the escalation path was confirmed eliminated through retesting. The restricted group memberships prevented OU import operations from being performed at the helpdesk tier, and the administrative group changes required manual InfoSec or Systems involvement — closing the self-service escalation vector entirely.

---

**Impact:** Left undetected, this escalation path could have allowed a compromised or malicious helpdesk-tier account to achieve Domain Admin-equivalent access through a sequence of identity platform operations, none of which individually would have triggered conventional alerting. Proactive identification and remediation eliminated this risk before it could be exploited.
