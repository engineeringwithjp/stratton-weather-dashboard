# Stratton Ski Resort Snow Dashboard

A zero-dependency, single-file ski resort weather dashboard powered by [Open-Meteo](https://open-meteo.com/). Drop `index.html` on any static host (Netlify, Vercel, GitHub Pages) and you're live. Its purely front-end HTML/CSS/JS

---

## Tech Stack 
Tailwind CDN — fast styling with zero setup
Instead of installing a full CSS framework or writing tons of custom styles, Tailwind lets you style everything right in your HTML using utility classes. Because it loads from a CDN, you don’t have to configure anything — you just drop in one script tag and instantly get access to a huge styling toolkit. It keeps the project lightweight and lets you focus on layout and design without wrestling with CSS files.

Inter font — clean, modern typography
Inter is a widely used, super-readable font that gives your dashboard a polished, professional look. Loading it from Google Fonts means you don’t have to host anything yourself. It’s subtle, but it makes the whole interface feel more refined.

Open‑Meteo API — free weather + historical data
This is the engine behind all your weather information. Open‑Meteo provides real‑time conditions, hourly forecasts, and even past weather data — all without needing an API key. You simply build a URL with the parameters you want, and it returns everything in JSON. It’s perfect for a project like this because it’s reliable, fast, and completely free.

Chart.js — interactive charts via CDN
Chart.js is what powers all the visualizations: snowfall trends, temperature profiles, wind vs gusts, freezing levels, and more. Loading it from a CDN means no installation or bundling — just one script tag and you can start drawing charts on <canvas> elements. It’s lightweight, flexible, and easy to customize.

Plain JavaScript modules — data handling + rendering
Instead of using a framework like React or Vue, the dashboard uses simple JavaScript modules. These handle:
• Fetching and transforming weather data
• Caching results
• Updating the UI
• Rendering charts
It keeps the project easy to understand and avoids unnecessary complexity. Everything is transparent and editable.

Pillow (Python) — generating social share images
To make your dashboard look legit when someone shares it on social media, you need Open Graph and Twitter card images. Pillow (a Python imaging library) is used to generate those graphics — usually a 1200×630 card with your resort name, colors, and a clean background. You run the script once, save the images, and your meta tags point to them.

Microsoft Clarity (optional) — scroll heatmaps
If you want analytics, Clarity gives you a visual look at how people interact with your page — where they scroll, what they click, and how far they get. It’s optional, but it’s a nice touch if you want to understand how users engage with your dashboard. You just paste a small snippet into the <head> and it starts collecting anonymous interaction data.

---

## 🏔️ How to Swap to a Different Resort
Clone the repository 

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

The social images are generated separately with Python + Pillow 

Generate social share cards so the site looks pro when linked. With Python + Pillow:

Create a 1200x630 gradient background.
Draw stylized mountains + stat cards.
Save as assets/social/og-image.png and assets/social/twitter-card.png.
It’s a quick script that runs once and makes your meta tags real.

Re-generate them with your new resort name and colors after swapping locations.

---

## 🌐 Data Source

All weather data comes from [Open-Meteo](https://open-meteo.com/) — free, no API key required. Data refreshes every 10 minutes (configurable via `CACHE_TTL_MS`).

For all the weather data in this project, everything comes from Open‑Meteo, which is great because you don’t need an API key or an account. You simply build one long URL that tells Open‑Meteo exactly what information you want, and it sends back a full JSON response.
Here’s what that URL typically includes:

1. Current Conditions
You request the real‑time values you want to display on the dashboard, such as:
• Air temperature
• Feels‑like temperature
• Weather code (used to determine icons)
• Wind speed and gusts
• Snowfall rate
• Snow depth
These give you the “right now” snapshot at the resort.

2. Hourly Forecast Data
Next, you ask for the hour‑by‑hour breakdown. This is what powers the 24‑hour forecast cards and several charts. It usually includes:
• Hourly precipitation
• Hourly snowfall
• Chance of precipitation
• Freezing level height
• Hourly wind speeds and gusts
This is the data that helps skiers understand how the next few hours will evolve.

3. Daily Forecast Data
You also request daily summaries, which are used for the 7‑day forecast and some of the comparison blocks:
• Daily high and low temperatures
• Total snowfall for each day
• Total precipitation
• Maximum wind speeds
• Sunrise and sunset times
This gives the dashboard a broader, multi‑day outlook.

4. Units and Timezone
You also specify:
• The timezone of the resort (e.g., America/New_York for Stratton)
• The units you want the data in
	◦ mph for wind
	◦ inches for precipitation
	◦ Fahrenheit for temperature
This ensures the API returns data in the format your dashboard expects.

5. Retry Logic
Because weather APIs can occasionally fail or time out, it’s smart to add a tiny helper function that retries the request if something goes wrong. Using exponential backoff means:
• First retry happens quickly
• If it still fails, the next retry waits a bit longer
• And so on
This prevents the dashboard from breaking if the API hiccups for a moment.

## Historical Data and Comparisons

To give the dashboard some real context, you’re not just pulling live and forecasted weather — you’re also grabbing what actually happened over the past week. Open‑Meteo has a separate archive API that lets you request historical weather for any location and date range. You simply plug in:

• The resort’s latitude and longitude
• A start date (7 days ago)
• An end date (yesterday)
• The daily metrics you want (max temp, min temp, snowfall totals, etc.)

The URL looks something like this:
```css
https://archive-api.open-meteo.com/v1/archive?latitude=...&start_date=YYYY-MM-DD&end_date=YYYY-MM-DD&daily=temperature_2m_max,temperature_2m_min,snowfall_sum&...

```

Once you fetch that data, you can use it in a few important parts of the dashboard:

1. Snow Trend Chart
The historical snowfall becomes the “observed” line on your snow trend graph.
This lets users compare:
• What actually fell over the last week
vs.
• What the forecast says is coming next
It makes the chart feel grounded in reality instead of just showing predictions.

2. Yesterday vs Today Comparison
You can pull out yesterday’s snowfall, temperature, and wind gusts and compare them to today’s values.
This powers the quick “Yesterday vs Today” block, which is super helpful for skiers trying to understand:
• Did conditions improve?
• Did the storm intensify or taper off?
• Is today warmer, colder, windier?
It’s a simple feature, but it adds a ton of clarity.

3. ChatGPT Trip Planner Context
The trip planner isn’t just guessing — it uses the historical data to understand recent patterns.
For example:
• Has it been snowing consistently?
• Was there a warm spell that might affect surface conditions?
• Did winds spike earlier in the week?
Including the past seven days gives ChatGPT enough context to make smarter suggestions about gear, timing, and what to expect on the mountain.

## Speaking of CHATGPT
One of the coolest parts of this project ended up being the built‑in trip planner powered by ChatGPT. It turns your weather dashboard into something interactive and genuinely useful for planning a ski day.
Here’s how it works, broken down in a clearer way:
1. Add a Trip Planner Card to the UI
Create a dedicated section on the page that includes:
• A “Copy Dashboard JSON” button
• A quick “Open ChatGPT” link
• A <pre> block that shows the JSON you’re generating
This gives users a clean place to grab all the data in one click.
---
2. Build a Structured JSON Payload
The dashboard automatically compiles a well‑organized JSON object that includes:
• Current conditions (temp, wind, snowfall, etc.)
• Next 24 hours of hourly data
• 7‑day forecast
• Past 7 days of snowfall and precipitation
• Resort metadata (name, coordinates, timestamp, share URL)
It’s basically a complete weather briefing in machine‑readable form.
---
3. Show the JSON Live on the Page
The <pre> block updates every time new weather data is fetched.
This means the JSON is always fresh — no manual refresh needed.
---
4. Copy to Clipboard
The “Copy JSON” button uses the Clipboard API to instantly copy the entire payload.
If the browser doesn’t support it, there’s a fallback so it still works.
---
5. Provide a Suggested Prompt
Right next to the JSON, include a simple prompt like:
“Use this Dashboard data to plan tomorrow’s ski day.”
This gives users a starting point so they can paste the JSON into ChatGPT and immediately get:
• Gear recommendations
• Ideal lap order
• Timing suggestions
• Safety considerations
• Weather‑based strategy
It turns your dashboard into a mini ski‑planning assistant.

## What are you waiting for Deploy it!

Finally got to the last part of the readme huh?
What are you waiting for 

You’ve made it all the way to the end — time to ship this thing. Getting the dashboard live is the easy part:
• Deploy the repo to Netlify, Vercel, or GitHub Pages
• Update your <link rel="canonical">, Open Graph URL, and JSON‑LD "url" so they point to your actual domain
• Run the site through a mobile emulator to make sure the horizontal chart scrolling and header layout feel smooth
• Use Google’s Rich Results Test to confirm your structured data is valid
• If you want analytics, drop your tracking snippet right before </head>
Once that’s done, your dashboard is officially mountain‑ready.

🎨 Vibe Coding Tips
A few lessons that make the build process way more enjoyable:
• Keep everything in one file while you’re experimenting — break it apart only if you truly need to
• Don’t be shy with console.log when exploring API responses
• Stress‑test your charts with extreme scenarios: huge storms, zero snowfall, high winds
• Think like a skier at sunrise — “What do I need to know before I head out?”
Let those questions shape your layout and priorities