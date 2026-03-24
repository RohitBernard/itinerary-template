# Itinerary Template

A reusable interactive travel itinerary UI. Add a new trip by dropping a JSON file in `trips/` — no code changes needed.

---

## Adding a new trip

1. Create `your-city.json` following the schema below and add it to the private `my-trips` repo
2. Commit and push — GitHub Action auto-updates this repo → Cloudflare Pages rebuilds automatically

---

## Local development

```bash
cd ~/projects/itinerary-template
python3 -m http.server 8765
open "http://localhost:8765/?trip=nola"
```

`config.js` is gitignored — create it locally with your API key:
```bash
echo "const MAPS_API_KEY = 'YOUR_KEY';" > config.js
```

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
