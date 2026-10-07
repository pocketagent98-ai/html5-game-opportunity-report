# MERGE & BLOOM — Complete Game Design Document (build-ready)

**Genre:** merge-2 hybridcasual puzzle + light idle "grow-a-garden" meta
**Platform:** HTML5 browser, portrait-first, published to Poki, CrazyGames, GameDistribution, GameMonetize, Yandex Games, Playgama (+ itch.io showcase)
**Monetisation:** ads first (rewarded video + interstitials via platform SDKs), IAP wired but hidden at launch, enabled later on supported stores
**Target build:** ≤6 MB initial / ≤12 MB total, first gameplay in ~3 s, 60 fps on mid-range Android
**Audience:** broad casual; core skews female 30+; family-friendly (PEGI 12 / all-ages)

> This document is written so that it can be handed directly to a developer or an AI coding assistant. Every system has concrete numbers, states and edge-cases. Where a value is a tunable starting point, it is marked **[TUNE]**.

---

## 0. The ads question — answered first (read this before anything else)

**Short answer: on the portals you do NOT add your own ads. You integrate the platform's SDK, the platform serves the ads, and the platform pays you a revenue share. Do NOT use AdMob for a web game.**

- On **Poki, CrazyGames, GameDistribution, GameMonetize, Yandex Games and Playgama**, advertising is delivered by that platform's own SDK. You call ad events in your code; the platform fills them with its ad partners. Bringing a third-party ad system is **against the rules** — Poki blocks all external requests and forbids any ad system but its own; CrazyGames allows only SDK ads; GameDistribution only GD ads; Yandex only SDK ads. So there is nothing to "plug in" from outside — the ads come from the store.
- **AdMob is the wrong tool here.** AdMob is Google's *mobile app* ad platform (Android/iOS apps). It is not the web-game product. It only becomes relevant if your HTML5 game runs inside a **mobile app WebView** (Android) that you own — not our case.
- **If (and only if) you self-host the game on your own website** do you need your own ad solution — and the correct Google product is **AdSense H5 Games Ads** (the "Ad Placement API", `adBreak()`), *not* AdMob. A strong alternative for self-hosting is **Playgama Ad** (one JS SDK, ~11 demand partners including Google Ad Manager, applied for at playgama.com/adv).
- **Plan for this game:** publish on the portals with **Playgama Bridge** as the single SDK integration layer (it wraps Poki, CrazyGames, GameDistribution, Yandex, etc.), so one build earns from every store's ads. Optionally self-host later with H5 Games Ads / Playgama Ad for a second, owned revenue stream.

| Situation | Who serves the ads | What you integrate |
|---|---|---|
| Published on Poki / CrazyGames / GD / GameMonetize / Yandex / Playgama | The platform | That platform's SDK (unified via **Playgama Bridge**) |
| Self-hosted on your own site | You | **Google AdSense H5 Games Ads** (`adBreak()`) or **Playgama Ad** — **not AdMob** |
| Inside your own mobile app WebView (Android) | Google | H5 Games Ads + AdMob slot IDs (edge case, ignore for now) |

---

## 1. One-paragraph brief (the "hand this to an AI" summary)

> Build a portrait HTML5 browser game called **Merge & Bloom**. The player taps a **Seed Pod** to spawn plants onto a **6×6 grid**, then drags one plant onto an identical one to **merge it into the next tier** (10 tiers, Sprout → World Tree). Filling **visitor orders** with merged plants earns **Coins**. Every merge also feeds a **Bloom Meter** that unlocks **Garden plots** — a persistent idle meta where planted plants passively generate Coins over time (including offline). Coins buy **upgrades** (bigger energy cap, faster regen, stronger Seed Pods, more plots, longer offline cap). Energy gates tapping; it regenerates over time and can be topped up by an optional rewarded video. The game must load in about 3 seconds, run at 60 fps on a mid-range phone, be fully playable in portrait and with an ad-blocker on, and monetise via rewarded video (opt-in boosts) and interstitials (at natural breaks, paced by the platform). Build it in **Phaser 3 + TypeScript + Vite**, integrate the **Playgama Bridge** SDK once, save via Bridge Storage, and keep the build under 6 MB.

---

## 2. Design pillars

1. **Instant play.** No splash, no menu, no text tutorial — the first merge happens within ~10 seconds of load.
2. **Constant satisfying action.** Merge feedback (pop, particles, sound) on every merge; no dead time.
3. **Web-first loops.** ~3-minute core loop; 5–15 minute sessions; nothing that punishes you for leaving.
4. **Politeness.** Ads are opt-in extras or at natural breaks; the game is fully playable with an ad-blocker and without ever watching an ad.
5. **Return reason.** The idle Garden keeps producing while away → a reason to come back tomorrow.
6. **Original.** A merge + idle-garden hybrid with a plant theme — not a fruit/Suika clone and not a 2048 clone.

---

## 3. Core game loop

### 3.1 The 3-minute core loop
1. **Tap a Seed Pod** → a tier-1 plant (Sprout) drops into an empty grid cell. Costs **1 Energy**.
2. **Drag a plant onto an identical plant** → they merge into the next tier (juicy pop + particles + chime).
3. **Complete visitor orders** (shown as speech bubbles) → earn **Coins** + XP.
4. Every merge adds to the **Bloom Meter**; filling it **unlocks a Garden plot** or an upgrade token.
5. When the grid fills up and no merges are possible, the **board "rests"** (soft reset — see §4.5) or the player spends a booster; this is the natural **interstitial** moment.

### 3.2 The meta loop (session-to-session)
- Spend **Coins** on **Garden upgrades**: +Energy cap, +regen speed, Seed Pod level, more plots, longer offline cap.
- The **Garden** passively produces Coins; collect on return (offline earnings), optionally **doubled via a rewarded video**.
- Daily goal + daily gift keep a light daily habit.

### 3.3 Long-term goal
- Fill all **12 Garden plots**, fully upgrade the Seed Pod to the **Golden Pod**, and reach the **World Tree** (tier 10). Cosmetic **garden themes** (unlocked via progression; later purchasable) provide a long tail.

---

## 4. Merge board system

### 4.1 The board
- **Grid:** 6 columns × 6 rows = 36 cells. Portrait. Cell size scales to viewport.
- **Interaction:** tap-to-spawn (on the pod), **drag-and-drop** to merge (mouse or touch). Also allow **tap-source → tap-target** as an accessible alternative.
- **Merge rule:** dragging a plant onto an **identical** plant merges them into one plant of the next tier. Only identical tiers merge. No auto-merge (prevents accidents). Merging is **free** (no energy cost) — energy is spent on *producing*, not merging.
- **Drop-off the top:** tier 10 does not merge further (it is the goal); two tier-10s can optionally be combined to trigger a **"World Bloom"** celebration + big coin bonus.

### 4.2 Tier table (the merge chain) **[TUNE]**
| Tier | Name | Order value (Coins) | Merge XP | Notes |
|---|---|---|---|---|
| 1 | Sprout | 1 | 1 | produced by Seed Pod |
| 2 | Seedling | 3 | 2 | |
| 3 | Bud | 8 | 4 | |
| 4 | Bloom | 20 | 8 | first "pretty" tier — strong visual reward |
| 5 | Berry Bush | 50 | 15 | |
| 6 | Fruit Tree | 120 | 25 | |
| 7 | Glow Vine | 300 | 40 | starts glowing |
| 8 | Crystal Bloom | 750 | 60 | |
| 9 | Golden Tree | 1,800 | 90 | rare, high value |
| 10 | **World Tree** | 5,000 | 150 | the goal tier |

> Each tier's order value ≈ ×2.5 the previous — this exponential curve is the genre standard and is what makes higher tiers feel valuable.

### 4.3 Seed Pod (the producer)
- **Tap** the pod → spawns the **base plant** into a random empty cell (nearest-to-centre preferred). Cost: **1 Energy/tap**.
- **Charge:** a pod has a limited number of taps (**30 [TUNE]**) before it goes on **cooldown** for **60 s [TUNE]**. Show charge as a small bar on the pod.
- **Pod levels:** Level 1 pod spawns tier-1. Upgrading the pod (**Golden Pod**, bought with Coins) raises the spawn table so higher-level taps occasionally spawn tier-2 or tier-3 directly — this is the genre's core efficiency upgrade (it accelerates progression without changing the merge rule).
  - Pod L1: 100% tier-1.
  - Pod L2: 90% t1 / 10% t2.
  - Pod L3: 80% t1 / 18% t2 / 2% t3. **[TUNE]**
- **Auto-Producer (later unlock):** an upgradeable plot that spawns a base plant automatically every N seconds (free, no energy) — gives the idle meta a bridge back into the board.

### 4.4 Energy **[TUNE]**
- **Cap:** 50 at start (upgradeable to 100 via Garden). Show as a bolt icon + number + a filling bar.
- **Regen:** +1 every **30 seconds**, pausing at the cap. (Web sessions are shorter than mobile, so regen is faster than the mobile-standard 2 min — this keeps web players from bouncing.)
- **Overflow is safe** (rewards/rewarded refills can exceed the cap; regen just pauses).
- **Rewarded refill:** +25 Energy (see §8). **Non-ad alternative:** buy 25 Energy for 100 Coins.
- **Design intent:** energy is not a punishment — it is a gentle pacing tool and the main opt-in ad hook. Because the board persists and the Garden produces while away, running out of energy never feels like a loss.

### 4.5 Board-full / "rest" state
- The game has **no hard fail**. If the grid fills with no possible merge, show a friendly **"The garden is resting"** panel with options: **Use a booster** (Shovel = remove one plant; Mixer = shuffle), **Watch a rewarded video** for a free booster, or **Wait** (the Garden still produces). This is the natural **interstitial** moment.
- Rationale: a fail state compounds frustration and is not needed; the genre's tension is "protecting momentum," not losing.

### 4.6 Merge feel (the most important polish)
- On merge: both plants scale-pop, emit a **particle burst** matching the tier colour, play a rising **chime** (pitch rises with tier), and the new plant lands with a soft bounce.
- **Combo:** merging twice within 1.5 s shows a **"Bloom Chain ×2"** flair and adds a small coin bonus. This is the game's signature skill expression and its differentiator versus plain merge clones.

---

## 5. Orders (the coin engine)

- **3 active visitor orders** at a time, shown as speech bubbles on the side/bottom.
- An order asks for **1–3 plants** of specific tiers (initially tiers 1–4; scales up with player level).
- **Reward:** Coins = sum of requested plants' order values **× 1.5 [TUNE]**, plus XP, plus occasional bonus (a booster or a small energy pack).
- When delivered, the order is replaced by a new one after a short animation. A **"new orders in 5 s"** timer keeps flow; a **rewarded video can refresh orders instantly** (cap 1/day).
- **Difficulty pacing:** the tiers requested and quantities grow with player level so the exponential merge curve stays meaningful.

---

## 6. The Garden (idle meta) — the return hook

- **12 plots** on a separate Garden scene (unlocked progressively via the Bloom Meter / Coins).
- **Planting:** drop a merged plant (tier 2+) onto an empty plot. The planted plant **produces Coins per hour** based on its tier:
  - `coins/hour = 10 × 2^(tier−2)` → tier 2 = 10/h, tier 4 = 40/h, tier 6 = 160/h, tier 8 = 640/h. **[TUNE]**
- **Collection:** a "Collect" button gathers accrued Coins. Accrual continues **offline** up to an **offline cap** of **4 hours** at start (upgradeable to 8/12/24 h).
- **Rewarded "Double Harvest":** on return, offer a rewarded video to **double** the offline harvest (cap 1/day). Non-ad alternative: none needed (the base harvest is already granted) — this keeps the ad purely additive.
- **Garden upgrades (spend Coins):** +Energy cap, +regen speed, +Seed Pod level, +plots, +offline cap, +auto-producer speed.
- **Visual:** the Garden is the game's "home screen" between sessions — pretty, calm, aspirational; each plot visibly grows with its plant tier. This is what makes the game feel like a *place*, not just a puzzle.

---

## 7. Currencies, economy & progression

### 7.1 Currencies (deliberately minimal — Poki discourages dual economies)
- **Energy** — gating resource; regenerates; the main rewarded-ad hook.
- **Coins** — the single soft currency; earned from **orders** + **Garden idle production**; spent on **Garden upgrades** and **boosters**.
- **Gems** — hard currency, **only on IAP-enabled builds later** (hidden entirely on the Poki and GameDistribution builds). Earned in small amounts from daily gifts/achievements even before IAP, but the Poki/GD builds must **not** show any purchase UI.

### 7.2 Sources & sinks
| Currency | Sources | Sinks |
|---|---|---|
| Energy | regen, level-up refill, rewarded video, Garden upgrade, daily gift | Seed Pod taps |
| Coins | orders, Garden idle, merges (combo bonus), daily gift, achievements | Garden upgrades, boosters, non-ad energy refills |
| Gems (later) | IAP, rare achievements, events | energy, boosters, timer skips, cosmetics |

### 7.3 Progression curve **[TUNE]**
- **Player level (XP):** from merges and orders. Each level unlocks: a new Garden plot, a pod/energy upgrade tier, or a new order tier.
- **First session target:** reach tier 5 (Berry Bush), unlock 1–2 plots, plant the first Garden crop.
- **Session 2–3:** first offline harvest + double-harvest rewarded video.
- **Session 7:** ~6 plots, pod L2, tier 7 seen.
- **Long-term:** all plots, Golden Pod, World Tree, cosmetic themes.
- **Key rule:** keep the first **2 minutes** generous (fast merges, quick first order) and ramp the exponential cost later — "make the first minutes brilliant before making the tenth hour deep."

---

## 8. Boosters & power-ups

| Booster | Effect | Earned | Bought | Rewarded? |
|---|---|---|---|---|
| **Shovel** | Remove one plant from the grid | order bonuses, Garden | Coins | yes (board-rest) |
| **Mixer** | Shuffle all plants to new cells | order bonuses | Coins | yes |
| **Golden Tap** | Next 10 pod taps spawn +1 tier | milestones | Coins | yes |
| **Time Bloom** | Instantly clear the pod cooldown | daily gift | Coins | yes |
| **Lucky Bloom** | Spawn one random tier-4+ plant | rare | Coins | yes (cap 2/day) |
| **Double Harvest** | Double the offline Garden harvest | — | — | yes (cap 1/day) |

Rules: every rewarded booster also has a **coin-purchase alternative** (platform requirement); rewarded buttons show a **clear video icon**, are **never green**, and are never placed to trick the player.

---

## 9. Ad integration map (ads-first)

### 9.1 Rewarded video (primary earner — opt-in only)
| Placement | Reward | Daily cap | Diminishing returns | Non-ad alternative |
|---|---|---|---|---|
| Energy refill | +25 Energy | 3 | 25 → 15 → 10 | 100 Coins |
| Double offline harvest | ×2 Garden Coins | 1 | — | — (base already given) |
| Clear pod cooldown | reset cooldown | 2 | — | 50 Coins |
| Lucky Bloom | random tier-4+ plant | 2 | — | 150 Coins |
| Refresh orders | new order set now | 1 | — | 40 Coins |
| Board-rest booster | free Shovel/Mixer | 2 | — | 60 Coins |

**Rules (moderation-enforced):** always offer a "continue without watching" of the **same size/colour** as the watch button; never chain two ads for one reward; never place a rewarded button on an active gameplay screen; celebrate the reward; scale rewards with level.

### 9.2 Interstitials (volume — paced by the platform)
- **Signal** `commercialBreak()` / `requestAd('midgame')` at **natural breaks only**: board-rest, order-set completion, return-to-Garden, session end.
- **Never** at game start (platforms auto-handle start ads), **never** during active gameplay, **never** after a loss/failure, **never** two in a row.
- **Delay the first interstitial** until after ~2 minutes of play / after the second order.
- **Do not** implement your own frequency timer — Poki and Yandex pace server-side; CrazyGames auto-caps at **1 midgame ad / 3 min**; GameDistribution uses pre-roll (on the Play/Start button) + mid-roll (on Game Over/Replay/Menu). Request at every natural break and let the platform decide.

### 9.3 SDK events the game MUST fire (via Playgama Bridge)
- `bridge.initialize()` on boot → `platform.sendMessage('game_ready')` when the first playable frame is ready.
- `gameplayStart()` on first input / each new round / unpause.
- `gameplayStop()` on board-rest / pause / return-to-Garden / tab hidden.
- Interstitial at the breaks above; rewarded on explicit player action.
- **Mute + pause the game and disable input during any ad.**
- Subscribe to **pause** and **audio-state** events (Bridge raises them for interstitials, rewarded, tab switches) and handle them in one place.

### 9.4 Banners
- Optional, on the **Garden/menu** screen only (never over gameplay). Poki's Gamebar Display ads are automatic for portrait games — no work needed.

---

## 10. IAP map (wired now, hidden at launch)

- **Integrate** the Bridge `payments` module and (for CrazyGames) the `userId` flow from day one, but **hide all IAP UI** on the Poki and GameDistribution builds (they forbid it) and during CrazyGames Basic Launch.
- **SKUs (enable later on supported stores):** cosmetic garden themes, season pass, coin bundles, ad-free month. Typical web/merge price points: starter pack ~$1.99–4.99, mid bundles $9.99–13.99, pass $9.99–12.99, ad-removal $2.99–4.99.
- Gate by platform using Bridge's `payments.isSupported`.

---

## 11. LiveOps (a lightweight web-appropriate calendar)

Web players churn faster than mobile, so keep LiveOps **simple and frequent**, not sprawling:
- **Daily gift** (open once/day → coins/energy/gems).
- **Daily goal** (e.g. "merge 5 tier-4 plants" → bonus).
- **Weekly theme event** (a recoloured Garden theme + a boosted reward track for 7 days).
- **Seasonal event** (holiday skin + a limited plant tier) — 2 weeks.
- **Leaderboard** (optional; "tallest plant this week") via Bridge Leaderboards.
- Note: do **not** build the mobile-grade 100-events/month machine — that needs live-ops tooling a solo web game doesn't have. Start with the daily + weekly loops.

---

## 12. UI / UX — screen list & flow

1. **Boot/Preload** — logo + progress bar (progressive loading; stream Garden art after gameplay starts). Fire `game_ready` when playable.
2. **Merge board (home)** — grid, Seed Pod, energy bar, coin counter, Bloom Meter, 3 order bubbles, pause/settings. **This is the landing screen — no menu.**
3. **Garden** — 12 plots, plant/collect, upgrade panel, themes.
4. **Upgrades panel** — spend Coins on energy/pod/plots/offline-cap.
5. **Board-rest / booster panel** — the no-fail "rest" moment.
6. **Order complete** — reward animation + optional "double reward" rewarded offer.
7. **Settings** — sound/music toggles, language, privacy policy, restore.
8. **Daily gift / daily goal** popovers.

**Flow:** Boot → Merge board (play immediately). Navigation between Merge board and Garden via a single bottom tab bar. Every screen reachable in ≤1 tap from the board.

**UX rules:** big tappable targets (≥44 px), high-contrast UI, works in portrait and landscape, no text-heavy tutorials (use icons + a single animated hand hint on the first merge), all text localised.

---

## 13. Onboarding (first 60 seconds) — critical

1. **0–3 s:** game loads; the board is already populated with a few tier-1 plants and one glowing Seed Pod; an animated hand taps the pod → a Sprout appears.
2. **3–8 s:** the hand drags one Sprout onto another → merge → satisfying pop. **First merge within ~10 s.** Fire `first_merge` analytics.
3. **8–20 s:** a visitor order bubble appears asking for 2 Seedlings; the player merges to fill it; delivers; coins rain.
4. **20–40 s:** Bloom Meter fills → a Garden plot unlocks → a one-tap "plant here" moment.
5. **40–60 s:** first upgrade offered (spend coins on +energy). Player now understands: tap → merge → order → coins → upgrade → garden.
- **No** splash, **no** start menu, **no** text blocks. Teach by doing.

---

## 14. Art & audio direction

- **Art:** bright, colourful, cohesive 2D with soft shading; chunky readable shapes; each plant tier visibly "levels up" in beauty (a strong reward signal). Plants distinguishable by **shape as well as colour** (colourblind-safe). Avoid muted/pixel/dark palettes (they under-perform on web). All art bundled (no external requests).
- **UI:** rounded, friendly, high-contrast; a single accent colour for rewarded buttons (never green, always with a video icon).
- **VFX:** particle bursts on merge, floating "+coins", Bloom Meter fill glow, World Bloom celebration.
- **Audio:** short, rising chimes per tier; soft ambient Garden loop; satisfying "pop" on merge. Keep audio files small (Vorbis/Opus, mono). Respect the platform mute events during ads.
- **Thumbnails (prepare both static + animated):** bright, text-free, show a satisfying merge moment and the World Tree; these are the #1 driver of click-through.

---

## 15. Technical specification

### 15.1 Stack (recommended)
```
Engine:       Phaser 3.90 + TypeScript + Vite
Physics:      none needed (grid merge) — keep it physics-free for web performance & size
Rendering:    WebGL (Phaser AUTO); per-scene texture atlases (TexturePacker MaxRects);
              canvas scaled to min(devicePixelRatio, 2)
Platform SDK: Playgama Bridge (JS Core) — ONE integration for all six portals
Save:         Bridge Storage (get on boot, set on change); tiny versioned JSON;
              offline-earnings timestamp; poki_ignore-prefixed local-only keys
Analytics:    Bridge/portal events + ByteBrew JS SDK (custom events with sub-params)
Build:        Vite -> dist/ (tree-shaken, minified); Brotli on host; relative paths only
Size budget:  <=6 MB initial / <=12 MB total
Orientation:  portrait-first (9:16), scalable to 16:9
```
- **Why Phaser, not Unity/Godot:** a measured 2048-style game shipped at ~4.4 MB raw / **1.8 MB gzip** in Phaser+Vite vs ~40.5 MB raw / 12.3 MB gzip in Godot — and Unity games typically land only ~60–65% conversion-to-play because of build weight. Poki's initial-download budget is **<8 MB**, so a web-native engine is decisive.
- **Construct 3** is the no-code alternative if you prefer visual scripting (small exports, Poki addon, full mobile/safe-area handling).

### 15.2 Build-size budgets per portal
- **Poki:** target initial **<8 MB** (aim ≤5 MB); players leave if load >10 s.
- **CrazyGames:** initial ≤50 MB (≤20 MB for mobile homepage); total ≤250 MB; **≤1500 files**.
- **Yandex:** ≤100 MB uncompressed; single `index.html` at root; no spaces/Cyrillic in names.
- **GameDistribution:** HTTPS-ready; JS or Construct 2/3 (Unity WebGL allowed but heavy).
- **GameMonetize:** `.zip` with `index.html` at root; SDK mandatory.

### 15.3 Performance rules
- Bundle **everything** (Poki blocks external requests — no Google Fonts/CDNs). Use texture atlases (≤4096 px) to keep draw calls to 1–4 per scene.
- Cap draw calls to ~200–500/frame on mobile; scale render resolution to `min(devicePixelRatio, 2)`.
- Use Phaser 3.60+ mobile WebGL pipeline.
- **Progressive loading:** ship board + tutorial + first plants in the initial bundle; stream Garden art, later tiers and themes in the background after gameplay starts.

### 15.4 Save data (tiny, versioned)
```json
{
  "v": 1,
  "coins": 0, "energy": 50, "xp": 0, "level": 1,
  "grid": [ {"cell": 0, "tier": 2}, ... ],
  "podLevel": 1, "podCharge": 30, "podCooldownEndsAt": 0,
  "plots": [ {"plot": 0, "tier": 4}, ... ],
  "offlineCapHours": 4, "lastCollectTs": 0,
  "daily": {"giftClaimedTs": 0, "rewardedCounts": {}},
  "settings": {"sound": true, "music": true, "lang": "en"}
}
```
- Save on: merge milestone, order complete, upgrade purchase, plot change, app pause/exit.
- **Idle accrual:** on load, `accrued = ratePerHour × min((now − lastCollectTs)/3600, offlineCapHours)`; show "Welcome back — your garden grew X coins."

### 15.5 Analytics events (ByteBrew + portal `measure`)
`tutorial_start`, `first_merge`, `merge {tier}`, `merge_tier_reached {tier}`, `order_complete {tier}`, `board_rest`, `rewarded_offer_visible {placement}`, `rewarded_complete {placement}`, `idle_collect {offline_hours}`, `upgrade_purchased {type}`, `session_start/end {duration}`. Build a funnel: `tutorial → first_merge → tier_5 → tier_10`.

### 15.6 Platform event map (per portal, via Bridge)
| Portal | Key calls | Notes |
|---|---|---|
| Poki | `gameLoadingFinished`, `gameplayStart`, `gameplayStop`, `commercialBreak`, `rewardedBreak` | mute+disable input during ads; SDK events are QA-checked |
| CrazyGames | `game.gameplayStart/Stop`, `ad.requestAd('midgame'/'rewarded')`, mute support | SDK required for Full Launch |
| Yandex | `LoadingAPI.ready()`, `GameplayAPI.start/stop` | SDK mandatory for moderation |
| GameDistribution | `SDK_GAME_PAUSE` (pause+mute), `SDK_GAME_START`, `gdsdk.showAd()` | pre-roll + mid-roll mandatory |
| GameMonetize | `SDK_GAME_PAUSE/START/READY`, `sdk.showBanner()` | pause+mute pattern |
| Playgama | Bridge equivalents; interstitial required | Bridge is mandatory for Playgama |

---

## 16. Accessibility & localisation

- **Colourblind-safe:** distinguish plant tiers by **shape/pattern**, not colour alone.
- Support **mouse-only and touch**; provide a tap-source→tap-target merge alternative to dragging.
- Adjustable text size; readable fonts; subtitles/visual feedback for all audio cues.
- **English mandatory** (CrazyGames, GameDistribution). **Yandex requires automatic language detection** via the SDK at launch. Recommended language set: **EN, RU** first, then **TR, ZH, KO, HI, VI**. Language names written in their own language.

---

## 17. Content & compliance rules (must-pass)

- **PEGI 12 / all-ages**; family-friendly, wholesome; no violence, gambling, or adult themes.
- **No misused IP, no clones** — the merge + idle-garden hybrid is the originality argument (Poki rejects games that don't "add to diversity").
- **No third-party ads, no IAP UI on Poki/GD, no ad-block prevention, no external requests, no external account systems, no chat** (emoji only). Profanity filtering if any username input ever added.
- Must remain **fully playable with an ad-blocker active**.
- Provide a **privacy-policy URL** and an in-game privacy UI if anything links externally.

---

## 18. Store rollout & submission checklist

**Assets to prepare once, reuse everywhere:** static + animated thumbnails (512×512, 512×384, 200×120, 16:9 cover), a short trailer, description + controls text, privacy-policy URL, and a clean build (no debug/splash/outgoing links).

**Rollout order:**
1. **CrazyGames Basic Launch** — go live fast (SDK optional), gather metrics.
2. **GameDistribution + GameMonetize** — fast monetised distribution (mandatory pre/mid-roll on GD).
3. **Playgama** — Bridge submission; 24-hour testing; pushes to 100+ partners.
4. **Poki** — apply with the polished, original build; use free Playtesting to fix onboarding.
5. **Yandex Games** — SDK + Game Ready; ≤100 MB; moderation 3–5 days.
6. **CrazyGames Full Launch** — enable SDK + monetisation (consider the 2-month exclusivity +50%).
7. **itch.io** — showcase page (no ads; donations only) for discovery/portfolio.
8. **Later:** YouTube Playables, MSN, Discord, Huawei/Xiaomi via Bridge; switch on IAP on supported stores; optionally self-host with H5 Games Ads / Playgama Ad.

**Per-store checklist:** ✅ SDK events implemented ✅ works with ad-block ✅ portrait ✅ <size budget ✅ no external requests ✅ English + auto-language ✅ no IAP UI (Poki/GD) ✅ PEGI-12 content ✅ thumbnails ✅ privacy policy.

---

## 19. KPIs & success targets

| Metric | Target |
|---|---|
| Conversion-to-play (Poki) | ≥65% (aim 70%+) |
| Average playtime | ≥5 min (aim 7+) |
| First merge | <15 s after load |
| Initial load | <3 s on mid-tier mobile |
| Build size | ≤6 MB initial |
| Day-1 retention (portal-relative) | above platform average |
| Rewarded opt-in rate | ≥35% of sessions |
| Session length | 5–15 min |

---

## 20. Build roadmap (solo dev, ~4–6 weeks)

**Week 1 — Core.** Phaser+Vite scaffold; grid; tap-pod spawn; drag-merge; tier table; merge VFX/SFX; energy bar; portrait scaling. Deliverable: playable merge board.
**Week 2 — Loop.** Orders + coins; Bloom Meter; board-rest + boosters; onboarding (animated hand, first merge <15 s); analytics events. Deliverable: full core loop.
**Week 3 — Meta.** Garden scene; plots; idle accrual + offline cap; upgrades panel; save/load via Bridge Storage. Deliverable: return loop.
**Week 4 — Monetisation & SDK.** Playgama Bridge integration; all SDK events; rewarded placements + caps + coin alternatives; interstitials at breaks; ad-block test. Deliverable: ad-ready build.
**Week 5 — Polish & content.** Atlases; Brotli; size pass (<6 MB); daily gift/goal; localisation (EN/RU); colourblind-safe art; thumbnails. Deliverable: store-ready build.
**Week 6 — Launch & iterate.** Submit to CrazyGames/GD/GameMonetize/Playgama; fix QA feedback; then Poki + Yandex. Deliverable: live on multiple portals.

---

## 21. Risks & mitigations

| Risk | Mitigation |
|---|---|
| Crowded genre (merge/puzzle) | Original merge+idle-garden hybrid, plant theme, Bloom-Chain combo, polish |
| Low hit-rate even in hot genres | Keep MVP small, soft-launch, iterate on real metrics; expect to build more than one game |
| Low conversion from slow load | Web-native engine, <6 MB, progressive loading, first merge <15 s |
| Poki rejection (curation) | Soft-launch elsewhere first; iterate with Playtesting; keep it original |
| Ad fatigue hurting retention | Rewarded-first, interstitial only at breaks, platform-paced, no ad after failure |
| Earnings decay after feature | Light weekly/seasonal LiveOps; multiple titles over time |

**Go / No-Go: GO.** Highest-demand mechanic (merge), proven web demand with thin high-quality supply, perfect fit for the web rewarded-ad economy, and a fully web-native, low-risk build path.

---

## 22. Sources (methodology & evidence)

Store mechanics & rules: Poki for Developers (requirements/quality, SDK overview & HTML5 SDK, monetization, engagement, release process); CrazyGames docs (requirements intro/technical/gameplay/ads, ad-monetization guide, basic-launch metrics); GameDistribution (developer terms & guidelines, SDK); GameMonetize (developers, FAQ, SDK); Yandex Games SDK (requirements, SDK methods, monetization, i18n); Playgama (developers, Bridge SDK wiki, terms); itch.io (html5, payments).

Ads question: Google AdSense Help (AdMob vs AdSense vs Ad Manager; H5 Games Ads; Ad Placement API); Google Developers (H5 game structure); Playgama Ad (standalone solution, self-host monetization).

Genre & design teardown: Deconstructor of Fun, Naavik, GameRefinery, AppMagic, GameDevReports, Gamigion; game wikis (Gossip Harbor, Travel Town, Merge Mansion, Merge Dragons, EverMerge, Merge County, Seaside Escape, Piece of Cake, Designville, Farm Merge Valley); Wikipedia (Suika Game).

Economy & monetisation: Deconstructor of Fun ("Does Merge-2 Monetize Better Than Match-3?"), Game Economist Consulting ("The Economics of Merge-2 Games"), Gamigion ("The Ad Placement Playbook of Top Merge Games"), AppLixir (web eCPM/rewarded benchmarks), CrazyGames/Poki monetization docs, Unity interstitial best practices.

Technical: Phaser/Godot/Unity web-size benchmarks; Matter.js benchmarks; MDN WebGL best practices; Playgama Bridge docs; CrazyGames/Poki/Yandex technical requirements; ByteBrew & GameAnalytics docs.

*(All figures marked [TUNE] are starting values for balancing; validate against live analytics after soft launch.)*
