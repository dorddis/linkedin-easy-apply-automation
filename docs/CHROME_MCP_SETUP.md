# Chrome DevTools MCP Setup Guide

Complete guide to setting up Chrome DevTools MCP for LinkedIn automation.

## What is Chrome DevTools MCP?

Chrome DevTools MCP is a Model Context Protocol server that allows Claude Code to control a Chrome browser. It enables:
- Navigating to URLs
- Taking page snapshots
- Clicking elements
- Filling forms
- Pressing keys

## Installation

### Step 1: Verify Prerequisites

```bash
# Check Node.js is installed
node --version  # Should be v18+

# Check npm is available
npm --version
```

### Step 2: Find Your Claude Code Config

Claude Code config location varies:
- **Default:** `~/.claude.json` or `C:\Users\YourUsername\.claude.json`
- **Personal config:** `~/.claude-personal/.claude.json`

Open the file in a text editor.

### Step 3: Add MCP Server Config

Add this inside your `.claude.json`:

**Windows:**
```json
{
  "mcpServers": {
    "chrome-devtools": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "chrome-devtools-mcp@latest",
        "--userDataDir=C:\\Users\\YourUsername\\.chrome-debug-linkedin"
      ],
      "env": {}
    }
  }
}
```

**Mac/Linux:**
```json
{
  "mcpServers": {
    "chrome-devtools": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "chrome-devtools-mcp@latest",
        "--userDataDir=/Users/yourusername/.chrome-debug-linkedin"
      ],
      "env": {}
    }
  }
}
```

**Important:** Replace `YourUsername` / `yourusername` with your actual username.

### Step 4: Restart Claude Code

Close and reopen Claude Code for changes to take effect.

## Persistent Sessions

### Why Use userDataDir?

Without `--userDataDir`, Chrome creates a temporary profile that:
- Loses your LinkedIn login every restart
- Requires manual login each time

With `--userDataDir`:
- Login persists between sessions
- Cookies and localStorage saved
- No re-authentication needed

### First-Time Login

1. Open Claude Code in your project directory
2. Ask: "Navigate to linkedin.com"
3. Chrome opens with the MCP server
4. Log in to LinkedIn manually
5. Session is now saved

### Verify Persistence

1. Close Claude Code
2. Reopen Claude Code
3. Ask: "Navigate to linkedin.com"
4. You should already be logged in

## Troubleshooting

### "Cannot find module 'chrome-devtools-mcp'"

```bash
# Clear npm cache and retry
npm cache clean --force
npx chrome-devtools-mcp@latest --version
```

### Chrome Doesn't Open

1. Check JSON syntax in `.claude.json` (no trailing commas)
2. Verify paths use double backslashes on Windows (`\\`)
3. Try running manually:
```bash
npx chrome-devtools-mcp@latest --userDataDir=C:\Users\YourUsername\.chrome-debug-linkedin
```

### "Session not persisting"

1. Check userDataDir path exists and is writable
2. Make sure you're not using `--isolated` flag
3. Verify the path in config matches your system

### Browser Opens But Can't Control

1. Close all Chrome instances
2. Make sure no other Chrome is running with same profile
3. Restart Claude Code

## Advanced: Multiple Browser Instances

For parallel automation (e.g., different job markets), add multiple MCP servers:

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "chrome-devtools-mcp@latest",
        "--userDataDir=C:\\Users\\YourUsername\\.chrome-debug-linkedin-1"
      ]
    },
    "chrome-devtools-2": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "chrome-devtools-mcp@latest",
        "--userDataDir=C:\\Users\\YourUsername\\.chrome-debug-linkedin-2"
      ]
    }
  }
}
```

Note: Each instance needs a separate userDataDir to avoid conflicts.

## Security Notes

- The userDataDir contains your LinkedIn session cookies
- Do not share this directory
- Do not commit it to git (add to .gitignore)
- Only use for automation, not personal browsing

## References

- [Chrome DevTools MCP GitHub](https://github.com/anthropics/chrome-devtools-mcp)
- [MCP Protocol Documentation](https://modelcontextprotocol.io/)
