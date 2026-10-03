# TokenBank MCP Server

An MCP server over **4,500+ tokenized real-world assets** — tokenized treasuries,
yield stablecoins, gold, real estate, tokenized stocks and staking — plus the
**Hyperliquid perpetuals** on the same stocks. Built around one question most
datasets skip: *several products track the same thing — how do they differ?*

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
| `compare_tokenized_access` | For a security with more than one way in: every token on it (issuer, structure, KYC gates, custody, chain, target/area) **and every Hyperliquid perp** (fees, 7-day funding, leverage, what the contract references). Also covers stocks that trade on-chain only as a perp. |
| `find_tokenized_security` | Does a tokenized version of a company, ETF or bond exist, and from which issuer? Backed / xStocks, Ondo Global Markets, Aktionariat, Binaryx, plus Dinari, Coinbase, Robinhood Chain and others. |
| `search_yield_products` | Search and filter the instruments for earning yield with crypto. |
| `get_product_details` | Full record for one instrument by ticker: yield, risk, custody, target/area, how to buy, source, verification date. |
| `compare_assets` | Two or more instruments side by side on the fields that decide between them, including the three KYC gates. |
| `compare_with_savings_account` | Low-risk, instant-access instruments against a bank savings rate, with the honest trade-offs rather than just the bigger number. |
| `find_permissionless_assets` | What a self-custody wallet can buy on a secondary market without KYC and hold itself. Strict: unverified access is excluded, not included. |
| `search_realt_properties` | The individual tokenized US rental properties behind RealT, each with net rental yield, rent history and a page of its own. |
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
  [`/api/v1/underlyings`](https://tokenbank.world/api/v1/underlyings)
- Machine-readable overview: [`llms.txt`](https://tokenbank.world/llms.txt)
- Issuers and their catalogues: [tokenbank.world/issuers](https://tokenbank.world/issuers)

## License

MIT for this repository (the manifest and documentation). The dataset behind the
server is published through the endpoints above.
