# Itinerary Template

A reusable interactive travel itinerary UI. Add a new trip by dropping a JSON file in `trips/` — no code changes needed.

**Live example:** `nola.itinerary.yourdomain.com`

---

## Adding a new trip

1. Create `trips/your-city.json` following the schema below
2. Commit and push to `my-trips` (private repo)
3. GitHub Action auto-updates this repo → Cloudflare Pages rebuilds
4. Access at `your-city.itinerary.yourdomain.com`

---

## JSON Schema

```json
{
  "meta": {
    "title": "City Name",
    "subtitle": "X–Y Day Itinerary · N stops across Z neighbourhoods",
    "icon": "🗺️"
  },
  "days": {
    "1": {
      "label": "DAY 1",
      "title": "Day title shown in the panel",
      "desc": "One or two sentences describing the day's theme.",
      "routeUrl": "https://www.google.com/maps/dir/Stop+1/Stop+2/Stop+3",
      "center": { "lat": 0.0, "lng": 0.0 },
      "zoom": 14,
      "sections": [
        {
          "label": "Morning",
          "night": false,
          "stops": [
            {
              "name": "Place Name",
              "type": "Category (e.g. Cafe, Museum, Bar)",
              "rating": 4.5,
              "reviews": "1,234",
              "desc": "2–3 sentence description with personality. What makes it worth visiting.",
              "emoji": "☕",
              "lat": 0.0000,
              "lng": 0.0000,
              "place_id": "ChIJ...",
              "tip": "Optional insider tip shown in gold italic. null if none."
            }
          ]
        },
        {
          "label": "Night Out",
          "night": true,
          "stops": []
        }
      ],
      "alt": {
        "label": "Short label for the alternative (e.g. 'Prefer something quieter?')",
        "text": "HTML string. Can include <a href=\"...\">links</a>."
      }
    },
    "2": {
      "...": "same structure"
    }
  }
}
```

### Field notes

| Field | Type | Notes |
|-------|------|-------|
| `meta.icon` | string | Single emoji displayed in the header |
| `days` | object | Keys are day numbers as strings: `"1"`, `"2"`, etc. |
| `sections[].night` | boolean | `true` applies gold text styling to stop names |
| `stop.rating` | number \| null | `null` = no rating shown |
| `stop.reviews` | string \| null | Formatted string, e.g. `"44,945"` |
| `stop.place_id` | string \| null | Google Maps Place ID (starts with `ChIJ`). Used for "Open in Maps" deep links. `null` falls back to lat/lng. |
| `stop.tip` | string \| null | `null` = no tip shown |
| `day.alt` | object \| null | `null` = no alternative block shown |
| `day.alt.text` | string | HTML allowed — use for linking to specific venues |

### Getting a Place ID

Search the place on [Google Maps](https://maps.google.com), click it, then extract the `place_id` from the URL — it starts with `ChIJ`. Or ask an AI: "What is the Google Maps Place ID for [place name, city]?"

---

## Setup (one-time)

### Repos
- `itinerary-template` — this repo (public) — Cloudflare Pages source
- `my-trips` — private repo — contains only JSON files + GitHub Action

### Cloudflare Pages
1. Connect to `itinerary-template`
2. Enable submodule cloning and grant access to `my-trips`
3. Add env var: `MAPS_API_KEY = your_google_maps_key`
4. Build command: `echo "const MAPS_API_KEY = '${MAPS_API_KEY}';" > config.js`
5. Output directory: `.`

### GitHub Action (`my-trips`)
The `update-trips-submodule.yml` action in this repo runs when `my-trips` is pushed.
1. Create a GitHub Personal Access Token with `repo` scope
2. Add it as a secret named `TEMPLATE_REPO_PAT` in `my-trips` settings
3. Update `YOUR_GITHUB_USERNAME` in the workflow file

### Routing (custom domain)
- Wildcard DNS: `*.itinerary.yourdomain.com → your-pages-project.pages.dev` (CNAME)
- Cloudflare Worker on `*.itinerary.yourdomain.com`:

```javascript
export default {
  async fetch(request) {
    const url = new URL(request.url);
    const trip = url.hostname.split('.')[0];
    url.hostname = 'your-pages-project.pages.dev';
    url.searchParams.set('trip', trip);
    return fetch(url.toString(), request);
  }
}
```
