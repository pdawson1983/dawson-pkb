---
title: "Building a Python MCP Server with Docker"
date: 2026-02-23
tags: [mcp, python, docker, github-api, pkb]
---

## Overview

Built a custom MCP server in Python that exposes a Personal Knowledge Base (PKB) as tools accessible from any Claude session via the Docker MCP Gateway.

## Architecture

```
Claude (any session)
    ↓
Docker MCP Gateway
    ↓
pkb-mcp-server container (Python)
    ↓
GitHub API → pdawson1983/dawson-pkb
```

## Key Components

- **MCP SDK:** Anthropic's official Python `mcp` library
- **GitHub Integration:** PyGithub library to read/write files via GitHub API
- **Transport:** stdio (standard for Docker-based MCP servers)
- **Config:** Environment variables for GITHUB_TOKEN and GITHUB_REPO

## Tools Exposed

- `add_til` - Create a dated TIL entry
- `add_prompt` - Save a reusable prompt
- `add_pattern` - Document a reusable pattern
- `search_pkb` - Search across all notes
- `list_entries` - List entries in a section

## Claude Desktop Config

```json
"pkb": {
  "command": "docker",
  "args": ["run", "-i", "--rm", "-e", "GITHUB_TOKEN", "-e", "GITHUB_REPO", "pkb-mcp-server"],
  "env": {
    "GITHUB_TOKEN": "your-pat-here",
    "GITHUB_REPO": "pdawson1983/dawson-pkb"
  }
}
```

## Gotchas

- Image name must be the LAST item in the args array - easy to miss
- GitHub PAT needs `Contents: Read and write` scope
- Never paste tokens in chat - they get exposed in conversation history
- Restart Claude Desktop after changing claude_desktop_config.json
