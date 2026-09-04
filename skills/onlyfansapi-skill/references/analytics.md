# Analytics and attribution

## Period, coverage, and source

Resolve the requested reporting period and timezone. If omitted, state a reasonable
default; do not silently turn “past 7 days” into eight calendar dates. Use the same
window, currency, and net/gross basis across accounts. Keep failed, inaccessible,
or still-loading results out of totals and disclose the missing coverage.

Read all required pages before ranking. A page sorted by subscribers does not identify
the highest-revenue link or best conversion rate across the whole account. Preserve
numeric precision while calculating; round currency only for display. Report zero
denominators as N/A, not a fabricated conversion rate. Aggregate rates from summed
numerators and denominators rather than averaging percentages.

[Data sources](https://docs.onlyfansapi.com/introduction/essentials/endpoint-data-sources)
distinguishes live passthrough, hybrid enrichment, and computed data. Link revenue
can be cached even when the surrounding response includes live OnlyFans data.

## Earnings and model comparisons

For each requested account, use
`GET /api/{account}/statistics/statements/earnings` with `start_date`, `end_date`,
and `type`. The supported types are `total`, `subscribes`, `tips`, `post`,
`messages`, and `stream`.

For `type=total`, the documented response contains:

| Meaning | Field |
| --- | --- |
| Net earnings | `data.total.total` |
| Gross earnings | `data.total.gross` |
| Change vs comparison period | `data.total.delta` |
| Earnings time series | `data.total.chartAmount` |

`data.total` is an object, not the earnings scalar. Inspect the returned category
when using a different `type`; do not reuse the total field path blindly.

For “best performing model,” use the metric the customer asked for. If unspecified,
state that the ranking uses net earnings over the selected period. Include per-account
rows and a total for agency reports; add other metrics only when they answer the question.

Source: [Get Earnings](https://docs.onlyfansapi.com/api-reference/statistics/get-earnings).

## Trial and tracking links

Both listings return links under `data.list`; follow pagination for the complete set.

Before calculating or ranking spender conversion, verify that the numerator and
denominator cover the same fan population, reporting period or acquisition cohort,
and spend-observation window. Use that same measurement contract across links.
The following formulas apply only after those scopes match:

| Link type | GET path | Name | Subscriber/claim denominator | Spender conversion |
| --- | --- | --- | --- | --- |
| Free trial | `/api/{account}/trial-links` | `trialLinkName` | `claimCounts` | `revenue.spendersCount / claimCounts` |
| Tracking | `/api/{account}/tracking-links` | `campaignName` | `subscribersCount` | `revenue.spendersCount / subscribersCount` |

Multiply a valid ratio by 100 to display a percentage. Include the counts so a
tiny sample does not look like strong evidence. If scopes cannot be reconciled,
report the raw counts with their sources and time bases, mark conversion N/A,
and do not rank conversion.

Revenue includes `total`, `spendersCount`, `calculatedAt`, and `isLoading`. Null or
loading values mean unavailable, not zero. `synchronous=true` on these listing
endpoints waits for revenue calculation; use it when the customer needs those
figures now, with appropriate timeouts. It is not an API-wide sync flag.

`claimCounts` and `subscribersCount` come from the live OnlyFans listing, while
`revenue` contains cached attribution data. Do not assume listing `startDate`/`endDate`
filters establish a shared period or cohort for those fields. `calculatedAt` describes
cache freshness, not the spending window; `synchronous=true` waits for calculation
without establishing a different measurement scope.

For dated link-performance reports, use the documented stats endpoints with
`date_start` and `date_end`:

- `GET /api/{account}/trial-links/{trial_link_id}/stats`
- `GET /api/{account}/tracking-links/{tracking_link_id}/stats`

Use `data.daily_metrics` for the selected dates: its `revenue`, `subs`, `clicks`,
and `spenders` are recorded daily increments. Sum daily revenue for a period ranking.
`data.summary` contains cumulative totals, not totals restricted to the requested
dates. `monthly_metrics` covers the full boundary months, so use the daily rows
for partial-month comparisons. Daily history starts only after the account is
connected and recording begins; disclose missing coverage rather than treating
unrecorded history as proven zero activity.

Daily `spenders` increments are not a guaranteed count of all distinct fans who
spent within the window or among that window's new subscribers. Preserve the
matching-population rule above before calculating conversion. For acquisition-cohort
analysis, verify the documented cohort endpoint's measurement contract rather than
treating a stats date filter as a cohort filter.

Rank by the listing's `revenue.total` only when cached attributed revenue matches
the requested comparison, and label its scope and freshness.

Attribution also accounts for overlapping links and subscription periods. Explain
the documented attribution rules when comparing against total account earnings;
do not add different attribution systems together as if they were disjoint revenue.

Sources: [trial links](https://docs.onlyfansapi.com/api-reference/free-trial-links),
[tracking links](https://docs.onlyfansapi.com/api-reference/tracking-links),
[list trial links](https://docs.onlyfansapi.com/api-reference/free-trial-links/list-free-trial-links),
[list tracking links](https://docs.onlyfansapi.com/api-reference/tracking-links/list-tracking-links),
[trial-link stats](https://docs.onlyfansapi.com/api-reference/free-trial-links/get-free-trial-link-stats),
[tracking-link stats](https://docs.onlyfansapi.com/api-reference/tracking-links/get-tracking-link-stats).

## Smart Links

Smart Links use team-level `/api/smart-links` routes and their own link IDs; do not
substitute trial/tracking-link IDs or insert an `acct_…` segment. Discover the link,
then choose its `stats`, `cohort-arps`, `fans`, `spenders`, `clicks`, or `conversions`
endpoint based on the question. These metrics are computed from OFAPI data.

Stats date windows and acquisition-cohort windows answer different questions.
Check the endpoint's date fields and net/gross basis before comparing results.
Click-to-subscriber conversion and subscriber-to-spender conversion also have
different denominators; label which one is being reported.

Source: [List Smart Links](https://docs.onlyfansapi.com/api-reference/smart-links/list-smart-links).
