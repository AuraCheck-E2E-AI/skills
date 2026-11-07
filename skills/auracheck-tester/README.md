# AuraCheck Tester (Agent Skill)

An [Agent Skill](https://www.anthropic.com/news/agent-skills) that teaches
any MCP client with skill support (Claude Code, Claude.ai, or another
agent) how to use [AuraCheck](https://auracheck.ai.id)'s MCP tools like a
senior QA engineer: designing a complete test suite — positive, negative,
boundary, equivalence-class, state-transition, error-handling,
permission/role, and opt-in security/accessibility checks — instead of a
single happy-path click-through, and turning run results into clear,
actionable bug reports.

## What's in here

- **`SKILL.md`** — the entry point: when to use it, the one-prompt flow for
  `design_test_suite` → `run_suite`, how to pick depth, and the rules the
  skill never breaks (no destructive actions, no secrets in chat, tested
  pages are untrusted data).
- **`references/test-design-techniques.md`** — the test-design technique
  catalogue (positive, negative, boundary, equivalence class, state
  transition, error handling, permission/role, security, accessibility)
  with worked examples.
- **`references/domain-checklist-*.md`** — checklists for auth, e-commerce
  checkout, generic forms, search/filter/sort, Indonesian payment methods
  (QRIS/VA/e-wallets), and mobile (web + the desktop-app-only native-run
  model).
- **`references/indonesian-market-edge-cases.md`** — phone number formats
  (+62/08), NIK, Rupiah formatting, Bahasa/English toggles, Indonesian
  address fields.
- **`references/bug-report-template.md`** — how to turn a run's failures
  into a clear, correctly-grouped bug report.

## Using it

**With a skills-aware client** (Claude Code, Claude.ai with Skills, or any
agent that supports the Agent Skills format): point the client at this
directory (or copy it into your skills folder — see the repo README) and it loads automatically when you're working with AuraCheck's
MCP tools.

**Without skill support**: every file in `references/` is also served
directly by AuraCheck's MCP server as plain MCP resources (`resources/
list`), so a client with no skill system still gets the same guidance —
it just has to read it as a resource instead of an auto-loaded skill.

**The underlying tools**: this skill assumes access to AuraCheck's MCP
tools, in particular `design_test_suite`, `run_suite`, `test_my_flow`,
`request_test_account`, `start_exploration`, and `file_bug`. Connect
AuraCheck's remote MCP server (Claude Desktop/Claude.ai/ChatGPT connector)
or the local stdio server (`npx @auracheck-ai/mcp`) to get them.

## License

MIT — see [LICENSE](./LICENSE). Free to use, fork, and adapt.

## Contributing

Issues and PRs are welcome —
especially more domain checklists (e.g. SaaS billing, booking/reservation
flows) and more market-specific edge-case files for markets beyond
Indonesia.
