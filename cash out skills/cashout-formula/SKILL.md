---
name: cashout-formula
description: Implement, explain, verify, or debug the BetOnline cash out formula used in the SB26 / FoshTech sportsbook. Use this skill whenever the user mentions cash out calculations, vig rate, true probability, market margin, bet change win/lose percentage, cashout offer amount, admin fee, or asks to check whether a cashout number is correct. Also use when building any calculator, prototype, or tool that needs to compute cashout values — 2-way or 3-way markets, pre-event or live. Load this skill before writing any cashout formula code or explaining the cashout math.
---

# Cash Out Formula

Core calculation engine for the BetOnline / FoshTech cash out feature. Covers both pre-event and live scenarios, 2-way and 3-way markets.

## Two scenarios

### 1. Pre-event, no line move

```
Cashout = Wager * (1 - Admin Fee)
```

Admin fee is the % applied when the price has not changed from the initial odd. Set per template in the back office (Sasha / Cash Out Management tool). Typical value: 5%.

### 2. Pre-event line moved, or live

```
Cashout = (Initial Odd / True Probability New Odd) * Wager * (1 - Vig Rate)
```

Equivalent form using simple new odd:

```
Cashout = (Initial Odd / New Odd) * Wager * (1 - (Vig Rate + Market Margin))
```

Both forms produce the same result.

---

## Variables

### Initial Odd
Decimal price of the selection when the wager was placed. Example: 2.05 (+105 American).

### True Probability New Odd
NOT the raw new odd. It is the new odd adjusted upward by the market margin:

```
True Probability New Odd = New Odd * Total Implied (new)
```

This is always larger than the raw new odd. Using it embeds an extra margin equal to the market margin of the new odds.

### Wager
Stake in dollars for this selection.

### Vig Rate
Taken from the vig rate table based on the % Bet Change Win/Lose. See the vig table section below.

---

## Step-by-step calculation

### Step 1: Compute Total Implied for initial and new odds

```
Total Implied = sum of (1 / decimal_odd) for every selection in the market

2-way:  TI = 1/odd1 + 1/odd2
3-way:  TI = 1/odd1 + 1/odd2 + 1/odd3
```

**Market margin = TI - 1**

Example (2-way, new odds SF -9346 / GB +1824):
- New odds decimal: 1.01 and 19.24
- TI_new = 1/1.01 + 1/19.24 = 0.9901 + 0.0520 = 1.0421
- Market margin = 4.21%

### Step 2: True probability of the selection

```
True Probability = (1 / selection_new_odd) / TI_new
True Probability New Odd = selection_new_odd * TI_new
```

Example (SF new odd = 1.01, TI_new = 1.0421):
- True Probability = (1/1.01) / 1.0421 = 95.01%
- True Probability New Odd = 1.01 * 1.0421 = 1.0525

### Step 3: % Bet Change Win/Lose

Determines whether the bet is winning or losing and by how much. Used to look up the vig rate.

**Winning position** (new odd < initial odd, probability increased):

```
% Bet Change = (Wager * Initial_Odd * True_Prob_New - Wager) / ((Initial_Odd - 1) * Wager)
```

**Losing position** (new odd > initial odd, probability decreased):

```
% Bet Change = (Wager * Initial_Odd * True_Prob_New - Wager) / Wager
```

Example (SF winning, $100 wager, io=2.05, tp_new=0.9501):
```
= (100 * 2.05 * 0.9501 - 100) / ((2.05 - 1) * 100)
= (194.77 - 100) / 105
= 94.77 / 105
= 90.26%  -> Winning
```

### Step 4: Vig rate lookup

Look up the % Bet Change in the vig rate table. See `references/vig-table.md` for the full table.

Example: 90.26% winning -> row "Winning 90%-91%" -> Vig Rate = 5%

### Step 5: Apply the formula

```
Cashout = (Initial_Odd / True_Prob_New_Odd) * Wager * (1 - Vig_Rate)
        = (2.05 / 1.0525) * 100 * (1 - 0.05)
        = 194.79 * 0.95
        = $185.03
```

---

## 3-way markets

Identical logic. Each selection gets its own cashout offer computed independently using the shared TI from all three new odds.

```
TI_new = 1/odd_home + 1/odd_away + 1/odd_draw
```

Each selection's true probability and true probability new odd use this same TI_new. Each selection has its own % Bet Change and vig rate.

Example labels: Home / Away / Draw

---

## Linked odds (margin-locked)

When the user changes any one odd, the others auto-adjust so TI stays constant:

```
Target TI = 1 + (margin% / 100)

When odd[i] changes to new_val:
  - Set odd[i] = new_val
  - Remaining implied probability = TI_target - (1 / new_val)
  - Split remaining evenly across the other (n-1) selections
  - Each other odd[j] = 1 / (remaining / (n-1))
```

This ensures the market margin never drifts when inputs are edited.

---

## Common mistakes to avoid

| Wrong | Correct |
|---|---|
| Using raw new odd in the denominator | Use True Probability New Odd = new_odd * TI_new |
| Computing TI from only one odd | TI requires ALL selections in the market |
| Applying cap as % of wager | Cap (if any) is a separate business rule, do not apply inside the formula |
| Using losing formula for winning bets | Check: isWin = (new_odd < initial_odd) |
| Different TI for each selection | TI_new is shared across all selections from the same market |

---

## Reference files

- `references/vig-table.md` — Full vig rate table with all ranges and rates
- `references/worked-examples.md` — Complete worked examples matching the xlsx file

For back office configuration (templates, admin fee, max cashout), see the `cashout-backoffice` skill.
For the HTML prototype, see the `cashout-html-suite` skill.
