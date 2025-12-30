![Status](https://img.shields.io/badge/status-draft-blue)
![Spec](https://img.shields.io/badge/MCPF-1.0--draft-important)
![License](https://img.shields.io/badge/license-MIT-green)
![Python](https://img.shields.io/badge/sdk-python-blue)
![TypeScript](https://img.shields.io/badge/sdk-typescript-blueviolet)
![FastAPI](https://img.shields.io/badge/registry-FastAPI-009688)

# MCP Trust Framework (MCPF)

The Model Context Protocol (MCP) defines how AI agents and hosts call external tools and data sources.
MCPF adds the missing layer MCP intentionally does not define:

- Identity for MCP servers and agents
- Discovery of MCP servers
- Trust, provenance, assurance, and verification
- Governance controls for which MCP servers a host may safely use

MCPF is designed to be compatible with existing MCP servers and hosts.
It does **not** modify MCP — it adds a trust + discovery layer on top.

## Repositories

Start here depending on what you’re doing:

### Specification (SSOT)
- **mcpf-specification** — Core specification, schemas, security considerations, versioning

### Reference implementation
- **mcpf-registry** — Minimal MCP Trust Registry (reference implementation)

### SDKs
- **mcpf-python** — Python client + verification helpers
- **mcpf-typescript** — Node/TypeScript client + verification helpers

### Adoption packs
- **mcpf-quickstarts** — “Hello MCPF” recipes for hosts, registries, and verifiers
- **mcpf-examples** — Example registry entries, DID docs, credentials, `.well-known` samples
- **mcpf-conformance** — Conformance tests + fixtures for implementations

### Optional (recommended)
- **mcpf-viewer** — Registry browser + validation UI (playground)
- **mcpf-mcp** — MCP server that exposes registry queries/verification as MCP tools

## Quick start

1) Read the spec: start with **mcpf-specification**
2) Run the reference registry: see **mcpf-registry**
3) Integrate verification in your host/app: use **mcpf-python** or **mcpf-typescript**
4) Validate with fixtures: use **mcpf-conformance**

## `.well-known` discovery

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
Clients can fetch this file first and use registry.baseUrl to configure an SDK client.

Status
Specification: Draft

Reference registry: MVP

SDKs: MVP

If you are a regulator, enterprise security team, or MCP host vendor and want to pilot MCPF, contact: hello@mcpf.dev
