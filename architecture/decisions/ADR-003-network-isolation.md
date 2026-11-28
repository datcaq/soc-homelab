# ADR-003 Host-only lab and temporary NAT

**Decision date** 2026-11-27

**Status** Owner approved reuse of VMnet1 following host-side verification. Guest routing and isolation await Phase 02B.

## Decision

Place the SIEM, both endpoints and the analyst's host adapter on VMnet1. Give the lab NIC static addressing without a gateway. Attach VMnet8 only for a maintenance window and disconnect it before a test. The host analyst needs local dashboard access, so a guest-only segment would require a separate management path. A bridged adapter would place lab guests on the physical network.

## Evidence and limits

VMware configuration maps VMnet1 to HostOnly and binds NAT to VMnet8. The proposed guest addresses lie outside VMnet1's DHCP range. Lease, cache and route inspection found no candidate conflict at the time of the survey. Powered-off guests and later VPN routes require a fresh check. Host-only networking retains a path to the host, so host firewall policy and guest sharing settings matter.

Use the [network plan](../network-design.md) to verify isolated and maintenance modes, denied traffic, allowed services and addressing during deployment. A configuration file documents the intended topology. Record runtime observations separately before accepting isolation.

[Broadcom networking types](https://knowledge.broadcom.com/external/article/309842/) · [Wazuh port reference](https://documentation.wazuh.com/current/getting-started/architecture.html).
