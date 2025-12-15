# MCP jefitWorkouts Debugging Guide

## Issue
MCP server "jefitWorkouts" shows "Server disconnected" error in Claude.ai

## Understanding the Problem

### What is MCP?
MCP (Model Context Protocol) allows Claude to connect to external services and data sources. When a server is "disconnected," it means Claude cannot communicate with that service.

## Troubleshooting Steps

### Step 1: Check if You Actually Need This Server

**Question:** Do you actually use JEFIT workout tracking?

- **NO** → Simply remove the connector from Claude settings
- **YES** → Continue to Step 2

### Step 2: Remove the Broken Connector

1. In Claude.ai, click the gear icon (⚙️) or "Manage connectors"
2. Find "jefitWorkouts" in the list
3. Click the toggle to disable it OR delete it completely
4. Refresh the page

### Step 3: Reinstall the jefitWorkouts MCP Server (If Needed)

If you want to use JEFIT integration, you need to:

#### A. Install the MCP Server Locally

```bash
# Create a directory for MCP servers
mkdir -p ~/mcp-servers
cd ~/mcp-servers

# Clone or install the jefit MCP server
# (You'll need the actual repository URL or npm package name)
# Example:
# npm install -g @your-username/jefit-mcp-server
# OR
# git clone https://github.com/your-username/jefit-mcp-server.git
# cd jefit-mcp-server
# npm install
```

#### B. Find the Server Command

After installation, you need to know:
- The command to run the server (e.g., `node /path/to/server.js`)
- Any required environment variables or API keys

#### C. Configure in Claude.ai

1. Go to Claude.ai settings
2. Add a new MCP connector
3. Enter the server details:
   - **Name:** jefitWorkouts
   - **Command:** The path to your server executable
   - **Args:** Any arguments needed
   - **Environment variables:** API keys, etc.

## Common Issues

### Issue: "I don't know where the server should come from"

**Answer:** MCP servers are typically:
- Installed via npm: `npm install -g package-name`
- Cloned from GitHub repositories
- Custom scripts you write yourself

### Issue: "I didn't install this myself"

**Answer:** Someone may have:
- Shared a Claude.ai configuration with you
- You copied settings from a tutorial
- You clicked "Add connector" and entered a name that doesn't exist

**Solution:** Just remove it if you don't need it.

### Issue: "I installed it but it still doesn't work"

**Checklist:**
- [ ] Is Node.js installed? (`node --version`)
- [ ] Can you run the server manually? (`node /path/to/server.js`)
- [ ] Does the server require API keys or credentials?
- [ ] Is the path in the Claude.ai configuration correct?
- [ ] Are there any error logs?

## Quick Fix: Just Remove It

If you don't actually need JEFIT integration:

1. Open Claude.ai
2. Click your settings/profile
3. Go to "Manage connectors" or "Developer settings"
4. Find "jefitWorkouts"
5. Toggle it OFF or DELETE it
6. Refresh Claude.ai
7. Error should be gone ✓

## Finding the jefitWorkouts MCP Server

If you want to actually use this:

### Check if it's a public package:
```bash
npm search jefit mcp
npm search workout mcp
```

### Check if it's a GitHub repository:
- Search GitHub for "jefit mcp server"
- Search GitHub for "jefit model context protocol"

### It might be custom:
- You may have created this yourself
- Someone may have shared it with you
- It might be from a tutorial or course

## Creating Your Own JEFIT MCP Server

If the server doesn't exist and you want to create it:

```javascript
// Example basic MCP server structure
// File: jefit-mcp-server/index.js

import { MCPServer } from '@modelcontextprotocol/sdk';

const server = new MCPServer({
  name: 'jefitWorkouts',
  version: '1.0.0'
});

// Add your JEFIT API integration here
server.tool('getWorkouts', async () => {
  // Fetch workouts from JEFIT API
  return { workouts: [] };
});

server.listen();
```

## Next Steps

1. **Immediate fix:** Remove the connector if you don't need it
2. **If you need it:** Determine where the MCP server should come from
3. **Install properly:** Follow installation instructions
4. **Configure:** Add correct paths and credentials to Claude.ai
5. **Test:** Verify the connection works

## Additional Resources

- [MCP Documentation](https://modelcontextprotocol.io)
- [Claude.ai MCP Setup Guide](https://docs.anthropic.com/claude/docs/mcp)
- Check if JEFIT has an official API documentation

---

**TL;DR:** The server "jefitWorkouts" is configured but not actually installed or running. Either remove it from your Claude.ai connectors settings, or install and properly configure the actual MCP server.
