# OnlyFansAPI MCP

Read this when offering setup, connecting a client, or choosing between MCP and REST.

## Two servers

| Server | URL | Purpose | Authentication |
| --- | --- | --- | --- |
| Docs MCP | `https://docs.onlyfansapi.com/api/mcp` | Search and read public documentation | None |
| API MCP | `https://app.onlyfansapi.com/mcp/onlyfans-mcp` | Operate connected accounts through API tools | OAuth or API-key bearer token |

Both are remote Streamable HTTP MCP servers. Connecting the Docs MCP does not grant
API access. For an OnlyFans-only API connection, `?platforms=onlyfans` limits the
tool catalog; preserve an existing multi-platform configuration unless asked to change it.
This filter is not an account permission boundary.

Sources: [Two MCP servers](https://docs.onlyfansapi.com/introduction/guides/develop-with-ai-agents#two-mcp-servers-different-jobs)
and [API MCP overview](https://docs.onlyfansapi.com/onlyfans-ai/mcp).

## Offer and connect

1. Discover existing tools/configuration using the current client's supported mechanism.
   Distinguish **connected**, **configured but unavailable**, **not configured**, and
   **client unsupported**. An unavailable server may need reconnection, not a duplicate install.
2. When the client supports MCP and the relevant server is missing, ask the user whether
   to add it, as instructed in `SKILL.md`. Explain which server(s) will be connected.
   Do not repeatedly prompt after a decline or make setup a prerequisite for the task.
3. After approval, use the client's native connector flow or documented configuration
   mechanism. Preserve unrelated servers. Prefer native remote HTTP support; use a
   stdio bridge only if the client's current documentation requires it.
4. Docs MCP needs no key. For API MCP, use the supported OAuth flow for interactive
   clients or a bearer API key from the client's secret store for backend integrations.
   OAuth does not require copying an API key into chat. Let the user complete sign-in
   and consent; setup approval does not authorize account mutations. API-key restrictions
   apply when using that key; OAuth uses the authorized team and role, not a separate
   key's restrictions. Check the granted access instead of assuming the same scope.
5. Verify tool discovery after connecting. For Docs MCP, read a relevant page; for API
   MCP, use an authorized account-list lookup when live verification is appropriate.
   If a restart or consent is pending, report that setup is pending rather than connected.

Client support, plan availability, menu names, and configuration syntax can change.
Check the current client documentation rather than guessing a config location or
copying another client's commands. Start with the applicable official OFAPI guide:

- [Claude](https://docs.onlyfansapi.com/onlyfans-ai/mcp/claude)
- [ChatGPT](https://docs.onlyfansapi.com/onlyfans-ai/mcp/chatgpt)
- [Manus](https://docs.onlyfansapi.com/onlyfans-ai/mcp/manus)
- [Embed in an app](https://docs.onlyfansapi.com/onlyfans-ai/mcp/embed)
- [Docs MCP and other clients](https://docs.onlyfansapi.com/introduction/guides/develop-with-ai-agents)

For Codex, Cursor, VS Code, or another client, use its own current MCP setup guide
with the server URL above; do not assume every AI environment can install connectors.

## Use the tools

Docs MCP exposes `search_docs(query, limit?)`, `get_doc_page(path)`, and
`get_overview()`. Search for the relevant topic, then read the specific page.
Use the full overview only when broad discovery is needed.

Discover API tool names and input schemas from the connected server. OnlyFans tools
are unprefixed; Fansly tools start with `fansly_`. Keep the user's OnlyFans task on
the OnlyFans tools and `acct_…` accounts. Do not hardcode a tool count or assume a tool
is available merely because an endpoint exists in the docs.

The current API MCP bridge exposes endpoint path/query/body arguments, but does not
expose arbitrary REST headers such as `Idempotency-Key`. If reliable retries for
direct message sending require that header, use authenticated REST unless the
discovered tool explicitly supports it. Never invent an `idempotency_key` argument.
Binary upload fields are also excluded: use `file_url` for already hosted media or
REST for a local file. Do not upload private files elsewhere just to fit an MCP tool.

JSON tool results wrap the REST payload as `{status, response}`; errors include
`{status, error}`. Inspect this embedded status/body, including asynchronous `202`
responses, rather than treating successful MCP transport as a completed operation.

MCP is subject to authentication, permissions, credits, and rate limits. Use errors
and returned metadata to determine the current limits; do not create extra keys to
work around a limit. Tool annotations do not grant permission or prove retry safety.
Apply the same authorization and uncertain-outcome rules as REST.
