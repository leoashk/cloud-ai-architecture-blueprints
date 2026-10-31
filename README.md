# Enterprise Cloud & AI Architecture Blueprints

A curated collection of production system design frameworks, Architecture Decision Records (ADRs), and cost/TCO trade-off models for multi-hybrid cloud and enterprise Generative AI platforms across AWS, GCP, and Azure.

---

## 🏛 Architecture Decision Records (ADRs)

| ID | Title | Status | Focus Area |
|---|---|---|---|
| **[ADR-001](./adrs/ADR-001-vllm-on-gke-vs-managed-cloud-apis.md)** | Self-Hosted vLLM on GKE vs Cloud-Native Managed Endpoints | Accepted | Infrastructure & Cost |
| **[ADR-002](./adrs/ADR-002-multi-cloud-vector-database-selection.md)** | Dedicated Vector DB (Qdrant) vs Integrated Search (OpenSearch) | Accepted | Data & Security |
| **[ADR-003](./adrs/ADR-003-zero-trust-iam-hybrid-cloud-boundary.md)** | Workload Identity Federation Across AWS EKS & GCP GKE | Proposed | Governance & IAM |

---

## 📐 System Design Artifacts

### 1. Enterprise Hybrid RAG Platform
High-level architecture illustrating secure, zero-trust retrieval-augmented generation for regulated enterprises.

```mermaid
graph TD
    Client[Enterprise App / Client] -->|TLS 1.3 / Auth| Gateway[API Gateway / WAF]
    Gateway -->|PrivateLink| Microservice[RAG Orchestration Microservice]
    
    subgraph VPC Perimeter - GCP / AWS Private Subnet
        Microservice -->|Dense Search| VectorDB[(Qdrant Vector Cluster)]
        Microservice -->|gRPC Stream| LLM[vLLM Inference Engine on GKE/EKS]
        LLM -->|GPU Pool| GPUs[NVIDIA L4 / A100 Nodes]
    end
    
    Microservice -->|Audit Logs| Logging[Cloud Logging & OpenTelemetry]