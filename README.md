# @pipeworx/jp-sesc-recommendations

Japan's Securities and Exchange Surveillance Commission (証券取引等監視委員会, SESC) recommendations that the Financial Services Agency order an administrative monetary penalty (課徴金納付命令勧告): disclosure-inspection cases (false statements in securities reports) and trading-investigation cases (market manipulation, deceptive schemes, insider trading).

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 2016+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `jp_sesc_recommendations_search(query?, year?, list?)` — recommendations for one fiscal year (April to March; default the current one; `year=2025` is 令和7年度), from 2009. `list` is `all` (default, two requests), `disclosure` (開示検査) or `unfair_trading` (取引調査). `query` matches the recommendation title in Japanese; full and half width are folded. Each row: the firm, the kind of violation, the recommendation date, the title and the commission's press record. Only firms are returned: a case titled after an employee, an officer, an unnamed trader or a bare stock is dropped and counted in `skipped_possible_individuals`. The answer states the period covered and what was searched; zero matches says which lists and year (`empty_reason: no_match`); a list with no page for the year is reported in `unread` (`list_not_published`), never as no cases.

## Auth

Keyless.

## Data sources

- Securities and Exchange Surveillance Commission (証券取引等監視委員会), recommendations for administrative monetary penalty orders. Read live per call, one page per list; nothing is stored. `data_as_of` is the newest recommendation date on the pages read.

Traps worth knowing: a recommendation is the commission asking the FSA Commissioner to issue a penalty order; it is not the order and not a court finding. Insider-trading cases are mostly about individuals, so this tool returns few of them (the skipped count says how many); the disclosure list is mostly listed companies. Titles are the commission's own wording in Japanese. Search a company by its Japanese name without the 株式会社 prefix to match either spelling.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "jp-sesc-recommendations": {
      "url": "https://gateway.pipeworx.io/jp-sesc-recommendations/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/jp-sesc-recommendations/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 2016+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/jp_sesc_recommendations_search \
  -H 'Content-Type: application/json' \
  -d '{"list":"disclosure","year":2025}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/jp_sesc_recommendations_search`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-jp-sesc-recommendations` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/jp-sesc-recommendations/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "jp-sesc-recommendations": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-jp-sesc-recommendations"]
    }
  }
}
```

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Jp Sesc Recommendations data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
