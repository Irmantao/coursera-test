# Claude Desktop Configuration for YAZIO

This repository contains a Claude Desktop configuration file for integrating the YAZIO MCP server.

## What is YAZIO MCP Server?

The YAZIO MCP server connects Claude Desktop to your Yazio nutrition data, allowing you to:
- Track your diet
- Search food products
- Manage your nutrition goals
- Access your Yazio account data directly from Claude

## Setup Instructions

### 1. Install Configuration File

Copy the `claude_desktop_config.json` file to your Claude Desktop configuration directory:

**macOS:**
```bash
cp claude_desktop_config.json ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

**Windows:**
```bash
copy claude_desktop_config.json %APPDATA%\Claude\claude_desktop_config.json
```

**Linux:**
```bash
cp claude_desktop_config.json ~/.config/Claude/claude_desktop_config.json
```

### 2. Configure Your Credentials

Edit the configuration file and replace the placeholder values with your actual Yazio credentials:

```json
{
  "mcpServers": {
    "yazio": {
      "command": "npx",
      "args": ["-y", "yazio-mcp"],
      "env": {
        "YAZIO_USERNAME": "your_email@email.com",
        "YAZIO_PASSWORD": "your_password"
      }
    }
  }
}
```

Replace:
- `your_email@email.com` with your Yazio account email
- `your_password` with your Yazio account password

### 3. Restart Claude Desktop

After updating the configuration file, restart Claude Desktop for the changes to take effect.

## Important Notes

- This is an **unofficial** MCP server that uses a reverse-engineered API
- Yazio does not provide an official API
- The server may stop working at any time if Yazio changes their internal API
- Your credentials are stored locally and only used to authenticate with Yazio

## Requirements

- Node.js and npm installed on your system
- A valid Yazio account

## Source

MCP Server: [yazio-mcp](https://github.com/fliptheweb/yazio-mcp)
MCP Market: [https://mcpmarket.com/server/yazio](https://mcpmarket.com/server/yazio)
