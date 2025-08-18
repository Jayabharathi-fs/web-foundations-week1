# Web Vitals

# Part 1

## What are Web Vitals vs Core Web Vitals

**Web Vitals** : Web Vitals are a set of performance metrics introduced by Google to measure how fast, responsive, and visually stable a website feels to real users.

Web Vitals focus on:

- Loading experience → How quickly something meaningful appears.

- Interactivity → How soon the page responds to user actions.

- Visual stability → Whether things jump around on the screen unexpectedly.

**Core web vitals** : Core Web Vitals are the most important Web Vitals that Google considers essential for a good user experience and uses in search ranking signals.

The current core web vitals are:

- LCP – Largest Contentful Paint (Loading)

  - How quickly the largest visible element (image, video, big text) appears.

- INP – Interaction to Next Paint (Interactivity) (replaced FID in 2024)

  - How long it takes for the page to visually respond after a user interaction (click, tap, key press).

- CLS – Cumulative Layout Shift (Visual Stability)

  - How much the page layout shifts unexpectedly while loading.

## FCP (First Contentful Paint)

**What it Measures :**
The time from when a page starts loading → until the first piece of content (text, image, SVG, or background image) is painted on the screen.
It’s the moment the user first sees something meaningful (not just a blank page).

**Good Threshold :**

- Good: ≤ 1.8 seconds
- Needs improvement: 1.8s – 3.0s
- Poor: > 3.0s

**Common Causes of poor FCP :**

- Render-blocking CSS/JavaScript (large files that must load before rendering starts)
- Slow server response (high TTFB – Time to First Byte)
- Unoptimized fonts (FOIT/FOUT delays first paint)
- Large images or non-compressed assets
- No caching/CDN → longer delivery time
- Too many third-party scripts slowing render

## LCP (Largest Contentful Paint)

**What it Measures :**

The time from when the page starts loading → until the largest visible content element (image, video, or large text block) in the viewport is fully rendered. It reflects how quickly the main content becomes visible to the user.

**Good Threshold :**

- Good: ≤ 2.5 seconds
- Needs improvement: 2.5s – 4.0s
- Poor: > 4.0s

**Common Causes of poor LCP :**

- Slow server response (high TTFB)
- Render-blocking CSS/JavaScript
- Unoptimized or large images/videos
- Lazy-loading important above-the-fold content
- Web fonts loading late, delaying text render
- Client-side rendering delays (JS frameworks loading main content too slowly)

## TBT (Total Blocking Time)

**What it Measures :**

The total time between First Contentful Paint (FCP) and Time to Interactive (TTI) where the main thread is blocked for more than 50 milliseconds, making the page unresponsive to user input (clicks, scrolls, taps). It shows how long users experience a “frozen” page during loading.

**Good Threshold :**

- Good: ≤ 200 milliseconds
- Needs improvement: 200ms – 600ms
- Poor: > 600ms

**Common Causes of poor TBT :**

- Heavy JavaScript execution (long tasks blocking the main thread)
- Large JS bundles that take time to parse and execute
- Third-party scripts running synchronously (ads, analytics)
- Expensive DOM manipulations or layout recalculations
- Excessive event listeners or inefficient JS code

## CLS (Cumulative Layout Shift)

**What it Measures :**

CLS measures how much visible content unexpectedly shifts or moves on a page during loading or user interaction. It tracks layout instability — when elements jump around, causing a poor user experience.

**Good Threshold :**

- Good: ≤ 0.1
- Needs Improvement: 0.1 – 0.25
- Poor: > 0.25

**Common Causes of poor CLS :**

- Images or videos without fixed width and height
- Ads, embeds, or iframes inserted without reserved space
- Web fonts causing late re-layout (FOIT/FOUT)
- Dynamically inserted content (e.g., banners, popups) pushing existing content
- Unexpected animations or transitions that shift layout

## SI (Speed Index)

**What it Measures :**

Speed Index measures how quickly the visible parts of a webpage are populated with meaningful content during loading — basically how fast the page looks like it’s loading to users.

**Good Threshold :**

- Good: ≤ 3.4 seconds
- Needs Improvement: 3.4s – 5.8s
- Poor: > 5.8s

**Common Causes of poor SI :**

- Render-blocking resources like CSS or JS delaying paint
- Large or unoptimized images/videos slowing visual load
- Complex or slow critical rendering path
- Too many network requests before first paint
- Heavy JavaScript execution blocking rendering

## Lab data (Lighthouse) vs Field data (CrUX/RUM)

**Lab Data** (e.g., Lighthouse)

- What it is: Performance data collected in a controlled, simulated environment.

- Conditions: Fixed device specs (usually mid-range desktop or mobile), fixed network speed (often throttled), no user variability.

- Purpose: Consistent, repeatable tests for debugging and optimizing performance.

**Field Data**(e.g., CrUX – Chrome User Experience Report, RUM – Real User Monitoring)

- What it is: Performance data gathered from real users visiting your site on diverse devices, browsers, locations, and network conditions.

- Conditions: Vary widely — slow/fast networks, old/new devices, different geographies, cached or fresh loads.

- Purpose: Understand actual user experience at scale.

---

# Part 2

## 1. Metrics Table

| Site                      | Device  | FCP  | LCP  | TBT    | CLS   | SI    | PerformanceScore | Accessibility | Best Practices | SEO |
| ------------------------- | ------- | ---- | ---- | ------ | ----- | ----- | ---------------- | ------------- | -------------- | --- |
| https://www.wikipedia.org | Mobile  | 1.6s | 1.7s | 0ms    | 0     | 1.6s  | 99               | 97            | 79             | 100 |
|                           |         |      |      |        |       |       |                  |               |                |     |
| https://www.wikipedia.org | Desktop | 0.4s | 0.6s | 0ms    | 0     | 0.4s  | 100              | 100           | 78             | 100 |
|                           |         |      |      |        |       |       |                  |               |                |     |
| https://www.nytimes.com   | Mobile  | 2.4s | 3.2s | 4210ms | 0.023 | 14.2s | 51               | 96            | 57             | 92  |
|                           |         |      |      |        |       |       |                  |               |                |     |
| https://www.nytimes.com   | Desktop | 0.7s | 7.6s | 630ms  | 0.029 | 4.3s  | 41               | 93            | 56             | 100 |
|                           |         |      |      |        |       |       |                  |               |                |     |

## 2. Top Issues

### wikipedia

**Mobile**

- LCP slightly higher than FCP (1.7s vs 1.6s) → likely small image or content render delay.

- Best Practices score low (79) → possible mixed content, insecure requests, or outdated APIs.

- Accessibility score slightly lower (97) → minor color contrast or ARIA issues.

**Desktop**

- Best Practices score low (78) → similar causes as Mobile (security or coding patterns).

- Accessibility perfect (100) → better compliance compared to Mobile.

- Performance metrics (FCP 0.4s, LCP 0.6s, SI 0.4s) → extremely fast, but small LCP gap still suggests image load sequencing could be optimized.

### nytimes

**Mobile**

- High TBT – NYTimes (4.21s) blocking main thread.

- Slow SI – NYTimes (14.2s) due to heavy content loading.

- High LCP – NYTimes (3.2s) from large hero/media content.

**Desktop**

- High LCP – NYTimes (7.6s) from large hero image or slow rendering.

- High SI – NYTimes (4.3s) due to heavy assets.

- TBT spikes – NYTimes (0.63s) from blocking JS tasks.

## 3. Root-Cause Notes

**FCP** – Slower on NYTimes due to large initial render and render-blocking resources.

**LCP** – Large hero images or delayed loading of main content images.

**TBT** – Long JS tasks, third-party ads, and scripts blocking main thread.

**CLS** – Small shifts (NYTimes 0.023–0.029) likely from late-loaded ads or fonts.

**SI** – Heavy DOM, large images, and multiple scripts delaying visual completeness.

## 4. Recommendations

- LCP – Compress/resize hero images, serve WebP/AVIF, preload hero image.

- TBT – Defer non-critical JS, split long tasks, async load third-party scripts.

- SI – Reduce unused CSS/JS, implement lazy loading for below-the-fold images.

- CLS – Reserve image/video dimensions, use font-display: swap, pre-allocate ad slots.

- General – Use HTTP/2 or 3, enable caching, implement CDN for static assets.
