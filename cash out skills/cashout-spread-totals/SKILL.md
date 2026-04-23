---
name: cashout-spread-totals
description: Implement, explain, or decide the cash out logic for spread and totals markets at BetOnline/FoshTech. Use this skill whenever the user asks about cashout on spread bets, totals/over-under bets, line moves, alt line pricing, points conversion tables, push scenarios, or how to handle the missing new odd problem when the main line has moved. Also use when deciding which markets to enable for cashout at launch vs post-launch.
---

# Spread & Totals — Cash Out Logic

## The problem statement

```
Customer bet:     Over 217 @ -110  (decimal 1.909)
Current market:   Over 218.5 @ -110 (decimal 1.909)

What we have:
  ✓ Initial odd:       1.909 (Over 217 @ -110)
  ✓ Wager:             $100
  ✓ Current main line: 218.5 @ -110

What we are missing:
  ✗ New odd for Over 217 (original line, current price)
  ✗ TI_new (requires both sides of Over 217 priced now)
  ✗ True Probability New Odd
  ✗ % Bet Change Win/Lose
  ✗ Vig rate (depends on % Bet Change)

Conclusion: cannot complete the cashout formula
```

---

## Why you can't use the current main line odd directly

It would be tempting to plug in Over 218.5 @ -110 as the new odd. This is wrong for two reasons:

**1. It's a different bet.** Over 218.5 is harder to win than Over 217 — the customer needs 1.5 more points. Using that price undervalues the customer's position and produces an incorrect cashout offer.

**2. The % Bet Change would be wrong.** The formula measures how much the customer's specific bet has moved. Using a different line contaminates that measurement and produces an incorrect vig assignment. You could end up applying 10% vig to a bet that deserves 2%, or vice versa — a direct financial error.

---

## The four options

### Option 1 — Suspend cashout when the line moves

The simplest and safest rule:

```
IF current_line == original_line → cashout available, use standard formula
IF current_line != original_line → suspend cashout
```

Applied to the example:
```
Original line: 217
Current line:  218.5
217 ≠ 218.5  → SUSPENDED
```

The customer sees the suspended state in the widget. No cashout offered until the bet settles.

**Fits current setup?** Yes — no additional data needed, no pricing model required.

**Real world impact:** NBA totals lines move frequently in-play. Cashout would be suspended for most of the game. Pre-game, if the line moves before tip-off, also suspended.

**When cashout IS available under Option 1:**
```
Original line: 217
Current line:  217   (same)
Original price: -110 (1.909)
Current price:  -130 (1.769)   ← price moved, line did not

→ cashout available, standard formula applies
```

---

### Option 2 — Derive the original line price using a points conversion table

Each half-point of line movement has an approximate price equivalent by sport. This is a known concept in sports betting — operators charge a price premium per half-point when customers buy points.

**NBA totals conversion (approximate):**
```
Each 0.5 point of total ≈ 10 American odds points
```

Applied to the example:
```
Original:         Over 217 @ -110
Line moved:       +1.5 points (218.5 vs 217) — harder for the customer
Price adjustment: (1.5 / 0.5) * 10 = +30 American odds points worse
Derived new odd:  -110 - 30 = -140  (decimal ~1.714)
```

Now you have an estimated new odd for Over 217 and can complete the formula:

```
Derived new odd (Over 217):  1.714
Derived new odd (Under 217): ~2.600  ← apply same conversion to other side

TI_new  = 1/1.714 + 1/2.600 = 0.5835 + 0.3846 = 0.9681... 
          (check: should be ~1.04 with margin applied — adjust conversion accordingly)

True Prob New Odd = 1.714 * TI_new
% Bet Change      = standard winning/losing formula
Vig rate          = from vig table
Cashout           = (1.909 / True Prob New Odd) * 100 * (1 - vig)
```

**Fits current setup?** Requires trading to validate and maintain the conversion table per sport.

**Risk:** the conversion is an approximation. If the actual market price diverges significantly from the derived price, the cashout offer is financially incorrect — either too generous (book loses money) or too low (customer loses trust).

**Points conversion table by sport (indicative — must be validated by trading):**

| Sport | Per 0.5pt — totals | Per 0.5pt — spread |
|---|---|---|
| NBA | ~10 American pts | ~10 American pts |
| NFL | ~10–15 American pts | ~10–15 American pts |
| MLB | ~15–20 American pts | ~20 American pts |
| NHL | ~15–20 American pts | N/A |
| Soccer (totals) | ~20–25 American pts | N/A |

These numbers are indicative starting points. Trading must sign off on the values used in production before this option is enabled.

---

### Option 3 — Request the original line price from the odds feed

If the odds provider (OpticOdds, Sportradar, etc.) offers alternative line pricing, query the current price of Over 217 directly even though the main line is now 218.5.

```
Request:  get_price(market=totals, line=217, side=over, game=NYK@ATL)
Response: Over 217 @ -140  (if available)
```

This is the cleanest solution — a real market price, not an estimate. The standard formula applies with no modification.

**Fits current setup?** Depends entirely on whether the odds feed carries alt line data. Check with whoever provides live odds (OpticOdds). If the feed has it, this becomes the preferred path.

**Risk:** the original line may not be priced in the feed once it's no longer the main line. Coverage drops significantly for older lines, especially in-play. Requires a fallback rule (Option 1 or Option 2) for when the feed doesn't return a price.

---

### Option 4 — Do not offer cashout on spread/totals

Accept the limitation and scope cashout to moneyline only. Configure the Settings Manager to only enable cashout on Money Line markets.

```
Settings Manager:
  Market: Money Line → cashout ENABLED
  Market: Spread     → cashout DISABLED
  Market: Total      → cashout DISABLED
```

**Fits current setup?** Yes, completely. Already supported in the back office configuration.

**Real world impact for WC2026:** minimal — soccer is almost entirely moneyline and 3-way. Spread/totals in soccer are low-volume markets at launch.

---

## Decision matrix

| Option | Formula complete? | Data needed | Financial risk | Complexity | Recommended for |
|---|---|---|---|---|---|
| 1 — Suspend on line move | Yes (when line = same) | None | None | None | Launch |
| 2 — Points conversion | Yes (estimated price) | Conversion table per sport, trading sign-off | Medium | Low–Medium | Post-launch NBA/NFL |
| 3 — Odds feed alt lines | Yes (real price) | Feed must support alt lines | Low | Low (if feed has it) | Post-launch if feed supports |
| 4 — Moneyline only | N/A | None | None | None | WC2026 launch |

---

## Recommended path for BetOnline

```
WC 2026 launch
  Cashout on  → Moneyline, 3-way (soccer)
  Cashout off → Spread, Totals
  Rationale   → WC is soccer-first, formula is proven, zero operational risk

Post-launch (NBA/NFL season)
  Step 1 → Check if OpticOdds feed provides alt line prices
  Step 2 → If yes: Option 3, real price, full standard formula
            If no:  Option 1, suspend on line move
  Step 3 → Evaluate Option 2 with trading validating conversion
            table sport by sport before enabling
```

---

## How the standard formula applies once you have the price

Once any of the above options produces a valid new odd for the original line (217), the rest of the calculation is **identical to moneyline**. No formula changes needed.

```
Inputs (resolved):
  io  = 1.909   (Over 217 @ -110, original)
  no  = 1.714   (Over 217 @ -140, derived or from feed)
  no2 = 2.600   (Under 217, other side)
  wager = $100

TI_new = 1/no + 1/no2
tp     = (1/no) / TI_new
tpo    = no * TI_new
isWin  = no < io  → 1.714 < 1.909 → TRUE (winning position)

bc = (wager * io * tp - wager) / ((io - 1) * wager)
vig = lookupVig(bc, vigTable)
co  = (io / tpo) * wager * (1 - vig)
```

The formula is the same. The only challenge is sourcing `no` and `no2` for the original line.

---

## Spread — same logic, same problem

Everything above applies identically to spread markets. If a customer bets **Knicks -1 @ -114** and the line moves to **Knicks -2.5**, the original line price is unknown and the same four options apply.

Additional consideration for spread: the **push scenario**. If the score lands exactly on the spread (e.g., Knicks win by exactly 1), the bet pushes — wager is returned, no win/loss. Recommended handling: suspend cashout when the live score margin equals the original spread line. This avoids needing to model the three-way outcome (win/push/lose).

```
IF live_margin == original_spread_line → suspend cashout (push risk)
IF live_margin != original_spread_line → apply normal winning/losing logic
```
