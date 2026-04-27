# 📋 Scope Document — Customer Site & Operations: Cash Out V1 Launch

![Version](https://img.shields.io/badge/version-v0.1-blue) ![Status](https://img.shields.io/badge/status-Draft-orange) ![Date](https://img.shields.io/badge/date-2026--04--27-lightgrey) ![Module](https://img.shields.io/badge/module-Customer%20Site%20%2B%20Operations-informational)

| Program Owner | Date | Version | Status |
|:---|:---|:---|:---|
| _To be assigned_ | 27 Apr 2026 | v0.1 | Draft |

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

> 💡 **In a nutshell:** Cash Out lets Punters settle pending Bets early — before the game starts or while it's live — at a price calculated from current market odds, with offers driven by a configurable back-office template and liability updated in Synergy and N-Game in real time.

The initiative delivers Cash Out across three surfaces: a customer-facing widget on the BetOnline / SB26 web sportsbook (Pre-Match and Live), a back-office configuration tool (templates, sport/league enablement, customer-group rules), and trading-platform liability reporting in Synergy (Pre-Match-placed Bets) and N-Game (Live-placed Bets). The cashout offer reuses the existing decimal-odds and market-margin pricing logic — no new settlement or wallet rules are introduced. WC2026 is the anchor event; soccer 2-way and 3-way Moneyline ship first. Spread and Totals are explicitly **out of scope for V1** because line-movement pricing is not yet validated.

**Expected outcome:**

- 📈 **Handle lift on enabled sports / leagues** — driven by retention of in-play wagering and reduced abandonment of long-tenor pre-match Bets _(baseline TBD — BDI to provide)_
- 🏆 **Competitive parity** — Cash Out is table stakes across BetMGM, FanDuel, DraftKings, Caesars, Bet365; absence is a documented gap in customer feedback
- 💰 **Net revenue from vig + admin fee retained on cashed-out Bets** — modelled per template, recognised in Synergy and N-Game reporting

---

## 2. Product Overview

### 2.1 The Problem

#### 🙋 Customer Problem

Punters with pending Bets — especially long-tenor Pre-Match and live in-play Bets — currently have no way to lock in a partial return before the event settles. They watch a winning position bleed back to neutral, or absorb the full loss on a losing position, with no exit. Customer feedback consistently flags Cash Out as the single most-requested missing feature, and Punters churning to competitors cite "couldn't cash out" as a top-3 reason. Outside Soccer 2-way / 3-way Moneyline at launch, the same friction persists for Spread / Totals — but those markets are explicitly deferred in V1 because the pricing problem (line moves between Bet placement and cashout request) has not yet been resolved.

#### 🏢 Business Problem

Three intersecting drivers:
1. **Competitive disadvantage.** Every major US sportsbook offers Cash Out. BetOnline / SB26 is one of the few brands without it. The gap is visible in customer-acquisition messaging from competitors and in retention churn.
2. **Margin leak via abandonment.** Long-tenor pre-match Bets (e.g. Bets placed 48–72 hours pre-event) carry abandonment risk — Punters reduce future deposits when they feel "stuck" with a bet. Cash Out converts that emotional state into a transaction, with vig + admin fee retained.
3. **Trading transparency.** Without per-bet cashout settlement, Trading has no real-time signal on which positions Punters consider losing, blunting hedging decisions on enabled markets.

### 2.2 Why This Problem?

#### 🔍 Evidence

- **Competitor scan** — BetMGM, FanDuel, DraftKings, Caesars, and Bet365 all offer Pre-Match + Live Cash Out across Moneyline, Spread, and Totals. Bet365 specifically markets it as a primary acquisition feature.
- **Customer signals** — Cash Out is the most-requested feature in support tickets and the betslip-feature feedback channel _(specific volume — CS to confirm)_.
- **Behavioural pattern** — long-tenor Pre-Match Bets show measurable engagement decay between placement and settlement. Cashing-out converts that decay window into transaction value.

#### 📊 Scale

| Dimension | Assessment |
|:---|:---|
| **Frequency** | Every active Pre-Match Punter session with a pending Bet on an enabled league / market sees the Cash Out icon. WC2026 alone projects significant Pre-Match Soccer volume across the 4-week tournament window. |
| **Severity** | High for retention. Moderate for direct revenue. Punters can technically place Bets without Cash Out, but the lack is a named churn driver. The reverse — having Cash Out — increases session length and re-bet frequency in competitor data. |

### 2.3 Audience

#### 🎯 Primary Audience

**Active BetOnline / SB26 web Punters with pending Bets on Settings-Manager-enabled sports / leagues / markets.** At V1 launch this is concretely: Punters with **Soccer 2-way and 3-way Moneyline** Bets, Pre-Match or Live, on leagues toggled On in the back office. WC2026 audience (international Soccer Punters, both VIP and Standard groups) is the framing cohort. This segment is already demonstrated to value Cash Out through competitor-product analytics and direct customer feedback.

#### 👥 Secondary Audience

| Stakeholder | Why They Care |
|:---|:---|
| **Trading / Risk** | Owns the vig rate table, admin fee values, sport / league enablement decisions, and the points-conversion table for any future Spread / Totals scope. Real-time liability visibility in Synergy and N-Game is required to hedge. |
| **Finance / Reporting** | Cashout amounts flow through ledger as a new transaction type ("Wager Cashed Out"). Reconciliation, NGR calculation, and reporting must include cashout credits and the original wager debit. |
| **Customer Support** | New ticket category — disputes about cashout amount, "why was it suspended", "I lost the offer". CS playbook needs to handle the validation flow (price changed, suspended, accept-changes toggle) and the new "Cashed Out" ticket status. |
| **Product Analytics / BDI** | Owns KPI baselines (cashout adoption, handle lift, NGR impact) and the dashboards that track them post-launch. |
| **VIP Relations** | Customer-Manager module lets specific groups, countries, or CSV-listed customers get differentiated cashout settings (different template, sport-level overrides). VIP-segment economics need agreed rules. |
| **Legal / Compliance** | New transaction type, new liability category, new automated decision affecting Punter funds. License-jurisdiction sign-off required before launch. |

---

## 3. Alignment with Business Goals & Company KPIs

### 3.1 Strategic Pillar

> 🎯 **Customer Retention & Competitive Parity** — close the largest documented product gap vs major US competitors and unlock retention engagement on long-tenor Pre-Match Bets.

### 3.2 Company KPIs Impacted

| KPI | Current Baseline | Target / Expected Impact |
|:---|:---|:---|
| **Cashout adoption rate** (% of eligible pending Bets cashed out) | _TBD — BDI to establish via competitor benchmark + first-30-day production data_ | _Target TBD — proposed: track-and-set after 30 days post-launch_ |
| **Pre-Match Soccer Moneyline handle from WC2026 cohort** | _TBD — BDI to provide WC qualifier baseline as proxy_ | **+5–10% lift** in WC2026 tournament window vs cohort baseline _(target needs Trading + BDI confirmation)_ |
| **Customer churn — "no cashout" cited reason** | _TBD — CS to provide last-12-month support-ticket tag rate_ | **Reduce attribution to <1% of churn citations** within 6 months of launch |
| **Net revenue per cashed-out Bet** (vig + admin fee retained vs original NGR projection) | Modelled in template | **Within ±10% of template-modelled NGR** — Trading + Finance to confirm post-launch reconciliation |
| **Cashout system reliability** (% of cashout requests resolved within 3 seconds end-to-end) | N/A — new system | **>95%** at WC2026 launch |

> **Rule applied:** every KPI names a precise metric (not "handle"), names a baseline or explicit "TBD — owner named", and names a target with a timeframe. Owners are flagged where unresolved.

---

## 4. Non-Financial Benefits

> Additional value that does not appear directly in financial metrics but is meaningful to the business or customer.

### 4.1 Additional Metrics

| Metric | Type | Trackable | Notes |
|:---|:---:|:---:|:---|
| Customer NPS shift on enabled-Bet experience | Customer | ✅ | Quarterly survey; segment cut by "used Cash Out at least once" |
| Session length on enabled live games | Customer | ✅ | In-play session duration for Punters with pending eligible Bet |
| Re-bet rate after a successful cashout | Customer | ✅ | % of Punters who place another Bet within 1 hour of cashing out |
| Trading hedging actions per market on enabled leagues | Business | ✅ | Volume of hedging trades correlated with cashout-driven liability shifts |
| Support-ticket category "Cashout - dispute / question" | Business | ✅ | New CS tag — track first 90 days for spike detection |

### 4.2 Measurement Approach

**📐 Baseline.** Baselines exist for NPS, session length, and re-bet rate at the platform level but **not** segmented by "Punter with eligible cashout Bet". BDI must extend the analytics pipeline to tag eligible Bets and segment metrics accordingly. _Owner: BDI — dependency confirmed before launch._

**📊 Dashboard.** No dashboard exists for Cash Out today. A new dashboard is required covering: cashout adoption rate, average cashout amount, vig captured per template, suspended-during-validation rate, accept-changes-toggle on/off ratio, and per-sport / per-league breakdowns. _Owner: BDI — dependency confirmed before launch._

### 4.3 Hypothesis

> 🔮 **Hypothesis:** If we ship Pre-Match + Live Cash Out for Soccer 2-way and 3-way Moneyline by WC2026 kick-off, then we expect adoption to reach **5–10% of eligible pending Bets** within the tournament window, resulting in **measurable Pre-Match Soccer handle lift** and a **reduction in "no cashout" citations** in the support / churn channels — without introducing additional liability risk because the offer formula reuses the existing market-margin pricing logic and the back-office template caps the maximum return.

**Targeted Areas**

- Customer betslip and My Bets surfaces (web)
- Trading workflow in Synergy (Pre-Match liability reports) and N-Game (Live liability reports)
- Back-office configuration workflow ("Sasha" — Settings, Templates, Customer Manager)
- CS ticket-handling playbook (new "Cashed Out" status, new transaction type)
- Analytics / BDI dashboards (new metrics, new segment cuts)

### 4.4 Cross-Department Review

| Department / Team | Reviewed? | Notes / Open Items |
|:---|:---:|:---|
| Product Operations | 🟡 Pending | Confirm V1 sport / league enablement list (Soccer leagues for WC2026; secondary leagues optional) |
| Business Data & Insights (BDI) | 🟡 Pending | Owns baselines for §3.2 KPIs; owns new Cash Out dashboard delivery |
| Engineering — Customer Site | 🟡 Pending | Widget states (8 in NBOL guide); My Bets / Bet History / Transactions integration |
| Engineering — Back Office | 🟡 Pending | Sasha tool — Settings / Templates / Customer Manager modules |
| Engineering — Trading Platforms | 🟡 Pending | Synergy + N-Game liability report wiring; routing rule (Pre→Synergy, Live→N-Game) |
| Trading / Risk | 🟡 Pending | Sign off on production vig rate table values; sign off on default admin fee (3–5% range); confirm sport / league enable list at launch |
| Finance / Reporting | 🟡 Pending | New transaction types: "Wager Cashed Out" (credit) and matching debit; reconciliation rules |
| Front-End / UX | 🟡 Pending | Widget visual states (8), Cash Out NEW badge cookie behaviour, mobile-web responsive |
| QA / Test | 🟡 Pending | Full state matrix: 8 widget states × 2 toggles (Show Confirmation, Accept Changes) × 2 modes (Pre, Live) |
| Customer Support | 🟡 Pending | New "Cashed Out" status playbook; dispute handling for price-changed-during-validation |
| Legal / Compliance | 🔴 Blocked | New automated decision affecting Punter funds; license-jurisdiction sign-off required |
| VIP Relations | 🟡 Pending | Customer Manager module rules — which VIP groups get differentiated templates |

> **Rule applied:** Trading, Risk, Finance, and CS are listed (PRINCIPLE 8). BDI is listed because new transaction and metric categories are introduced. Legal is listed because the feature creates a new automated decision affecting Punter funds.

### 4.5 Incremental Delivery / Phases

| Phase | Description / Deliverable | Timeline | Value Delivered |
|:---|:---|:---|:---|
| **Phase 1 — V1 Launch** | Pre-Match + Live Cash Out for **Soccer 2-way and 3-way Moneyline only**. Sasha back office (Settings, Templates, Customer Manager). Synergy + N-Game liability reports. NBOL-guide-compliant widget with all 8 states. | **WC2026 kick-off** — _exact date TBD by Trading + Eng joint readiness_ | WC2026 retention & competitive parity on the highest-volume launch event |
| **Phase 2 — Market Expansion** | Add Moneyline support for NBA, NFL, MLB, NHL leagues (existing 2-way Moneyline pricing logic reuses Phase 1 system). | **Post-WC2026, Q3 2026** | Coverage across all major US sports for Moneyline |
| **Phase 3 — Spread / Totals (conditional)** | Resolve original-line-price problem (per [cashout-spread-totals](https://github.com/KikePinnaple90/cash-out/blob/main/cash%20out%20skills/cashout-spread-totals/SKILL.md) decision matrix — preferred: odds-feed alt-line pricing; fallback: suspend-on-line-move). | **Q4 2026 — Q1 2027** | Full market parity with competitors |

> Phase 3 is explicitly conditional on Trading sign-off of the points-conversion approach OR confirmation that the odds feed (OpticOdds / current provider) supports alt-line pricing.

---

## 5. Use Case Scope

### 5.1 In Scope

> Scenarios and workflows that **ARE** covered by V1 launch.

---

#### 🎬 Use Case 1: Cash Out a Winning Pre-Match Soccer Moneyline Bet Before Kick-Off

| | |
|:---|:---|
| **👤 Actor** | Punter on BetOnline / SB26 web with a Pre-Match Soccer Moneyline Bet placed on a league enabled in the Settings Manager |
| **🎯 Goal** | Lock in a partial return before the game starts because the line has moved in their favour |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter places $100 on **Argentina @ +105** Pre-Match (Settings Manager has Argentina-league enabled, Pre toggle On, template = "World Cup 2026").
2. Two days before kick-off, Argentina's price moves to **-130** (decimal 1.769) — Punter's position is now winning.
3. Punter opens the BetOnline web sportsbook → My Bets sidebar → **Cash Out** tab. The widget shows the bet card with the Cash Out icon, current Risk / To Win, and a live-updating "Cash Out $XX.XX" button.
4. Punter clicks the button. With "Show Confirmation" ON, the button switches to "Confirm $XX.XX ▶" highlighted in light green for 5 seconds.
5. Punter clicks again to confirm. Validation runs against the current market price.
6. Price has not changed during validation → cashout proceeds. Success message shown ("✓ Cashed Out · $XX.XX returned to balance"). After 5 seconds the bet card disappears from the widget.
7. The Punter sees the bet under My Bets → Graded with status "Cashed Out", in Bet History with To Return = $XX.XX, and in Transactions as a "Cashed Out" credit + the original "Wager (Debit)" line.
8. Trading sees the liability change in **Synergy** (Pre-Match-placed Bet → Synergy report): Profit, Payout, and Volume update on both the Argentina and the opponent selection.

</details>

---

#### 🎬 Use Case 2: Cash Out a Live 3-Way Soccer Bet When Price Changes UP During Validation

| | |
|:---|:---|
| **👤 Actor** | Punter with a Live-placed 3-way Bet on a Soccer match (Home/Draw/Away) — placed in-play, currently in-play |
| **🎯 Goal** | Cash out a live bet and have the system handle a favourable price move automatically |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter has a live Bet of $50 on **Brazil to win** (placed in-play). The Cash Out widget shows "Cash Out $42.30".
2. Punter clicks Cash Out. Validation begins. With "Show Confirmation" OFF, the validation starts immediately on first click.
3. **During validation, Brazil scores** — the price moves up. Cashout offer recalculates to $48.20.
4. Widget displays "▲ The Cash Out return has changed" message in green. The new amount $48.20 is highlighted in light-green pill style.
5. Because price moved **UP** (favourable), cashout proceeds automatically at the new higher amount regardless of the "Accept Cash Out Changes" toggle setting.
6. Success: "✓ Cashed Out · $48.20 returned to balance". Message clears after 3 seconds, line disappears from widget after 5 seconds.
7. Trading sees the liability change in **N-Game** (Live-placed Bet → N-Game report): Liability updates with the formula `ROUND(Σ Cashout Amounts + Σ Returns of remaining bets − (Stake A + Stake B + Stake C), 0)`, applied identically to all three selections.

</details>

---

#### 🎬 Use Case 3: Cash Out Interrupted Because Price Moved DOWN and "Accept Changes" is OFF

| | |
|:---|:---|
| **👤 Actor** | Punter with a pending Bet on an enabled market — has "Accept Cash Out Changes" toggle OFF |
| **🎯 Goal** | Be offered a chance to confirm or reject the new lower amount when the market moves against them mid-validation |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter clicks "Cash Out $80" on a pending Bet. Validation begins.
2. During validation, market price moves against the Punter — new offer is $75.
3. Widget shows "▼ The Cash Out return has changed" in amber. The new amount $75 is highlighted in amber pill style.
4. Because "Accept Cash Out Changes" toggle is **OFF**, the cashout flow is **interrupted**. The bet is NOT cashed out. The button now reads "Cash Out $75" — Punter must click again if they want to proceed at the lower amount.
5. The original $80 offer is gone — the Punter cannot recover it. Their next click will start a fresh validation against whatever the price is at that moment.
6. If Punter does not act within the live-update window, the offer continues to update with the market.

</details>

---

#### 🎬 Use Case 4: Cash Out Suspended During Validation — Market Goes Off-Price Mid-Flow

| | |
|:---|:---|
| **👤 Actor** | Any Punter clicking Cash Out on a Live Bet |
| **🎯 Goal** | Receive a clear signal that the cashout could not complete because the market suspended, and not be debited or credited |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter clicks "Cash Out $XX.XX". Validation begins.
2. Mid-validation the live market suspends (e.g., a goal is being VAR-reviewed, or the trader pulls the price).
3. Widget shows "The Cash Out was suspended" message for 3 seconds.
4. Button reverts to the standard Suspended state (lock icon + "Suspended" label).
5. Bet remains pending. No funds movement. The Punter sees no change in My Bets / Bet History / Transactions.
6. When the market reopens, the cashout offer reappears in the widget at the new market price.

</details>

---

#### 🎬 Use Case 5: Trading Operator Configures a New Cash Out Template for a New Soccer League

| | |
|:---|:---|
| **👤 Actor** | Trading operator with access to "Sasha" back office |
| **🎯 Goal** | Create a template with a specific admin fee and vig table, then assign it to a league via the Settings Manager |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Operator opens **Sasha → Templates Manager** and clicks "+ New".
2. Names the template "MLS 2026", sets **Admin fee = 4%**, enables **Max cashout cap** at 90% return, sets **Min wager = $5**.
3. Edits the **vig rate table** — 25 default rows from -95% to +95%, with vig values approved by Trading. Adjusts row "Winning 30%–74%" to vig = 12% (instead of default 10%) for tighter pricing on this league.
4. Saves. The template appears in the active list and is now selectable in the Settings Manager and Calculator.
5. Operator switches to **Settings Manager**, selects Soccer → MLS league row, clicks Edit, sets Live = On, Pre = On, Template dropdown = "MLS 2026", clicks Save.
6. Within minutes, the customer-site widget begins offering Cash Out on MLS Bets using the new template's pricing. The Calculator tab uses this template to validate offer amounts ad-hoc.

</details>

---

#### 🎬 Use Case 6: Pre-Match Bet Cashed Out During Live Game — Liability Routes to Synergy

| | |
|:---|:---|
| **👤 Actor** | Trading operator monitoring liabilities on Synergy during a live event |
| **🎯 Goal** | See real-time liability adjustments from Pre-Match-placed Bets that are cashed out mid-match, without confusion about which platform owns the data |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter A places $100 on Patriots -150 **Pre-Match** (decimal 1.667). Synergy shows: Patriots Profit -66, Risk 100, Payout 166, Volume 66, Count 1.
2. Game kicks off. Patriots are leading at half-time. Punter A opens the Cash Out widget and cashes out the Bet for **$84**.
3. **Routing rule applies:** because the Bet was placed Pre-Match, the liability update routes to **Synergy** (NOT N-Game) regardless of the fact that the cashout happens in-play.
4. Synergy report updates: Patriots Profit = +16, Payout = 84 (replaces 166), Volume = -16. The opposite selection (Eagles) Profit also updates per the formula `Total Profit = OtherSide_Total_Risk + ThisSide_Total_Payout + Σ(Profit of cashed-out bets on opposite side)`.
5. Wager Coverage column in Synergy now shows the Cash Out icon next to this row. Status in Wager Details popup = "Cashed Out". Paid = $84. Lost / Won = empty. Transactions row shows Description = "Wager Cashed Out", Credit = $84.
6. Trading uses the updated Synergy view to recalibrate hedging on the live market with no manual reconciliation required.

</details>

---

### 5.2 Out of Scope

> What is explicitly **NOT** included in V1 launch.

- ❌ **Spread and Totals markets** — pricing problem (original-line-price unknown when main line moves) is unresolved. Three options exist (suspend on line move, points-conversion, odds-feed alt-line) — none has Trading sign-off. See [cashout-spread-totals](https://github.com/KikePinnaple90/cash-out/blob/main/cash%20out%20skills/cashout-spread-totals/SKILL.md) skill.
- ❌ **Parlay / Teaser / If-Bet cash out** — single Bet only in V1. Parlay cashout requires per-leg pricing logic and a different vig model.
- ❌ **Mobile native app** — V1 is web-only (BetOnline / SB26 desktop + mobile-web). Native iOS/Android app integration is a Phase-2+ scope.
- ❌ **Retail / cashier / kiosk surfaces** — no in-store cashout in V1.
- ❌ **Partial cashout** (cashing out only part of a Bet) — full cashout only in V1.
- ❌ **Auto-cashout at preset thresholds** ("cash out automatically when offer reaches $X") — not in V1.
- ❌ **Alt-line manual pricing for Spread / Totals** — even if the odds feed exposes alt-line prices, V1 does not query them; this is V3 scope.
- ❌ **Cashout on settled / void Bets** — eligibility ends the moment the Bet grades by normal settlement. Void handling continues per the existing void/refund flow.

> **Rule applied:** Out-of-scope items here are scenario-level. Feature-level exclusions (e.g., "no auto-cashout engine") are also listed in §6 Won't Have to align expectations on capability.

---

## 6. Product Feature Scope (MoSCoW)

> 📚 For more on MoSCoW prioritization, see [productplan.com/glossary/moscow-prioritization](https://www.productplan.com/glossary/moscow-prioritization).

### 🔴 Must Have — Non-negotiable. The initiative fails without these.

| # | Feature / Capability | Rationale | Owner |
|:--:|:---|:---|:---|
| 1 | Cashout offer formula — Pre-Match (no line move): `Cashout = Wager × (1 − AdminFee)` | Foundation of all pre-event cashout pricing; trivially calculable, must be exactly the template's admin fee | Trading + Eng (Customer Site) |
| 2 | Cashout offer formula — Pre-Match (line moved) + Live: `Cashout = (InitialOdd / TruePropOdd) × Wager × (1 − VigRate)` with vig looked up from the template's vig table by `% Bet Change Win/Lose` | Core pricing logic; reuses existing market-margin model; without this no live cashout works | Trading + Eng (Customer Site) |
| 3 | 2-way and 3-way market support (Moneyline) including 3-way Soccer (Home/Draw/Away) | WC2026 anchor event is Soccer 3-way; without 3-way support there is no V1 launch | Eng (Customer Site) + UX |
| 4 | "Sasha" back office — **Settings Manager** module: brand / market / sport / league hierarchy, Live / Pre toggles, template assignment per league, edit-mode gating | Trading needs a single surface to enable / disable cashout per league and assign templates | Eng (Back Office) + Trading Ops |
| 5 | "Sasha" back office — **Templates Manager** module: name, admin fee, max cashout cap (with toggle + return %), min wager, editable 25-row vig rate table, Save / Clone / Delete | Different sports / events need different vig profiles; without templates Trading cannot calibrate pricing per market | Eng (Back Office) + Trading |
| 6 | "Sasha" back office — **Customer Manager** module: brand + group/country/CSV + market filter, cashout enable toggle, per-group template, advanced sport-level overrides | VIP and risk-flagged groups need differentiated rules; commercial requirement | Eng (Back Office) + VIP Relations |
| 7 | Customer site — **Cash Out widget** with all 8 states from NBOL guide (Empty, Available, Suspended, Confirm, Changed Up, Changed Down, Suspended-during-validation, Cashed) | Each state is named explicitly in the functional guide; QA matrix depends on all 8 | Eng (Customer Site) + UX + QA |
| 8 | Customer site — **Show Confirmation** toggle (5-second confirm window with light-green amount pill) | Customer-protection feature in the NBOL guide; reduces accidental cashouts | Eng (Customer Site) + UX |
| 9 | Customer site — **Accept Cash Out Changes** toggle (controls behaviour when price moves DOWN mid-validation) | Customer-control feature in the NBOL guide; without it the change-down flow has no path to completion | Eng (Customer Site) + UX |
| 10 | Customer site — Cash Out icon in Offering, Betslip, My Bets pending, with NEW badge gone-after-first-visit cookie behaviour | Discoverability — Punters need to see where the feature applies | Eng (Customer Site) + UX |
| 11 | Customer site — My Bets new "Cashed Out" status; Bet History new "To Return" column with status filter; Transactions new type "Cashed Out" with description "Wager Cashed Out" | Post-cashout visibility — Punter must see what was returned and where; Finance reconciliation needs the transaction type | Eng (Customer Site) + Finance |
| 12 | **Synergy liability report** updates for Pre-Match-placed cashed-out Bets — Profit, Payout, Volume update on both selections; Risk and Count unchanged. Wager Coverage shows Cash Out icon. Wager Details Status = "Cashed Out", Paid = cashout amount. Trading Settings Mgmt → Performance/Wagers and Transactions reflect the new state. | Trading cannot hedge without real-time liability visibility; Pre-Match-placed Bets route to Synergy regardless of cashout timing | Eng (Trading Platforms) + Trading |
| 13 | **N-Game liability report** updates for Live-placed cashed-out Bets — Liability updates per `ROUND(Σ CO_amounts + Σ Returns_of_remaining_bets − Σ All_Stakes, 0)`; same value applies to all selections. Stake unchanged. | Trading cannot hedge live without N-Game visibility; Live-placed Bets route to N-Game | Eng (Trading Platforms) + Trading |
| 14 | **Routing rule**: Bet placed Pre-Match → Synergy (regardless of when cashed out). Bet placed Live → N-Game (regardless of when cashed out). | Single, deterministic ownership of liability data per Bet; prevents double-counting | Eng (Trading Platforms) |
| 15 | Cashout button **suspended** when no price for the market | Without a market price, the formula has no `New Odd` and no `True Probability New Odd` — must not offer | Eng (Customer Site) |
| 16 | New transaction type in Finance ledger: "Wager Cashed Out" credit + matching wager-debit reference | Finance reconciliation, NGR calculation, license-jurisdiction reporting | Finance + Eng (Trading Platforms) |
| 17 | New analytics events: cashout-offered, cashout-clicked, cashout-confirmed, cashout-suspended, cashout-completed, accept-changes-toggle, show-confirmation-toggle | Without these, none of §3.2 KPIs can be measured | BDI + Eng (Customer Site) |

### 🟠 Should Have — Important but not vital.

| Feature / Capability | Rationale | Owner |
|:---|:---|:---|
| Customer Manager — CSV upload for customer-ID lists | Faster operationally than NGame/Synergy group sync for ad-hoc segments | Eng (Back Office) + Trading Ops |
| Calculator tab in Sasha — ad-hoc cashout offer validation against any template | Trading verification tool; reduces "is this the right number?" tickets | Eng (Back Office) + Trading |
| Live-update widget without manual refresh (websocket / SSE-driven price updates) | Improves customer experience; reduces stale-offer abandonment | Eng (Customer Site) |
| Validation flow telemetry — % suspended-during-validation, % accept-changes-on, % accept-changes-off | Feeds back into vig table calibration | BDI |

### 🟡 Could Have — Nice to have.

| Feature / Capability | Rationale | Owner |
|:---|:---|:---|
| Spanish-localised widget messages ("La opción de Cash Out se ha suspendido", etc.) | Hispanic market is a documented BetOnline cohort | UX + Localization |
| Print Bet Slip with Cashed-Out status | Some Punters request hardcopy ticket records via CS | Eng (Customer Site) |
| Customer-facing analytics ("you've cashed out N times this month, saved $X") | Engagement gamification; defer to V2 if WC2026 timeline is tight | Product + Eng |

### ⚫ Won't Have — Explicitly excluded in V1.

| Feature / Capability | Rationale |
|:---|:---|
| Spread and Totals cashout | Original-line-price problem unresolved; deferred to Phase 3 conditional on Trading sign-off |
| Parlay / Teaser / If-Bet cashout | Multi-leg pricing logic + per-leg vig is a separate model; V2 |
| Auto-cashout at preset thresholds | No customer-controlled rules engine in V1 |
| Partial cashout | Full-only in V1 |
| Mobile native app cashout | Web-only at launch |
| Retail / kiosk cashout | Not in scope for digital-first V1 |
| Cashout on settled or voided Bets | Existing void/refund flow remains canonical |

### 6.1 Assumptions & Constraints

<details open>
<summary><strong>✅ Assumptions</strong></summary>

- The existing decimal-odds model and market-margin (Total Implied) calculation are the canonical pricing inputs and are NOT being redesigned by this initiative.
- The same wallet / balance / settlement engine handles cashout credits via the new "Wager Cashed Out" transaction type — no new wallet rules required.
- WC2026 schedule and league enablement list will be available in time for back-office configuration ahead of kick-off.
- Trading will provide validated production values for the default vig rate table and admin fee per template before launch.
- The OpticOdds (or current) odds feed delivers live decimal odds for all enabled Soccer leagues at the granularity required by the formula.
- The "Sasha" back-office tool replaces no existing critical workflow at launch — Trading currently has no cashout config to migrate from.
- Punters cashing out a Pre-Match Bet during a live game is rare enough that real-time routing to Synergy (not N-Game) does not introduce performance issues.

</details>

<details open>
<summary><strong>⚠️ Constraints</strong></summary>

- Must not break or weaken existing settlement logic, void handling, or wallet integrity.
- Must not introduce new regulatory categories without Legal sign-off in each license jurisdiction.
- Must operate within the existing odds-feed cadence — no requirement for a faster feed at launch.
- N-Game post-cashout liability template is **manually maintained** in the xlsx today; if cashout volume scales, an automation path is required (named in §6.2 Open Items).
- The 4 known formula-bugs in the reference xlsx (T22, T23 anomalies in Synergy multi-bet template, missing $ anchors in NGame 1-bet sheet) must NOT be replicated in production.

</details>

### 6.2 Unknowns & Open Items

| Open Item / Unknown | Owner | Due Date | Status |
|:---|:---|:---:|:---:|
| Confirm V1 sport / league enablement list (which Soccer leagues for WC2026; which secondary leagues if any) | Product Operations | TBD — before code freeze | 🔴 Open |
| Validate production vig rate table values and default admin fee per template | Trading | TBD — before code freeze | 🔴 Open |
| Decide N-Game post-cashout liability automation path (extend xlsx or build into N-Game UI) | Eng (Trading Platforms) + Trading | TBD — before launch | 🔴 Open |
| Confirm OpticOdds (or current feed) supports live decimal odds for all enabled Soccer leagues at required cadence | Eng (Trading Platforms) + Vendor Mgmt | TBD — before code freeze | 🔴 Open |
| Confirm Legal / Compliance sign-off path per license jurisdiction (US states, international) | Legal | TBD — before launch | 🔴 Open |
| Confirm Customer Support playbook handover — new "Cashed Out" status, dispute handling, change-down flow | CS Manager | TBD — 4 weeks before launch | 🟡 In Progress |
| Confirm BDI baselines and dashboard delivery timeline for §3.2 KPIs | BDI | TBD — before launch | 🟡 In Progress |
| Confirm whether Phase 2 NBA / NFL / MLB Moneyline expansion is gated on a separate Trading approval cycle or rides V1 release | Product | TBD — Q3 2026 planning | 🔴 Open |
| Confirm "Show Confirmation" and "Accept Changes" toggle defaults at first-time-user state | UX + Product | TBD — UX review | 🔴 Open |

> **Rule applied:** every open item is a specific answerable question with a named owner — no generic "TBD".

### 6.3 Active Programs / Potential Combines

| Program / Initiative | Overlap / Relationship | Recommendation |
|:---|:---|:---|
| **WC2026 Launch Readiness Program** | Cash Out is a named V1 deliverable for WC2026 | **Combine** — Cash Out V1 timeline is gated by WC2026 kick-off |
| **SB26 / FoshTech Site v2 redesign** _(if in flight)_ | Cash Out widget visual states must match new design system | **Coordinate** — UX joint review required; visual styling owned by Site v2 design system |
| **OpticOdds feed migration / upgrade** _(if in flight)_ | Cashout requires live decimal odds at speed; feed reliability is a critical dependency | **Monitor for conflict** — feed regression is a launch blocker |
| **Trading Tools modernisation (Synergy / N-Game upgrades)** _(if in flight)_ | Liability reports change in this initiative; if Trading Tools are also being updated, sequencing matters | **Coordinate** — joint planning to avoid double-rework |
| **Wallet / Payment Reconciliation modernisation** _(if in flight)_ | New "Wager Cashed Out" transaction type lands in the same ledger | **Coordinate** — Finance to confirm no overlap |
| **Mobile native app rollout** | Cash Out V1 is web-only; Mobile is explicit Phase-2 scope | **Do NOT combine** — mobile parity is a separate program |

If any of the above are not currently active programs, this section should be updated before the Investment Template.

---

## 7. Use Case Diagrams

> Outline user experience flows and workflows. Insert diagrams, flowcharts, or wireframe references below.

### 🎨 Diagram 1: Cash Out Widget — Validation Flow

> 🔗 _Working interactive HTML reference: [cashout_suite_v2_5.html](https://github.com/KikePinnaple90/cash-out/blob/main/cashout_suite_v2_5.html) → Customer site → Widget tab. Final Figma link **TBD**._

**Description.** Diagrams the end-to-end validation flow for a cashout request. From the moment Punter clicks the Cash Out button, through the validation window, to the four resolution branches: (a) no price change → cashout proceeds at offered amount; (b) price up → cashout proceeds at new higher amount automatically; (c) price down + Accept Changes ON → cashout proceeds at new lower amount; (d) price down + Accept Changes OFF → flow interrupted, new offer surfaced for re-confirmation. The decision flowchart is mirrored in the v2.5 HTML prototype's Widget tab.

---

### 🎨 Diagram 2: Back-Office Configuration Hierarchy (Sasha)

> 🔗 _Working interactive HTML reference: [cashout_suite_v2_5.html](https://github.com/KikePinnaple90/cash-out/blob/main/cashout_suite_v2_5.html) → Management tab. Final Figma link **TBD**._

**Description.** Shows the data hierarchy `Brand → Market → Sport → League → Template → (Admin fee, Cap, Vig table)` plus the Customer Manager override layer `Brand + Market + Customer Group → Optional sport-level overrides`. Illustrates the three modules (Settings, Templates, Customer) and how a single template change propagates to every league assigned to it, and how the Calculator tab uses the active template for ad-hoc validation.

---

### 🎨 Diagram 3: Liability Routing — Synergy vs N-Game

> 🔗 _Working interactive HTML reference: [cashout_suite_v2_5.html](https://github.com/KikePinnaple90/cash-out/blob/main/cashout_suite_v2_5.html) → Reports tab → Routing rules. Final Figma link **TBD**._

**Description.** Single decision diagram illustrating the routing rule and the resulting field updates per platform:

- **(a)** Bet placed Pre-Match → liability flows to **Synergy** regardless of cashout timing. Updates Profit, Payout, Volume on both selections; Risk and Count unchanged.
- **(b)** Bet placed Live → liability flows to **N-Game**. Updates Liability identically across all selections via the post-cashout formula. Stake unchanged.
- **(c)** Wager Coverage and Wager Details visual changes in Synergy — Cash Out icon column, Status = "Cashed Out", Paid = cashout amount, Lost / Won = empty.

---

## 8. Roadmap Alignment

| | |
|:---|:---|
| **🏁 Roadmap / Swim Lane** | Customer Experience & Trading Operations |
| **🎯 Roadmap Theme** | Competitive parity & retention engagement |
| **⏰ Timeframe** | **Near-term (V1)** + **Mid-term (Phase 2 expansion)** + **Long-term conditional (Phase 3 Spread/Totals)** — V1 anchored to WC2026 kick-off; Phase 2 follows in Q3 2026; Phase 3 is conditional on Trading sign-off of Spread/Totals approach. |

### Delivery Milestones

| Roadmap Year | Initiative / Milestone | Dependency | Status |
|:---|:---|:---|:---|
| **2026 — Near-term** | **Phase 1:** Cash Out V1 — Pre-Match + Live, 2-way + 3-way Moneyline (Soccer focus, WC2026 anchor) | Trading sign-off on vig table + admin fee; OpticOdds live decimal feed for enabled leagues; Sasha back office Eng delivery; Synergy + N-Game integration | 🟡 Drafting Scope |
| **2026 — Mid-term** | **Phase 2:** Market expansion — NBA, NFL, MLB, NHL Moneyline | Phase 1 stable; Trading capacity to validate per-sport vig profiles | 🔴 Not started |
| **2026 / 2027 — Long-term** | **Phase 3 (conditional):** Spread + Totals cashout | Trading sign-off on points-conversion table OR confirmation of odds-feed alt-line pricing | 🔴 Not started |

---

> 📝 _This document is a living template. Update version and status as the initiative progresses through review and approval._
