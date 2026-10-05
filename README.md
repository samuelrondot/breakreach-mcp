# Breakreach MCP Server: a social media MCP server for Claude, ChatGPT, Cursor and any MCP client

[Breakreach](https://www.breakreach.com) is an AI-native social media scheduling platform. Its remote [MCP](https://modelcontextprotocol.io) server lets any MCP-compatible client (claude.ai, Claude Desktop, Claude Code, ChatGPT, Cursor and more) run your social media across **12 platforms**: X, Instagram, TikTok, Facebook, Threads, LinkedIn, YouTube, Pinterest, Bluesky, Reddit, Telegram, and Discord.

- **Plan and publish**: schedule posts, publish now, save drafts, edit or reschedule anything that hasn't gone out yet
- **Manage your community**: read and reply to comments, hide or delete spam, answer direct messages
- **Understand what works**: per-post performance, account stats, daily trends, and the full insights of any post

No local install required. The server is hosted, stateless, and speaks Streamable HTTP.

```
https://api.breakreach.com/mcp
```

## Setup

MCP access requires an active Breakreach plan or free trial.

### Option 1: from the connector directory (one click)

- **Claude** (web and desktop): open [Breakreach in the Claude directory](https://claude.ai/directory/api-breakreach-com), or go to **Settings → Connectors**, find Breakreach and click **Connect**
- **ChatGPT**: go to **Settings → Apps & Connectors**, search for Breakreach and click **Connect**

Sign in with your Breakreach account and approve access. No API key needed.

### Option 2: custom connector with OAuth

Any client that supports OAuth for remote MCP servers only needs the URL:

- **claude.ai / Claude Desktop**: **Settings → Connectors → Add custom connector**, paste `https://api.breakreach.com/mcp`
- **Claude Code**:

  ```bash
  claude mcp add --transport http breakreach https://api.breakreach.com/mcp
  ```

  then run `/mcp` inside Claude Code to sign in.

### Option 3: API key

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

25 tools. Every tool has a title and explicit `readOnlyHint`, `destructiveHint` and `openWorldHint` annotations, so clients can tell reads from writes and ask before destructive actions.

### Posts

| Tool | Description |
| --- | --- |
| `list_workspaces` | List your workspaces (slug, name, timezone) |
| `list_accounts` | List the connected social accounts of a workspace |
| `create_workspace` | Create a workspace for a new brand or client, with its own accounts, posts and posting schedule |
| `create_connect_link` | Get a link where you, or a client of yours, connect social accounts to a workspace without a Breakreach login |
| `list_posts` | List draft, scheduled, published and failed posts, with metrics for published ones |
| `create_post` | Schedule a post (explicit time or next free slot), publish it now, or save it as a draft. Platform options: `tiktokSettings`, `youtubeSettings` (title, visibility), `instagramSettings` (post or story; 2 to 10 files make an Instagram carousel), `pinterestBoardId`, `redditSubreddit`, `redditFlairText` |
| `update_post` | Edit, reschedule or publish now any post that hasn't gone out yet, retry a failed post, or move a post back to drafts |
| `delete_post` | Delete a post: scheduled posts are unscheduled, published posts are removed from Breakreach only |
| `upload_media` | Host an image or video from a public URL so it stays available until publish time |
| `get_next_slot` | Get the next free slot from your posting schedule |
| `list_pinterest_boards` | List the boards of the connected Pinterest account |

### Comments

| Tool | Description |
| --- | --- |
| `list_recent_comments` | Latest comments across your Instagram, Facebook, Threads and YouTube accounts (including posts not published with Breakreach), plus replies to your X posts |
| `get_post_comments` | Comments on one post published with Breakreach (Instagram, Facebook, Threads, X, YouTube) |
| `reply_to_comment` | Reply to a comment on Instagram, Facebook, Threads, X or YouTube |
| `hide_comment` | Hide or unhide a comment on Instagram, Facebook or Threads |
| `delete_comment` | Delete a comment on Instagram or Facebook |

### Direct messages

| Tool | Description |
| --- | --- |
| `list_conversations` | List DM conversations on Instagram, Facebook Messenger and X |
| `get_conversation_messages` | Read the messages of one conversation |
| `send_message` | Reply to a DM (Instagram and Messenger accept replies within 24 hours of the person's last message) |

### Analytics

| Tool | Description |
| --- | --- |
| `get_content_performance` | Views, likes, comments, shares and engagement rate per post over up to a year, on Instagram, Facebook, Threads, X, YouTube and Bluesky, including posts not published with Breakreach |
| `get_post_insights` | Everything about one post: every lifetime metric the platform exposes (reach, saves, watch time, retention…), its comments, and a comparison with your average post |
| `get_account_stats` | Followers and 28-day views, reach and engagement for every connected account |
| `get_account_trends` | Daily account trends over up to 90 days (Facebook Pages, Instagram, Threads) |
| `get_post_metrics` | Views, likes, comments and shares of one post published with Breakreach |
| `get_analytics` | Totals and top posts across everything published with Breakreach |

## Example prompts

- "Write 5 posts about my product launch and schedule them at my next free slots this week."
- "Upload this image and publish it to Instagram and Threads tomorrow at 9am."
- "Save this announcement as a draft for LinkedIn and X, I'll review it in Breakreach."
- "Move tomorrow's LinkedIn post to Friday 9am and make it shorter."
- "Show me the new comments on my posts, suggest a reply to each one, and hide the spam."
- "Did I get any new DMs on Instagram? Summarize them and draft replies."
- "Which of my posts performed best over the last 90 days, and what do they have in common?"
- "Cross-post my latest announcement to Bluesky, Telegram, and Discord."
- "Create a workspace for my client Acme and give me a link so they can connect their Instagram and LinkedIn."

## Step-by-step guides

Setup steps for each network, and what each one accepts (formats and limits):

| Network | Claude | ChatGPT |
| --- | --- | --- |
| Instagram | [Post to Instagram from Claude](https://www.breakreach.com/connect/post-to-instagram-from-claude) | [Post to Instagram from ChatGPT](https://www.breakreach.com/connect/post-to-instagram-from-chatgpt) |
| TikTok | [Post to TikTok from Claude](https://www.breakreach.com/connect/post-to-tiktok-from-claude) | [Post to TikTok from ChatGPT](https://www.breakreach.com/connect/post-to-tiktok-from-chatgpt) |
| X (Twitter) | [Post to X from Claude](https://www.breakreach.com/connect/post-to-x-from-claude) | [Post to X from ChatGPT](https://www.breakreach.com/connect/post-to-x-from-chatgpt) |
| LinkedIn | [Post to LinkedIn from Claude](https://www.breakreach.com/connect/post-to-linkedin-from-claude) | [Post to LinkedIn from ChatGPT](https://www.breakreach.com/connect/post-to-linkedin-from-chatgpt) |
| YouTube | [Post to YouTube from Claude](https://www.breakreach.com/connect/post-to-youtube-from-claude) | [Post to YouTube from ChatGPT](https://www.breakreach.com/connect/post-to-youtube-from-chatgpt) |
| Facebook | [Post to Facebook from Claude](https://www.breakreach.com/connect/post-to-facebook-from-claude) | [Post to Facebook from ChatGPT](https://www.breakreach.com/connect/post-to-facebook-from-chatgpt) |
| Threads | [Post to Threads from Claude](https://www.breakreach.com/connect/post-to-threads-from-claude) | [Post to Threads from ChatGPT](https://www.breakreach.com/connect/post-to-threads-from-chatgpt) |
| Pinterest | [Post to Pinterest from Claude](https://www.breakreach.com/connect/post-to-pinterest-from-claude) | [Post to Pinterest from ChatGPT](https://www.breakreach.com/connect/post-to-pinterest-from-chatgpt) |
| Bluesky | [Post to Bluesky from Claude](https://www.breakreach.com/connect/post-to-bluesky-from-claude) | [Post to Bluesky from ChatGPT](https://www.breakreach.com/connect/post-to-bluesky-from-chatgpt) |
| Reddit | [Post to Reddit from Claude](https://www.breakreach.com/connect/post-to-reddit-from-claude) | [Post to Reddit from ChatGPT](https://www.breakreach.com/connect/post-to-reddit-from-chatgpt) |
| Telegram | [Post to Telegram from Claude](https://www.breakreach.com/connect/post-to-telegram-from-claude) | [Post to Telegram from ChatGPT](https://www.breakreach.com/connect/post-to-telegram-from-chatgpt) |

## Links

- Website: [breakreach.com](https://www.breakreach.com)
- Developers (REST API and MCP): [breakreach.com/developers](https://www.breakreach.com/developers)
- Free tools and guides (best time to post, image sizes, AI post generators): [breakreach.com/guides](https://www.breakreach.com/guides)
- Claude directory: [Breakreach connector](https://claude.ai/directory/api-breakreach-com)
- MCP endpoint: `https://api.breakreach.com/mcp`

## License

[MIT](LICENSE)
