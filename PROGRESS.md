# Verified progress

| Date | Verified observation | Method |
| --- | --- | --- |
| 2026-11-26 | Reviewed handbook, embedded diagram, six use cases, acceptance matrix and appendices. Found the handbook and four sibling directories | Read the Word package and inspected the directory including hidden entries |
| 2026-11-27 | Owner reviewed Phase 00, supplied the upstream URL and approved the three guest allocations, rollout order and 250 GiB budget | Phase 01 project instructions |
| 2026-11-27 | C had 557.49 GiB free, E had 682.79 GiB free. The host had 7.67 GiB RAM available of 30.65 GiB visible | Read-only logical disk and OS counters at 13:17 UTC+07. [Inventory](documentation/inventory.md) |
| 2026-11-27 | The empty destination became a clean clone on main tracking origin/main at 8bf7d99. Tracked baseline contains README, MIT LICENSE and Python ignore rules | Checked remote symbolic HEAD, then local status, remote URLs, file list and log |
| 2026-11-27 | VMware maps VMnet1 to HostOnly and VMnet8 to NAT. Candidate IPs fall outside the VMnet1 DHCP pool. No matching records appeared in the inspected leases or IPv4 neighbor cache | Read adapter, route, lease, DHCP, registry and NAT configuration. [Network design](architecture/network-design.md) |

**2026-11-27 documentation review.** Rendered all three Mermaid diagrams with the installed VS Code preview engine in a local browser. All produced flowchart SVGs. Verified 15 new Markdown files, local file links, FR/NFR/SEC and UC/test identifiers, and an empty staging area. Git showed no change to the original README, license or ignore file. The owner subsequently accepted the Phase 01 design.

**Phase 02A, 2026-11-27.** The owner approved the design and the seven-day retention and 60-second latency targets. At 16:19 and 22:26 UTC+07 Windows reported 11.01 and 9.30 GiB of available RAM, respectively. CIM showed WHP installed and VBS running. Git ignore checks covered 32 excluded and 17 allowed paths and independently checked all three tracked files. The guest and SIEM acceptance tests await deployment.

## Phase 02B preflight

- **27 November 2026.** The owner accepted Phase 02A and authorized four local commits, the private VMware network backup and Ubuntu media verification. The remote main branch and the checkout shared the same starting commit, `8bf7d99`.
- **Network recovery.** The operator saved four VMware configuration files, the VMnet1 and VMnet8 registry exports and a network settings baseline outside Git. The four copies matched their sources. On 28 November all seven backup entries passed a fresh SHA-256 check. External VM inventory and private network details stay in local recovery records.
- **28 November 2026, 10:21 UTC+07.** Windows reported 11.20 GiB available out of 30.65 GiB visible, 4% CPU utilization at sampling, and 678.93 GiB free on the lab volume. Recheck before boot. Aim for 12–15 GiB available for the first guest.
- **Ubuntu installation media.** Verified the detached OpenPGP signature on Canonical's checksum manifest with the published Ubuntu CD Image signing fingerprint. The 4,080,486,400-byte amd64 server ISO matched its exact manifest entry. The [media guide](documentation/setup/iso-preparation.md) records the image, digest and key fingerprint.

The VM creation, guest boot, installation and network isolation tests await owner confirmation and execution. [Open tasks and owner decisions](TODO.md) track the next actions.
