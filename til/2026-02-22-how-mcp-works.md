# How MCP Works

**Date:** 2026-02-22  
**Tags:** mcp, ai, docker, github

## What I Learned

MCP (Model Context Protocol) is an open standard by Anthropic that lets AI models connect to external tools via a standardized client-server architecture.

## Key Concepts

- **Host** - AI app (Claude Desktop)
- **Client** - Manages MCP connections inside the host
- **Server** - Exposes tools/resources/prompts (e.g. github-mcp-server)
- **Gateway** - Docker's MCP gateway routes calls to the right server container

## How Docker MCP Gateway Works

1. Claude Desktop runs `docker mcp gateway run`
2. Gateway spawns MCP server containers as needed
3. Tools are discovered via handshake
4. Claude calls tools → Gateway routes → Server executes → API responds

## Gotchas

- The gateway spawns a new container per session causing container proliferation
- Fine-grained GitHub PATs need `Administration: Read and write` scope to create repos
- OAuth tokens have fixed scopes; PATs give granular control
- Never paste tokens in chat - regenerate immediately if exposed

## References

- [MCP Documentation](https://modelcontextprotocol.io)
- [Docker MCP Gateway](https://docs.docker.com/ai/mcp-catalog-and-toolkit/)
