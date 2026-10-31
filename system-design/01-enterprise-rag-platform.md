graph TD
    Client[Enterprise Web / Mobile App] -->|TLS 1.3 / mTLS| WAF[API Gateway & WAF]
    WAF -->|gRPC / REST| Auth[Workload Identity Proxy]
    Auth -->|Validated Token| Orchestrator[RAG Orchestration Microservice]

    subgraph DataPerimeter ["Isolated Enterprise VPC (Private Subnets)"]
        Orchestrator -->|PII Masking| Guard[Sanitization & Guardrails]
        Guard -->|Dense Search| VectorDB[(Qdrant Vector Cluster)]
        Orchestrator -->|Prompt + Context| Router[Inference Router]
        
        Router -->|GPU Pool (L4/A100)| vLLM1[vLLM Node 1]
        Router -->|GPU Pool (L4/A100)| vLLM2[vLLM Node 2]
        
        Orchestrator -->|Fallback| ManagedAPI[AWS Bedrock / Vertex AI]
    end

    vLLM1 -->|Metrics| Prom[Prometheus & Grafana]
    Guard -->|Audit Logs| SIEM[OpenTelemetry / SIEM]