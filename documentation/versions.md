# Version register

Capture the exact build, source and date before changing any deployed component. Phase 00 tool versions below were measured on 26 November 2026. Phase 01 verified the clone and reviewed the design against official documentation on 27 November 2026.

| Component | Observed or proposed version | Status and next check |
| --- | --- | --- |
| Windows host | Windows 11 Pro x64 25H2, build 26200.9974 | Observed in Phase 02A |
| VMware Workstation | 17.6.2 build-24409262 | Installed. Workstation Pro 26H1u1 for Windows is recommended. Installation and qualification await owner action |
| Git for Windows | 2.51.1.windows.1 | Observed in Phase 00, clone verified in Phase 01 |
| PowerShell | 7.6.9 and Windows PowerShell 5.1.26100.9818 | Observed |
| Python / pip | 3.13.9 x64 / 25.3 | Observed. Confirm interpreter when testing the offline CLI |
| OpenSSH client | OpenSSH for Windows 9.5p2 | Observed, optional for management |
| Ubuntu guests | Ubuntu Server 24.04.5 LTS amd64 | Local ISO verified on 28 November 2026 against the authenticated Canonical manifest. Guest installation pending |
| Windows guest | Windows 11 x64, licensed Pro or eligible Enterprise Evaluation | Owner chooses entitlement, language and edition. Local media verification pending |
| Wazuh server, indexer, dashboard and agent | Exact package versions pending | Planned. Check same-release central components, compatible agents and package checksums before installation |
| Filebeat | Version compatible with the selected Wazuh installation method | Planned. Record the delivered package version |
| Sysmon | Exact build and XML schema pending | Planned. Verify Microsoft source and supported schema, then record policy revision |

See the [VMware upgrade guide](setup/vmware-upgrade.md) for the security review and the [media guide](setup/iso-preparation.md) for filenames, terms and hashes. Record the actual installed build and local verification result after execution. [Broadcom advisory](https://support.broadcom.com/web/ecx/support-content-notification/-/external/content/SecurityAdvisories/0/25390) · [Broadcom host support matrix](https://knowledge.broadcom.com/external/article/315653/)

Deployment references: [Wazuh quickstart](https://documentation.wazuh.com/current/quickstart.html) · [Microsoft Sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon) · [Windows 11 VM requirements](https://learn.microsoft.com/en-us/windows/whats-new/windows-11-requirements).
