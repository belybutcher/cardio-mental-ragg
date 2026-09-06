

 System Overview & Architecture

enterprise-grade AI service built with FastAPI, LangChain (LCEL), Groq LLM inference, and Supabase (PostgreSQL with `pgvector`). It features strict clinical guardrails against medical hallucinations, local ONNX embeddings, real-time conversation logging, and a zero-cost public HTTPS deployment.

```mermaid
flowchart TD
    subgraph PublicLayer ["1. Public Internet & Client Access"]
        Client["Frontend / Mobile App / Team Member"]
        Swagger["Interactive Swagger UI (/docs)"]
        CFTunnel["Cloudflare Enterprise Tunnel (HTTPS)"]
    end

    subgraph BackendApp ["2. Application Layer (FastAPI + LangChain)"]
        Uvicorn["Uvicorn ASGI Server (:8000)"]
        FastAPI["FastAPI Routing (/chat)"]
        LCEL["LangChain LCEL Pipeline"]
        PromptGuard["Medical Safeguard & Hallucination Filter"]
        LLM["Groq High-Speed LLM (openai/gpt-oss-120b)"]
        Embeddings["FastEmbed ONNX (BAAI/bge-small-en-v1.5)"]
    end

    subgraph SupabaseCloud ["3. Data & Storage Layer (Supabase EU-West-1)"]
        VectorDB[("public.documents (pgvector)")]
        ChatLog[("public.chat_history (Audit Trail)")]
    end

    Client -->|HTTPS Request| CFTunnel
    Swagger -->|HTTPS Request| CFTunnel
    CFTunnel -->|Reverse Proxy| Uvicorn
    Uvicorn --> FastAPI
    FastAPI --> LCEL
    LCEL --> PromptGuard
    LCEL --> Embeddings
    Embeddings <-->|Semantic Search| VectorDB
    LCEL --> LLM
    LLM -->|Streamed Response| FastAPI
    FastAPI -->|Async Persistence| ChatLog
```

---

