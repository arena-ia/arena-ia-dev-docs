# Changelog

All notable changes to Arena IA integrations will be documented here.

## [Unreleased]

## [1.2.0] — 2026-09-01
### Added
- MCP Servers: documentation for the **official transport** (Streamable HTTP, MCP spec 2026-06-18) and for **OAuth (per-user login)** authentication, including the pre-registered OAuth client fields (Client ID / Secret / Scopes) for servers that don't support Dynamic Client Registration (e.g. Salesforce Hosted MCP)
- MCP Servers: the **Protocol** field (Auto / MCP official / Simplified HTTP) and standard-server code snippets (`@modelcontextprotocol/sdk`, FastMCP)
- Example: Official MCP server over Streamable HTTP (`examples/mcp-servers/official-streamable-http.md`)
- API Keys: how to target a custom model by name slug or UUID, and multimodal (`image_url` / `file`) message content
### Changed
- MCP tool call timeout documented as **30 seconds** (Custom Actions remain 15 seconds)
- The "Generic MCP Server" example is relabeled as the simplified-dialect path and now points to the official-transport example

## [1.1.0] — 2026-08-03
### Added
- Documentation for API Keys (reverse/inbound integration)
- Example: Zapier MCP integration
- Postman collection for MCP server testing

## [1.0.0] — 2025-01-01
### Added
- Initial documentation for MCP Servers
- Initial documentation for Custom Model Actions
- Examples: CEP lookup, CNPJ lookup, Generic GET, Generic POST, Bearer Auth
- Bilingual support (PT-BR / EN)
