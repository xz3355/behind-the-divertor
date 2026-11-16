# SPECIFICATIONS: Proposed Regulation Simulator

*Chelsea Zhao · Beyond the Brand · Session 9 · November 16, 2026*
*Companion to CONCEPT-IDEA.md. Implemented as Section 6 of the "Behind the Divertor" demo site.*

## 1. Scope

A new section on the demo site, **"6 · Proposed regulation simulator"**, that:

1. Lets the user choose a **regulation scenario** (preset or custom).
2. Applies the scenario's **rule logic** to a list of **supply chain nodes** (suppliers / parts).
3. Shows, for every node, a **result**: Blocked, Exempt, Watch, or Unaffected, with the reason.
4. Summarizes **exposure** (share of supply hit), **timing**, **certainty**, and **suggested responses**.

Runs entirely in the browser (plain HTML + JavaScript, no server), so it works on GitHub Pages.

## 2. Data model

### 2.1 Scenario (the "injected intelligence")

| Field | Type | Example (transceiver preset) |
|---|---|---|
| `id` | string | `fcc-transceiver-3t2` |
| `title` | string | FCC ban on new Chinese optical transceivers |
| `certainty` | enum: `in_force` · `proposed_bill` · `proposed_rule` · `reported` | `reported` |
| `authority` | string | FCC (Covered List / equipment authorization) |
| `scope_part` | string | Optical transceivers |
| `trigger_countries` | list | `["CHN"]` |
| `definition` | enum: `producer` · `location` | user-selectable (default `producer`) |
| `min_generation` | enum: `800G` · `1.6T` · `3.2T` | user-selectable (default `3.2T`) |
| `effective_year` | number | 2028 (3.2T ramp, reported) |
| `exemption_us_bom` | number 0–100 or null | user-selectable (default 65) |
| `applies_to_existing` | bool | false (new models only) |
| `transition_years` | number | 0 |
| `sources` | list of {label, url, type: primary/secondary} | Reuters via Fusion Worldwide; Morgan Stanley via 404K Research |

### 2.2 Node (a supplier or part in the chain)

| Field | Type | Example |
|---|---|---|
| `name` | string | Eoptolink (Thailand plant) |
| `role` | string | Module maker |
| `hq_country` | ISO3 | CHN |
| `production_country` | ISO3 | THA |
| `parent_listed` | list | `[]` or `["1260H"]` |
| `market_share` | number (%) | 14 (company-level, Cignal AI 1Q26) |
| `us_bom_share` | number (%), **assumption** | 60 |
| `sayari_url` | url | link to entity in Sayari Graph |
| `note` | string | Sayari shows Thai, Singapore and US subsidiaries |

**Rule:** every number that is an assumption (not sourced) is labeled "illustrative" in the UI.

## 3. Rule logic (per node)

```
is_chinese =
  definition == "producer"  ? hq_country in trigger_countries
                            : production_country in trigger_countries

if not is_chinese:                       result = UNAFFECTED
elif exemption_us_bom != null
     and us_bom_share >= exemption_us_bom: result = EXEMPT
elif generation in scope:                result = BLOCKED (new models from effective_year)
else:                                    result = WATCH

if parent_listed not empty: add flag "Listed parent (e.g. 1260H) - DoD/federal risk even if exempt"
```

Exposure = sum of `market_share` for BLOCKED nodes (and separately for WATCH).

### Tungsten preset (DFARS, in force)

```
blocked if any stage (mine, refine, produce) is in {CHN, RUS, PRK, IRN}
applies to DoD procurement from 2027-01-01
```

## 4. User interface

1. **Scenario picker:** two buttons: "Optical transceivers (FCC, reported)" and "Tungsten (DFARS, in force)".
2. **Certainty badge** next to the title: green *In force*, amber *Proposed bill*, red *Reported only*.
3. **Assumption controls** (transceiver preset):
   - Rule starts at generation: 800G / 1.6T / 3.2T
   - "Chinese" defined by: company HQ / production location
   - US-BOM exemption threshold: slider 0–100% (off at 0)
   - Assumed US share in Chinese-made modules: slider (illustrative)
4. **Node table:** name (linked to Sayari), country, result chip, reason in one line.
5. **Summary cards:** share of supply blocked, when it starts, certainty.
6. **Responses panel:** plain-language actions that change with the result (stockpile, dual-source, raise US content, check parents in Sayari).
7. **Sources list** with primary vs secondary marked.

Accessible: works with keyboard; results are text, not color alone; responsive on mobile; light/dark theme follows the existing site.

## 5. Preset data (transceiver demo)

| Node | HQ | Production | Share (company) | Listed parent | Source |
|---|---|---|---|---|---|
| Zhongji InnoLight | CHN | CHN | ~34% | 1260H (Jun 2026) | Cignal AI; DoW 1260H list; Sayari |
| Eoptolink (Chengdu) | CHN | CHN | ~14% (company total, split with Thai plant) | none; named in S.5548 | Cignal AI; S.5548; Sayari |
| Eoptolink (Thailand plant) | CHN | THA | (part of above) | none | Sayari subsidiaries |
| HG Genuine | CHN | CHN | ~3% | none | Cignal AI (three Chinese leaders = 51%) |
| Coherent | USA | multiple | combined ~23% with Lumentum | none | Counterpoint via FourWeekMBA |
| Lumentum | USA | multiple | (see above) | none | same |
| Applied Optoelectronics | USA | USA | small, expanding | none | AOI newsroom via Broadband Breakfast |

## 6. Primary sources to cite in the UI

| Claim | Source | Type |
|---|---|---|
| FCC 26-50 component rule, effective Oct 13, 2026 | FCC-26-50A1 (docs.fcc.gov); Federal Register 2026-18535 | Primary |
| Routers added to Covered List, Mar 23, 2026 | FCC fact sheet DOC-420034A1 | Primary |
| Power inverters added, Jul 28, 2026 | FCC Public Notice DA 26-786 | Primary |
| InnoLight on 1260H list, Jun 8, 2026 | DoW Section 1260H list | Primary |
| S.5548 introduced Sep 24, 2026 | Congress.gov S.5548 | Primary |
| Transceiver ban, 3.2T, 65% BOM | Reuters (Aug 4, 2026); Morgan Stanley (Oct 1, 2026) | Secondary / reported only |
| DFARS tungsten ban from Jan 1, 2027 | DFARS final rule, Federal Register, May 30, 2024 | Primary |

## 7. Out of scope (next iteration)

- Live Sayari API calls from the page (needs a backend and keys).
- Pulling real BOM data per module; for now the US-BOM share is a user assumption.
- Cost and lead-time modeling.
- Custom scenario builder form (fields above are ready for it).

## 8. Acceptance checks

- Switching presets changes the badge, controls, table and summary.
- Transceiver preset defaults: 3.2T, HQ definition, 65% threshold, 60% assumed US share → InnoLight, Eoptolink and HG Genuine BLOCKED for new 3.2T models; Coherent, Lumentum, AOI UNAFFECTED; exposure ≈ 51%.
- Raising assumed US share to 65% or more → Chinese makers move to EXEMPT; InnoLight keeps the 1260H flag.
- Switching definition to production location → Eoptolink Thailand plant moves to UNAFFECTED.
- Choosing 800G → note that existing authorized models are still not affected (new models only).
