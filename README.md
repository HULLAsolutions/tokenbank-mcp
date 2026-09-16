# TokenBank MCP Server

An MCP server over **4,168 tokenized real-world assets** — tokenized treasuries,
yield stablecoins, gold, real estate, tokenized stocks and staking — with the
field most datasets leave out: **who is actually allowed to buy each one.**

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

Most RWA datasets answer *what exists* and *how big it is*. The harder question
for anyone actually investing is **whether they are allowed to hold it** — and
the answer differs per instrument, per issuer and per country.

For real-world assets the gate to *mint* and the gate to *buy on a secondary
market* routinely differ: a fund can be permissioned at the issuer while its
token trades freely. And where several issuers tokenize the *same* security, one
of them may be reachable for EU retail while another is closed. That comparison
is what this server is built around.

## Tools

| Tool | What it answers |
|---|---|
| `search_yield_products` | Search and filter the instruments for earning yield with crypto. |
| `get_product_details` | Full record for one instrument by ticker: yield, risk, custody, availability, how to buy, source, verification date. |
| `compare_assets` | Two or more instruments side by side on the fields that decide between them — including the three access gates. |
| `compare_with_savings_account` | Low-risk, instant-access instruments against a bank savings rate, with the honest trade-offs rather than just the bigger number. |
| `find_permissionless_assets` | What a self-custody wallet can buy without KYC and hold itself. Strict by design: merely unverified access is excluded, not included. |
| `find_tokenized_security` | Does a tokenized version of a company, ETF or bond exist, and from which issuer? |
| `compare_tokenized_access` | For a security tokenized by more than one issuer: every token on it, with a verdict on **who may buy each**. |
| `search_realt_properties` | The individual tokenized US rental properties behind RealT, each with net rental yield, token price, monthly rent and occupancy. |
| `get_market_coverage` | Which countries this dataset can see into, which it cannot, and why. |
| `get_market_stats` | Shape and freshness of the dataset. |

Every tool is annotated `readOnlyHint: true` — nothing here writes, charges or
sends — and declares an `outputSchema`, so a client can type the answer instead
of parsing a string.

## Two tiers, never silently mixed

- **Curated** — rows that state a yield, a minimum and a risk. The only ones that
  can answer *"what does this pay"*. Ranking tools use this tier by default and
  report how many rows they left out.
- **Imported** — issuer-catalogue rows carrying identity, classification and
  access terms, but no figures. Use them for *"is X tokenized"*, never for yield
  rankings.

Every record carries its `tier`. Counts are reported per tier rather than as one
number, because a single total would read as *"this is all verified"* when four
fifths of it is catalogue.

## Paging

Tools returning lists accept `offset` and cap `limit` at 100. The answer carries
`nextOffset` while more remains, and omits it at the end — so walking the full
catalogue is a loop, not a guess.

## Honest limits

- **Yields are variable and not guaranteed.** Figures come from the issuers and
  their published catalogues; they are sourced, not audited.
- **Availability differs per country.** `euAccess` states what the issuer said —
  and "not stated" is reported as unknown rather than quietly treated as open.
- **Not investment advice.** Every response carries this disclaimer.
- Some markets are structurally invisible to any public dataset: Japanese and
  Korean security tokens run on permissioned chains with domestic-only
  distribution. `get_market_coverage` says so explicitly instead of returning an
  empty list that looks like an answer.

## Related

- Website and instrument pages: [tokenbank.world](https://tokenbank.world)
- Open REST API, also without a key: `https://tokenbank.world/api/v1/assets`
- Machine-readable overview: [`llms.txt`](https://tokenbank.world/llms.txt)

## License

MIT for this repository (the manifest and documentation). The dataset behind the
server is published through the endpoints above.
