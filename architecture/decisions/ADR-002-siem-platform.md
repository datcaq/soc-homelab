# ADR-002 Wazuh on a single Ubuntu VM

**Decision date** 2026-11-27

**Status** Owner-approved design. Deployment and service acceptance are planned for Phase 03.

## Decision

Place the Wazuh manager, Filebeat, indexer and dashboard together on Ubuntu Server 24.04 LTS. Allocate 4 vCPU, 8 GiB RAM and a 60 GiB growable disk. Two guest agents cover the Windows and Linux sources. Keep the analyst browser on the host and the reporting workflow offline.

A multi-node installation would add service isolation and capacity but consume guest memory, require more certificates and introduce cluster traffic. The MVP needs traceable detections from two endpoints and reproducible investigations. The all-in-one quickstart provides that path with one central guest to maintain.

## Consequences and review

A central outage interrupts search and processing. Plan controlled restart, service checks, backup/restore and free-space monitoring. Use compatible package versions and record them before enrollment. Revisit sizing or topology if measured ingestion, alert retention or recovery time fails its target. Preserve source-to-index evidence when diagnosing dropped or delayed alerts.

[Wazuh quickstart sizing and OS support](https://documentation.wazuh.com/current/quickstart.html) · [Component data paths](https://documentation.wazuh.com/current/getting-started/architecture.html).
