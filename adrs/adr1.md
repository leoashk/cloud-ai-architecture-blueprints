```markdown
# ADR-001: Self-Hosted vLLM on GKE vs Cloud-Native Managed Endpoints

* **Status:** Accepted
* **Deciders:** Cloud Architecture Team
* **Date:** 2026-10-31

## Context & Problem Statement
The enterprise requires a scalable, low-latency LLM inference engine to process 50M+ tokens daily while maintaining strict compliance, private data isolation within a custom VPC, and predictable cost structures under high request concurrency.

## Decision Drivers
* **Latency & Throughput:** Time-to-First-Token (TTFT) under 200ms for continuous streaming.
* **Data Security & Privacy:** Zero data retention by third parties; all processing remains inside enterprise VPC boundaries.
* **Total Cost of Ownership (TCO):** Unit economics when scaling beyond 50M tokens/day.

## Considered Options
1. **Option 1:** Self-hosted open-weights models (e.g., Llama 3 / Qwen) using vLLM deployed on GCP GKE GPU node pools (NVIDIA L4/A100).
2. **Option 2:** Fully managed cloud endpoints (AWS Bedrock / GCP Vertex AI APIs).

## Decision Outcome
**Chosen Option:** **Option 1 (Self-hosted vLLM on GKE)** for primary workloads, with Option 2 configured as a secondary fallback for peak burst capacity.

### Positive Consequences
* **Fixed Infrastructure Costs:** Lower per-token cost at high volume compared to consumption-based pricing.
* **Complete Data Control:** Prompts and embeddings never leave the enterprise private subnet.
* **Optimized Hardware Usage:** PagedAttention and continuous batching in vLLM yield up to 3x throughput over baseline serving frameworks.

### Negative Consequences & Mitigation
* **Operational Overhead:** Requires ongoing maintenance of Kubernetes GPU drivers, node autoscaling policies, and container images.
  * *Mitigation:* Automated infrastructure provisioning via Terraform and deployment management via Helm and GitOps (ArgoCD).

---

## Architectural Comparison Matrix

| Evaluation Criteria | Option 1: vLLM on GKE | Option 2: Managed APIs (Bedrock/Vertex) |
|---|---|---|
| **Data Privacy** | Full VPC Isolation | Shared Multi-Tenant SaaS Endpoint |
| **Token Cost (High Volume)** | Low (Fixed Node Cost) | High (Pay-per-Token Model) |
| **Autoscaling Precision** | Custom Metrics (Queue Depth/GPU RAM) | Managed by Vendor |
| **Maintenance Effort** | Medium / High | Low |