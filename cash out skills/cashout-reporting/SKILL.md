---
name: cashout-reporting
description: Implement, explain, or verify how cash out bets update liabilities in Synergy and N-Game trading platforms at BetOnline/FoshTech. Use this skill whenever the user mentions Synergy reports, N-Game reports, liability updates after cashout, Profit/Risk/Payout/Volume/Count calculations, wager details status, Trading Settings Management, or asks how cashed-out bets affect the trading tools. Also use when the user references the Synergy or N-Game liability Excel templates (JIRA CH-499, CH-500) or asks about the routing rule between the two platforms.
---

# Cash Out: Synergy and N-Game Liabilities Reporting

When a bet is cashed out, the trading platforms must be updated in real time so traders see correct liability information. Without this, traders act on stale data and make incorrect hedging decisions.

---

## Routing rule — which platform gets updated

The platform depends solely on **when the bet was placed**, not when it is cashed out.

```
Bet placed PRE-MATCH  →  Synergy  (regardless of when cashout occurs)
Bet placed LIVE       →  N-Game   (regardless of when cashout occurs)
```

A pre-match bet cashed out during a live game still updates **Synergy**.

---

## Shared formula: American to decimal odds

Used in both Synergy and N-Game templates.

```
Decimal = IF(American > 0, (American / 100) + 1, (-100 / American) + 1)
```

Examples: -150 → 1.667 | +130 → 2.300 | +159 → 2.590

---

## Synergy

### Report columns

**Profit · Risk · Payout · Volume · Count**

When a bet is cashed out: **Payout, Profit, and Volume update** on both selections. Risk and Count do not change.

---

### Per-bet formulas (initial wagers)

```
Payout  = Risk * DecimalOdds
Profit  = Risk - Payout
Volume  = IF(Risk < (Payout - Risk), Risk, Payout - Risk)
        = MIN(Risk, Profit)          ← the smaller of stake or potential profit
```

> Volume is always positive. It equals Risk when Risk < Profit, otherwise it equals Profit.

---

### Totals row — initial wagers

```
Total Risk    = SUM(all risks for this selection)
Total Payout  = SUM(all payouts for this selection)
Total Profit  = Total Risk (OPPOSITE selection) + Total Payout (this selection)
Total Volume  = SUM(all volumes for this selection)
Count         = COUNT(risk cells)
```

**Example — 2-way market:**
- Patriots @ -150 (decimal 1.667), one bet of $100
- Eagles @ +130 (decimal 2.300), one bet of $50

Patriots totals:
```
Total Risk   = 100
Total Payout = 100 * 1.667 = 166.67
Total Profit = Eagles_Total_Risk + Patriots_Total_Payout = 50 + 166.67 = 216.67
Total Volume = MIN(100, 66.67) = 66.67
```

---

### After cashout — per-bet update

When a bet is cashed out for amount **CO**:

```
Payout_updated  = CO                          ← replaces Risk * Decimal
Profit_updated  = Risk - CO
Volume_updated  = IF(Risk < (CO - Risk), Risk, CO - Risk)
```

For non-cashed-out bets: all formulas remain unchanged.

**3-way shorthand (used in xlsx template):**
```
Payout = IF(CashedOutAmount > 0, CashedOutAmount, Risk * DecimalOdds)
```

---

### Totals row — after cashout (2-way)

```
Total Risk    = SUM(all risks)  ← unchanged

Total Payout  = SUM(updated payouts)
                = SUM(CO amounts for cashed-out bets)
                + SUM(Risk * DecimalOdds for remaining bets)

Total Profit  = OtherSide_Total_Risk
              + ThisSide_Total_Payout_updated
              + SUM(Profit_updated for OtherSide cashed-out bets)

Total Volume  = SUM(updated volumes for this selection)
```

**Worked example — 1 bet, $100 on Patriots @ -150, cashed out at $84:**

Patriots after cashout:
```
Payout  = 84
Profit  = 100 - 84 = 16
Volume  = IF(100 < (84-100), 100, 84-100) = IF(100 < -16, 100, -16) = -16
```

Patriots totals after cashout (no Eagles bets):
```
Total Risk    = 100
Total Payout  = 84
Total Profit  = Eagles_Risk(0) + Patriots_Payout(84) + Eagles_CO_Profit(0) = 84
Total Volume  = -16
```

Eagles totals (opposite selection, same profit update):
```
Total Profit  = Patriots_Risk(100) + Eagles_Payout(0) + Patriots_CO_Profit(16) = 116
```

---

### Totals row — after cashout (3-way)

For a 3-way market (Home / Draw / Away), Total Profit for each selection includes the updated profits from all **other** selections' cashed-out bets:

```
Total Profit (Home) = Sum(Away_Risk non-CO) + Sum(Draw_Risk non-CO)
                    + Sum(CO amounts for Away cashed-out bets)
                    + Sum(CO amounts for Draw cashed-out bets)
                    + Sum(Home_Payout_updated)
```

The xlsx formula (from `3way example` G26):
```
= SUM(Draw_Stakes_non-CO) + SUM(Away_Stakes_non-CO)
  + (SUM(Home_Profit_updated) + Draw_CO_Profit + Away_CO_Profit)
```

---

### Synergy wager details

#### Wager Coverage

| Change | Detail |
|---|---|
| New column | **Cash Out** column added alongside existing columns |
| Icon | Cash out icon shown on rows where bet has been cashed out |
| Filter | Column supports filtering and sorting like all others |

#### Wager Details popup

| Field | Update |
|---|---|
| Status | → **Cashed Out** |
| Paid | → cashout amount returned to customer |
| Lost | → empty |
| Won | → empty |

#### Trading Settings Management paths

| Path | What updates |
|---|---|
| `Synergy > Trading Settings Management > Performance > Wagers` | Status → Cashed Out; Paid → cashout amount |
| `Synergy > Trading Settings Management > Transactions` | Description: "Wager Cashed Out"; Credit: cashout amount |

---

### Synergy liability report template

**Source of truth: Excel file in JIRA CH-499**
Sheets: `1 bet example`, `multiple bets example`, `3way example`

| Section | Contents |
|---|---|
| Game odds | American odds (hardcoded) + decimal odds (formula) for each selection |
| Initial wagers | Per-bet Profit / Risk / Payout / Volume / Count; totals row |
| Cash Out details | Cashed-out bets in **red font**; cashout amount column |
| Liabilities after cash out | Full table recalculated with cashout amounts substituted |

Color coding:
- Yellow background = Selection A (favourite)
- Orange background = Selection B (underdog)
- No background = Draw (3-way only)
- Red font = cashed-out bets

---

## N-Game

### Report columns

**Bets · Stake · Liability**

When a bet is cashed out: **Stake does not change**. Only **Liability** updates, and the new value applies **identically to all selections** in the market.

---

### Per-bet formulas (initial wagers)

```
Return    = Stake * DecimalOdds
Liability = ROUND(Stake * DecimalOdds - Stake, 0)
          = ROUND(Return - Stake, 0)
```

---

### Total liabilities — before cashout

```
Total Stake (selection X) = SUM(all stakes for X)

Liability (selection X) = ROUND(
    Total Return (selection X) - (Total Stake A + Total Stake B)
, 0)
```

This is the same formula for every selection — each side's liability is its own potential total return minus all stakes combined.

**Example — $50 on Green Bay @ +159 (decimal 2.59):**
```
GB Return   = 50 * 2.59 = 129.50
GB Liability = ROUND(129.50 - (50 + 0), 0) = 80

SF Return    = 0
SF Liability = ROUND(0 - (0 + 50), 0) = -50
```

---

### Total liabilities — after cashout

```
Liability (any selection) = ROUND(
    SUM(all cashout amounts, selection A)
  + SUM(all cashout amounts, selection B)
  + SUM(Return of NON-cashed-out bets for this selection)
  - (Total Stake A + Total Stake B)
, 0)
```

The same value applies to **both** (or all) selections — the liability is shared across the market.

**Example — $50 on Green Bay @ +159, cashed out at $100:**
```
Liability = ROUND(0 + 100 + 0 - (0 + 50), 0) = 50
```

Both SF and GB show Liability = 50 after cashout.

**Multi-bet example (from xlsx `Multiple bets example`):**

Two bets cashed out ($65 on SF, $33 on GB); remaining bets have returns H8, H9 (SF side) and M7, M8, M9 (GB side):

SF post-cashout liability:
```
= ROUND(65 + 33 + H8 + H9 - (Total_Stake_SF + Total_Stake_GB), 0)
```

GB post-cashout liability:
```
= ROUND(65 + 33 + M7 + M8 + M9 - (Total_Stake_SF + Total_Stake_GB), 0)
```

> **Note from xlsx:** The N-Game post-cashout liability template is **not automated**. If you add more cashout rows, you must manually extend the formula to include the new cashout amounts and any additional non-cashed-out returns.

---

### N-Game liability report template

**Source of truth: Excel file in JIRA CH-500**
Sheets: `1 bet example`, `Multiple bets example`

| Section | Contents |
|---|---|
| Game odds | American + decimal odds for each selection |
| Initial wagers | Per-bet Stake, Return, Liability for both selections |
| Total liabilities before cashout | Bets / Stake / Liability per selection (mirrors live N-Game view) |
| Cash Out | Cashed-out bets in **red font**; cashout amount column |
| Total liabilities after cashout | N-Game table recalculated with cashout amounts |

---

## Summary — what changes where

| Platform | Routing trigger | Fields updated | Unchanged |
|---|---|---|---|
| Synergy | Bet placed pre-match | Profit, Payout, Volume (both selections) | Risk, Count |
| N-Game | Bet placed live | Liability (all selections, same value) | Bets, Stake |

---

## Known template anomalies (xlsx source files)

These are bugs in the reference Excel files — do not replicate them in production:

| File | Cell | Issue |
|---|---|---|
| NGame 1 bet example | H6–H11, Return formulas | References `$E$7`/`$E$8` (blank cells) instead of `$D$6`/`$D$7` |
| NGame 1 bet example | M6 | `=L6*D7` missing $ anchors — breaks if row is copied |
| Synergy multiple bets | T22 | `=S22` missing `*$D$9` multiplier |
| Synergy multiple bets | T23 | `=S23*D23` self-references row 23 instead of `$D$9` |

---

## Reference files

- JIRA CH-499 — Synergy Liability Report 2.0.xlsx (1 bet, multiple bets, 3-way tabs)
- JIRA CH-500 — NGame Liability Report.xlsx (1 bet, multiple bets tabs)

For the customer-facing cashout flow, see the `cashout-site` skill.
For back office cashout configuration, see the `cashout-backoffice` skill.
For the cashout offer calculation, see the `cashout-formula` skill.
