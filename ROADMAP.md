# Implementation roadmap

Advance after the owner reviews the preceding phase. The handbook's 21 study days are a pacing suggestion. Use the acceptance records to determine the actual schedule.

| Phase | Goal and work | Dependencies | Deliverables | Definition of done |
| --- | --- | --- | --- | --- |
| 00 Project initialization | Read handbook and survey workspace, hardware, software and Git | Existing workspace and handbook | Feasibility report | Owner reviews findings and selects target roles, platform and budget |
| 01 Repository, architecture and network | Clone upstream, refresh capacity, inspect VMware, design topology and tests | 00 and owner-supplied URL | Management documents, three diagrams, three ADRs, inventory, version register and proposed ignore rules | Verify clone, documentation and requirements coverage. Assign runtime checks and obtain owner review of design and ignore proposal |
| 02 Virtual infrastructure | Confirm patched hypervisor, image provenance, available RAM, addressing, time and isolation | 01 approval, authorization for downloads and changes, Windows entitlement | Three guest baselines and sanitized network observations | Each guest boots, rollback works on a baseline, host-only connectivity and denied egress are demonstrated |
| 03 Wazuh SIEM | Install compatible central components, secure credentials, check TLS, services, restart and dashboard | 02 approved | Deployment notes, version record and private recovery material | T-01 platform health and HTTPS pass. Confirm alert delivery with first agent in 04 |
| 04 Endpoint monitoring | Enroll Linux then Windows, enable selected sources, baseline FIM | Accepted 03 platform and guest baselines | Sanitized settings and source-to-manager evidence | T-02 active agents and fields verified. Close T-01 end-to-end delivery with first indexed alert |
| 05 Detection engineering | Validate six hypotheses, controls, two custom rules and ATT&CK rationale | 04 telemetry | Coverage matrix, rules, positive/negative and live evidence | T-03/T-04 pass with actual rule IDs, limitations and fixture labels |
| 06 Incident investigation and response | Triage, hunt related events, construct UTC timelines, document scope and verdict | 05 alerts | IR-001 through IR-003 | T-05 links each conclusion to evidence, confidence, action and cleanup |
| 07 Python security automation | Build offline summarizer, handle malformed rows and safe spreadsheet output | 06 case context and reviewed fixtures | Python CLI, tests, CSV and Markdown examples | T-06 reproduces expected group counts and invalid-row handling |
| 08 Testing and evaluation | Run T-01–T-07, measure retention, latency, false positives, resources and repeatability | 02–07 artifacts | Test matrix, measurements, failures and limitations | Independent replay of one case, complete evidence and owner disposition of outstanding issues |
| 09 GitHub portfolio and release | Curate README, diagrams, evidence links, demo and release notes | 08 review and owner authorization | Portfolio, demo and optional release | Staged content reviewed for secrets, claims trace to measured results and publication is approved |

The owner accepted the Phase 01 design, including seven days of searchable alerts and an event-to-manager-alert target of at most 60 seconds. Phase 02A prepared the four runbooks and the owner accepted them. Phase 02B starts with the private network backup and Ubuntu media verification. Guest creation and power-on await explicit owner confirmation after the VMware upgrade and host checks. Operational results remain Not Tested. Start with [host preparation](documentation/operations/host-preparation.md).

Roll out SOC-SRV, then LIN-ENDPOINT, then WIN-ENDPOINT, then integrate. See [requirements and test IDs](PROJECT_SPEC.md) and [open work](TODO.md).
