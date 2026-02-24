# Data Boundary Policy

## Core Rule

No data processed by the system may leave the local environment except through
explicitly approved boundary crossings. This applies to all stages of the
pipeline: ingestion, processing, indexing, retrieval, and reasoning.

## Approved Boundary Crossings

Only two outbound data flows are permitted:

1. **Kiro IDE reasoning** — the development assistant used for coding and spec
   work. This is an interactive tool under developer control.
2. **Amazon Bedrock** — the cloud LLM and embedding API, operating under the
   user's own AWS account and data processing agreements.

All other outbound data flows are prohibited.

## Prohibited Patterns

The following patterns violate the data boundary and must be caught during
design review, security review, and code review:

- Direct vendor API calls (OpenAI, Anthropic, Voyage AI, Cohere, Google, etc.)
  that send corpus content, embeddings, or metadata to third-party servers
- Embedding APIs that transmit document text to external services for vectorization
- Cloud vision APIs accessed directly (not through Bedrock)
- Telemetry, analytics, or crash reporting that includes corpus-derived content
- Dependency auto-update mechanisms that transmit usage data
- Any HTTP/HTTPS call that includes document content, metadata, or user queries
  in the request body or URL parameters

## Permitted Inbound-Only Network Access

The following inbound-only network operations are acceptable because they
download artifacts without sending corpus data:

- ML model downloads from Hugging Face, PyPI, or other registries (CLIP, easyocr,
  sentence-transformers, etc.)
- Python package installation via pip
- Git operations on code repositories (specs and code, not corpus data)

These are acceptable because no corpus content leaves the machine. However, for
maximum security in air-gapped environments, models and packages should be
pre-downloaded and cached locally.

## Enforcement Points

### Design Phase (Layer 1 Specs)

Every Layer 1 spec that involves cloud API calls, embedding models, or LLM
interactions must:

1. Identify all outbound data flows in the Architecture section
2. Confirm each flow routes through an approved boundary (Bedrock or local)
3. Explicitly state that direct vendor APIs are not used

The Architecture Challenger review should flag any spec that introduces a new
outbound data flow not routed through Bedrock.

### Implementation Phase (Layer 3 Specs + Code)

- The `core/llm` abstraction layer must only support Bedrock and local providers.
  Direct vendor SDK clients (openai, anthropic, voyageai, cohere) must not be
  used for runtime data processing.
- Embedding model backends must use either Bedrock or local models
  (sentence-transformers, etc.). No direct embedding vendor APIs.
- The Security Review checklist includes data boundary verification.

### Code Review

Any new dependency that makes HTTP calls should be evaluated for data exfiltration
risk. Dependencies that are purely local (parsing libraries, ML inference
libraries) are fine. Dependencies that phone home or transmit usage data must be
configured to disable telemetry or rejected.

## Rationale

This policy exists because the system processes sensitive document corpora
(emails, internal documents, proprietary data). The operator must have full
control over where this data goes. Routing all cloud calls through Bedrock
ensures the data stays within the operator's AWS account boundary, subject to
their own data processing agreements and compliance controls.

Local-only processing (local embedding models, local OCR, local classification)
is always preferred when quality is sufficient. Bedrock is the fallback for tasks
where cloud-grade models provide meaningfully better results.
