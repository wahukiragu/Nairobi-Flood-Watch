# 🌊 NairobiFloodWatch — Maji Alert System

> A community-powered, real-time flood intelligence dashboard for Nairobi.
> Built as a prototype to show what's possible. **Looking for developers to plug in real data.**

![Status](https://img.shields.io/badge/status-prototype-orange) ![License](https://img.shields.io/badge/license-MIT-blue) ![Contributions](https://img.shields.io/badge/contributions-welcome-brightgreen)

---

## The Problem

Nairobi floods kill people not just because of the water — but because **nobody can see the system in real time.** Matatu drivers, residents, emergency responders, and city planners are all making decisions blind.

This dashboard is a prototype of what a shared situational awareness tool could look like: crowd-sourced reports, flood zone mapping, river levels, and route status — all in one place, accessible on any phone.

---

## What's In the Prototype

- **Live flood zone map** — Nairobi areas color-coded by severity (Critical / High / Medium / Low)
- **Community report feed** — crowd-sourced incident reports with location and severity
- **River level gauges** — Nairobi River, Mathare River, Ngong River, Ruiru River
- **Rainfall intensity bars** — per zone
- **Route status** — key roads (Mombasa Road, Jogoo Road, Ngong Road, etc.)
- **Report button** — submit a flood incident that appears on the map instantly
- **Emergency contacts** — Kenya Red Cross, Fire, Emergency, County Disaster

Currently all data is **simulated / static**. The architecture is ready for real data sources.

---

## 🔌 Data Sources to Connect

This is where we need help. Below are the APIs and feeds that would make this real:

### 1. Community Reports — Ushahidi
**What it does:** Crowd-sourced incident reporting (text, photo, GPS location)
**API:** [Ushahidi Platform API v5](https://docs.ushahidi.com/platform-developer-documentation/api)
**Notes:** Ushahidi was literally built in Nairobi. They have an existing deployment at [ushahidi.io](https://ushahidi.io). Reports submitted via SMS, WhatsApp, or web can appear on the map.

```js
// Fetch recent flood posts from Ushahidi
GET https://{your-deployment}.ushahidi.io/api/v5/posts?form=flood_report&status=published
```

---

### 2. Flood Forecasting — Google Flood Hub
**What it does:** AI-based river flood forecasting, 7-day predictions, gauge readings
**Coverage:** Kenya rivers are included
**API:** [Google Flood Hub API](https://developers.google.com/flood-hub)
**Notes:** Returns flood inundation maps and gauge data per river reach. Free for humanitarian use.

```js
// Get flood forecast for a river gauge
GET https://floodhub.googleapis.com/v1/gauges/{gaugeId}/forecastedFlood
```

---

### 3. Rainfall Data — Kenya Meteorological Department
**What it does:** Official rainfall readings from weather stations across Kenya
**Contact:** [www.meteo.go.ke](http://www.meteo.go.ke)
**Notes:** No public API currently exists — this is a gap. A developer could scrape bulletins or work with KMD directly to expose an API endpoint. KMD publishes daily rainfall bulletins as PDFs.

**Alternative:** Open-Meteo (free, no API key needed)
```js
// Get hourly rainfall for Nairobi
GET https://api.open-meteo.com/v1/forecast?latitude=-1.2921&longitude=36.8219&hourly=precipitation&timezone=Africa/Nairobi
```

---

### 4. Traffic / Road Status — Google Maps Roads API
**What it does:** Real-time traffic conditions on named roads
**API:** [Google Maps Roads API](https://developers.google.com/maps/documentation/roads)
**Notes:** Requires a Google Cloud API key. Can detect slow/stopped traffic which often correlates with flooding.

```js
// Get traffic on a route
GET https://roads.googleapis.com/v1/snapToRoads?path=-1.2864,36.8172|-1.3035,36.8142&key={API_KEY}
```

---

### 5. Satellite Imagery — Copernicus Emergency Management Service
**What it does:** Rapid flood mapping using Sentinel satellite imagery
**API:** [CEMS API](https://emergency.copernicus.eu/mapping/ems-product-component/API)
**Notes:** Free for humanitarian response. Can generate flood extent maps within hours of activation.

---

### 6. WhatsApp Reporting — Twilio / WhatsApp Business API
**What it does:** Let residents report floods by sending a message to a WhatsApp number
**API:** [Twilio WhatsApp API](https://www.twilio.com/whatsapp)
**Notes:** User sends location + photo → webhook parses it → adds to Ushahidi feed → appears on map. This is the highest-impact integration for community reach.

```
User sends: "Maji mengi sana Mathare 3rd Street" + photo
→ Twilio webhook receives message
→ Parses location, adds to Ushahidi as new post
→ Appears on map within 60 seconds
```

---

## Architecture (Proposed)

```
Community Reports          Official Data             Satellite
─────────────────          ─────────────             ─────────
WhatsApp → Twilio    →     KMD Rainfall API   →      Copernicus
SMS → Africa's Talking     Google Flood Hub           Sentinel
Web form (this UI)         Google Maps Traffic
                               │
                          [Ushahidi Backend]
                               │
                    ┌──────────┴──────────┐
                    │   NairobiFloodWatch  │
                    │   (This Dashboard)   │
                    └─────────────────────┘
                               │
                    ┌──────────┴──────────┐
               Web Browser          WhatsApp Bot
               (This file)          (Alerts out)
```

---

## How to Run Locally

No build step needed. It's a single HTML file.

```bash
# Clone or download
git clone https://github.com/YOUR_USERNAME/nairobi-flood-watch

# Open in browser
open nairobi-flood-watch.html

# Or serve locally
npx serve .
```

---

## How to Deploy (GitHub Pages — Free)

1. Create a GitHub account if you don't have one
2. Create a new repository: `nairobi-flood-watch`
3. Upload `nairobi-flood-watch.html` and rename it to `index.html`
4. Go to **Settings → Pages → Source → Deploy from branch → main**
5. Your dashboard is live at `https://YOUR_USERNAME.github.io/nairobi-flood-watch`

---

## Contributing

This project needs:

- [ ] **Backend developer** — connect Ushahidi API, build a data aggregation layer
- [ ] **KMD contact** — help get official rainfall data as a feed
- [ ] **Twilio/WhatsApp integration** — build the SMS/WhatsApp reporting pipeline
- [ ] **GIS/mapping** — replace the SVG map with real Leaflet.js + OpenStreetMap tiles
- [ ] **Mobile optimization** — make it usable on a 2G phone in the field
- [ ] **Swahili localization** — UI in Kiswahili for wider reach

**To contribute:** Fork the repo, make your changes, open a pull request.

---

## Relevant Organizations

| Org | What they do | Link |
|-----|-------------|------|
| **Ushahidi** | Crisis mapping platform, born in Nairobi | [ushahidi.com](https://ushahidi.com) |
| **iHub Kenya** | Nairobi tech community hub | [ihub.co.ke](https://ihub.co.ke) |
| **Code for Kenya** | Civic tech, open data | [codeforkenya.org](https://codeforkenya.org) |
| **Kenya Red Cross** | Disaster response | [redcross.or.ke](https://redcross.or.ke) |
| **Kenya Meteorological Dept** | Official weather/rainfall data | [meteo.go.ke](http://meteo.go.ke) |
| **Google Flood Hub** | AI flood forecasting, Kenya covered | [sites.research.google/floods](https://sites.research.google/floods/) |

---

## License

MIT — free to use, modify, and deploy. Just build something useful.

---

*Started by one person who looked at Nairobi flooding and thought: data and technology can move us one step toward sense.*

*If you build on this, tag [@NairobiFloodWatch](#) on X so we can track progress.*
