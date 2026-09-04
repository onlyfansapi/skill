# OnlyFans API Skills

Official [OnlyFansAPI.com](https://onlyfansapi.com) skills for downloading media,
understanding account performance, planning content, summarizing fans, and building
OnlyFans API integrations. Each skill works on its own with an Agent Skills-compatible
AI client; install the ones that match your work.

## Choose a skill

| Skill | Use it to | Example request |
| --- | --- | --- |
| [onlyfansapi-skill](skills/onlyfansapi-skill/SKILL.md) | Use the general OnlyFans API workflow, including authentication, analytics, messaging, and MCP | “Compare net earnings across my accounts.” |
| [onlyfans-video-downloader](skills/onlyfans-video-downloader/SKILL.md) | Save videos or archive media from connected accounts | “Download this vault video into my project folder.” |
| [onlyfans-api-docs](skills/onlyfans-api-docs/SKILL.md) | Find endpoints, understand responses, and troubleshoot requests | “Show me how to fetch new subscriber statistics.” |
| [onlyfans-account-health](skills/onlyfans-account-health/SKILL.md) | Diagnose revenue, acquisition, chat monetization, geography, and spending concentration | “Why did revenue dip this month?” |
| [onlyfans-chat-summary](skills/onlyfans-chat-summary/SKILL.md) | Prepare a fan handover or recap a conversation | “Summarize this fan for the next chatter.” |
| [onlyfans-content-planner](skills/onlyfans-content-planner/SKILL.md) | Turn message and media performance into a production plan | “What should I create next based on last month's messages?” |
| [onlyfans-ai-chatbot](skills/onlyfans-ai-chatbot/SKILL.md) | Build a webhook-driven chatbot in your existing stack | “Build a bot that handles typing and PPV purchases without duplicate replies.” |

## Install

List the available skills:

```bash
npx skills add onlyfansapi/skill --list
```

Install one skill, replacing the name with your choice from the table:

```bash
npx skills add onlyfansapi/skill --skill onlyfans-account-health
```

Or install the collection:

```bash
npx skills add onlyfansapi/skill --skill '*'
```

The CLI lets you choose the target agent and installation scope. Add `--global`
for a user-wide installation, or use the default project scope. Reload your agent
if needed for newly installed skills to appear.

## Existing users: one-time migration

The original `onlyfansapi-skill` keeps its name and general functionality. It now
lives at `skills/onlyfansapi-skill/SKILL.md`, together with its own references.

Existing installed copies continue working. Because the skills CLI records the
source file path, an install pointing at the former root `SKILL.md` may not migrate
through `skills update`. Reinstall once in the same scope and for the same agent:

```bash
# Existing project installation: run from that project
npx skills add onlyfansapi/skill --skill onlyfansapi-skill

# Existing global installation
npx skills add onlyfansapi/skill --skill onlyfansapi-skill --global
```

Choose the same agent(s) as before and replace the existing copy when prompted.
Review local customizations before replacement. Raw links to the old root file
must be updated to the new path. New specialists are separate choices; updating
the general skill does not automatically install them.

There is intentionally no root `SKILL.md`: it would prevent normal CLI discovery
of the nested collection. Each skill includes its own required files and has no
dependency on a separately installed sibling skill.

## Account access

Documentation help and code generation do not require credentials. For live REST
work, create an [OnlyFansAPI account](https://app.onlyfansapi.com), connect your
creator account(s), and configure an [API key](https://app.onlyfansapi.com/api-keys)
securely in the agent's environment:

```bash
export ONLYFANSAPI_API_KEY="your_api_key_here"
```

Keep the key out of source control and frontend code. The API MCP can authenticate
through OAuth instead. Live requests and services such as summary generation and
exports may consume credits; the skills follow your requested scope and budget.

## Optional MCP connections

When invoked in a compatible AI client, each skill checks for existing connections
and offers to connect a relevant missing MCP server. The offer is made once per
conversation across the skills, respects a decline, and does not block useful
documentation or REST work.

| Server | URL | Purpose |
| --- | --- | --- |
| Docs MCP | `https://docs.onlyfansapi.com/api/mcp` | Search public documentation; no authentication |
| API MCP | `https://app.onlyfansapi.com/mcp/onlyfans-mcp` | Work with connected accounts using OAuth or an API key |

Installing a skill does not automatically install MCP or authorize live account
changes. See the [setup guide](https://docs.onlyfansapi.com/introduction/guides/develop-with-ai-agents)
and the general skill's [MCP reference](skills/onlyfansapi-skill/references/mcp.md).

## Documentation

- [OnlyFans API reference](https://docs.onlyfansapi.com/api-reference)
- [Machine-readable OpenAPI schema](https://app.onlyfansapi.com/scribe-docs/openapi.yaml)
- [Build an OnlyFans AI chatbot](https://docs.onlyfansapi.com/onlyfans-ai/build-ai-chatbot-for-onlyfans)
- [OnlyFansAPI Console](https://app.onlyfansapi.com)
