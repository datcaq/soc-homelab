# VMware Workstation upgrade and compatibility guide

**Decision reviewed on 27 November 2026.** Select **VMware Workstation Pro 26H1u1 for Windows x64** for this lab. Broadcom's November remediation matrix still points to this release. Check the portal and advisories again at download time. If the vendor has superseded it, record the newer release, its fixes and supported host before proceeding. Installation, retained settings, guest boot, performance and rollback are **Not Tested**.

## Host and vendor assessment

| Item | Verified evidence | Deployment decision |
| --- | --- | --- |
| Installed hypervisor | The local Workstation executable reports 17.6.2 build 24409262 | Replace this build before powering on a lab VM |
| Windows host | Windows 11 Pro x64 25H2, build 26200.9974, on an AMD Ryzen 7 PRO 8840U. Broadcom KB 315653 lists Windows 11 for 26H1u1 at OS-family granularity | Qualify this exact Windows revision and driver combination with a guest boot and networking checks |
| Hypervisor and host protections | Ordinary-user CIM reads returned HypervisorPlatform install state 1 (enabled), HypervisorPresent true, and VirtualizationBasedSecurityStatus 2 (running) | Keep Windows Hypervisor Platform (WHP), VBS, Defender and Firewall enabled |
| Execution mode | Broadcom documents WHP for supported Workstation versions on Hyper-V/VBS hosts. Its 25H2 generation introduced visibility into Hyper-V/WHP mode. Guest performance depends on this path and host load | After installation, inspect Workstation's running mode, measure SOC-SRV first, and record responsiveness, paging and I/O before a second guest |
| Support scope | 26H1u1 is the newest fixed Workstation release verified in the reviewed Broadcom advisories and has Windows 11 host coverage. The free edition uses community/self-service support | At upgrade time consult Product Lifecycle for formal support dates. Listing an older release in the OS matrix does not establish ongoing security maintenance |

[Host support matrix](https://knowledge.broadcom.com/external/article/315653/) · [Broadcom WHP requirements](https://knowledge.broadcom.com/external/article/315420/) · [Broadcom performance and execution-mode guidance](https://knowledge.broadcom.com/external/article/417896/) · [Desktop hypervisor FAQ](https://www.vmware.com/docs/desktop-hypervisor-faqs) · [Product lifecycle](https://support.broadcom.com/group/ecx/productlifecycle).

### Security evidence for the version choice

| Broadcom bulletin | Workstation-specific finding |
| --- | --- |
| [VMSA-2025-0004](https://support.broadcom.com/web/ecx/support-content-notification/-/external/content/SecurityAdvisories/0/25390) and [VMSA-2025-0013](https://support.broadcom.com/web/ecx/support-content-notification/-/external/content/SecurityAdvisories/0/35877) | Their 17.x rows give 17.6.3 and 17.6.4 respectively as fixed versions. Both fixes postdate the installed 17.6.2 |
| [VMSA-2026-0007, 3 September](https://support.broadcom.com/web/ecx/support-content-notification/-/external/content/security-advisories/0/38288) | The Workstation row covers 25H2 and 26H1. Its fix for CVE-2026-59346 and CVE-2026-59347 is 26H1u1 |
| [VMSA-2026-0011, 24 November](https://support.broadcom.com/web/ecx/support-content-notification/-/external/content/SecurityAdvisories/0/39626) | The Workstation 25H2 row names CVE-2026-123912 and CVE-2026-123916 and again specifies 26H1u1. The advisory covers other products with their own fixes |

The two 2026 rows name specific affected release families. The recommendation for this 17.6.2 installation rests on its directly documented 17.x exposure and the current supported Windows target. Check the selected file's exact build and hash in the portal. The advisory and compatibility matrix were accessible during review. The separate full release-notes page returned HTTP 403 to the documentation reader.

## Owner procedure for the approved maintenance window

1. **Prepare recovery.** Save work, shut down existing VMware guests normally, exit Workstation and schedule a possible reboot. Locate any VMs outside this project. Back up their powered-off files and private encryption/recovery material separately. Ensure a tested host recovery image or equivalent rollback path is available before changing network drivers.
2. **Capture both virtual networks privately.** In Edit > Virtual Network Editor, record VMnet1 and VMnet8 types, subnets, host adapters, DHCP pools, NAT gateway, port forwards and any custom networks. If the editor offers Export, save the settings outside Git. Also keep dated, private copies of the relevant existing files under `%ProgramData%\VMware`, including `netmap.conf`, `vmnetdhcp.conf` and `vmnetnat.conf`. Capture host routes for comparison. These records permit reconstruction if driver replacement changes adapters, addresses or interface indices. [Network design](../../architecture/network-design.md)
3. **Obtain the package.** Open the Broadcom Support Portal, sign in to the owner's account if requested and select **VMware Workstation Pro → 26H1 for Windows → 26H1u1**. Check platform, release, filename, size, SHA-256 and release notes in the portal. Keep installers and verification evidence outside the checkout. [Official download selection](https://support.broadcom.com/group/ecx/productdownloads?subfamily=VMware%20Workstation%20Pro&freeDownloads=true) · [Portal instructions](https://knowledge.broadcom.com/external/article/368734/) · [Release notes](https://techdocs.broadcom.com/us/en/vmware-cis/desktop-hypervisors/workstation-pro/26H1/release-notes/vmware-workstation-pro-26h1u1-release-notes.html)
4. **Verify the Windows installer.** Use the read-only PowerShell checks below with the exact file selected. Require a trusted, valid Authenticode signature from the expected VMware/Broadcom publisher and a SHA-256 match to the official record for that file. A valid signature from another publisher or a digest mismatch is grounds to stop. Resolve discrepancies through the portal before launch. [Microsoft Authenticode verification](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.security/get-authenticodesignature)
5. **Run the vendor installer only after the owner authorizes host changes.** Use the guided maintenance/upgrade path supported by the selected installer. Retain product configuration when offered. 26H1 moved the Windows application to 64-bit. Review installation paths and existing VM library paths. If setup requires removal of an earlier version or a restart, first verify private backups and authorize that operation. Keep existing guests at their original virtual hardware version until the upgrade and recovery checks pass. [Broadcom 26H1 architecture change](https://blogs.vmware.com/cloud-foundation/2026/05/14/announcing-vmware-workstation-and-fusion-26h1/)
6. **Compare before any guest boot.** Verify Help > About, file version and build. Confirm VBS remains running, WHP enabled, Defender active and host Firewall enabled. Compare VMnet1 host-only, its host address 192.168.175.1/24 and DHCP .128–.254 against the saved values. Compare VMnet8 NAT subnet, gateway, DHCP pool and zero incoming mappings with the private baseline. Verify both adapters, routing and sharing state. A matching VMnet label alone cannot validate the isolation policy.
7. **Qualify in Phase 02B.** With sufficient measured RAM, boot SOC-SRV on VMnet1 only. Confirm guest clocks and IPv4/IPv6 routes, host-to-guest access and isolation using the provisioning checks. In a separate maintenance test, confirm temporary VMnet8 outbound access, disconnect that NIC and verify isolation again. Record the selected build, UTC check time, observed execution mode, issues and actual resource measurements.

```powershell
$packagePath = Read-Host 'Full path to the downloaded Workstation installer'
$expectedHash = (Read-Host 'SHA-256 from the matching Broadcom portal entry').Trim()
if ($expectedHash -notmatch '^[0-9a-fA-F]{64}$') { throw 'Expected a 64-character SHA-256 digest' }
$signature = Get-AuthenticodeSignature -LiteralPath $packagePath
$signature | Select-Object Status, StatusMessage,
    @{Name='Publisher'; Expression={$_.SignerCertificate.Subject}},
    @{Name='TimestampSigner'; Expression={$_.TimeStamperCertificate.Subject}}
if ($signature.Status -ne 'Valid') { throw 'Installer signature validation failed' }
if ((Get-FileHash -LiteralPath $packagePath -Algorithm SHA256).Hash -ine $expectedHash) {
    throw 'Installer digest differs from the official portal entry'
}
```

Review the displayed publisher identity before accepting the installer prompt. Record the version, build and verification outcome in the [version register](../versions.md) after the owner performs these checks.

## Recovery decisions

| Failure | Owner action |
| --- | --- |
| Installer failure or reboot pending | Keep guest VMs off. Save the exact error and installer log privately. Check for a pending restart and closed VMware applications. Use the vendor-supported repair path for the selected patched build |
| VMnet1/VMnet8 mismatch or missing adapters | Keep guests disconnected. Compare saved settings first. With owner approval, restore the saved compatible network export when available or recreate the specific differences in Virtual Network Editor. Recheck DHCP, NAT, forwarding and host routes. Restore Defaults can overwrite unrelated networks |
| Host instability or broken connectivity | Save the failure evidence. With approval, recover the known-good host image or use the vendor-supported uninstall/reinstall procedure to regain a stable host, then restore private network settings and retest. Keep lab guests powered off if recovery returns the vulnerable 17.6.2 build |
| Poor performance with VBS enabled | Inspect available RAM, paging, I/O and competing workloads, then try the single-guest plan. Escalate unresolved host/driver compatibility for review with the protections enabled |

VM snapshots capture guest state. The owner needs the separate host and network recovery material to undo a hypervisor or virtual adapter change.
