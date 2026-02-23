# Model Context Protocol (MCP)

Learnings, configurations, and patterns for MCP.

## What is MCP

MCP (Model Context Protocol) is an open standard by Anthropic that lets AI models connect to external tools, data sources, and services in a standardized way.

## Key Concepts

- **MCP Host** - The AI application (e.g. Claude Desktop)
- **MCP Client** - Lives inside the host, manages server connections
- **MCP Server** - Exposes tools, resources, and prompts
- **Primitives** - Tools (actions), Resources (data), Prompts (templates)
- **Transports** - stdio (local) or HTTP+SSE (remote)

## Docker MCP Gateway

Docker Desktop includes a built-in MCP gateway (`docker mcp gateway run`) that manages MCP server containers and routes tool calls.

### Config Location
```
%APPDATA%\Claude\claude_desktop_config.json
```

### Example Config
```json
{
  "mcpServers": {
    "MCP_DOCKER": {
      "command": "docker",
      "args": ["mcp", "gateway", "run"]
    }
  }
}
```

## Lessons Learned

- Fine-grained PATs need `Administration: Read and write` to create repos
- The Docker MCP gateway spawns new containers per session causing container proliferation
- OAuth tokens have fixed scopes; PATs give more granular control
- Never paste tokens in chat - regenerate immediately if exposed
