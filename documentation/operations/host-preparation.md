# Prepare the Windows host

Use this checklist before the first server boot and again before increasing guest concurrency. Phase 02A recorded host observations and prepared deployment instructions. The owner accepted the preparation guides. See the [progress log](../../PROGRESS.md) for newer samples. Recheck capacity immediately before boot and aim for 12–15 GiB available for the first guest.

## Review the starting point

| Evidence on 27 November 2026 (UTC+07) | Value | Decision |
| --- | --- | --- |
| Installed and visible RAM | 32.00 and 30.65 GiB | Use the available-memory counter to choose the session size |
| Available RAM | 11.01 GiB at 16:19, 9.30 GiB at 22:26 | The later sample is 1.70 GiB below the single-server planning target |
| Lab drive E | 682.75 GiB free at 22:26 | The initial 250 GiB lab envelope fits. Check for growth before snapshots or backup |
| Host security at 16:19 | VBS running, WHP installed, Defender antivirus and real-time protection on, all three firewall profiles enabled | Preserve this posture through the hypervisor upgrade |
| VMware | Workstation 17.6.2 build 24409262 on Windows 11 Pro 25H2 build 26200.9974 | Review and approve the [upgrade plan](../setup/vmware-upgrade.md) first |

## Decide how many guests to start

| Workload | Available memory target before booting the group | Shortfall at 22:26 (GiB) |
| --- | ---: | ---: |
| SOC-SRV | 11 GiB | 1.70 |
| SOC-SRV and LIN-ENDPOINT | 13 GiB | 3.70 |
| SOC-SRV and WIN-ENDPOINT | 17 GiB | 7.70 |
| All three | 19 GiB | 9.70 |

Each target allows about 3 GiB above the guests' configured RAM. These are operational starting points. Measure paging, responsiveness, CPU and disk under a representative guest workload before accepting concurrency.

In ordinary PowerShell, sample memory again immediately before boot:

```powershell
$os = Get-CimInstance Win32_OperatingSystem -Property FreePhysicalMemory,TotalVisibleMemorySize
[pscustomobject]@{
    AvailableGiB = [math]::Round($os.FreePhysicalMemory / 1MB, 2)
    VisibleGiB = [math]::Round($os.TotalVisibleMemorySize / 1MB, 2)
}
Get-Process | Group-Object ProcessName | ForEach-Object {
    [pscustomobject]@{
        App = $_.Name
        WorkingSetMiB = [math]::Round(($_.Group | Measure-Object WorkingSet64 -Sum).Sum / 1MB)
    }
} | Sort-Object WorkingSetMiB -Descending | Select-Object -First 8
```

For a low reading, save work, then close selected high-memory browser tabs and unused foreground applications through their own UI. If an emulator is running, the owner can save its state and shut it down in the product that launched it. Working-set sums can include shared pages more than once. Check Available memory again to measure what actually became available. Leave system processes, memory compression and security services running.

The QEMU process observed in the earlier survey was absent from both Phase 02A samples. An Android SDK emulator binary with the same name exists at the standard SDK location. To attribute a future instance, inspect its executable location, publisher/product metadata and parent application locally while it is running. Installing that SDK alone cannot establish the origin of the earlier process. Store process details privately.

## Owner preflight

1. Verify that the [current build and recovery plan](../setup/vmware-upgrade.md) fit the maintenance window. Record VMnet1 and VMnet8 settings and secure recovery material outside the Git checkout before changing host drivers.
2. Select the [official media](../setup/iso-preparation.md). Record the publisher's verification results in the private inventory after download. One Ubuntu ISO serves both Linux guests.
3. Observe available RAM and free space on both volumes. Close selected applications if needed. Choose server-only, paired or integrated mode using the table above.
4. Keep VM/ISO/private evidence in their respective sibling directories. Check the E volume against the [storage budget](../../architecture/resource-allocation.md).
5. Reconcile VMnet1 addresses against powered-off guests, active VPN routes and DHCP before assignment. Confirm VMnet1 host-only, no bridge or forwarding, and VMnet8 reserved for temporary maintenance.
6. Once the owner confirms creating or powering on the guest after the upgrade checks, follow the [SOC-SRV runbook](../setup/soc-srv-provisioning.md). Record the recheck time, chosen guest count, post-boot memory pressure and actual results before starting a second guest.
