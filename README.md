# Find local businesses without a website — free n8n workflow

An n8n workflow that builds the most obvious prospect list there is: **businesses that show up on
Google Maps but have no website**. It searches Google Places API (New) in any city, paginates,
removes businesses already saved in earlier runs, keeps only the ones with no website, and appends
them to Google Sheets.

Free, MIT licensed. **No scraping service, no SerpAPI/Apify/Scrape.do, no community nodes** — only
nodes that ship with n8n.

![nodes](https://img.shields.io/badge/nodes-14%20(9%20working%20%2B%205%20notes)-blue)

## What it does

1. **Four values to edit** in a single Set node: `nicho`, `ciudad`, `regionCode`, `nombreHoja`.
2. **`places:searchText`** with 20 results per page, up to 3 pages (60 businesses per run), stopping
   when Google stops returning `nextPageToken`.
3. **A narrow field mask** (13 fields). Places bills by SKU depending on the fields you ask for;
   asking for more moves the run into a more expensive tier.
4. **Dedupe by `placeId`** against previous executions (5000-key history): run it daily and it only
   appends what is new.
5. **Only businesses with no website pass** (`tieneWeb = false`). Discarded ones end in a visible
   branch instead of vanishing.
6. **Append to Google Sheets**, 14 columns.

## Measured, not claimed

The workflow was executed on a **clean `n8nio/n8n:3.0.0` instance** against a mock of Places API
(New) with 3 pages and 11 businesses:

| Check | Result |
|---|---|
| Missing node types in 3.0.0 · typeVersion above maximum | 0 · 0 |
| Import into a clean instance | 14/14 nodes |
| Rows written out of 11 businesses | **7** — exactly the 7 with no website |
| `placeId` of the rows | match the 7 with no website, one to one |
| Columns in the sheet | 14 of 14 |
| Second run (dedupe) | **0 rows** — nothing repeated |
| Locality column | `Santiago`, not the country (`Chile`) |

One real bug came out of that test: Google's `formattedAddress` ends with the **country**, so
"locality" was being filled with `Chile`. This workflow takes the second-to-last part instead.

## What you need

- n8n (tested on 3.0.0; built-in nodes only).
- A **Google Places API (New)** key with billing enabled, stored in an *HTTP Header Auth*
  credential (`X-Goog-Api-Key`).
- An empty Google Sheet and the Google Sheets credential. Put the spreadsheet ID in the node where
  it says `REPLACE_WITH_YOUR_SPREADSHEET_ID`.

## Import

Download [`workflows/find-local-businesses-without-website.json`](workflows/find-local-businesses-without-website.json)
and import it in n8n (*Workflows → Import from File*). Test it with the **manual** trigger before
turning the 08:00 schedule on.

## Related

- **MCP server (free, MIT):** [mcp-local-leads](https://github.com/ellokillox/mcp-local-leads) — the
  same first step from inside your AI client (Claude Desktop, Claude Code, Cursor): ask for local
  businesses without a website in any city and get them back as a list or a CSV.

## What this is not

This is **step 1** of a larger pipeline (find + filter). It does not include a lead scoring rubric,
per-business research prompts, first-contact templates, an MCP server to query the list from an AI
client, a measured API-cost guide, or industry configurations. Those are part of a paid kit:
**https://b2b.medicpc51.cl** (Spanish site, local payment in Chile).

If you only need this, take it and use it. That is the point of MIT.

## En español

Flujo de n8n que arma la lista de negocios locales que aparecen en Google Maps **pero no tienen sitio
web**. Busca con Places API (New) en cualquier ciudad, pagina hasta 3 páginas, deduplica por
`placeId` entre corridas y deja en Google Sheets solo los negocios sin web.

Página con la explicación y la descarga: **https://b2b.medicpc51.cl/plantilla-n8n/**

## License

MIT — see [LICENSE](LICENSE).
