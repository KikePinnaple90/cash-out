# 📋 Scope Document — Sportsbook: Cash Out V1 Launch

![Version](https://img.shields.io/badge/version-v0.2-blue) ![Status](https://img.shields.io/badge/status-Draft-orange) ![Date](https://img.shields.io/badge/date-2026--04--27-lightgrey) ![Module](https://img.shields.io/badge/module-Sportsbook%20Core-informational)

| Program Owner | Date | Version | Status |
|:---|:---|:---|:---|
| _To be assigned_ | 27 Apr 2026 | v0.2 | Draft |

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

> 💡 **In a nutshell:** Cash Out lets Punters settle pending Bets early — at any point between Bet placement and event settlement — at a price calculated from current market odds. The feature is platform-agnostic and ships across three surfaces: a customer-facing widget, a back-office configuration tool, and unified liability reporting for Trading.

The initiative delivers Cash Out as a complete feature: a customer-facing widget on the sportsbook web site, a back-office configuration tool (templates, sport / league enablement, customer-group rules), and unified trading liability reporting that updates in real time on every cashed-out Bet. The cashout offer reuses the existing decimal-odds and market-margin pricing logic — no new settlement or wallet rules are introduced. The same configuration, the same formula, and the same reporting flow apply to every Bet regardless of when it was placed or when it is cashed out. **Moneyline 2-way and 3-way markets ship in V1.** Spread and Totals are explicitly **out of scope for V1** because the original-line-price problem is not yet resolved.

**Expected outcome:**

- 📈 **Handle lift on enabled sports / leagues** — driven by retention of pending wagers and reduced abandonment _(baseline TBD — Analytics to provide)_
- 🏆 **Competitive parity** — Cash Out is table stakes across major sportsbooks; absence is a documented gap in customer feedback and churn signals
- 💰 **Net revenue from vig + admin fee retained on cashed-out Bets** — modelled per template, recognised in liability reporting

---

## 2. Product Overview

### 2.1 The Problem

#### 🙋 Customer Problem

Punters with pending Bets currently have no way to lock in a partial return before the event settles. They watch a winning position drift back to neutral, or absorb the full loss on a losing position, with no exit. Customer feedback consistently flags Cash Out as the single most-requested missing feature, and Punters churning to competitors cite "couldn't cash out" as a top-3 reason. The friction applies equally to Bets placed long before the event and Bets placed during the event — Punters expect early-settlement to be available throughout the entire window between placement and grading.

#### 🏢 Business Problem

Three intersecting drivers:
1. **Competitive disadvantage.** Every major sportsbook offers Cash Out. Absence is visible in competitor acquisition messaging and in retention churn data.
2. **Margin leak via abandonment.** Long-tenor pending Bets carry abandonment risk — Punters reduce future deposits when they feel "stuck". Cash Out converts that emotional state into a transaction with vig + admin fee retained.
3. **Trading transparency.** Without per-bet cashout settlement, Trading has no real-time signal on which positions Punters consider losing, blunting hedging decisions on enabled markets.

### 2.2 Why This Problem?

#### 🔍 Evidence

- **Competitive scan** — every major regulated sportsbook offers Cash Out across Moneyline, Spread, and Totals. Several market it as a primary acquisition feature.
- **Customer signals** — Cash Out is the most-requested feature in support tickets and the betslip-feature feedback channel _(specific volume — Customer Support to confirm)_.
- **Behavioural pattern** — pending Bets show measurable engagement decay between placement and settlement. Cashing-out converts that decay window into transaction value.

#### 📊 Scale

| Dimension | Assessment |
|:---|:---|
| **Frequency** | Every active Punter session with a pending Bet on an enabled league / market sees the Cash Out icon. |
| **Severity** | High for retention. Moderate for direct revenue. Punters can technically place Bets without Cash Out, but the lack is a named churn driver. The reverse — having Cash Out — measurably increases session length and re-bet frequency in competitor data. |

### 2.3 Audience

#### 🎯 Primary Audience

**Active web-sportsbook Punters with pending Bets on sports / leagues / markets enabled in the back office.** At V1 launch this is concretely: Punters with **Moneyline 2-way or 3-way** Bets on enabled leagues. The launch tournament is the framing cohort. This segment is already demonstrated to value Cash Out through competitor-product analytics and direct customer feedback.

#### 👥 Secondary Audience

| Stakeholder | Why They Care |
|:---|:---|
| **Trading / Risk** | Owns the vig rate table, admin fee values, sport / league enablement decisions, and the future Spread / Totals approach. Real-time liability visibility is required to hedge. |
| **Finance / Reporting** | Cashout amounts flow through the ledger as a new transaction type ("Wager Cashed Out"). Reconciliation, NGR calculation, and reporting must include cashout credits and the original wager debit. |
| **Customer Support** | New ticket category — disputes about cashout amount, "why was it suspended", "I lost the offer". CS playbook needs to handle the validation flow (price changed, suspended, accept-changes toggle) and the new "Cashed Out" Bet status. |
| **Product Analytics** | Owns KPI baselines (cashout adoption, handle lift, NGR impact) and the dashboards that track them post-launch. |
| **VIP Relations** | Customer Manager rules let specific groups, countries, or list-uploaded customers receive differentiated cashout settings. VIP-segment economics need agreed rules. |
| **Legal / Compliance** | New transaction type, new liability category, new automated decision affecting Punter funds. License-jurisdiction sign-off required before launch. |

---

## 3. Alignment with Business Goals & Company KPIs

### 3.1 Strategic Pillar

> 🎯 **Customer Retention & Competitive Parity** — close the largest documented product gap vs major competitors and unlock retention engagement on long-tenor pending Bets.

### 3.2 Company KPIs Impacted

| KPI | Current Baseline | Target / Expected Impact |
|:---|:---|:---|
| **Cashout adoption rate** (% of eligible pending Bets cashed out) | _TBD — Analytics to establish via competitor benchmark + first-30-day production data_ | _Target TBD — proposed: track-and-set after 30 days post-launch_ |
| **Moneyline handle on enabled leagues** | _TBD — Analytics to provide pre-launch baseline_ | **+5–10% lift** in launch tournament window vs cohort baseline _(target needs Trading + Analytics confirmation)_ |
| **Customer churn — "no cashout" cited reason** | _TBD — CS to provide last-12-month support-ticket tag rate_ | **Reduce attribution to <1% of churn citations** within 6 months of launch |
| **Net revenue per cashed-out Bet** (vig + admin fee retained vs original NGR projection) | Modelled in template | **Within ±10% of template-modelled NGR** — Trading + Finance to confirm post-launch reconciliation |
| **Cashout system reliability** (% of cashout requests resolved within 3 seconds end-to-end) | N/A — new system | **>95%** at launch |

> **Rule applied:** every KPI names a precise metric, names a baseline or explicit "TBD — owner named", and names a target with a timeframe.

---

## 4. Non-Financial Benefits

> Additional value that does not appear directly in financial metrics but is meaningful to the business or customer.

### 4.1 Additional Metrics

| Metric | Type | Trackable | Notes |
|:---|:---:|:---:|:---|
| Customer NPS shift on enabled-Bet experience | Customer | ✅ | Quarterly survey; segment cut by "used Cash Out at least once" |
| Session length on enabled events | Customer | ✅ | Session duration for Punters with a pending eligible Bet |
| Re-bet rate after a successful cashout | Customer | ✅ | % of Punters who place another Bet within 1 hour of cashing out |
| Trading hedging actions per market on enabled leagues | Business | ✅ | Volume of hedging trades correlated with cashout-driven liability shifts |
| Support-ticket category "Cashout — dispute / question" | Business | ✅ | New CS tag — track first 90 days for spike detection |

### 4.2 Measurement Approach

**📐 Baseline.** Baselines exist for NPS, session length, and re-bet rate at the platform level but **not** segmented by "Punter with eligible cashout Bet". Analytics must extend the data pipeline to tag eligible Bets and segment metrics accordingly. _Owner: Analytics — dependency confirmed before launch._

**📊 Dashboard.** No dashboard exists for Cash Out today. A new dashboard is required covering: cashout adoption rate, average cashout amount, vig captured per template, suspended-during-validation rate, accept-changes-toggle on/off ratio, and per-sport / per-league breakdowns. _Owner: Analytics — dependency confirmed before launch._

### 4.3 Hypothesis

> 🔮 **Hypothesis:** If we ship Cash Out for Moneyline 2-way and 3-way markets by the launch tournament, then we expect adoption to reach **5–10% of eligible pending Bets** within the launch window, resulting in **measurable handle lift on enabled leagues** and a **reduction in "no cashout" citations** in the support / churn channels — without introducing additional liability risk because the offer formula reuses the existing market-margin pricing logic and the back-office template caps the maximum return.

**Targeted Areas**

- Customer betslip and My Bets surfaces
- Trading workflow — unified liability reporting for cashed-out Bets
- Back-office configuration workflow (Settings, Templates, Customer Manager modules)
- Customer Support ticket-handling playbook (new "Cashed Out" status, new transaction type)
- Analytics dashboards (new metrics, new segment cuts)

### 4.4 Cross-Department Review

| Department / Team | Reviewed? | Notes / Open Items |
|:---|:---:|:---|
| Product Operations | 🟡 Pending | Confirm V1 sport / league enablement list |
| Analytics / Data | 🟡 Pending | Owns baselines for §3.2 KPIs; owns new Cash Out dashboard delivery |
| Engineering — Customer Site | 🟡 Pending | Widget states; My Bets / Bet History / Transactions integration |
| Engineering — Back Office | 🟡 Pending | Settings / Templates / Customer Manager modules |
| Engineering — Trading Tools | 🟡 Pending | Liability reporting wiring |
| Trading / Risk | 🟡 Pending | Sign off on production vig rate table values; default admin fee; sport / league enable list |
| Finance / Reporting | 🟡 Pending | New transaction types ("Wager Cashed Out" credit + matching debit); reconciliation rules |
| Front-End / UX | 🟡 Pending | Widget visual states, NEW badge cookie behaviour, mobile-web responsive |
| QA / Test | 🟡 Pending | Full state matrix: every widget state × Show Confirmation × Accept Changes × every market type |
| Customer Support | 🟡 Pending | New "Cashed Out" status playbook; dispute handling for price-changed-during-validation |
| Legal / Compliance | 🔴 Blocked | New automated decision affecting Punter funds; license-jurisdiction sign-off required |
| VIP Relations | 🟡 Pending | Customer Manager rules — which groups get differentiated templates |

> **Rule applied:** Trading, Risk, Finance, and CS are listed (PRINCIPLE 8). Analytics is listed because new transaction and metric categories are introduced. Legal is listed because the feature creates a new automated decision affecting Punter funds.

### 4.5 Incremental Delivery / Phases

| Phase | Description / Deliverable | Timeline | Value Delivered |
|:---|:---|:---|:---|
| **Phase 1 — V1 Launch** | Cash Out for **Moneyline 2-way and 3-way only** across enabled leagues. Back-office configuration tool (Settings, Templates, Customer Manager). Unified liability reporting. Full widget state coverage. | **Launch tournament kick-off** _(exact date TBD by Trading + Eng joint readiness)_ | Retention & competitive parity on the highest-volume launch event |
| **Phase 2 — Market Expansion** | Add Moneyline support across all priority leagues that were not enabled in Phase 1. The same pricing logic and reporting reuses Phase 1. | **Post-launch tournament** | Coverage across all major sports for Moneyline |
| **Phase 3 — Spread / Totals (conditional)** | Resolve original-line-price problem (preferred path: real-time alt-line pricing from the odds feed; fallback path: suspend cashout when the main line moves). | **Conditional — gated on Trading sign-off** | Full market parity with competitors |

> Phase 3 is explicitly conditional on Trading sign-off of the chosen approach for handling line moves on Spread / Totals.

---

## 5. Use Case Scope

### 5.1 In Scope

> Scenarios and workflows that **ARE** covered by V1 launch.

---

#### 🎬 Use Case 1: Cash Out a Pending Bet When the Line Has Not Moved

| | |
|:---|:---|
| **👤 Actor** | Punter with a pending Moneyline Bet on a league enabled in the back office, where the current market price is the same as the price at Bet placement |
| **🎯 Goal** | Settle the Bet early at a known fee, without waiting for the event to grade |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter places $100 on **Selection A @ +105**. The back-office template assigned to this league has Admin Fee = 5%.
2. Some time later, the market price for Selection A is unchanged.
3. Punter opens the sportsbook → My Bets → **Cash Out** tab. The widget shows the Bet card with the Cash Out icon and a "Cash Out $95.00" button — calculated as `Wager × (1 − Admin Fee) = 100 × (1 − 0.05) = $95.00`.
4. Punter clicks the button. With "Show Confirmation" ON, the button switches to "Confirm $95.00 ▶" highlighted in light green for 5 seconds.
5. Punter clicks again to confirm. Validation runs against the current market price.
6. Price has not changed during validation → cashout proceeds. Success message ("✓ Cashed Out · $95.00 returned to balance"). After 5 seconds the Bet card disappears from the widget.
7. The Punter sees the Bet under My Bets → Graded with status "Cashed Out", in Bet History with To Return = $95.00, and in Transactions as a "Cashed Out" credit + the original "Wager (Debit)" line.
8. Trading sees the liability change in the unified liability report — Profit, Payout, and Volume update on both selections.

</details>

---

#### 🎬 Use Case 2: Cash Out a Pending Bet When the Line Has Moved in Punter's Favour

| | |
|:---|:---|
| **👤 Actor** | Punter with a pending Bet on a league enabled in the back office, where the market price has moved to favour their selection |
| **🎯 Goal** | Lock in a partial return that reflects the favourable price move, minus the vig the platform retains |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter has $100 on Selection A. The current market makes Selection A more likely to win than at Bet placement.
2. Cashout offer is calculated using the live formula: `(Initial Odd / True Probability New Odd) × Wager × (1 − Vig Rate)`. Vig is looked up from the template's vig table by `% Bet Change Win/Lose`.
3. Widget shows "Cash Out $185.03" (example value). Amount updates as the market price moves.
4. Punter clicks Cash Out. Validation begins. With "Show Confirmation" OFF, the validation starts immediately on first click.
5. Price does not change during validation → cashout proceeds. Success state shown. Line disappears from widget after 5 seconds. Bet graded as "Cashed Out".
6. Trading sees Profit, Payout, and Volume update on both selections in the unified liability report. The opposite selection's Profit also updates per the formula `Total Profit = OtherSide_Total_Risk + ThisSide_Total_Payout + Σ(Profit of cashed-out bets on opposite side)`.

</details>

---

#### 🎬 Use Case 3: Cash Out a 3-Way Bet When Price Changes UP During Validation

| | |
|:---|:---|
| **👤 Actor** | Punter with a pending 3-way Bet (Home / Draw / Away) on a league enabled in the back office |
| **🎯 Goal** | Cash out a 3-way Bet and have the system handle a favourable price move automatically |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter has $50 on **Home** in a 3-way market. Cashout offer reads "Cash Out $42.30".
2. Punter clicks Cash Out. Validation begins.
3. **During validation, Home's price moves up.** Cashout offer recalculates to $48.20 using the same formula with the new market state. Total Implied is calculated across all 3 selections (Home / Draw / Away).
4. Widget shows "▲ The Cash Out return has changed" message in green. New amount $48.20 is highlighted in light-green pill style.
5. Because the price moved **UP** (favourable), cashout proceeds automatically at the new higher amount regardless of the Accept Cash Out Changes toggle.
6. Success: "✓ Cashed Out · $48.20 returned to balance". Message clears after 3 seconds. Line disappears from widget after 5 seconds.
7. Trading sees the liability update across all three selections in the unified liability report.

</details>

---

#### 🎬 Use Case 4: Cash Out Interrupted Because Price Moved DOWN and "Accept Changes" is OFF

| | |
|:---|:---|
| **👤 Actor** | Punter with a pending Bet on an enabled market — has "Accept Cash Out Changes" toggle OFF |
| **🎯 Goal** | Be offered a chance to confirm or reject the new lower amount when the market moves against them mid-validation |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter clicks "Cash Out $80" on a pending Bet. Validation begins.
2. During validation, market price moves against the Punter — new offer is $75.
3. Widget shows "▼ The Cash Out return has changed" in amber. New amount $75 is highlighted in amber pill style.
4. Because Accept Cash Out Changes is **OFF**, the cashout flow is **interrupted**. The Bet is NOT cashed out. The button now reads "Cash Out $75" — Punter must click again if they want to proceed at the lower amount.
5. The original $80 offer is gone — the Punter cannot recover it. The next click starts a fresh validation against whatever the price is at that moment.
6. If Punter does not act, the offer continues to update with the market.

</details>

---

#### 🎬 Use Case 5: Cash Out Suspended During Validation — Market Goes Off-Price Mid-Flow

| | |
|:---|:---|
| **👤 Actor** | Any Punter clicking Cash Out on a pending Bet |
| **🎯 Goal** | Receive a clear signal that the cashout could not complete because the market suspended, and not be debited or credited |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Punter clicks "Cash Out $XX.XX". Validation begins.
2. Mid-validation the market suspends (e.g., a goal is being reviewed, or the trader pulls the price).
3. Widget shows "The Cash Out was suspended" message for 3 seconds.
4. Button reverts to the standard Suspended state (lock icon + "Suspended" label).
5. Bet remains pending. No funds movement. No change in My Bets / Bet History / Transactions.
6. When the market reopens, the cashout offer reappears in the widget at the new market price.

</details>

---

#### 🎬 Use Case 6: Trading Operator Configures a New Cash Out Template

| | |
|:---|:---|
| **👤 Actor** | Trading operator with access to the Cash Out back-office tool |
| **🎯 Goal** | Create a template with a specific admin fee and vig table, then assign it to a league via the Settings module |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Operator opens the back-office tool → **Templates Manager** and clicks "+ New".
2. Names the template, sets **Admin fee = 4%**, enables **Max cashout cap** at 90% return, sets **Min wager = $5**.
3. Edits the **vig rate table** — 25 default rows from -95% to +95%, with vig values approved by Trading.
4. Saves. The template appears in the active list and is now selectable in the Settings module and Calculator.
5. Operator switches to **Settings Manager**, selects the league row, clicks Edit, sets **Cashout enabled = On**, sets **Template** dropdown to the new template, clicks Save.
6. Within minutes, the customer-site widget begins offering Cash Out on Bets in this league using the new template's pricing. The Calculator tab uses this template to validate offer amounts ad-hoc.

</details>

---

#### 🎬 Use Case 7: Trading Operator Applies a Differentiated Cash Out Rule to a Customer Group

| | |
|:---|:---|
| **👤 Actor** | Trading operator with access to the Customer Manager module |
| **🎯 Goal** | Override the default sport / league cashout settings for a specific customer segment (e.g. a VIP group) |

<details>
<summary><strong>📖 Scenario</strong> — click to expand</summary>

1. Operator opens **Customer Manager**, selects the brand, selects the customer group type (Group / Country / CSV upload), selects the group.
2. Operator selects the market filter (e.g. Money Line) and sees the global enable + template assignment for this group.
3. Operator opens **Advanced settings**, sees the per-sport override table, and disables Cash Out only for one specific sport for this group while leaving all others on.
4. Saves. Punters in this group now see Cash Out on every other sport but not on the disabled one. Punters outside this group are unaffected.

</details>

---

### 5.2 Out of Scope

> What is explicitly **NOT** included in V1 launch.

- ❌ **Spread and Totals markets** — pricing problem (original-line-price unknown when the main line moves) is unresolved and deferred to Phase 3.
- ❌ **Parlay / Teaser / If-Bet cashout** — single Bet only in V1. Multi-leg pricing requires a separate model.
- ❌ **Mobile native app** — V1 is web only (desktop + mobile-web). Native app integration is Phase 2+.
- ❌ **Retail / cashier / kiosk surfaces** — no in-store cashout in V1.
- ❌ **Partial cashout** (cashing out only part of a Bet) — full cashout only in V1.
- ❌ **Auto-cashout at preset thresholds** ("cash out automatically when offer reaches $X") — not in V1.
- ❌ **Cashout on settled / void Bets** — eligibility ends the moment the Bet grades by normal settlement; void handling continues per the existing void / refund flow.

> **Rule applied:** Out-of-scope items here are scenario-level. Feature-level exclusions (e.g., "no auto-cashout engine") are also listed in §6 Won't Have to align expectations on capability.

---

## 6. Product Feature Scope (MoSCoW)

> 📚 For more on MoSCoW prioritization, see [productplan.com/glossary/moscow-prioritization](https://www.productplan.com/glossary/moscow-prioritization).

### 🔴 Must Have — Non-negotiable. The initiative fails without these.

| # | Feature / Capability | Rationale | Owner |
|:--:|:---|:---|:---|
| 1 | **Cashout offer formula** with two price-state branches: when the market price equals the placement price → `Cashout = Wager × (1 − AdminFee)`. When the market price differs → `Cashout = (InitialOdd / TruePropOdd) × Wager × (1 − VigRate)`, with vig looked up from the template's vig table by `% Bet Change Win/Lose`. The same formula applies to every Bet regardless of when it was placed or when it is cashed out. | Foundation of all cashout pricing. The two branches are determined by **price state**, not bet timing — one unified formula. | Trading + Eng (Customer Site) |
| 2 | **2-way and 3-way market support** including 3-way markets (Home / Draw / Away). Total Implied is computed across all selections in the market regardless of count. | The launch event includes 3-way markets; without 3-way support there is no V1 launch | Eng (Customer Site) + UX |
| 3 | **Back office — Settings Manager**: brand / market / sport / league hierarchy. **One Cashout-enabled toggle per league**. Template assignment per league. Edit-mode gating with Save / Cancel. | Trading needs a single surface to enable / disable cashout per league and assign templates. The same configuration applies to every Bet on that league. | Eng (Back Office) + Trading Ops |
| 4 | **Back office — Templates Manager**: name, admin fee %, max cashout cap (toggle + return % + min wager), editable 25-row vig rate table, Save / Clone / Delete. | Different sports / events need different vig profiles; without templates Trading cannot calibrate pricing per market | Eng (Back Office) + Trading |
| 5 | **Back office — Customer Manager**: brand + group / country / CSV-list + market filter; cashout enable toggle; per-group template; advanced sport-level overrides for the selected group. | VIP and risk-flagged groups need differentiated rules; commercial requirement | Eng (Back Office) + VIP Relations |
| 6 | **Customer site — Cash Out widget** with the full state set: Empty, Available, Suspended, Confirm, Changed Up, Changed Down, Suspended-during-validation, Successfully Cashed Out | Each state is a distinct UX requirement and a QA matrix entry. Without all of them the customer flow has gaps. | Eng (Customer Site) + UX + QA |
| 7 | **Customer site — Show Confirmation toggle**: 5-second confirm window with a light-green amount pill before cashout proceeds | Customer-protection feature; reduces accidental cashouts | Eng (Customer Site) + UX |
| 8 | **Customer site — Accept Cash Out Changes toggle**: controls behaviour when price moves DOWN mid-validation (ON → proceeds at lower amount; OFF → flow interrupted, customer must re-confirm) | Customer-control feature; without it the change-down flow has no completion path | Eng (Customer Site) + UX |
| 9 | **Customer site — Cash Out icon and discoverability**: icon shown in Offering, Betslip, and My Bets pending. NEW badge shown to first-time visitors and dismissed once seen. | Discoverability — Punters need to see where the feature applies | Eng (Customer Site) + UX |
| 10 | **Customer site — post-cashout visibility**: My Bets new "Cashed Out" status; Bet History new "To Return" column with status filter; Transactions new type "Cashed Out" + description "Wager Cashed Out" | Punters must see what was returned and where; Finance reconciliation needs the transaction type | Eng (Customer Site) + Finance |
| 11 | **Unified Trading liability reporting** for cashed-out Bets. The same liability flow applies to every Bet regardless of placement timing or cashout timing. The cashed-out bet's payout is replaced by the cashout amount; Profit, Payout, and Volume update on both / all selections; Risk and Count are unchanged. Wager Coverage shows a Cash Out icon. Wager Details Status = "Cashed Out", Paid = cashout amount. | Trading cannot hedge without real-time liability visibility. A single unified reporting surface avoids ambiguity about where a given Bet shows up. | Eng (Trading Tools) + Trading |
| 12 | **Cashout button suspended** when no current price for the market | Without a market price, the formula cannot evaluate — must not offer a stale price | Eng (Customer Site) |
| 13 | **New transaction type in the ledger**: "Wager Cashed Out" credit + matching wager-debit reference | Finance reconciliation, NGR calculation, license-jurisdiction reporting | Finance + Eng (Trading Tools) |
| 14 | **New analytics events** for the full validation flow: cashout-offered, cashout-clicked, cashout-confirmed, cashout-suspended, cashout-completed, accept-changes-toggle, show-confirmation-toggle | Without these, none of §3.2 KPIs can be measured | Analytics + Eng (Customer Site) |

### 🟠 Should Have — Important but not vital.

| Feature / Capability | Rationale | Owner |
|:---|:---|:---|
| Customer Manager — CSV upload for customer-ID lists | Faster operationally than syncing from external customer systems for ad-hoc segments | Eng (Back Office) + Trading Ops |
| **Calculator tab** in the back office — ad-hoc cashout offer validation against any template | Trading verification tool; reduces "is this the right number?" tickets to Eng | Eng (Back Office) + Trading |
| **Live-update widget** without manual refresh (push-driven price updates) | Improves customer experience; reduces stale-offer abandonment | Eng (Customer Site) |
| Validation flow telemetry — % suspended-during-validation, % accept-changes-on, % accept-changes-off | Feeds back into vig table calibration | Analytics |

### 🟡 Could Have — Nice to have.

| Feature / Capability | Rationale | Owner |
|:---|:---|:---|
| Localised widget messages (multiple languages) | Multi-region customer base | UX + Localization |
| Customer-facing analytics ("you've cashed out N times this month, saved $X") | Engagement gamification | Product + Eng |
| Print Bet Slip with Cashed-Out status | Some Punters request hardcopy ticket records via CS | Eng (Customer Site) |

### ⚫ Won't Have — Explicitly excluded in V1.

| Feature / Capability | Rationale |
|:---|:---|
| Spread and Totals cashout | Original-line-price problem unresolved; deferred to Phase 3 conditional on Trading sign-off |
| Parlay / Teaser / If-Bet cashout | Multi-leg pricing logic + per-leg vig is a separate model |
| Auto-cashout at preset thresholds | No customer-controlled rules engine in V1 |
| Partial cashout | Full-only in V1 |
| Mobile native app cashout | Web only at launch |
| Retail / kiosk cashout | Not in scope for digital-first V1 |
| Cashout on settled or voided Bets | Existing void / refund flow remains canonical |

### 6.1 Assumptions & Constraints

<details open>
<summary><strong>✅ Assumptions</strong></summary>

- The existing decimal-odds model and market-margin (Total Implied) calculation are the canonical pricing inputs and are NOT being redesigned.
- The same wallet / balance / settlement engine handles cashout credits via the new "Wager Cashed Out" transaction type — no new wallet rules required.
- The same back-office configuration applies to every Bet regardless of placement timing — Trading does not need separate Pre and Live templates, sport toggles, or vig tables.
- The same liability reporting flow applies to every cashed-out Bet regardless of placement or cashout timing — Trading does not need to consult separate tools for the same data.
- The same cashout formula applies regardless of placement or cashout timing — only the price state matters.
- Trading will provide validated production values for the default vig rate table and admin fee per template before launch.
- The odds feed delivers current decimal odds for all enabled leagues at the granularity required by the formula.
- Punters cashing out at any moment between placement and grading is the design intent — no time-window restrictions.

</details>

<details open>
<summary><strong>⚠️ Constraints</strong></summary>

- Must not break or weaken existing settlement logic, void handling, or wallet integrity.
- Must not introduce new regulatory categories without Legal sign-off in each license jurisdiction.
- Must operate within the existing odds-feed cadence — no requirement for a faster feed at launch.
- Liability reporting must update in near-real-time so Trading hedging decisions are not based on stale data.
- The customer site, back office, and trading tools must remain in lockstep — a template change in the back office must propagate to live offers and to liability calculations within minutes.

</details>

### 6.2 Unknowns & Open Items

| Open Item / Unknown | Owner | Due Date | Status |
|:---|:---|:---:|:---:|
| Confirm V1 sport / league enablement list | Product Operations | TBD — before code freeze | 🔴 Open |
| Validate production vig rate table values and default admin fee per template | Trading | TBD — before code freeze | 🔴 Open |
| Confirm odds feed supports current decimal odds for all enabled leagues at required cadence | Eng (Trading Tools) + Vendor Mgmt | TBD — before code freeze | 🔴 Open |
| Confirm Legal / Compliance sign-off path per license jurisdiction | Legal | TBD — before launch | 🔴 Open |
| Confirm Customer Support playbook handover — new "Cashed Out" status, dispute handling, change-down flow | CS Manager | TBD — 4 weeks before launch | 🟡 In Progress |
| Confirm Analytics baselines and dashboard delivery timeline for §3.2 KPIs | Analytics | TBD — before launch | 🟡 In Progress |
| Confirm whether Phase 2 league expansion rides V1 release or follows a separate Trading approval cycle | Product | TBD — Phase 2 planning | 🔴 Open |
| Confirm "Show Confirmation" and "Accept Changes" toggle defaults at first-time-user state | UX + Product | TBD — UX review | 🔴 Open |
| Confirm liability reporting near-real-time SLA (acceptable lag between cashout event and Trading-tool update) | Trading + Eng (Trading Tools) | TBD — before code freeze | 🔴 Open |

> **Rule applied:** every open item is a specific answerable question with a named owner — no generic "TBD".

### 6.3 Active Programs / Potential Combines

| Program / Initiative | Overlap / Relationship | Recommendation |
|:---|:---|:---|
| Launch tournament readiness program | Cash Out is a named V1 deliverable for the launch tournament | **Combine** — Cash Out V1 timeline is gated by the launch event |
| Customer site redesign / design system refresh | Cash Out widget visual states must align with current design system | **Coordinate** — UX joint review required |
| Odds feed migration / upgrade | Cashout requires current decimal odds at speed; feed reliability is a critical dependency | **Monitor for conflict** — feed regression is a launch blocker |
| Trading tools modernisation | Liability reports change in this initiative; if Trading tools are also being updated, sequencing matters | **Coordinate** — joint planning to avoid double-rework |
| Wallet / payment reconciliation modernisation | New "Wager Cashed Out" transaction type lands in the same ledger | **Coordinate** — Finance to confirm no overlap |
| Mobile native app rollout | Cash Out V1 is web-only; mobile is explicit Phase-2 scope | **Do NOT combine** — mobile parity is a separate program |

If any of the above are not currently active programs, this section should be updated before the Investment Template.

---

## 7. Use Case Diagrams

> Outline user experience flows and workflows. Insert diagrams, flowcharts, or wireframe references below.

### 🎨 Diagram 1: Cash Out Widget — Validation Flow

> 🔗 _Working interactive HTML reference: [cashout_suite_v2_5.html](https://github.com/KikePinnaple90/cash-out/blob/main/cashout_suite_v2_5.html) → Customer site → Widget tab. Final design link **TBD**._

**Description.** Diagrams the end-to-end validation flow for a cashout request. From the moment Punter clicks the Cash Out button, through the validation window, to the four resolution branches: (a) no price change → cashout proceeds at offered amount; (b) price up → cashout proceeds at new higher amount automatically; (c) price down + Accept Changes ON → cashout proceeds at new lower amount; (d) price down + Accept Changes OFF → flow interrupted, new offer surfaced for re-confirmation.

---

### 🎨 Diagram 2: Back-Office Configuration Hierarchy

> 🔗 _Working interactive HTML reference: [cashout_suite_v2_5.html](https://github.com/KikePinnaple90/cash-out/blob/main/cashout_suite_v2_5.html) → Management tab. Final design link **TBD**._

**Description.** Shows the data hierarchy `Brand → Market → Sport → League → Template → (Admin fee, Cap, Vig table)` plus the Customer Manager override layer `Brand + Market + Customer Group → Optional sport-level overrides`. Illustrates the three modules (Settings, Templates, Customer) and how a single template change propagates to every league assigned to it. The configuration is unified — there is no separate Pre-Match or Live track.

---

### 🎨 Diagram 3: Liability Update Flow

> 🔗 _Working interactive HTML reference: [cashout_suite_v2_5.html](https://github.com/KikePinnaple90/cash-out/blob/main/cashout_suite_v2_5.html) → Reports tab. Final design link **TBD**._

**Description.** Single decision diagram showing how a cashed-out Bet updates the unified liability report:

- **(a)** The cashed-out Bet's payout in the report is replaced by the cashout amount.
- **(b)** Profit, Payout, and Volume update on this selection AND on the opposite / other selections in the market via the formulas in [cashout-reporting](https://github.com/KikePinnaple90/cash-out/blob/main/cash%20out%20skills/cashout-reporting/SKILL.md).
- **(c)** Risk and Count are unchanged. Wager Coverage shows a Cash Out icon. Wager Details Status = "Cashed Out", Paid = cashout amount, Lost / Won fields are emptied.

---

## 8. Roadmap Alignment

| | |
|:---|:---|
| **🏁 Roadmap / Swim Lane** | Customer Experience & Trading Operations |
| **🎯 Roadmap Theme** | Competitive parity & retention engagement |
| **⏰ Timeframe** | **Near-term (V1)** + **Mid-term (Phase 2 expansion)** + **Long-term conditional (Phase 3 Spread/Totals)** — V1 anchored to the launch tournament; Phase 2 follows; Phase 3 is conditional on Trading sign-off of the Spread / Totals approach. |

### Delivery Milestones

| Roadmap Year | Initiative / Milestone | Dependency | Status |
|:---|:---|:---|:---|
| **Year 1 — Near-term** | **Phase 1:** Cash Out V1 — Moneyline 2-way + 3-way (launch tournament anchor) | Trading sign-off on vig table + admin fee; odds feed for enabled leagues; back-office Eng delivery; unified liability reporting integration | 🟡 Drafting Scope |
| **Year 1 — Mid-term** | **Phase 2:** Market expansion across all priority leagues for Moneyline | Phase 1 stable; Trading capacity to validate per-sport vig profiles | 🔴 Not started |
| **Year 1 / Year 2 — Long-term** | **Phase 3 (conditional):** Spread + Totals cashout | Trading sign-off on chosen approach for handling line moves | 🔴 Not started |

---

> 📝 _This document is a living template. Update version and status as the initiative progresses through review and approval._
