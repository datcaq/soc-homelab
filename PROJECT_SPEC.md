# SOC Home Lab product specification

## Problem, users and scope

A junior analyst needs to explain why an endpoint alert matters, what the endpoint actually recorded and which uncertainties remain. Separate Windows and Linux logs make correlation and a reproducible investigation difficult. This lab brings selected events into Wazuh, links alerts back to their sources, tests detection hypotheses and reports on sanitized exports.

The operator is a SOC student working on a single Windows laptop. A mentor or interviewer can review a case from source to verdict. A second learner should be able to repeat a selected test with their own lab credentials. The MVP comprises a three-VM architecture, six defensive use cases, at least two custom rules, at least three case studies, an offline Python reporter and an English portfolio.

## System scope

SOC-SRV hosts the manager, Filebeat, indexer and dashboard. WIN-ENDPOINT supplies Windows Security and Sysmon events. LIN-ENDPOINT supplies authentication and FIM observations. The analyst uses the host-only dashboard and exports a reviewed subset for Python. Guests may run in rotation. See the [system](architecture/system-architecture.md), [network](architecture/network-design.md), [resource](architecture/resource-allocation.md) and [data flow](architecture/data-flow.md) designs.

The MVP excludes production monitoring, real malware, testing outside the lab, automated containment, SOAR, cluster availability, cloud hosting, Kali, Docker and VirtualBox. Sigma and network packet analysis remain possible extensions. Collect only selected sources. Searchable alerts and raw logs have different retention needs.

## Requirements and acceptance traceability

All runtime, testing and publication statuses below are **Not Tested**. A test ID and an evidence ID identify planned records. Populate them only after execution. T-01 through T-07 follow the handbook acceptance matrix. Use the same identifiers in the test run, case and README.

| Requirement | Data source or input | Detection or product behavior | Verification | Evidence to retain | Acceptance status |
| --- | --- | --- | --- | --- | --- |
| FR-001 Central SIEM | Manager, indexer, Filebeat and dashboard state | Services healthy and an alert is searchable from the host | T-01 service, delivery and HTTPS checks | EV-SIEM service summary, query, certificate check | Not Tested |
| FR-002 Endpoint visibility | Windows and Linux agent status and source records | One agent per guest reaches the manager, including after restart | T-02 each endpoint | EV-AGENT dated connection and delivery checks | Not Tested |
| FR-003 Source collection | Security, Sysmon, Linux authentication and FIM | Preserve source identity, relevant fields and UTC time | T-02 / T-03 compare source with collected record | EV-SRC redacted paired records per source | Not Tested |
| FR-004 Detection coverage | Six UC sources below | Each positive behavior and negative control has a declared expected result | T-03, TC-01 through TC-06 | EV-01 through EV-06 | Not Tested |
| FR-005 Custom detection | Verified decoded fields and representative events | At least two local rules produce the intended outcomes in rule isolation and live delivery | T-04, record rule IDs, positive and negative cases | EV-RULE-1/2 input, rule revision, logtest, live output | Not Tested |
| FR-006 Investigation and hunting | Three cases drawn from UC-01/02, UC-03/04 and UC-05/06 respectively | Query related events, validate scope, verdict and response | T-05 review IR-001 through IR-003 | EV-IR source links, timeline, alternatives, decision and cleanup | Not Tested |
| FR-007 Offline reporting | Sanitized JSON/JSONL alert export | Summarize counts by agent/rule, report valid/invalid rows, produce CSV and Markdown | T-06 two identical alerts, one other, one malformed row | EV-PY tests, two groups, 3 valid, 1 invalid, reviewed output | Not Tested |
| FR-008 Reproduction and portfolio | English setup guide, versions, diagram and test case | Reader follows one complete path on a clean baseline using own credentials | T-07 walkthrough and disclosure review | EV-REPRO steps, versions, gaps, reviewed README | Not Tested |
| FR-009 FIM | Guest test directory baseline and file changes | Distinguish create, modify and delete and report relevant path | T-03 / TC-06 | EV-06 baseline and ordered observations | Not Tested |

| Requirement | Acceptance method / planned evidence | Status |
| --- | --- | --- |
| NFR-001 Resource fit | Record host available memory, CPU and disk across server-only, paired and integrated loads. Compare to approved budget and document any rotation | Not Tested |
| NFR-002 Timeliness | Measure event-to-manager-alert latency for eligible positives with synchronized UTC timestamps (T-03, EV-LAT) | Not Tested |
| NFR-003 Reproducibility | Repeat one documented case from a clean baseline and recover expected fields and verdict (T-07, EV-REPRO) | Not Tested |
| NFR-004 Retention | Retrieve a known alert seven days later, with timestamp and disk use (EV-RET) | Not Tested |
| NFR-005 Detection quality | Evaluate every declared positive and negative and report sample sizes and unexpected matches (T-03/T-04) | Not Tested |
| NFR-006 Evidence integrity | Each case links a redacted, attributable event, query, evidence ID and UTC timeline (T-05) | Not Tested |
| SEC-001 Network isolation | Test intended host-only flows, denied paths and absent external egress during simulations (EV-NET) | Not Tested |
| SEC-002 Data minimization | Before publishing, inspect each staged path and sample content for secrets, personal identifiers, raw logs and VM/ISO files (T-07) | Not Tested |
| SEC-003 Host and guest safeguards | Inspect VBS/firewall posture, authorized guest scope, guest snapshot and rollback documentation (EV-SAFE) | Not Tested |
| SEC-004 Access control | Check dashboard TLS identity and allowed source, protect enrollment and VM keys, limit management services to the permitted peers (EV-ACCESS) | Not Tested |

## Six use case test designs

Declare the target rule, scope, negative window and expected outcome before running each case. Distinguish a source event, a generic alert, a target detector match and the analyst's verdict. The following are future benign validation cases. Record actual event fields, rule IDs and versions at execution.

| Use case / purpose | Data source and required telemetry | Positive behavior and expected target alert | Negative control and false positives | ATT&CK interpretation |
| --- | --- | --- | --- | --- |
| UC-01 Windows Failed Logon: identify a series worth investigating | Security 4625 with logon-failure auditing, guest, account alias, failure reason, logon type, UTC and source if available | A lab account's failures in a declared time window satisfy a correlation condition. Expect a grouped authentication alert attributed to the correct guest and account | Isolated typo should not fire the burst rule. Stale saved credentials can cause real repeated failures. Predetermine a safe threshold below lockout or use a labeled fixture | T1110 only when the grouped evidence supports the hypothesis |
| UC-02 Linux Authentication Anomaly: distinguish repeated failures from isolated errors | One verified SSH authentication source (journal or auth.log), auth method, account, result, source if logged, UTC | A bounded lab failure sequence in the agreed source and window produces the selected SSH anomaly alert. If only a fixture is possible, label live coverage pending | Approved key login or one failure should not fire the anomaly rule. Misconfigured clients and stale keys can create bursts. Keep existing SSH authentication policy | T1110 when the authentication pattern and context support it |
| UC-03 PowerShell Execution: verify process context | Sysmon Operational 1, image, parent, user alias, harmless test marker/command line and UTC. PowerShell Operational 4104 is optional | A harmless, predeclared marker in a lab PowerShell invocation is collected. Expect the marker-specific alert if the rule is configured | A normal administrative command without the marker should not match. PowerShell is common during maintenance | T1059.001 describes the execution technique. A benign positive establishes visibility |
| UC-04 Local Account Creation: identify creation outside a test approval list | Windows Security 4720 with successful user account management auditing, subject, target, guest and UTC | A designated disposable non-admin account outside the approved list creates a source record and a policy alert naming actor and target | An approved change may still generate a generic alert. It must be excluded by the policy rule or triaged benign according to the predeclared expectation. Provisioning is a common cause | T1136.001 applies to the unauthorized-account hypothesis |
| UC-05 Scheduled Task Creation: review a new task against change policy | Windows Security 4698 with appropriate object access auditing, task name, subject, action and privilege context. Task Scheduler Operational can corroborate | A low-privilege benign test task with a designated lab marker produces the creation event and the selected lab policy alert | Expected maintenance task lacks the test condition. Updaters and scheduled maintenance commonly create legitimate tasks | T1053.005 only with supporting execution or persistence context |
| UC-06 File Integrity Monitoring: verify scoped change coverage | Wazuh FIM baseline on the lab Linux directory, agent, path, operation, UTC and hashes where available | Create, modify and delete a disposable text file, observing each step after baseline. Expect three ordered file integrity observations/alerts under the selected policy | A disposable file outside the monitored directory should yield zero matching FIM alerts. An authorized in-scope change may properly trigger an alert and receive a benign verdict | No default mapping. Assess manipulation only if the context supports it |

| Requirement → test | Evidence requirement | Per-case acceptance | Status |
| --- | --- | --- | --- |
| FR-003/004 → UC-01 → TC-01 ± | EV-01 redacted 4625 sequence, grouping/window, alert ID and negative query | Verify group and window at source and in detector output. Negative produces zero burst matches | Not Tested |
| FR-003/004 → UC-02 → TC-02 ± | EV-02 authentication excerpts, query, agent alias, rule ID and auth method | Validate one source, expected group, and absence of target match on control. Disclose fixture-only results | Not Tested |
| FR-003/004 → UC-03 → TC-03 ± | EV-03 Sysmon process record, marker, parent, alert and control | Match marker case only, explain normal parent/user context | Not Tested |
| FR-003/004 → UC-04 → TC-04 ± | EV-04 4720 source, actor/target, approval record, alert or documented generic-only result | Demonstrate intended policy alert and approved-control verdict, record cleanup | Not Tested |
| FR-003/004 → UC-05 → TC-05 ± | EV-05 4698 source, selected action, policy alert, maintenance control | Show expected detection and context, differentiate authorized maintenance and record cleanup | Not Tested |
| FR-003/004/009 → UC-06 → TC-06 ± | EV-06 baseline, ordered operations, paths, source notes and out-of-scope query | Account for all three changes and absence of the out-of-scope target alert | Not Tested |

For FR-005 choose at least two appropriate cases once real fields are visible. Preserve a known-good negative event in the same observation window. Mark a missing expected alert as a failed test even if the source event is present. Synthetic JSON can demonstrate pipeline behavior separately. [Wazuh rule testing](https://documentation.wazuh.com/current/user-manual/ruleset/testing.html) · [Wazuh FIM example](https://documentation.wazuh.com/current/proof-of-concept-guide/poc-file-integrity-monitoring.html) · [MITRE authentication](https://attack.mitre.org/techniques/T1110/) · [PowerShell](https://attack.mitre.org/techniques/T1059/001/) · [Local Account](https://attack.mitre.org/techniques/T1136/001/) · [Scheduled Task](https://attack.mitre.org/techniques/T1053/005/).

## Evaluation plan

The owner approved seven days of searchable alerts and a maximum detection latency of 60 seconds. The remaining measures define how the lab will be evaluated. Capture dates, versions, sample counts and exclusions. Record a numerator and denominator even when reporting a percentage. An empty denominator is N/A.

| Metric | Target or measurement method | Status |
| --- | --- | --- |
| Endpoint connectivity | Both agent identities active in their scheduled session and delivering after restart. Report simultaneous/rotating mode | Not Tested |
| Collection correctness | Every declared source yields a paired observation with the required fields, endpoint and UTC time. Identify any deduplication or unavailable field | Not Tested |
| Detection coverage | 6/6 planned use cases pass positive and negative criteria. Report passed/6 and executed/6 separately, plus count of real custom rules passing both logtest and live tests | Not Tested |
| Alert latency | Approved target ≤60 seconds from the event completing a detection condition to manager alert creation. Record all samples, median, maximum and clock offset. Measure search visibility separately | Not Tested |
| False positives | Target zero unexpected target-rule alerts on the declared negative controls. Report negative tests with unexpected match divided by executed negatives, with n and actual match counts | Not Tested |
| Resource use | Sample available RAM, host and guest CPU, paging, disk growth and responsiveness during 15 minutes of representative use. Document sustained paging and adjust concurrent guest count if observed. Compare E lab footprint to 250 GiB | Not Tested |
| Reproducibility | One clean-baseline repeat by a second reader or a documented independent replay. Compare rule, evidence fields and verdict, explain differences | Not Tested |
| Alert retention | Approved initial seven days searchable for a known indexed marker, verified on day seven. Measure daily growth before changing policy | Not Tested |
| Investigation completeness | Three reports with UTC timeline, source and alert references, scope, verdict/confidence, response or rationale, cleanup and learning | Not Tested |

MVP acceptance requires T-01 through T-07 with traceable evidence, documented limitations, a verified offline report, a reproducible English README and an owner review before release. Phase 01 accepts the design separately from these future product results.
