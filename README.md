# mcp-besttime

BestTime MCP — foot-traffic / venue busyness forecasts (besttime.app)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1576+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `besttime_forecast` | How busy is <venue> by hour/day (foot-traffic forecast) — creates a busyness forecast for a public venue (restaurant, bar, gym, store, museum) by name + address. Returns the 24-hour busyness curve (0-100) for each weekday plus busy/quiet/peak hours and a venue_id for later queries. Example: besttime_forecast({ venue_name: "McDonald's", venue_address: "Ocean Ave, San Francisco", _apiKey: "pri_xxx:pub_yyy" }) |
| `besttime_query` | Busyness for a venue at a specific hour or day — reads an already-forecasted venue (by venue_id from besttime_forecast). Pass `hour` for a single hour, or omit it to get the whole day's curve. Example: besttime_query({ venue_id: "ven_...", day: 4, hour: 18, _apiKey: "pri_xxx:pub_yyy" }) |
| `besttime_live` | Is <venue> busy right now vs usual — returns live foot-traffic busyness compared to the forecasted level for this hour. Identify the venue by venue_id, or by venue_name + venue_address. Example: besttime_live({ venue_name: "Blue Bottle Coffee", venue_address: "66 Mint St, San Francisco", _apiKey: "pri_xxx:pub_yyy" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "besttime": {
      "url": "https://gateway.pipeworx.io/besttime/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/besttime/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 1576+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "besttime": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-besttime"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-besttime
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Besttime data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
