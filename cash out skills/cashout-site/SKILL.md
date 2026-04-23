---
name: cashout-site
description: Build, update, or explain the customer-facing cash out site features at BetOnline/FoshTech. Use this skill whenever the user mentions the betslip cash out widget, My Bets cashout status, Bet History To Return column, Transaction Report cashout entries, the Accept Cash Out Changes toggle, Show Confirmation toggle, suspended cashout state, price changed states, or any customer-facing UI that displays or processes a cashout offer. Also use when wiring the customer site to the back office settings or the cashout formula.
---

# Customer Site — Cash Out

All customer-facing touchpoints for the cash out feature. Covers pre-event and live site, the widget flow, My Bets, Bet History, and Transaction Report.

---

## 1. Cash Out icon

A distinctive icon indicates that cashout is available (or has been used) on a given market or bet. Appears in three places:

- **Offering / league view**: shown on the market header row to indicate the league/game supports cashout
- **Betslip**: shown on the bet row when cashout is available for that selection
- **My Bets / pending tab**: shown on bets that have cashout available

A "NEW" badge appears alongside the Cash Out tab on first visit. It disappears once the user has visited the tab (stored in cookies).

---

## 2. Cash Out widget states

The widget lives in the betslip sidebar under the "Cash Out" tab.

### 2a. No bets available (empty state)
Message: *"Cash Out is available on selected events, markets, both pre-event and live. A market eligible for Cash Out will have the Cash Out icon."*

### 2b. Cash out available
Bet card shows:
- Team / matchup + live/pre badge
- Risk amount + To Win amount
- Bet change % and position (winning / losing / neutral)
- Vig applied and admin fee (meta row)
- **"Cash Out $XX.XX"** button

Amount updates in real time as market prices move.

### 2c. Suspended
When no price is available for the market, the button shows:
- Lock icon + "Suspended" label
- Button is greyed out and non-interactive

### 2d. Price changed (during validation)

Two sub-states when price moves while the user's cashout request is being validated:

**Price changed up** (moved in customer's favour):
- Warning message: *"The cash out return has changed"* (upward arrow)
- Button updates to show new higher amount in green
- After 3 seconds the message clears

**Price changed down** (moved against customer):
- Warning message: *"The cash out return has changed"* (downward arrow)
- Button updates to show new lower amount in amber
- If "Accept Cash Out Changes" toggle is ON: cashout proceeds at new amount
- If toggle is OFF: flow is interrupted, new offer is displayed for manual acceptance

### 2e. Suspended during validation
If market suspends while the request is processing:
- Message: *"The Cash Out was suspended"*
- Clears after 3 seconds
- Button returns to suspended state

### 2f. Successfully cashed out
Once the cashout is confirmed:
- Bet card shows success message: *"Cashed out · $XX.XX returned to balance"*
- Line disappears from the widget after 5 seconds
- Bet is graded as "Cashed Out" in My Bets, Bet History, and Transaction Report

---

## 3. Widget toggles

Two customer-controlled toggles at the bottom of the Cash Out widget:

### Accept Cash Out Changes
When ON: if the cashout amount changes (up or down) during validation, the cashout proceeds automatically at the new amount.
When OFF: any change interrupts the flow and shows the new amount for manual confirmation.

### Show Confirmation
When ON: clicking "Cash Out $X" first shows a confirmation state ("Confirm $X ▶") for 5 seconds. The customer must click again to proceed.
When OFF: cashout initiates immediately on first click.

---

## 4. My Bets

### Pending tab
- Bets with cashout available show the cash out icon and current offer amount
- Bets that have been cashed out show "Cashed Out" status with the returned amount

### Cashed Out status
A new "Cashed Out" status is created for bets graded via the feature. Fields shown:
- Ticket number
- Accepted date
- Graded date
- Amount (original wager)
- Status: Cashed Out (with icon)
- Type (market type)
- **To Return**: the amount that was paid out via cashout

---

## 5. Bet History

Accessed via My Account > Bet History. New field added:

### To Return column
New column showing the cashout amount for cashed-out bets. For pending bets, shows the current cashout offer. For won/lost bets, empty.

### Status filter
Users can filter their history by "Cashed Out" status to see all cashed-out bets.

### Bet history row (cashed out)
- Proposition (matchup + market + selection)
- Price (original odd)
- Date placed
- Amount (wager)
- To Win
- Result: "Cashed Out" badge
- To Return: cashout amount paid

---

## 6. Transaction Report

My Account > Transactions. When a bet is cashed out, two entries appear:

| Entry | Type | Description | Amount |
|---|---|---|---|
| Cashout payment | Cashed Out | Wager cashed out | +$XX.XX (green) |
| Original wager | Wager (Debit) | 1 wager(s) placed | -$XX.XX (red) |

The cashout entry shows:
- Transaction ID
- Date/time
- Type: "Cashed Out"
- Product: Sportsbook / Live Betting
- Description: "Wager cashed out · [Template name]"
- Amount: positive value in green

---

## 7. Connection to back office

The bets displayed in the widget are driven by the Settings Manager:
- Only leagues with Live or Pre cashout enabled show bets
- Each bet's cashout offer is calculated using the template assigned to its sport/league
- Disabling a league in the Settings Manager removes its bets from the widget
- Template changes propagate to the cashout offer in real time

For cashout offer calculations, see the `cashout-formula` skill.
For back office configuration, see the `cashout-backoffice` skill.

---

## Reference files

- `references/widget-flow.md` — Full cashing-out validation flow diagram
