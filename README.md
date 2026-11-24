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

- **94 tools**: 35 that need Read and 59 that need Write, plus three prompts
  (`daily_briefing`, `get_started`, `weekly_review`). The write tools work
  only when the user ticks Write at sign-in.
- **Periods**: `search_transactions` and `set_category` take a period of up
  to 366 days. A longer one is clipped to its last 366 days, and the answer's
  note says so. Spending by category covers one calendar month, or any
  period up to a year with `from` and `to`; budget targets cover one
  calendar month per call; the cash-flow forecast looks 30, 60 or 90 days
  ahead.
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
  details, project and unit can change. Taking a person out of a unit has no
  restore: coming back takes a new invitation from the Finometr app.
- **Projects, hours and people**: projects with planned and actual income,
  costs and hours, time entries, and the people of the user's units with
  their roles (owner, admin, editor, viewer). Bank connections are made in
  the Finometr app; `prepare_invite` prepares an invitation to a unit that
  the user sends with one button in the app, nothing is sent from the chat.
- **Duplicates**: a statement import is a dry run by default, and rows
  already imported count as duplicates. A manual or planned transaction
  identical to a live one is not added twice unless the user confirms a
  second one.

## What's new in 0.2.4

- Shared spending: a unit without accounts (a family, a project, a trip)
  shows what was spent, and `get_unit_contributions` tells who paid how
  much.
- Spending by category covers any period up to a year.
- `set_data` saves structured details on a transaction, and
  `search_transactions` finds them.
- `get_playbook` and `set_playbook` keep notes on how a unit's or
  project's records are kept.
- `move_transactions` moves up to 200 transactions in one call.
- The alert level can be set for each unit.

## What's new in 0.2.3

- `prepare_invite`: the assistant prepares an invitation to a unit and
  gives a link to a page of the Finometr app, where the user sends it with
  one button. Nothing is sent from the chat; the draft lasts 24 hours.
- Plainer words in the listing and the skills, as on the site and in the
  emails: alert level, before money runs short, accounts you keep by hand,
  financing interest (not a loan request). The skills tell the assistant
  which words to use with the user.
- Review test cases: inviting from the chat is now a positive case
  (`prepare_invite`); the negative case is sending without the app.

## What's new in 0.2.2

- E-mail confirmation with a code: while the account's e-mail is not
  confirmed, answers carry `context.emailNotice`; `request_email_code` sends
  a six-digit code to the account's own address and `confirm_email` takes
  it. The setup skill describes it.
- `get_account`, the account profile ChatGPT reads (`_meta`
  `openai/profile`): a stable id and the account's e-mail, so the app shows
  which Finometr account is connected.
- Clearer descriptions for `import_statement` (a manual account first, at
  most 500 rows a call, which its schema now says too), `get_link`
  (connecting a new bank or the banks already connected) and `create_unit`
  (what Personal is, and that units are limited).

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
