# OnlyFans API Skills

Analyze account performance, download videos, summarize fans, plan your next content,
and build OnlyFans API integrations with your AI agent.

Built by [OnlyFansAPI.com](https://onlyfansapi.com). Seven practical skills for
Claude Code, Codex, and other agents that support Agent Skills.

## Get started

**Recommended: install through [skills.sh](https://skills.sh).** Run this from your project:

```bash
npx skills@latest add onlyfansapi/skill
```

Choose the skills you want and the agent you use. Add `--global` for a user-wide
installation. Reload your agent if needed for newly installed skills to appear.

> Already using `onlyfansapi-skill`? Your installed copy still works. Read the
> [one-time migration instructions](#existing-users-one-time-migration) before updating.

### Connect your account when needed

You can ask documentation questions and build integration code without credentials.
To work with live data, create an [OnlyFansAPI account](https://app.onlyfansapi.com),
connect your creator account, and configure an [API key](https://app.onlyfansapi.com/api-keys)
in your agent's environment or secret store:

```bash
export ONLYFANSAPI_API_KEY="your_api_key_here"
```

Keep the key out of source control and frontend code. You can also connect through
the [optional API MCP](#optional-mcp-connections) using OAuth. API calls and services
such as summary generation and exports may consume credits; the skills follow your
requested scope and budget.

### Try a useful request

With the account-health skill installed, ask:

> Review my account over the last 30 days. Why did revenue change, and what should I investigate next?

The agent compares earnings, subscriber growth, and chatting ratios, then explains
the evidence and next actions. Without account access, try the API docs skill:

> Show me how to fetch new subscriber statistics with the OnlyFans API.

## Choose a skill

### Analyze and plan

| Skill | Ask your agent | What you get |
| --- | --- | --- |
| [onlyfans-account-health](skills/onlyfans-account-health/SKILL.md) | “Why did revenue dip this month?” | A period comparison, acquisition and chatting ratios, relevant country/top-fan data, and prioritized actions. |
| [onlyfans-chat-summary](skills/onlyfans-chat-summary/SKILL.md) | “Summarize this fan for the next chatter.” | A fan handover or conversation recap, with known facts and open questions. |
| [onlyfans-content-planner](skills/onlyfans-content-planner/SKILL.md) | “What should I create next based on last month's message sales?” | Ranked content ideas and a production plan grounded in message and media performance. |

### Download media

| Skill | Ask your agent | What you get |
| --- | --- | --- |
| [onlyfans-video-downloader](skills/onlyfans-video-downloader/SKILL.md) | “Download this vault video into my project folder.” | Verified local files or a vault archive from your connected account, with completion status. |

### Build integrations

| Skill | Ask your agent | What you get |
| --- | --- | --- |
| [onlyfansapi-skill](skills/onlyfansapi-skill/SKILL.md) | “Compare net earnings across my accounts.” | General API workflows covering authentication, analytics, messaging, media, and MCP. |
| [onlyfans-api-docs](skills/onlyfans-api-docs/SKILL.md) | “Show me how to fetch new subscriber statistics.” | Relevant endpoints, verified request/response details, and usable examples. |
| [onlyfans-ai-chatbot](skills/onlyfans-ai-chatbot/SKILL.md) | “Build a bot that handles typing and PPV purchases without duplicate replies.” | A webhook-driven chatbot integration in your existing stack, with idempotency and focused checks. |

Every skill works independently. To select one directly, list the collection, or install all seven:

```bash
npx skills@latest add onlyfansapi/skill --skill onlyfans-account-health
npx skills@latest add onlyfansapi/skill --list
npx skills@latest add onlyfansapi/skill --skill '*'
```

## Native plugins: an alternative

Prefer installing the whole collection as a plugin? Both plugins contain the same
seven skills. **Use one installation route per agent** to avoid duplicate copies.
If switching, preserve local customizations and remove the standalone copies after
verifying that the plugin works. Installing a plugin does not migrate them automatically.

### Claude Code

```bash
claude plugin marketplace add onlyfansapi/skill
claude plugin install onlyfansapi@onlyfansapi --scope user
```

Run `/reload-plugins` in Claude Code when prompted, or start a new session. Invoke a
skill explicitly with its plugin namespace, such as
`/onlyfansapi:onlyfans-account-health`, or describe your task normally.

### Codex

```bash
codex plugin marketplace add onlyfansapi/skill
codex plugin add onlyfansapi@onlyfansapi
```

Start a new Codex session after installation so its bundled skills become available.
Use a Codex surface with plugin support; other clients can use the skills.sh route.

These commands install from this repository's marketplace. They do not require a
listing in an official plugin directory. Neither plugin bundles MCP connections or
asks for an API key during installation.

## Optional MCP connections

When you invoke a skill in a compatible client, it offers to connect a relevant
missing MCP server. It asks once per conversation across the skills, respects a
decline, and continues useful documentation or REST work while setup is pending.

| Server | URL | Purpose |
| --- | --- | --- |
| Docs MCP | `https://docs.onlyfansapi.com/api/mcp` | Search public documentation; no authentication. |
| API MCP | `https://app.onlyfansapi.com/mcp/onlyfans-mcp` | Work with connected accounts using OAuth or an API key. |

Installing a skill or plugin does not connect MCP or authorize live account changes.
See the [setup guide](https://docs.onlyfansapi.com/introduction/guides/develop-with-ai-agents)
and [MCP reference](skills/onlyfansapi-skill/references/mcp.md).

## Updates

For skills installed through skills.sh, complete the migration below if needed, then run:

```bash
npx skills update
```

For the Claude Code plugin:

```bash
claude plugin marketplace update onlyfansapi
claude plugin update onlyfansapi@onlyfansapi
```

Run `/reload-plugins` when prompted or start a new session. For the Codex plugin:

```bash
codex plugin marketplace upgrade onlyfansapi
codex plugin add onlyfansapi@onlyfansapi
```

Start a new Codex session after reinstalling. Update settings vary by client; do
not assume a plugin updates automatically.

## Existing users: one-time migration

The original `onlyfansapi-skill` keeps its name and general functionality. Its
source moved from the root `SKILL.md` to `skills/onlyfansapi-skill/SKILL.md`, with
its references alongside it. Existing installed copies keep working.

The skills CLI records that source path. An update may say the original skill was
**deleted upstream** and offer to remove the local copy. Decline removal and reinstall
the general skill once in the same scope and for the same agent:

```bash
# Existing project installation: run from that project
npx skills@latest add onlyfansapi/skill --skill onlyfansapi-skill

# Existing global installation
npx skills@latest add onlyfansapi/skill --skill onlyfansapi-skill --global
```

Choose the same agent(s), review local customizations, and replace the existing
copy when prompted. Update any raw links to the old root file. The six specialists
are optional and are not installed automatically when you update the general skill.

Native plugin installation is a separate choice; it does not repair an old
standalone installation's update path.

## Documentation

- [OnlyFans API reference](https://docs.onlyfansapi.com/api-reference)
- [Machine-readable OpenAPI schema](https://app.onlyfansapi.com/scribe-docs/openapi.yaml)
- [Build an OnlyFans AI chatbot](https://docs.onlyfansapi.com/onlyfans-ai/build-ai-chatbot-for-onlyfans)
- [OnlyFansAPI Console](https://app.onlyfansapi.com)

## Maintaining the collection

The standalone skills and both plugins share the same `skills/` directory. Keep the
two plugin manifest versions synchronized and bump them when publishing changes to
the bundled skills or plugin metadata. There is no root `SKILL.md`, so the skills
CLI can discover all seven skills normally.
