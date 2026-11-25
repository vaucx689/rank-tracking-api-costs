# Rank Tracking API: What One Keyword Really Costs, Whether You Need Built-In History, and When a SERP Scraper Wins (ScraperAPI Plans Compared)

A rank tracking API is billed per keyword, per check, per engine, and the multipliers stack faster than most teams expect. On ScraperAPI, one Google search request costs 25 credits, so the 1,000 free credits you get at signup cover roughly 40 keyword lookups. Run 500 keywords once a day for a month and you are at 375,000 credits before a second device or a second country enters the picture.

That arithmetic decides most of the build-versus-buy question on its own. What follows is the ScraperAPI plan structure, what its Google Search endpoint actually hands back, the limits that sit in the documentation rather than the landing page, and the cases where a dedicated rank tracker still beats a raw SERP scraper.

> **The short version.** If you already own the database and the dashboard and only need structured Google SERP JSON with a `position` field, ScraperAPI's Google Search endpoint is the leanest way to get it. If you want campaigns, stored history and client-ready reports without writing the plumbing, a dedicated rank tracker will save you the engineering. ScraperAPI's own pricing page carries the current numbers for every tier, and credits are consumed per request rather than per keyword.
>
> 👉 [See ScraperAPI's current plans and credit allowances](https://www.scraperapi.com/pricing?fp_ref=coupons)

## ScraperAPI plans at a glance

ScraperAPI publishes a free tier plus seven paid tiers. The table below sticks to what the pricing page and help documentation state directly: the free allowance, the overage mechanics attached to each tier, and where each one tends to land. Credit allowances and prices scale by tier and by monthly versus annual billing, so check the live figures before you commit.

| Plan | Credits and overage behaviour | Price | Typical fit | Get started |
| --- | --- | --- | --- | --- |
| Free | 1,000 API credits, max 5 concurrent connections; scraping pauses when credits hit zero | $0, no card needed to sign up | Testing the Google Search endpoint end to end (about 40 Google lookups) | [Free plan details](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Hobby | Tier allowance; auto-upgrade to the next plan when credits run out | Listed on the official pricing page, monthly or annual | One site and a short keyword list you refresh on a schedule | [Hobby pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Startup | Tier allowance; auto-upgrade available | Same | A freelancer or a two-person SEO team running client reports | [Startup pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Business | Tier allowance; auto-upgrade available | Same | An agency with a steady monthly reporting load | [Business pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Scaling | Tier allowance; auto-upgrade **and** pay-as-you-go up to a monthly cap | Same | Keyword counts that grow month over month without a plan change | [Scaling pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Professional | Tier allowance; auto-upgrade **and** pay-as-you-go | Same | Multi-client dashboards where a mid-month spike cannot stall collection | [Professional pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Advanced | Pay-as-you-go only; no auto-upgrade | Same | High, predictable volume where you set the spend cap yourself | [Advanced pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Enterprise | Pay-as-you-go with custom terms | Same | Volume plus contractual terms | [Enterprise pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |

Two things in that table matter more than the tier names. The free plan's 5 concurrent connections and 1,000 credits are enough to prove your integration works, not enough to validate a real keyword list. And pay-as-you-go only appears from Scaling upward, so on Hobby, Startup and Business a runaway job simply stops until credits renew unless you switch auto-upgrade on.

## Run the meter before you pick a tier

ScraperAPI's credit table is where the real cost of rank tracking lives. A standard page request costs 1 credit. Google and Bing requests, including their subdomains, cost 25. Amazon costs 5, LinkedIn 30. Sites sitting behind Cloudflare, DataDome or PerimeterX add 10 credits per request when ScraperAPI bypasses the block.

Every rank check is a Google request, so 25 credits is your base unit. Put numbers on it:

- 100 keywords, daily, Google desktop only, 30 days: 3,000 requests = 75,000 credits. That already exceeds the free allowance by 75×.
- 500 keywords, daily, desktop and mobile: 30,000 requests = 750,000 credits per month.
- Add a second country or a city-level location per keyword and you double it again.

This is the same shape of meter Similarweb uses for its rank tracking API, where 1 unit equals one keyword tracked once on one search engine. The unit definition is identical in spirit; the unit price is not, and neither is what you get in return. When a vendor quotes you "per keyword," ask what one keyword-check consumes, then multiply. The free tier being worth about 40 Google searches is the most useful number on the whole site, because it tells you immediately whether your list fits.

👉 [Check what your keyword volume would cost on the current plans](https://www.scraperapi.com/pricing?fp_ref=coupons)

## What the Google Search endpoint gives you, and what it doesn't

ScraperAPI exposes Google Search as a Structured Data Endpoint. Each entry in `organic_results` carries `position`, `title`, `link`, `snippet` and `displayed_link`. The response also includes `related_questions`, `related_searches` and `pagination`.

The parameter set is what makes it usable for rank tracking across markets: `country_code` targets a country's Google, `domain` switches the Google domain (co.uk, com.au and so on), `uule` encodes a specific city for local results, `num` sets how many organic results come back per page, `output_format` returns `json` by default or `markdown` for LLM pipelines, and `time_period` filters by recency. For volume work you switch the request method to Async and paste your structured endpoint URLs one per line.

What you do not get is a campaign object or a stored history. There is no "project" to create and no series to query later. ScraperAPI's own tutorial is explicit about the workaround: save each response as a dated file, then load any two dates and diff the `organic_results` array by `position`.

That trade cuts both ways. You own the retention, so you can keep three years of rank history instead of a vendor-defined window. You also own the storage, the diffing logic and the scheduling. For a team that already runs a data warehouse this is a weekend. For a team that wants to log in and see a chart, it is a project.

## Four limits that don't show up on the landing page

**Depth costs requests, not one request.** The official Python walkthrough shows pagination through `start=0`, `start=10`, `start=20` and recommends setting `num` to 100 to pull a full top 100 in one payload. If your integration paginates instead, each page is a separate Google request at 25 credits. Pulling 100 positions ten at a time turns one keyword check into 250 credits.

**Not every keyword has 100 results.** A long-tail query may return a handful of organic results and nothing else. Your code needs a defined answer for "no position found," because a null position and a position of 90 mean very different things in a client report.

**Cost per request varies by target.** The 25-credit figure applies to Google and Bing. Bot-protected targets add 10. A rank tracker that also scrapes competitor landing pages will mix credit costs in ways a flat per-keyword budget won't predict. The Domain Cost Estimator in the dashboard prices any specific URL, and `max_cost` caps what a single scrape is allowed to spend.

**Concurrency on the free tier is five.** A backfill of 15,000 requests through five parallel connections is a queue, not an afternoon. Backfills are the usual reason people move up a tier sooner than they planned.

## Ranking third can still mean no traffic

Position alone is a weak metric. Mangools states the problem plainly on its own API page: a keyword can rank third and still lose visibility to ads, featured snippets, local packs, shopping results, videos or People Also Ask boxes. Anyone who has reported a "top 3 ranking" to a client that saw flat traffic knows this.

ScraperAPI's response helps here more than a bare position number does, because `related_questions` and `related_searches` tell you what else occupies the page. Building a visibility score that weights position against SERP composition is on you. If that scoring is the product you actually want, a dedicated rank tracker with built-in share-of-voice and visibility metrics is the shorter path.

## How the options compare

All four approaches below produce keyword positions. They differ in who runs the machinery and how long the data sticks around.

| Approach | Engine coverage | History retained | Access model | Prerequisite |
| --- | --- | --- | --- | --- |
| ScraperAPI Google Search endpoint | Google Search structured endpoint; Bing priced at the same 25 credits per the credit table | None built in; you store snapshots | API key, single request or Async batch | None beyond an account |
| Similarweb (rank tracking API, Rank Ranger technology) | Top 100 data documented for Google; Yahoo, Bing, Yandex and Baidu described as coming | 60 days for Top 100; 24 hours for the on-demand query method | Dedicated key requested through an account manager; existing Similarweb API keys don't work | A campaign must exist before any endpoint call |
| Mangools SERPWatcher | Location-based tracking down to country, state and city; desktop and mobile | Not published | `X-Access-Token` header against REST endpoints | A tracking project created via API first |
| Self-hosted wrapper over SerpApi | Google to position 100, Bing to 50, DuckDuckGo to 30; anything beyond is recorded as 0 | Whatever you build | Your own Node/Express service plus a SerpApi key | You host, deploy and maintain it |

Two details in that table are worth dwelling on. Similarweb's 60-day window and 24-hour window belong to two different products, a scheduled Top 100 feed and a real-time on-demand query, so a request for six months of rank curves cannot be served by either without your own storage. And the self-hosted route has a hidden engineering cost: Google returns ten results per page, so reaching position 100 requires pagination, which is precisely the work ScraperAPI's `num` parameter removes.

## Which one fits your situation

**You have a warehouse and a BI tool.** ScraperAPI's Google Search endpoint, Async requests for the batch, `country_code` and `uule` for location, your own table for history. You pay per request and keep the data forever.

**You are an agency shipping client-facing dashboards this month.** A dedicated rank tracker with campaigns, stored history and visibility metrics gets you there without a data engineering sprint. Similarweb requires a campaign and an account-manager-issued key; Mangools lets you create the tracking through the API and issues a key on a free account with no card.

**You want to learn how the pipeline works before committing.** The open-source Node/Express wrapper around SerpApi is a readable reference for engine-specific depth limits and the "0 means not found within the maximum depth" convention. Just budget for the pagination work.

**You need positions for one market, one device, a few hundred keywords.** Any of the four works. Pick on price per keyword-check, and on whether you want the history handed to you or built by you.

## Before you pay

Confirm two things. First, price your actual target: run a handful of representative keywords through the Domain Cost Estimator and check the per-request credit cost, because Google at 25 credits is five times a standard page and twenty-five times what a casual reading of "1 credit per request" suggests. Second, decide where history lives. If the answer is "not our servers," a scraper is the right shape of tool and the retention question disappears.

ScraperAPI's free 1,000 credits will tell you within an hour whether the response schema fits your pipeline, and about 40 Google lookups is enough to test a real keyword set against a real location. After that the decision is arithmetic.

👉 [Start on the free plan, then pick the tier that matches your monthly Google request count](https://www.scraperapi.com/pricing?fp_ref=coupons)
