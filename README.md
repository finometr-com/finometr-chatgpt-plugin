# Finometr plugin for ChatGPT

The package for the ChatGPT plugin directory. It connects ChatGPT to the
Finometr MCP server at `https://finometr.com/mcp` (cash flow, accounts,
transactions, planned payments, budgets and a daily brief for small
businesses, freelancers and households) with OAuth 2.1 and PKCE: ChatGPT
registers through its client metadata document (CIMD), so no client id or
secret lives here. On Finometr's sign-in page the user ticks Read, Write or
both; with Read alone, the write tools are refused. The connection to an
assistant is part of Pro and higher plans, and every new Finometr account
starts with 30 days of Pro, no credit card required.

- `plugin.json`: the manifest, the directory listing under
  `extensions.com.openai.interface`, and the review material (test cases
  against the reviewer demo account, the walkthrough video).
- `mcp.json`: the server URL and the OAuth client mode. It does not list
  tools: ChatGPT reads them from the server when a version is submitted.
- `skills/`: setup (the onboarding skill), the daily brief and statement
  import. They started as copies of the Claude plugin's skills
  (finometr-com/finometr-claude-plugin) without the Claude Code parts; a
  change to one is worth checking against the other. The two copies say the
  same things; only the sign-in steps differ.
- `assets/`: the logo and composer icon.

## What the Finometr tools cover

- **85 tools**: 32 that read and 53 that write, plus three prompts
  (`daily_briefing`, `get_started`, `weekly_review`). The write tools work
  only when the user ticks Write at sign-in.
- **Periods**: `search_transactions` and `set_category` take a period of up
  to 366 days. A longer one is clipped to its last 366 days, and the answer's
  note says so. Spending by category and budget targets cover one calendar
  month per call; the cash-flow forecast looks 30, 60 or 90 days ahead.
- **Search totals**: a search with a text, category, account or amount
  filter gives the net, money out and money in over every matching row of
  the period in its summary, not only the page; without a filter it sums
  the page.
- **Custom categories**: on Pro and higher (the plan of the unit's owner), a
  unit can have custom categories inside the standard ones, for spending a
  standard category lumps together. Finometr's AI sorts new transactions into
  them by name and description. `set_category` moves earlier rows, up to 200
  a call; with a dry run it only counts the rows a filter finds and shows a
  sample. A unit has up to 25 active custom categories on Pro and 50 on Team.
  Category names and descriptions go to Finometr's AI provider.
- **Removing and restoring**: removing is soft. Manual and planned
  transactions, manual accounts (with their rows, recurring payments and
  plans), empty projects, recurring payments, time entries and shared copies
  can be removed and brought back: `list_removed_transactions`,
  `list_removed_accounts` and `list_removed_projects` list what can come
  back, and a `restore_*` tool brings each back. Custom categories are
  archived, not deleted. Bank transactions are never removed, and their
  amounts and texts stay as the bank reported them; their category, note,
  project and unit can change. Taking a person out of a unit has no
  restore: coming back takes a new invitation from the Finometr app.
- **Projects, hours and people**: projects with planned and actual income,
  costs and hours, time entries, and the people of the user's units with
  their roles (owner, admin, editor, viewer). Bank connections and new
  invitations are made in the Finometr app.
- **Duplicates**: a statement import is a dry run by default, and rows
  already imported count as duplicates. A manual or planned transaction
  identical to a live one is not added twice unless the user confirms a
  second one.

## What's new in 0.2.1

- Custom categories inside the standard ones on Pro and higher
  (`create_category`, `update_category`, `archive_category`,
  `restore_category`), and the skills describe moving earlier rows with
  `set_category`.
- The skills describe the 366-day period limit, the search totals over
  every matching row, the lists of removed rows
  and accounts with their restore tools, and the Read and Write choice at
  sign-in.
- The tool descriptions ChatGPT freezes at submission are the server's
  current ones.

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
