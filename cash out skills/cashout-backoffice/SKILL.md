---
name: cashout-backoffice
description: Build, update, or explain the Cash Out Management back office tool (formerly called Sasha) used to configure cashout templates, sport/league settings, and customer rules at BetOnline/FoshTech. Use this skill whenever the user mentions the back office cashout config, templates manager, settings manager, customer manager, admin fee configuration, vig rate table editing, sport-league cashout enable/disable, or asks to build or update the management UI for cash out. Also use when connecting the back office to the calculator or customer site prototype.
---

# Cash Out Management (Back Office)

The back office tool — internally called "Sasha" — lets trading and operations configure the cash out feature. There are three modules.

## Module 1: Settings Manager

Controls which sports and leagues have cashout enabled, and which template applies to each.

### Access path
Settings Manager tab > Brand filter > Market filter > Sport sidebar > League table

### Filters
- **Brand**: All / BetOnline / SportsBetting (etc.)
- **Market**: Money Line / Spread / Total

### Sport sidebar
Lists all sports. Selecting a sport loads its league table on the right.

### League table columns
| Column | Description |
|---|---|
| Sport | Sport name |
| League | League / competition name |
| Region | Country/region |
| Live | Toggle — cashout enabled for live events |
| Pre | Toggle — cashout enabled for pre-event |
| Template | Dropdown — which cashout template to apply |

### Edit mode
User must click Edit to unlock the table. Changes are applied with Save / discarded with Cancel. In view mode, Live/Pre show as coloured badges (green = On, red = Off), Template shows as a grey badge.

### Global enable
At the top of the page: a single Live/Pre toggle that enables/disables cashout across all brands at once.

## Module 2: Templates Manager

Creates and edits cashout configuration templates. Templates are then assigned to sport/league rows in the Settings Manager.

### Template list
Left panel shows all templates. Click to select. "+ New" creates a blank template.

### Template editor fields

#### Template name
Editable inline. Free text. Examples: "Cashout MVP", "World Cup 2026", "Template New".

#### Admin fee (%)
Applied in the pre-event no-line-move scenario:
```
Cashout = Wager * (1 - Admin Fee)
```
Typical value: 3-5%.

#### Max cashout
- **Enable cap toggle**: on/off
- **Return % cap**: when cashout reaches this threshold relative to the potential return, it is capped
- **Minimum wager**: template only applies to bets above this stake

#### Vig rate table
Editable version of the vig rate table for this template. Each row has:
- From % (lower bound of the range)
- Up to % (upper bound)
- Vig rate %

Rows can be added (+Row) or deleted (x). Default table has 25 rows covering -95% to +95%.

### Template actions
- **Save**: persists changes
- **Clone**: creates a copy with " (copy)" suffix
- **Delete**: removes template (disabled if only one template exists)

## Module 3: Customer Manager

Applies cashout settings to specific customer segments, overriding the default sport/league settings.

### Filters
- **Brand**: select which brand's customers to configure
- **Customer group type**: Group / Country / CSV
  - **Group**: groups defined in NGame and Synergy
  - **Country**: all users in a given country get these settings
  - **CSV**: upload a list of customer IDs
- **Market**: Money Line / Spread / Total

### General settings (top level)
Once brand + group + market are selected:
- **Cashout enabled toggle**: Live/Pre
- **Template selector**: assigns a template to this entire group
- Save / Cancel

### Advanced settings
Opens a sport-level override table for the selected group. Columns:
- Sport name
- Enable toggle (per sport)
- Template selector (per sport)

This allows, for example, disabling cashout for a VIP group only in Soccer, while keeping it on for all other sports.

## Data model (entity hierarchy)

```
Brand
  └── Market
        └── Sport
              └── League / Competition
                    └── Template assignment
                          └── Template
                                ├── Admin fee
                                ├── Max cashout cap
                                └── Vig rate table (25 rows)

Customer groups override the sport/league defaults:
Brand + Market + Customer Group
  └── Sport (optional advanced override)
```

## Connection to the calculator and site

- Templates created here are available as a dropdown in the Calculator tab
- When a template is selected in the Calculator, its admin fee and vig table drive all cashout calculations
- The Settings Manager league table (live/pre enable + template assignment) drives which bets appear in the Customer Site widget
- Disabling a league in Settings Manager removes its bets from the site widget in real time

## UI conventions

- **Edit mode gating**: tables are read-only by default; user must click Edit
- **Toggle rendering**: in view mode = coloured badge; in edit mode = actual toggle input
- **Template badges**: grey pill in view mode; dropdown in edit mode
- **Sidebar active state**: blue left-border accent on selected sport
- Active/selected template in list gets a blue "active" badge if it's the template currently loaded in the Calculator

## Reference files

- `references/template-defaults.md` — default vig table and default field values for new templates
