# Security Finding & Remediation
## Vendor Account Bypass of CyberArk Authentication via Okta SSO

*Identified and Remediated by Phoenix Boisnier*

---

| | |
|---|---|
| **Finding Type** | Authentication Control Bypass — Vendor Account Access to Privileged Credential Store |
| **Severity** | Critical — Vendors could access environment passwords without CyberArk authentication, bypassing all vendor access restrictions |
| **Status** | Remediated |
| **Timeline** | May 21, 2025 |

---

## Background

During an investigation into password management controls for vendor accounts — specifically evaluating means by which to prevent self-service password resets on CyberArk-managed credentials — a misconfiguration was identified that allowed unintended direct access to the CyberArk privileged access management platform via Okta single sign-on.

## Finding

An Okta group rule was configured to automatically assign users — including external vendors — to an application access group granting them a CyberArk tile in the Okta dashboard. By launching CyberArk through this SSO tile, affected users could authenticate into the platform without completing the CyberArk-native authentication flow.

This bypass had the following material consequences:

- Vendors could access assigned environment-specific passwords stored in CyberArk without going through the CyberArk authenticator, which is the primary control point for vendor session governance.
- Vendor access restrictions enforced at the CyberArk authentication layer were rendered ineffective for users accessing the platform via the Okta SSO path.

The root cause was an overly broad group rule that assigned CyberArk application access without accounting for the authentication control implications of SSO-based entry.

## Remediation

Remediation was executed in a sequenced approach to minimize risk while eliminating the access path:

1. The overly broad application access group was removed from the CyberArk application assignment in Okta. The application retains its intended privileged user groups, preserving access for authorized administrators.

2. A manual membership review of the access group was performed to identify and remove extraneous users, including all non-internal accounts.

3. The group rule driving automatic assignment was deleted, eliminating the automated re-population of the access group.

No communication plan was required, as the remediation addressed a security control gap rather than a functional change for authorized users.

## Verification

Post-remediation, CyberArk application access in Okta is limited to explicitly authorized privileged user groups. Vendor accounts no longer have an SSO path into the platform and are subject to the full CyberArk authentication flow for any access. Manual review confirmed no residual extraneous memberships remain in the access group.

---

**Impact:** This finding was surfaced as a secondary discovery during unrelated privileged account research, illustrating the value of broad-scope awareness during identity security work. Left unaddressed, the misconfiguration would have rendered vendor access controls effectively optional — any vendor with an Okta account and CyberArk tile access could have bypassed session governance entirely. Remediation restored the integrity of the privileged access management boundary without operational disruption to authorized users.
