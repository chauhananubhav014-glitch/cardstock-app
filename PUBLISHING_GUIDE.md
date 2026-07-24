# Getting Cardstock onto the Play Store

Your app is a website. The Play Store only takes Android app packages (`.aab` files). The bridge between the two is a **Trusted Web Activity (TWA)** — a tiny native Android wrapper, officially supported by Google, that opens your hosted website full-screen with no browser bar. It's the standard way real Play Store apps built from web code get published (Twitter Lite, Starbucks, and thousands of others work this way).

Everything below assumes that path. Total cost: **$25** (one-time Play Console fee) and free hosting.

---

## Part 1 — Host the app somewhere public (you do this)

The TWA needs a real HTTPS URL to point at — it can't point at a file on your computer.

**Easiest free option: GitHub Pages**
1. Create a free GitHub account if you don't have one.
2. Create a new repository, e.g. `cardstock-app`.
3. Upload these 6 files (all in this folder) to the repo root: `index.html`, `manifest.json`, `sw.js`, `privacy.html`, and the `icons/` folder.
4. In the repo, go to **Settings → Pages**, set source to the `main` branch, root folder. Save.
5. GitHub gives you a URL like `https://yourusername.github.io/cardstock-app/`. That's your live app — open it on your phone to confirm it loads and "Add to wallet" works.

(Netlify or Vercel work identically if you'd rather use those — drag-and-drop the folder, get a URL.)

**Before moving on:** edit `privacy.html` and replace the placeholder contact line with your real email — Play Console will reject the listing without one.

---

## Part 2 — Package it as an Android app (you or I can help live)

Google's `bubblewrap` CLI does this in a few commands. You'll need [Node.js](https://nodejs.org) and a JDK installed.

```bash
npm install -g @bubblewrap/cli
bubblewrap init --manifest https://yourusername.github.io/cardstock-app/manifest.json
```

It will ask a few questions (package name like `com.yourname.cardstock`, app name, etc. — defaults are usually fine). Then:

```bash
bubblewrap build
```

This produces `app-release-signed.aab` — the file the Play Store actually accepts — plus a signing key (`android.keystore`). **Back up that keystore file somewhere safe.** If you lose it, you can never update this app again under the same listing.

**One extra step for TWAs:** you need to prove you own the website, via a "Digital Asset Links" file. `bubblewrap` will generate the exact JSON it needs — you just upload it to `https://yourusername.github.io/cardstock-app/.well-known/assetlinks.json`. Bubblewrap prints the exact content and path when you run `build`.

I can walk you through any of these commands interactively if you paste me the output/errors as you go — I can't run them myself since they need your Google/GitHub accounts.

---

## Part 3 — Play Console account and store listing (you do this — needs your identity + payment)

1. Go to [play.google.com/console](https://play.google.com/console/signup), sign up, pay the one-time **$25** registration fee, verify your identity (can take a few hours to 2 days).
2. Click **Create app** → fill in app name (Cardstock), default language, "App" type, "Free".
3. Play Console will walk you through a required checklist. The main things you'll need ready:
   - **App icon**: 512×512 PNG — use `icons/icon-512.png` from this folder.
   - **Feature graphic**: 1024×500 banner image (I can generate this for you if you'd like).
   - **Screenshots**: at least 2, phone-sized — take these from your own phone once the TWA is installed, or I can mock some up.
   - **Short description** (80 chars) and **full description** (up to 4000 chars) — draft below, edit freely.
   - **Privacy policy URL**: `https://yourusername.github.io/cardstock-app/privacy.html`
   - **Content rating questionnaire**: answer honestly — this app has no user-generated content, ads, or data collection, so it should land in the lowest/all-ages rating easily.
   - **Data safety form**: since Cardstock stores everything locally and collects nothing, you'll answer "No" to essentially every data collection question — this is usually the fastest part.
4. Upload the `.aab` from Part 2 under **Production → Create new release** (or start with **Internal testing** track first — recommended, so you can install and check it privately before the public release).
5. Submit for review. First-time app reviews typically take a few hours to a few days.

**Draft store copy** (edit as you like):
- Short description: *"Track your credit cards' benefits — lounge access, movie offers, and more — in one place."*
- Full description: *"Cardstock helps you keep tabs on the perks buried in your credit cards' fine print: airport lounge visits, movie ticket offers, dining discounts, fuel surcharge waivers, and insurance cover. Add the cards you hold to see everything at a glance, or browse and compare other popular cards side by side before you apply for a new one. All your data stays on your device — nothing is uploaded anywhere."*

---

## What I already changed in the code for you

- Swapped the storage from a Claude-only API to standard `localStorage`, so the app now works as a normal website (this was the main blocker).
- Added `manifest.json`, a service worker (`sw.js`), and app icons so it behaves as an installable PWA — a prerequisite for the TWA wrapper.
- Added `privacy.html`, since Play Console requires a hosted privacy policy URL even for a simple, data-free app like this.

## What I can't do for you

I don't have a Google account, can't pay the $25 fee, verify your identity, or run commands on your machine. But paste me any error messages or console output as you go through Parts 1–3, and I'll help you debug them in real time.
