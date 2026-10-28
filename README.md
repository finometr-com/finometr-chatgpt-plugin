# Finometr plugin for ChatGPT

The package for the ChatGPT plugin directory. It connects ChatGPT to the
Finometr MCP server at `https://finometr.com/mcp` — cash flow, accounts,
transactions, planned payments, budgets and a daily brief for SMEs — with
OAuth: ChatGPT registers through its client metadata document (CIMD), so no
client id or secret lives here.

- `plugin.json`: the manifest, the directory listing under
  `extensions.com.openai.interface`, and the review material (test cases
  against the reviewer demo account, the walkthrough video).
- `mcp.json`: the server.
- `skills/`: setup (the onboarding skill), the daily brief and statement
  import. They started as copies of the Claude plugin's skills
  (finometr-com/finometr-claude-plugin) without the Claude Code parts; a
  change to one is worth checking against the other.
- `assets/`: the logo and composer icon.

## Build the archive

Files go at the root of the ZIP:

```bash
zip -r ../finometr-chatgpt.zip plugin.json mcp.json skills assets
```

Upload it on platform.openai.com → Plugins (organization Finometr) as a new
version. To try it in ChatGPT against staging, change the server URL in
`mcp.json` to `https://finometr-staging.web.app/mcp` and `name`/`displayName`
in a copy that is never committed, then ChatGPT → Plugins → Add → Upload
plugin archive.
