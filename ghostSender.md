# Ghost Sender Vulnerability — Responsible Disclosure and Remediation Summary

**Date:** June 2026

---

## Background

A responsible disclosure was received from an external party reporting that the affected environment's Exchange Online environment was vulnerable to the Ghost Sender misconfiguration — a widely documented issue allowing unauthenticated SMTP spoofing directly to Exchange Online's inbound MX endpoint, bypassing all configured email filtering and rendering SPF, DKIM, and DMARC controls ineffective.

---

## Vulnerability Summary

Ghost Sender exploits the default behavior of Exchange Online when an external MX record is in use. By connecting directly to the tenant's Exchange Online inbound endpoint, an attacker can deliver email from any sender address — internal or external — straight to the recipient's inbox. Standard protections including anti-phishing policies, preset security policies, and Enhanced Filtering for Connectors do not mitigate this behavior without additional configuration.

**Demonstrated impact:** A controlled test confirmed that a spoofed message could be accepted by the inbound endpoint, demonstrating the misconfiguration.

---

## Validation

The security team validated the vulnerability using the following approach:

- Confirmed direct SMTP connectivity to the tenant MX endpoint
- Reproduced delivery of a spoofed email targeting the tenant's Exchange Online inbound endpoint with an arbitrary sender address
- Confirmed that the existing inbound connector was not configured to enforce sender restrictions, leaving the tenant exposed
- Verified that the expected rejection response was not being returned, confirming the absence of effective mitigation

---

## Remediation

Remediation was carried out collaboratively between the security operations team and the messaging infrastructure team, implementing the Microsoft-recommended mitigations.

### Controls Implemented

**Partner Organization Connector (Primary Mitigation)**

The existing inbound connector was reconfigured to apply to all inbound mail using a wildcard domain entry, with IP-based restriction scoped to the organization's authorized email relay provider. This configuration causes Exchange Online to reject any direct-to-MX connection that does not originate from an authorized source.

### Validation of Remediation

Following implementation, both controls were confirmed effective:

- Direct SMTP connections to the tenant MX endpoint from unauthorized sources now return the expected rejection response
- Configuration was validated against published testing guidance

---

## Notes

- Remediation required coordination between security operations and messaging infrastructure teams
- The wildcard domain entry on the inbound connector is the critical configuration element; a connector scoped to specific domains does not provide protection against spoofed senders outside those domains
- Microsoft's Configuration Analyzer does not surface this misconfiguration; proactive validation is required

---

## References

- InfoGuard Labs — Ghost-Sender: Universal Email Spoofing against Exchange Online: https://labs.infoguard.ch/posts/ghost-sender/
- Microsoft TechCommunity — Direct Send vs. Sending Directly to Exchange Online: https://techcommunity.microsoft.com/blog/exchange/direct-send-vs-sending-directly-to-an-exchange-online-tenant/4439865
