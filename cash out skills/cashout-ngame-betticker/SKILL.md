---
name: cashout-ngame-betticker
description: Implement, explain, or verify how Cash Out integrates with N-Game (the live trading platform) and Bet Ticker (the live bet ticker). Use this skill whenever the user mentions N-Game game settings, the per-game Cashout toggle, the "Cashout: On" market flag, the Cash Out Settings module, the master Live + Pre toggle in Maintenance, denying Cash Out for a player via Manage Players, the Action Log, or the Bet Ticker showing cashed-out bets with status / amount / odd info and a configurable highlight when Cashout Amount > Risk. Also use when the user asks how Trading enables / disables Cash Out per game, globally, or per customer in N-Game.
---

# Cash Out — N-Game & Bet Ticker

N-Game is the **live trading platform**. Bet Ticker is the **live bet ticker** that streams real-time alerts to traders — including cashed-out bets that occur once the game is in-play.

> **Routing rule for tickers:**
> - Bet cashed out **before the game starts** → shown in **Instant Action** (Synergy side, see [cashout-synergy-instantaction](../cashout-synergy-instantaction/SKILL.md))
> - Bet cashed out **after the game has started** → shown in **Bet Ticker** (N-Game side — this skill)
>
> Determined by **game state at cashout time**, not placement time.

---

## N-Game

### 1. Game Settings & Info

#### 1a. Enable / Disable Cash Out per Game

- Within the N-Game game view, the Game Actions panel includes a **Cashout** toggle (On / Off).
- Toggling Cash Out **at game level** applies to **every market within that game** that has the feature **ON from Sasha** (the back-office Cash Out Management tool).
- Markets that are switched OFF upstream in Sasha do not get cashout enabled even if the per-game N-Game toggle is ON — Sasha is the canonical enable source; the N-Game toggle is a per-game override.

#### 1b. Cash Out Flag

- When Cash Out is enabled for a game, every market in that game with the feature available shows a **"Cashout: On"** flag in the market header row.
- Flag appears alongside other market-state indicators (Active / Activate / Freeze / Replace / Move / More).
- Visible in the market list view per game so traders can scan which markets in a live game have cashout currently offered.

#### 1c. Liabilities Updated in Real Time

- Liabilities for every market are updated in real time as bets are cashed out — Stake values are unchanged, Liability values are recalculated.
- For full calculation rules, see [cashout-reporting](../cashout-reporting/SKILL.md).

#### 1d. Action Log

- Every Cash Out enable / disable action (per game, per market, master toggle, per-player) is recorded in the N-Game Action Log for audit.
- _(Detailed Action Log entry format / columns — TBD; not documented in the source guide.)_

---

### 2. Cash Out Settings — Master Toggle

Path: `Maintenance → Cashout Settings → Cashout: Enable/Disable General Cashout (Live and Pre)`

- A single On / Off toggle that controls the master Cash Out switch across **both Live and Pre** at the platform level.
- When OFF, Cash Out is suppressed system-wide regardless of per-game or per-player settings.
- The Maintenance menu also exposes: Manage Players, Manage Bet Amount, Manage Users, Manage Roles, Client Settings, Video & Tracker Settings, Country Profile Settings, **Cashout Settings**.

---

### 3. Manage Players — Per-Player Deny

Path: `Maintenance → Manage Players → Find a Player → Edit → Deny Cashout` (checkbox)

- Open the Find a Player screen, search by Account No. or Host Player ID.
- Edit the player's profile.
- Tick the **Deny Cashout** checkbox (sits alongside LiveStream, BP Match Tracker, Wiseguy, Rating, Additional delay, BetTicker visibility).
- Save.

When `Deny Cashout = true`:
- The cashout option is hidden from the customer's site for every game.
- The cashout engine rejects any cashout request that arrives for this player.

This is the **N-Game-side** equivalent of the Synergy "Deny Cashout Across all Games" flag — depending on the platform's data model, the two flags may either be a single property surfaced in both tools or two parallel flags that both need to be cleared. _(Confirm with Trading whether the flag is shared or independent.)_

---

## Bet Ticker

Live bet ticker for traders. Real-time stream of betting events (Bet Sold, Bet Cashed Out, etc.) once games are in-play.

Access: `N-Game Bet Ticker` (top nav, alongside `N-Game Admin`).

### 1. Cash Out Alerts

Cashed-out bets appear in the Bet Ticker stream with a new alert format. Each row contains:

| Column | Example | Notes |
|---|---|---|
| **Status** | `Bet Cashed Out` | Distinct status from `Bet Sold` — visually flagged |
| Time | `05:46:30 AM` | When the cashout was effectuated |
| Customer | `B2661024` | Customer ID |
| Risk | `Risk: 10.00` | Original wager amount placed |
| **Cashed out amount** | `CashOut: 10.09` | Amount returned to the player |
| Game | `WAS vs. PIT` | Match |
| Market | `Moneyline` | Market the bet was on |
| **Initial odd \ new odd** | `WAS -110\-150` | Original placement odd `\` odd used at cashout |
| Net P/L | `Net P/L: $-169.00` | Running customer P/L (existing field, unchanged) |

The three new pieces of info specific to cashed-out alerts are:
1. **Status** — `Bet Cashed Out` (vs `Bet Sold`)
2. **Amount** — `CashOut: $X.XX` shown in the same column slot where `To Win: $X.XX` appears for normal bets
3. **Initial odd / new odd** — backslash-separated, e.g. `-110\-150`, so the trader can see how far the price moved between placement and cashout

The header bar (`Connected`, `Disconnect`, `Clear`, `Settings`, `Scrollable`, `Show P & L`) and the existing filters (`Wager History`, `IA V3`, `CustomerID`, `Customer Type`, `Bet Type`, `Store`, `Apply Filters`, `Board Market`, `Custom Filter`, `Clear All Filters`) all continue to work.

### 2. Bet Ticker Settings — Highlight Profitable Cashouts

Path: `Bet Ticker → Settings → Bet Message Colors`

A new **Cashout Setting** block in the Settings dialog lets traders **highlight cashed-out bets where the cashout amount is greater than the original risk** — i.e. cases where the customer pulled out at a profit, which is the operationally interesting subset for risk monitoring.

| Field | Behaviour |
|---|---|
| **Cashout Amount > Risk** label | Indicates the rule the colour applies to |
| **Background** | Trader-selectable background colour for the row when Cashout > Risk |
| **Foreground** | Trader-selectable foreground (text) colour |

Result: in the live ticker stream, every cashed-out bet where the customer cashed out at a profit gets a custom colour, drawing trader attention to those rows so they can monitor risk on profitable-cashout patterns.

Settings dialog also exposes the standard Bet Ticker controls, all of which continue to work alongside the new Cashout Setting:

- **Bet Amount Filter** — filter by amount threshold (Either / Smaller / Larger; `>= 0`)
- **Bet Message Colors** — generic colour rules for `Bet posted`, `Bet on hold`, etc.
- **Prop Type Filter** — Point Spread, Total, General
- **Assigned Trader Filter**
- **Sport / League Filter**

The Cashout Setting integrates with these — it adds a colour rule that fires conditional on the Cashout-Amount-vs-Risk comparison, on top of any other filters in effect.

---

## Reference flow

```
Bet placed (pre or live)
    │
    ▼
Cashout requested (live game in progress)
    │
    ▼
Cashout completes
    │
    ├─ Bet Ticker alert (N-Game) — italic row with new format
    │     ├─ Status: Bet Cashed Out
    │     ├─ Amount: CashOut: $X.XX
    │     └─ Odds: original \ cashout
    │
    ├─ N-Game game view: Liability column updates in real time
    │
    └─ If "Cashout Amount > Risk" colour rule is set
          └─ Row highlighted with trader-chosen colours
```

---

## Related skills

- [cashout-reporting](../cashout-reporting/SKILL.md) — N-Game and Synergy liability calculations updated when bets are cashed out
- [cashout-synergy-instantaction](../cashout-synergy-instantaction/SKILL.md) — Synergy (pre-match trading platform) + Instant Action counterpart to this skill
- [cashout-backoffice](../cashout-backoffice/SKILL.md) — "Sasha" / Cash Out Management tool that drives sport / league / template configuration upstream of N-Game
- [cashout-formula](../cashout-formula/SKILL.md) — pricing formulas that produce the cashout amount shown in alerts and updated in liabilities

---

## Reference paths (N-Game menu navigation)

| Action | Path |
|---|---|
| Master enable / disable Live + Pre Cash Out | `Maintenance → Cashout Settings → Enable/Disable General Cashout (Live and Pre)` |
| Deny Cash Out for a specific player | `Maintenance → Manage Players → Find a Player → Edit → Deny Cashout` |
| Per-game enable / disable | N-Game game view → Game Actions panel → `Cashout` toggle |
| Per-market Cashout flag visibility | Market header row → `Cashout: On` indicator |
| Bet Ticker live alerts | Top nav → `N-Game Bet Ticker` |
| Highlight cashouts > risk | Bet Ticker → `Settings` → `Cashout Setting` → choose Background + Foreground |
