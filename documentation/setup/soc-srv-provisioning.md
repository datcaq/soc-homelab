# Provision SOC-SRV on VMware Workstation

**Status: Planned for Phase 02B.** Phase 02A prepared this runbook. Boot, installation, network behavior, resource use and restore results are **Not Tested**. Record a UTC timestamp and evidence ID only after completing a check. The first guest provides an Ubuntu baseline for a later Wazuh installation.

## Before the first boot

- Obtain the owner's separate confirmation to create or power on SOC-SRV after the VMware upgrade and media checks. Check that the patched Workstation build, Windows host protections and VMnet configuration passed their preparation checks. Verify the Canonical Ubuntu Server 24.04 LTS amd64 ISO against its authenticated checksum.
- Locate the workspace on E. The planned VM folder is `virtual-machines/SOC-SRV/` and the verified media is in `iso-images/`, both relative to the workspace root and outside the Git checkout. If the target already contains files, pause and identify the existing VM before proceeding.
- Measure host **Available memory** immediately before boot. For the first SOC-SRV boot, prefer 12–15 GiB of available host RAM. The planning thresholds are about 11 GiB for SOC-SRV alone, 13 GiB with Linux, 17 GiB with Windows and 19 GiB for all three. Check that the VM, media, snapshots and working space still fit the 250 GiB budget. Record the sample and chosen concurrency.
- In the Virtual Network Editor, inspect VMnet1 host-only, host address `192.168.175.1/24`, its DHCP pool and VMnet8 NAT settings. Reconcile reserved/static guests, leases, neighbor entries and VPN routes before assigning `192.168.175.10/24`. An empty neighbor cache cannot account for a powered-off static guest. If the address is occupied or routing overlaps, record the conflict and agree a replacement.
- On the host, inspect both IPv4 and IPv6 interface forwarding, applicable route tables, network bridges and Internet Connection Sharing on the physical, VPN and VMware adapters. Confirm VMnet1 has no routed/shared uplink and VMnet8 has no inbound mappings. Keep host VBS, Defender and firewalls enabled.

## Create the virtual machine

1. In Workstation, open **File > New Virtual Machine**. Choose the option to install the operating system later so that the network and storage can be inspected before first boot. Choose Linux and the Ubuntu 64-bit profile supported by the selected build.
2. Enter `SOC-SRV` and browse to the designated VM folder. Use a **new virtual disk**, 60 GiB maximum. Leave **Allocate all disk space now** unchecked if the option appears. Choose UEFI firmware for a fresh installation and keep the chosen firmware mode throughout the guest's lifetime.
3. In **Customize Hardware** or **VM > Settings**, select **1 processor × 4 cores**, **8192 MB RAM**, the verified ISO as the virtual CD/DVD, and **Custom: VMnet1** for the primary NIC. Check its connect-at-power-on box. Review the virtual disk destination and ensure it is a new guest disk.
4. Keep shared folders, drag-and-drop and copy/paste disabled. Remove unused devices from the proposed configuration as appropriate. Leave the VM unbridged. Take note of the primary adapter identity privately so the guest's corresponding interface can be identified later.
5. Start from the VM console. Install from the verified ISO offline where possible. If the installer needs an online mirror or subsequent updates need Internet access, use the temporary maintenance procedure below.

## Install Ubuntu Server and set the lab address

1. **Language and installer update.** Select English for consistent messages in the runbook. Use the installer on the verified media for an offline build. If it offers a newer installer, record the version if you choose it during a maintenance window.
2. **Keyboard.** Select the layout matching the physical keyboard and test the symbols in the provided field before entering credentials.
3. **Installation type.** Select standard Ubuntu Server. Its management tools are useful for the initial baseline. Leave third-party drivers and optional software unselected unless a documented guest requirement calls for them.
4. **Network, proxy and mirror.** Edit the VMnet1 interface using manual IPv4: subnet `192.168.175.0/24`, address `192.168.175.10` once checked. Leave gateway, DNS, search domain and proxy blank on the isolated interface. Choose offline installation if offered. For repository access use the separate temporary VMnet8 procedure below.
5. **Guided storage and confirmation.** Select only the new 60 GiB guest disk. Review the target name, capacity and partitions before accepting the destructive confirmation inside the guest. With LVM, check how much space the root filesystem will actually receive and record the available volume-group space.
6. **Profile.** Enter hostname `soc-srv`. The owner types a dedicated local administrator username and a unique password at the VM console and keeps them private.
7. **Ubuntu Pro, SSH and featured server snaps.** Skip Pro unless the owner has an intended subscription. Console access is enough for installation. Select OpenSSH only for the planned host-to-guest administration path and follow the key/firewall checks below. Leave featured snaps unselected. Wazuh is a later phase.
8. **Finish and reboot.** Wait for installation to finish, choose Reboot Now, and disconnect the ISO from the virtual CD/DVD when prompted. Press Enter, boot from the virtual disk, and sign in at the console. Record the output of `cat /etc/os-release`, `lscpu`, `free -h`, `lsblk` and `df -h /`.
9. Identify the lab interface with `ip -br link` and compare it with VMware's adapter settings. Review the installer-generated configuration with `sudo netplan get`. For an adjustment, use the console, save the current guest configuration privately, and edit the existing interface definition. Substitute its actual name in the example below.

[Canonical installer screen guide](https://canonical-subiquity.readthedocs-hosted.com/en/latest/tutorial/screen-by-screen.html)

```yaml
network:
  version: 2
  ethernets:
    LAB_INTERFACE_NAME:
      dhcp4: false
      dhcp6: false
      accept-ra: false
      addresses:
        - 192.168.175.10/24
```

Keep host-side and guest-side forwarding off. Preserve unrelated definitions, keep any temporary DHCP client on its own NIC and avoid overlapping Netplan definitions for the same interface. Check `sudo netplan generate`, then use `sudo netplan try` from the VMware console. Confirm only after checking the address, host path and routing. On a timeout or unexpected result, inspect the live state and saved files from the console and restore the known-good configuration before reboot. [Ubuntu installation](https://ubuntu.com/server/docs/tutorial/basic-installation/) · [Netplan trial and rollback caveats](https://netplan.readthedocs.io/en/stable/netplan-try/)

## Temporary maintenance network

Power off if the operating procedure requires it. Add a **separate** custom NIC on VMnet8, configure DHCP on that guest interface and inspect its addresses, DNS and default routes. Keep the original lab NIC on VMnet1. Use the maintenance connection only for approved package metadata, updates or installation. Review pending packages and use the official Ubuntu repositories. Reboot after updates when required and record the installed release and kernel.

When maintenance ends, disconnect VMnet8 and clear **Connect at power on**, or remove that temporary adapter while powered off. Eject installation media. Recheck guest routes and resolver state immediately and again after a cold boot. An enabled NAT service on the host is expected for maintenance. Guest NIC attachment and routing determine whether this VM has egress. [VMware networking modes](https://knowledge.broadcom.com/external/article/309842/)

## Optional SSH, guest firewall and time

- The VMware console is sufficient for this baseline. For optional SSH, use a dedicated passphrase-protected client key on the analyst host and place only its **public** key in the lab user's authorized keys in the guest. Use owner-only permissions on the guest's SSH directory and authorized keys. Verify the server host-key fingerprint at the guest console before trusting the first connection.
- Apply the firewall from the **VMware console**. Inspect `sudo ufw status verbose`, existing rules and whether UFW covers IPv6. For a fresh guest use deny-by-default incoming and allow outgoing, subject to the isolated routing policy. If enabling SSH, add only the source/interface exception before enabling UFW. Example, after replacing the interface placeholder: `sudo ufw allow in on LAB_INTERFACE_NAME from 192.168.175.1 to 192.168.175.10 port 22 proto tcp`. Confirm enabled status and the effective IPv4/IPv6 rules. Guest ports 443, 1514, 1515, 9200 and 55000 have no baseline service requirement.
- With the console available, verify key-based login from the host in a **second** session. If restricting SSH to keys, validate the effective authentication policy with `sudo sshd -T`, check syntax with `sudo sshd -t`, reload the service and test a fresh session before closing the existing one. Record only the result. Keep recovery access through the console.
- Use UTC in the guest and record the clock source. Inspect `timedatectl status` and `date -u`. If needed, set the guest timezone with `sudo timedatectl set-timezone Etc/UTC`. Use one intended continuous clock discipline. If using network time during maintenance, confirm it actually synchronized. After NAT is removed, compare the guest's UTC time to the synchronized host before and after each session and after snapshot restore. Record offset and uncertainty. Resynchronize before interpreting alert latency if clock uncertainty would affect the 60-second target. [Ubuntu firewall](https://ubuntu.com/server/docs/how-to/security/firewalls/) · [OpenSSH](https://ubuntu.com/server/docs/how-to/security/openssh-server/) · [Time synchronization](https://ubuntu.com/server/docs/how-to/networking/timedatectl-and-timesyncd/)

## Acceptance checks and private evidence

Execute from the stated vantage point with only the intended NIC attached. Inspect the actual guest routing rules and all relevant tables, including more-specific and VPN routes. Save timestamps, high-level results and selected metrics under sibling `local-data/`. Remove usernames, hostnames outside the lab, public addresses, hardware identifiers and keys from any public summary.

| Check | Method and expected evidence | Result |
| --- | --- | --- |
| Guest identity and addressing | From console, run `ip -br addr`, `ip -4 rule`, `ip -6 rule`, `ip -4 route show table all`, `ip -6 route show table all` and `resolvectl status`. Confirm VMnet1 owns the agreed address and that an unintended NIC, default route, resolver or router advertisement has not survived maintenance | Not Tested |
| Host-only access | Check `ip -4 route get 192.168.175.1` resolves via the lab NIC. If SSH is enabled, from Windows run `Test-NetConnection 192.168.175.10 -Port 22` and verify authorized key login. With console-only management, demonstrate IP reachability using an already permitted local exchange or bidirectional neighbor resolution and record its scope | Not Tested |
| External IPv4 and IPv6 | From the guest run `ip -4 route get 192.0.2.1` and `ip -6 route get 2001:db8::1`. Expect no usable route. These are documentation addresses and route lookups send no traffic. Repeat for a representative **actual** physical LAN address and an off-lab destination per available address family. A link-local IPv6 address alone is normal. Check Windows forwarding for both families, ICS, adapter mode and port forwards alongside the guest results | Not Tested |
| Denied paths | If the lab has approved, known-listening destinations outside VMnet1, establish a positive control from an authorized vantage point and make one bounded connection attempt for each applicable family from the isolated guest. Expect no connection. Record IPv6 or external reachability as unverified where an appropriate control is unavailable. A timeout from an unknown listener has limited evidentiary value | Not Tested |
| Guest exposure | Inspect `ss -lntup` and effective UFW policies from the console. Permit SSH only when selected. Verify an unauthorized lab peer cannot use SSH when a second guest later exists. Validate Wazuh service flows after those services are deployed | Not Tested |
| Resources and clock | Record host available RAM, CPU, sustained paging and disk I/O before boot and during a 15-minute session. In the guest use `free -h`, `vmstat 1 10`, `df -h` and UTC clock comparison. Record peak load and impact on host responsiveness. Document SIEM workload results separately in its deployment phase | Not Tested |

## Baseline snapshot and rollback

1. After the above checks, shut down the guest cleanly. Confirm the ISO is ejected, only the lab NIC will reconnect on boot and free space on E can absorb snapshot growth.
2. In Snapshot Manager take a **powered-off** baseline such as `soc-srv-os-baseline-YYYYMMDD`. Record the UTC time, guest version, verified ISO digest, NIC mode, address, firewall status and pending checks in the private inventory. Use the name of a checked baseline only when its evidence exists.
3. Before relying on restoration, plan a disposable marker file in the guest. Create it **after** taking the snapshot. Power off, preserve any valuable new evidence separately, and use Snapshot Manager to revert to the named baseline. Revert discards later guest changes. Verify the marker is absent, expected OS/configuration remains, the clock is correct and the isolation checks still pass.
4. Keep a separate private copy of essential configuration and recovery notes. A VMware snapshot depends on its parent virtual disk. Manage the chain through Workstation and account for disk growth before creating further snapshots.

## Troubleshooting

| Symptom | Next check and recovery |
| --- | --- |
| VM fails to start or host begins sustained paging | Compare current headroom with 11 GiB, check the selected patched build and its VBS-compatible mode, then retry with only SOC-SRV after the owner frees capacity. Preserve host security settings |
| Installer cannot reach a mirror | Confirm the intended mode. Continue offline if supported, or attach the temporary VMnet8 NIC and inspect DHCP, DNS and default route there. Disconnect it after updates |
| Host cannot reach SOC-SRV | Open the VMware console. Check VMnet selection, guest address/prefix, duplicate address, host adapter, listening service and firewall source rule |
| Guest has external routing while isolated | Stop the acceptance run. Disconnect any temporary/bridged NIC, check autoconnect, host sharing/forwarding, DHCP and IPv6 RA. Repeat the IPv4 and IPv6 checks before accepting the guest |
| SSH key login fails after a change | Use the console to check host key identity, public key ownership/permissions, listener, firewall and effective sshd configuration. Restore the backed-up guest configuration if needed |
| Clock drifts after isolation or restore | Compare UTC with the host, identify the active provider and use the approved time source during maintenance. Recheck after returning to host-only mode |
| Disk space differs from the planned size or snapshot grows | Inspect virtual capacity, guest partitions/LVM, filesystem free space and E headroom separately. Pause further snapshots until the owner reviews actual usage |

Record the execution date, results, authorized exceptions, redacted evidence references and unresolved checks before accepting the first guest. See [the network design](../../architecture/network-design.md) and [the resource budget](../../architecture/resource-allocation.md).
