# Universal AI Engineering & Architecture Documentation Skill

When triggered, perform the following:

## System Analysis
Scan the repository to map core modules, data flows, and API boundaries.

## Documentation Generation

### DECISION_LOG.md
Maintain a chronological record of technical pivots. Document the "Why" for every major choice (e.g., choosing Arize Phoenix for local-first tracing over cloud-based alternatives).

### ARCHITECTURE.md
Generate a high-level technical overview including a Mermaid.js diagram of the processing pipeline.

### PRIVACY.md
Automatically update privacy analysis if new data types (video, audio, PII) are tracked, ensuring alignment with consent and retention standards.

## Observability Sync
Ensure all AI-driven events (like "Coaching Nudges") are logged as spans with metadata for latency (ms) and trigger accuracy.

## Project Execution (Nerdy MVP)
For AI-Powered Live Session Analysis builds, apply:

- **Modular Structure:** Initialize /video-processor, /metrics-engine, and /coaching-system
- **Performance Baseline:** Optimize for analysis latency consistently <300ms with metric updates at 1-2 Hz
- **Accuracy Calibration:** Set targets for 85%+ eye contact and 95%+ for speaking time accuracy
- **Chaos Engineering:** Generate a skills.sh script to simulate low-light, network jitter, and high CPU load scenarios to verify graceful handling of data gaps
