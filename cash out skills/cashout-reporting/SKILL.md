---
name: cashout-reporting
description: Implement, explain, or verify how cash out bets update liabilities in Synergy and N-Game trading platforms at BetOnline/FoshTech. Use this skill whenever the user mentions Synergy reports, N-Game reports, liability updates after cashout, Profit/Risk/Payout/Volume/Count calculations, wager details status, Trading Settings Management, or asks how cashed-out bets affect the trading tools. Also use when the user references the Synergy or N-Game liability Excel templates (JIRA CH-499, CH-500) or asks about the routing rule between the two platforms.
---

# Cash Out: Synergy and N-Game Liabilities Reporting

When a bet is cashed out, the trading platforms must be updated in real time so traders can see correct liability information and act accordingly. Without this update, traders make decisions based on stale data — missing cashed-out bets means incorrect liabilities.

---

## Routing rule — which platform gets updated

The platform that receives the liability update depends solely on **when the bet was placed**, not when it is cashed out.

```
Bet placed PRE-MATCH  →  Synergy  (regardless of cashout timing)
Bet placed LIVE       →  N-Game   (regardless of cashout timing)
```

A pre-match bet cashed out during a live game still updates Synergy, not N-Game.

---

## Synergy

### Report fields

Synergy reports show five columns: **Profit, Risk, Payout, Volume, Count**

When a bet is cashed out, the following fields update: **Profit, Payout, Volume**. Risk and Count do not change.

### How the fields update

The cashout amount replaces the original potential payout. Profit recalculates for both selections based on the new payout. Volume follows.

**Example — SF 49ers vs Green Bay Packers**

Bet: $100 on SF @ -150 (decimal 1.667). Potential payout = $166.

Before cashout:

| Game | Profit | Risk | Payout | Volume | Count |
|---|---|---|---|---|---|
| San Francisco | -66 | 100 | 166 | 66 | 1 |
| Green Bay Packers | 100 | | | | |

Bet cashed out at **$84.00**:

| Game | Profit | Risk | Payout | Volume | Count |
|---|---|---|---|---|---|
| San Francisco | **16** | 100 | **84** | **-16** | 1 |
| Green Bay Packers | **16** | | | | |

Changes:
- Payout: 166 → 84 (cashout amount)
- Profit SF: -66 → 16
- Profit GB: 100 → 16
- Volume: 66 → -16

**The profit update applies to both selections** (winning and losing side of the market).

---

### Synergy liability calculations

#### Initial wagers (per bet)

```
Payout  = Risk * decimal_odd
Profit  = Risk - Payout
Volume  = Risk - Payout
```

#### Totals for initial wagers (per selection)

```
Total Risk    = SUM(all risks for selection)
Total Payout  = SUM(all payouts for selection)
Total Profit  = Total Risk (opposite selection) + Total Payout (this selection)
Total Volume  = SUM(all volumes for selection)
```

#### Total liabilities after cash out (per selection)

```
Total Risk   = SUM(all risks) — no change

Total Payout = SUM(all payouts for selection)
               where cashed-out bets use cashout amount instead of original payout

Total Profit = Total Risk (opposite selection)
             + Total Payout (this selection, updated with cashout amounts)
             + current profit from opposite selection bets that were cashed out

Total Volume = SUM(all volumes for selection)
               where cashed-out bets use cashout amount instead of original payout
```

---

### Synergy liability report template

Source of truth: **Excel file in JIRA CH-499**

The template has four sections:

| Section | Contents |
|---|---|
| Game odds | Odds for selections A and B in American and decimal format |
| Initial wagers & liabilities | Per-bet Profit, Risk, Payout, Volume; totals per selection |
| Cash Out details per selection | List of cashed-out bets highlighted in red; cashout amount column |
| Liabilities after cash out | Synergy report recalculated with cashout amounts |

---

### Wager details in Synergy

#### Wager Coverage view

- A **Cash Out** column is added alongside existing columns
- The cash out icon is displayed on rows where the bet has been cashed out
- The column supports filtering and sorting like all other columns

#### Wager Details popup

| Field | Update |
|---|---|
| Status | Updated to **Cashed Out** |
| Paid | Shows the cashout amount returned to the customer |
| Lost | Empty (no win/loss grading) |
| Won | Empty (no win/loss grading) |

#### Trading Settings Management — Performance / Wagers

Path: `Synergy > Trading Settings Management > Performance > Wagers`

- Status field updated to **Cashed Out**
- Paid amount updated to the cashout amount

#### Trading Settings Management — Transactions

Path: `Synergy > Trading Settings Management > Transactions`

- Description: **Wager Cashed Out**
- Credit column: cashout amount returned
- Mirrors the customer-facing Transaction Report entry

---

## N-Game

### Report fields

N-Game reports show three columns: **Bets, Stake, Liability**

When a bet is cashed out, **Stake does not change**. Only **Liability** updates — and the update applies to **all selections in the market**.

### How liability updates

```
New Liability (per selection) = Cashout Amount - (Total Stake A + Total Stake B)
```

This formula applies the same liability value to every selection in the market.

**Example — SF 49ers vs Green Bay Packers**

Bet: $50 on Green Bay @ +159 (decimal 2.59). Return = $129.50.

Before cashout:

| Outcome | Odds | Bets | Stake | Liability |
|---|---|---|---|---|
| San Francisco | -200 | 0 | 0 | -50 |
| Green Bay Packers | +159 | 1 | 50 | 80 |

Bet cashed out at **$100.00**:

```
Liability = 100 - (0 + 50) = 50
```

After cashout:

| Outcome | Odds | Bets | Stake | Liability |
|---|---|---|---|---|
| San Francisco | -200 | 0 | 0 | **50** |
| Green Bay Packers | +159 | 1 | 50 | **50** |

**The liability update applies to both selections** — same value on both sides.

---

### N-Game liability calculations

#### Initial wagers (per bet)

```
Return    = Stake * decimal_odd
Liability = (Stake * decimal_odd) - Stake
          = Return - Stake
```

#### Total liabilities before cash out (per selection)

```
Total Stake = SUM(all stakes for selection)
Liability   = Total Return (selection A) - (Total Stake A + Total Stake B)
```

#### Total liabilities after cash out (per selection)

```
Total Stake = SUM(all stakes) — no change

Liability   = Total Cashed Out A
            + Total Cashed Out B
            + SUM(Return from non-cashed-out bets for this selection)
            - (Total Stake A + Total Stake B)
```

> **Note:** The N-Game liability after cash out template is not automated. If you add cashout data rows to the Excel template, you must manually extend the formula range.

---

### N-Game liability report template

Source of truth: **Excel file in JIRA CH-500** (two tabs: single-bet example and multi-bet example)

The template has four sections:

| Section | Contents |
|---|---|
| Game odds | Odds for selections A and B in American and decimal format |
| Initial wagers | Per-bet Stake, Return, Liability for both selections |
| Total liabilities before cash out | Total Bets, Stake, Liability per selection (matches live N-Game view) |
| Cash Out | Cashed-out bets in red, cashout amount column |
| Total liabilities after cash out | N-Game report recalculated with cashout amounts |

---

## Summary — what changes where

| Platform | Routing rule | Fields updated | Stake/Risk changes? |
|---|---|---|---|
| Synergy | Bet placed pre-match | Profit, Payout, Volume (both selections) | Risk: no. Count: no |
| N-Game | Bet placed live | Liability (all selections, same value) | Stake: no |

## Reference files

- JIRA CH-499 — Synergy liability Excel template
- JIRA CH-500 — N-Game liability Excel template (single-bet and multi-bet tabs)

For the customer-facing cashout flow, see the `cashout-site` skill.
For back office cashout configuration, see the `cashout-backoffice` skill.
For the cashout offer calculation, see the `cashout-formula` skill.
