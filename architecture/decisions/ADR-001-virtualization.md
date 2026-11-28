# ADR-001 VMware Workstation for the lab

**Decision date** 2026-11-27

**Status** Owner-approved platform preference, subject to patch, compatibility and performance checks.

## Context and decision

Use the existing VMware Workstation environment for three independent x64 guests. Keep host VBS, Defender and Windows Firewall active. Reuse the existing private adapter. This keeps guest installation, snapshot recovery and NIC inspection close to the handbook and lets the analyst rotate endpoints on a laptop.

Hyper-V remains a possible alternative if Workstation cannot meet the acceptance checks with VBS enabled. Its full management feature set has not been verified. VirtualBox and a Docker/WSL deployment would add a different platform and network model outside the agreed MVP.

## Consequences and review

Phase 02A confirmed Workstation 17.6.2. See the [upgrade guide](../../documentation/setup/vmware-upgrade.md) for the 26H1u1 recommendation, dated vendor advisories, WHP compatibility and network recovery procedure. The owner will authorize any upgrade separately.

On a host with VBS, Workstation may use Windows Hypervisor Platform. In Phase 02, verify ordinary guest boot, snapshot restore, clock stability and responsiveness with representative load. Record peak RAM, paging and CPU during each startup order. If performance is unacceptable, try rotating endpoints and review the platform choice before further changes.

[Broadcom advisory](https://support.broadcom.com/web/ecx/support-content-notification/-/external/content/SecurityAdvisories/0/25390) · [Supported host systems](https://knowledge.broadcom.com/external/article/315653/) · [Broadcom on VBS and Workstation performance](https://knowledge.broadcom.com/external/article/417896).
