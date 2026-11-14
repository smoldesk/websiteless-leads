# Websiteless: Google Maps Leads Without a Website

![Websiteless: local businesses that are open, just not online](https://raw.githubusercontent.com/smoldesk/apify-scrapers/main/assets/websiteless/banner.webp)

Find local businesses on Google Maps that need a website: no website at all, only a Facebook or Instagram page, only a Yelp, Booksy or DoorDash listing, a dead domain, expired certificate or parked page, or an outdated site. Every lead comes with a lead score from 0 to 100 and plain-English reasons to pitch. Chains, duplicates and businesses with a working website are skipped for free. No login, no browser. Runs on [Apify](https://apify.com/smoldesk/websiteless-leads).

## What you get

- **Lead type** (`presence`): `none`, `social_only`, `listing_only`, `broken_website` or `outdated_website`, plus a one-word `websiteStatus` (`dead`, `error`, `parked`, `placeholder`, `live`).
- **Lead score and tier**: 0 to 100 from web presence, reviews, rating, phone, email and Google profile gaps. HIGH at 75+, MEDIUM at 55+.
- **Reasons to pitch**: `pitchReason` in plain English, ready for a mail-merge field, and the full `reasons` list.
- **Contact basics**: phone (local and international), Facebook and Instagram links, the listed link and what kind of site it is.
- **Google profile gaps**: unclaimed listing, no opening hours, fewer than 5 photos.
- **Emails** (optional): from the business website when it still loads. Charged only when found.
- **Only real breakage counts**: timeouts and bot walls are never sent as broken-website leads.
- **Weekly prospecting**: `onlyNewLeads` saves only leads that weren't there in earlier runs.

## Run it

Open [Websiteless on Apify](https://apify.com/smoldesk/websiteless-leads), type a trade and a city and click Start. Or call it from code with your [Apify token](https://console.apify.com/settings/integrations):

```bash
curl -X POST "https://api.apify.com/v2/acts/smoldesk~websiteless-leads/run-sync-get-dataset-items?token=YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "searchTerms": ["roofer"],
    "locations": ["Tucson, AZ"],
    "maxLeadsPerSearch": 20,
    "requirePhone": true
  }'
```

Python (`pip install apify-client`):

```python
from apify_client import ApifyClient

client = ApifyClient("YOUR_TOKEN")
run = client.actor("smoldesk/websiteless-leads").call(run_input={
    "searchTerms": ["nail salon", "barber"],
    "locations": ["Leeds, UK"],
    "leadTypes": ["noWebsite", "socialOnly", "listingOnly", "brokenWebsite"],
    "maxLeadsPerSearch": 50,
    "minLeadScore": 60,
})
for lead in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(lead["leadScore"], lead["name"], lead["phone"], lead["pitchReason"])
```

JavaScript (`npm install apify-client`):

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: 'YOUR_TOKEN' });
const run = await client.actor('smoldesk/websiteless-leads').call({
    searchTerms: ['plumber'],
    locations: ['Denver, CO'],
    maxLeadsPerSearch: 30,
    compact: true,
});
const { items } = await client.dataset(run.defaultDatasetId).listItems();
console.log(items.map((l) => [l.name, l.presence, l.leadScore]));
```

## Sample output

```json
{
  "name": "Desert Ridge Roofing",
  "presence": "none",
  "leadScore": 90,
  "leadTier": "HIGH",
  "pitchReason": "No website on the Google listing; 4.8★ from 212 reviews (established business); Google listing not claimed by the owner",
  "category": "Roofing contractor",
  "phone": "(520) 555-0182",
  "website": null,
  "websiteStatus": null,
  "rating": 4.8,
  "reviewsCount": 212,
  "claimedByOwner": false,
  "profileGaps": ["unclaimed_listing"],
  "address": "2210 E Example Rd, Tucson, AZ 85719, United States",
  "googleMapsUrl": "https://www.google.com/maps/place/?q=place_id:ChIJExample-DesertRidge01"
}
```

Export as JSON, CSV or Excel, or pipe it into Google Sheets, Make, Zapier or n8n.

## Use with AI agents (MCP)

Add it as a tool through the Apify MCP server:

```json
{
  "mcpServers": {
    "websiteless": {
      "url": "https://mcp.apify.com?tools=smoldesk/websiteless-leads"
    }
  }
}
```

Then ask your agent things like "find 30 roofers in Phoenix with no website and write a short cold email for each". Set `"compact": true` for short rows.

## Pricing

Pay per lead. No monthly fee. Prices drop on paid Apify plans.

| Result (per 1,000) | Free | Starter | Scale | Business |
|---|---|---|---|---|
| Lead | $5.00 | $4.00 | $3.00 | $2.50 |
| Contact details found (add-on) | +$2.00 | +$1.50 | +$1.20 | +$1.00 |

Businesses with a working website, chains, duplicates and filtered-out places are free.

## Limits

- About 120 places per Google search. Search depth "Deep" and "Deeper" search smaller areas when that runs out.
- Service-area businesses (plumbers, handymen, cleaners) often hide their street address. Coordinates and country are still there.
- Businesses with no website have no email to find. Emails are not verified.
- "Outdated" is a hint, not a verdict, so outdated sites are off by default and score lower.
- Chains are matched by name. Turn off `excludeChains` if a local business gets skipped.

## FAQ

**How do I find businesses without a website on Google Maps?**
Type a trade and a city, click Start. Only listings with no website, a social or directory page instead of a site, or (if you choose) a broken or outdated site are kept.

**Which trades have the most leads?**
Handymen, roofers, cleaners, nail salons and barbers. Dentists and lawyers usually have a site.

**Do I need a Google account or proxies?**
No. It uses no Google account and switches to Apify datacenter proxy on its own if Google slows it down.

## More scrapers

Websiteless is one of 21 no-login scrapers by Smoldesk Studios. See the full list: [smoldesk/apify-scrapers](https://github.com/smoldesk/apify-scrapers).
