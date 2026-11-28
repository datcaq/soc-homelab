# Working on the SOC Home Lab

Use the implementation handbook kept outside Git as the project baseline. The owner communicates in Vietnamese. Write repository documentation, source, code comments, test descriptions, commits and pull requests in English. Explain the purpose of each material change and the evidence needed to verify it.

## Authority and phase boundaries

- Follow the current owner-approved phase. Record changes to scope or architecture for owner review. Before starting the next phase, show its predecessor's deliverables, test results, limitations and open decisions.
- Phase 02B authorizes host inspection, a private VMware network backup, official Ubuntu Server media acquisition and verification, and four local documentation/security commits. The owner reviews the commit log and paths before authorizing a push, runs the VMware installer personally, and separately confirms any VM creation or power-on. Keep Windows media and Wazuh deployment for their later phases.
- Use ordinary user permissions. Obtain specific authorization before elevation, installation/removal, edits to host/VMware/BIOS/network settings or destructive operations. Preserve existing data and respect edits made by the owner.
- Keep host VBS, Defender and firewalls enabled. Use only authorized lab guests, disposable test accounts, harmless activity and lab-owned files. Keep the lab disconnected from NAT during simulations. Use an offline alert export for Python.
- Preserve the handbook, raw evidence, private backup and VM/ISO files outside the checkout. Keep passwords, API tokens, agent enrollment material, host identifiers, private keys and personal logs out of Git. Use a private credential store. Scrub screenshots and review the contents of fixture files before staging.

## Evidence and testing

1. Record the requirement, use case, expected positive and negative result, prerequisites, versions, UTC window and cleanup before a test.
2. Match the endpoint source event to the agent and manager fields. Capture the actual rule ID, query and timestamp when an alert is expected. For a negative test retain proof that the source event arrived and the target rule did not fire within the declared window.
3. Label synthetic fixtures and show which pipeline stage they exercise. Keep measured results, estimates and proposals distinct. Use Planned or Not Tested until evidence exists. Never manufacture logs, screenshots, measurements or a successful run.
4. Document failures and available evidence. Diagnose the source, collection, rule or indexing stage before changing configuration. Back up the affected guest configuration, record rollback and repeat the affected test.
5. Link sanitized evidence IDs to the test record and case. Describe scope, analyst confidence, remaining uncertainty and cleanup. Use ATT&CK only when observed behavior supports the mapping.

## Git and documentation

Clone the verified upstream once and check the remote and branch before editing. Protect existing README, license and ignore rules. Review proposed ignore changes with the owner before applying them. Use focused changes, relative links and reproducible version records. Preserve unrelated changes.

Inspect tracked and untracked files with `git status --short --branch --untracked-files=all` and inspect the actual content of new files. Before an authorized commit, stage named paths only and review `git diff --cached` for content and secrets. Commit and push only on the owner's instruction. Use a short English subject such as `docs: design the lab network`. Confirm remote, branch and visibility before publishing.

Record verified work in [PROGRESS.md](PROGRESS.md), material changes in [CHANGELOG.md](CHANGELOG.md), and open work in [TODO.md](TODO.md). Keep requirements and phase criteria aligned with [PROJECT_SPEC.md](PROJECT_SPEC.md) and [ROADMAP.md](ROADMAP.md).
