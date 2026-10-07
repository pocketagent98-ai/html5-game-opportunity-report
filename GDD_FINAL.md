# MERGE & BLOOM — FINAL Game Design Document (build-ready, every detail)

**Genre:** merge-2 hybridcasual puzzle + light idle "grow-a-garden" meta
**Platform:** HTML5 browser, portrait-first → Poki, CrazyGames, GameDistribution, GameMonetize, Yandex Games, Playgama (+ itch.io showcase)
**Monetisation:** **ADS ONLY at launch, served entirely by the platforms** (no external ad network, no AdMob/AdSense). IAP wired but hidden, enabled later on supported stores only.
**Target build:** ≤6 MB initial / ≤12 MB total · first gameplay ~3 s · 60 fps on a mid-range Android · PEGI 12 / all-ages
**Audience:** broad casual; core skews female 30+; family-friendly

> This is the final, superseding edition. It folds in the merge-genre teardown, the economy/ad teardown, a web build spec, a content/balancing reference, launch/ASO/revenue research, an implementation deep-dive, and a run economy simulation. Everything marked **[TUNE]** is a starting value to validate after soft launch. Hand this whole document to a developer or an AI coding assistant.

---

## 0. The ads answer (settled — read first)

**All ads are served by the platforms. You do not add any ad network.** On Poki, CrazyGames, GameDistribution, GameMonetize, Yandex Games and Playgama you integrate that platform's SDK (unified via **Playgama Bridge**), call the ad events, and the platform fills them and pays you a revenue share. Third-party ad systems are **forbidden** on all of them (Poki blocks external requests entirely; CrazyGames/GD/Yandex allow only their own SDK ads). **AdMob is a mobile-app tool and is irrelevant here.** Only if you self-host your own website would you use Google **AdSense H5 Games Ads** (`adBreak()`) or **Playgama Ad** — and even then, not AdMob.

**Consequence for this game:** no ad SDK to pick, no eCPM to negotiate, no ad-server to run — just implement the SDK events correctly (that *is* the monetisation). Revenue = plays × session length × impressions/session × platform eCPM × your share.

---

## 1. Master brief

> Build **Merge & Bloom**, a portrait HTML5 browser game. The player taps a **Seed Pod** to spawn plants onto a **6×6 grid**, then drags one plant onto an identical one to **merge it up a 12-tier chain** (Sprout → World Tree). Fulfilling **visitor orders** with merged plants earns **Coins**; every merge also feeds a **Bloom Meter** that unlocks **Garden plots** — a persistent idle meta that produces Coins over time, including offline. Coins buy upgrades (energy cap, regen, pod level, more plots, longer offline cap). Energy gates tapping; it regenerates and can be topped up by an optional rewarded video. The game must load in ~3 s, run at 60 fps on a mid-range phone, be fully playable in portrait and with an ad-blocker, and monetise via rewarded video (opt-in boosts) and interstitials (at natural breaks, paced by the platform). Build in **Phaser 3.90 + TypeScript + Vite**, integrate the **Playgama Bridge** SDK once, save via Bridge Storage, keep the build under 6 MB.

---

## 2. Master AI build prompt (copy-paste this to an AI coder)

```
You are a senior HTML5 game developer. Build a complete, production-ready browser game
called "Merge & Bloom" in Phaser 3.90 + TypeScript + Vite. Follow this spec exactly.

CORE
- Portrait 9:16 (scalable to 16:9). No physics engine. Pure grid + tweens.
- 6x6 grid of cells. Each cell holds a plant of tier 1..12 or is empty.
- Tap a Seed Pod to spawn a tier-1 plant into an empty cell. Costs 1 energy.
- Drag one plant onto an identical plant -> merge into tier+1 (max 12). Merging is free.
- No auto-merge. One consume per tile per resolution pass (guard with a canUpgrade flag).
- Merge = data first (pure model), then animate: move tween 150ms Cubic.easeInOut,
  pop scale 1.15 yoyo 90ms, particle burst, rising chime. Combo flair if two merges <1.5s.

TIERS (name, order value coins): 1 Sprout 1, 2 Seedling 3, 3 Bud 8, 4 Bloom 20,
5 Berry Bush 50, 6 Fruit Tree 120, 7 Glow Vine 300, 8 Crystal Bloom 750,
9 Golden Tree 1800, 10 Sunflower Crown 3500, 11 Aurora Tree 4000, 12 World Tree 6000.

ENERGY (soft pacing, never a hard wall)
- Cap 50 (upgradeable to 100). Regen +1 / 40s, pauses at cap. Overflow safe.
- Tapping the pod costs 1 energy. Merge is free.
- If energy hits 0: show a friendly panel; offer rewarded +25, or 100 coins, or wait.
  NEVER block the player with a paywall; the Garden still produces.

SEED POD (producer)
- Charge 30 taps then 60s cooldown (show a bar).
- Pod levels raise the spawn table: L1 100% t1; L2 90/10 t1/t2; L3 80/18/2;
  L4 70/24/5/1. Efficiency (tier-1 equivalents per energy): 1.00 / 1.10 / 1.24 / 1.46.

ORDERS
- 4 concurrent orders, difficulty-spread: 1 very easy, 2 medium, 1 hard.
- An order requests 1-3 plants of specific tiers (scales with player level).
- Reward coins = sum(item order values) * 1.5, plus XP.
- Delivered orders are replaced; a rewarded video can refresh orders instantly (cap 1/day).

GARDEN (idle meta)
- 12 plots, unlocked via Bloom Meter / coins. Plant a tier>=2 plant in a plot.
- Production: coins/hour = 10 * 2^(tier-2). Accrues offline up to a cap (4h start -> 24h).
- On load, credit min(elapsed, cap) * rate. Use epoch ms, clamp negatives to 0, ignore <60s.
- Rewarded "double offline harvest" (cap 1/day). Garden upgrades cost coins:
  +energy cap, +regen, +pod level, +plots, +offline cap, +auto-producer.

CURRENCY
- Energy, Coins (only soft currency at launch). Gems only on IAP builds later (hidden on Poki/GD).
- Coins come from orders + garden idle; spent on garden upgrades + boosters.

ADS (platform-served only; via Playgama Bridge)
- Rewarded (opt-in, with a coin alternative): +25 energy (cap 3/day), double offline
  harvest (1/day), clear pod cooldown (2/day), Lucky Bloom random t4+ plant (2/day),
  refresh orders (1/day), board-rest booster (2/day).
- Interstitial: signal at natural breaks only (board-rest, order set complete,
  return-to-garden, session end). Never at start, never mid-gameplay, never after a loss.
- Mute+pause during every ad. Never grant a reward on a failed ad.

SAVE
- Playgama Bridge Storage only (never raw localStorage). Versioned JSON envelope:
  { version, savedAt, state }. Write migrations as a chain of single steps.
- Save on meaningful change + on visibilitychange 'hidden' + throttled autosave.

ANALYTICS
- Events: tutorial_start, first_merge, merge{tier}, merge_tier_reached{tier},
  order_complete, board_rest, rewarded_complete{placement}, idle_collect{hours},
  upgrade_purchased{type}, session_start/end.

ARCHITECTURE
- src/game/model (pure TS, no Phaser, unit-tested), view (Phaser scenes), platform
  (thin Bridge adapter), config (all tunables), analytics (event facade), scenes.
- Scenes: Boot, Preload (asset manifest), Game (grid), UI (HUD overlay), Garden.
- Object pooling for plants/particles. Cap devicePixelRatio at 2. Texture atlases.
- Size budget <=6MB; fire game_ready only when the first playable frame is ready.

DELIVER: a runnable Vite project with the core loop, orders, garden, energy, ads hooks,
save/load, and unit tests for the pure model.
```

---

## 3. Design pillars

1. **Instant play** — no splash, no menu, first merge within ~10–15 s.
2. **Constant satisfying action** — a merge always feels good; no dead time.
3. **Web-first loops** — ~3-minute core loop; 5–15 minute sessions; nothing punishes leaving.
4. **Polite ads** — opt-in extras + breaks only; fully playable with an ad-blocker, and without ever watching an ad.
5. **Return reason** — the Garden keeps producing while away.
6. **Original** — a merge + idle-garden hybrid with a plant theme (not a fruit/2048 clone).

---

## 4. Core loops

**Core (≈3 min):** tap Seed Pod (costs energy) → drag identical plants to merge up tiers → fulfil visitor orders → earn Coins + XP → Bloom Meter fills → unlock a Garden plot/upgrade → when the board fills with no merge, the board "rests" (a friendly no-fail moment; the natural interstitial break).

**Meta (session-to-session):** spend Coins on Garden upgrades; the Garden produces Coins passively (incl. offline); collect on return, optionally double via rewarded video; daily gift + daily goal build habit.

**Long-term:** fill all 12 plots, upgrade the Seed Pod to Golden Pod, plant a **World Tree** (tier 12), unlock cosmetic garden themes.

---

## 5. Merge board

- **Grid:** 6×6 = 36 cells, portrait, cell size scales to viewport. No physics.
- **Interaction:** tap-pod to spawn; **drag-and-drop** to merge; a **tap-source → tap-target** alternative for accessibility. Drag threshold ignores micro-drags.
- **Merge rule:** dragging a plant onto an **identical** plant merges them into one plant of the next tier. Only identical tiers merge; **no auto-merge**. Merging is **free** (energy is spent on *producing*). Guard with a per-tile "canUpgrade" flag so a tile is consumed once per resolution pass; re-scan only after all movement tweens finish.
- **Top tier:** tier 12 does not merge further; combining two tier-12s triggers a **"World Bloom"** celebration + big coin bonus.

### 5.1 Merge chain (12 tiers) **[TUNE]**
| Tier | Name | Order value (Coins) | Base items (2^(t−1)) |
|---|---|---|---|
| 1 | Sprout | 1 | 1 |
| 2 | Seedling | 3 | 2 |
| 3 | Bud | 8 | 4 |
| 4 | Bloom | 20 | 8 |
| 5 | Berry Bush | 50 | 16 |
| 6 | Fruit Tree | 120 | 32 |
| 7 | Glow Vine | 300 | 64 |
| 8 | Crystal Bloom | 750 | 128 |
| 9 | Golden Tree | 1,800 | 256 |
| 10 | Sunflower Crown | 3,500 | 512 |
| 11 | Aurora Tree | 4,000 | 1,024 |
| 12 | **World Tree** | 6,000 | 2,048 |

> 12 tiers (not 10) is deliberate: the top item needs **2,048** base items, so building a World Tree is a genuine multi-day goal (§9), which keeps the merge curve meaningful on web. Order value ≈ ×2.4 per tier.

### 5.2 Merge feel (the most important polish)
Both plants scale-pop; a **particle burst** in the tier's colour; a **rising chime** (pitch rises with tier); the new plant lands with a soft bounce. **Bloom Chain:** merging twice within 1.5 s shows a "×2" flair and adds a small coin bonus — the game's signature skill expression.

---

## 6. Seed Pod (producer) & energy

### 6.1 Producer
- Tap → spawns a plant into a random empty cell (centre-biased). Cost **1 energy/tap**.
- **Charge:** 30 taps, then **60 s cooldown** (visible bar).
- **Pod levels** raise the spawn table (the genre's core efficiency upgrade):
| Pod | Spawn table | L1-equiv / energy |
|---|---|---|
| L1 | 100% t1 | 1.00 |
| L2 | 90% t1 / 10% t2 | 1.10 |
| L3 | 80% t1 / 18% t2 / 2% t3 | 1.24 |
| L4 | 70% t1 / 24% t2 / 5% t3 / 1% t4 | 1.46 |
- **Auto-Producer** (later unlock): a plot that spawns a base plant every N seconds, free (bridges the idle meta back to the board).

### 6.2 Energy — a soft pacing tool, **never a hard wall**
- **Cap 50** (upgradeable to 100); **regen +1 / 40 s**, pausing at the cap; overflow safe.
- **Important design lesson:** a shipped merge game that added an energy wall saw **D30 retention go DOWN** and IAP gain not compensate it — "players who can play when they want come back; players walled at the moment of engagement do not." So: keep energy **forgiving**, **delay the first energy gate**, and always offer a "parachute" (a small playable amount on cooldown) rather than a stop.
- **When energy hits 0:** show a friendly panel — **rewarded +25 energy**, **buy 25 for 100 Coins**, or **wait**. The Garden keeps producing regardless.
- **Rewarded refill** is the main opt-in ad hook (cap 3/day, diminishing 25→15→10).

---

## 7. Orders (the coin engine)

- **4 concurrent orders**, deliberately **difficulty-spread**: one very easy (completable in under a session), two medium, one hard (big reward). A 5th limited-time order can appear for an extra reward.
- An order requests **1–3 plants** of specific tiers; the tier/quantity **scales with player level** (progression-segmented order tables).
- **Reward:** Coins = sum(requested plants' order values) **× 1.5**, plus XP, plus occasional bonus (booster or small energy pack).
- **Refresh:** delivered orders are replaced after a short animation; a rewarded video can refresh instantly (cap 1/day). Add **cooldowns on hard orders** so a new session starts with fresh, completable goals.
- **Avoid the mid-game stall:** always keep the difficulty spread, so a player never has only day-long orders left. (This is exactly how Travel Town / Merge Mansion keep retention.)

### 7.1 Order formulas (for balancing)
- Level-1 equivalents of an item at tier T = **2^(T−1)**.
- Expected energy for an order = **(L1-equivalent demand) / (L1-equivalents per energy at the current pod level)**.
- Orders/day = **(daily energy spent × L1-equiv per energy) / (L1-equiv demand per order)**.
- **Rule:** keep order demand and pod efficiency rising *together* so expected energy per order stays roughly constant; if they drift apart the economy turns punitive.

### 7.2 Sample order table (pod L2 = 1.10 L1-equiv/energy) **[TUNE]**
| Band | Order | L1-eq demand | Energy | Coins |
|---|---|---|---|---|
| 1 | 3× T1 + 1× T2 | 5 | 5 | 9 |
| 2 | 3× T2 + 1× T3 | 10 | 9 | 26 |
| 3 | 3× T3 + 1× T4 | 20 | 18 | 66 |
| 4 | 3× T4 + 1× T5 | 40 | 36 | 165 |
| 5 | 3× T5 + 1× T6 | 80 | 73 | 405 |
| 6 | 2× T6 + 1× T7 | 128 | 116 | 810 |

---

## 8. The Garden (idle meta)

- **12 plots**, unlocked progressively via the Bloom Meter / Coins.
- **Plant** a tier ≥ 2 plant in a plot. **Production: coins/hour = 10 × 2^(tier−2).**
| Planted tier | Coins/hour | 8 h away |
|---|---|---|
| 2 Seedling | 10 | 80 |
| 4 Bloom | 40 | 320 |
| 6 Fruit Tree | 160 | 1,280 |
| 8 Crystal Bloom | 640 | 5,120 |
| 10 Sunflower Crown | 2,560 | 20,480 |
- **Offline accrual:** `credited = rate_per_hour × min(elapsed_hours, cap) × efficiency`. Offline cap **4 h** at start → upgradeable to **8 / 12 / 24 h**. Efficiency ~**80%** of active rate (common idle-game value). Ignore breaks < 1 minute.
- **Clock-cheat guards:** use **epoch ms** (`Date.now()`), clamp **negative elapsed to 0**, clamp to the cap, and ignore sub-minute gaps. Persist the timestamp in the save; write it on `visibilitychange = hidden`.
- **Rewarded "Double Harvest"** on return (cap 1/day) — the base harvest is always granted, so the ad is purely additive.
- **Garden upgrades (spend Coins):** +energy cap, +regen speed, +pod level, +plots, +offline cap, +auto-producer speed.
- **Visual:** the Garden is the calm, aspirational "home" between sessions; each plot visibly grows with its plant tier.

---

## 9. Economy, progression & balance

### 9.1 Currencies (deliberately minimal)
- **Energy** — gating/pacing; regenerates; the rewarded hook.
- **Coins** — the **single** soft currency (orders + Garden idle); spent on upgrades + boosters.
- **Gems** — hard currency **only on IAP builds later** (hidden entirely on Poki/GD).
> Poki discourages dual economies (they add confusion and grind), so the launch build has **one** soft currency.

### 9.2 Balance simulation (model, not measured) **[TUNE]**
Run from the economy model (pod efficiency, order tables, energy budget):
- **Energy/day:** regen +1/40 s ≈ **2,160/day** + rewarded 3×25 = **~2,235/day** theoretical.
- **Mid-game daily output** (band-4 orders, pod L2, 65% of energy spent): **~1,921 energy/day → ~53 orders/day → ~8,700 Coins/day.**
- **Top-tier build time:** one World Tree = 2,048 base items → **~1.4 days** at 2,235 energy/day (65% used). (With the 10-tier version it would be ~0.4 days — too fast, hence 12 tiers.)
- **Coin-gated upgrades** (cost = 250 × 1.55^(level−1)):
| Upgrade level | Cost (Coins) | Days at 8,700/day |
|---|---|---|
| L1 | 250 | 0.03 |
| L4 | 931 | 0.11 |
| L7 | 3,467 | 0.40 |
| L10 | 12,910 | 1.48 |
- **Real gates:** board space (36 cells), order demand consuming high tiers, coin-gated upgrades, and the pod charge/cooldown — not raw energy.
- **First session:** 50 starting energy ≈ 50 pod taps ≈ enough to complete band-1 orders and see up to ~tier 4.

### 9.3 Progression curve
- **XP** from merges + orders; each level unlocks a Garden plot, a pod/energy upgrade tier, or a new order band.
- **Session 1:** tier 4–5, 1–2 plots planted, first upgrade. **Session 2–3:** first offline harvest + double-harvest ad. **Session 7:** ~6 plots, pod L2, tier 7. **Long-term:** all plots, Golden Pod, World Tree, themes.
- Keep the **first 2 minutes** generous (fast merges, quick first order); ramp the exponential cost later.

---

## 10. Boosters & power-ups

| Booster | Effect | Earned | Bought | Rewarded? |
|---|---|---|---|---|
| **Shovel** | Remove one plant | order bonuses, Garden | Coins | yes (board-rest) |
| **Mixer** | Shuffle plants to new cells | order bonuses | Coins | yes |
| **Golden Tap** | Next 10 pod taps spawn +1 tier | milestones | Coins | yes |
| **Time Bloom** | Clear the pod cooldown | daily gift | Coins | yes |
| **Lucky Bloom** | Spawn a random tier-4+ plant | rare | Coins | yes (cap 2/day) |
| **Double Harvest** | ×2 the offline Garden harvest | — | — | yes (cap 1/day) |

Every rewarded booster also has a **coin-purchase alternative** (platform requirement). Rewarded buttons show a **clear video icon**, are **never green**, and are never trick-placed.

---

## 11. Ad integration map (ads-first, platform-served)

### 11.1 Rewarded video (primary earner — opt-in only)
| Placement | Reward | Daily cap | Diminishing | Non-ad alternative |
|---|---|---|---|---|
| Energy refill | +25 Energy | 3 | 25→15→10 | 100 Coins |
| Double offline harvest | ×2 Garden Coins | 1 | — | — (base given) |
| Clear pod cooldown | reset cooldown | 2 | — | 50 Coins |
| Lucky Bloom | random tier-4+ plant | 2 | — | 150 Coins |
| Refresh orders | new order set now | 1 | — | 40 Coins |
| Board-rest booster | free Shovel/Mixer | 2 | — | 60 Coins |

**Rules (moderation-enforced):** the "continue without watching" option must be the **same size/font/colour** as the watch button; never chain two ads for one reward; never place a rewarded button on an active gameplay screen; celebrate the reward; scale rewards with level; **grant the reward only when the ad completes** (never on a failed ad).

### 11.2 Interstitials (volume — paced by the platform)
- Signal at **natural breaks only**: board-rest, order-set completion, return-to-Garden, session end.
- **Never** at start, **never** during gameplay, **never** after a loss, **never** two in a row. Delay the first to after ~2 min / after the second order.
- **Do not** implement your own frequency timer — Poki and Yandex pace server-side; CrazyGames auto-caps at **1 midgame ad / 3 min**; GameDistribution uses pre-roll (Play/Start) + mid-roll (Game Over/Replay/Menu).

### 11.3 SDK events (via Playgama Bridge)
`bridge.initialize()` → `platform.sendMessage('game_ready')` when the first playable frame is ready → `gameplayStart()` on first input/new round/unpause → `gameplayStop()` on board-rest/pause/return-to-Garden/tab hidden → interstitial at breaks → rewarded on explicit action. **Mute + pause during any ad, and disable input.** Subscribe **once** to the platform `PAUSE_STATE_CHANGED` and `AUDIO_STATE_CHANGED` events (they fire for interstitials, rewarded, tab switches) and handle them in one place. Also read `isAudioEnabled` once at start.

| Portal | Key calls |
|---|---|
| Poki | `gameLoadingFinished`, `gameplayStart`, `gameplayStop`, `commercialBreak`, `rewardedBreak` |
| CrazyGames | `game.gameplayStart/Stop`, `ad.requestAd('midgame'/'rewarded')`, mute support |
| Yandex | `LoadingAPI.ready()`, `GameplayAPI.start/stop` |
| GameDistribution | `SDK_GAME_PAUSE` (pause+mute), `SDK_GAME_START`, `gdsdk.showAd()` |
| GameMonetize | `SDK_GAME_PAUSE/START/READY`, `sdk.showBanner()` |
| Playgama | Bridge equivalents (interstitial required) |

### 11.4 Banners
Optional, on the Garden/menu screen only. Poki's Gamebar Display ads are automatic for portrait games.

---

## 12. IAP map (wired now, hidden at launch)

- Integrate Bridge `payments` and (CrazyGames) the `userId` flow from day one, but **hide all IAP UI** on Poki/GD and during CrazyGames Basic Launch.
- **SKUs (later, supported stores only):** cosmetic garden themes, season pass, coin bundles, ad-free month. Typical web/merge price points: starter $1.99–4.99, mid $9.99–13.99, pass $9.99–12.99, ad-removal $2.99–4.99.
- Gate by platform via Bridge `payments.isSupported`.

---

## 13. LiveOps (lightweight, web-appropriate)

Web players churn faster than mobile, so keep LiveOps **simple and frequent**, not sprawling:
- **Daily gift** — 7-day looping calendar, day-7 meaningfully better; **soft reset** (one free miss) beats a hard reset; use server/epoch day boundaries.
- **Daily goal** — 3–6 rotating tasks (merge X, complete N orders, collect the Garden); completing all grants a bonus. (Genre norm is ~5 tasks/day.)
- **Weekly theme event** — a recoloured Garden theme + a boosted reward track for 7 days.
- **Seasonal event** — holiday skin + a limited plant tier, ~2 weeks.
- **Leaderboard** (optional) — "tallest plant this week" via Bridge Leaderboards.
- Do **not** build the mobile-grade 100-events/month machine; start with daily + weekly loops.

---

## 14. Retention mechanics

- **Daily gift streak** with soft reset and a grace period; churn risk peaks at days 3, 7, 14 → make day 7 the checkpoint.
- **Comeback rewards** — never punish a returning player; show what they can earn today (the offline harvest), not what they lost.
- **Bloom Meter** as a visible short-term goal; **Garden growth** as the long-term one.

---

## 15. UI / UX

1. **Boot/Preload** — logo + progress bar; progressive loading; fire `game_ready` when playable.
2. **Merge board (home)** — grid, Seed Pod, energy bar, coin counter, Bloom Meter, order bubbles, pause/settings. **The landing screen — no menu.**
3. **Garden** — 12 plots, plant/collect, upgrade panel, themes.
4. **Upgrades panel** — spend Coins.
5. **Board-rest / booster panel** — the no-fail "rest" moment.
6. **Order complete** — reward animation + optional "double reward" rewarded offer.
7. **Settings** — sound/music, language, privacy policy.
8. **Daily gift / daily goal** popovers.

**Rules:** big tap targets (≥44 px), high-contrast UI, portrait + landscape, no text-heavy tutorials, everything localised, every screen ≤1 tap from the board. Use safe-area insets for notches (Bridge `device.safeArea`).

---

## 16. Onboarding (first 60 seconds)

1. **0–3 s:** load; the board is pre-populated with a few tier-1 plants and a glowing Seed Pod; an animated hand taps the pod.
2. **3–8 s:** the hand drags one Sprout onto another → merge → pop. **First merge within ~10–15 s.** Fire `first_merge`.
3. **8–20 s:** a visitor order asks for 2 Seedlings; the player merges to fill it; delivers; coins rain.
4. **20–40 s:** Bloom Meter fills → a Garden plot unlocks → one-tap "plant here".
5. **40–60 s:** first upgrade offered (spend coins on +energy).
- **No** splash, **no** menu, **no** text blocks. Teach by doing (visual cues only). Introduce new elements gradually, one focused tutorial per major unlock (rapid mechanic-dumping is the #1 FTUE churn cause).

---

## 17. Art & audio

- **Art:** bright, colourful, cohesive 2D with soft shading; chunky readable shapes; each tier visibly more beautiful (a reward signal). Plants distinguishable by **shape as well as colour** (colourblind-safe). Avoid muted/pixel/dark palettes (they under-perform on web). All art bundled.
- **UI:** rounded, friendly, high-contrast; one accent colour for rewarded buttons (never green, always with a video icon).
- **VFX:** particle bursts, floating "+coins", Bloom Meter glow, World Bloom celebration. Watch transparent overdraw (the classic mobile particle killer).
- **Audio:** short rising chimes per tier; soft ambient Garden loop; a satisfying merge pop. Small files (Vorbis/Opus, mono). Respect platform mute.
- **Thumbnails (static + animated):** bright, **text-free**, show a satisfying merge and the World Tree. This is the **#1 driver of click-through** on both Poki and CrazyGames.

---

## 18. Technical specification

### 18.1 Stack
```
Engine:       Phaser 3.90 + TypeScript + Vite
Physics:      NONE (grid merge) — the single biggest mobile-CPU saving
Rendering:    WebGL (Phaser AUTO); per-scene texture atlases; devicePixelRatio capped at 2
Platform SDK: Playgama Bridge (JS Core) — ONE integration for all six portals
Save:         Bridge Storage (get on boot, set on change); versioned JSON envelope
Analytics:    Bridge/portal events + ByteBrew JS SDK (custom events w/ sub-params)
Build:        Vite -> dist/ (tree-shaken, minified); Brotli on host; relative paths only
Size budget:  <=6 MB initial / <=12 MB total
Orientation:  portrait-first (9:16), scalable to 16:9
```
Why Phaser, not Unity/Godot: a measured 2048-style game shipped at ~**1.8 MB gzip** in Phaser+Vite vs ~**12.3 MB gzip** in Godot, and Unity games typically land only ~60–65% conversion-to-play because of build weight. Poki's initial-download budget is **<8 MB**. Construct 3 is the no-code alternative.

### 18.2 Build-size budgets
- **Poki:** initial **<8 MB** (aim ≤5 MB); players leave if load >10 s.
- **CrazyGames:** initial ≤50 MB (≤20 MB for the mobile homepage); total ≤250 MB; **≤1500 files**.
- **Yandex:** ≤100 MB uncompressed; single `index.html` at root; no spaces/Cyrillic in names.
- **GameDistribution:** HTTPS-ready; JS or Construct 2/3.
- **GameMonetize:** `.zip` with `index.html` at root; SDK mandatory.

### 18.3 Architecture (module split)
- `model/` — **pure TS, zero Phaser imports** (grid, merge rules, economy, idle maths, save schema + migrations) → unit-testable.
- `view/` — Phaser scenes/GameObjects, tweens, pooling, input binding.
- `platform/` — one thin adapter over Bridge: `init`, `getSave`, `setSave`, `sendMessage`, `onPause`, `onAudio`, `showInterstitial`, `showRewarded`, `getLang`.
- `config/` — all tunables (tiers, drop tables, costs, idle rates).
- `analytics/` — event facade.
- `scenes/` — Boot, Preload (asset manifest), Game, UI (overlay), Garden.
- **Pure model functions:** `canMerge(grid, from, to)`, `applyMerge(...) → MergeResult`, `scoreForTier(t)`, `idlePerSecond(state)`, `applyOffline(state, now) → {state, gained, seconds}`, `serialize/deserialize/migrate`. Pass a **seeded RNG** into random functions for reproducible tests.

### 18.4 Save data (versioned)
```json
{ "version": 1, "savedAt": 0,
  "state": {
    "coins": 0, "energy": 50, "xp": 0, "level": 1,
    "grid": [ {"c":0,"tier":2} ],
    "podLevel": 1, "podCharge": 30, "podCooldownEndsAt": 0,
    "plots": [ {"p":0,"tier":4} ], "offlineCapHours": 4, "lastCollectTs": 0,
    "daily": {"giftClaimedTs":0, "rewardedCounts":{}},
    "settings": {"sound":true,"music":true,"lang":"en"}
  } }
```
- **Migrations:** a chain of single-step `migrations[n]` (v(n−1)→v(n)); apply in order on read; append-only. Add `version` from the very first build.
- **Portal limits:** CrazyGames Data ≤ **1 MB** JSON; Poki cloud saves ≤ **1 MB gzip**. Keep the payload tiny (a 6×6 grid + scalars is trivial; never log analytics into the save).
- **Backing differences:** Poki/GD emulate storage with device-local localStorage (not synced); CrazyGames syncs when logged in; Yandex/YouTube are cloud-backed. Always be able to boot from defaults; wrap in try/catch (incognito restricts localStorage); never assume a write landed synchronously.
- **Throttle writes** (save on meaningful change; CrazyGames debounces ~1 s); write `savedAt` on `visibilitychange = hidden`.

### 18.5 App state machine & lifecycle
States: `BOOTING → LOADING → MENU → PLAYING → PAUSED → AD → GAME_OVER`. Hook Phaser's `Core.Events` (`HIDDEN`, `VISIBLE`, `PAUSE`, `RESUME`) and Bridge's `PAUSE_STATE_CHANGED`/`AUDIO_STATE_CHANGED`. On pause: stop timers, mute, write `savedAt`. On resume: unmute (if allowed), advance the idle sim by elapsed time in **fixed steps**, re-sync the HUD. Never let one huge resume delta drive the sim.

### 18.6 Performance rules
- **Zero steady-state allocation** in hot paths (no per-frame `.map/.filter/slice/spread`, no fresh `{x,y}` in loops) — GC hitches read as "lag".
- **Texture atlases** (≤4096 px, 1–2 px padding) → 1–4 draw calls/scene; mobile ceiling ~150–400 draw calls/frame.
- **Cap devicePixelRatio at 2** (the single biggest mobile lever).
- Object pooling for plants/particles (pool only if you measure a problem). Kill global tweens in scene shutdown.
- Only make draggable pieces interactive; use a drag threshold.
- Test on a 2–3 year-old mid-range Android (DevTools emulation doesn't show real GPU performance).

### 18.7 Analytics & KPI targets
Events: `tutorial_start`, `first_merge`, `merge{tier}`, `merge_tier_reached{tier}`, `order_complete`, `board_rest`, `rewarded_offer_visible/complete{placement}`, `idle_collect{hours}`, `upgrade_purchased{type}`, `session_start/end{duration}`. Funnel: `tutorial → first_merge → tier_5 → tier_10`.

| KPI | Target |
|---|---|
| Conversion-to-play (Poki) | ≥65% (aim 70%+) |
| Average playtime | ≥5 min (aim 7+) |
| First merge | <15 s |
| Initial load | <3 s mid-tier mobile |
| Build size | ≤6 MB initial |
| Rewarded opt-in | ≥35% of sessions |
| Session length | 5–15 min |

---

## 19. Accessibility & localisation

- **Colourblind-safe:** distinguish tiers by **shape/pattern**, not colour alone.
- Support **mouse-only and touch**; provide tap-source→tap-target as a drag alternative.
- Adjustable text; readable fonts; visual feedback for audio cues; safe-area padding.
- **English mandatory** (CrazyGames, GD). **Yandex requires automatic language detection** at launch. Language set: **EN, RU** first → **TR, ZH, KO, HI, VI**. Language names in their own script.

---

## 20. Content & compliance rules (must-pass)

- **PEGI 12 / all-ages**, family-friendly; no violence, gambling, or adult themes.
- **No misused IP, no clones** — the merge + idle-garden hybrid is the originality argument (Poki rejects games that don't "add to diversity").
- **No third-party ads, no IAP UI on Poki/GD, no ad-block prevention, no external requests, no external accounts, no chat** (emoji only).
- Must be **fully playable with an ad-blocker active**.
- Provide a **privacy-policy URL** and an in-game privacy UI if anything links externally.

---

## 21. Launch, discovery & revenue (ads-only, platform-served)

### 21.1 The exclusivity decision (decide first)
- **Poki's default deal is web-exclusive for 5 years** (open web only; Steam/app stores/consoles stay yours) and pays **100%** on traffic you bring, **50/50** on Poki-sourced traffic. Taking it means **not** releasing the same game on other web portals.
- **CrazyGames** offers **+50%** for a **2-month** launch exclusivity — this **conflicts** with Poki's web exclusivity. You cannot take both.
- **Recommendation for a first title:** launch **non-exclusive** across CrazyGames + GameDistribution + GameMonetize + Playgama + Yandex (stacked reach), *unless* you believe the game is strong enough to pursue a Poki exclusive (or a CrazyGames "Originals" deal). Syndication usually beats one exclusive for a first-timer; an exclusive only pays if it comes with real promotion.

### 21.2 Launch sequence
1. **CrazyGames Basic Launch** (SDK optional, no monetisation) → gather metrics → Full Launch.
2. **GameDistribution + GameMonetize** (fast, monetised; GD needs pre-roll + mid-roll).
3. **Playgama** (Bridge submission, ~24 h review, pushes to 100+ partners).
4. **Poki** — apply with the polished build; use free Playtesting + the web-fit test (~10,000 players scoring CTR, time-on-page, conversion-to-play). Soft release (2–3 weeks, non-skippable) → global.
5. **Yandex Games** — SDK + Game Ready; moderation 3–5 days; localise RU+EN.
6. **itch.io** — showcase only (no ads → **$0 ad revenue**; donations possible).
7. Later: YouTube Playables, MSN, Discord, Huawei/Xiaomi via Bridge; enable IAP on supported stores.

### 21.3 Discovery (ASO)
- **Thumbnail is the #1 CTR lever** on both Poki and CrazyGames — text-free, gameplay-representative, bright. A weak thumbnail directly suppresses traffic.
- **Naming:** put the mechanic first — e.g. **"Merge & Bloom: Garden Merge"**; reserve the subtitle for the sharpest modifier (cozy / idle / no-ads). Browser portals rank mostly by **engagement**, so titles/tags feed search + curation but don't override performance.
- **How you get featured:** Poki ranks on the web-fit test metrics + the promotion system; CrazyGames gives every new game **48 guaranteed homepage hours** then ranks on engagement; Yandex ranks via ML (New section <30% of traffic); Playgama reviews in ~24 h and syndicates.

### 21.4 Revenue model & projections (MODELLED)
`revenue = plays × impressions/session × gross eCPM × share ÷ 1000`.
Assumptions: **2.5 impressions/session** (range 1.5–4.0); 60/40 interstitial/rewarded mix; geo blend 30/40/30 tier-1/2/3 → blended gross eCPM ≈ **$4.68** (US-heavy ≈ $6.33).

| Portal (share) | 10k plays/mo | 100k plays/mo | 1M plays/mo |
|---|---|---|---|
| Poki platform-sourced (50%) | $58 | $585 | $5,850 |
| Poki direct-traffic (100%) | $117 | $1,170 | $11,700 |
| CrazyGames (~50–60%) | $58–70 | $585–702 | $5,850–7,020 |
| GameDistribution (33%) | $39 | $386 | $3,861 |
| GameMonetize (45%) | $53 | $526 | $5,265 |
| Playgama partner ladder (~80%) | $94 | $936 | $9,360 |
| Yandex (unified; ad share not published) | — | — | — |

**Reality checks:** the shape is **spike-while-featured-then-decay** (a documented title went ~$50/day → ~$5/day as placement cooled). **Geo mix dominates** (tier-3 eCPM is a fraction of tier-1). **Placement, not talent, gates everything** — most games never get placement and sit at $0. Honest first-game band: **~$50–$1,500/month**, with ~$400/mo a realistic 100k-plays case; a strong portfolio of 3–10 small titles compounds.

---

## 22. Build roadmap (solo dev, ~4–6 weeks)

**W1 — Core.** Phaser+Vite scaffold; 6×6 grid; tap-pod spawn; drag-merge; 12-tier table; merge VFX/SFX; energy bar; portrait scaling.
**W2 — Loop.** 4-slot orders + coins; Bloom Meter; board-rest + boosters; onboarding (<15 s first merge); analytics events.
**W3 — Meta.** Garden scene; 12 plots; idle accrual + offline cap + clock guards; upgrades panel; save/load + migrations via Bridge Storage.
**W4 — Monetisation & SDK.** Bridge integration; all SDK events; rewarded placements + caps + coin alternatives; interstitials at breaks; ad-block test.
**W5 — Polish.** Atlases; Brotli; size pass (<6 MB); daily gift/goal; localisation (EN/RU); colourblind-safe art; thumbnails.
**W6 — Launch.** Submit to CrazyGames/GD/GameMonetize/Playgama; fix QA; then Poki + Yandex.

---

## 23. Risks & mitigations

| Risk | Mitigation |
|---|---|
| Crowded genre | Original merge+idle-garden hybrid, plant theme, Bloom-Chain combo, polish |
| Low hit-rate even in hot genres | Small MVP, soft-launch, iterate; expect to ship more than one game |
| Low conversion from slow load | Web-native engine, <6 MB, progressive loading, first merge <15 s |
| Poki rejection | Soft-launch elsewhere first; iterate with Playtesting; keep it original |
| Energy wall hurting retention | Forgiving energy, delayed first gate, always a non-ad path (lesson from a shipped merge game's D30 drop) |
| Ad fatigue | Rewarded-first, interstitial only at breaks, platform-paced, no ad after failure |
| Earnings decay | Light weekly/seasonal LiveOps; ship a portfolio |
| Geo mix diluting eCPM | Chase Tier-1 traffic; localise; keep sessions long |

**Go / No-Go: GO.** Highest-demand mechanic (merge), proven web demand with thin high-quality supply, perfect fit for the web rewarded-ad economy, and a fully web-native, low-risk build path.

---

## 24. Sources (evidence base)

**Store mechanics & rules:** Poki for Developers (requirements/quality, SDK overview & HTML5 SDK, monetization, engagement, release process, web-fit-test, thumbnails, payouts); CrazyGames docs (requirements intro/technical/gameplay/ads, ad-monetization guide, basic-launch metrics, data module, covers, FAQ) and Developer Terms (Aug 2025); GameDistribution (developer terms & guidelines, partnership, SDK); GameMonetize (developers, FAQ, SDK); Yandex Games SDK (requirements, SDK methods, monetization, purchases, i18n, moderation, ranking); Playgama (developers, Bridge SDK wiki — setup/storage/advertisement/platform/device, terms); itch.io (html5, payments, faq).

**Ads question:** Google AdSense Help (AdMob vs AdSense vs Ad Manager; H5 Games Ads; Ad Placement API); Google Developers (H5 game structure); Playgama Ad (standalone solution, self-host monetization).

**Genre & design teardown:** Deconstructor of Fun, Naavik, GameRefinery, AppMagic, GameDevReports, Gamigion, PocketGamer.biz; game wikis (Gossip Harbor, Travel Town, Merge Mansion, Merge Dragons, EverMerge, Merge County, Seaside Escape, Piece of Cake, Designville, Farm Merge Valley); Wikipedia (Suika Game).

**Economy & monetisation:** Deconstructor of Fun ("Does Merge-2 Monetize Better Than Match-3?"), Game Economist Consulting ("The Economics of Merge-2 Games" / "What Merge-2 Economics Is Missing"), Gamigion ("The Ad Placement Playbook of Top Merge Games"), AppLixir (web eCPM/rewarded benchmarks), PixelGameCraft (HTML5 monetization 2026), CrazyGames/Poki monetization docs, Unity interstitial best practices, Byteager ("A Year Building a Merge Game").

**Launch, ASO & revenue:** Poki release process & web-fit-test; CrazyGames Basic Launch guide; Yandex ranking/moderation docs; Playgama distribution docs; portalready.world exclusivity comparison; developer post-mortems (Blumgi, Emolingo, Artem Lanin, Mickael Bergeron Neron, Gamezdev).

**Implementation:** phaserjs/template-vite-ts; ourcade template; sgbj/suika-clone; devshareacademy/phaser-4-suika-game; moonfloof/suika-game; Emanuele Feronato (4096, DragAndMatch); qinkangwu/phaser3-typescript-2048; MockingFinch/tile-matching-game; deadronos/vibe-idle-bricks; sf-play/bake-tycoon; IdleKit save/offline docs; Phaser docs (Input, Core.Events, Audio, Scale); MDN WebGL best practices; Playgama Bridge storage/platform/device docs; CrazyGames data-module docs; Poki cloud-save docs.

*(Every [TUNE] value is a starting point; validate against live analytics after soft launch. Revenue figures are modelled, not guaranteed.)*
