# Security Finding & Remediation
## Helpdesk Tier Access to Privileged Account Password Reset — Okta

*Identified, Scoped, and Remediated by Phoenix Boisnier*

---

| | |
|---|---|
| **Finding Type** | Excessive Privilege — Helpdesk Tier Access to Privileged Account Credentials via Okta |
| **Severity** | High — Helpdesk-tier accounts could reset passwords on Domain Admin and Super Admin accounts |
| **Status** | Remediated |
| **Timeline** | March 24 – April 17, 2026 |

---

## Background

During remediation of a separate privilege escalation finding in the Okta and Active Directory integration, a related gap was identified: helpdesk-tier Okta admin accounts retained the ability to perform password resets on sensitive privileged accounts, including Super Admin accounts and Domain Controller Admin accounts. This access was not intentional and represented a residual risk requiring independent remediation.

## Finding

Helpdesk-tier Okta admin accounts were scoped to a resource set that included privileged account types they should not have been able to modify. Specifically, the following account categories were accessible for password reset operations by helpdesk staff:

- Okta Super Admin accounts
- Accounts designated under the Okta Admin Managers user type, including Domain Admin accounts and enhanced administrative accounts

This represented a violation of least-privilege principles and an exploitable path: a compromised or malicious helpdesk account could reset credentials on highly privileged identities, potentially achieving full administrative control over the Okta environment and, by extension, downstream AD-integrated systems.

## Remediation Approach

A proof-of-concept (PoC) was designed and executed using a dedicated resource set restricting helpdesk admin role scope to a defined group explicitly excluding protected account types. The implementation followed a structured iterative testing process to ensure operational continuity for the helpdesk team.

**Implementation Plan**

1. Test accounts were created in Okta and enrolled in the restricted access configuration. Helpdesk staff enrolled test accounts with MFA factors prior to testing.

2. Helpdesk staff used test accounts by default for all Okta tasks during the testing period. Tasks blocked by the new restrictions were logged with notes, then completed using primary accounts, allowing gap analysis without disrupting operations.

3. Each logged restriction was evaluated to determine whether it represented an intended protection or an unintended block. Resource sets were iteratively tuned to permit legitimate helpdesk functions while enforcing the access boundary.

4. Steps 2 and 3 were repeated throughout the testing week in an iterative tuning cycle until the configuration reached a stable, operationally validated state.

**Backout Plan**

Test accounts could be deleted at any point to immediately revert to prior state, with no impact to production accounts or configurations.

## Testing Results & Verification

Testing with helpdesk staff confirmed that the HelpdeskManagedUsers resource set successfully prevented password reset operations on all protected account types while preserving the ability to perform routine helpdesk tasks. Concerns raised during testing were addressed in real time through resource set adjustments.

The PoC was reviewed with Systems leadership and confirmed to eliminate the remaining escalation paths identified across this and the related finding.

---

**Impact:** This finding, surfaced as a secondary discovery during active remediation work, demonstrates the value of thorough scope evaluation during security gap analysis. Unaddressed, this access path would have allowed any compromised helpdesk credential to serve as a stepping stone to full Okta administrative control. The iterative testing methodology ensured operational continuity for the helpdesk team while validating the control boundary prior to production deployment.
