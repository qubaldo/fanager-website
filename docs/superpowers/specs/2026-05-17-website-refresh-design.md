# Fanager Website Refresh — Design Spec

**Date:** 2026-05-17
**Status:** Approved (pending written-spec review)
**Scope:** `fanager-website/index.html` + `images/` assets

## Goal

Bring the public marketing site (fanager.app) up to date with the app's
1.1.0 feature set, replace dated March screenshots with fresh app
captures, and redesign the page into a "Bold Editorial" scroll story.
The current site is stuck on a pre-launch "Coming soon" message and is
missing every feature shipped since: Lineups, live scores, Predictions,
Practices + RSVP, team handoff, Goal Difference, and the player audience.

## Decisions (locked with user)

1. **Launch status: iOS live, Android coming soon.** Every "Coming soon"
   block becomes a live App Store badge + a muted "Google Play coming
   soon" note. App Store URL: `https://apps.apple.com/app/id6759442301`.
   Play Store URL (for when it launches):
   `https://play.google.com/store/apps/details?id=com.leaguemgr.league_mgr_app`.
2. **Scope: full content + visual refresh.** New screenshots, rewritten
   copy, added features, new Players audience, modernized layout.
3. **Visual direction: Bold Editorial.** Big display type, asymmetric
   sections, screenshot-led storytelling, per-audience accent color.
   Keeps the existing dark-navy brand tokens.

## Brand tokens (unchanged)

- `navy #0A1628`, `card-dark #111827`, `border-dark #1F2937`,
  `footer-bg #060d1a`, `cyan #06B6D4`
- Accents: Owners `blue #3B82F6`, Managers `green #22C55E`,
  Players `cyan #06B6D4`, Fans `amber #F59E0B`
- Font: Inter (400/600/700) via Google Fonts
- Tailwind via CDN (keep current setup — no build step)

## Assets

**Source screenshots** (clean, unframed device captures, 1170×2532):
`league-mgr-app/tools/appstore-screenshots/public/screenshots/raw/`
- `01-my-leagues-owner.png`
- `02-score-entry.png`
- `03-standings.png`
- `04-team-lineup.png`
- `05-schedule-rsvp.png`
- `06-game-detail-prediction.png`
- `07-following.png`
- `08-create-league.png`

Action: copy these 8 into `fanager-website/images/screens/` with
web-friendly names (e.g. `screen-my-leagues.png`). Use the **raw**
(unframed) versions — the site applies its own phone-frame/glow
treatment so baked-in marketing frames would double up. Old
`images/feature-*.png`, `role-*.png`, `hero-featured.png` are
superseded; leave on disk but stop referencing them. `og-banner.png`
stays (social card).

## Page structure (approved scroll story)

| # | Section | Change | Screenshot |
|---|---------|--------|-----------|
| 1 | Hero | Updated — display headline, live App Store badge, hero phone | 01 My Leagues |
| 2 | Stat strip | Keep — Free · iOS now · Android soon | — |
| 3 | Act I — League Owners (blue) | Keep+ — setup wizard, auto schedules, Stripe fees, live standings + Goal Difference, brackets, join requests | 08 Create League, 03 Standings |
| 4 | Act II — Team Managers (green) | Expanded — roster + jerseys, Lineups builder (up to 10, captains), recurring Practices + RSVP, team handoff by email/code, score entry | 04 Team Lineup, 02 Score Entry |
| 5 | Act III — Players (cyan) | New section — RSVP matches & practices, see starting XI 30 min pre-kickoff, notified when lineup is set | 05 Schedule & RSVP |
| 6 | Act IV — Fans (amber) | Expanded — browse free, follow, live scores, Predictions with reveal, push | 07 Following, 06 Game Prediction |
| 7 | Platform strip | Keep — sign-in, push, moderation, profanity, reporting, blocking, account deletion, free | — |
| 8 | How it works | Keep — 3 steps | — |
| 9 | FAQ | Updated — **7 total**: keep 6 (refresh answers), add 1 new Q (see FAQ section) | — |
| 10 | Final CTA + Footer | Updated — App Store badge live, Google Play soon; footer unchanged | — |

## Copy source of truth

Feature copy derives from three durable sources, in priority order:

1. The in-app release notes
   (`league-mgr-app/lib/core/constants/release_notes.dart`, v1.1.0).
2. Verified shipped features confirmed in the codebase (cited inline
   where the release notes are silent — e.g. recurring practices are
   backed by `league-management/database/practice_series.go` +
   migration `042_practice_schedules.sql` + Flutter
   `lib/core/models/practice.dart` `rrule`; the
   `lineup_assigned_to_game` push is in
   `league-management/database/team_members.go` and `core/core.go`).
3. The approved App Store description, pasted verbatim below so the
   implementer needs no prior-session context.

Do not invent features not present in one of these three.

### Approved App Store description (verbatim)

```
The easiest way to run a recreational soccer league. The best way to follow one.

Fanager takes the chaos out of league management. No more spreadsheets, group chats, or manual scheduling. Create your league, invite teams, collect fees, and let Fanager handle the rest — with live scores, lineups, and predictions baked in.

LEAGUE OWNERS
• Create leagues with a step-by-step setup wizard
• Auto-generate round-robin and tournament schedules with visual brackets
• Set game days, time slots, and blackout dates
• Collect team fees via Stripe (flat rate or per-player)
• Record scores and watch standings update live, with Goal Difference at a glance
• Approve team join requests and manage your season

TEAM MANAGERS
• Create and manage your team roster — edit player names and jerseys with duplicate-number protection
• Upload a custom team logo
• Build up to 10 lineups per team, pick captains, and assign one to any match in a tap
• Schedule recurring practices and track per-player attendance through the same RSVP system as matches
• See who's Going, Maybe, Not Going, or hasn't replied in the Match RSVP view
• Request to join league seasons and pay fees in-app
• Track Next Match, Last 5 results, and full match history
• Hand your team off to a new manager by email or 6-digit claim code when it's time

PLAYERS
• Lock in your RSVP for every match and practice in one tap
• See your starting lineup published 30 minutes before kickoff
• Get notified when your lineup is set for a match

FANS & SPECTATORS
• Browse leagues, teams, and standings without an account
• Follow your favorite teams and leagues
• Watch scores and standings update live during match days
• Lock in match Predictions before kickoff, then see the reveal animation when the final whistle blows
• Get push notifications for updates on teams you follow

Fanager is free to use. League owners can optionally collect fees from teams through built-in Stripe payments.
```

### Act content (bullets to render as editorial feature blocks)

**Act I — League Owners**
- Step-by-step league & season setup wizard
- Auto-generate round-robin and tournament schedules with visual brackets
- Game days, time slots, blackout dates
- Collect team fees via Stripe (flat rate or per-player)
- Record scores; live standings with a Goal Difference column
- Approve team join requests, manage the season

**Act II — Team Managers**
- Roster management; edit player names & jerseys with duplicate-number protection
- Custom team logo
- Lineups builder — up to 10 lineups per team, pick captains, assign to any match in a tap
- Schedule recurring practices; track per-player RSVPs (recurring practices verified: `practice_series.go` + migration 042)
- See who's Going / Maybe / Not Going / no-reply (Match RSVP)
- Request to join seasons, pay fees in-app
- Next Match, Last 5, full match history
- Hand a team off to a new manager by email or 6-digit claim code

**Act III — Players (new)**
- RSVP every match and practice in one tap
- See your starting lineup published 30 minutes before kickoff
- Get notified when your lineup is set for a match (verified: `lineup_assigned_to_game` push)

**Act IV — Fans**
- Browse leagues, teams, standings without an account
- Follow favorite teams and leagues
- Live scores and standings on match days
- Lock in match Predictions before kickoff, animated reveal at full time
- Push notifications for updates on teams you follow

### FAQ content (7 entities — visible + JSON-LD must match)

Keep and lightly refresh these 6 (update any Android-available wording):

1. Is Fanager free?
2. What sports does Fanager support?
3. How do payments work?
4. Do I need an account to browse?
5. Is my data safe?
6. How do I get started? — revise answer: App Store now, Android coming soon.

Add this 7th question (place after #2 "What sports"):

7. **Can I build lineups and schedule practices?** — "Yes. Team
   managers can build up to 10 lineups per team, pick captains, and
   assign one to any match. You can also schedule recurring practices
   and collect RSVPs from players for both matches and practices."

Both the visible accordion and the FAQPage JSON-LD `mainEntity` array
must contain exactly these 7, with answer text matching verbatim.

## Bold Editorial layout notes

- **Hero:** oversized headline (text-6xl→7xl), short pitch line, App
  Store badge (links to App Store URL) + small muted "Google Play
  coming soon", single hero phone with soft radial glow.
- **Acts:** alternating asymmetric layouts (text/screenshot swap sides
  each act), large act number or label, accent-colored heading and a
  thin accent rule. Each act: a lead statement + 3–6 feature lines +
  1–2 phone screenshots in a lightweight frame (rounded, subtle border,
  accent-tinted glow matching the act).
- **Sticky audience nav:** keep the existing sticky tab switcher,
  extend to 4 tabs (Owners, Managers, Players, Fans) with scroll-spy.
  Implementation note — the current file hard-codes 3 sections in
  three places that ALL must be updated for the 4th:
  1. CSS `scroll-margin-top` rule (currently
     `#league-owners, #team-managers, #fans`) — add `#players`.
  2. JS `allColors` array (currently 3 accent classes) — add the
     Players cyan accent.
  3. The scroll-spy `IntersectionObserver` section list + the
     `data-target`/`data-color` markup on the tab buttons — add the
     Players tab + `#players` section id.
  Use section id `#players`; Players accent = `cyan #06B6D4`. Note
  `cyan` is also a brand token used elsewhere — ensure the Players
  accent styling is scoped so it doesn't restyle unrelated cyan UI.
- **Screenshot frame:** consistent reusable treatment — `rounded-[2rem]`,
  `border border-border-dark`, accent-tinted `shadow`. No real device
  bezel images; CSS frame only.
- **Motion:** keep existing reveal-on-scroll IntersectionObserver and
  `prefers-reduced-motion` guard. No new JS libraries.
- **Accessibility:** every screenshot needs a descriptive `alt`;
  maintain heading order; AA contrast on accent text.

## Android-available language inventory (must all change)

The current `index.html` makes the app sound available on Android in
several places beyond the visible "Coming soon" blocks. iOS-only launch
means **every** one of these must change to "iOS now, Android coming
soon" framing (not just the obvious "Coming soon" spans). Inventory
against the current file:

| Approx. line | Current text | Change to |
|---|---|---|
| ~7 `<meta name="description">` | "...Free on iOS and Android." | "...Free on iOS. Android coming soon." |
| ~9 `og:description` | "...Free on iOS and Android." | same as above |
| ~13–14 `twitter:*` | (verify; align if it claims Android) | match new description |
| ~45–46 nav | "Coming soon to App Store & Google Play" | App Store link/badge; drop nav coming-soon line or shorten to "Now on iOS" |
| ~58 hero | "Coming soon to the App Store and Google Play" | App Store badge + muted "Google Play coming soon" |
| ~75 stat strip | "iOS + Android" | "iOS now · Android soon" |
| ~406 FAQ "How do I get started?" | "Download Fanager from the App Store or Google Play." | "Download Fanager from the App Store (Android coming soon)." |
| ~424 JSON-LD answer | "...App Store or Google Play..." | match the revised FAQ answer text exactly |
| ~433 final CTA | "Download Fanager for free on iOS and Android." | "Download Fanager free on iOS. Android coming soon." |
| ~435 final CTA | "Coming soon to the App Store and Google Play" | App Store badge + muted "Google Play coming soon" |

Line numbers are approximate (the file is being restructured); the
inventory is by **content**, not line. The implementer must grep the
final file for `Android`, `Google Play`, and `Coming soon` and confirm
zero "available on Android" claims remain.

## SEO / meta

- Update `<title>` / meta description / OG / Twitter copy to drop
  "Coming soon" framing, reflect iOS-live + Android-soon, and mention
  lineups, live scores, predictions.
- Update the FAQPage JSON-LD: it must stay in sync with the rendered
  FAQ — **7 question entities**, answer text matching the visible
  answers verbatim (including the revised Android wording and the new
  lineups/practices question).
- `sitemap.xml` / `robots.txt` unchanged (single page).

## Constraints / non-goals

- Single static `index.html`; no build step, no framework, Tailwind CDN.
- Do not touch auth/legal pages (`verify-email.html`,
  `reset-password.html`, `claim.html`, `join-community.html`,
  `privacy-policy/`, `terms-of-service/`, `share.html`).
- No Android download link until Android actually launches (muted
  "coming soon" text only).
- Don't restructure the docs/help screenshot system under `images/docs/`.
- Keep deployment path identical (Firebase Hosting, `firebase deploy
  --only hosting`).

## Testing / verification

- Open `index.html` locally in a browser; verify all 8 screenshots
  load, no broken `images/` references remain.
- Check responsive breakpoints (mobile / tablet / desktop) for each
  act's asymmetric layout.
- Verify App Store badge links to the correct URL; Google Play shows
  muted coming-soon, not a dead link.
- Confirm reduced-motion disables animations.
- Validate the FAQ JSON-LD parses (no trailing-comma / syntax errors).
- Spot-check AA contrast on the four accent heading colors over navy.

## Out of scope (future)

- Android launch swap (flip muted text → live Play badge).
- Localized copy.
- Per-screenshot lightbox / zoom.
