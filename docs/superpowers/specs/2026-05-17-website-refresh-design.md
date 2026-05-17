# Fanager Website Refresh — Design Spec

**Date:** 2026-05-17
**Status:** Approved (pending written-spec review)
**Scope:** `fanager-website/index.html` + `images/` assets

## Goal

Bring the public marketing site (fanager.app) up to date with the app's
1.1.0 feature set, replace dated March screenshots with fresh framed
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
| 5 | Act III — Players (cyan) | New section — RSVP matches & practices, see starting XI 30 min pre-kickoff, push when named | 05 Schedule & RSVP |
| 6 | Act IV — Fans (amber) | Expanded — browse free, follow, live scores, Predictions with reveal, push | 07 Following, 06 Game Prediction |
| 7 | Platform strip | Keep — sign-in, push, moderation, profanity, reporting, blocking, account deletion, free | — |
| 8 | How it works | Keep — 3 steps | — |
| 9 | FAQ | Updated — keep 6, refresh answers, add lineups/practices Q | — |
| 10 | Final CTA + Footer | Updated — App Store badge live, Google Play soon; footer unchanged | — |

## Copy source of truth

Feature copy derives from the in-app release notes
(`league-mgr-app/lib/core/constants/release_notes.dart`, v1.1.0) and the
App Store description finalized earlier this session. Do not invent
features not present there.

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
- Lineups builder — up to 10 formations, pick captains, assign to any match in a tap
- Schedule recurring practices; track per-player RSVPs
- See who's Going / Maybe / Not Going / no-reply (Match RSVP)
- Request to join seasons, pay fees in-app
- Next Match, Last 5, full match history
- Hand a team off to a new manager by email or 6-digit claim code

**Act III — Players (new)**
- RSVP every match and practice in one tap
- See your starting lineup published 30 minutes before kickoff
- Push notification when your name is in the XI

**Act IV — Fans**
- Browse leagues, teams, standings without an account
- Follow favorite teams and leagues
- Live scores and standings on match days
- Lock in match Predictions before kickoff, animated reveal at full time
- Push notifications for results, lineups, followed-team updates

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
- **Screenshot frame:** consistent reusable treatment — `rounded-[2rem]`,
  `border border-border-dark`, accent-tinted `shadow`. No real device
  bezel images; CSS frame only.
- **Motion:** keep existing reveal-on-scroll IntersectionObserver and
  `prefers-reduced-motion` guard. No new JS libraries.
- **Accessibility:** every screenshot needs a descriptive `alt`;
  maintain heading order; AA contrast on accent text.

## SEO / meta

- Update `<title>` / meta description / OG / Twitter copy to drop
  "Coming soon" framing and mention lineups, live scores, predictions.
- Update the FAQPage JSON-LD to match revised FAQ answers and the new
  lineups/practices question.
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
