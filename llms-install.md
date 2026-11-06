# Installing the CaptureKit MCP server

Instructions for AI agents installing this server for a user.

1. Check that Node.js 20 or newer is available (`node --version`).
2. Ask the user for their CaptureKit API key. They can create one at https://app.capturekit.dev/register and copy it from https://app.capturekit.dev.
   Never print the key back or commit it to a file in a repository.
3. Prefer the hosted server: add it with URL `https://mcp.capturekit.dev/` and the header `x-api-key: <key>`:

```json
{
  "mcpServers": {
    "capturekit": {
      "url": "https://mcp.capturekit.dev/",
      "headers": {
        "x-api-key": "YOUR_API_KEY"
      }
    }
  }
}
```

Or run it locally over stdio:

```json
{
  "mcpServers": {
    "capturekit": {
      "command": "npx",
      "args": ["-y", "@capturekitdev/mcp"],
      "env": {
        "CAPTUREKIT_API_KEY": "YOUR_API_KEY"
      }
    }
  }
}
```

4. Restart the client and check that the `analyze` tool is listed.

The server needs no other configuration. Each tool call that reaches the API consumes CaptureKit credits.
