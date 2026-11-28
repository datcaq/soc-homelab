# Lab inventory

Figures are a dated capacity sample for the design review. The owner keeps the original handbook, VM files, ISO media and private evidence outside the Git working tree.

| Host item | Observation | Source |
| --- | --- | --- |
| Platform | Lenovo ThinkPad T14 Gen 5, x64, Windows 11 Pro | Phase 00 host survey |
| Processor | AMD Ryzen 7 PRO 8840U, 8 physical cores, 16 logical processors | Phase 00 CPU inventory |
| Memory | 32 GiB installed, 30.65 GiB visible, 9.30 GiB available at 22:26 UTC+07 | DIMM inventory and Windows OS memory counters |
| System volume | 556.58 GiB free of 798.34 GiB | Phase 02A at 16:19, previous volume size |
| Lab volume | 682.75 GiB free of 986.61 GiB | Phase 02A at 22:26, previous volume size |
| Firmware virtualization and VBS | Firmware virtualization enabled at initial survey. VBS running and WHP installed in Phase 02A | Host survey |
| Networking | Dedicated VMware host adapter with a separate VMware NAT network | Phase 01 adapter and VMware configuration inspection |
| Guest assets | Three approved guest roles | [Resource allocation](../architecture/resource-allocation.md) |

Measurements are dated 27 November 2026 (UTC+07). Windows reported 11.01 GiB available at 16:19 and 9.30 GiB at 22:26. Recheck volatile counters before boot. Grouped working sets were inspected to explain current memory pressure. Application names, personal paths, network identifiers outside the lab and process details are excluded from this public inventory.

VMnet1 host and proposed guest addresses are listed where needed in the [network design](../architecture/network-design.md). Runtime agent IDs, endpoint connection dates, guest builds and measured VM resource use should be added after deployment and verification.
