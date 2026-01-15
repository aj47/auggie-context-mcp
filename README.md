# There is now an official Augment Code Context Engine MCP:
https://docs.augmentcode.com/context-services/mcp/overview


---
# Auggie Context MCP Server

[![npm version](https://badge.fury.io/js/auggie-context-mcp.svg)](https://www.npmjs.com/package/auggie-context-mcp)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A Model Context Protocol (MCP) server that exposes Auggie CLI for codebase context retrieval. This allows AI agents like Claude, Cursor, and others to query codebases using Augment's powerful context engine.

## Quick Start

This MCP server is designed to be used with MCP clients like **Claude Desktop** or **Cursor**. It cannot be used standalone.

### Prerequisites

1. **Install Auggie CLI**: https://docs.augmentcode.com/cli/overview
2. **Authenticate with Auggie**:
   ```bash
   auggie login
   ```
   This opens a browser for authentication. Once logged in, you're ready to go!

### Setup with Claude Desktop

1. Edit `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows)
2. Add this configuration:

```json
{
  "mcpServers": {
    "auggie-context": {
      "command": "npx",
      "args": ["-y", "auggie-context-mcp@latest"]
    }
  }
}
```

3. Restart Claude Desktop
4. You should now see the `query_codebase` tool available in Claude

**Note**: If you need to use a specific token instead of your Auggie CLI login, you can add an `env` section with `AUGMENT_SESSION_AUTH`. See the [Authentication](#authentication) section for details.

### Setup with Cursor

1. Create or edit `.cursor/mcp.json` in your project or globally
2. Add the same configuration as above
3. Restart Cursor

See [Installation & Usage](#installation--usage) section for detailed instructions.

## Features

- 🔍 **Codebase Query**: Intelligent Q&A over repositories using Augment's context engine
- 🚀 **Simple Setup**: Pure TypeScript/Node.js implementation (no Python required)
- 🔒 **Read-Only**: Safe context retrieval without file modification capabilities
- ⚡ **Fast**: Direct integration with Auggie CLI
- 📦 **Easy Distribution**: Single npm package, works with `npx`

## Requirements

- **Node.js 18+**
- **Auggie CLI** installed and available on PATH
  - Install: See [Auggie CLI installation guide](https://docs.augmentcode.com/cli/overview)
  - Verify: `auggie --version`
- **Augment Authentication** (see Authentication section below)

## Authentication

The server supports two authentication methods:

### Option 1: Auggie CLI Login (Recommended)

Simply log in using the Auggie CLI:

```bash
auggie login
```

This opens a browser for authentication. Once logged in, the MCP server will automatically use your Auggie CLI session. No additional configuration needed!

### Option 2: Environment Variable (AUGMENT_SESSION_AUTH)

Alternatively, you can provide an explicit access token via the `AUGMENT_SESSION_AUTH` environment variable.

**Get Your Token:**

```bash
# 1. Ensure Auggie CLI is installed
auggie --version

# 2. Sign in to Augment (opens browser)
auggie login

# 3. Print your access token
auggie token print
```

This will output something like:
```
TOKEN={"accessToken":"your-token-here","tenantURL":"https://...","scopes":["read","write"]}
```

**Set the Token in MCP client config:**

Add the token to your MCP client configuration:

```json
{
  "mcpServers": {
    "auggie-context": {
      "command": "npx",
      "args": ["-y", "auggie-context-mcp@latest"],
      "env": {
        "AUGMENT_SESSION_AUTH": "{\"accessToken\":\"your-token-here\",\"tenantURL\":\"https://...\",\"scopes\":[\"read\",\"write\"]}"
      }
    }
  }
}
```

**Or set in shell environment:**

```bash
# Get your token
TOKEN=$(auggie token print | grep '^TOKEN=' | cut -d= -f2-)

# One-time for current session
export AUGMENT_SESSION_AUTH="$TOKEN"

# Or persist in ~/.zshrc or ~/.bashrc
echo "export AUGMENT_SESSION_AUTH='$TOKEN'" >> ~/.zshrc
source ~/.zshrc
```

### Which Method Should I Use?

- **Use Auggie CLI Login** if you're the only user on your machine and want the simplest setup
- **Use AUGMENT_SESSION_AUTH** if you need to use a specific token or are in a shared/CI environment

⚠️ **Security**: Never commit tokens to source control. Use environment variables or secure config stores.

## Installation & Usage

**Note**: This server is designed to be used with MCP clients (Claude Desktop, Cursor, etc.). It uses the MCP protocol over stdio and cannot be run standalone.

### Cursor Configuration

Add to your Cursor MCP config (`.cursor/mcp.json` - global or per-project):

**Simple setup (uses your Auggie CLI login):**

```json
{
  "mcpServers": {
    "auggie-context": {
      "command": "npx",
      "args": ["-y", "auggie-context-mcp@latest"]
    }
  }
}
```

**With explicit token (optional):**

```json
{
  "mcpServers": {
    "auggie-context": {
      "command": "npx",
      "args": ["-y", "auggie-context-mcp@latest"],
      "env": {
        "AUGMENT_SESSION_AUTH": "{\"accessToken\":\"your-token-here\",\"tenantURL\":\"https://...\",\"scopes\":[\"read\",\"write\"]}"
      }
    }
  }
}
```

### Claude Desktop (macOS)

Edit `~/Library/Application Support/Claude/claude_desktop_config.json`:

**Simple setup (uses your Auggie CLI login):**

```json
{
  "mcpServers": {
    "auggie-context": {
      "command": "npx",
      "args": ["-y", "auggie-context-mcp@latest"]
    }
  }
}
```

**With explicit token (optional):**

```json
{
  "mcpServers": {
    "auggie-context": {
      "command": "npx",
      "args": ["-y", "auggie-context-mcp@latest"],
      "env": {
        "AUGMENT_SESSION_AUTH": "{\"accessToken\":\"your-token-here\",\"tenantURL\":\"https://...\",\"scopes\":[\"read\",\"write\"]}"
      }
    }
  }
}
```

### Claude Desktop (Windows)

Edit `%APPDATA%\Claude\claude_desktop_config.json`:

**Simple setup (uses your Auggie CLI login):**

```json
{
  "mcpServers": {
    "auggie-context": {
      "command": "npx",
      "args": ["-y", "auggie-context-mcp@latest"]
    }
  }
}
```

**With explicit token (optional):**

```json
{
  "mcpServers": {
    "auggie-context": {
      "command": "npx",
      "args": ["-y", "auggie-context-mcp@latest"],
      "env": {
        "AUGMENT_SESSION_AUTH": "{\"accessToken\":\"your-token-here\",\"tenantURL\":\"https://...\",\"scopes\":[\"read\",\"write\"]}"
      }
    }
  }
}
```

## Available Tools

### `query_codebase`

Query a codebase using Augment's context engine.

**Parameters:**

- `query` (required): The question or query about the codebase
- `workspace_root` (optional): Absolute path to the workspace/repository root. Defaults to current directory.
- `model` (optional): Model ID to use. Example: `claude-3-5-sonnet-20241022`
- `rules_path` (optional): Path to additional rules file
- `timeout_sec` (optional): Query timeout in seconds. Default: 240
- `output_format` (optional): Output format (`text` or `json`). Default: `text`

**Example Usage in Claude/Cursor:**

```
What is the architecture of this codebase?

How does the authentication system work?

Where is the user registration logic implemented?

Show me how the payment processing is handled.
```

## Development

### Setup

```bash
# Clone the repository
git clone https://github.com/aj47/auggie-mcp.git
cd auggie-mcp

# Install dependencies
npm install

# Build
npm run build
```

### Development Mode

```bash
# Watch mode (auto-rebuild on changes)
npm run watch

# Run in development
npm run dev
```

### Testing Locally

```bash
# Build the project
npm run build

# Make sure you're logged in to Auggie
auggie login

# Test with MCP Inspector (recommended)
npx @modelcontextprotocol/inspector node dist/index.js

# Or test with a real MCP client (Claude Desktop, Cursor)
# by pointing it to your local build instead of npx
```

**Optional**: If you want to test with an explicit token instead of your Auggie CLI login:
```bash
export AUGMENT_SESSION_AUTH=$(auggie token print | grep '^TOKEN=' | cut -d= -f2-)
npx @modelcontextprotocol/inspector node dist/index.js
```

## Architecture

```
┌─────────────────────┐
│   AI Agent          │
│ (Claude, Cursor)    │
└──────────┬──────────┘
           │ MCP Protocol (stdio)
           ▼
┌─────────────────────┐
│ auggie-context-mcp  │
│  (TypeScript/Node)  │
│                     │
│  Tool:              │
│  - query_codebase   │
└──────────┬──────────┘
           │ subprocess
           ▼
┌─────────────────────┐
│   Auggie CLI        │
│  --print --quiet    │
│                     │
│  Augment Context    │
│  Engine             │
└─────────────────────┘
```

## Troubleshooting

### Server not showing up in Claude/Cursor

1. **Check config file syntax**: Ensure your JSON is valid (no trailing commas, proper quotes)
2. **Verify authentication**: Make sure you've run `auggie login` or set `AUGMENT_SESSION_AUTH` in the config
3. **Restart the client**: Completely quit and restart Claude Desktop or Cursor
4. **Check logs**:
   - **Claude Desktop (macOS)**: `~/Library/Logs/Claude/mcp*.log`
   - **Claude Desktop (Windows)**: `%APPDATA%\Claude\logs\mcp*.log`
   - **Cursor**: Check MCP logs in settings

### "Auggie CLI not found"

The server cannot find the Auggie CLI. Ensure it's installed and on your PATH:

```bash
auggie --version
```

If not found, install from: https://docs.augmentcode.com/cli/overview

### "Authentication required" or "not logged in"

The Auggie CLI needs authentication. You have two options:

**Option 1: Use Auggie CLI login (recommended)**
```bash
auggie login
```

**Option 2: Set explicit token in MCP config**
1. Run `auggie token print` to get your token
2. Copy the entire JSON value (everything after `TOKEN=`)
3. Add it to the `env` section in your MCP config (see examples above)

### "Query timed out"

For large codebases, queries may take longer. The default timeout is 240 seconds (4 minutes). If you need more time, you can't currently configure this in the MCP client config, but you can modify the source code and rebuild.

### Tool not appearing in Claude/Cursor

1. Verify the server is configured correctly in your MCP config
2. Check that the config file is in the correct location
3. Restart the client application
4. Look for the `query_codebase` tool in the available tools list

## Security

- **Read-only**: This server only queries codebases; it cannot modify files
- **Token safety**: Never commit `AUGMENT_SESSION_AUTH` to version control
- **Workspace isolation**: Queries are scoped to the specified workspace

## License

MIT

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Links

- [Augment Code](https://www.augmentcode.com/)
- [Auggie CLI Documentation](https://docs.augmentcode.com/cli/overview)
- [Model Context Protocol](https://modelcontextprotocol.io/)
- [MCP Specification](https://spec.modelcontextprotocol.io/)

