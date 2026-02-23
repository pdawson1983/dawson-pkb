# Build PKB MCP Server

## Objective
Build a Python-based MCP (Model Context Protocol) server that exposes my Personal Knowledge Base (PKB) as a set of tools accessible from any AI session via the Docker MCP Gateway.

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

## Project Structure
Create the following structure in the current directory:
```
pkb-mcp-server/
├── Dockerfile
├── requirements.txt
├── server.py
└── README.md
```

## Technical Specs

- **Language:** Python
- **MCP SDK:** `mcp` (Anthropic's official Python MCP SDK)
- **GitHub Integration:** GitHub API via `PyGithub` library (no local git clone needed)
- **Transport:** stdio (standard for Docker-based MCP servers)
- **Deployment:** Docker container

## Tools to Implement

### 1. `add_til`
Create a new Today I Learned entry.
- **Inputs:** `title` (str), `content` (str), `tags` (list[str])
- **Behavior:**
  - Generate filename: `YYYY-MM-DD-slugified-title.md`
  - Use the TIL template format (see Template Formats below)
  - Commit to `til/` folder in `pdawson1983/dawson-pkb`
  - Update `til/README.md` to include the new entry in the entries list
- **Returns:** GitHub URL of the created file

### 2. `add_prompt`
Save a reusable prompt to the prompt library.
- **Inputs:** `name` (str), `category` (str: coding|infrastructure|documentation|general), `purpose` (str), `prompt_text` (str), `notes` (str, optional)
- **Behavior:**
  - Use the prompt template format (see Template Formats below)
  - Commit to `ai/prompts/{category}/` folder
  - Filename: slugified name + `.md`
- **Returns:** GitHub URL of the created file

### 3. `add_pattern`
Document a reusable pattern.
- **Inputs:** `name` (str), `category` (str: agent|cloud|devops), `problem` (str), `solution` (str), `implementation` (str), `when_to_use` (str), `when_not_to_use` (str)
- **Behavior:**
  - Use the pattern template format (see Template Formats below)
  - Commit to `ai/agents/patterns/` (if agent) or `cloud/patterns/` (if cloud) or `devops/` (if devops)
  - Filename: slugified name + `.md`
- **Returns:** GitHub URL of the created file

### 4. `search_pkb`
Search across all PKB files for a keyword or phrase.
- **Inputs:** `query` (str)
- **Behavior:**
  - Search file contents and filenames across the entire `pdawson1983/dawson-pkb` repo
  - Return matching files with a snippet of the matching content
- **Returns:** List of matches with file path, URL, and content snippet

### 5. `list_entries`
List entries in a PKB section.
- **Inputs:** `section` (str: til|prompts|patterns|all)
- **Behavior:**
  - Return a list of files in the requested section
  - Include filename, path, and last modified date
- **Returns:** Formatted list of entries

## Template Formats

### TIL Template
```markdown
# {title}

**Date:** {YYYY-MM-DD}
**Tags:** {tag1, tag2, tag3}

## What I Learned

{content}

## References

- Add references here
```

### Prompt Template
```markdown
# {name}

**Category:** {category}
**Model:** Claude
**Date Added:** {YYYY-MM-DD}

## Purpose

{purpose}

## Prompt

```
{prompt_text}
```

## Notes

{notes}
```

### Pattern Template
```markdown
# {name}

**Category:** {category}
**Date Added:** {YYYY-MM-DD}

## Problem

{problem}

## Solution

{solution}

## Implementation

```
{implementation}
```

## When to Use

{when_to_use}

## When NOT to Use

{when_not_to_use}
```

## Configuration

The server reads configuration from environment variables:
- `GITHUB_TOKEN` - GitHub Personal Access Token with `Contents: Read and write` scope
- `GITHUB_REPO` - Target repo, default: `pdawson1983/dawson-pkb`

## Dockerfile Requirements

- Base image: `python:3.12-slim`
- Install dependencies from `requirements.txt`
- Run `server.py` as entrypoint
- Accept `GITHUB_TOKEN` and `GITHUB_REPO` as environment variables

## requirements.txt
```
mcp
PyGithub
python-slugify
```

## Docker Build & Run

After creating all files, provide the commands to:
1. Build the Docker image: `docker build -t pkb-mcp-server .`
2. Test run the container locally
3. Register it with the Docker MCP Gateway by adding to `%APPDATA%\Claude\claude_desktop_config.json`

## Claude Desktop Config Entry
After building, add this to the `mcpServers` section of `claude_desktop_config.json`:
```json
"pkb": {
  "command": "docker",
  "args": [
    "run", "--rm", "-i",
    "-e", "GITHUB_TOKEN",
    "-e", "GITHUB_REPO",
    "pkb-mcp-server"
  ],
  "env": {
    "GITHUB_TOKEN": "your-github-pat-here",
    "GITHUB_REPO": "pdawson1983/dawson-pkb"
  }
}
```

## Success Criteria

- All 5 tools are implemented and functional
- Server communicates via stdio transport
- Docker container builds without errors
- Tools can be called from Claude Desktop via the MCP gateway
- New entries are committed and visible in the GitHub repo
