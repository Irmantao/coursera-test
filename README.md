# Claude Desktop MCP Server Configuration

This repository contains a Claude Desktop configuration file for integrating multiple MCP servers:
- **YAZIO** - Nutrition tracking and diet management
- **Redmine** - Project management and issue tracking

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

Edit the configuration file and replace the placeholder values with your actual credentials:

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
    },
    "redmine": {
      "command": "uvx",
      "args": ["--from", "mcp-redmine==2025.09.03.141435", "--refresh-package", "mcp-redmine", "mcp-redmine"],
      "env": {
        "REDMINE_URL": "https://your-redmine-instance.com",
        "REDMINE_API_KEY": "your-api-key"
      }
    }
  }
}
```

**For YAZIO:**
- `your_email@email.com` → your Yazio account email
- `your_password` → your Yazio account password

**For Redmine:**
- `https://your-redmine-instance.com` → your Redmine instance URL
- `your-api-key` → your Redmine API access key

### 3. Restart Claude Desktop

After updating the configuration file, restart Claude Desktop for the changes to take effect.

## Important Notes

- This is an **unofficial** MCP server that uses a reverse-engineered API
- Yazio does not provide an official API
- The server may stop working at any time if Yazio changes their internal API
- Your credentials are stored locally and only used to authenticate with Yazio

## What is Redmine MCP Server?

The Redmine MCP server connects Claude Desktop to your Redmine project management instance, allowing you to:
- View and create issues
- Update issue status and assignments
- Search projects and issues
- Track time entries
- Manage project workflows directly from Claude

### Redmine Configuration

The configuration file includes Redmine server setup:

```json
{
  "mcpServers": {
    "redmine": {
      "command": "uvx",
      "args": ["--from", "mcp-redmine==2025.09.03.141435", "--refresh-package", "mcp-redmine", "mcp-redmine"],
      "env": {
        "REDMINE_URL": "https://your-redmine-instance.com",
        "REDMINE_API_KEY": "your-api-key"
      }
    }
  }
}
```

Replace:
- `https://your-redmine-instance.com` with your Redmine instance URL
- `your-api-key` with your Redmine API access key

**To get your Redmine API key:**
1. Log into your Redmine instance
2. Go to "My account"
3. Find "API access key" section
4. Click "Show" or "Generate" to get your key

## Requirements

- **For YAZIO:**
  - Node.js and npm installed on your system
  - A valid Yazio account

- **For Redmine:**
  - Python's `uv` package manager installed ([installation instructions](https://github.com/astral-sh/uv))
  - Valid Redmine account with API access enabled
  - Redmine API key

## Sources

- **YAZIO MCP Server:** [yazio-mcp](https://github.com/fliptheweb/yazio-mcp) | [MCP Market](https://mcpmarket.com/server/yazio)
- **Redmine MCP Server:** [mcp-redmine](https://github.com/runekaagaard/mcp-redmine)
