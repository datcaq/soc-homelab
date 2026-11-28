# Project tasks

P0 is an upcoming decision or prerequisite. P1 is required for the MVP. Completed records a finished deliverable or owner decision. Verified records an observation or check. Planned and Not Tested identify future actions and outstanding test evidence.

| Task | Priority | Dependency | Status | Acceptance criterion |
| --- | --- | --- | --- | --- |
| INIT-01 Verify remote and clone | P0 | Owner URL | Verified | main tracks origin/main and three original files exist |
| DES-01 Review Phase 01 design | P0 | INIT-01 | Completed | Owner approved the architecture, rollout, seven-day alert retention and 60-second latency target |
| SEC-01 Apply and validate repository exclusions | P0 | INIT-01 | Verified | 32 excluded and 17 allowed sample paths passed. All three tracked files were checked |
| PREP-02A Review preparation guides | P0 | DES-01, SEC-01 | Completed | Owner accepted the four preparation guides and authorized the Phase 02B preflight |
| VM-01 Upgrade and verify Workstation | P0 | DES-01 | Awaiting owner upgrade | Upgrade target approved. Owner runs installer, then recheck build, host protections and both VMnets against the private backup |
| VM-02U Verify Ubuntu Server media | P0 | DES-01 | Verified | On 28 November the local 24.04.5 amd64 server ISO matched the entry in the Canonical-signed checksum manifest |
| VM-02W Select Windows guest media | P1 | DES-01 | Deferred to Windows endpoint phase | Confirm entitlement or evaluation terms and verify the selected file after its download is authorized |
| NET-00 Back up VMware network settings | P0 | DES-01 | Verified | Private copies of four configuration files, two VMnet registry exports and a host snapshot of network settings matched all seven entries in the backup manifest |
| GIT-02B Review four local commits before push | P0 | Owner authorization for commits | Pending owner review before push | Review four commit IDs and their file lists, then obtain explicit push approval |
| NET-01 Recheck address ownership, routes and sharing | P0 | DES-01 | Planned | Reconcile static guests, DHCP and any active VPN before IP assignment |
| VM-03 Create guest baselines in approved order | P0 | VM-01, verified media for the selected guest, NET-01, owner confirmation for VM creation | Planned | Current headroom adequate, guests boot, clocks align and rollback is verified |
| NET-02 Validate network modes | P0 | VM-03 | Planned | Demonstrate intended reachability and denied egress in simulation mode. Test service ports once their services exist |
| SIEM-01 Deploy SIEM | P1 | Accepted 02 | Planned | T-01 services, HTTPS and delivery with first agent verified |
| LOG-01 Connect Linux and Windows | P1 | SIEM-01 platform ready | Planned | T-02 agent activity and four telemetry sources validated |
| DET-01 Validate six use cases and two rules | P1 | LOG-01 | Planned | T-03/T-04 meet per-case controls and live criteria |
| IR-01 Investigate three cases | P1 | DET-01 | Planned | T-05 links each UTC timeline and verdict to evidence |
| PY-01 Test offline reporter | P1 | IR-01 and reviewed input | Planned | T-06 produces two groups, 3 valid, 1 invalid and safe outputs |
| QA-01 Measure and reproduce | P1 | DET-01, IR-01, PY-01 | Planned | Complete T-01–T-07, including resource use, retention, repeat and sample counts |
| PUB-01 Review release | P1 | QA-01, SEC-01 and owner authorization | Planned | Reviewed staged files and README claims, evidence links and authorized publication |

## Private evidence and generated files

Keep raw exports in an excluded private directory. Place reviewed text logs in `sample-logs/sanitized/` and regenerated outputs in `reports/generated/` or `automation/generated/`. See the [active ignore rules](.gitignore). Review staged contents separately before any authorized commit.
