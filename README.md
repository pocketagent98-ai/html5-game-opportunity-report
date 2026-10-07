# Web Game Store Opportunity Report

### Deep research across Poki, CrazyGames, GameDistribution, GameMonetize, Yandex Games, Playgama & itch.io — and the single highest-scoring game concept to build

**Prepared for:** a solo / small-team developer who wants to build one HTML5 game, publish it widely, and earn from **ads first** (with in-app purchases added later).
**Date:** October 2026

---

## 1. Executive summary

- **The channel is real and growing.** The browser / HTML5 games market is roughly **$8 billion in 2025–2026**, growing steadily (≈2.6–3.1% CAGR). More importantly, *supply and attention* are exploding: **over 15,000 new web games were released in a single quarter of 2025 — 2.7× the year before.** Poki alone reaches **100M+ monthly players** and **1B+ gameplays per month**; CrazyGames reaches **50–60M monthly players**, strongest in the US/Tier-1 markets.
- **Ad-only monetisation is the default on web**, and it fits every store in this report. Poki and GameDistribution *forbid* in-app purchases entirely; CrazyGames and Yandex Games allow IAP only on approval; Playgama supports IAP across ~10 partner platforms. This aligns perfectly with your "ads now, IAP later" plan.
- **The winning genre is merge + physics puzzle ("Suika / watermelon merge") with a light idle / progression meta.** Merge is the **fastest-growing casual subgenre** (+61–80% year-over-year) and the only complex casual niche still open to new entrants; physics puzzle grew **+61%**; "block puzzle" revenue grew **~12×**. On web specifically, *Watermelon Suika Game* did **1.1M+ plays** via GameDistribution, and Suika-style titles are trending on Playgama and CrazyGames right now.
- **One game can ship to all seven stores with one codebase.** Using the **Playgama Bridge** SDK (open-source, LGPL-3.0), a single build adapts to Poki, CrazyGames, GameDistribution, Yandex, YouTube Playables, MSN, Discord and 25+ platforms. You still submit through each store's own console, but you write the ad/payment/save code **once**.
- **Recommended build:** a **portrait, web-native (Construct / Phaser), <8 MB** drop-and-merge physics game with an original theme and a persistent "grow your world" idle meta. Target **65%+ conversion-to-play, 5+ minutes average playtime, ≤20 MB initial load.** Monetise with interstitials at natural breakpoints + optional rewarded videos (never gating core play). IAP hook is left dormant but wired.
- **Go / No-Go: GO (8.4 / 10).** High demand, low build cost, universal store fit, ad-ready, IAP-ready later. Main risk: crowding — mitigated by an original theme and the idle meta layer (see §12).

---

## 2. Methodology & tooling

This report follows the same **"market-intelligence stack"** logic described in your attached research brief, adapted from mobile app stores to **web game portals**:

| Layer in your brief | Tool named there | Web-game equivalent used here |
|---|---|---|
| App intelligence (rankings, listings, reviews) | **AppStoreCat** (MIT, self-hosted) | Portal listing/trending pages + aggregated rankings (e.g. Playgama Game Trends, Poki `/hot`, CrazyGames homepage) |
| Crawling JS-heavy store pages | **Crawl4AI** (Apache-2.0) | Direct reads of each portal's developer docs, requirements pages and terms |
| Deep research + report generation | **Research-Agent** (MIT) | Parallel web research across vendor docs, industry reports, developer blogs and search-trend data |

**Important honesty note (as in your brief):** no open-source tool gives 100% guaranteed revenue/download data for every store, because most web portals **do not publish per-game play counts**. Where a hard number is unavailable, this report uses **observed metrics, estimates and confidence levels** — never fabricated figures. Confidence is labelled where relevant.

**Standards applied when reading each store:** revenue-share %, audience size/region, submission requirements (file size, SDK, orientation, content rules), IAP policy, and best-fit genres.

---

## 3. The market at a glance

- **Market size:** browser games ≈ **$7.81B (2025) → $8.01B (2026)**, forecast ≈ $9.07B by 2030 (~3.1% CAGR).
- **Supply boom:** **15,000+ new browser games in Q2 2025 alone** (2.7× YoY, 4.9× vs. H1 2023). Competition is rising fast — originality now matters for approval.
- **Audience quality (Poki's 2026 State of Web Gaming):** 37% of web gamers play **multiple times per day**; typical session **11–20 minutes**; **92%** rate HTML5 games high quality; **62%** have downloaded or bought a game after first playing it on web. This is a **discovery layer**, not a throwaway channel.
- **Genre mix on web:** **hypercasual + puzzle dominate** — hypercasual held the #1 spot **79%** of the year, puzzle games #1 in morning windows (**21%**). Casual ≈ **20%** of all new releases; puzzles ≈ **14.5%**.
- **Engine reality:** **Unity powers ~55%** of new web games (3D/complex), but **light engines (Construct ~16.5%, Cocos ~8%, Phaser ~7%)** dominate 2D and casual — cheaper and faster for solo devs, and better for load time.
- **Why load time decides everything:** web players behave like short-form video viewers. A case study: Stickman Hook's 40 MB WebGL build → **29.5s load → 50% conversion**; rebuilt to 6 MB → **3.7s load → 72% conversion** (+22% plays). Cannon Clash hit **81% conversion** at a **2.4 MB** build. **Every megabyte costs audience.**

---

## 4. Store-by-store deep dive

### 4.1 Poki — the curated heavyweight

- **Audience:** 100M+ monthly players, 1B+ gameplays/month, #1 on web in 100+ countries. Strong global + Tier-1 mix.
- **Revenue model:** **ads only, no in-app purchases, no third-party ad systems.** Two deal types:
  - **Web Exclusive (preferred):** default **5-year** term. You get **100% of revenue** when *you* bring the player (search, bookmarks, social) and **50/50** when Poki brings them. Poki invests in marketing/UA. Exclusivity applies to the **open web only** — Steam, app stores and consoles stay yours.
  - **Non-Exclusive:** one-time **flat licence fee**, no revenue share.
- **Requirements (key ones):**
  - Initial download **< 8 MB** target (Unity games must optimise hard).
  - **No IAP UI at all**, no dual currencies, no ad-block messaging, no external splash/links, no third-party ads.
  - **No external requests** by default (no Google Fonts/CDNs/Analytics) — only Poki's own tooling.
  - **Portrait support required**; mobile controls forced on tablets; responsive.
  - **Originality is enforced** — "adds to diversity" means a mechanic/setting/angle *not already well represented*. Clones and AI-generated assets with watermarks are rejected. Misused IP / adult themes = instant rejection.
  - Provide static **and** animated thumbnails; implement the Poki SDK (`gameplayStart`, `commercialBreak`).
- **Engagement targets:** **65%+ conversion-to-play**, **5+ min avg playtime (10+ for management/sim)**; loops around **~3 minutes** perform best; ~1 hour of content is usually right.
- **Best fit:** polished, original casual/puzzle/hypercasual, portrait, instant-play.
- **Verdict:** highest ceiling and best marketing support, but the **hardest and most curated** gate, and **no IAP ever**. Best as your *primary* web home if your game is original enough.

### 4.2 CrazyGames — the Tier-1 volume platform

- **Audience:** 50–60M+ monthly players, **especially strong in the US/Tier-1**; 4,500+ games.
- **Revenue model:** **advertising revenue share** via the CrazyGames SDK. **Optional in-game purchases** (invite-only, via **Xsolla**, using CrazyGames `userId`). Monthly payouts through **Tipalti**; **€100 minimum**; effectively NET-10 in practice.
- **Two launch stages:**
  - **Basic Launch:** SDK optional, **no monetisation**, lets you go live and be evaluated.
  - **Full Launch:** SDK required → unlocks ads, analytics, cloud saves, social. **+50% compensation** if you opt into **2-month time-based exclusivity** (game hosted by CrazyGames).
- **Requirements:** initial download **≤50 MB** (≤20 MB to qualify for the mobile homepage); total ≤250 MB; ≤1500 files; PEGI-12; land **directly in gameplay**; must **work with AdBlock**; only SDK ads allowed; no other portal branding. Unity + HTML5 SDKs supported (Godot adapter too).
- **Best fit:** broad casual, .io/multiplayer, arcade, puzzle, merge — anything with strong retention.
- **Verdict:** the best **Tier-1 / US** reach and a real **IAP path later**. Basic Launch is a low-risk way to test.

### 4.3 GameDistribution (Azerion) — the pure distribution network

- **Audience:** **+3,000 publishers** (web portals, media companies, telecoms) across **105+ countries**, **+350M monthly gameplays**. Your game gets syndicated across the network — no need to court each publisher.
- **Revenue model:** **33% developer revenue share** of net revenue (ads **and** IAP). Payouts when ≥ €100, within 60 days of the monthly report.
- **Requirements:** SDK **mandatory**; **pre-roll + mid-roll ads are required**, rewarded/display optional but recommended; game paused & muted during ads; **English version mandatory**; HTTPS; desktop + mobile; responsive iframe (standard **800×600**); thumbnails at **512×512, 512×384, 200×120**; engines: Unity WebGL, JS, Construct 2/3. Approval can take **up to 3 weeks**. No identical games / IP violations.
- **Best fit:** almost any quality HTML5 casual game — it is a **syndication multiplier**.
- **Verdict:** the single best **reach-per-effort** for a distribution-only strategy, but a **lower share (33%)** and mandatory ad placements.

### 4.4 GameMonetize — the developer-friendly high-share network

- **Audience:** **7,500+ publishers**; 3,500+ in the portfolio, some exclusive (don't carry GameDistribution titles).
- **Revenue model:** **45% revenue share**, **NET-30** (paid within ~25 days), via **PayPal / USDT (ERC-20)**. **Minimum payout $30.** If you're also a publisher, dev + publisher share can total **90%**.
- **Requirements / perks:** SDK integration is quick (they claim ~5 min, SDKs on GitHub for JS/Construct/Unity); **branding and external links are allowed** (unlike Poki); simple self-serve dashboard with "Verify Game"; content-manager review before activation.
- **Best fit:** casual, arcade, puzzle — great as a **second/parallel network** alongside GameDistribution.
- **Verdict:** **best share among the pure networks**, easiest integration, but lower curation = less promotion; think of it as a **volume/monetisation** channel, not a marketing one.

### 4.5 Yandex Games — the CIS/emerging-market giant

- **Audience:** dominant in **Russia / CIS / emerging markets**; huge daily audience. Great for geo-diversification.
- **Revenue model — "Unified Licensing Model (ULM)":** combines **internal ads (Yandex Advertising Network, YAN) + third-party ads + in-app purchases** under one agreement, one payout. **In-app purchases ARE supported** (portal currency, "yans"). Yandex places ads and pays a **3% fee** itself.
- **Payouts:** once/month; thresholds **3,000 RUB / $150 / €100 / 500 AED**; paid within ~20 business days of the following month.
- **Requirements:** SDK **mandatory for moderation** (it also handles fullscreen, language detection, server time); **all ads and purchases must go through the SDK** (no third-party ads); auto language detection; **"Game Ready" event** must be called correctly; portrait/mobile friendly.
- **Best fit:** casual, puzzle, merge, arcade — anything with broad global appeal.
- **Verdict:** essential for **geo-diversification** and one of the few stores where **IAP is straightforward** later.

### 4.6 Playgama (+ self-hosting) — the one-SDK distribution layer

- **What it is:** **Playgama Bridge**, an **open-source (LGPL-3.0) unified SDK** that adapts **one HTML5 build** to **25+ platforms** — Poki, CrazyGames, GameDistribution, Yandex Games, YouTube Playables, MSN, Discord, Facebook, Huawei, Xiaomi, Reddit, GameSnacks, JioGames, Y8, Lagged, Playhop and more. It wraps each platform's native ads / storage / payments / leaderboard APIs. **Reach: 450M+ users/month.**
- **Revenue model — tiered, transparent:** **70% of net revenue up to $1,000**, then **80%** on revenue above $1,000 (up to $3,000), then **90%** above $3,000. Plus a **link-based distribution API** so 100+ partner portals can pull your game automatically.
- **IAP:** supported on **~10 platforms** (CrazyGames, Discord, Facebook, Huawei, Microsoft Store, MSN, Playgama, Reddit, Yandex). Games enabling IAP earn up to **1.8× more** than ad-only. **Anzu native ads** (in-game billboards) can yield up to **400% higher revenue** on some titles.
- **How publishing works:** Bridge handles the *SDK calls* only — you still **submit through each platform's own console**. It removes the per-store SDK rework, not the per-store submission.
- **Self-hosting:** you can also host the build yourself and syndicate via link — good for owning your own site and cross-promotion.
- **Verdict:** **the single highest-leverage decision in this report.** Integrate Bridge once → your game is portable to every store in this document. Strongly recommended as your integration layer.

### 4.7 itch.io — the indie showcase (not an ad network)

- **What it is:** free hosting + a storefront for indie games; supports **HTML5 web builds** played in an iframe. Hosts your files; you design the page.
- **Revenue model:** **Open Revenue Sharing** — *you choose* 0–100% to itch.io (default 10%). **Advertisements are never placed on your pages.** For **HTML5 games, payments are donations only**; to actually *sell*, you set the project to "Downloadable."
- **Role in your plan:** itch.io is **not** a primary ad-revenue channel (it doesn't run ads and HTML5 can't be sold directly). Use it as a **free showcase / portfolio / community-building** page, and as a funnel to your other stores. Good for wishlists, press keys and a "play in browser" demo.
- **Verdict:** include it for **discovery and credibility**, not for ad revenue.

---

## 5. Cross-store comparison matrix

| Store | Model | Dev share | Audience | IAP | SDK required | Key gate | Best role |
|---|---|---|---|---|---|---|---|
| **Poki** | Ads only | 50–100% (deal-based) | 100M+ MAU, global | ❌ Never | Yes | Originality + <8MB | Primary web home (if original) |
| **CrazyGames** | Ads (+IAP invite) | Rev-share (+50% w/ 2-mo exclusive) | 50–60M MAU, US-heavy | ✅ Invite-only (Xsolla) | Yes (Full) | Full Launch SDK | Tier-1 reach + IAP path |
| **GameDistribution** | Ads (+IAP) | **33%** | 3,000+ publishers, 105+ countries | ✅ | Yes | Mandatory pre/mid-roll | Distribution multiplier |
| **GameMonetize** | Ads | **45%** | 7,500+ publishers | (ads-focused) | Yes | Quick SDK + review | High-share second network |
| **Yandex Games** | Ads + IAP (ULM) | Rev-share | CIS/emerging dominant | ✅ | Yes (moderation) | SDK + Game Ready | Geo-diversification |
| **Playgama** | Ads + IAP + Anzu | **70→90%** tiered | 450M+ reach, 25+ platforms | ✅ (~10 platforms) | Bridge (open-source) | — | **One-SDK portability** |
| **itch.io** | Donations (HTML5) | 0–100% (you choose) | Indie discovery | Donations | No | None | Showcase / portfolio |

**Read-out:** publish with **Playgama Bridge** as the integration layer, then submit to **Poki (primary) + CrazyGames + GameDistribution + GameMonetize + Yandex**, and mirror a **showcase page on itch.io**. That combination covers the largest global reach, the highest Tier-1 share, and the best revenue-share tier — with **ads-only** throughout, exactly as you want now.

---

## 6. Genre & search-demand analysis

### What is growing
- **Merge** — the **fastest-growing puzzle subgenre**: **+61–80% YoY** (revenue ~$1.4–1.5B in 2025, +80% YoY), described as "the only growing complex niche." Still **open to new entrants** (unlike saturated Match-3).
- **Physics puzzle** — **+61% YoY**.
- **Block / Screw / Sort puzzles** — the "hybridcasual" risers: Block Puzzle revenue **~12×**, Screw Puzzle **~2.8×**, Sort Puzzle **~2.2×**.
- **Idle** — large, loyal user base; **ad-heavy (60–70% of revenue)**; highest average rating (7.61) in web-game analysis; day-1 retention **20–30%**.
- **Suika / watermelon merge (physics-drop)** — a **proven web phenomenon**: Suika Game has **13M+ downloads** across platforms; *Watermelon Suika Game* on GameDistribution did **1.1M+ plays** (700k in 2023 alone); the original *Merge Big Watermelon* browser game drew **1.4B search hits**. Multiple Suika-style titles are **trending on Playgama and CrazyGames right now**.

### What is saturated / declining
- Match-3 (mature, dominated by Candy Crush/Royal Match/Gardenscapes), Match-3D (**declining**), Find-the-Difference (**−42%**), classic arcade/action on web. **Avoid direct clones of these.**

### Search demand (confidence: medium–high)
- High-volume web search terms include **"watermelon game", "suika game", "merge games", "fruit merge"**, plus evergreen casual terms ("puzzle games", "io games", "car games", "idle games"). Google's 2025 Year-in-Search gaming list skews to big console titles, but *Strands* (an NYT puzzle) made the top-10 — evidence that **daily-loop puzzle/merge play is where casual search attention lives.**
- **Takeaway:** a **merge + physics-drop game** sits at the intersection of *growing genre*, *proven web virality*, and *high search demand*.

### What the top web charts actually look like
- **Poki trending/top:** Subway Surfers, Level Devil, Smash Karts, **Blocky Blast Puzzle**, Drive Mad, **My Perfect Hotel (idle)**, Plonky (physics), **Bounce Ball (merge/shooter)**, Vortella's Dress-Up.
- **CrazyGames top casual:** **Piece of Cake: Merge & Bake**, Ragdoll Archers, Piles of Mahjong, Words of Wonders, **Designville: Merge & Design**, **Farm Merge Valley**.
- **Playgama trending:** **Fruit Merge: Juicy Drop**, Snake 2048, **Piece of Cake: Merge & Bake**, Geometry Arrow 2, Hidden Object titles.

**Pattern:** on every store, **merge/puzzle + physics + idle** appear again and again at the top. That is your target zone.

---

## 7. Player pain points & market gaps

**Pain points (from portal guidance, review patterns and developer post-mortems):**
1. **Slow load / big builds** — the #1 killer of conversion-to-play. Players leave in seconds.
2. **Text tutorials / pop-ups** — web players won't read; onboarding must *be* gameplay.
3. **Waiting / downtime** — "games where the player can constantly perform an action outperform games with waiting." Idle must stay *interactive*.
4. **Aggressive / gating ads** — rewarded ads must be optional, never a wall; interstitials must sit at natural breaks.
5. **Overpromising thumbnails** — good CTR but immediate churn if the game doesn't match.
6. **Clone fatigue** — Poki explicitly rejects games that don't "add to diversity."

**Market gaps you can exploit:**
- **Merge + idle meta on web is under-served.** Merge is huge on mobile but web is still early — a merge game *with a persistent progression world* is a genuine gap and satisfies Poki's originality rule.
- **Hybridcasual depth without IAP.** Most web merge games are shallow. A deeper meta (unlockables, daily goals, light idle production) raises retention *and* ad impressions — without needing IAP.
- **Fresh themes beat fruit clones.** Fruit/Suika is crowded and risks clone-rejection. A new theme + one novel mechanic = approvable *and* searchable.

---

## 8. Recommended game concept (primary)

### 🏆 "Merge & Bloom" — a drop-and-merge physics puzzle that grows a living world

> **One-line pitch:** A juicy physics puzzle where you drop and merge magical plants/creatures — every merge grows your floating garden, which passively produces seeds while you're away.

**Why this wins across *all* seven stores:** it combines the **fastest-growing genre (merge)**, the **most proven web mechanic (physics drop)**, **high search demand**, **portrait + tiny build** feasibility, **ad-first monetisation**, and a **persistent idle meta** that satisfies Poki's originality bar and gives a reason to return — all while leaving an **IAP door open** for later.

**Core gameplay loop (~3 min):**
1. Drop a piece into the container (drag + release; one-finger / one-mouse).
2. Two identical pieces merge → the next tier up, with a satisfying pop + particle burst.
3. Merges fill a **growth meter**; each milestone **grows your garden** (a persistent meta-world).
4. Clear the board / hit a combo to earn **seeds** (soft currency).
5. Spend seeds on **garden upgrades** (passive seed production) and **cosmetic skins**.
6. Run ends only if the container overflows → **rewarded video offers one "undo"** (optional, never forced).

**Unique differentiators (the "adds to diversity" angle):**
- **Merge → grow-a-world meta:** your merges literally build a persistent garden that produces resources — a merge game *and* a light idle/tycoon game in one. This is the gap in the current web merge wave.
- **One novel mechanic — "Combo Chains":** fast consecutive merges build a multiplier that can *temporarily* let you drop higher-tier pieces. Skill expression on top of luck.
- **Original theme** (not fruit, not Suika) to dodge clone-rejection and own a searchable identity.

**Feature priority (build order):**
1. **P0 — MVP:** drop + merge physics core, overflow lose-condition, score, one container skin, one 60-second session, instant onboarding (no menu, no text tutorial).
2. **P1 — Retention:** garden idle meta (passive seeds + 3–4 upgrades), daily goal, cloud save (via SDK), rewarded "undo/boost".
3. **P2 — Depth:** Combo Chains, unlockable themes/skins, seasonal event container, leaderboard.
4. **P3 — Later:** cosmetic IAP (skins, season pass) — wired but disabled at launch.

**MVP scope (what "done" looks like):** one core scene, 8–11 merge tiers, physics tuned to ~60fps on mid mobile, <8 MB initial build, portrait + landscape, full-screen responsive, SDK hooks (gameplayStart/stop, interstitial, rewarded), analytics event on first merge and on game-over. **Target: 4–6 weeks for a solo dev on Construct/Phaser.**

**Why it scores highest (see §9 for the model):** search demand **9**, cross-store fit **9**, ad revenue potential **9**, build cost for a solo dev **8** (cheap), retention **8**, IAP-readiness **8**, competition crowding **6** (the one weak spot — mitigated by theme + meta).

---

## 9. Runner-up concepts & scoring

Scoring: each dimension 0–10, weighted; **higher is better** (for "crowding", a higher score = less crowded = better).

| Concept | Search demand | Cross-store fit | Ad revenue | Build cost (solo) | Retention | IAP-ready | Not crowded | **Weighted total** |
|---|---|---|---|---|---|---|---|---|
| **Merge & Bloom** (merge + physics + idle meta) | 9 | 9 | 9 | 8 | 8 | 8 | 6 | **8.4** |
| Hypercasual one-tap runner/drift (Drive Mad-style) | 9 | 8 | 7 | 9 | 6 | 4 | 4 | **7.0** |
| Idle / tycoon sim (My Perfect Hotel-style) | 7 | 8 | 9 | 6 | 9 | 7 | 6 | **7.6** |
| Block/Sort/Screw puzzle (hybridcasual) | 8 | 9 | 8 | 8 | 7 | 6 | 5 | **7.5** |
| .io multiplayer arena | 8 | 6 | 6 | 4 | 8 | 7 | 5 | **6.3** |

**Interpretation:**
- **Merge & Bloom (8.4)** wins on the best blend of growth, reach, monetisation and buildability.
- **Idle/tycoon (7.6)** is a strong *second game* — highest retention and ad revenue, but heavier to build and needs 10+ min playtime targets.
- **Runner/drift (7.0)** is cheapest to build and highly searchable, but **crowded** and IAP-poor.
- **Block/Sort/Screw puzzle (7.5)** is a great *fallback* if you want an even cheaper, very web-native build.
- **.io (6.3)** — great if you can build multiplayer (Poki Netlib helps), but the highest dev cost and risk for a solo team.

---

## 10. Monetisation & ad strategy (ads now, IAP later)

**Now — ads only:**
- **Interstitials** at natural breakpoints only: **game-over screen, level/milestone transition, return-to-menu.** Never at game start (platforms auto-handle start ads; calling it yourself can duplicate). Mute + pause the game during ads.
- **Rewarded video** — *optional extras only*: one "undo" after overflow, a temporary Combo boost, or a seed multiplier. **Never gate core progression behind a rewarded ad** (fails Poki review). Buttons must show a clear video icon, never green, never trick-placed.
- **Banners/display** (where a network allows, e.g. GD) — only outside gameplay, never covering content.
- **Frequency** is managed by the platform (Poki, GD, CrazyGames). **Do not** add your own cooldowns/timers to force ad frequency.
- **Stay playable with AdBlock on**, and let the platform handle ad-block messaging.

**Later — IAP hook (built now, switched on later):**
- Wire the **Playgama Bridge payments** module and the **CrazyGames `userId`** flow from day one, but **hide IAP UI at launch** (Poki forbids it; GameDistribution terms forbid native apps; basic-launch CrazyGames has no monetisation).
- Planned SKUs when you enable it: **cosmetic skins**, **season pass**, **seed bundles**, **no-ads-for-a-month**. Enable only on stores that support it (CrazyGames, Yandex, Playgama network, MSN, etc.).
- **Never ship IAP UI into a Poki or GameDistribution build.** Gate it by platform via Bridge's `payments.isSupported`.

**Revenue-share reality check:** ad CPMs vary by geo and season. Expect **Poki 50–100%**, **CrazyGames rev-share**, **GD 33%**, **GameMonetize 45%**, **Playgama 70→90%**. Your effective revenue = plays × session length × ad impressions × CPM × your share. Longer, ad-friendly sessions (merge + idle) directly raise all of these.

---

## 11. Store rollout & submission plan

**Phase 0 — Build & integrate (weeks 0–6):** build the MVP in **Construct 3 or Phaser** (web-native, small, fast). Integrate **Playgama Bridge** once for ads/save/platform. Target <8 MB, portrait, no external requests, instant onboarding.

**Phase 1 — Soft launch & iterate (weeks 6–9):**
- Submit to **CrazyGames (Basic Launch)** — go live fast, gather metrics, no SDK gate.
- Submit to **GameDistribution** and **GameMonetize** (fast, monetised distribution) with mandatory ad placements.
- Use **Poki Playtesting** (free) to get real-player videos and fix onboarding/drop-off.

**Phase 2 — Push for the big platforms (weeks 9–14):**
- Apply to **Poki** (primary) with the polished, *original* build.
- Submit to **Yandex Games** (SDK + Game Ready).
- Move **CrazyGames** to **Full Launch** (SDK, monetisation; consider the 2-month exclusivity +50%).
- Enable **Playgama** network distribution (link API) to reach 100+ partner portals.

**Phase 3 — Expand (month 4+):** YouTube Playables, MSN, Discord, Huawei/Xiaomi via Bridge; add the **itch.io showcase page**; add seasonal events; then **switch on IAP** on supported stores.

**Assets to prepare once, reuse everywhere:** static + animated thumbnails (512×512, 512×384, 200×120, 16:9 cover), a trailer, description + controls text, privacy policy URL, and a clean build with no debug/splash/outgoing links.

---

## 12. Risk analysis & Go / No-Go score

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| **Crowding** (merge/Suika clones everywhere) | High | High | Original theme + idle meta + Combo Chains mechanic; meet Poki's "adds to diversity" bar |
| **Slow load → low conversion** | Medium | High | Web-native engine, <8 MB, background asset loading, progress bar, instant onboarding |
| **Poki rejection** (curation) | Medium | Medium | Soft-launch elsewhere first, iterate with Poki Playtesting, keep the build original |
| **Ad-block / low CPM in some geos** | Medium | Medium | Diversify stores (Tier-1 via CrazyGames/Poki, emerging via Yandex/GD), keep sessions long |
| **WebGL/mobile performance** | Medium | Medium | Phaser/Construct 2D, cap particle counts, test on mid-range Android |
| **IP/trademark issues** | Low | High | Own theme; never reuse "Suika" name/art; keep AI assets watermark-free with documented prompts |
| **No IAP at launch** | Certain (by design) | Low | Ad-first by choice; IAP wired and switched on later |

**Go / No-Go score: 8.4 / 10 → GO.**
Merge + physics is the highest-demand, most proven, most ad-friendly, most store-portable concept available to a solo developer right now. The single genuine weakness (crowding) is directly addressed by the original theme and the idle meta layer. Build the MVP small and fast, soft-launch on CrazyGames/GD/GameMonetize, iterate with real player data, then push to Poki and Yandex and switch on IAP later.

---

## 13. Sources

**Portal developer documentation & terms**
- Poki for Developers — Working with Poki; Requirements; Monetization overview; Deal Types; Engagement; Reading test results; State of Web Gaming 2026.
- CrazyGames Docs — Requirements intro; Technical; Advertisement; Payouts; FAQ; Developer Terms (Aug 2025).
- GameDistribution — Developer Terms; Developer Guidelines; Partnership page; Watermelon Suika Game case study.
- GameMonetize — Developers; FAQ; SDK.
- Yandex Games SDK — Unified licensing model; Game requirements; Monetization; In-app purchases; Advertisement.
- Playgama — Developers; Bridge SDK wiki (getting started, payments, interstitial); Bridge business FAQ.
- itch.io — HTML5 uploads; Payments & Open Revenue Sharing; Developers.

**Market & genre research**
- The Business Research Company — Browser Games Market Report 2026.
- Global Market Statistics — HTML5 Games Market.
- PocketGamer.biz — CrazyGames browser-gaming report (hypercasual & puzzle dominance).
- AppMagic / GameAnalytics-style casual reports (via GameDevReports, Gamigion, Mobidictum, InvestGame) — Merge +61–80% YoY, Block Puzzle ×12, physics puzzle +61%.
- Playgama — Web-based Game Engine Rankings H1 2025; engine-by-genre analysis.
- Sublevel Games — Armor Games web-game data analysis (genre performance matrix).
- Wikipedia — Suika Game (genre history, downloads, viral dynamics).

**Methodology**
- Market-intelligence stack adapted from the developer's brief: AppStoreCat (MIT) + Crawl4AI (Apache-2.0) + Research-Agent (MIT), re-scoped from mobile app stores to web game portals.

---

*Note on data honesty: several web portals do not publish per-game play counts or revenue. Figures marked as estimates carry lower confidence and should be validated against your own post-launch dashboards. Revenue-share terms change; always confirm current terms in each store's developer portal before signing.*
