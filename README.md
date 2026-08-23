# Breakreach MCP Server — run your social media from Claude, Cursor or any MCP client

[Breakreach](https://www.breakreach.com) is an AI-native social media scheduling platform. Its remote [MCP](https://modelcontextprotocol.io) server lets any MCP-compatible client — Claude Desktop, claude.ai, Cursor, and more — schedule, publish, and analyze posts across **12 platforms**: X, Instagram, TikTok, Facebook, Threads, LinkedIn, YouTube, Pinterest, Bluesky, Reddit, Telegram, and Discord.

No local install required. The server is hosted, stateless, and speaks Streamable HTTP.

```
https://api.breakreach.com/mcp
```

## Setup

A Breakreach **Pro or Agency** plan is required for MCP access.

### Option 1 — OAuth (recommended, zero setup)

The server supports OAuth, so OAuth-capable clients like claude.ai and Claude Desktop don't need an API key:

1. In claude.ai (or Claude Desktop), go to **Settings → Connectors → Add custom connector**
2. Paste `https://api.breakreach.com/mcp`
3. Sign in with your Breakreach account and approve access

That's it — no API key needed.

### Option 2 — API key

For clients that don't do OAuth (plain header-based configs), the server also accepts a Bearer API key:

1. Sign in at [breakreach.com](https://www.breakreach.com)
2. Go to **Settings → API & MCP**
3. Create an API key (it looks like `br_...`)
4. Send it as a header: `Authorization: Bearer br_...`

In `claude_desktop_config.json` via `mcp-remote`:

```json
{
  "mcpServers": {
    "breakreach": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://api.breakreach.com/mcp",
        "--header",
        "Authorization: Bearer br_YOUR_API_KEY"
      ]
    }
  }
}
```

#### Cursor

Add to `~/.cursor/mcp.json` (or your project's `.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "breakreach": {
      "url": "https://api.breakreach.com/mcp",
      "headers": {
        "Authorization": "Bearer br_YOUR_API_KEY"
      }
    }
  }
}
```

## Authentication details

For developers building MCP clients: the server implements OAuth 2.1 with PKCE and dynamic client registration, per the [MCP authorization spec](https://modelcontextprotocol.io/specification/basic/authorization). Discovery endpoints:

- `https://api.breakreach.com/.well-known/oauth-authorization-server`
- `https://api.breakreach.com/.well-known/oauth-protected-resource`

## Tools

| Tool | Description |
| --- | --- |
| `list_workspaces` | List the workspaces your API key can access |
| `list_accounts` | List connected social accounts in a workspace |
| `list_posts` | List scheduled, published, and failed posts |
| `create_post` | Schedule or publish a post — supports `useNextSlot`, `tiktokSettings`, `pinterestBoardId`, `redditSubreddit` |
| `delete_post` | Delete a scheduled post |
| `get_analytics` | Get performance analytics for your accounts and posts |
| `upload_media` | Upload images or videos to attach to posts |
| `get_next_slot` | Get the next available best-time posting slot |
| `list_pinterest_boards` | List Pinterest boards for a connected account |

## Example prompts

- "Schedule this post to X and LinkedIn at the next best-time slot."
- "Upload this image and publish it to Instagram and Threads tomorrow at 9am."
- "How did my TikTok posts perform this week?"
- "Cross-post my latest announcement to Bluesky, Telegram, and Discord."

## Links

- Website: [breakreach.com](https://www.breakreach.com)
- MCP endpoint: `https://api.breakreach.com/mcp`

## License

[MIT](LICENSE)
