
[![Status](https://img.shields.io/badge/status-draft-blue)](https://github.com/MCPTrustFramework)
[![Spec](https://img.shields.io/badge/MCPF-1.0--draft-important)](https://github.com/MCPTrustFramework/MCPF-specification)
[![License](https://img.shields.io/badge/license-CC--BY--NC--4.0-green)](LICENSE)
[![Python](https://img.shields.io/badge/sdk-python-blue)](https://github.com/MCPTrustFramework/MCPF-python)
[![TypeScript](https://img.shields.io/badge/sdk-typescript-blueviolet)](https://github.com/MCPTrustFramework/MCPF-typescript)
[![FastAPI](https://img.shields.io/badge/registry-FastAPI-009688)](https://github.com/MCPTrustFramework/MCPF-registry)

# MCP Trust Framework (MCPF)

The Model Context Protocol (MCP) defines how AI agents and hosts call external tools and data sources.
MCPF adds the missing layer MCP intentionally does not define:

* **Identity** for MCP servers and agents
* **Discovery** of MCP servers
* **Trust**, provenance, assurance, and verification
* **Governance** controls for which MCP servers a host may safely use

MCPF is designed to be compatible with existing MCP servers and hosts.
It does **not** modify MCP — it adds a trust + discovery layer on top.

---

##  Repositories

Start here depending on what you're doing:

### Core Specification (SSOT)

* **[mcpf-specification](https://github.com/MCPTrustFramework/MCPF-specification)** — Core specification, schemas, security considerations, versioning

### Identity & Credentials Layer

* **[mcpf-did-vc](https://github.com/MCPTrustFramework/MCPF-did-vc)** — DID/VC infrastructure (W3C DID Core v1.0, VC Data Model v2.0)

### Trust Services (Reference Implementations)

* **[mcpf-ans](https://github.com/MCPTrustFramework/MCPF-ans)** — Agent Name Service (ANS) - Node.js/Express + PostgreSQL
* **[mcpf-registry](https://github.com/MCPTrustFramework/MCPF-registry)** — MCP Trust Registry - Python/FastAPI + PostgreSQL
* **[mcpf-a2a-registry](https://github.com/MCPTrustFramework/MCPF-a2a-registry)** — Agent-to-Agent delegation control - Node.js/Express + PostgreSQL

### SDKs

* **[mcpf-python](https://github.com/MCPTrustFramework/MCPF-python)** — Python client + verification helpers
* **[mcpf-typescript](https://github.com/MCPTrustFramework/MCPF-typescript)** — TypeScript/Node.js client + verification helpers

### Adoption Packs

* **[mcpf-quickstarts](https://github.com/MCPTrustFramework/MCPF-quickstarts)** — "Hello MCPF" recipes (5-min, 15-min, 1-hour paths)
* **[mcpf-examples](https://github.com/MCPTrustFramework/MCPF-examples)** — Real-world examples (banking, healthcare, customer service)
* **[mcpf-conformance](https://github.com/MCPTrustFramework/MCPF-conformance)** — Conformance tests + fixtures for implementations

### Optional (Future)

* **mcpf-viewer** — Registry browser + validation UI (playground) _[planned]_
* **mcpf-mcp** — MCP server that exposes registry queries/verification as MCP tools _[planned]_

---

##  Quick Start

### 1. Read the Spec
Start with **[mcpf-specification](https://github.com/MCPTrustFramework/MCPF-specification)** to understand the architecture.

### 2. Try a Quickstart
Choose your path in **[mcpf-quickstarts](https://github.com/MCPTrustFramework/MCPF-quickstarts)**:
-  **5-Minute Start** — Docker demo (zero code)
-  **15-Minute Start** — SDK integration (write code)
-  **1-Hour Start** — Full system deployment

### 3. Run Reference Services

```bash
# ANS (Agent Name Service)
git clone https://github.com/MCPTrustFramework/MCPF-ans.git
cd MCPF-ans
docker-compose up

# MCP Trust Registry
git clone https://github.com/MCPTrustFramework/MCPF-registry.git
cd MCPF-registry
docker-compose up

# A2A Delegation
git clone https://github.com/MCPTrustFramework/MCPF-a2a-registry.git
cd MCPF-a2a-registry
docker-compose up
```

### 4. Integrate with SDKs

**TypeScript:**
```typescript
import { MCPF } from 'mcpf-typescript';

const mcpf = new MCPF({
  ansUrl: 'http://localhost:4001',
  registryUrl: 'http://localhost:4002',
  a2aUrl: 'http://localhost:4003'
});

const agent = await mcpf.ans.resolve({ name: 'my-agent.example.agent' });
const isValid = await mcpf.did.verifyAgent(agent.did);
```

**Python:**
```python
from mcpf import MCPF

mcpf = MCPF(
    ans_url='http://localhost:4001',
    registry_url='http://localhost:4002',
    a2a_url='http://localhost:4003'
)

agent = await mcpf.ans.resolve('my-agent.example.agent')
is_valid = await mcpf.did.verify_agent(agent.did)
```

### 5. Validate Conformance
```bash
npm install -g @mcpf/conformance
mcpf-test all --config mcpf-config.json
```

---

##  `.well-known` Discovery

Registry operators may publish discovery metadata at:

`https://<domain>/.well-known/mcp-trust-registry.json`

Example:
```json
{
  "mcpfVersion": "1.0",
  "registry": {
    "name": "Example MCP Trust Registry",
    "baseUrl": "https://registry.example.com/mcp"
  },
  "issuer": {
    "did": "did:web:example.com",
    "name": "Example Org"
  }
}
```

Clients can fetch this file first and use `registry.baseUrl` to configure an SDK client.

---

##  Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                    MCP Host (Client)                    │
│              (Claude Desktop, Cursor, etc)              │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
        ┌────────────────────────────┐
        │   MCPF Trust Framework     │
        └────────────────────────────┘
                     │
        ┌────────────┴────────────┐
        │                         │
        ▼                         ▼
┌───────────────┐         ┌──────────────┐
│  ANS Service  │         │  DID/VC      │
│  (Port 4001)  │         │  Layer       │
└───────┬───────┘         └──────┬───────┘
        │                        │
        │    ┌───────────────────┴───────────────┐
        │    │                                   │
        ▼    ▼                                   ▼
┌──────────────────┐                    ┌────────────────┐
│  MCP Registry    │                    │  A2A Registry  │
│  (Port 4002)     │                    │  (Port 4003)   │
└──────────────────┘                    └────────────────┘
        │                                        │
        └────────────────┬───────────────────────┘
                         ▼
                 ┌───────────────┐
                 │  PostgreSQL   │
                 └───────────────┘
```

---

##  Status

| Component | Status | Language | Version |
|-----------|--------|----------|---------|
| Specification | Draft | Markdown | 1.0-draft |
| DID/VC Layer | Structure | Python | 1.0.0-alpha |
| ANS Service | Working | Node.js | 1.0.0-alpha |
| MCP Registry | Working | Python | 1.0.0-alpha |
| A2A Registry | Working | Node.js | 1.0.0-alpha |
| Python SDK | Complete | Python | 1.0.0-alpha |
| TypeScript SDK | Complete | TypeScript | 1.0.0-alpha |
| Examples | Complete | Multi | 1.0.0-alpha |
| Quickstarts | Complete | Multi | 1.0.0-alpha |
| Conformance | Structure | JavaScript | 1.0.0-alpha |

---

##  Contributing

We welcome contributions! Please see individual repository CONTRIBUTING.md files.

---

##  License

All MCPF repositories are licensed under **CC BY-NC 4.0** (Creative Commons Attribution-NonCommercial).

**Copyright (c) 2025 VeriTrust **

-  Free for research, education, and personal use
-  Attribution required
-  Commercial use requires permission

For commercial licensing inquiries: **hello@veritrust.vc**

---

##  Contact

- **Website:** https://mcpf.dev _(planned)_
- **Email:** hello@mcpf.dev
- **GitHub:** https://github.com/MCPTrustFramework

---

**If you are a regulator, enterprise security team, or MCP host vendor and want to pilot MCPF, contact:** [hello@mcpf.dev](mailto:hello@mcpf.dev)
