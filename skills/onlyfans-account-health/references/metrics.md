# Account-health metrics

Read the sections needed for the customer's question. Paths below are relative to
`https://app.onlyfansapi.com`; use the endpoint's documented date format and query
parameter casing. A successful response alone does not prove complete coverage.

## Earnings and chatting ratios

For [Get Earnings](https://docs.onlyfansapi.com/api-reference/statistics/get-earnings),
call `GET /api/{account}/statistics/statements/earnings` with `start_date`,
`end_date`, and the relevant `type`: `total`, `subscribes`, `tips`, `post`,
`messages`, or `stream`. With `type=total`, `data.total.total` is net earnings,
`data.total.gross` is gross, and `data.total.chartAmount` is the amount series.
For a category, inspect its returned bucket rather than assuming that field path.

[Get Subscriber Statistics](https://docs.onlyfansapi.com/api-reference/statistics/get-subscriber-statistics)
uses `GET /api/{account}/subscribers/statistics` with `start_date`, `end_date`,
and `type=new` for new subscribers (`renew` and `total` answer other questions).
`data.subscribes` contains counts and `data.earnings` contains gross subscription
amounts as `{date, count}` series. For the net chatting-ratio denominator, convert
this gross subscription revenue with `0.80`. Do not apply that conversion to
earnings already reported as net; verify the basis if the contract changes.

For a single account and common period, define:

| Symbol | Amount |
| --- | --- |
| C | Sum of net message revenue plus net tips |
| N | Sum of new subscribers |
| S | Net earnings from new subscriptions, gross new-subscription earnings × 0.80 |

- **Free strategy chatting ratio:** `C / N`, expressed as currency per new subscriber.
- **Paid strategy chatting ratio:** `C / S`, expressed as a multiplier.
- Return N/A if the denominator is zero, missing or not comparable.
- Use ratios of period sums; never average daily ratios or combine the two units.
- Explain that C includes earnings from established fans, while N and S concern
  new subscriptions. This diagnostic does not measure the acquired cohort's LTV.

For trend views, use closed UTC windows of 30/15/7/3/1 days and display the
numerator and denominator alongside the ratio. Compare traffic or revenue totals
across unequal window lengths only after normalizing per day. Percent change is
`(current - baseline) / baseline × 100` for a positive baseline; report the
absolute change when the baseline is zero. Distinguish a genuine zero from missing
dates. If using stored hourly data, require all expected finalized hours for an
exact daily total; never add a daily aggregate to the hours it already represents.

## Acquisition and visitor geography

[Get Subscriber Metrics](https://docs.onlyfansapi.com/api-reference/statistics/get-subscriber-metrics):
`GET /api/{account}/statistics/subscriber-metrics` with `start_date`, `end_date`,
`detailed=true`, `detailed_type=new` supplies the free/paid split. Read
`data.new_subscriptions` and `data.detailed.{free_subscriptions,paid_subscriptions,unknown_subscriptions}`.
Unknown subscriptions can represent deleted fan accounts; do not reclassify them
or force free plus paid to equal the total. Reconcile disagreements with subscriber
statistics instead of silently replacing a ratio's denominator. Large detailed
windows can time out; fetch smaller non-overlapping windows when needed.

[Get Profile Visitors](https://docs.onlyfansapi.com/api-reference/statistics/get-profile-visitors):
`GET /api/{account}/statistics/reach/profile-visitors` accepts `start_date`,
`end_date`, `type=total|users|guests`, `filter=chart|topCountries`, and `limit`.
Use the chart for reach trends and `filter=topCountries` for geography. Check
`data.isAvailable`, `hasStats` and any `hasMore` before describing coverage.

Country share is `row.viewsCount.total / topCountries.totals.total × 100` for
the same period. Keep “Others” in the denominator even when listing only named
countries. Share change is a **percentage-point** difference. Profile-view
geography does not reveal individual fans' locations or revenue by country.

An aggregate new-subscriber/profile-visitor ratio can show funnel movement, but
does not establish a matched visitor cohort or campaign attribution. If traffic
source attribution is requested, use the relevant tracking or Smart Link contracts;
do not infer attributed sales from country mix or add overlapping link revenues.

## Top fans and revenue concentration

[List Top Fans](https://docs.onlyfansapi.com/api-reference/fans/list-top-fans):
`GET /api/{account}/fans/top` with **both** `start_date` and `end_date`, plus
`by=total` (other options: `subscribes`, `tips`, `messages`, `post`, `streams`).
Always specify dates for a period report. Inspect the actual collection: the
documented example uses `data.users`. Do not assume pagination parameters exist
or that a returned top list covers every spender.

The example includes `subscribedOnData.totalSumm` and category `*Summ` values.
Their presence does not establish that every nested field follows the ranking's
date filter or uses the same net/gross basis as statements. Verify that before
calculating shares; otherwise use the list as a ranking and state that amounts
need verification.

For precise period concentration when those amounts cannot be established, use
[List Transactions](https://docs.onlyfansapi.com/api-reference/transactions/list-transactions):
`GET /api/{account}/transactions` with `startDate` and documented marker pagination.
The endpoint has no documented `end_date`; filter `createdAt` to the report's end
locally. Complete the required pages, deduplicate transaction IDs, and sum `net`
by `user.id` within the exact period and currency. Explain any status, refund or
unidentified-user treatment and reconcile the period total with statement earnings.
Do not drop unidentified transactions from the denominator.

Report top-1/top-5/top-10 shares as `net earnings from those fans / comparable
total net earnings × 100`. The denominator must cover the whole requested period
and account, not just the returned top fans. If full reconciliation is unavailable,
label the share's actual coverage or omit it. Compare leading fans across periods
to separate broad growth from a few unusually large purchases.
