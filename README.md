# SnowLab — Ski Resort Snow Dashboard

A zero-dependency, single-file ski resort weather dashboard powered by [Open-Meteo](https://open-meteo.com/). Drop `index.html` on any static host (Netlify, Vercel, GitHub Pages) and you're live.

---

## 🏔️ How to Swap to a Different Resort

Everything resort-specific lives in **one config block** and a handful of clearly labeled spots. You do not need to touch any of the chart or rendering logic — just follow the steps below.

---

### Step 1 — Find Your Resort's Coordinates & Elevations

Before opening the file, grab these three things for your target resort:

| What you need | Where to find it |
|---|---|
| Latitude & Longitude | [latlong.net](https://www.latlong.net/) or Google Maps (right-click → "What's here?") |
| Village / Base elevation (ft) | Resort's official trail map or website |
| Mid-mountain elevation (ft) | Same as above |
| Summit elevation (ft) | Same as above |

> **Convert feet → meters:** multiply feet × 0.3048.
> Example: 3,940 ft summit → 3940 × 0.3048 = **1,201 m**

---

### Step 2 — Edit `APP_CONFIG` (the main config block)

Open `index.html` and find this block near the top of the `<script>` section (around line 470):

```js
const APP_CONFIG = {
    resortName: 'Stratton Mountain',
    latitude: 43.1117,
    longitude: -72.9094,
    timezone: 'America/New_York',
    shareUrl: 'https://stratton-report.netlify.app/'
};
```

Replace every value for your new resort:

```js
const APP_CONFIG = {
    resortName: 'Stowe Mountain Resort',        // ← Resort display name
    latitude: 44.5303,                          // ← Decimal latitude
    longitude: -72.7814,                        // ← Decimal longitude (negative = West)
    timezone: 'America/New_York',               // ← IANA timezone string (see table below)
    shareUrl: 'https://your-site.netlify.app/'  // ← Your live URL (update after deploy)
};
```

**Common IANA timezones for North American ski resorts:**

| Region | Timezone string |
|---|---|
| Vermont, New Hampshire, Maine | `America/New_York` |
| Colorado, Utah, Wyoming | `America/Denver` |
| California, Oregon, Washington | `America/Los_Angeles` |
| British Columbia (Canada) | `America/Vancouver` |
| Alberta (Canada) | `America/Edmonton` |
| Quebec (Canada) | `America/Toronto` |

---

### Step 3 — Update `RESORT_ELEVATIONS`

Find this block directly below `APP_CONFIG`:

```js
const RESORT_ELEVATIONS = [
    { label: 'Base',         value: 570  },
    { label: 'Mid-Mountain', value: 884  },
    { label: 'Summit',       value: 1201 }
];
```

Replace the `value` numbers (in **meters**) with your resort's elevations. Keep the three labels or rename them to match the resort's own terminology (e.g. `'Village'`, `'Alpine Zone'`, `'Peak'`).

```js
// Example — Stowe Mountain Resort, VT
const RESORT_ELEVATIONS = [
    { label: 'Base',         value: 420  },   // 1,380 ft
    { label: 'Mid-Mountain', value: 900  },   // 2,953 ft
    { label: 'Summit',       value: 1339 }    // 4,393 ft
];
```

These bands appear as dotted reference lines on the **Freezing Level Tracker** chart, so accurate values make the chart genuinely useful.

---

### Step 4 — Set the Wind Hold Threshold

Find this line just below `RESORT_ELEVATIONS`:

```js
const WIND_THRESHOLD_HOLD = 45; // mph
```

Change `45` to the typical lift-hold wind speed for your resort (in **mph** if using imperial, **km/h** if you switched back to metric). This sets the red shaded zone on the **Wind vs. Gusts** chart.

Most US resorts hold lifts somewhere between **40–55 mph**. Check the resort's operations page or ask their snow report line for the exact number.

---

### Step 5 — Update the Cache Key

Find this line:

```js
const CACHE_KEY = 'stratton-weather-cache-v2';
```

Change it to something unique for your new resort:

```js
const CACHE_KEY = 'stowe-weather-cache-v2';
```

This prevents stale Stratton data from loading when a user first opens the new resort's page in the same browser.

---

### Step 6 — Update Text & Branding in the HTML

Use **Find & Replace** (`Ctrl+H` / `Cmd+H`) to swap out resort-specific text in the HTML section (above the `<script>` tag):

| Find | Replace with |
|---|---|
| `Stratton Mountain` | Your resort name |
| `Stratton, Vermont` | Your resort location |
| `stratton-report.netlify.app` | Your new live URL |
| `stratton.com/the-mountain/mountain-report/webcams` | Your resort's webcam page URL |

**Webcam list** — find the `<ul>` inside the Mountain Cams card and update the `<li>` items with your resort's actual cam locations:

```html
<ul class="list-disc list-inside mt-4 text-gray-600 space-y-1">
    <li>Base Lodge</li>
    <li>Summit</li>
    <li>Mid-Mountain</li>
</ul>
```

---

### Step 7 — Update SEO & Social Meta Tags

In the `<head>`, update these tags so your page looks correct when shared or indexed:

```html
<title>Stowe Mountain Snow Report</title>

<meta name="description" content="Get the ultimate Stowe Mountain snow report with live conditions...">
<meta name="keywords" content="Stowe Mountain, snow report, ski conditions, Vermont...">

<link rel="canonical" href="https://your-site.netlify.app/">

<meta property="og:title" content="Stowe Mountain Snow Report — Live Conditions & Forecast">
<meta property="og:url" content="https://your-site.netlify.app/">
<meta property="og:image" content="https://your-site.netlify.app/assets/social/og-image.png">

<meta name="twitter:title" content="Stowe Mountain Snow Report — Live Conditions & Forecast">
<meta name="twitter:image" content="https://your-site.netlify.app/assets/social/twitter-card.png">
```

---

### Step 8 — Update the JSON-LD Structured Data

Also in the `<head>`, find the `<script type="application/ld+json">` block and update the geographic and social fields:

```json
{
  "name": "Stowe Mountain Snow Report",
  "description": "Real-time snow report for Stowe Mountain Resort, Vermont.",
  "url": "https://your-site.netlify.app/",
  "geo": {
    "latitude": 44.5303,
    "longitude": -72.7814
  },
  "areaServed": {
    "name": "Stowe Mountain Resort, Vermont"
  },
  "sameAs": [
    "https://www.stowe.com/",
    "https://www.instagram.com/stowemountainresort/",
    "https://www.facebook.com/StoweMountainResort/"
  ]
}
```

---

### Step 9 — Optional: Change the Color Scheme

The color theme is controlled by CSS variables near the top of the `<style>` block. The key values to change:

```css
/* Main accent color (stat numbers) */
.stat-metric {
    color: #15803d;   /* ← swap to any hex color */
}

/* Page background */
body {
    background-color: #f0f7f2;   /* ← swap to any hex color */
}

/* Snow card highlight background */
.bg-blue-50 { background-color: #f0fdf4 !important; }

/* Button/action color */
.bg-blue-600 { background-color: #d97706 !important; }
.hover\:bg-blue-700:hover { background-color: #b45309 !important; }
```

Look up the target resort's brand colors on their website or press kit and plug them in here.

---

## ✅ Quick Checklist

Before deploying, confirm you've updated:

- [ ] `APP_CONFIG.resortName`
- [ ] `APP_CONFIG.latitude` and `APP_CONFIG.longitude`
- [ ] `APP_CONFIG.timezone`
- [ ] `APP_CONFIG.shareUrl`
- [ ] `RESORT_ELEVATIONS` (all three bands in meters)
- [ ] `WIND_THRESHOLD_HOLD` (in mph or km/h matching your unit choice)
- [ ] `CACHE_KEY` (unique string for this resort)
- [ ] Page `<title>` and all `<meta>` tags
- [ ] `<link rel="canonical">` URL
- [ ] JSON-LD structured data (name, geo, sameAs)
- [ ] Webcam button URL and cam list
- [ ] Color scheme (optional)

---

## 📁 Project Structure

```
.
├── index.html          ← Everything lives here
└── assets/
    └── social/
        ├── og-image.png        ← 1200×630 Open Graph image
        └── twitter-card.png    ← Twitter/X share card
```

The social images are generated separately with Python + Pillow (see professor's Step 8). Re-generate them with your new resort name and colors after swapping locations.

---

## 🌐 Data Source

All weather data comes from [Open-Meteo](https://open-meteo.com/) — free, no API key required. Data refreshes every 10 minutes (configurable via `CACHE_TTL_MS`).
