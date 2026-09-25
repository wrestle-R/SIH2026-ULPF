# Team Pony

**Problem statement:** SIH26156-Log
**Project:** Universal Log Pre-processing Framework (ULPF)

ULPF is a vendor-independent framework for turning perimeter network device logs into consistent, analytics-ready security events while retaining the original evidence for investigation and compliance.

## The problem

Enterprises collect logs from network devices, servers, applications, cloud services, IoT systems, and security tools. These sources use formats such as Syslog, JSON, XML, CSV, CEF, LEEF, and vendor-specific schemas. The differences make centralized monitoring, SIEM integration, threat detection, and analytics harder, while lossy conversions can discard information needed for forensics.

## Our solution

ULPF parses supported log formats, maps their fields into a common security schema, and keeps a traceable connection to the original event. The project is designed to make source onboarding straightforward and to provide normalized data that downstream SIEM, data lake, analytics, and machine learning systems can consume.

### Key capabilities

- **Lossless evidence:** Retains the original event text and bytes, with SHA-256 fingerprints and tamper-evident batch links.
- **Common representation:** Normalizes supported perimeter-device events to the OCSF 1.9 profile implemented by this prototype.
- **Traceability:** Carries field-level lineage and unmapped attributes alongside normalized records.
- **Source onboarding:** Supports built-in parsers and bounded YAML/JSON parser manifests for custom formats.
- **Portable output:** Exports OCSF, ECS, JSON, and NDJSON for downstream tools.
- **Offline container:** Runs as a standalone container without runtime internet access, suitable for air-gapped demonstrations.
- **Scale-out direction:** Documents a production architecture using durable ingestion, partitioned workers, immutable raw storage, and normalized lakehouse outputs.

## Scope

The target is to convert perimeter network device logs and events, regardless of vendor or format, into a standardized, lossless, analytics-ready representation. This repository currently provides a single-node reference prototype: its input is pasted text or uploaded files, and its supported OCSF schema is the perimeter-focused subset used by the included scenarios. It is not yet a distributed billion-events-per-day deployment or a live network log collector.

## What we have built

- Parsers for CEF, LEEF, RFC 5424 Syslog, JSON/NDJSON, XML, CSV, and key-value logs.
- Raw evidence preservation, SHA-256 evidence fingerprints, and tamper-evident batch chains.
- OCSF 1.9 Network Activity and Detection Finding normalization with field-level lineage.
- Custom parser onboarding through bounded YAML/JSON manifests.
- Browser-local session history and custom manifests, plus OCSF, ECS, JSON, and NDJSON exports.
- A web interface with an automatic live demo, Parser Lab, source registry, schema explorer, and event explorer.
- A standalone Docker image and health endpoint for local deployment.

## Technology stack

| Area | Technologies |
| --- | --- |
| Web application | Next.js 16, React 19, TypeScript |
| Runtime | Node.js 20.9 or newer |
| Styling and UI | Tailwind CSS 4, shadcn/ui components |
| Parsing and validation | Zod, YAML, fast-xml-parser, Papa Parse |
| Packaging | Docker |

Kafka, object storage, distributed workers, and lakehouse storage are part of the documented production scale-out design; they are not components of the current single-node prototype.

## Run locally

Requirements: Node.js 20.9 or newer and npm.

```bash
cd universal-log_ps/next
npm install
npm run dev
```

Open [http://localhost:3000](http://localhost:3000). Choose **Watch live demo** for the guided showcase, or open **Parser Lab** to paste a log or upload a sample from `public/samples`.

## Run with Docker

```bash
cd universal-log_ps/next
docker build -t ulpf-sih-demo .
docker run --rm -p 3000:3000 ulpf-sih-demo
```

The health endpoint is available at [http://localhost:3000/api/health](http://localhost:3000/api/health).

## Verification

Run these from `universal-log_ps/next`:

```bash
npm run typecheck
npm run lint
npm test
npm run build
npm run test:e2e
```

## Documentation

- [Research and differentiation](universal-log_ps/docs/RESEARCH.md)
- [Architecture brief and production scale-out path](universal-log_ps/docs/ARCHITECTURE.md)
- [Schema and traceability](universal-log_ps/docs/SCHEMA-AND-TRACEABILITY.md)
- [Parser plugin guide](universal-log_ps/docs/PARSER-PLUGIN-GUIDE.md)
- [SIH demo and presentation guide](universal-log_ps/docs/SIH-DEMO-GUIDE.md)
- [Testing and scaling](universal-log_ps/docs/TESTING-AND-SCALING.md)

## Current limitations

- Ingestion is through pasted text or file upload; there is no live UDP/TCP listener yet.
- Session history and custom manifests are stored locally in the browser and capped.
- Integrity hashes can reveal unexpected modification, but do not prove who created an event; signer authenticity needs key management and signatures.
- The implemented OCSF schema covers the perimeter-focused scenarios in this prototype, not every OCSF class.
