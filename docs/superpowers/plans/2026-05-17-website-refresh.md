# Fanager Website Refresh Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Refresh the single-page marketing site `fanager.app` for app v1.1.0 — new screenshots, updated feature copy, a new Players audience act, iOS-live/Android-soon messaging, and a "Bold Editorial" visual redesign.

**Architecture:** One static `index.html` (Tailwind via CDN, no build step), edited section-by-section in place. New screenshots copied into `images/screens/`. Existing reveal-on-scroll IntersectionObserver, FAQ accordion, and sticky role-tab JS are preserved and extended (3 → 4 tabs). Deployed unchanged via Firebase Hosting.

**Tech Stack:** Static HTML5, Tailwind CSS (CDN config block), vanilla JS (no libraries), Inter font (Google Fonts), JSON-LD FAQ schema.

**Source spec:** `docs/superpowers/specs/2026-05-17-website-refresh-design.md` (read it once before starting; this plan implements it).

**Working directory:** `/Users/qubaldo/Development/fanager-website`

**Note on "tests":** This is a static site with no test framework. Each task's verification step is a concrete shell/browser check (grep, image-exists, JSON parse, open in browser) — run it and confirm the stated expected output before committing.

---

## File Structure

- **Create:** `images/screens/` — 8 web-named screenshots (the only new asset dir).
- **Modify:** `index.html` — every section; this is the bulk of the work.
- **Untouched:** `images/badges/app-store-badge.svg` (reused), `images/og-banner.png` (reused), all legal/auth pages, `sitemap.xml`, `robots.txt`, `firebase.json`. Old `images/feature-*.png`, `role-*.png`, `hero-featured.png` are left on disk but no longer referenced.

Screenshot name map (source → destination):

| Source (`league-mgr-app/tools/appstore-screenshots/public/screenshots/raw/`) | Destination (`images/screens/`) |
|---|---|
| `01-my-leagues-owner.png` | `screen-my-leagues.png` |
| `02-score-entry.png` | `screen-score-entry.png` |
| `03-standings.png` | `screen-standings.png` |
| `04-team-lineup.png` | `screen-team-lineup.png` |
| `05-schedule-rsvp.png` | `screen-schedule-rsvp.png` |
| `06-game-detail-prediction.png` | `screen-game-prediction.png` |
| `07-following.png` | `screen-following.png` |
| `08-create-league.png` | `screen-create-league.png` |

---

### Task 1: Copy and verify screenshot assets

**Files:**
- Create: `images/screens/screen-my-leagues.png` (+ 7 more per the map above)

- [ ] **Step 1: Create the directory and copy the 8 files**

```bash
cd /Users/qubaldo/Development/fanager-website
mkdir -p images/screens
SRC=/Users/qubaldo/Development/league-mgr-app/tools/appstore-screenshots/public/screenshots/raw
cp "$SRC/01-my-leagues-owner.png"        images/screens/screen-my-leagues.png
cp "$SRC/02-score-entry.png"             images/screens/screen-score-entry.png
cp "$SRC/03-standings.png"               images/screens/screen-standings.png
cp "$SRC/04-team-lineup.png"             images/screens/screen-team-lineup.png
cp "$SRC/05-schedule-rsvp.png"           images/screens/screen-schedule-rsvp.png
cp "$SRC/06-game-detail-prediction.png"  images/screens/screen-game-prediction.png
cp "$SRC/07-following.png"               images/screens/screen-following.png
cp "$SRC/08-create-league.png"           images/screens/screen-create-league.png
```

- [ ] **Step 2: Verify all 8 exist and are valid PNGs of expected size**

Run:
```bash
cd /Users/qubaldo/Development/fanager-website
ls images/screens | wc -l
sips -g pixelWidth -g pixelHeight images/screens/screen-my-leagues.png | tail -2
```
Expected: count is `8`; dimensions `pixelWidth: 1170` / `pixelHeight: 2532`. If a source file is missing, STOP — do not proceed; report which file is absent.

- [ ] **Step 3: Commit**

```bash
git add images/screens
git commit -m "assets: add v1.1.0 app screenshots for website refresh"
```

---

### Task 2: Update `<head>` — title, meta, OG, Twitter (SEO + iOS-live messaging)

**Files:**
- Modify: `index.html` (the `<head>` block, lines ~6–14)

- [ ] **Step 1: Replace the title + meta + OG + Twitter tags**

Find this block in `index.html`:

```html
    <title>Fanager — Manage Your Recreational Soccer League</title>
    <meta name="description" content="The all-in-one app for recreational soccer leagues. Create leagues, manage teams, collect payments, and track standings. Free on iOS and Android.">
    <meta property="og:title" content="Fanager — Manage Your Recreational Soccer League">
    <meta property="og:description" content="The all-in-one app for recreational soccer leagues. Create leagues, manage teams, collect payments, and track standings. Free on iOS and Android.">
    <meta property="og:type" content="website">
    <meta property="og:url" content="https://fanager.app">
    <meta name="twitter:card" content="summary_large_image">
    <meta name="twitter:title" content="Fanager — Manage Your Recreational Soccer League">
    <meta name="twitter:description" content="The all-in-one app for recreational soccer leagues. Create leagues, manage teams, collect payments, and track standings.">
```

Replace it with:

```html
    <title>Fanager — Run &amp; Follow Your Recreational Soccer League</title>
    <meta name="description" content="Run your rec soccer league or follow your favorite team. Auto schedules, Stripe fees, live scores, lineups, practices, and match predictions. Free on iOS — Android coming soon.">
    <meta property="og:title" content="Fanager — Run &amp; Follow Your Recreational Soccer League">
    <meta property="og:description" content="Run your rec soccer league or follow your favorite team. Auto schedules, Stripe fees, live scores, lineups, practices, and match predictions. Free on iOS — Android coming soon.">
    <meta property="og:type" content="website">
    <meta property="og:url" content="https://fanager.app">
    <meta property="og:image" content="https://fanager.app/images/og-banner.png">
    <meta name="twitter:card" content="summary_large_image">
    <meta name="twitter:title" content="Fanager — Run &amp; Follow Your Recreational Soccer League">
    <meta name="twitter:description" content="Run your rec soccer league or follow your favorite team. Live scores, lineups, practices, predictions. Free on iOS — Android coming soon.">
    <meta name="twitter:image" content="https://fanager.app/images/og-banner.png">
```

- [ ] **Step 2: Verify the old "available on Android" claim is gone from `<head>`**

The new copy intentionally contains the phrase "Android coming soon", so do NOT grep for a bare "coming soon" — grep only for the OLD available-now claims:

```bash
sed -n '1,40p' index.html | grep -nEi "Free on iOS and Android|Coming soon to (the )?App Store" || echo "CLEAN"
```
Expected: `CLEAN` (the only Android mention left in the head is the new "Android coming soon" wording, which is correct and must NOT be flagged).

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "seo: refresh head meta — iOS-live messaging, 1.1.0 features"
```

---

### Task 3: Nav bar — App Store link, drop "Coming soon"

**Files:**
- Modify: `index.html` nav (lines ~42–48)

- [ ] **Step 1: Replace the nav right-side cluster**

Find:

```html
            <div class="flex items-center gap-6">
                <a href="#features" class="text-slate-400 hover:text-white text-sm hidden sm:inline">Features</a>
                <a href="#download" class="text-slate-400 hover:text-white text-sm hidden sm:inline">Download</a>
                <span class="hidden lg:inline text-sm text-gray-500 italic">Coming soon to App Store &amp; Google Play</span>
                <span class="lg:hidden text-sm text-gray-500 italic">Coming soon</span>
            </div>
```

Replace with:

```html
            <div class="flex items-center gap-6">
                <a href="#features" class="text-slate-400 hover:text-white text-sm hidden sm:inline">Features</a>
                <a href="#download" class="text-slate-400 hover:text-white text-sm hidden sm:inline">Download</a>
                <a href="https://apps.apple.com/app/id6759442301" class="bg-white text-navy text-sm font-semibold px-4 py-2 rounded-full hover:bg-slate-200 transition-colors hover:no-underline">Get the app</a>
            </div>
```

- [ ] **Step 2: Verify**

Run:
```bash
grep -n "apps.apple.com/app/id6759442301" index.html | head -1
grep -nEi "Coming soon to App Store" index.html || echo "NO NAV COMING-SOON"
```
Expected: line printed for the App Store URL; `NO NAV COMING-SOON`.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "nav: replace coming-soon text with App Store link"
```

---

### Task 4: Bold Editorial hero

**Files:**
- Modify: `index.html` hero `<section>` (lines ~52–65)

- [ ] **Step 1: Replace the hero section**

Find the section starting `<section class="min-h-screen flex items-center justify-center pt-20 pb-16 px-6 bg-gradient-to-b from-navy to-[#112240]">` and ending at its closing `</section>` (the block containing `<h1 ...>Your League,<br>Your Way</h1>` and `/images/hero-featured.png`).

Replace the entire section with:

```html
        <section class="min-h-screen flex items-center pt-24 pb-16 px-6 bg-gradient-to-b from-navy to-[#112240] overflow-hidden">
            <div class="max-w-6xl mx-auto grid lg:grid-cols-2 gap-12 lg:gap-8 items-center w-full">
                <div class="animate-fade-up">
                    <span class="inline-block bg-cyan/10 border border-cyan/20 text-cyan text-xs font-semibold px-3 py-1 rounded-full mb-6">Now on the App Store</span>
                    <h1 class="text-5xl sm:text-6xl lg:text-7xl font-bold leading-[1.05] tracking-tight mb-6">Run the whole<br>season from<br><span class="text-cyan">one app.</span></h1>
                    <p class="text-lg text-slate-400 max-w-md mb-8">The easiest way to run a rec soccer league — and the best way to follow one. Schedules, fees, live scores, lineups, and predictions. No spreadsheets.</p>
                    <div class="flex flex-col sm:flex-row sm:items-center gap-4">
                        <a href="https://apps.apple.com/app/id6759442301" aria-label="Download Fanager on the App Store">
                            <img src="/images/badges/app-store-badge.svg" alt="Download on the App Store" class="h-12 w-auto">
                        </a>
                        <span class="text-gray-500 italic text-sm">Google Play coming soon</span>
                    </div>
                </div>
                <div class="flex justify-center lg:justify-end animate-slide-up relative">
                    <div class="absolute w-72 h-72 rounded-full bg-blue-500/20 blur-3xl -z-0"></div>
                    <img src="/images/screens/screen-my-leagues.png" alt="Fanager My Leagues screen showing followed leagues with live match cards" class="relative w-72 rounded-[2.5rem] border border-border-dark shadow-2xl shadow-blue-500/20">
                </div>
            </div>
        </section>
```

- [ ] **Step 2: Verify hero renders with the new screenshot and badge**

Run:
```bash
grep -n "screen-my-leagues.png" index.html
grep -n "app-store-badge.svg" index.html | head -1
open index.html
```
Expected: both grep hits present. In the browser: headline "Run the whole season from one app.", an App Store badge, "Google Play coming soon" text, and the My Leagues phone screenshot with a glow. No reference to `hero-featured.png`.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "hero: Bold Editorial redesign with App Store badge + new screenshot"
```

---

### Task 5: Stat strip — iOS now / Android soon

**Files:**
- Modify: `index.html` social-proof bar (lines ~67–78)

- [ ] **Step 1: Replace the stat strip inner content**

Find:

```html
            <div class="max-w-4xl mx-auto flex justify-center gap-12 sm:gap-16 text-center reveal">
                <div>
                    <span class="text-green-500 font-bold text-lg">Free</span>
                    <span class="text-slate-400 text-sm ml-1">to use</span>
                </div>
                <div>
                    <span class="text-cyan font-bold text-lg">iOS + Android</span>
                </div>
            </div>
```

Replace with:

```html
            <div class="max-w-4xl mx-auto flex justify-center gap-12 sm:gap-16 text-center reveal">
                <div>
                    <span class="text-green-500 font-bold text-lg">Free</span>
                    <span class="text-slate-400 text-sm ml-1">to use</span>
                </div>
                <div>
                    <span class="text-cyan font-bold text-lg">iOS now</span>
                    <span class="text-slate-400 text-sm ml-1">· Android soon</span>
                </div>
            </div>
```

- [ ] **Step 2: Verify**

Run: `grep -nEi "iOS \+ Android" index.html || echo "CLEAN"`
Expected: `CLEAN`.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "content: stat strip iOS-now / Android-soon"
```

---

### Task 6: Shared act styles (Bold Editorial scaffolding)

**Files:**
- Modify: `index.html` `<style>` block (near the end, lines ~453–465)

- [ ] **Step 1: Append reusable act + screenshot-frame CSS**

In the existing `<style>` block, immediately AFTER the line
`#league-owners, #team-managers, #fans { scroll-margin-top: 140px; }`
add:

```css
        #players { scroll-margin-top: 140px; }
        .act { padding: 6rem 1.5rem; }
        .act-num { font-size: 5rem; font-weight: 800; line-height: 1; opacity: 0.12; }
        .act-rule { width: 56px; height: 4px; border-radius: 999px; margin: 0.75rem 0 1.25rem; }
        .shot { border-radius: 2rem; border: 1px solid #1F2937; }
        .shot-blue   { box-shadow: 0 25px 60px -15px rgba(59,130,246,0.35); }
        .shot-green  { box-shadow: 0 25px 60px -15px rgba(34,197,94,0.35); }
        .shot-cyan   { box-shadow: 0 25px 60px -15px rgba(6,182,212,0.35); }
        .shot-amber  { box-shadow: 0 25px 60px -15px rgba(245,158,11,0.35); }
```

- [ ] **Step 2: Verify the CSS is inside the `<style>` block**

Run: `grep -n "act-rule" index.html`
Expected: one hit, located before `</style>`.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "style: add reusable Bold Editorial act + screenshot-frame CSS"
```

---

### Task 7: Act I — League Owners (blue)

**Files:**
- Modify: `index.html` — replace the existing `<section id="league-owners" ...>` block (lines ~183–226)

- [ ] **Step 1: Replace the League Owners section**

Find the entire `<section id="league-owners" class="role-section py-20 px-6">` … matching `</section>` block and replace it with:

```html
        <section id="league-owners" class="role-section act">
            <div class="max-w-6xl mx-auto grid lg:grid-cols-2 gap-12 items-center reveal">
                <div>
                    <div class="act-num text-blue-500">I</div>
                    <h2 class="text-3xl sm:text-4xl font-bold text-blue-500">League Owners</h2>
                    <div class="act-rule bg-blue-500"></div>
                    <p class="text-slate-300 text-lg mb-6">Stand up a season in minutes and let Fanager run the schedule, the money, and the table.</p>
                    <ul class="space-y-3 text-slate-400">
                        <li>• Step-by-step league &amp; season setup wizard</li>
                        <li>• Auto-generate round-robin and tournament schedules with visual brackets</li>
                        <li>• Game days, time slots, and blackout dates</li>
                        <li>• Collect team fees via Stripe (flat rate or per-player)</li>
                        <li>• Record scores; live standings with a Goal Difference column</li>
                        <li>• Approve team join requests and manage the season</li>
                    </ul>
                </div>
                <div class="flex justify-center gap-4">
                    <img src="/images/screens/screen-create-league.png" alt="Fanager create-league setup wizard" class="w-56 shot shot-blue">
                    <img src="/images/screens/screen-standings.png" alt="League standings table with Goal Difference column" class="w-56 shot shot-blue mt-12">
                </div>
            </div>
        </section>
```

- [ ] **Step 2: Verify**

Run:
```bash
grep -n 'id="league-owners"' index.html
grep -n "screen-create-league.png\|screen-standings.png" index.html
open index.html
```
Expected: section present; both screenshots referenced; in browser the blue act shows the two phones, accent rule, and 6 bullets. No `role-owners.png` reference remains in this section.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "content: Act I League Owners — Bold Editorial + new screenshots"
```

---

### Task 8: Act II — Team Managers (green)

**Files:**
- Modify: `index.html` — replace `<section id="team-managers" ...>` block (lines ~229–260)

- [ ] **Step 1: Replace the Team Managers section**

Find the entire `<section id="team-managers" class="role-section py-20 px-6 bg-[#0d1b30]">` … `</section>` block and replace with (note the screenshot column is FIRST here for the alternating asymmetric layout):

```html
        <section id="team-managers" class="role-section act bg-[#0d1b30]">
            <div class="max-w-6xl mx-auto grid lg:grid-cols-2 gap-12 items-center reveal">
                <div class="flex justify-center gap-4 order-2 lg:order-1">
                    <img src="/images/screens/screen-team-lineup.png" alt="Lineup builder with players on a pitch and a captain" class="w-56 shot shot-green">
                    <img src="/images/screens/screen-score-entry.png" alt="Match score-entry sheet" class="w-56 shot shot-green mt-12">
                </div>
                <div class="order-1 lg:order-2">
                    <div class="act-num text-green-500">II</div>
                    <h2 class="text-3xl sm:text-4xl font-bold text-green-500">Team Managers</h2>
                    <div class="act-rule bg-green-500"></div>
                    <p class="text-slate-300 text-lg mb-6">Run your squad end to end — roster, lineups, practices, and RSVPs in one place.</p>
                    <ul class="space-y-3 text-slate-400">
                        <li>• Roster management; edit player names &amp; jerseys with duplicate-number protection</li>
                        <li>• Upload a custom team logo</li>
                        <li>• Lineups builder — up to 10 lineups per team, pick captains, assign to any match in a tap</li>
                        <li>• Schedule recurring practices; track per-player RSVPs</li>
                        <li>• See who's Going / Maybe / Not Going / no-reply (Match RSVP)</li>
                        <li>• Request to join seasons, pay fees in-app</li>
                        <li>• Next Match, Last 5, and full match history</li>
                        <li>• Hand a team off to a new manager by email or 6-digit claim code</li>
                    </ul>
                </div>
            </div>
        </section>
```

- [ ] **Step 2: Verify**

Run:
```bash
grep -n "screen-team-lineup.png\|screen-score-entry.png" index.html
open index.html
```
Expected: both screenshots referenced; in browser the green act renders with screenshots on the left (desktop), 8 bullets including Lineups/Practices/handoff. No `role-managers.png` reference remains.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "content: Act II Team Managers — lineups, practices, handoff"
```

---

### Task 9: Act III — Players (cyan, NEW section)

**Files:**
- Modify: `index.html` — insert a new `<section id="players">` immediately AFTER the closing `</section>` of `#team-managers` and BEFORE `<section id="fans" ...>`

- [ ] **Step 1: Insert the new Players section**

Directly after the `#team-managers` section's closing `</section>`, insert:

```html
        <section id="players" class="role-section act">
            <div class="max-w-6xl mx-auto grid lg:grid-cols-2 gap-12 items-center reveal">
                <div>
                    <div class="act-num text-cyan">III</div>
                    <h2 class="text-3xl sm:text-4xl font-bold text-cyan">Players</h2>
                    <div class="act-rule bg-cyan"></div>
                    <p class="text-slate-300 text-lg mb-6">Know where you stand for every match and practice — without the group-chat noise.</p>
                    <ul class="space-y-3 text-slate-400">
                        <li>• RSVP every match and practice in one tap</li>
                        <li>• See your starting lineup published 30 minutes before kickoff</li>
                        <li>• Get notified when your lineup is set for a match</li>
                    </ul>
                </div>
                <div class="flex justify-center">
                    <img src="/images/screens/screen-schedule-rsvp.png" alt="Player schedule with match and practice RSVP controls" class="w-60 shot shot-cyan">
                </div>
            </div>
        </section>
```

- [ ] **Step 2: Verify the new section exists and is ordered correctly**

Run:
```bash
grep -n 'id="team-managers"\|id="players"\|id="fans"' index.html
```
Expected: line numbers in this order — `team-managers` < `players` < `fans`.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "content: add Act III Players section (new audience)"
```

---

### Task 10: Act IV — Fans (amber)

**Files:**
- Modify: `index.html` — replace `<section id="fans" ...>` block (lines ~263–290)

- [ ] **Step 1: Replace the Fans section**

Find the entire `<section id="fans" class="role-section py-20 px-6">` … `</section>` block and replace with (screenshot column first for alternation, since Players ended text-left):

```html
        <section id="fans" class="role-section act bg-[#0d1b30]">
            <div class="max-w-6xl mx-auto grid lg:grid-cols-2 gap-12 items-center reveal">
                <div class="flex justify-center gap-4 order-2 lg:order-1">
                    <img src="/images/screens/screen-following.png" alt="Following tab with favorite teams and leagues" class="w-56 shot shot-amber">
                    <img src="/images/screens/screen-game-prediction.png" alt="Match prediction with animated result reveal" class="w-56 shot shot-amber mt-12">
                </div>
                <div class="order-1 lg:order-2">
                    <div class="act-num text-amber-500">IV</div>
                    <h2 class="text-3xl sm:text-4xl font-bold text-amber-500">Fans</h2>
                    <div class="act-rule bg-amber-500"></div>
                    <p class="text-slate-300 text-lg mb-6">Follow every team you care about and call the result before kickoff.</p>
                    <ul class="space-y-3 text-slate-400">
                        <li>• Browse leagues, teams, and standings without an account</li>
                        <li>• Follow your favorite teams and leagues</li>
                        <li>• Live scores and standings on match days</li>
                        <li>• Lock in match Predictions before kickoff, with an animated reveal at full time</li>
                        <li>• Push notifications for updates on teams you follow</li>
                    </ul>
                </div>
            </div>
        </section>
```

- [ ] **Step 2: Verify**

Scope the old-image check to the Fans section ONLY — `role-owners.png` legitimately still exists elsewhere (the Key-Features showcase) until Task 12; a whole-file grep here would false-fail.

```bash
grep -n "screen-following.png\|screen-game-prediction.png" index.html
# slice just the #fans section and confirm no old role image inside it:
awk '/<section id="fans"/,/<\/section>/' index.html | grep -E "role-fans\.png|role-owners\.png|role-managers\.png" && echo "OLD IMG STILL IN FANS — FIX" || echo "FANS SECTION CLEAN"
open index.html
```
Expected: both new screenshots referenced; `FANS SECTION CLEAN`; amber act renders correctly. (The authoritative whole-file old-image gate is Task 12 Step 3, after the showcase images are repointed.)

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "content: Act IV Fans — live scores + predictions"
```

---

### Task 11: Sticky audience nav — 3 → 4 tabs

**Files:**
- Modify: `index.html` sticky tab switcher markup (lines ~174–180), CSS scroll-margin (Task 6 added `#players` — verify), JS `allColors` + scroll-spy (lines ~507–543)

- [ ] **Step 1: Add the Players tab button**

Find:

```html
            <div class="max-w-4xl mx-auto flex justify-center gap-2">
                <button class="role-tab px-5 py-2 rounded-full text-sm font-semibold bg-blue-500 text-white transition-colors" data-target="league-owners" data-color="bg-blue-500">League Owners</button>
                <button class="role-tab px-5 py-2 rounded-full text-sm font-semibold bg-gray-700 text-gray-400 transition-colors" data-target="team-managers" data-color="bg-green-500">Team Managers</button>
                <button class="role-tab px-5 py-2 rounded-full text-sm font-semibold bg-gray-700 text-gray-400 transition-colors" data-target="fans" data-color="bg-amber-500">Fans</button>
            </div>
```

Replace with:

```html
            <div class="max-w-4xl mx-auto flex flex-wrap justify-center gap-2">
                <button class="role-tab px-5 py-2 rounded-full text-sm font-semibold bg-blue-500 text-white transition-colors" data-target="league-owners" data-color="bg-blue-500">League Owners</button>
                <button class="role-tab px-5 py-2 rounded-full text-sm font-semibold bg-gray-700 text-gray-400 transition-colors" data-target="team-managers" data-color="bg-green-500">Team Managers</button>
                <button class="role-tab px-5 py-2 rounded-full text-sm font-semibold bg-gray-700 text-gray-400 transition-colors" data-target="players" data-color="bg-cyan">Players</button>
                <button class="role-tab px-5 py-2 rounded-full text-sm font-semibold bg-gray-700 text-gray-400 transition-colors" data-target="fans" data-color="bg-amber-500">Fans</button>
            </div>
```

- [ ] **Step 2: Update the JS `allColors` array**

Find:

```javascript
        const allColors = ['bg-blue-500', 'bg-green-500', 'bg-amber-500'];
```

Replace with:

```javascript
        const allColors = ['bg-blue-500', 'bg-green-500', 'bg-cyan', 'bg-amber-500'];
```

- [ ] **Step 3: Verify scroll-spy + CSS already cover `#players`**

The scroll-spy uses `document.querySelectorAll('.role-section')` and `setActiveTab(entry.target.id)`, so the new `<section id="players" class="role-section ...">` is picked up automatically — no observer list edit needed. Confirm:

Run:
```bash
grep -n "querySelectorAll('.role-section')" index.html
grep -n "#players { scroll-margin-top" index.html
grep -n "bg-cyan'" index.html
```
Expected: the `.role-section` querySelectorAll line exists (auto-covers Players), the `#players` scroll-margin rule from Task 6 exists, and `allColors` now contains `'bg-cyan'`.

- [ ] **Step 4: Verify in browser**

Run: `open index.html`
Expected: 4 sticky tabs (League Owners / Team Managers / Players / Fans). Scrolling through each act highlights the matching tab; clicking a tab smooth-scrolls to its section. The Players tab turns cyan when active; others are unaffected.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "nav: extend sticky audience switcher to 4 tabs (add Players)"
```

---

### Task 12: Key-features showcase + Role-highlights cards — refresh screenshots & copy

**Files:**
- Modify: `index.html` — "Role Highlights" grid (lines ~81–108) and "Key Features Showcase" block (lines ~110–171)

- [ ] **Step 1: Update the 3 role-highlight cards to 4 and fix anchors**

Find this EXACT block (the full grid `<div>` with its three `<a>` cards and the grid's closing `</div>` — copy it verbatim as the match anchor so there is no ambiguity about which nested `</div>` ends the grid):

```html
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6 reveal">
                    <a href="#league-owners" class="block bg-card-dark border border-border-dark rounded-xl p-6 hover:border-blue-500/50 transition-colors group">
                        <div class="w-10 h-10 bg-blue-500/10 rounded-lg flex items-center justify-center mb-4">
                            <svg class="w-5 h-5 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 9c0 5.591 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.042-.133-2.052-.382-3.016z"/></svg>
                        </div>
                        <h3 class="text-lg font-semibold text-blue-500 mb-2">For League Owners</h3>
                        <p class="text-slate-400 text-sm">Create leagues, collect fees, manage schedules</p>
                    </a>
                    <a href="#team-managers" class="block bg-card-dark border border-border-dark rounded-xl p-6 hover:border-green-500/50 transition-colors group">
                        <div class="w-10 h-10 bg-green-500/10 rounded-lg flex items-center justify-center mb-4">
                            <svg class="w-5 h-5 text-green-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0z"/></svg>
                        </div>
                        <h3 class="text-lg font-semibold text-green-500 mb-2">For Team Managers</h3>
                        <p class="text-slate-400 text-sm">Manage rosters, pay fees, track performance</p>
                    </a>
                    <a href="#fans" class="block bg-card-dark border border-border-dark rounded-xl p-6 hover:border-amber-500/50 transition-colors group">
                        <div class="w-10 h-10 bg-amber-500/10 rounded-lg flex items-center justify-center mb-4">
                            <svg class="w-5 h-5 text-amber-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z"/></svg>
                        </div>
                        <h3 class="text-lg font-semibold text-amber-500 mb-2">For Fans</h3>
                        <p class="text-slate-400 text-sm">Follow teams, check scores, browse freely</p>
                    </a>
                </div>
```

Replace that entire block with this 4-card grid:

```html
                <div class="grid grid-cols-2 lg:grid-cols-4 gap-4 reveal">
                    <a href="#league-owners" class="block bg-card-dark border border-border-dark rounded-xl p-5 hover:border-blue-500/50 transition-colors">
                        <h3 class="text-base font-semibold text-blue-500 mb-1">League Owners</h3>
                        <p class="text-slate-400 text-sm">Create leagues, collect fees, run the schedule</p>
                    </a>
                    <a href="#team-managers" class="block bg-card-dark border border-border-dark rounded-xl p-5 hover:border-green-500/50 transition-colors">
                        <h3 class="text-base font-semibold text-green-500 mb-1">Team Managers</h3>
                        <p class="text-slate-400 text-sm">Rosters, lineups, practices, RSVPs</p>
                    </a>
                    <a href="#players" class="block bg-card-dark border border-border-dark rounded-xl p-5 hover:border-cyan/50 transition-colors">
                        <h3 class="text-base font-semibold text-cyan mb-1">Players</h3>
                        <p class="text-slate-400 text-sm">RSVP and see your starting lineup</p>
                    </a>
                    <a href="#fans" class="block bg-card-dark border border-border-dark rounded-xl p-5 hover:border-amber-500/50 transition-colors">
                        <h3 class="text-base font-semibold text-amber-500 mb-1">Fans</h3>
                        <p class="text-slate-400 text-sm">Follow teams, live scores, predictions</p>
                    </a>
                </div>
```

- [ ] **Step 2: Repoint the 4 "Key Features Showcase" mockup images to new screenshots**

In the `<section id="features" ...>` block there are 4 feature rows referencing `/images/feature-schedule.png`, `/images/role-owners.png`, `/images/feature-standings.png`, `/images/feature-brackets.png`. Replace each `<img ...>` `src` and `alt` as follows (leave the surrounding text/markup intact):

- `feature-schedule.png` → `src="/images/screens/screen-create-league.png"` `alt="Create-league setup wizard"`
- `role-owners.png` → `src="/images/screens/screen-score-entry.png"` `alt="Match score-entry sheet"`
- `feature-standings.png` → `src="/images/screens/screen-standings.png"` `alt="Live standings with Goal Difference"`
- `feature-brackets.png` → `src="/images/screens/screen-game-prediction.png"` `alt="Match prediction with animated reveal"`

- [ ] **Step 3: Verify no orphaned old images are referenced anywhere**

Run:
```bash
grep -nE "feature-schedule|feature-standings|feature-brackets|hero-featured|role-owners\.png|role-managers\.png|role-fans\.png" index.html || echo "NO OLD IMAGE REFS"
```
Expected: `NO OLD IMAGE REFS`.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "content: 4-audience highlight cards + new feature-showcase screenshots"
```

---

### Task 13: FAQ — 7 questions (visible accordion)

**Files:**
- Modify: `index.html` FAQ `#faq-list` block (lines ~354–409)

- [ ] **Step 1: Insert the new Q7 after "What sports does Fanager support?"**

Locate the FAQ item whose question is `What sports does Fanager support?` (its `</div>` closing the `.faq-item`). Immediately after that item's closing `</div>`, insert this new item:

```html
                    <div class="faq-item border border-border-dark rounded-xl overflow-hidden">
                        <button class="faq-toggle w-full text-left px-6 py-4 flex items-center justify-between hover:bg-card-dark/50 transition-colors" onclick="toggleFaq(this)">
                            <span class="font-semibold">Can I build lineups and schedule practices?</span>
                            <svg class="w-5 h-5 text-slate-400 transition-transform duration-300 faq-icon" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"/></svg>
                        </button>
                        <div class="faq-answer max-h-0 overflow-hidden transition-all duration-300">
                            <p class="px-6 pb-4 text-slate-400 text-sm leading-relaxed">Yes. Team managers can build up to 10 lineups per team, pick captains, and assign one to any match. You can also schedule recurring practices and collect RSVPs from players for both matches and practices.</p>
                        </div>
                    </div>
```

- [ ] **Step 2: Revise the "How do I get started?" answer (Android-soon)**

Find the answer paragraph for `How do I get started?`:

```html
                            <p class="px-6 pb-4 text-slate-400 text-sm leading-relaxed">Download Fanager from the App Store or Google Play. You can browse as a guest immediately, or create an account with Apple, Google, or email to start your first league.</p>
```

Replace with:

```html
                            <p class="px-6 pb-4 text-slate-400 text-sm leading-relaxed">Download Fanager from the App Store (Android coming soon). You can browse as a guest immediately, or create an account with Apple, Google, or email to start your first league.</p>
```

- [ ] **Step 3: Verify exactly 7 visible FAQ items and the Q6 answer was revised**

Scope the Android-wording check to the **visible accordion** (`#faq-list`) only — the JSON-LD `<script>` still contains the old "App Store or Google Play" string until Task 14, so a whole-file grep here would false-fail. That's expected and is fixed in Task 14; the whole-file sweep is Task 16.

```bash
grep -c 'class="faq-item' index.html
# Slice ONLY the visible accordion: from #faq-list up to (not including) the
# "FAQPage JSON-LD" comment that precedes the JSON-LD <script>.
awk '/id="faq-list"/{f=1} /FAQPage JSON-LD/{f=0} f' index.html | grep -i "App Store or Google Play" && echo "OLD Q6 STILL VISIBLE — FIX" || echo "VISIBLE FAQ CLEAN"
```
Expected: count is `7`; `VISIBLE FAQ CLEAN`. (The `awk` window starts at `id="faq-list"` and closes at the `<!-- FAQPage JSON-LD ... -->` comment, so it covers the visible accordion only — the JSON-LD block, still carrying the old wording until Task 14, is deliberately excluded.)

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "content: FAQ — add lineups/practices Q, iOS-only get-started answer"
```

---

### Task 14: FAQ JSON-LD — sync to 7 entities

**Files:**
- Modify: `index.html` FAQPage JSON-LD `<script type="application/ld+json">` (lines ~414–427)

> **Spec requirement (lines 184-185):** the JSON-LD answer text must match
> the **visible accordion answers verbatim**. The current visible answers for
> Q3/Q4/Q5 are LONGER than the old shortened JSON-LD answers, and Task 13 does
> NOT shorten the visible answers — so the JSON-LD below uses the full visible
> strings (Q5's `<a>Privacy Policy</a>` becomes the plain text "Privacy
> Policy"). Do not re-shorten them.

- [ ] **Step 1: Replace the ENTIRE JSON-LD `<script>` block**

Locate the full element from `<script type="application/ld+json">` through its
matching `</script>` (the one whose body contains `"@type": "FAQPage"`) and
replace the **whole element, opening and closing tags included**, with exactly
this block (do not nest or keep the old tags):

```html
        <script type="application/ld+json">
        {
          "@context": "https://schema.org",
          "@type": "FAQPage",
          "mainEntity": [
            {"@type": "Question", "name": "Is Fanager free?", "acceptedAnswer": {"@type": "Answer", "text": "Yes, Fanager is completely free to use. League owners can optionally collect team fees through built-in Stripe payments, but using the app itself costs nothing."}},
            {"@type": "Question", "name": "What sports does Fanager support?", "acceptedAnswer": {"@type": "Answer", "text": "Fanager is currently focused on recreational soccer (futbol) leagues. Support for additional sports may be added in the future."}},
            {"@type": "Question", "name": "Can I build lineups and schedule practices?", "acceptedAnswer": {"@type": "Answer", "text": "Yes. Team managers can build up to 10 lineups per team, pick captains, and assign one to any match. You can also schedule recurring practices and collect RSVPs from players for both matches and practices."}},
            {"@type": "Question", "name": "How do payments work?", "acceptedAnswer": {"@type": "Answer", "text": "League owners connect their Stripe account and set per-season fees — either a flat rate or per-player pricing. Team managers pay directly in the app. League owners can track payments, issue refunds, and manage billing for each season."}},
            {"@type": "Question", "name": "Do I need an account to browse?", "acceptedAnswer": {"@type": "Answer", "text": "No. Guests can browse all leagues, teams, and standings without creating an account. You only need to sign up to follow teams, create leagues, or manage a team."}},
            {"@type": "Question", "name": "Is my data safe?", "acceptedAnswer": {"@type": "Answer", "text": "Yes. Fanager includes automatic content moderation, profanity filtering, image moderation, and user reporting and blocking. See our Privacy Policy for full details."}},
            {"@type": "Question", "name": "How do I get started?", "acceptedAnswer": {"@type": "Answer", "text": "Download Fanager from the App Store (Android coming soon). You can browse as a guest immediately, or create an account with Apple, Google, or email to start your first league."}}
          ]
        }
        </script>
```

Note the em dash `—` in the payments answer and the literal phrase "See our
Privacy Policy for full details." in the data answer — these are taken verbatim
from the current visible answers (`index.html` "How do payments work?" and "Is
my data safe?") so the two stay in sync.

- [ ] **Step 2: Validate the JSON-LD parses, has 7 entities, and exactly one script block**

Run:
```bash
python3 - <<'PY'
import re, json, pathlib
html = pathlib.Path("index.html").read_text()
blocks = re.findall(r'<script type="application/ld\+json">(.*?)</script>', html, re.S)
assert len(blocks) == 1, f"expected exactly 1 ld+json block, found {len(blocks)}"
data = json.loads(blocks[0])
assert data["@type"] == "FAQPage"
print("entities:", len(data["mainEntity"]))
PY
```
Expected: no exception; `entities: 7` (the `len(blocks) == 1` assertion catches the "accidentally nested / duplicated `<script>` tags" failure mode).

- [ ] **Step 2b: Verify each JSON-LD answer matches its visible accordion answer verbatim**

Run:
```bash
python3 - <<'PY'
import re, json, pathlib, html as ht
src = pathlib.Path("index.html").read_text()
ld = json.loads(re.search(r'<script type="application/ld\+json">(.*?)</script>', src, re.S).group(1))
# visible answers: the <p class="px-6 pb-4 ...">...</p> blocks inside #faq-list
vis_html = re.search(r'id="faq-list"(.*?)<!-- FAQPage JSON-LD', src, re.S).group(1)
vis = re.findall(r'<p class="px-6 pb-4[^"]*">(.*?)</p>', vis_html, re.S)
def norm(s):
    s = re.sub(r'<a [^>]*>(.*?)</a>', r'\1', s)   # strip the Privacy Policy link, keep its text
    s = re.sub(r'\s+', ' ', s).strip()
    return ht.unescape(s)
vis = [norm(v) for v in vis]
ldans = [norm(q["acceptedAnswer"]["text"]) for q in ld["mainEntity"]]
assert len(vis) == 7, f"expected 7 visible answers, got {len(vis)}"
for i,(a,b) in enumerate(zip(vis, ldans)):
    assert a == b, f"MISMATCH #{i+1}\n visible: {a!r}\n json-ld: {b!r}"
print("all 7 visible answers match JSON-LD verbatim")
PY
```
Expected: `all 7 visible answers match JSON-LD verbatim`. If it reports a MISMATCH, make the JSON-LD `text` exactly equal the visible answer (normalized) — the visible accordion is the source of truth; do not edit the visible answers here.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "seo: sync FAQPage JSON-LD to 7 entities, iOS-only wording"
```

---

### Task 15: Final CTA + footer

**Files:**
- Modify: `index.html` `<section id="download" ...>` (lines ~430–438); footer untouched

- [ ] **Step 1: Replace the final CTA inner content**

Find:

```html
            <div class="max-w-3xl mx-auto text-center reveal">
                <h2 class="text-3xl sm:text-4xl font-bold mb-4">Ready to Run Your League?</h2>
                <p class="text-slate-400 mb-8">Download Fanager for free on iOS and Android.</p>
                <div class="flex flex-col sm:flex-row gap-4 justify-center">
                    <span class="text-gray-500 italic text-sm">Coming soon to the App Store and Google Play</span>
                </div>
            </div>
```

Replace with:

```html
            <div class="max-w-3xl mx-auto text-center reveal">
                <h2 class="text-3xl sm:text-4xl font-bold mb-4">Ready to Run Your League?</h2>
                <p class="text-slate-400 mb-8">Download Fanager free on iOS. Android coming soon.</p>
                <div class="flex flex-col sm:flex-row gap-4 justify-center items-center">
                    <a href="https://apps.apple.com/app/id6759442301" aria-label="Download Fanager on the App Store">
                        <img src="/images/badges/app-store-badge.svg" alt="Download on the App Store" class="h-12 w-auto">
                    </a>
                    <span class="text-gray-500 italic text-sm">Google Play coming soon</span>
                </div>
            </div>
```

- [ ] **Step 2: Verify**

Run:
```bash
grep -nEi "iOS and Android|Coming soon to the App Store" index.html || echo "CLEAN"
grep -c "apps.apple.com/app/id6759442301" index.html
```
Expected: `CLEAN`; the App Store URL count is `3` (nav, hero, final CTA).

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "cta: final download section — App Store badge, Android soon"
```

---

### Task 16: Android-language sweep (whole-file gate)

**Files:**
- Modify: `index.html` (only if stragglers found)

- [ ] **Step 1: Grep the whole file for any remaining Android-available / coming-soon claims**

Run:
```bash
grep -nEi "iOS and Android|on iOS and Android|App Store or Google Play|Coming soon to" index.html || echo "ZERO ANDROID-AVAILABLE CLAIMS"
grep -nEi "android|google play" index.html
```
Expected: first command prints `ZERO ANDROID-AVAILABLE CLAIMS`. The second will still show the legitimate "Google Play coming soon" / "Android coming soon" muted phrases and the Play Store URL only if present — confirm every remaining hit is a *coming-soon* phrasing, never an *available-now* claim, and there is **no** clickable Google Play link/badge anywhere.

- [ ] **Step 2: If any straggler is an "available" claim, fix it** to the "Android coming soon" phrasing consistent with Tasks 2/5/13/15, then re-run Step 1 until it prints `ZERO ANDROID-AVAILABLE CLAIMS`.

- [ ] **Step 3: Commit (only if changes were made)**

```bash
git add index.html
git commit -m "content: final Android-available language sweep"
```
If no changes were needed, skip the commit and note "no stragglers" in the task log.

---

### Task 17: Full verification pass

**Files:**
- Modify: `index.html` (only if a check fails)

- [ ] **Step 1: Broken-reference + asset check**

Run:
```bash
cd /Users/qubaldo/Development/fanager-website
for p in $(grep -oE '/images/[A-Za-z0-9/_-]+\.(png|svg|jpg)' index.html | sort -u); do
  f=".${p}"; [ -f "$f" ] && echo "OK  $p" || echo "MISSING $p";
done
```
Expected: every line starts with `OK`. Any `MISSING` is a blocker — fix the path or restore the asset before continuing.

- [ ] **Step 2: Browser walkthrough**

Run: `open index.html`
Confirm visually:
- Hero: headline, App Store badge (click → opens `apps.apple.com/app/id6759442301`), "Google Play coming soon", My Leagues phone with glow.
- Four acts in order I→II→III→IV with correct accent colors (blue/green/cyan/amber), alternating screenshot/text sides, all 8 screenshots load crisp.
- Sticky tab bar has 4 tabs; scroll-spy highlights the right tab; clicking scrolls.
- FAQ shows 7 questions; accordion opens/closes; the new lineups/practices Q is present.
- Final CTA shows App Store badge + "Google Play coming soon".

- [ ] **Step 3: Responsive + reduced-motion**

In the browser, use devtools responsive mode at 375px, 768px, 1280px widths. Confirm: acts stack cleanly on mobile (screenshot below text), no horizontal overflow, sticky tabs wrap rather than overflow. Then in devtools enable "prefers-reduced-motion: reduce" and reload — confirm reveal animations are disabled (content visible immediately).

- [ ] **Step 4: Contrast spot-check**

Confirm the four accent heading colors are legible on the navy/`#0d1b30` backgrounds (blue `#3B82F6`, green `#22C55E`, cyan `#06B6D4`, amber `#F59E0B`). These are the existing brand tokens already used on the live site; if any act heading is hard to read at body distance, bump the heading weight to `font-bold` (do not change the token hex).

- [ ] **Step 5: Final JSON-LD re-validate**

Run:
```bash
python3 - <<'PY'
import re, json, pathlib
html = pathlib.Path("index.html").read_text()
m = re.search(r'<script type="application/ld\+json">(.*?)</script>', html, re.S)
data = json.loads(m.group(1))
assert data["@type"] == "FAQPage"
assert len(data["mainEntity"]) == 7, len(data["mainEntity"])
print("JSON-LD OK: 7 entities")
PY
```
Expected: `JSON-LD OK: 7 entities`.

- [ ] **Step 6: Commit any fixes + final state**

```bash
git add -A
git commit -m "verify: full website-refresh verification pass" || echo "nothing to commit"
```

---

## Deployment (manual, after plan completion — do NOT run during task execution)

Per spec, deployment is unchanged and out of band:

```bash
cd /Users/qubaldo/Development/fanager-website
firebase deploy --only hosting
```

Do not deploy as part of this plan unless the user explicitly asks — the plan's done-state is "committed locally and verified in a local browser."

---

## Self-Review (completed by plan author)

**Spec coverage:**
- Launch status iOS-live/Android-soon → Tasks 2, 3, 4, 5, 13, 15, 16 ✓
- 8 new screenshots into `images/screens/` → Task 1; referenced in 4, 7, 8, 9, 10, 12 ✓
- Bold Editorial hero → Task 4 ✓
- Stat strip → Task 5 ✓
- Act I/II/III(new)/IV with accent colors + alternating layout + screenshots → Tasks 7–10 ✓
- Shared screenshot-frame/act CSS → Task 6 ✓
- Sticky nav 3→4 tabs (markup + allColors + CSS scroll-margin + scroll-spy) → Task 6 (CSS) + Task 11 ✓
- Platform strip + How-it-works kept → untouched (no Android-available text in them; confirmed by Task 16 sweep) ✓
- FAQ 7 entities visible + JSON-LD → Tasks 13, 14 ✓
- Final CTA + footer → Task 15 (footer intentionally untouched) ✓
- Android-language inventory fully neutralized → Task 16 whole-file gate ✓
- SEO/meta/OG/Twitter → Task 2 ✓
- Old images orphaned not deleted → Tasks 7/8/10/12 verify no refs; files left on disk ✓
- Testing/verification (browser, responsive, reduced-motion, badge link, JSON-LD, contrast) → Task 17 ✓
- Non-goals respected: no legal/auth page edits, no build step, Firebase deploy left manual/out-of-band ✓

**Placeholder scan:** No TBD/TODO; every code step contains the full markup/JSON to paste.

**Type/name consistency:** Section ids consistent (`league-owners`, `team-managers`, `players`, `fans`); accent classes consistent (`bg-blue-500`/`bg-green-500`/`bg-cyan`/`bg-amber-500`); screenshot filenames identical between Task 1 map and every consuming task; `allColors` array order matches tab order.

No gaps found.
