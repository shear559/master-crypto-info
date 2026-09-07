# MasterCrypto

> **A Hebrew, right-to-left landing page for a paid cryptocurrency course — one static HTML file, no build step, no framework.**

**Live:** [crypto-course-landing-tau.vercel.app](https://crypto-course-landing-tau.vercel.app)

<p align="center">
  <img src="assets/preview.webp" alt="MasterCrypto — hero of the live site" width="100%">
</p>

`HTML` · `CSS` · `vanilla JS` · `GSAP 3.12.5` · `ScrollTrigger` · `Lenis 1.1.14` · `Vercel`

---

## Shipping the entire product as one HTML file

The page keeps its layout, styles and vanilla JavaScript in `index.html`, with a separate SVG brand mark and Vercel configuration. There is no package manager, bundler or framework. Vercel serves the static page directly.

## Isolating numerals so digits survive the RTL flow

Hebrew runs right-to-left; Latin digits, currency and percentages run left-to-right. Dropped unprotected into an RTL paragraph, a string like `+18.6%` visually reorders. Every numeral goes in a `.num` span:

```css
.num, .mono {
  font-family: var(--font-mono);
  font-feature-settings: 'tnum' 1;
  direction: ltr;
  unicode-bidi: isolate;
}
```

`unicode-bidi: isolate` pins the run so surrounding Hebrew cannot reorder it; `tnum` keeps a live price ticker from jittering as digit widths change. The candlestick time axis deliberately runs left-to-right *against* the page direction — the convention financial charts are read in.

## Degrading five motion engines to a static page

Five namespaced, self-contained engines: `mc-net` (hero lattice canvas), `mc-coincard` (tilting course-concept cards), `mc-chain` (blockchain showcase), `mc-candles` (draw-on-scroll candlestick), `mc-ticker` (RTL marquee).

Each obeys one contract — GPU-only transforms, `requestAnimationFrame` loops paused when the section leaves the viewport (`IntersectionObserver`) or the tab is hidden (`visibilitychange`), and a fully-formed static fallback.

Reveal animations sit behind a `gsap-pending` class with a 1.6s safety timeout that lifts the gate if `window.gsap` never appears: a CDN failure costs the animation, never the content.

A reduce-motion bridge mirrors *both* the OS `prefers-reduced-motion` setting and the in-page accessibility toggle into shared flags (`html.mc-reduce`, `window.__mcReduceMotion`) and an `mc-reduce-change` event. Engines subscribe to the bridge, not the media query alone — otherwise the in-page toggle would leave canvas animation running.

## Constraining third-party scripts with a CSP allowlist

`vercel.json` sets a Content-Security-Policy response header whose `script-src` allows `'self'`, `'unsafe-inline'` — the page's JavaScript is inline, so the policy has to permit it — and three named hosts, with `object-src 'none'` and `base-uri 'self'`. The allowlist constrains which third-party origins can serve script, not what inline script may run. The page loads four external scripts. The three CDN libraries — GSAP, ScrollTrigger, Lenis — are Subresource-Integrity pinned with `sha384` hashes, so a tampered CDN response fails closed instead of executing; the fourth, the accessibility widget, is served from its own allowlisted host without a pin.

Accessibility ships as a shared widget loaded by script tag rather than a per-site panel, so it is maintained in one place across projects. A `.skip-link` leads to `#main-content`; mixed-script labels carry direction and language annotations.

## Following the learning journey

The page starts with what cryptocurrency is, shows a transaction moving through a blockchain, and introduces the tools before explaining buying, KYC, selling and cashing out. Risk and market-reading sections lead into the course curriculum. Automation remains in the curriculum, without a duplicate trading-bot showcase.

Market prices and charts are labelled as simulations. Invented graduate counts, ratings, completion rates and instructor credentials have been removed. The enrollment section explicitly says online registration is not connected; pricing links lead to that status instead of an inactive contact channel.

## How it was verified

- **2026-09-07** — the live HTML matched the committed `index.html` at `85881ca`. Chromium loaded the current learning journey without page errors or failed network requests.
- The CSP header is present on the live response (`server: Vercel`) and matches `vercel.json`.
- The live page shows the simulated-market label and the explicit enrollment status.

## Screenshots

<p align="center">
  <img src="assets/chain-showcase.webp" alt="Blockchain showcase section — the mc-chain engine's linked block cards on the dark canvas" width="100%">
</p>

<p align="center">
  <img src="assets/candlestick-chart.webp" alt="Draw-on-scroll BTC/USD candlestick chart — a left-to-right time axis inside the right-to-left page" width="100%">
</p>

<p align="center">
  <img src="assets/mobile-home.webp" alt="Mobile home view at 390 px — course introduction, simulated market strip and accessibility widget" width="45%">
</p>

---

Source is private. Built by [@shear559](https://github.com/shear559).
