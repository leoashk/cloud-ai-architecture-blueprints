graph TD
    Client[Enterprise Web / Mobile App] -->|TLS 1.3 / mTLS| WAF[API Gateway & Enterprise WAF]
    
    subgraph Control_Plane [API & Ingress Gateway Layer]
        WAF -->|gRPC / REST| Auth[OIDC / IAM Workload Identity Proxy]
        Auth -->|Auth Token Validated| Orchestrator[RAG Orchestration Microservice]
    end

    subgraph Data_Perimeter [Isolated Enterprise VPC / Private Subnets]
        Orchestrator -->|PII Masking & Tokenization| Guard[Sanitization & Guardrails Engine]
        Guard -->|Dense Embeddings| VectorDB[(Qdrant Vector Cluster / High Availability)]
        
        Orchestrator -->|Prompt + Context| Router[Inference Router & Load Balancer]
        
        subgraph Kubernetes_GPU_Pool [GKE / EKS Autoscaling GPU Node Pool]
            Router -->|Inference Request| vLLM_Node1[vLLM Pod - NVIDIA L4 / A100]
            Router -->|Inference Request| vLLM_Node2[vLLM Pod - NVIDIA L4 / A100]
        end
        
        Orchestrator -->|Cross-Region Fallback| ManagedAPI[AWS Bedrock / GCP Vertex AI Fallback]
    end

    subgraph Observability_Governance [Monitoring & Audit Perimeter]
        vLLM_Node1 -->|Prometheus Metrics| Metrics[Grafana / Prometheus Dashboard]
        Guard -->|Audit Logs| SIEM[Cloud Logging & OpenTelemetry Collector]
    end