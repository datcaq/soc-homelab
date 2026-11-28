# VM and storage allocation

The owner approved a three-guest design on 27 November 2026. Start with SOC-SRV, then add LIN-ENDPOINT, then WIN-ENDPOINT. Keep the SIEM running while rotating endpoints if host memory is tight. The VM numbers identify roles and do not specify startup order.

| Role | Guest OS and workload | vCPU | RAM (GiB) | Maximum guest disk (GiB) |
| --- | --- | ---: | ---: | ---: |
| VM01 SOC-SRV | Ubuntu Server 24.04 LTS x64, manager, indexer, dashboard and Filebeat | 4 | 8 | 60 |
| VM02 WIN-ENDPOINT | Licensed Windows 11 x64 or official evaluation, Security audit, Sysmon and agent | 2 | 6 | 80 |
| VM03 LIN-ENDPOINT | Ubuntu Server 24.04 LTS x64, agent, authentication and FIM | 2 | 2 | 24 |
| Total planned | All three powered on | 8 | 16 | 164 |

Use growable virtual disks. Place guest files in per-role subdirectories under the sibling `virtual-machines/` folder on E. Keep ISO images in the sibling `iso-images/` folder and sensitive exports in the sibling `local-data/` folder. Windows requires a supported CPU, UEFI/Secure Boot capability and vTPM 2.0. Keep VM encryption and recovery material in the owner's private store. [Windows 11 VM requirements](https://learn.microsoft.com/en-us/windows/whats-new/windows-11-requirements)

## What the laptop can support

Windows saw 30.65 GiB of the installed 32 GiB. The 27 November samples ranged from 7.67 to 11.01 GiB available. At 22:26 UTC+07, available memory was 9.30 GiB. Sample Available memory again before startup. As a planning margin, aim for guest RAM plus roughly 3 GiB of additional available host memory (11 GiB for SOC-SRV, 13 for SOC-SRV + Linux, 17 for SOC-SRV + Windows, 19 for all three). These are suggested start points. Verify paging, disk I/O and host responsiveness during actual runs. If the host is under pressure, the owner may free memory or defer the session. Rotating endpoints preserves all six use cases.

Wazuh's quickstart recommends 4 vCPU, 8 GiB and 50 GB of storage for 1–25 agents. The proposed central guest has capacity for a small trial. Actual retention depends on event volume, index growth and usable guest space after the OS. [Wazuh quickstart](https://documentation.wazuh.com/current/quickstart.html)

## Initial E drive budget

| Category | Planned ceiling (GiB) | Operating rule |
| --- | ---: | --- |
| Three base guest disks | 164 | Sum of maximum sizes, allocated as they grow |
| ISO media | 12 | Retain only the chosen Windows and Ubuntu media |
| Snapshot deltas and VM overhead | 32 | Prefer short-lived powered-off snapshots and monitor growth |
| Selected backup data | 32 | Keep guest configuration, recovery instructions and selected exports |
| Evidence and working margin | 10 | Review before each test session |
| Total planning envelope | 250 | Review allocation if any category needs more space |

The 22:26 Phase 02A E reading is 682.75 GiB free. Reserving the envelope on paper leaves 432.75 GiB for other uses. The 250 GiB budget does not cover a full second copy of three fully grown disks. A full-VM backup needs its own measured allocation, preferably on independent storage. Snapshots depend on their base disk. Verify a restore from a selected backup during the infrastructure phase. Include suspend memory and snapshot growth in the review before long sessions.
