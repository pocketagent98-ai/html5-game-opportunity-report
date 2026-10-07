# Web Game Store Opportunity Report — v2 (Deep Research)

### A full market-intelligence study of Poki, CrazyGames, GameDistribution, GameMonetize, Yandex Games, Playgama & itch.io — and the single highest-scoring HTML5 game concept to build

**Prepared for:** a solo / small-team developer who wants to build one browser game, publish it widely, and earn from **ads first** (with in-app purchases added later).
**Date:** October 2026 · **Edition:** v2 (deep research)

> This edition supersedes v1. It adds verified store mechanics, real genre growth/decline numbers, published developer earnings and ad-economics benchmarks, a demand-vs-supply gap analysis, a weighted scoring model across all seven stores, and a full design brief for the winning concept.

---

## 0. How this research was done (and its limits)

This study follows the **market-intelligence stack** from the original brief, re-scoped from mobile app stores to **web game portals**:

- **Crawl4AI** (Apache-2.0) — fetched and installed in the workspace as the intended crawler. Its bundled browser driver could not launch under this environment's sandbox restrictions, so live pages were rendered with the sandbox's **headless Chrome** and read through the workspace's content-extraction fetcher instead.
- **AppStoreCat** (MIT) — fetched and inspected as the reference for structured store-intelligence (its Apple/Google adapters are the template for the "store adapter" pattern; it does not cover web portals, so no data was pulled from it).
- **Research-Agent** (MIT) — the intended report generator; the repo could not be fetched (unauthenticated GitHub rate limit). Its *method* (parallel multi-source research → consolidate → fact-check → report) was reproduced manually using four parallel deep-research agents covering: (1) store mechanics, (2) genre demand/supply, (3) developer earnings & ad economics, (4) player behaviour, search demand and pain points.

**Honesty limits (important):**
- The sandbox has **no direct outbound network from code**, so portals could not be crawled at the byte level; all page content came through the tool-mediated fetcher.
- Most portals **do not publish per-game play counts or revenue**. Where a hard figure is unavailable, this report uses published benchmarks and clearly-labelled estimates — never invented numbers.
- Search-volume figures are **third-party modelled estimates** (they vary by tool) and are directional only.
- Several revenue-share and payout terms differ between a portal's own pages (flagged inline); always confirm current terms in each portal's console before signing.

---

## 1. Executive summary

- **The channel is large and still growing.** Browser games are an ≈**$8B (2025–26)** market, and supply is exploding: **15,000+ new HTML5 games shipped in H1 2025 alone** (2.7× YoY). Poki reaches **100M+ monthly players / 1B+ monthly gameplays**; CrazyGames **50–60M monthly players**; Yandex Games **~50M MAU**; GameDistribution **350M+ monthly gameplays** across 3,000+ publishers; Playgama claims **450M+ monthly reach** across 25+ platforms.
- **Ads-only is the default on web and fits every store here.** Poki and GameDistribution forbid IAP; CrazyGames and Yandex allow IAP only on approval; Playgama supports IAP on ~10 of 25 platforms. This matches your "ads now, IAP later" plan exactly.
- **Two genres own the browser:** hypercasual and puzzle. CrazyGames' own 12-month US study (1.74B sessions) found hypercasual held the #1 spot **79%** of the year and puzzle **21%** — together the top-3 in **99.9%** of tracked hours.
- **The growth is in hybridcasual puzzle sub-genres.** Year-over-year: **Merge +80%** (fastest-growing casual niche), **Sort +176%**, **Screw +100%** (Screwdom alone >$75M IAP in 2025), **Block puzzle** revenue up many-fold. **Match-3 is flat** (~+1%) and downloads are falling — avoid.
- **The winning move is a merge-based hybridcasual puzzle with a light idle "grow" meta, built web-native (portrait, <8 MB, fast load).** It rides the #1 fastest-growing mechanic, has proven web demand but thin, high-quality web supply, and monetises perfectly on the web rewarded-ad economy. **Weighted score 8.45/10.**
- **One build, every store:** use the **Playgama Bridge** SDK (open-source, LGPL-3.0) as the integration layer, then submit through each store's console. Note: Playgama pays **70/80/90%** on partner-site revenue but a **flat 50%** on its own portal/iframe network.
- **Go / No-Go: GO (8.45/10).** Main risk is crowding; mitigated by an original theme, a light story/meta, and web-first tuning.

---

## 2. Store mechanics (verified)

### 2.1 Cross-store matrix

| Store | Model | Developer share | Payout | Audience | IAP | SDK | Key gate |
|---|---|---|---|---|---|---|---|
| **Poki** | Ads only | **100%** self-sourced, **50/50** Poki-sourced | Wire/PayPal (threshold & NET not published) | 100M+ MAU, 100+ countries | ❌ Never | Yes | Curated; <8 MB; originality |
| **CrazyGames** | Ads (+IAP invite) | Not published as a %; jam terms ~60% ads / 70% IAP | €100 min; ~NET-10 in practice (docs vs terms conflict) | 50–60M MAU, US/Tier-1 heavy | ✅ Invite-only (Xsolla) | Yes (Full) | Full Launch SDK; PEGI-12 |
| **GameDistribution** | Ads (+IAP) | **33%** of net | €100 min; within 60 days | 3,000+ publishers, 105 countries | (implied) | Yes | Mandatory pre-roll + mid-roll |
| **GameMonetize** | Ads | **45%** | $30 min; NET-30 (~25 days) | 7,500+ publishers | Not published | Yes | Quick SDK + review |
| **Yandex Games** | Ads + IAP (ULM) | Rev-share (unified) | 3,000 RUB / $150 / €100 / 500 AED; ≤monthly | ~50M MAU (60% Russia / 40% global) | ✅ | Yes (moderation) | SDK + Game Ready; ≤100 MB |
| **Playgama** | Ads + IAP + native | **70/80/90%** partner sites; **flat 50%** own portal/iframe | $100 min | 450M+ reach, 25+ platforms | ✅ (~10 platforms) | Bridge (open-source) | Bridge integration |
| **itch.io** | Donations (HTML5) | 0–100% (you choose; default 10%) | $5 min; 10–14 day review | Indie discovery | Donations only | No | None (self-publish) |

### 2.2 Key details per store

**Poki.** Preferred **web-exclusive** deals default to **5 years**; exclusivity covers the *open web only* (Steam, app stores, consoles stay yours) — and Poki counts Discord and YouTube Playables as "web". It pays **100%** when you bring the player and **50/50** when Poki does. **IAP is forbidden** (remove all purchase UI; dual currencies discouraged). Requirements: **initial download <8 MB** target, **16:9** scale, portrait strongly advised, **no external requests** (no Google Fonts/CDNs), must run with an **ad blocker** on, no splash/outgoing links, static **and** animated thumbnails. Free playtesting (up to 500 players, twice daily). Engagement bar: **65%+ conversion-to-play**, **5+ min** average playtime (**10+** for management/sim). In 2025 Poki reported **625M players, 11.1B gameplays, 227 new releases, 1,018 games over 1M plays**; its top titles were **Plonky** and **SnapStyle Dress Up**.

**CrazyGames.** Two stages: **Basic Launch** (SDK optional, **no monetisation**, 7–21 days, needs ≥500 plays) → **Full Launch** (SDK required, ads on). Requirements: initial download **≤50 MB** (≤20 MB for the mobile homepage), total ≤250 MB, ≤1500 files, PEGI-12, land directly in gameplay, must work with AdBlock. Optional **2-month exclusivity = +50% compensation**. **IAP invite-only via Xsolla.** Ad pacing is SDK-capped at **max 1 midgame ad / 3 min**. Per-genre benchmarks: **Puzzle 21 min / 6.2% D1**, Word 14/7.6%, Action 13/8.1%, Hypercasual 8.6/6.0%, .io 9/5.7%. Platform average D1 ≈ **6.7%**.

**GameDistribution (Azerion).** **33%** of net revenue; **pre-roll and mid-roll ads are mandatory** (display/rewarded optional). English version mandatory, HTTPS, responsive iframe (**800×600** recommended), thumbnails at **512×512 / 512×384 / 200×120**. Approval up to **3 weeks**. No duplicate games, no IP violations. A pure **distribution multiplier** (3,000+ publishers).

**GameMonetize.** **45%** share (90% if you're also the publisher), **NET-30**, **$30** minimum, PayPal/USDT. SDK is quick (JS/Construct/Unity), **branding and external links allowed**. A high-share, low-curation **volume** channel.

**Yandex Games.** **Unified Licensing Model** combines YAN ads + third-party ads + **IAP** under one agreement (Yandex pays the 3% fee itself). SDK **mandatory for moderation**; ads and purchases only through the SDK. **≤100 MB uncompressed**, index.html at archive root, English localisation needed (~40% of traffic is non-Russian). Moderation **3–5 business days** (1–2 for content-only); resubmission cooldowns double after each rejection. In 2025: **~50M MAU (+10% YoY)**, IAP revenue **+75% YoY**, ~10,000 low-rated games removed; **midcore entered its top-5 genres**.

**Playgama.** **Bridge** is an open-source (**LGPL-3.0**) SDK that adapts **one build to 25+ platforms** (Poki, CrazyGames, GameDistribution, Yandex, YouTube Playables, MSN, Discord, Facebook, Huawei, Xiaomi, Reddit, GameSnacks, JioGames, Y8, Lagged, Playhop, Microsoft Store…). Revenue: **70/80/90%** ladder on **third-party partner-site** revenue (thresholds $1,000 / $3,000; YouTube Playables flat 75%), but a **flat 50%** on playgama.com and its iframe partner network. **IAP works on ~10 of 25 platforms** (CrazyGames, Discord, Facebook, Huawei, Microsoft Store, MSN, Playgama, Reddit, Yandex) — **not** on Poki or GameDistribution. Submission: ≤300 MB ZIP; 24-hour testing; 1–5 business days on Playgama, up to 2 weeks on partner platforms. **Anzu native ads** can add revenue.

**itch.io.** **Open Revenue Sharing** (you choose 0–100%, default 10%). **No ads ever** on your pages. **HTML5 games can only take donations**; to *sell*, the project must be "Downloadable". No approval gate. Use it as a **showcase / portfolio / funnel**, not an ad channel.

---

## 3. Market & genre intelligence

### 3.1 Genre growth / decline (casual + web, YoY)

| Sub-genre | Signal | Direction | Web read |
|---|---|---|---|
| **Merge (esp. Merge-2)** | ~$1.4B (+80% YoY); only growing "complex niche" | 🟢 Fastest growth | Proven web demand; deep versions thin on web |
| **Sort puzzle** | ~$280M, **+176% YoY** | 🟢 Rising | Light clones on web; polish gap |
| **Screw / physics puzzle** | **+100%**; Screwdom >$75M IAP | 🟢 Rising | Hybridcasual, ad-friendly; thin on web |
| **Block puzzle** | revenue up many-fold; Color Block Jam ~75% of it | 🟢 Rising (concentrated) | Light clones on web |
| **Idle / clicker** | ad-heavy (60–70% of revenue) | 🟡 Steady, ad-friendly | Strong on web; rewarded-ad magnet |
| **Hidden object** | durable demand; older/female skew | 🟡 Steady | **Only ~4.8% of revenue is web** → low web supply |
| **Match-3** | ~$4.8B but **flat (+1%)**, downloads −8% | 🔴 Saturated | Incumbent-locked — avoid |
| **Hypercasual runners** | downloads **−14.8%** | 🔴 Declining | Crowded |
| **Dress-up (plain)** | downloads **−21.9%** | 🔴 Declining | Only wins with a shareable twist |
| **Racing / draw-line / rhythm** | −13% / −15% / −8% | 🔴 Declining | Avoid |
| **Generic arena .io** | mature since ~2016 | 🔴 Saturated | Thin monetisation; avoid |

### 3.2 Demand vs supply — where the gaps are

The browser slice of any sub-genre is almost never broken out in the major (mobile-first) reports, which is precisely why solo devs can find **high-demand / low-supply** web niches. Three stand out:

1. **Merge-2 "complex-meta" casual (the #1 gap).** Merge is the fastest-growing casual mechanic and is proven on web (Piece of Cake, Designville, Farm Merge Valley are top CrazyGames casual titles; Piece of Cake and Fruit Merge trend on Playgama; GameDistribution states Merge2/Merge3 perform well). Yet the **browser merge catalogue skews to light physics-drop / fruit / number merges** — deep, story/meta merge titles (mobile's growth engine) are comparatively scarce on Poki and CrazyGames.
2. **Hybridcasual screw / sort / block puzzles.** The breakout puzzle sub-genres, natively hybrid-monetised (rewarded ads + IAP), but on web they appear mostly as **light clones**, not polished products. Screwdom's ~**17% D30** retention shows the loop retains when executed well.
3. **Hidden-object / seek-and-find.** Large, loyal (older, female-skewed) demand and visible portal traffic, but web is only **~4.8%** of the genre's revenue and supply is thin/low-quality — the **lowest-competition** option.

**Avoid:** match-3, generic runners, generic arena .io, plain dress-up, generic mahjong/bubble clones.

### 3.3 Engines on web

Unity powers ~**55%** of new web releases (3D/midcore, but a large WebAssembly blob that hurts load time). **Construct (~16.4%)**, **Cocos (8.1%)**, **Phaser (7.1%)** dominate 2D/casual — cheaper, faster, and **web-native (faster load)**. For a merge/puzzle game, **Construct or Phaser** is the right stack.

---

## 4. Developer economics (what you can actually earn)

**Case studies (measured plays; revenue often undisclosed):**
- **Geometry Arrow** — **>€12,500 in ~3–4 months** via GameDistribution + Playgama (one of the few hard revenue figures).
- **Watermelon Suika Game** — **1.1M+ plays** on GameDistribution (700k in 2023, 442k across 2024–25); revenue not stated.
- **Emolingo Games** (Poki) — 8 games each >10M plays; two >75M; one hit **100M** plays; ~800k plays/day. Revenue undisclosed.
- **Blumgi** (Poki) — **100M+ plays in ~2 years**, "income that allows me to live comfortably".
- **Artem Lanin** (Poki) — **67M gameplays** in 2025; web dev became his primary income.
- **CrazyGames dev (Liquid Swarm)** — ~**€31/day** six weeks after Full Launch; adding a rewarded ad lifted revenue ~25% next day.
- **Anul Agarwal** — one CrazyGames game: 50k plays ≈ **$250** (~$5 / 1,000 plays); another game $25k lifetime.
- **Older GD portfolio** — ~€0.3–0.9 per 1,000 plays (2019–21, tier-3-skewed); **older CrazyGames** — ~€1.20 per 1,000 plays.
- **Platform-level:** Poki says some studios earn **$1M/yr** from ads alone; Yandex says hundreds of devs exceed **$20k/month**.

**Ad economics (web):**
- **Rewarded video is the highest-paying web format** — gross ranges ≈ **$15–28** eCPM US/Canada, **$8–15** EU, **$1–3** tier-3; interstitials ≈ $3–12 US; banners ≈ $0.20–1.50. **Realised** network averages are far lower (AppLixir web network ≈ **$3.62 global, $6.20 tier-1, $6.98 US**).
- **Geography dominates.** US RPM is ~5–10× tier-3 RPM. A US-heavy audience is worth multiples per play.
- **Format mix:** rewarded ≈ highest eCPM; interstitials drive the most *total* revenue (shown at every natural break); banners the least. A typical web mix is ~75% rewarded / ~18% interstitial / ~7% banner.
- **Modelled per-million:** 1,000,000 plays × ~60% opt-in × ~$15 blended eCPM × ~60% share ≈ **$5,400/month** — an estimate, not a guarantee. Realistic first-game bands: **$0–400/month**; well-performing casual titles **$200–2,000/month**; earnings spike while featured, then decay.
- **Seasonality:** Q4 raises eCPM, Q1 is a trough — plan against a 12-month rolling average.

**Takeaway:** web ads reward **volume + session length + a Tier-1 audience**. That argues for a game with **long sessions, many natural ad breaks, and US/UK appeal** — exactly what a merge/hybridcasual puzzle with a light meta delivers.

---

## 5. Player behaviour, search & pain points

**Discovery reality.** Players do **not** search "browser games" (near-zero interest). They search **brands** (Poki ~1.5M, CrazyGames 4M+ monthly brand searches), **portals** ("cool math games" ~7.5M), and the **"unblocked games" cluster** (~2.7–7.5M monthly across tools). Head-terms are unwinnable for a solo dev, so discoverability must come from **genre/mechanic long-tails + portal placement**. Name the game around a **searchable mechanic/theme**, not "browser game".

**Search demand that matters to you.** "watermelon game" / "suika" is a real, global search term (the original *Merge Big Watermelon* browser game drew **1.4B** Sina Weibo search hits; Suika has **13M+** downloads). "merge games", "car games" (~10% of Poki plays), "io games" and "puzzle games" are high-demand. **Merge/suika is a searchable, proven mechanic.**

**Retention & engagement benchmarks.**
- Poki: **65%+ conversion-to-play**, **5+ min** average playtime (10+ sim); ~3-minute loops; ~1 hour of content; "make the first minutes brilliant".
- CrazyGames: conversion (80%+ play >1 min), playtime (10+ min success), **D1 10–15% success**; platform average D1 ≈ 6.7%.
- Typical web session **11–20 min** (Poki) / ~**30 min** (CrazyGames); **2–3 titles per session**; **37%** play multiple times a day.
- Mobile-app contrast (for calibration): median D1 ~22%, D7 ~4%, D30 <1%.

**What separates hits from flops.**
1. **Load time / file size is the #1 lever.** Stickman Hook: 40 MB / 29.5 s → **50%** conversion; 6 MB / 3.7 s → **72%** (+22% plays). **46%** of players have quit a game over load time.
2. **Onboarding** — skip menus, drop players straight into gameplay, teach visually (never text), first levels easy.
3. **Portrait + mobile-first** — the audience is majority mobile; portrait games get more gameplay entries and extra ad eligibility.
4. **Thumbnail** — Poki calls it "the single most important factor for CTR"; text-free, bright, colourful art wins.
5. **Originality** — Poki rejects clones/"heavily tool-assisted" games; a novel core mechanic can earn an exclusivity deal.

**Top player complaints:** ads (the #1 — forced/unskippable, no ad-free option), **slow loading**, **clones/IP copies**, and **bugs/difficulty/controls**. Design for polite ads (rewarded = optional extras, interstitials at natural breaks only) and fast, clean loads.

---

## 6. The scoring model

Each concept scored 0–10 per dimension (higher = better; for *Risk*, higher = lower risk). Weights: demand 15%, cross-store fit 15%, ad monetisation 15%, retention 15%, supply-gap/differentiation 15%, solo build feasibility 10%, IAP-readiness 5%, risk 10%.

| Concept | Demand | Cross-store | Ads | Retention | Gap | Build | IAP | Risk | **Total** |
|---|---|---|---|---|---|---|---|---|---|
| **A. Merge-2 hybridcasual puzzle + idle "grow" meta** | 9 | 9 | 9 | 8 | 9 | 7 | 9 | 7 | **8.45** |
| B. Hybridcasual screw/sort/block puzzle | 8 | 9 | 9 | 8 | 8 | 8 | 8 | 7 | **8.20** |
| C. Physics-drop merge (suika) + meta | 9 | 9 | 8 | 7 | 6 | 8 | 7 | 6 | **7.60** |
| D. Idle / tycoon sim | 7 | 8 | 9 | 9 | 6 | 6 | 7 | 7 | **7.50** |
| E. Hidden-object / seek-and-find + story | 7 | 8 | 8 | 7 | 9 | 6 | 6 | 7 | **7.45** |
| F. Hypercasual runner / drift | 9 | 8 | 7 | 6 | 4 | 9 | 4 | 6 | **6.80** |
| G. Generic match-3 (control) | 6 | 8 | 8 | 8 | 3 | 6 | 8 | 4 | **6.35** |
| H. Generic .io arena | 8 | 6 | 6 | 8 | 5 | 4 | 6 | 6 | **6.15** |

**Winner: Concept A.** **Runner-up: Concept B** (a valid alternative if you prefer an even cheaper, more web-native build).

---

## 7. THE recommended concept

### 🏆 "Merge & Bloom" — a merge-2 hybridcasual puzzle with a living-garden idle meta

**One-line pitch:** A portrait, instant-play merge puzzle where combining pieces grows a persistent magical garden that keeps producing resources while you're away — one game that is both a merge puzzle *and* a light idle tycoon.

**Why this wins across all seven stores:**
- **Genre:** rides **merge**, the #1 fastest-growing casual mechanic (+80% YoY), with proven web demand and thin high-quality web supply.
- **Monetisation:** its loops create perfect, polite ad breaks (level end, stuck-state, return-to-menu) → ideal for the web **rewarded-video** economy (the highest-paying format).
- **Retention:** the idle "grow" meta adds a return reason beyond the puzzle loop (addresses web's weak D1/D7).
- **Cross-store fit:** portrait, small build, ads-only at launch — passes Poki (no IAP) and GameDistribution (ads-only), and is IAP-ready for CrazyGames/Yandex/Playgama later.
- **Originality:** a *merge + idle-world* hybrid with a fresh theme is genuinely under-represented on web → meets Poki's "adds to diversity" bar.

**Core loop (~3 min):**
1. Merge identical pieces (drag-and-drop) to the next tier; juicy pop + particle feedback.
2. Each merge feeds a **growth meter**; milestones **expand your garden** (persistent meta-world).
3. Clearing boards / combos earn **seeds** (soft currency).
4. Spend seeds on **garden upgrades** (passive seed production) and **cosmetic themes**.
5. **Stuck-state → optional rewarded video** for a booster or an extra move (never a forced gate).
6. Run ends only on overflow; the garden keeps producing → a reason to return.

**Differentiators:**
- **Merge → grow-a-world meta:** the exact gap on web (merge-2 depth), delivered as a merge + idle hybrid.
- **One novel mechanic — "Bloom Chains":** fast consecutive merges build a multiplier that can unlock higher-tier drops (skill on top of luck).
- **Fresh, searchable theme** (not fruit/Suika) to dodge clone-rejection and own an identity — while the mechanic stays in the high-search "merge/watermelon" space.

**Feature priority (build order):**
1. **P0 — MVP:** merge core + overflow lose-condition + score + instant onboarding (no menu, no text tutorial) + one skin. **Portrait-first, <8 MB, load <5 s.**
2. **P1 — Retention:** garden idle meta (passive seeds + 3–4 upgrades), daily goal, cloud save via SDK, rewarded booster.
3. **P2 — Depth:** Bloom Chains, unlockable themes, seasonal event, leaderboard.
4. **P3 — Later:** cosmetic IAP (skins, season pass) — wired but **hidden** at launch.

**MVP scope / effort:** one core scene, 8–11 merge tiers, physics-free or light physics, 60 fps on mid Android, portrait + landscape, SDK hooks (`gameplayStart/Stop`, interstitial, rewarded), analytics on first merge and game-over. **Estimate 4–6 weeks solo in Construct 3 or Phaser.**

---

## 8. Runner-up concepts (briefs)

- **B. Hybridcasual screw/sort/block puzzle** (8.20). A polished, web-tuned "screw/sort/block" with the *stuck → rewarded ad / IAP continue* loop. Native hybridcasual, extremely web-native build, Screwdom-like retention (~17% D30). Best if you want the cheapest, fastest, most web-first build.
- **E. Hidden-object / seek-and-find + light story** (7.45). Lowest competition on web (~4.8% of genre revenue is web), loyal older/female audience, great for short ad-break sessions. Best if you want to avoid the crowded puzzle/merge space.
- **D. Idle / tycoon sim** (7.50). Highest retention and ad revenue, but heavier to build and needs 10+ min sessions. Best as a *second* game.

---

## 9. Monetisation & ad strategy (ads now, IAP later)

**Now — ads only:**
- **Rewarded video (primary earner):** optional extras only — one "extra move"/"undo" on a stuck-state, a temporary Bloom-Chain boost, a seed multiplier. Clear video icon, never green, never trick-placed. **Never gate core progression behind a rewarded ad.**
- **Interstitials:** at natural breaks only (level end, game-over, return-to-menu). Never at game start (platforms auto-handle start ads). Mute + pause during ads.
- **Banners/display** (where a network allows, e.g. GD): outside gameplay, never covering content.
- **Let the platform pace ads** (Poki, GD, CrazyGames cap frequency — e.g. CrazyGames ≤1 midgame ad / 3 min). **Do not** add your own ad timers.
- **Stay playable with AdBlock on**, no ad-block messaging.
- **Optimise for a Tier-1 audience** (US/UK) — it multiplies revenue per play several-fold.

**Later — IAP (wired now, switched on later):**
- Integrate the **Playgama Bridge payments** module and the **CrazyGames `userId`** flow from day one, but **hide IAP UI at launch** (Poki and GameDistribution forbid it; Basic-Launch CrazyGames has no monetisation).
- Planned SKUs: **cosmetic themes/skins, season pass, seed bundles, ad-free month**. Enable only where supported (CrazyGames, Yandex, Playgama network, MSN, etc.) using Bridge's `payments.isSupported`.

**Revenue reality:** effective revenue ≈ plays × session length × ad impressions × eCPM × your share. Longer, ad-friendly sessions (merge + idle) raise every factor. Expect **Poki 50–100%**, **CrazyGames ~60% ads**, **GD 33%**, **GameMonetize 45%**, **Playgama 70–90%** (partner sites).

---

## 10. Store rollout & submission plan

**Phase 0 — Build & integrate (weeks 0–6).** Build the MVP in **Construct 3 / Phaser**; integrate **Playgama Bridge** once. Target <8 MB, portrait, no external requests, instant onboarding.

**Phase 1 — Soft launch & iterate (weeks 6–9).** Submit to **CrazyGames (Basic Launch)**, **GameDistribution** (mandatory ad placements) and **GameMonetize** (fast monetised distribution). Use **Poki Playtesting** (free) to fix onboarding drop-off.

**Phase 2 — Push the big platforms (weeks 9–14).** Apply to **Poki** (primary, original build) and **Yandex Games** (SDK + Game Ready). Move **CrazyGames to Full Launch** (consider the 2-month exclusivity +50%). Enable **Playgama** network distribution (link API → 100+ partner portals).

**Phase 3 — Expand (month 4+).** YouTube Playables, MSN, Discord, Huawei/Xiaomi via Bridge; add the **itch.io showcase page**; add seasonal events; then **switch on IAP** on supported stores.

**Assets to prepare once:** static + animated thumbnails (512×512, 512×384, 200×120, 16:9 cover), a trailer, description + controls, a privacy-policy URL, and a clean build (no debug/splash/outgoing links).

---

## 11. Risk analysis & Go / No-Go

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| **Crowding** (merge/puzzle clones) | High | High | Original theme + story/idle meta + Bloom Chains; meet Poki's "adds to diversity" bar |
| **Low hit-rate even in hot genres** (~1.6% of merge-2 launches succeed) | High | High | Keep MVP small, soft-launch, iterate on real metrics; expect to build more than one game |
| **Slow load → low conversion** | Medium | High | Web-native engine, <8 MB, background loading, progress bar, instant onboarding |
| **Poki rejection (curation)** | Medium | Medium | Soft-launch elsewhere first; iterate with Playtesting; keep it original |
| **Low eCPM in emerging geos** | Medium | Medium | Diversify stores (Tier-1 via Poki/CrazyGames; emerging via Yandex/GD); chase Tier-1 |
| **WebGL/mobile performance** | Medium | Medium | Phaser/Construct 2D, cap particles, test on mid-range Android |
| **IP/trademark** | Low | High | Own theme; never reuse "Suika" name/art; document AI-tool use |
| **Earnings decay after feature** | High | Medium | Live-ops/seasonal content; multiple games in the catalogue |

**Go / No-Go score: 8.45 / 10 → GO.** Merge-based hybridcasual puzzle with an idle "grow" meta is the highest-demand, most ad-friendly, most store-portable concept available to a solo developer. Build small, soft-launch, iterate, then push to Poki/Yandex and switch on IAP later.

---

## 12. Sources

**Portal developer documentation & terms:** Poki for Developers (revenue-deal-types, how-monetization-works, requirements, engagement, reading-results, payouts-billing, game-thumbnail, easy-access); Poki blog (2025 year in review; State of Web Gaming 2026; what-makes-high-quality-browser-game; building-web-browser-games-2026). CrazyGames Docs (requirements/intro, technical, ads, ad-monetization-guide, midgame-ads-pacing, basic-launch-metrics, monetizing-puzzle/word/hypercasual-io/clicker/midcore-idle/action, payouts, FAQ) and Developer Terms (Aug 2025). GameDistribution (developer terms, developer guidelines, partnership, Editor's Picks, Geometry Arrow and Watermelon Suika case studies). GameMonetize (developers, FAQ, SDK, network). Yandex Games SDK (unified licensing model, requirements, monetization, in-app purchases, advertisement, metrics, moderation, promotion). Playgama (developers, developer terms, Bridge SDK wiki — getting started/payments/interstitial, submitting-a-game, platform-specific requirements, payments FAQ, engine rankings, game-trends). itch.io (html5 uploads, payments/open revenue sharing, developers, FAQ).

**Market & genre research:** AppMagic casual-games reports (via Mobidictum, GameDevReports, Gamigion) — Merge +80%, Sort +176%, Screw +100%, Match-3 flat; Naavik — niche puzzle sub-genres; Deconstructor of Fun — screw-puzzle gold rush; Sensor Tower — physics/hidden-object; CrazyGames US browser study via PocketGamer.biz and GamesPress; The Business Research Company / Global Market Statistics — browser & HTML5 market size; Playgama — web engine rankings; Sublevel Games — Armor Games genre matrix.

**Economics & benchmarks:** AppLixir (web eCPM, rewarded benchmarks, ARPU/CPM guides); Playgama (web monetization breakdown, rewarded-vs-interstitial, RocketGoal case study); Google H5 Games Ads case studies; developer blogs (Blumgi/Poki, Emolingo/Poki, Artem Lanin, Anul Agarwal, Mickael Bergeron Neron, Gamezdev, ImposterGame).

**Player behaviour & search:** Poki State of Web Gaming 2026; CrazyGames US browser study; Google Trends (via third-party summaries); keyword tools (clicks.so, ASOTools, AppTweak, Semrush) — **all volumes are modelled estimates**; Wikipedia (Suika Game); app-store review mining.

**Methodology:** Crawl4AI (Apache-2.0), AppStoreCat (MIT), Research-Agent (MIT) — re-scoped from mobile app stores to web game portals, as described in §0.

---

*Data-honesty note: several portals do not publish per-game play counts, revenue, payout thresholds or NET terms (Poki's threshold/NET; CrazyGames' revenue-share %; GameDistribution/GameMonetize file-size and orientation limits; itch.io audience size). These are labelled "not published" rather than guessed. Revenue-share terms change; always confirm current terms in each store's console before signing. Search volumes are third-party estimates. Figures marked as estimates carry lower confidence and should be validated against your own post-launch dashboards.*
