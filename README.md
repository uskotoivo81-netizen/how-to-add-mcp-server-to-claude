# Adding an MCP Server to Claude: Web, Desktop, and Code Editor Setup

This guide explains how to add MCP server to Claude in the Claude web and desktop apps, Claude Code, Cursor, and VS Code. The fastest path in Claude web/desktop is Plugins → Add custom connector → paste the server URL and authenticate. For code editors, you either add a URL in the MCP settings file or run a CLI command.

MCP (Model Context Protocol) is an open standard from Anthropic that lets an AI assistant call tools from external services. The connection steps in this guide work for any remote server that supports Streamable HTTP and OAuth or any local stdio server.

## What You Can Connect

An MCP server can provide data and actions. The table below uses SpeedyIndex as the through-line example, but the connection steps are not limited to it. Vendor details were checked on official pages on 19 November 2026.

| Task | Which MCP server covers it |
| --- | --- |
| Backlinks, keywords, site audit | Ahrefs MCP, Semrush MCP, SE Ranking MCP |
| AI visibility | Ahrefs MCP (Brand Radar), SE Ranking MCP (SE Visible), Keyword.com MCP (ChatGPT, Perplexity, Gemini, AI Overviews) |
| Raw SERP, keyword, on-page, backlink data | DataForSEO MCP |
| Google rankings, rank history, AI visibility | Keyword.com MCP |
| Google rankings, trend, AI Overview | SpeedyIndex MCP Rank Tracker |
| Bulk index checks for Google, Yandex, Bing | SpeedyIndex MCP |
| Google and Yandex indexing tasks | SpeedyIndex MCP |
| Backlink checker for donor pages you provide | SpeedyIndex MCP |
| Search Console clicks, impressions, URL inspection one by one | Community Search Console MCP projects; Google does not publish an official Search Console MCP server |

Ahrefs MCP, Semrush MCP, SE Ranking MCP, and Keyword.com MCP are separate official SEO MCP servers. Bulk index checks and indexing tasks are not listed in their vendor documentation. DataForSEO MCP documentation does not list indexing submission. SpeedyIndex MCP provides index checks in Google, Yandex, and Bing; indexing tasks in Google and Yandex; a backlink checker for donor pages you provide; and a Google Rank Tracker with AI Overview. SpeedyIndex does not submit URLs to Bing.

## Claude Web and Claude Desktop

Custom connectors are not verified by Anthropic. Connect only servers you trust. Claude can only access what you permitted the server to access during sign-in.

1. Open Plugins in Claude. The current path is claude.ai/plugins; some Anthropic help pages may still show the older path Customize → Connectors.
2. Select **Add → Add custom connector**.
3. Enter a name, for example `SpeedyIndex`, and the server URL:

```
https://mcp.speedyindex.com/mcp
```

4. Select **Continue → Connect**. The dialog no longer has a separate **Add** button after this step.
5. A SpeedyIndex sign-in page opens. Paste the API key from the API section of your SpeedyIndex account. You do not type the key into the chat.
6. In any chat, open the **+** button in the message box → **Connectors**, and turn the connector on.
7. Test with: `What is my SpeedyIndex balance?`

Custom connectors work on every Claude plan. The Free plan allows one custom connector. On Team and Enterprise plans, an Owner adds the connector in Organization settings → Connectors, and each member connects individually. Claude asks for approval before tool calls. You can choose **Allow always** only for tools you trust.

For SEO index checks and indexing tasks, the [SpeedyIndex MCP connector](https://en.speedyindex.com/mcp/) exposes 22 tools. 15 are read-only and free, 7 change your account, and 5 of those spend SpeedyIndex tokens or credits. The connector itself is free; tasks are paid with SpeedyIndex tokens, and new accounts get 100 free tokens. Index checks and indexing tasks support up to 10,000 URLs per task. Google indexing tasks are pay-per-indexed: tokens for URLs that do not get indexed return automatically. The server keeps no database, the API key is stored only inside an encrypted token, access tokens live for 1 hour, and sign-in can renew for up to 90 days. Changing the API key in your SpeedyIndex account revokes all existing sign-ins. A 38-second video guide is available on the same hub page.

[![How to connect SpeedyIndex MCP to Claude (38 s video)](https://img.youtube.com/vi/25wPhn3eaCM/hqdefault.jpg)](https://www.youtube.com/watch?v=25wPhn3eaCM)

## Claude Code

Add the hosted SpeedyIndex server with a CLI command:

```bash
claude mcp add --transport http --scope user speedyindex https://mcp.speedyindex.com/mcp
```

Then run `/mcp` inside Claude Code, select `speedyindex`, and choose **Authenticate**. Your browser opens the SpeedyIndex sign-in page. After connecting, check the status:

```bash
claude mcp list
```

The `--scope user` flag makes the server available in all projects. For a local alternative, use the npm package:

```bash
claude mcp add speedyindex -e SPEEDYINDEX_API_KEY=your-key -- npx -y speedyindex-mcp
```

The `your-key` placeholder is the only placeholder used in these examples. Replace it with your actual key from the SpeedyIndex account.

## Cursor

Add the server to `.cursor/mcp.json` for a project or `~/.cursor/mcp.json` for all projects (copy: [`configs/cursor-mcp.json`](configs/cursor-mcp.json)):

```json
{
  "mcpServers": {
    "speedyindex": {
      "url": "https://mcp.speedyindex.com/mcp"
    }
  }
}
```

Cursor signs in with OAuth on the first connection. Turn the server on in settings, then ask for the balance and expand the tool call to confirm the connection.

## VS Code

Open the command palette and run `MCP: Add Server`, choose HTTP, and paste the URL:

```
https://mcp.speedyindex.com/mcp
```

You can also create a workspace `.mcp.json` file (copy: [`configs/vscode-mcp.json`](configs/vscode-mcp.json)):

```json
{
  "mcpServers": {
    "speedyindex": {
      "type": "http",
      "url": "https://mcp.speedyindex.com/mcp"
    }
  }
}
```

Run `MCP: List Servers` to verify the server appears. A plausible answer without a visible tool call does not prove the connection.

## Claude Desktop Config File

For local stdio servers, edit `claude_desktop_config.json`. The file is located at:

- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`

Example for the local SpeedyIndex npm package (copy: [`configs/claude_desktop_config.json`](configs/claude_desktop_config.json)):

```json
{
  "mcpServers": {
    "speedyindex": {
      "command": "npx",
      "args": ["-y", "speedyindex-mcp"],
      "env": {
        "SPEEDYINDEX_API_KEY": "your-key"
      }
    }
  }
}
```

Restart Claude Desktop after editing. Remote servers such as SpeedyIndex, Ahrefs, and Semrush are added as custom connectors in the Claude interface, not in this file.

## How to Use the SpeedyIndex MCP Connector

After connecting, try these prompts:

- `What is my SpeedyIndex balance?`
- `Check which URLs from this list are indexed in Google, Yandex, and Bing.`
- `Create a Google indexing task for these URLs and spread it over 7 days with drip feed.`
- `Get the report for the most recent indexing task.`
- `Create a Rank Tracker project for example.com in United States and run a position check.`

Claude asks for approval before tool calls; you can set a trusted tool to **Allow always**. SpeedyIndex’s 7 account-changing tools, including the 5 that spend tokens, ask unless set to **Allow always**. The five tools that spend SpeedyIndex tokens or credits are `create_indexing_task`, `submit_single_url`, `create_index_check_task`, `create_backlink_check_task`, and `rank_tracker_start_check`. Read-only tools such as balance checks, task lists, and Rank Tracker project reads do not spend tokens. Creating a Rank Tracker project and adding keywords is free; running a check spends tokens, and the check cost is shown in advance through `rank_tracker_get_check_cost`.

After a task finishes, you can use the [SpeedyIndex index checking tool](https://en.speedyindex.com/google-index-checker/) to verify which URLs are in Google’s index.

## Troubleshooting

- Confirm the connector is toggled on inside the chat from the **+** → **Connectors** menu.
- Re-check the API key in your SpeedyIndex account. If you changed it, all existing sign-ins are revoked.
- In Claude Code, run `/mcp`, select the server, and re-run **Authenticate**.
- Remove the connector and add it again if the settings path changed.
- On Team and Enterprise plans, ask the Owner to add the connector in Organization settings → Connectors.
- For custom connectors, the server must be reachable from the public internet on HTTPS port 443. If you change a connector definition, remove and re-add it.
- In VS Code, run `MCP: List Servers`. In Cursor, expand the tool call after asking for the balance.

## FAQ

**Is adding a custom connector safe?**
A custom connector is not verified by Anthropic. Claude can only access what you permitted the server to access during sign-in. Connect only servers you trust, and review the tools before selecting **Allow always**.

**Can I use custom MCP connectors on the free Claude plan?**
Yes. The Free plan allows one custom connector. Team and Enterprise plans are configured by an Owner and then connected individually by each member.

**Why does Claude ask for confirmation before some tools?**
Claude asks for approval before tool calls. SpeedyIndex’s 7 account-changing tools, including the 5 that spend tokens, ask unless the tool is set to **Allow always**. Read-only tools do not change your account, but you should review any tool before allowing it.

**Do I need to edit claude_desktop_config.json to add SpeedyIndex?**
No. The hosted SpeedyIndex server is added as a custom connector in the Claude interface. The config file is only for local stdio servers, such as the local SpeedyIndex npm package or a local DataForSEO server.

## Sources

- Anthropic support for connectors: https://support.claude.com

Claude is a trademark of Anthropic. SpeedyIndex is an independent connector.
