---
name: cashout-synergy-instantaction
description: Implement, explain, or verify how Cash Out integrates with Synergy (the pre-match trading platform) and Instant Action (the pre-match bet ticker). Use this skill whenever the user mentions Synergy game settings, the Cash Out toggle in market settings, the cash out note icon, Wager Coverage with the Cash Out column, Wager Details status updates, the System Settings Cash Out checkbox, denying Cash Out for a specific customer, Customer Transactions showing "Wager Cashed Out", or the Instant Action bet ticker showing cashed-out bets in italic format. Also use when the user asks how Trading enables / disables Cash Out per game, per brand, or per customer in Synergy.
---

# Cash Out — Synergy & Instant Action

Synergy is the **pre-match trading platform**. Instant Action is the **pre-match bet ticker** that shows real-time alerts — including cashed-out bets, when the cashout happens **before the game starts**.

> **Routing rule for tickers:**
> - Bet cashed out **before the game starts** → shown in **Instant Action** (Synergy side)
> - Bet cashed out **after the game has started** → shown in **Bet Ticker** (N-Game side)
>
> This is determined by the **game state at cashout time**, not by when the bet was placed. (Note: this is *different* from the liability-report routing rule, which is determined by *placement* time — see [cashout-reporting](../cashout-reporting/SKILL.md).)

---

## Synergy

Access: `http://synergy/`

### 1. Game Settings & Info

#### 1a. Enable / Disable Cash Out per Game

- Cash Out can be enabled or disabled **within the market settings popup** for a specific game.
- The control is a checkbox labelled **"CashOut"** in the Money Line / market settings dialog (alongside other market-level toggles like "Take Offline", "Circle game", "Maximum Loss").
- When Cash Out is enabled for a game, a **note icon** is displayed in the games list next to the event date / time info, so traders can see at a glance which games have Cash Out turned on.

#### 1b. Liabilities Updated in Real Time

- Liabilities for the market are updated in real time as bets are cashed out.
- For the full calculation rules (Profit / Risk / Payout / Volume / Count updates per cashed-out bet, including 2-way and 3-way market handling), see the [cashout-reporting](../cashout-reporting/SKILL.md) skill.

#### 1c. Wager Coverage

The Wager Coverage view (per market, per game) gets a new column:

| Column / Element | Behaviour |
|---|---|
| **Cash Out** column | New column added alongside Posted / CustomerID / Choice / Line / Volume / Wager Number. |
| Cash Out icon | Displayed on the row of every wager that has been cashed out. Identifies graded-via-cashout bets at a glance. |
| Filter | Like every other column, the Cash Out column is filterable — traders can click to show only cashed-out wagers (or only non-cashed-out). |

Use case: Trading scans a market's Wager Coverage and instantly sees which wagers contributed to the live liability number via cashout vs normal grading.

#### 1d. Wager Details

When opening the Wager Details popup for a cashed-out wager:

| Field | Update |
|---|---|
| **Status** | → `Cashed Out` |
| **Paid** | → cashout amount returned to the customer |
| **Lost** | → empty |
| **Won** | → empty |
| Out Come | → `Cashed Out` |

All other ticket fields (Posted, Total Ticket, Type, Risk, To Win, Doc #, Ties, Acct, Ticket #, Game Time, Wager description) remain as posted.

---

### 2. System Settings — Brand-Level Master Switch

Path: `Trading Settings Management → System Settings → Cashout Configuration`

- A single checkbox: **"Enable / Disable Cash Out for all brands"**.
- When unchecked, Cash Out is disabled across every brand the platform serves — overriding any per-game or per-customer setting.
- Used as an emergency master kill-switch or for staged rollout / rollback.

The same screen also includes the existing settings: archive data age, Include Cents, Truncate, Weekly Figure Starts, Seconds to update Ticket Writer lines, Seconds to update SYNERGY action, Notify SYNERGY (Synergy ID, To Exceed Wager Maximum, Insufficient Fund, To Accept Wagers Minutes following Cutoff), Max Bet limits.

---

### 3. Customer Settings & Info

#### 3a. Deny Cash Out for a Specific Customer

Path: `Trading Settings Management → Customer → Profile`

- New checkbox in the customer Profile area: **"Deny Cashout Across all Games"**.
- When checked: Cash Out is suppressed for this specific customer across every game / market — the cashout option is hidden from the customer site, and the cashout engine rejects any request that arrives for this customer.
- Used for risk-flagged accounts, VIP-segment exceptions, or compliance / regulatory holds.

The flag sits alongside other customer-level toggles in the Profile area: Wise Action, Always Show on Instant Action Panel, Deny Re-bet Across all Games / Contests, Euro Wise Action, Informational Player, Euro Always Show on Instant Action Panel, Seconds to Delay Internet Confirmation.

#### 3b. Cash Out Info in Customer Transactions

Customer Profile → Transactions tab now lists cashout entries inline with normal wager entries:

```
Date                  Doc #         Description
7/20/2021 11:30:01    453792444     1 Wager(s) Placed
7/20/2021 11:34:46    453792446     1 Wager(s) Placed
...
7/20/2021 11:40:04    453792450     Wager Cashed Out         ← new
...
7/20/2021 12:10:18    453792455     Wager Cashed Out         ← new
```

The "Wager Cashed Out" entry references the original wager and shows the cashout amount as a credit.

---

## Instant Action

Pre-match bet ticker. Real-time stream of betting events (bets sold, bets cashed out, etc.) for traders monitoring activity before games start.

> **Important scoping rule:** Only bets cashed out **when the game is not yet started** are displayed in Instant Action — even if the bet was placed pre-match. If the game has already started by the time the cashout happens, the alert is sent to **Bet Ticker** (N-Game side) instead.

### 1. Cash Out Alerts

Cashed-out bets appear as a new alert format in the Instant Action stream, displayed in **italic font** to distinguish them from normal "bet placed" alerts.

Each cashout alert shows:

| Field | Description |
|---|---|
| **Time** | When the cashout was effectuated (e.g. `06:18:35`) |
| **Customer** | Customer ID (e.g. `B1543492`) |
| **Name** | Customer display name |
| **Country** | Customer country |
| **Limit Info** | e.g. `#12 - 0.07% - s25000` |
| **Risk** | Original wager amount placed by the player (e.g. `$1.54`) |
| **Description** | Status + Amount + Odd + Description, formatted as: `Cashed Out $1.90 with Odd -130 | Desktop - FOOTBALL - SS130 Tampa Bay Buccaneers -130 for GAME` |
| **WagerType** | e.g. `Straight` |

The **Description** field combines four pieces of info that traders can scan in one row:
1. **Status** — `Cashed Out`
2. **Amount** — what was returned to the player (the cashout payout)
3. **Odd** — the odd considered for cashout when the bet closed (the "new odd" used in the cashout formula, not the original placement odd)
4. **Description** — the underlying wager description (sport, league, team, market)

Italic font is the primary visual differentiator — traders can pattern-match cashed-out alerts at a glance from the wider Instant Action stream.

### Existing Instant Action filters apply

The standard Instant Action filter bar (Customer Type, Bet Type, Store, Apply Filters, Board Market, Custom Filter, Clear All Filters) continues to work — cashout alerts can be filtered the same way as any other alert type.

---

## Reference flow

```
Bet placed (pre-match)
    │
    ▼
Cashout requested
    │
    ├─ Game not yet started?
    │     └─ Cashout alert → Instant Action (italic row, Synergy side)
    │
    └─ Game already started (live)?
          └─ Cashout alert → Bet Ticker (N-Game side, see cashout-ngame-betticker)
```

---

## Related skills

- [cashout-reporting](../cashout-reporting/SKILL.md) — Synergy and N-Game liability calculations updated when bets are cashed out
- [cashout-ngame-betticker](../cashout-ngame-betticker/SKILL.md) — N-Game (live trading platform) + Bet Ticker counterpart to this skill
- [cashout-backoffice](../cashout-backoffice/SKILL.md) — "Sasha" / Cash Out Management tool that drives sport / league / template configuration upstream of Synergy
- [cashout-formula](../cashout-formula/SKILL.md) — pricing formulas that produce the cashout amount shown in alerts and updated in liabilities

---

## Reference paths (Synergy menu navigation)

| Action | Path |
|---|---|
| Master enable / disable Cash Out across all brands | `Trading Settings Management → System Settings → Cashout Configuration → Enable/Disable Cash Out for all brands` |
| Deny Cash Out for a specific customer | `Trading Settings Management → Customer → Profile → Deny Cashout Across all Games` |
| Per-game enable / disable | Game's market settings popup → `CashOut` checkbox |
| Wager Coverage | Game-level → Wager Coverage view → Cash Out column |
| Wager Details | Wager Coverage → click wager → Wager Details popup |
| Customer Transactions | Customer → Profile → Transactions tab |
