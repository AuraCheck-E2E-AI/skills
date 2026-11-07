# AuraCheck Skills

Open-source [Agent Skills](https://www.anthropic.com/news/agent-skills) from
[AuraCheck](https://www.auracheck.ai.id) — real-browser AI end-to-end testing.

| Skill | What it does |
|---|---|
| [`auracheck-tester`](skills/auracheck-tester) | Makes your AI assistant test a website like a senior QA engineer: a complete suite (positive, negative, boundary, state, error, permission, opt-in security/a11y) instead of one happy path, run on AuraCheck, turned into a clear bug report. Includes domain checklists (auth, checkout, forms, search, Indonesian payments, mobile) and Indonesian-market edge cases. |

## Install

**Claude Code** (personal, all projects):

```bash
git clone https://github.com/AuraCheck-E2E-AI/skills.git
cp -R skills/skills/auracheck-tester ~/.claude/skills/
```

Or per project: copy it into `.claude/skills/` in your repo.

**Claude.ai / Claude Desktop**: zip the `skills/auracheck-tester` folder and
upload it under Settings → Capabilities → Skills.

**Other agents** that support the Agent Skills format: point them at
`skills/auracheck-tester/`.

The skill drives AuraCheck's MCP tools — connect the AuraCheck connector in
Claude/ChatGPT, or run the local server with `npx @auracheck-ai/mcp`.
Clients without skill support still get the same guidance, served as MCP
resources by the AuraCheck server.

## License

MIT — see [LICENSE](LICENSE). Contributions welcome: more domain checklists
and market-specific edge-case files especially.
