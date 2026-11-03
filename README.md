# E-commerce Search & Rankings MCP: Amazon, eBay, Walmart, Google Shopping, AliExpress, Shopify, Etsy

**Marketplace search results and product rankings as MCP tools**, in one bundle: what shows up, in what order and at what price when a shopper searches Amazon, eBay, Walmart, Google Shopping, AliExpress or Etsy, plus full catalogs of any public Shopify store. One hosted endpoint, one Actor per marketplace.

Hosted remote MCP server (Streamable HTTP) on [Apify's MCP gateway](https://mcp.apify.com). **Client config plus a small open-source stdio proxy server. No Actor source.**

**Disclosure:** entries marked "(maintained by us)" are Actors I maintain on Apify Store (publisher `publicrecords`). Every other Actor is independent, made by its own publisher and picked on merit (Store usage, ratings and recent maintenance). This repo is not affiliated with those publishers or with the sites the Actors read.

## MCP endpoint

```
https://mcp.apify.com/?tools=junglee/Amazon-crawler,delicious_zebu/ebay-product-listing-scraper,e-commerce/walmart-fast-product-scraper,epctex/google-shopping-scraper,piotrv1001/aliexpress-listings-scraper,autofacts/shopify,publicrecords/etsy-search-scraper
```

Auth: sign in with Apify (OAuth) when your client prompts, or send an Apify API token as a Bearer token. Runs are billed on your own Apify account under each Actor's own Store pricing; see each Store page.

## Tools

| Actor | Store page | MCP tool | What it returns |
|---|---|---|---|
| **Amazon Product Scraper** | [junglee/Amazon-crawler](https://apify.com/junglee/Amazon-crawler) | `junglee--Amazon-crawler` | Amazon search results and product pages by keyword, category or URL: title, price, rating, review count, ASIN, seller, position. |
| **eBay Listings Scraper** | [delicious_zebu/ebay-product-listing-scraper](https://apify.com/delicious_zebu/ebay-product-listing-scraper) | `delicious_zebu--ebay-product-listing-scraper` | eBay search and category results across 8 marketplaces with keyword, price, condition and seller filters. |
| **Walmart Fast Product Scraper** | [e-commerce/walmart-fast-product-scraper](https://apify.com/e-commerce/walmart-fast-product-scraper) | `e-commerce--walmart-fast-product-scraper` | Walmart.com and Walmart.ca search and category pages: name, price, availability, average rating, seller. |
| **Google Shopping Insights** | [epctex/google-shopping-scraper](https://apify.com/epctex/google-shopping-scraper) | `epctex--google-shopping-scraper` | Google Shopping results: products, prices, merchants, reviews and offer links. |
| **AliExpress Search Scraper** | [piotrv1001/aliexpress-listings-scraper](https://apify.com/piotrv1001/aliexpress-listings-scraper) | `piotrv1001--aliexpress-listings-scraper` | AliExpress keyword search: price, original price, discount, rating and sales volume in your ship-to country. |
| **Shopify Scraper** | [autofacts/shopify](https://apify.com/autofacts/shopify) | `autofacts--shopify` | Any public Shopify store's catalog: every variant's price, SKU and stock status. |
| **Etsy Search Scraper** (maintained by us) | [publicrecords/etsy-search-scraper](https://apify.com/publicrecords/etsy-search-scraper) | `publicrecords--etsy-search-scraper` | Etsy search results by keyword, category or market page: title, price, rating, reviews, Bestseller and Star Seller badges, ad flag and search rank. |

The Apify gateway also exposes helper tools to read run results (`get-actor-run`, `get-dataset-items`). Want fewer tools? Drop any Actor from the `tools=` list.

## Who it's for

- Sellers and brands: where does my product rank for a keyword on each marketplace?
- Pricing teams: what do the top results for a query cost on Amazon, Walmart and Google Shopping today?
- Dropshippers and sourcing: which AliExpress listings sell the most for a keyword?
- Market research: how crowded is a niche across marketplaces?

## Example prompts

- Search Amazon, eBay and Walmart for `<keyword>` and compare the top 10 results by price and rating.
- Which Etsy listings rank on page one for `<keyword>`, and which carry a Bestseller badge or are ads?
- Find Google Shopping offers for `<product>` and list the cheapest merchants.
- Pull the full catalog of `<shopify-store-url>` with prices and stock status.

## Cursor / Claude Code / any remote-MCP client

```json
{
  "mcpServers": {
    "ecommerce-search": {
      "url": "https://mcp.apify.com/?tools=junglee/Amazon-crawler,delicious_zebu/ebay-product-listing-scraper,e-commerce/walmart-fast-product-scraper,epctex/google-shopping-scraper,piotrv1001/aliexpress-listings-scraper,autofacts/shopify,publicrecords/etsy-search-scraper"
    }
  }
}
```

Claude Code:

```sh
claude mcp add --transport http ecommerce-search "https://mcp.apify.com/?tools=junglee/Amazon-crawler,delicious_zebu/ebay-product-listing-scraper,e-commerce/walmart-fast-product-scraper,epctex/google-shopping-scraper,piotrv1001/aliexpress-listings-scraper,autofacts/shopify,publicrecords/etsy-search-scraper"
```

## Claude Desktop (local bridge)

```json
{
  "mcpServers": {
    "ecommerce-search": {
      "command": "npx",
      "args": ["-y", "@apify/actors-mcp-server", "--actors", "junglee/Amazon-crawler,delicious_zebu/ebay-product-listing-scraper,e-commerce/walmart-fast-product-scraper,epctex/google-shopping-scraper,piotrv1001/aliexpress-listings-scraper,autofacts/shopify,publicrecords/etsy-search-scraper"],
      "env": { "APIFY_TOKEN": "YOUR_APIFY_TOKEN" }
    }
  }
}
```

## Local stdio server (open source)

This repo also ships a small MCP server in [`src/`](src/) for clients that only run local stdio servers. It starts over stdio, connects to the hosted Apify MCP endpoint above with **your own** `APIFY_TOKEN`, and forwards `tools/list` and `tools/call` unchanged. The token is only sent in the `Authorization` header and is never logged.

```json
{
  "mcpServers": {
    "ecommerce-search": {
      "command": "npx",
      "args": ["-y", "github:GeminiGeorge22/ecommerce-search-rankings-mcp"],
      "env": { "APIFY_TOKEN": "YOUR_APIFY_TOKEN" }
    }
  }
}
```

Or from a clone:

```bash
git clone https://github.com/GeminiGeorge22/ecommerce-search-rankings-mcp.git
cd ecommerce-search-rankings-mcp && npm install
APIFY_TOKEN=YOUR_APIFY_TOKEN node src/server.js
```

Optional env: `APIFY_ACTORS` (comma-separated subset of the Actors above) and `APIFY_MCP_URL` (full endpoint override).

Tests run against an in-memory mock server, with no network and no token: `npm test`. Requires Node.js 18+.

## See also

- [ecommerce-sales-mcp](https://github.com/GeminiGeorge22/ecommerce-sales-mcp): E-commerce sales signals
- [forecast-accuracy-mcp](https://github.com/GeminiGeorge22/forecast-accuracy-mcp): Forecast accuracy and track records
- [sports-betting-stats-mcp](https://github.com/GeminiGeorge22/sports-betting-stats-mcp): Sports betting odds and stats
- [sports-odds-mcp](https://github.com/GeminiGeorge22/sports-odds-mcp): Kambi sportsbook odds
- [etsy-sales-mcp](https://github.com/GeminiGeorge22/etsy-sales-mcp): Etsy search and shop sales
- [ats-jobs-mcp](https://github.com/GeminiGeorge22/ats-jobs-mcp): ATS job postings (Greenhouse, Lever, Ashby, SmartRecruiters)
- [gov-tenders-mcp](https://github.com/GeminiGeorge22/gov-tenders-mcp): Government tenders and grants

## Registry

Official MCP Registry name: `io.github.GeminiGeorge22/ecommerce-search-rankings-mcp`

## License

MIT, for this documentation, config and the proxy server code in `src/`. Each Actor belongs to its publisher.
