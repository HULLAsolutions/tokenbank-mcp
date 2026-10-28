# TokenBank MCP Server

An MCP server over **9,000 tokenized real-world assets and DeFi yields** —
tokenized treasuries, yield stablecoins, private credit, reinsurance tokens,
bonds, gold, real estate, tokenized stocks, staking, lending markets and DEX
liquidity pools — plus the **Hyperliquid perpetuals** on the same stocks. Built
around three questions most datasets skip: *several products track the same
thing — how do they differ?*, *how does an AI agent actually buy one on its own?*
and *how should it spread its money instead of betting on one contract?*

**15 read-only tools**, including a purchase path for every token an agent can
buy, a risk level 1–5 with its reasons, and **`build_portfolio`**: one amount
spread over several sources of yield, with the reasons an agent can read.

No API key. No sign-up. Read-only.

```
claude mcp add --transport http tokenbank https://mcp.tokenbank.world
```

Any MCP client works; the endpoint speaks Streamable HTTP.

| | |
|---|---|
| **Endpoint** | `https://mcp.tokenbank.world` |
| **Registry** | [`world.tokenbank/tokenbank`](https://registry.modelcontextprotocol.io/v0/servers?search=tokenbank) |
| **Transport** | Streamable HTTP, stateless |
| **Protocol** | `2025-06-18`, `2025-03-26`, `2024-11-05` |
| **Auth** | none |
| **Website** | [tokenbank.world](https://tokenbank.world) |

## For AI agents: spread it — `build_portfolio`

Buying more than one product? Call **`build_portfolio { "amount_usd": 1000, "max_risk": 3 }`**
(or `GET https://tokenbank.world/api/v1/agents/portfolio?amount_usd=1000&max_risk=3`).
An agent that always buys the single highest yield puts every dollar on one
contract; one depeg, exploit or default then takes everything. The tool returns:

- **Blocks** — one product per category within the risk budget, never the same
  exposure twice (five staked-ETH tokens are one exposure; a Pendle PT carries its
  underlying's), each paying at least the 13-week T-bill, each checked at its own
  amount (gas per chain included), equal weights under a cap (default 30 %).
- **Admixture** — 10 % in a product above the risk budget for extra yield, its
  possible loss capped by its size (`admixture_pct`, 0 switches it off).
- **Categories** — median yield, range and risk levels per category.
- **Summary and why** — yield of the blocks vs. the single best product, the worst
  single failure as % of the amount, failures on record (xUSD, USDX, Penpie).
- **Guide** — the steps (then `get_purchase_steps` per block) and when to rebalance.

Live example at [tokenbank.world/agents/portfolio](https://tokenbank.world/agents/portfolio).
For one purchase, `GET /api/v1/agents/evaluate?amount_usd=100&max_risk=3` checks
every option against an amount and a risk budget — each check with value, limit
and result, `chosen` and `backup` — the record a bot keeps of its decision.

**Risk levels 1–5** come from measurable signals only: source of yield (government
debt, borrowers, staking, trading fees, reinsurance, strategy), spread over the
13-week T-bill, size, exit liquidity, route, impermanent loss for pools. Every
level carries its reasons; unknown counts as the higher risk. Descriptive, not
advice. Products of a related project carry `relatedToOperator`.

## For AI agents: from discovery to purchase

Start with **`get_agent_buying_guide`** (or `GET https://tokenbank.world/api/v1/agents`).
It returns the whole how-to as data — six steps from wallet to confirmed purchase,
each with the MCP or API call to make:

1. **Find candidates** — `find_permissionless_assets`, `search_yield_products`
2. **Get the purchase path for one token** — `get_purchase_steps { "ticker": "syrupUSDC" }`
3. **Verify the contract** — the verified address per chain comes with the path
4. **Fund the wallet** — network and stablecoin pair to hold
5. **Approve, simulate, buy** — exact-amount approval, simulation first
6. **Confirm and know the exit** — redemption terms, KYC at redemption, settlement

Every curated token gets one of three purchase paths:

| Path | Meaning |
|---|---|
| `wallet-only` | A self-custody wallet buys it on a secondary market with no identity check (yield stablecoins, staking, DeFi lending, liquidity pools, Pendle fixed rates, some treasuries and reinsurance tokens) |
| `onboarding` | A person is onboarded once with the issuer; the agent then operates the approved wallet (most tokenized funds) |
| `platform` | Bought inside a platform account |

The server **describes** a purchase; it never builds, signs or sends a
transaction. Who may buy is set by the issuer's terms and the buyer's own law —
the server says so instead of guessing. Human-readable version:
[tokenbank.world/agents](https://tokenbank.world/agents).

## Why this exists

Apple exists on-chain as seven separate tokens from different issuers. NVIDIA as
eight, plus a perpetual future. They share a price driver and almost nothing
else: issuer, legal wrapper, holder rights, chain, custody, KYC gates and cost
all differ — and a perp is not ownership at all.

TokenBank keeps the **underlying** (the company, ETF or bond) apart from the
**instrument** (one issuer's token on it), so these can be compared side by side
instead of appearing as unrelated rows. See it on the web:
[every security tokenized more than once](https://tokenbank.world/underlyings) ·
[NVIDIA, compared](https://tokenbank.world/underlying/nvda) ·
[Apple, compared](https://tokenbank.world/underlying/aapl).

**Target / Area, not eligibility.** Records carry `targetArea` — whom the issuer
aims a product at (retail, professional, institutional) and in which market (EU,
US, UK, …), as the issuer states it. It describes the product; it is not a check
of whether *you* may buy it. That is set by the issuer's own terms, and the
server says so instead of guessing.

## Tools

| Tool | What it answers |
|---|---|
| `build_portfolio` | Spread an amount over several sources of yield: blocks, admixture, median yield per category, why spreading beats the single best, steps and rebalancing. |
| `get_agent_buying_guide` | Start here when an agent or bot should buy tokens on its own: the six steps with calls, purchase paths with live counts, which asset classes a wallet reaches without KYC, wallet-only candidates by yield, guardrails. |
| `get_purchase_steps` | How an agent buys ONE token: purchase path, the three KYC gates, network and verified contract, issuer route, measured trading venues, ordered steps and how to exit. |
| `find_permissionless_assets` | What a self-custody wallet can buy on a secondary market without KYC and hold itself. Strict: unverified access is excluded, not included. |
| `find_answer` | Answer pages and guides for a natural-language question — "can I buy Tesla as a token", "best stablecoin yield on Base". |
| `search_yield_products` | Search and filter the instruments for earning yield with crypto. |
| `get_product_details` | Full record for one instrument by ticker: yield, risk, custody, target/area, how to buy, source, verification date. |
| `compare_assets` | Two or more instruments side by side on yield, risk, minimum, custody, KYC gates, issuer and verification date. |
| `compare_with_savings_account` | Low-risk, instant-access instruments against a typical bank savings rate, with the trade-offs. |
| `find_tokenized_security` | Does a tokenized version of a company, ETF or bond exist, and from which issuer? By name, ticker, ISIN or a plain question, across all issuer catalogues. |
| `compare_tokenized_access` | For a security tokenized by more than one issuer: every token on it side by side, plus every Hyperliquid perpetual on it. |
| `search_realt_properties` | The individual tokenized US rental properties behind RealT, each with net rental yield and rent history. |
| `get_country_registers` | Official registers of tokenized assets and digital-asset firms for 250 jurisdictions. |
| `get_market_coverage` | Which countries this dataset can see into, which it cannot, and why. |
| `get_market_stats` | Shape and freshness of the dataset. |

Every tool is annotated `readOnlyHint: true` — nothing here writes, charges or
sends — and declares an `outputSchema`, so a client can type the answer instead
of parsing a string. Every record links to its page on tokenbank.world.

## Perps, honestly

The stock perpetuals come from Hyperliquid's public API (HIP-3 markets such as
XYZ and Paragon), refreshed daily. Each is mapped to its underlying from the
venue's own description of what the contract references — so an ADS worth 1/10
of a share stays apart from the share itself. Fees are stated at the entry tier,
checked against real fills; the front end you trade through may add its own.
TokenBank takes no fee and has no referral on any of them.

## Two tiers, never silently mixed

- **Curated** — rows that state a yield, a minimum and a risk. The only ones that
  can answer *"what does this pay"*. Ranking tools use this tier by default and
  report how many rows they left out.
- **Imported** — issuer-catalogue rows carrying identity, classification and
  target/area, but no figures. Use them for *"is X tokenized"*, never for yield
  rankings.

Every record carries its `tier`.

## Paging

Tools returning lists accept `offset` and cap `limit` at 100. The answer carries
`nextOffset` while more remains, and omits it at the end.

## Honest limits

- **Yields are variable and not guaranteed.** Figures come from the issuers and
  their published catalogues; they are sourced, not audited.
- **Who may buy is set by the issuer.** `targetArea` describes the product; an
  empty list means nothing is stated, not that the product is closed.
- **Not investment advice.** Every response carries this disclaimer.
- Some markets are structurally invisible to any public dataset: Japanese and
  Korean security tokens run on permissioned chains with domestic-only
  distribution. `get_market_coverage` says so explicitly.

## Also without MCP

- Open REST API, no key: [`/api/v1/assets`](https://tokenbank.world/api/v1/assets) ·
  [`/api/v1/underlyings`](https://tokenbank.world/api/v1/underlyings) ·
  [`/api/v1/agents`](https://tokenbank.world/api/v1/agents) (agent buying guide) ·
  [`/api/v1/agents/portfolio`](https://tokenbank.world/api/v1/agents/portfolio) (portfolio) ·
  [`/api/v1/agents/evaluate`](https://tokenbank.world/api/v1/agents/evaluate) (checks for one budget) ·
  [`/data/agent-buyable.json`](https://tokenbank.world/data/agent-buyable.json) (every agent-buyable product with route, gas, risk level) ·
  [`/api/v1/guides`](https://tokenbank.world/api/v1/guides) (every guide as JSON, `?topic=agents`)
- Agent buying guide for humans: [tokenbank.world/agents](https://tokenbank.world/agents)
- Machine-readable overview: [`llms.txt`](https://tokenbank.world/llms.txt) · [`openapi.json`](https://tokenbank.world/openapi.json)
- Issuers and their catalogues: [tokenbank.world/issuers](https://tokenbank.world/issuers)

## License

MIT for this repository (the manifest and documentation). The dataset behind the
server is published through the endpoints above.
