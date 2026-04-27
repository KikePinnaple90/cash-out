# 📋 Scope Document — SB Cash Out

![Version](https://img.shields.io/badge/version-v0.7-blue) ![Status](https://img.shields.io/badge/status-Draft-orange) ![Date](https://img.shields.io/badge/date-2026--04--27-lightgrey) ![Module](https://img.shields.io/badge/module-Sportsbook%20Core-informational)

| Program Owner | Date | Version | Status |
|:---|:---|:---|:---|
| _To be assigned_ | 27 Apr 2026 | v0.7 | Draft |

---

## 📑 Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Product Overview](#2-product-overview)
   - 2.1 [The Problem](#21-the-problem)
   - 2.2 [Why This Problem?](#22-why-this-problem)
   - 2.3 [Audience](#23-audience)
3. [Alignment with Business Goals & Company KPIs](#3-alignment-with-business-goals--company-kpis)
4. [Non-Financial Benefits](#4-non-financial-benefits)
5. [Use Case Scope](#5-use-case-scope)
6. [Product Feature Scope (MoSCoW)](#6-product-feature-scope-moscow)
7. [Use Case Diagrams](#7-use-case-diagrams)
8. [Roadmap Alignment](#8-roadmap-alignment)

---

## 1. Executive Summary

> 💡 **In a nutshell:** Lets a player "sell" their open bet for cash before the market is settled — lock in profit, or cut a loss.

Cash Out is a standard sportsbook feature that lets a player settle an open bet before its outcome is decided, locking in a profit or cutting a loss at a price the book offers in real time. This initiative delivers the three foundational variants together in v1 — **Full** (settle the whole stake), **Partial** (settle a portion, let the rest ride), and **Auto** (settle automatically when a target value is reached) — so the engine is built properly the first time; further variants and enhancements layer on top in later increments. Primary v1 surface is the bet history page, with room to expand to other contexts after launch.

Pricing is in-house. We feed odds into our existing proprietary cashout algorithm (Using the *VigRate Table*) and add an uplift margin on top of whatever margin the market already carries, so cashout remains profitable. Preferred input is the provider's 100% (true) odds; where those aren't available we fall back to the currently-served odds and compensate with a larger uplift. Default availability is "on for everything we offer," with a limited exception list to be supplied by Trading before scope is locked.

Configurability is hierarchical and operator-driven: cashout can be switched on/off at the level of sport, league, event, or market from trading tooling. A separate per-punter disable flag suppresses cashout for flagged accounts on **both** sides — the betting engine rejects cashout requests, and the UI hides the cashout option entirely — giving Trading a complete risk lever covering both the offer side and the request side. Player-facing preferences (punter-set auto-cashout targets, player-configured partial defaults) are explicitly **out of v1**; Auto Cash Out in v1 is operator-configured, with player self-serve held back as a future enhancement once the engine is proven.

**Expected outcome:**

- 📈 **Handle lift on enabled markets** — driven by retention of pending wagers and reduced abandonment _(baseline TBD — Analytics to provide)_
- 🏆 **Competitive parity** — Cash Out is table stakes; absence is a documented gap in customer feedback and churn signals
- 💰 **Net revenue from uplift retained on cashed-out Bets** — modelled per sport / league / market and recognised in liability reporting

---

## 2. Product Overview

### 2.1 The Problem

#### 🙋 Customer Problem

Punters with open Bets currently have no way to lock in a return before the market is settled. They watch a winning position drift back to neutral, or absorb the full loss on a losing position, with no exit. The friction applies equally to Bets placed long before the event and Bets placed during the event — and equally to Punters who want to settle the whole stake, only part of it, or have it auto-settle at a target value.

#### 🏢 Business Problem

Three intersecting drivers:
1. **Competitive disadvantage.** Every major sportsbook offers Cash Out, typically in Full + Partial + Auto variants. Absence is visible in competitor acquisition messaging and in retention churn data.
2. **Margin leak via abandonment.** Long-tenor open Bets carry abandonment risk — Punters reduce future deposits when they feel "stuck". Cash Out converts that emotional state into a transaction with uplift retained.
3. **Trading transparency and risk control.** Without per-bet cashout settlement and a per-punter disable lever, Trading has no real-time signal on which positions Punters consider losing and no way to surgically remove cashout from flagged accounts.

### 2.2 Why This Problem?

#### 🔍 Evidence

- **Competitive scan** — every major regulated sportsbook offers Cash Out across Full and Partial, with Auto becoming a standard expectation. Several market it as a primary acquisition feature.
- **Customer signals** — Cash Out is the most-requested feature in support tickets and the betslip-feature feedback channel _(specific volume — Customer Support to confirm)_.
- **Behavioural pattern** — open Bets show measurable engagement decay between placement and settlement. Cashing out converts that decay window into transaction value.

#### 📊 Scale

| Dimension | Assessment |
|:---|:---|
| **Frequency** | Every active Punter session with an open Bet on an enabled sport / league / event / market sees the Cash Out option on the bet history page. |
| **Severity** | High for retention. Moderate for direct revenue. Punters can technically place Bets without Cash Out, but the lack is a named churn driver. The reverse — having Cash Out — measurably increases session length and re-bet frequency in competitor data. |

### 2.3 Audience

#### 🎯 Primary Audience

**Active web-sportsbook Punters with open Bets on sports / leagues / events / markets enabled in the trading tooling.** This includes Punters across the full open-Bet lifecycle, on every variant of cashout (Full, Partial, Auto-as-operator-configured) we ship in v1.

#### 👥 Secondary Audience

| Stakeholder | Why They Care |
|:---|:---|
| **Trading / Risk** | Owns the VigRate Table calibration, the uplift model, the exception list, the per-punter disable lever, and the sport / league / event / market enablement decisions. Real-time liability visibility is required to hedge. |
| **Finance / Reporting** | Cashout amounts flow through the ledger as a new transaction type ("Wager Cashed Out"). Reconciliation, NGR calculation, and reporting must include cashout credits and the original wager debit. Partial cashout produces a partial-credit + partial-stake-still-pending pattern that Finance must support cleanly. |
| **Customer Support** | New ticket category — disputes about cashout amount, "why was it suspended", "I lost the offer", auto-cashout fired at unexpected value. CS playbook needs to handle the validation flow, the new "Cashed Out" / "Partially Cashed Out" Bet statuses, the per-punter disable flag visibility, and suspension scenarios. |
| **Product Analytics** | Owns KPI baselines (cashout adoption by variant, handle lift, NGR impact) and the dashboards that track them post-launch. |
| **VIP Relations** | Per-punter disable flag and any group-level config affect VIP economics. |
| **Legal / Compliance** | New transaction type, new liability category, new automated decision affecting Punter funds (Auto Cash Out triggers automatically). License-jurisdiction sign-off required before launch. |

---

## 3. Alignment with Business Goals & Company KPIs

### 3.1 Strategic Pillar

> 🎯 **Customer Retention & Competitive Parity** — close the largest documented product gap vs major competitors and unlock retention engagement on long-tenor open Bets.

### 3.2 Company KPIs Impacted

| KPI | Current Baseline | Target / Expected Impact |
|:---|:---|:---|
| **Cashout adoption rate** (% of eligible open Bets cashed out, split by Full / Partial / Auto) | _TBD — Analytics to establish via competitor benchmark + first-30-day production data_ | _Target TBD — proposed: track-and-set after 30 days post-launch_ |
| **Handle on enabled markets** | _TBD — Analytics to provide pre-launch baseline_ | **+5–10% lift** in first 90 days post-launch _(target needs Trading + Analytics confirmation)_ |
| **Customer churn — "no cashout" cited reason** | _TBD — CS to provide last-12-month support-ticket tag rate_ | **Reduce attribution to <1% of churn citations** within 6 months of launch |
| **Net revenue per cashed-out Bet** (uplift retained vs original NGR projection) | Modelled in VigRate Table | **Within ±10% of modelled NGR** — Trading + Finance to confirm post-launch reconciliation |
| **Cashout system reliability** (% of cashout requests resolved within 3 seconds end-to-end) | N/A — new system | **>95%** at launch |

> **Rule applied:** every KPI names a precise metric, names a baseline or explicit "TBD — owner named", and names a target with a timeframe.

---

## 4. Non-Financial Benefits

> Additional value that does not appear directly in financial metrics but is meaningful to the business or customer.

### 4.1 Additional Metrics

| Metric | Type | Trackable | Notes |
|:---|:---:|:---:|:---|
| Customer NPS shift on enabled-Bet experience | Customer | ✅ | Quarterly survey; segment cut by "used Cash Out at least once" |
| Cashout variant mix | Customer | ✅ | % Full vs % Partial vs % Auto across cashed-out Bets — sets the baseline for player-self-serve scoping later |
| Session length on enabled events | Customer | ✅ | Session duration for Punters with an open eligible Bet |
| Re-bet rate after a successful cashout | Customer | ✅ | % of Punters who place another Bet within 1 hour of cashing out |
| Trading hedging actions per market on enabled leagues | Business | ✅ | Volume of hedging trades correlated with cashout-driven liability shifts |
| Per-punter disable flag activations | Business | ✅ | Tracks how often Trading uses the lever; informs whether the bar is appropriate |
| Support-ticket category "Cashout — dispute / question" | Business | ✅ | New CS tag — track first 90 days for spike detection |

### 4.2 Measurement Approach

**📐 Baseline.** Baselines exist for NPS, session length, and re-bet rate at the platform level but **not** segmented by "Punter with eligible cashout Bet". Analytics must extend the data pipeline to tag eligible Bets and segment metrics accordingly. _Owner: Analytics — dependency confirmed before launch._

**📊 Dashboard.** No dashboard exists for Cash Out today. A new dashboard is required covering: cashout adoption rate (by variant), average cashout amount, uplift captured per market, suspended-during-validation rate, accept-changes-toggle on/off ratio, per-sport / per-league / per-event / per-market breakdowns, and per-punter-disable-flag activations. _Owner: Analytics — dependency confirmed before launch._

### 4.3 Hypothesis

> 🔮 **Hypothesis:** If we ship Full + Partial + Auto Cash Out (operator-configured) at v1, then we expect adoption to reach **5–10% of eligible open Bets** within the first 90 days, resulting in **measurable handle lift on enabled markets** and a **reduction in "no cashout" citations** in the support / churn channels — without introducing additional liability risk because the offer formula uses the proprietary VigRate Table with calibrated uplift, the per-punter disable flag gives Trading a surgical risk lever, and the maximum return is capped at the trading-tooling level.

**Targeted Areas**

- Customer bet history surface
- Trading workflow — VigRate Table calibration, exception list management, per-punter disable, liability reporting, and real-time ticker alerts (with profit-cashout highlight)
- Trading tooling configuration workflow (sport / league / event / market enablement)
- Customer Support ticket-handling playbook (new statuses, new transaction type, suspension scenarios, per-punter disable visibility)
- Analytics dashboards (new metrics, new segment cuts, variant breakdowns)

### 4.4 Cross-Department Review

| Department / Team | Reviewed? | Notes / Open Items |
|:---|:---:|:---|
| Product Operations | 🟡 Pending | Confirm v1 sport / league / event / market enablement defaults and the exception list scope |
| Analytics / Data | 🟡 Pending | Owns baselines for §3.2 KPIs; owns new Cash Out dashboard delivery, including variant-mix segmentation |
| Engineering — Customer Site | 🟡 Pending | Bet history page integration; Full / Partial / Auto UI; widget states; My Bets / Bet History / Transactions integration |
| Engineering — Trading Tooling | 🟡 Pending | Sport / league / event / market hierarchical enable; per-punter disable flag; VigRate Table maintenance UI; **trader ticker alerts (real-time stream + profit-cashout highlight rule)**; **master kill-switch**; **cashout config audit log** |
| Engineering — Pricing / Cashout Engine | 🟡 Pending | VigRate Table application, uplift model, true-odds vs served-odds fallback math |
| Trading / Risk | 🟡 Pending | Sign off on production VigRate Table values; uplift model; exception list; per-punter disable workflow; v1 Auto Cash Out semantics |
| Finance / Reporting | 🟡 Pending | New transaction types ("Wager Cashed Out" credit + matching debit, including Partial-cashout pattern); reconciliation rules |
| Front-End / UX | 🟡 Pending | Bet history page Cash Out UI; Full / Partial slider / Auto setup; widget visual states; mobile-web responsive |
| QA / Test | 🟡 Pending | Full state matrix: every widget state × Show Confirmation × Accept Changes × every variant (Full / Partial / Auto) × per-punter disable on/off |
| Customer Support | 🟡 Pending | New "Cashed Out" / "Partially Cashed Out" status playbook; dispute handling for price-changed-during-validation; per-punter disable communication; Auto trigger explanations |
| Legal / Compliance | 🔴 Blocked | New automated decision affecting Punter funds (especially Auto Cash Out automatic-trigger semantics); license-jurisdiction sign-off required |
| VIP Relations | 🟡 Pending | Per-punter disable flag use cases; VIP-segment economics |

> **Rule applied:** Trading, Risk, Finance, and CS are listed (PRINCIPLE 8). Analytics is listed because new transaction and metric categories are introduced. Legal is listed because the feature creates a new automated decision affecting Punter funds.

### 4.5 Incremental Delivery / Phases

| Phase | Description / Deliverable | Timeline | Value Delivered |
|:---|:---|:---|:---|
| **Phase 1 — V1 Launch** | Full + Partial + Auto (operator-configured) Cash Out on the bet history page across every market enabled in trading tooling. Hierarchical sport / league / event / market enable. Per-punter disable flag. VigRate Table integration with true-odds-preferred / served-odds-fallback. Trading liability reporting. Full widget state coverage. | **Target launch — TBD by joint Trading + Eng readiness** | Engine built right the first time; competitive parity on the three foundational variants |
| **Phase 2 — Player Self-Serve and Surface Expansion** | Player-set Auto Cash Out targets; player-configured Partial defaults; expansion to additional surfaces (e.g. live betslip, push / email notifications). | **Post-V1, scope follows Phase 1 stability** | Engagement deepening once the engine is proven |

---

## 5. Use Case Scope

### 5.1 In Scope

> Scenarios and workflows that **ARE** covered by V1 launch.

---

#### 🎬 Use Case 1: Full Cash Out — Settle the Whole Stake When the Price Has Not Moved

| | |
|:---|:---|
| **👤 Actor** | Punter with an open Bet on a market enabled in trading tooling, where the current market price is the same as the price at Bet placement |
| **🎯 Goal** | Settle the entire stake early at a known fee, without waiting for the market to settle |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter places $100 on **Selection A @ +105**. Trading tooling assigns this market the default VigRate Table with an uplift profile.
2. Some time later, the market price for Selection A is unchanged.
3. Punter opens the sportsbook → bet history page → **Cash Out** view. The widget shows the open Bet card with a Cash Out icon and a "Cash Out $XX.XX" button.
4. Punter clicks the button. With "Show Confirmation" ON, the button switches to "Confirm $XX.XX ▶" highlighted in light green for 5 seconds.
5. Punter clicks again to confirm. Validation runs against the current market price.
6. Price has not changed during validation → cashout proceeds. Success message ("✓ Cashed Out · $XX.XX returned to balance"). After 5 seconds the Bet card moves from open → graded.
7. The Punter sees the Bet under Bet History with status "Cashed Out", To Return = $XX.XX, and in Transactions as a "Cashed Out" credit + the original "Wager (Debit)" line.
8. Trading sees the liability change in the platform's liability report — Profit, Payout, and Volume update on every selection in the market.

</details>

---

#### 🎬 Use Case 2: Full Cash Out — Settle When the Price Has Moved

| | |
|:---|:---|
| **👤 Actor** | Punter with an open Bet on an enabled market, where the market price has moved since Bet placement |
| **🎯 Goal** | Settle the whole stake at a price that reflects the move, minus the uplift the platform retains |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter has $100 on Selection A. The current market makes Selection A more likely to win than at Bet placement.
2. Cashout offer is calculated using the VigRate Table applied to the live odds. Preferred input: the provider's 100% (true) odds. If those aren't available, the system falls back to the currently-served odds and applies a larger uplift to compensate.
3. Widget on the bet history page shows "Cash Out $185.03" (example value). Amount updates as the market price moves.
4. Punter clicks Cash Out. Validation begins. With "Show Confirmation" OFF, the validation starts immediately on first click.
5. Price does not change during validation → cashout proceeds. Success state shown. Bet graded as "Cashed Out".
6. Trading sees Profit, Payout, and Volume update on every selection in the market in the platform's liability report.

</details>

---

#### 🎬 Use Case 3: Partial Cash Out — Settle a Portion, Let the Rest Ride

| | |
|:---|:---|
| **👤 Actor** | Punter with an open Bet on an enabled market who wants to bank some return now and keep exposure on the rest |
| **🎯 Goal** | Cash out a portion of the original stake — receive the proportional cashout amount immediately, while a residual stake remains live until the market settles |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter has $100 on Selection A. Cashout offer reads "Cash Out $180". The Punter wants to bank some of the gain but keep upside on a portion.
2. Punter opens the **Partial Cash Out** UI — a slider or numeric input lets them choose how much of the stake to cash out (e.g. 60%).
3. Widget shows two values: **Cash Out Now $108** (60% of $180) and **Remaining Bet $40 stake @ original odds**.
4. Punter clicks Cash Out. Validation runs. Cashout proceeds at the displayed amount.
5. Bet ledger:
   - One entry credits $108 ("Wager Cashed Out — Partial").
   - The remaining $40 stake stays open at the original odds; To Win and To Return on the residual update accordingly.
6. Bet history shows two rows: a "Partially Cashed Out" graded portion and a "Pending — residual" open portion that remains eligible for further cashout actions until the market settles.
7. Trading sees the liability impact on the cashed-out portion in the unified liability report; the residual continues to appear as a normal open Bet.

</details>

---

#### 🎬 Use Case 4: Auto Cash Out — Operator-Configured Trigger Fires at Threshold

| | |
|:---|:---|
| **👤 Actor** | Punter with an open Bet on a market that has an operator-configured Auto Cash Out trigger active |
| **🎯 Goal** | Have the open Bet automatically cashed out when the live cashout offer crosses the operator-configured threshold, without the Punter having to be actively watching |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Trading has configured an Auto Cash Out trigger for this market in trading tooling — e.g. "auto-cash-out at 90% of potential return for any open Bet on this market".
2. Punter places $100 on Selection A. Potential return = $200. The auto-trigger threshold is therefore $180.
3. As the market moves, the live cashout offer crosses $180.
4. The cashout engine evaluates the trigger condition is met → fires the cashout automatically using the same validation flow as a manual request.
5. Cashout completes at the prevailing offer (which may be ≥ the threshold by a small margin). Bet is graded as "Cashed Out — Auto".
6. Punter receives a notification (in-app + email, where Punter has enabled email notifications) showing the auto-fire occurred and the amount returned.
7. Trading sees the same liability update flow as a manual cashout.

> **Note:** Auto Cash Out in v1 is **operator-configured only**. Punter-set targets are deferred to Phase 2.

</details>

---

#### 🎬 Use Case 5: Cash Out When Price Changes UP During Validation

| | |
|:---|:---|
| **👤 Actor** | Punter with an open Bet on an enabled market |
| **🎯 Goal** | Cash out and have the system handle a favourable price move automatically |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter has $50 on Selection A. Cashout offer reads "Cash Out $42.30".
2. Punter clicks Cash Out. Validation begins.
3. **During validation, Selection A's price moves up.** Cashout offer recalculates to $48.20 using the same formula with the new market state.
4. Widget shows "▲ The Cash Out return has changed" message in green. New amount $48.20 is highlighted in light-green pill style.
5. Because the price moved **UP** (favourable), cashout proceeds automatically at the new higher amount regardless of the Accept Cash Out Changes toggle.
6. Success: "✓ Cashed Out · $48.20 returned to balance". Message clears after 3 seconds.
7. Trading sees the liability update in the platform's liability report.

</details>

---

#### 🎬 Use Case 6: Cash Out Interrupted Because Price Moved DOWN and "Accept Changes" is OFF

| | |
|:---|:---|
| **👤 Actor** | Punter with an open Bet on an enabled market — has "Accept Cash Out Changes" toggle OFF |
| **🎯 Goal** | Be offered a chance to confirm or reject the new lower amount when the market moves against them mid-validation |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter clicks "Cash Out $80" on an open Bet. Validation begins.
2. During validation, market price moves against the Punter — new offer is $75.
3. Widget shows "▼ The Cash Out return has changed" in amber. New amount $75 is highlighted in amber pill style.
4. Because Accept Cash Out Changes is **OFF**, the cashout flow is **interrupted**. The Bet is NOT cashed out. The button now reads "Cash Out $75" — Punter must click again if they want to proceed at the lower amount.
5. The original $80 offer is gone. The next click starts a fresh validation against whatever the price is at that moment.

</details>

---

#### 🎬 Use Case 7: Cash Out Suspended During Validation — Market Goes Off-Price Mid-Flow

| | |
|:---|:---|
| **👤 Actor** | Any Punter clicking Cash Out on an open Bet |
| **🎯 Goal** | Receive a clear signal that the cashout could not complete because the market suspended, and not be debited or credited |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter clicks "Cash Out $XX.XX". Validation begins.
2. Mid-validation the market suspends.
3. Widget shows "The Cash Out was suspended" message for 3 seconds.
4. Button reverts to the standard Suspended state (lock icon + "Suspended" label).
5. Bet remains open. No funds movement. No change in Bet History / Transactions.
6. When the market reopens, the cashout offer reappears at the new market price.

</details>

---

#### 🎬 Use Case 8: Cash Out When the Bet's Original Line Has Moved

| | |
|:---|:---|
| **👤 Actor** | Punter with an open Bet whose original line is no longer the platform's main line |
| **🎯 Goal** | Receive an accurate cashout offer for their original-line Bet even though the platform's main line has moved |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter places $100 on **Selection A at line 217 @ -110**. Current main line on the platform is now **line 218.5 @ -110**.
2. The system needs the current price for the **original** line (217), not the current main line (218.5), to apply the formula correctly.
3. The platform applies the Trading-selected approach (see §6.2 open item):
   - **(a) Preferred:** query the odds feed for the current price of the original line. Apply the standard formula.
   - **(b) Fallback:** suspend cashout while the main line is different from the original line. Cashout reappears when the line returns to the original or when the Bet grades.
4. If approach (a) succeeds: widget shows "Cash Out $XX.XX". Punter follows the standard cashout flow.
5. If approach (b) is in effect: widget shows the Suspended state. Punter sees a tooltip / message explaining the line moved. Bet remains open. No funds movement.

</details>

---

#### 🎬 Use Case 9: Cash Out Suspended When the Bet is at Risk of Push

| | |
|:---|:---|
| **👤 Actor** | Punter with an open Bet where the live event-state has reached the line that would settle the Bet as a push |
| **🎯 Goal** | Be protected from cashing out during a push-risk window so the Punter does not lose value because of a 50/50 win-or-push state |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter has an open Bet whose settlement depends on a numeric outcome being above or below the original line.
2. Mid-event the live result reaches a value **equal** to the original line — the Bet is in a push-risk state. If the event ended now the wager would be returned (push, no win or loss).
3. The widget shows the Suspended state with a tooltip explaining "the live result is on the line — cashout temporarily unavailable".
4. The result moves off the line: cashout returns to the available state with a new offer reflecting the new market state.
5. If the event ends with the live result still equal to the original line, the existing void / push handling settles the Bet (wager returned). No cashout is offered during the push-risk window.

</details>

---

#### 🎬 Use Case 10: Trader Sees a Cashed-Out Bet Alert in Real Time

| | |
|:---|:---|
| **👤 Actor** | Trading operator monitoring the trading ticker stream |
| **🎯 Goal** | See cashed-out bets the moment they happen, with enough information to make immediate hedging decisions and to flag profitable-cashout patterns |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. A Punter cashes out an open Bet on an enabled market.
2. Within seconds, a new alert row appears in the trading ticker the operator is watching, distinctly formatted from a normal "Bet Sold" alert.
3. The alert row carries: **Status** (Bet Cashed Out), **Time**, **Customer**, **original Risk**, **Cashout amount**, the **original odd**, the **new odd** used at cashout, and the underlying wager **description** (sport / market / selection).
4. If the cashed-out **amount is greater than the original Risk** — the customer cashed out at a profit — the alert row is automatically highlighted with a trader-configured background and foreground colour. This colour rule is set once in the ticker Settings and applies to every subsequent profitable-cashout alert.
5. The operator scans the ticker, identifies the cashed-out alerts in the stream, and routes hedging decisions accordingly.
6. The same liability-report update for this bet is reflected in the trading liability view (see [cashout-reporting](https://github.com/KikePinnaple90/cash-out/blob/main/cash%20out%20skills/cashout-reporting/SKILL.md)) — the alert is the trader's real-time visual signal; the liability report is the running aggregate.

> **Note:** The same alert flow applies whether the cashout happens before the game starts or during live play — the alert always lands in the ticker the trader is currently watching. The alert format and the profit-highlight rule are consistent across both ticker surfaces.

</details>

---

#### 🎬 Use Case 11: Trading Disables Cash Out for a Specific Punter

| | |
|:---|:---|
| **👤 Actor** | Trading operator with access to the per-punter disable flag in trading tooling |
| **🎯 Goal** | Surgically remove cashout availability from a flagged account so the Punter cannot request it AND the engine would reject any request that somehow arrived |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Trading flags Punter X in trading tooling (cashout-disabled = true).
2. **UI side:** when Punter X opens the bet history page, the Cash Out icon and offer are entirely hidden on every open Bet. The widget shows the Bet as a normal open wager with no cashout affordance.
3. **Engine side:** if a stale client somehow submits a cashout request for Punter X (e.g. from a cached page), the betting engine rejects the request with a clear error that does not expose the disable rationale to the Punter.
4. The flag is reversible — Trading can clear it at any time, restoring cashout availability immediately.
5. The flag is logged for audit and visible in CS tooling so support agents can see why a Punter is asking "where's my cashout button".

</details>

---

#### 🎬 Use Case 12: Trading Configures a Sport / League / Event / Market Enablement

| | |
|:---|:---|
| **👤 Actor** | Trading operator with access to the cashout enablement hierarchy in trading tooling |
| **🎯 Goal** | Enable or disable cashout at any level of the hierarchy with the change propagating immediately to the customer site and the cashout engine |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Operator opens the cashout enablement view in trading tooling. Hierarchy: **Sport → League → Event → Market**. Default is "on for everything we offer", with a Trading-supplied exception list for ineligible markets.
2. Operator drills into a specific event and disables cashout for the **Spread** market on that event only — leaving the rest of the event's markets cashout-enabled.
3. Save. The change propagates within minutes:
   - The customer site stops surfacing cashout for any open Bet on that specific event-market.
   - The cashout engine rejects any new cashout requests for open Bets on that event-market with a "market not eligible" reason.
   - Existing open Bets on that event-market remain valid wagers and settle normally at event end.
4. The operator can re-enable cashout on the same surgical scope at any time.

</details>

---

### 5.2 Out of Scope

> What is explicitly **NOT** included in V1 launch.

- ❌ **Player-set Auto Cash Out targets** — V1 Auto Cash Out is operator-configured only. Punter self-serve is Phase 2.
- ❌ **Player-configured Partial defaults** — V1 Partial uses a session-level slider / input; no saved player preferences. Phase 2.
- ❌ **Multi-Bet cashout** (Bets composed of multiple selections) — single-Bet only in V1. Multi-leg pricing requires a separate model.
- ❌ **Mobile native app** — V1 is web only (desktop + mobile-web). Native app integration is Phase 2+.
- ❌ **Retail / cashier / kiosk surfaces** — no in-store cashout in V1.
- ❌ **Cashout on settled / void Bets** — eligibility ends the moment the Bet settles by normal grading; void handling continues per the existing flow.
- ❌ **Alternative-line cashout offer** (cash out at a different line than the Bet's original) — V1 always references the Bet's original line.
- ❌ **Surfaces beyond bet history** — live betslip in-place cashout, push / email cashout-offer notifications, and similar contextual placements are Phase 2 scope.

> **Rule applied:** Out-of-scope items here are scenario-level. Feature-level exclusions are also listed in §6 Won't Have to align expectations on capability.

---

## 6. Product Feature Scope (MoSCoW)

> 📚 For more on MoSCoW prioritization, see [productplan.com/glossary/moscow-prioritization](https://www.productplan.com/glossary/moscow-prioritization).

### 🔴 Must Have — Non-negotiable. The initiative fails without these.

| # | Feature / Capability | Rationale | Owner |
|:--:|:---|:---|:---|
| 1 | **Full Cash Out** — settle the whole open stake at the offered amount in one transaction | Foundational variant. Without it the feature does not exist. | Trading + Eng (Pricing) + Eng (Customer Site) |
| 2 | **Partial Cash Out** — settle a Punter-chosen portion of the stake; remainder stays open at the original odds and remains eligible for further cashout actions until settlement | Engine built properly the first time means the proportional ledger pattern is in place from day one. Retro-fitting Partial later forces invasive changes. | Trading + Eng (Pricing) + Eng (Customer Site) + Finance |
| 3 | **Auto Cash Out (operator-configured)** — Trading sets per-market trigger thresholds; the engine evaluates and fires the cashout automatically when the live offer meets the threshold | Same engine reasoning as Partial — auto-firing has to be supported correctly from day one to avoid retro-fit. Player-set targets are explicitly Phase 2. | Trading + Eng (Pricing) + Eng (Customer Site) |
| 4 | **Cashout pricing via VigRate Table with calibrated uplift on top of the market margin**. Preferred input: provider's 100% (true) odds; fallback: currently-served odds with a larger calibrated uplift to compensate for the already-baked-in provider margin. | The pricing engine is in-house and proprietary. Without the VigRate Table + uplift discipline, cashout offers are either uncompetitive or unprofitable. | Trading + Eng (Pricing) |
| 5 | **Hierarchical operator enablement** at sport / league / event / market level from trading tooling, with a Trading-supplied exception list. Default: "on for everything we offer". | Trading needs surgical control to enable / disable cashout at any granularity without touching code. | Trading + Eng (Trading Tooling) |
| 6 | **Per-punter disable flag** — when set, the cashout UI is hidden for that Punter on every open Bet AND the cashout engine rejects any cashout request for that Punter. Flag is reversible, audit-logged, and visible to CS. | Complete risk lever — covers both the offer side (UI) and the request side (engine). Without both, Trading has a half-measure. | Trading + Eng (Customer Site) + Eng (Pricing) + CS |
| 7 | **Cashout entry point on the bet history page** with the full widget state set: Empty, Available, Suspended, Confirm, Changed Up, Changed Down, Suspended-during-validation, Successfully Cashed Out, Partially Cashed Out, Auto-Cashed-Out | Bet history is the v1 primary surface. Each state is a distinct UX requirement and a QA matrix entry. | Eng (Customer Site) + UX + QA |
| 8 | **Show Confirmation toggle** — 5-second confirm window with a light-green amount pill before cashout proceeds | Customer-protection feature; reduces accidental cashouts | Eng (Customer Site) + UX |
| 9 | **Accept Cash Out Changes toggle** — controls behaviour when price moves DOWN mid-validation (ON → proceeds at lower amount; OFF → flow interrupted, customer must re-confirm) | Customer-control feature; without it the change-down flow has no completion path | Eng (Customer Site) + UX |
| 10 | **Cash Out icon and discoverability** — icon shown in Offering, Betslip, and Bet History pending. NEW badge shown to first-time visitors and dismissed once seen. | Discoverability — Punters need to see where the feature applies | Eng (Customer Site) + UX |
| 11 | **Post-cashout visibility** — Bet History new "Cashed Out" / "Partially Cashed Out" / "Auto Cashed Out" statuses; new "To Return" column with status filter; Transactions new type "Cashed Out" + description "Wager Cashed Out" | Punters must see what was returned and where; Finance reconciliation needs the transaction type | Eng (Customer Site) + Finance |
| 12 | **Trading liability reporting** for cashed-out Bets, including the Partial-cashout pattern (proportional impact + residual still open). The cashed-out portion's payout is replaced by the cashout amount; Profit, Payout, and Volume update on every selection in the market; Risk and Count adjust correctly for Full vs Partial. | Trading cannot hedge without real-time liability visibility. Partial pattern must report cleanly so liability isn't double-counted. | Eng (Trading Tooling) + Trading |
| 13 | **Original-line price resolution** when the Bet's original line is no longer the platform's main line. Trading-selected approach: (a) query the odds feed for the original-line price; or (b) suspend cashout while the original line is no longer the main line. | Without this, cashout cannot operate correctly when the line has moved — the formula has no valid `New Odd` for the original line. | Trading + Eng (Pricing) |
| 14 | **Push-risk suspension** — when the live result reaches a value equal to the line that would settle the Bet as a push, cashout is suspended for that Bet until the result moves off the line or the event grades | Protects the Punter from accepting a cashout offer that ignores the push outcome; protects the platform from having to model the three-way outcome inside the cashout formula | Trading + Eng (Customer Site) |
| 15 | **Cashout button suspended** when no current price for the market, when the market is suspended, when the original-line price cannot be resolved, when the Bet is in a push-risk window, or when the per-punter disable flag is set | Without a usable price, a clear settlement state, or an entitled Punter, the formula cannot evaluate — must not offer | Eng (Customer Site) + Eng (Pricing) |
| 16 | **New transaction types in the ledger** — "Wager Cashed Out" (Full) credit + matching debit; "Wager Cashed Out — Partial" credit + residual stake unchanged; "Wager Cashed Out — Auto" credit + matching debit | Finance reconciliation, NGR calculation, license-jurisdiction reporting. Partial requires distinct ledger handling so the residual is accounted correctly. | Finance + Eng (Pricing) |
| 17 | **New analytics events** for the full validation flow — cashout-offered, cashout-clicked, cashout-confirmed, cashout-suspended, cashout-completed, cashout-by-variant (Full / Partial / Auto), cashout-by-market, accept-changes-toggle, show-confirmation-toggle, per-punter-disable-activation | Without these, none of §3.2 KPIs can be measured and Trading cannot calibrate the VigRate Table over time | Analytics + Eng (Customer Site) |
| 18 | **Trader cashed-out alerts in the trading ticker** — every cashed-out Bet appears in real time as a distinct alert row with: status, time, customer, original Risk, cashout amount, original odd, new odd used at cashout, and the underlying wager description. The alert format is visually distinct from normal "Bet Sold" alerts so traders can pattern-match cashouts at a glance. | Trading needs a real-time visual signal of cashout activity to make immediate hedging decisions. Without it, cashouts only show up in aggregate liability reports — too slow for live risk management. | Eng (Trading Tools) + Trading + UX |
| 19 | **Profit-cashout highlight rule** — when Cashout Amount > Risk (customer cashed out at a profit), the ticker alert row is rendered with a trader-configured background and foreground colour. The colour rule is set once in the ticker Settings and applies to every subsequent profitable-cashout alert. | Profitable cashouts are the operationally interesting subset for risk monitoring. The highlight lets Trading scan the stream and act on patterns without manually filtering. | Eng (Trading Tools) + Trading + UX |
| 20 | **Master Cash Out kill-switch** — single platform-level toggle to disable Cash Out across every brand / sport / league / market in one click. Overrides all other settings. Reversible. | Emergency lever for staged rollout, rollback, or platform-wide incident response. Without it, Trading would have to disable each level of the hierarchy individually. | Trading + Eng (Trading Tools) |

### 🟠 Should Have — Important but not vital.

| Feature / Capability | Rationale | Owner |
|:---|:---|:---|
| **Calculator / validation tool** in trading tooling — ad-hoc cashout offer validation against any current VigRate Table calibration | Trading verification tool; reduces "is this the right number?" tickets to Eng | Eng (Trading Tooling) + Trading |
| **Live-update widget** without manual refresh (push-driven price updates) | Improves customer experience; reduces stale-offer abandonment | Eng (Customer Site) |
| **Per-punter disable audit trail** in CS tooling — agents can see when and why a flag was set without exposing the rationale to the Punter | Reduces CS escalation time when flagged Punters call in | Eng (Trading Tooling) + CS |
| Validation flow telemetry — % suspended-during-validation, % accept-changes-on, % accept-changes-off, % suspended-due-to-line-move, % suspended-due-to-push-risk, % per-punter-disable-blocked | Feeds back into VigRate Table calibration and the line-move-handling decision | Analytics |
| **Cashout config audit log** — every cashout configuration change (master toggle, per-sport / league / event / market enable, per-punter disable activation, template change) is logged with timestamp, operator, and before / after values | Required for compliance review and post-incident operational analysis; without it, Trading cannot reconstruct who changed what when | Eng (Trading Tools) + Trading Ops |

### 🟡 Could Have — Nice to have.

| Feature / Capability | Rationale | Owner |
|:---|:---|:---|
| Localised widget messages (multiple languages) | Multi-region customer base | UX + Localization |
| Customer-facing analytics ("you've cashed out N times this month, saved $X") | Engagement gamification | Product + Eng |
| Print Bet Slip with Cashed-Out / Partially Cashed Out status | Some Punters request hardcopy ticket records via CS | Eng (Customer Site) |

### ⚫ Won't Have — Explicitly excluded in V1.

| Feature / Capability | Rationale |
|:---|:---|
| Player-set Auto Cash Out targets | V1 Auto is operator-configured only; Phase 2 |
| Player-configured Partial defaults (saved cross-session) | V1 Partial is session-only; Phase 2 |
| Multi-Bet cashout (Bets composed of multiple selections) | Multi-leg pricing logic + per-leg uplift is a separate model |
| Mobile native app cashout | Web only at launch |
| Retail / kiosk cashout | Not in scope for digital-first V1 |
| Cashout on settled or voided Bets | Existing void / refund flow remains canonical |
| Alt-line cashout offer (cash out on a different line than the Bet's original) | V1 always references the original line; alt-line cashout is a separate UX |
| Cashout surfaces beyond bet history | Live betslip in-place cashout, push / email notifications, and similar are Phase 2 scope |

### 6.1 Assumptions & Constraints

<details open>
<summary><strong>✅ Assumptions</strong></summary>

- The proprietary cashout algorithm (VigRate Table) is the canonical pricing engine and is NOT being redesigned by this initiative.
- The platform's existing wallet / balance / settlement engine handles cashout credits via the new "Wager Cashed Out" transaction types — no new wallet rules required, including for the Partial residual pattern.
- The same back-office configuration applies to every open Bet — Trading does not need separate templates for different timing of placement / cashout.
- The same liability reporting flow applies to every cashed-out Bet, including Partial.
- The same cashout formula applies to every Bet — only price state matters.
- Trading will provide validated production VigRate Table values and uplift calibration before launch, including a separate (larger) uplift profile for the served-odds fallback path.
- Trading will supply the ineligible-markets exception list before scope is locked.
- Trading will select and approve a single line-move-handling approach (alt-line query OR suspend on line move) before code freeze.
- Trading will define v1 Auto Cash Out semantics (per-market threshold model: % of potential return, fixed amount, or other) before code freeze.
- The odds feed delivers 100% (true) odds for the markets we want to cashout-enable, at the cadence required by the formula. Where 100% odds aren't available, the served-odds fallback engages with the larger uplift.
- Push-risk suspension is acceptable to Trading as the V1 handling — alternative push-modelling inside the formula is not required.

</details>

<details open>
<summary><strong>⚠️ Constraints</strong></summary>

- Must not break or weaken the platform's existing settlement logic, void handling, or wallet integrity.
- Must not introduce new regulatory categories without Legal sign-off in each license jurisdiction (especially for Auto Cash Out, which is automated decisioning on Punter funds).
- Must operate within the existing odds-feed cadence — no requirement for a faster feed at launch.
- Liability reporting must update in near-real-time so Trading hedging decisions are not based on stale data.
- The customer site, trading tooling, and pricing engine must remain in lockstep — a configuration change must propagate to live offers and to liability calculations within minutes.
- Cashout cannot offer a price derived from an unverified estimate (e.g. a points-conversion table) without explicit Trading sign-off; the production approach for line moves must be either real feed pricing or suspension.

</details>

### 6.2 Unknowns & Open Items

| Open Item / Unknown | Owner | Due Date | Status |
|:---|:---|:---:|:---:|
| **Margin uplift model** — single global uplift, or tunable per sport / league / market / bet type? | Trading | TBD — before code freeze | 🔴 Open |
| **Exception list** — which markets or bet types are structurally ineligible for cashout? | Trading | TBD — before code freeze | 🔴 Open |
| **Fallback math** — what additional uplift compensates for using served (already-margined) odds when true 100% odds aren't available from the provider? | Trading | TBD — before code freeze | 🔴 Open |
| **Auto Cash Out semantics (v1)** — operator-set trigger targets per market, or a simpler global default (e.g. "trigger at X% of current cashout value")? | Trading + Product | TBD — before code freeze | 🔴 Open |
| **Partial Cash Out granularity** — slider (continuous), preset chips (e.g. 25/50/75%), or numeric input (any %)? | UX + Product | TBD — UX review | 🔴 Open |
| **Per-punter disable surface** — is the flag managed from existing risk tooling, a new screen, or both? | Trading Ops + Eng (Trading Tooling) | TBD — before code freeze | 🔴 Open |
| **Ticker alert visual format** — confirm field set, ordering, status label, and the visual differentiator between "Bet Sold" and "Bet Cashed Out" rows | UX + Trading | TBD — before code freeze | 🔴 Open |
| **Profit-cashout highlight default colours** — confirm the default background and foreground colours used before a trader configures their own | UX + Trading | TBD — UX review | 🔴 Open |
| **Line-move-handling approach** (alt-line feed query OR suspend on line move) and confirm the chosen approach is implementable within timeline | Trading + Eng (Pricing) + Vendor Mgmt | TBD — before code freeze | 🔴 Open |
| **Push-risk suspension UX** — message and tooltip wording | UX + CS | TBD — before code freeze | 🔴 Open |
| **Legal / Compliance sign-off path** per license jurisdiction, with extra attention to the Auto Cash Out automatic-trigger pattern | Legal | TBD — before launch | 🔴 Open |
| **CS playbook handover** — new statuses (Cashed Out, Partially Cashed Out, Auto Cashed Out), dispute handling for change-down flow, per-punter disable explanation | CS Manager | TBD — 4 weeks before launch | 🟡 In Progress |
| **Analytics baselines and dashboard delivery timeline** for §3.2 KPIs, including variant-mix segmentation | Analytics | TBD — before launch | 🟡 In Progress |
| **Phase 2 scoping** — player-set Auto targets and surface expansion (live betslip, push / email notifications) | Product | TBD — Phase 2 planning | 🔴 Open |
| **"Show Confirmation" and "Accept Changes" toggle defaults** at first-time-user state | UX + Product | TBD — UX review | 🔴 Open |
| **Liability reporting near-real-time SLA** (acceptable lag between cashout event and Trading-tool update) | Trading + Eng (Trading Tooling) | TBD — before code freeze | 🔴 Open |

> **Rule applied:** every open item is a specific answerable question with a named owner — no generic "TBD".

### 6.3 Active Programs / Potential Combines

| Program / Initiative | Overlap / Relationship | Recommendation |
|:---|:---|:---|
| Customer site redesign / design system refresh | Cash Out widget visual states must align with current design system | **Coordinate** — UX joint review required |
| Odds feed migration / upgrade | Cashout requires current decimal odds at speed; reliability is a critical dependency. If the line-move approach selected is alt-line querying, this dependency intensifies. | **Monitor for conflict** — feed regression is a launch blocker |
| Trading tooling modernisation | The hierarchical sport / league / event / market enablement, the per-punter disable flag UI, and VigRate Table maintenance all live in trading tooling | **Coordinate** — joint planning to avoid double-rework |
| Wallet / payment reconciliation modernisation | New "Wager Cashed Out" transaction types (Full, Partial, Auto) land in the same ledger | **Coordinate** — Finance to confirm no overlap |
| Mobile native app rollout | Cash Out V1 is web-only; mobile is explicit Phase-2 scope | **Do NOT combine** — mobile parity is a separate program |

If any of the above are not currently active programs, this section should be updated before the Investment Template.

---

## 7. Use Case Diagrams

> Outline user experience flows and workflows. Insert diagrams, flowcharts, or wireframe references below.

### 🎨 Diagram 1: Cash Out Validation Flow (Full)

> 🔗 _Working interactive HTML reference: [cashout_suite_v2_5.html](https://github.com/KikePinnaple90/cash-out/blob/main/cashout_suite_v2_5.html) → Customer site → Widget tab. Final design link **TBD**._

**Description.** Diagrams the end-to-end validation flow for a Full cashout request from the bet history page. From the moment the Punter clicks the Cash Out button, through the validation window, to the four resolution branches: (a) no price change → cashout proceeds; (b) price up → cashout proceeds at the higher amount automatically; (c) price down + Accept Changes ON → cashout proceeds at the lower amount; (d) price down + Accept Changes OFF → flow interrupted, new offer surfaced.

---

### 🎨 Diagram 2: Partial Cash Out Flow

> 🔗 _Final design link **TBD**._

**Description.** Shows the Partial Cash Out flow: open Bet at $100 stake → Punter selects a portion (slider or input) → engine calculates proportional cashout offer + residual stake → validation runs identically to Full → on completion, ledger receives a "Wager Cashed Out — Partial" credit + the residual remains open at the original odds and is itself eligible for further cashout actions. Bet History shows two rows derived from the same original ticket.

---

### 🎨 Diagram 3: Auto Cash Out (Operator-Configured) Flow

> 🔗 _Final design link **TBD**._

**Description.** Shows the operator-configured Auto Cash Out flow: Trading sets a trigger threshold per market in trading tooling → engine continuously evaluates the live cashout offer for every open Bet → when threshold crossed, engine fires the cashout automatically using the same validation flow as a manual request → Punter is notified post-fact. Auto in v1 is operator-side only; player-set targets are Phase 2.

---

### 🎨 Diagram 4: Hierarchical Enablement and Per-Punter Disable

> 🔗 _Final design link **TBD**._

**Description.** Two-part diagram:
- **(a)** The hierarchical enablement tree — Sport → League → Event → Market — showing how a Trading toggle at any level cascades down. Default: on for everything; exceptions are explicit overrides.
- **(b)** The per-punter disable flag — when set, both the customer-site UI hides the cashout option AND the betting engine rejects any cashout request for that Punter. The flag is logged for audit and visible to CS.

---

### 🎨 Diagram 5: Trading Liability Update Flow

> 🔗 _Working interactive HTML reference: [cashout_suite_v2_5.html](https://github.com/KikePinnaple90/cash-out/blob/main/cashout_suite_v2_5.html) → Reports tab. Final design link **TBD**._

**Description.** Single decision diagram showing how a cashed-out Bet updates the platform's liability report:

- **(a)** **Full** — the Bet's payout in the report is replaced by the cashout amount; Profit, Payout, Volume update on every selection; Risk and Count unchanged.
- **(b)** **Partial** — the cashed-out portion's payout is replaced proportionally; the residual continues to appear as a normal open Bet with proportional Risk / Payout. Trading sees both impacts cleanly.
- **(c)** **Auto** — same update flow as Full or Partial depending on the trigger configuration.

---

### 🎨 Diagram 6: Line-Move and Push-Risk Decision Flow

> 🔗 _Working reference: [cashout-spread-totals SKILL](https://github.com/KikePinnaple90/cash-out/blob/main/cash%20out%20skills/cashout-spread-totals/SKILL.md). Final design link **TBD**._

**Description.** Combined decision diagram covering the two scenarios where the platform must take special action:

- **(a)** Original line equals current main line → standard cashout flow applies.
- **(b) — Trading-selected approach (a)**: original line ≠ main line → query the odds feed for the original-line price → apply standard formula.
- **(c) — Trading-selected approach (b)**: original line ≠ main line → cashout suspended; reverts when the line returns to original or the Bet grades.
- **(d) Push-risk window**: when the live result equals the line that would settle the Bet as a push, cashout is suspended until the result moves off the line or the event grades.

---

## 8. Roadmap Alignment

| | |
|:---|:---|
| **🏁 Roadmap / Swim Lane** | Customer Experience & Trading Operations |
| **🎯 Roadmap Theme** | Competitive parity, retention engagement, and complete risk-control coverage |
| **⏰ Timeframe** | **Near-term (V1)** + **Mid-term (Phase 2 player self-serve and surface expansion)** |

### Delivery Milestones

| Roadmap Year | Initiative / Milestone | Dependency | Status |
|:---|:---|:---|:---|
| **Year 1 — Near-term** | **Phase 1:** Cash Out V1 — Full + Partial + Auto (operator-configured) on the bet history page; hierarchical enablement; per-punter disable; VigRate Table integration with true-odds-preferred / served-odds-fallback; trading liability reporting. | Trading sign-off on VigRate Table + uplift model + exception list + Auto v1 semantics; line-move-handling approach selected; trading tooling Eng delivery; pricing engine delivery; customer site delivery. | 🟡 Drafting Scope |
| **Year 1 — Mid-term** | **Phase 2:** Player self-serve — punter-set Auto targets, player-configured Partial defaults — and surface expansion (live betslip in-place cashout, push / email cashout-offer notifications). | Phase 1 stable; Trading capacity; new compliance review for player-set automated decisions. | 🔴 Not started |

---

> 📝 _This document is a living template. Update version and status as the initiative progresses through review and approval._
