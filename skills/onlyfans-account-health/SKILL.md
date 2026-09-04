---
name: onlyfans-account-health
description: >-
  Diagnose OnlyFans account performance using OnlyFansAPI revenue, subscriber
  growth, chatting ratios, traffic geography, and top-fan spending. Use for account
  health reviews, revenue dips, growth analysis, and prioritized business actions.
metadata:
  author: OnlyFansAPI.com
  version: "1.0"
---

# OnlyFans Account Health

Explain what changed, which evidence supports the likely drivers, and what the
customer should do next. Scale the analysis to the question; a revenue dip does
not require collecting every available metric.

## When invoked

Inspect the current AI client's remote MCP support and existing relevant servers.
If supported and a relevant server is missing, ask once per conversation:
**“Would you like me to connect the OnlyFansAPI MCP as well?”** Explain the options:

- Docs MCP: public documentation search at `https://docs.onlyfansapi.com/api/mcp`.
- API MCP: real connected-account access at `https://app.onlyfansapi.com/mcp/onlyfans-mcp`.

Offer the relevant server. Follow the current
[setup guide](https://docs.onlyfansapi.com/introduction/guides/develop-with-ai-agents)
for the client and authentication. Preserve earlier offers, consent or decline from
any OnlyFans skill; invocation alone does not authorize installation. If support is unknown, verify it before
offering setup. Continue useful documentation or REST work while awaiting an answer,
or when MCP is unsupported or declined. Reconnect an unavailable configured server
instead of duplicating it, and verify tool discovery before claiming setup is complete.

## Establish scope and retrieve evidence

Use the selected creators and requested dates. Use the requested timezone, or UTC
when none is specified, and disclose it. For a general review with no dates, use
the last 30 complete calendar days in that timezone versus the preceding 30. For
emerging changes, also compare trailing 7/3/1-day daily averages and chatting ratios
with the 30-day baseline. Construct all windows and date comparisons in the selected
timezone. These overlapping windows are trend signals, not independent before/after
periods.

Read [metric definitions and sources](references/metrics.md) for the metrics needed.
Verify current inputs and response fields through the Docs MCP or
[API reference](https://docs.onlyfansapi.com/api-reference) before requesting data.

For REST, use `https://app.onlyfansapi.com/api`, Bearer authentication, and the
customer's configured `ONLYFANSAPI_API_KEY` or secret store. Never print or commit
secrets. Resolve `{account}` to an OFAPI `acct_…` ID through `GET /api/accounts`
when necessary; numeric fan IDs are different. Account analysis authorizes the
necessary reads of the selected accounts. It does not authorize messages, profile
changes, or other live mutations. Without live access, analyze supplied exports or
prepare the exact requests and state what remains unverified.

Collect the relevant earnings categories, new subscriptions, reach, geography and
top fans. Keep total account earnings, new-subscription earnings and message-plus-tip
earnings separately labeled. Retain source, currency, net/gross basis, dates and
coverage. Do not turn unavailable, incomplete or loading data into zero. Match
account coverage across comparisons and identify omissions.

Follow documented pagination for complete aggregates, retaining filters and
deduplicating IDs. Validate next-page URLs against the API origin before attaching
credentials. Pace calls using response rate-limit metadata; honor `Retry-After`
and bound retries. Detailed subscriber breakdowns can need smaller date windows.
Do not fetch full transaction history when a bounded report answers the question.

## Diagnose the change

Compute ratios from summed numerators and denominators, with N/A for zero or unknown
denominators. Preserve precision until display. Use the customer's known free/paid
strategy; a paid subscription price can coexist with a free-trial strategy. State
any analytical classification override without changing account settings. If the
classification is uncertain, label the assumption or show both formulas. Analyze
free and paid profiles separately before combining an agency report.

Use these as investigation directions, not causal conclusions:

| Observed pattern | Next evidence to inspect |
| --- | --- |
| Revenue and new subscriptions fall; chatting ratio holds | Reach, acquisition sources and profile conversion |
| Reach holds; new subscriptions fall | Offer, price, trial mix and visitor-country mix |
| New subscriptions hold; message/tip revenue and ratio fall | PPV sales, messaging activity and changes among leading spenders |
| Chatting ratio rises while new subscriptions fall | Whether a shrinking denominator explains the apparent improvement |
| Revenue grows while spending concentrates in a few fans | Whether growth persists outside those fans |

Same-period chat revenue includes existing fans: chatting ratio is neither cohort
LTV nor ROAS. Country data describes profile views, not payer locations. Different
currencies, account strategies, freshness or partial periods can explain apparent
differences. Separate observed facts, plausible explanations and missing evidence.

## Deliver the review

Lead with the performance verdict and its strongest evidence. Give a compact
comparison with exact periods, amounts and percentage changes; include chatting
ratio inputs so the result is auditable. Show leading countries and fans when they
help explain the change, with their measurement basis. End with prioritized actions
and the specific signal that would confirm each one. Disclose coverage gaps and
avoid invented benchmark scores or claims of proven causation.
