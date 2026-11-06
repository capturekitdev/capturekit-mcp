# CaptureKit MCP Server

Official [Model Context Protocol](https://modelcontextprotocol.io) server for the [CaptureKit](https://www.capturekit.dev) screenshot API.

Capture websites as PNG, JPEG, WebP or PDF, extract their content, or analyse them with AI.

It lets Claude, Cursor, VS Code, Windsurf and any MCP client call the API as tools. Runs over **stdio** (local) or **Streamable HTTP** (self-hosted).

## Requirements

- Node.js 20 or newer
- a CaptureKit API key — [sign up](https://app.capturekit.dev/register), then copy it from the [dashboard](https://app.capturekit.dev)

## Hosted server (recommended)

CaptureKit runs this server for you at **`https://mcp.capturekit.dev/`** (Streamable HTTP). Send your API key in the `x-api-key` header; nothing to install.

```sh
claude mcp add --transport http capturekit https://mcp.capturekit.dev/ --header "x-api-key: YOUR_API_KEY"
```

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

The hosted server and this package expose the same API; run the package locally when you want stdio, your own network egress, or to self-host it.

## Run it locally

### Claude Code

```sh
claude mcp add capturekit --env CAPTUREKIT_API_KEY=YOUR_API_KEY -- npx -y @capturekitdev/mcp
```

### Claude Desktop, Cursor, Windsurf and other clients

Add the server to the client's MCP configuration (`claude_desktop_config.json`, `.cursor/mcp.json`…):

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

### VS Code

```json
{
  "servers": {
    "capturekit": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@capturekitdev/mcp"],
      "env": {
        "CAPTUREKIT_API_KEY": "${input:capturekit-api-key}"
      }
    }
  },
  "inputs": [
    {
      "type": "promptString",
      "id": "capturekit-api-key",
      "description": "CaptureKit API key",
      "password": true
    }
  ]
}
```

### Claude Desktop extension

Download the `.mcpb` bundle from the releases page and open it; Claude Desktop asks for your API key.

### Docker

```sh
docker build -t capturekit-mcp .
docker run -i --rm -e CAPTUREKIT_API_KEY=YOUR_API_KEY capturekit-mcp
```

## Tools

| Tool              | Description                                                                                                                                                                   |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `analyze`         | AI Content API analyzes any webpage and returns AI insights summary, value proposition, features, categories, use cases, and optional contact signals.                        |
| `capture`         | Browser-like Screenshot API that captures webpages as PNG/JPEG/WebP or PDF for previews, QA, reporting, monitoring, and archiving no headless setup.                          |
| `content`         | CaptureKit Content API extracts structured webpage data, metadata, links, HTML/Markdown: in one request for SEO, monitoring, indexing and automation.                         |
| `stats`           | Infrastructure health for an engine or endpoint over the last hour. Not scoped to your account or tenant. Enabled per account: HTTP 403 means contact support to activate it. |
| `usage`           | Retrieve real-time usage statistics and credit balance for your CaptureKit account.                                                                                           |
| `website_crawler` | Website Crawler API fetches raw HTML from any webpage in request mode (no rendering) for fast scraping, audits, monitoring, and link/metadata extraction.                     |

Every tool that calls the API consumes credits. JSON results are returned as text; images are returned inline and other files (PDF…) as embedded resources, up to 4 MB.

## Streamable HTTP

```sh
npx @capturekitdev/mcp --http --port 3000
```

The endpoint is `POST /mcp` (stateless, JSON responses). Each client sends its own key in the `x-api-key` header (or `Authorization: Bearer <key>`); the server never uses a key of its own. It binds to `127.0.0.1` by default and rejects browser origins unless you allow them — put it behind TLS before exposing it.

`createHttpHandler()` is exported for Fetch-compatible runtimes (Cloudflare Workers, Deno, Bun):

```ts
import { createHttpHandler } from "@capturekitdev/mcp";

export default { fetch: createHttpHandler({ allowedOrigins: [] }) };
```

## Security

- The API key is read from `CAPTUREKIT_API_KEY` (stdio) or the request headers (HTTP) and removed from error messages.
- Content fetched from websites is untrusted: the server instructions tell the model to treat it as data, not instructions.
- See [SECURITY.md](SECURITY.md) to report a vulnerability.

## Development

```sh
npm install
npm test
npm run build
npx @modelcontextprotocol/inspector node dist/stdio.js
```

## Built on the SDK

The server uses the official [Node.js SDK](https://github.com/capturekitdev/capturekit-node) (`@capturekitdev/sdk`).

## All CaptureKit SDKs

| SDK        | Package                                                                      | Install                                         |
| ---------- | ---------------------------------------------------------------------------- | ----------------------------------------------- |
| Node.js    | [`@capturekitdev/sdk`](https://www.npmjs.com/package/@capturekitdev/sdk)     | `npm install @capturekitdev/sdk`                |
| Python     | [`capturekitdev`](https://pypi.org/project/capturekitdev/)                   | `pip install capturekitdev`                     |
| Go         | [`capturekit-go`](https://pkg.go.dev/github.com/capturekitdev/capturekit-go) | `go get github.com/capturekitdev/capturekit-go` |
| .NET       | [`CaptureKit`](https://www.nuget.org/packages/CaptureKit)                    | `dotnet add package CaptureKit`                 |
| Ruby       | [`capturekitdev`](https://rubygems.org/gems/capturekitdev)                   | `gem install capturekitdev`                     |
| PHP        | [`capturekitdev/sdk`](https://packagist.org/packages/capturekitdev/sdk)      | `composer require capturekitdev/sdk`            |
| Java       | [`capturekit-java`](https://github.com/capturekitdev/capturekit-java)        | build from source, see its README               |
| CLI        | [`@capturekitdev/cli`](https://www.npmjs.com/package/@capturekitdev/cli)     | `npm install -g @capturekitdev/cli`             |
| MCP server | [`@capturekitdev/mcp`](https://www.npmjs.com/package/@capturekitdev/mcp)     | `npx -y @capturekitdev/mcp`                     |

## License

MIT — see [LICENSE](LICENSE).
