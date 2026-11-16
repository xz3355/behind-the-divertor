# CONCEPT-IDEA: Proposed Regulation Simulator

*Chelsea Zhao · Beyond the Brand · Session 9 · November 16, 2026*
*Addition to the "Behind the Divertor" Sayari demo*

**Repo:** github.com/xz3355/behind-the-divertor  
**Live site:** xz3355.github.io/behind-the-divertor (Section 6: Proposed Regulation Simulator)

## The idea in one sentence

Let an analyst take a new piece of intelligence about a **possible** regulation, turn it into a scenario, and drop it into the supply chain map to see which suppliers, parts and timelines it would hit, before the rule is final.

## Why this is needed

My demo so far answers "what does this supply chain look like today?" Companies also need to answer "what happens if Washington does X next year?" Regulations on data center hardware are arriving fast, and they come at different levels of certainty:

| Signal | Status | What it means |
|---|---|---|
| FCC 26-50 component rule | **In force** since Oct 13, 2026 | New devices with a logic-bearing component from a Covered List entity cannot get FCC authorization |
| Foreign-made routers (Mar 2026) and power inverters (Jul 2026) on the Covered List | **In force** | New models need a DoW/DHS Conditional Approval |
| InnoLight on the Pentagon 1260H list | **In force** since Jun 8, 2026 | DoD cannot buy from listed companies (from Jun 30, 2026) |
| S.5548 (InnoLight, Eoptolink out of federal national-security systems) | **Proposed bill** | Introduced Sep 24, 2026; in committee; 5-year transition |
| FCC ban on new Chinese optical transceivers from 3.2T, possible 65% US-BOM exemption | **Reported only** | Reuters (Aug 2026) and a Morgan Stanley note (Oct 2026); no FCC document yet |

A planner cannot treat these five the same way. The simulator keeps them apart and shows how the answer changes when an assumption changes.

## Who would use it

- **Supply chain and procurement teams** at hyperscalers (Microsoft, Amazon, Google) deciding what optics to buy for data centers that open in 2028–2030.
- **Trade and regulatory counsel** (like our guest, Sibilla Grenon) who need to explain to the business which rule matters, when, and how sure we are.
- **Students and researchers** in this course, who can plug in their own Sayari findings on real parts later.

## What questions it answers

1. If this rule passes, **which of my suppliers are blocked, exempt, or unaffected?**
2. **How much of my supply** (by market share) is exposed?
3. **When** does it bite? (Which product generation, which year, any transition period?)
4. **What hidden exposure** does Sayari show? (A "Western" supplier owned by a listed parent; a Chinese supplier producing in Thailand.)
5. **Which response** works best: stockpile, switch supplier, or ask the supplier to raise US content?

## Demo scenario (from the assignment)

**Optical transceivers for AI data centers.** Chinese makers hold about 60% of datacom transceiver revenue. InnoLight alone held about 34% in 1Q26. A ban on *new* Chinese models from the 3.2T generation, with a 65% US-BOM exemption, would leave today's 800G and 1.6T alone but reshape buying for 2028 and after.

**The key insight the simulator surfaces:** the outcome depends on *definitions* that are not yet decided:
- **"Chinese" by producer or by production location?** The FCC has proposed splitting the Covered List into producer-based and location-based entries. Sayari shows Eoptolink has subsidiaries in Thailand, Singapore and the US. A location-based rule could let Thai-made Eoptolink modules through.
- **What counts as US content?** A US DSP (Broadcom or Marvell) plus a US laser (Lumentum or Coherent) may get a Chinese-assembled module close to 65%. That would shift profit to US component makers rather than remove Chinese assemblers.

## Second scenario (connects to my tungsten site)

**DFARS tungsten rule, in force January 1, 2027.** DoD may not buy tungsten mined, refined or produced in China, Russia, North Korea or Iran. Run on my tungsten map, it flags the Chinese fusion-grade powder maker (AT&M) and any ore that passes through Chinese refiners, while Vietnam's Masan refinery and Japan's A.L.M.T. remain usable for defense buyers.

## What success looks like

- A non-technical user can pick a scenario, move two or three sliders, and read a plain-language result in under a minute.
- Every result shows its **certainty level** and **source**, so a rumor is never presented as law.
- The same structure works for any part and any rule (generic), not just transceivers.

## Datacenter deployment and design considerations it should inform

- **Buy timing:** lock in 800G/1.6T volumes before any 3.2T cut-off; plan 3.2T sourcing early.
- **Design for swap-ability:** standard pluggable form factors make it easier to switch module vendors; co-packaged optics (optics inside the switch) make it harder.
- **Dual-source by country**, not just by company.
- **Track the whole box:** under FCC 26-50, a Western-branded switch or server can still be blocked by a covered chip inside it.
- **Power and network gear too:** foreign-made inverters for battery backup now need Conditional Approval for new models.
- **Federal tenants:** S.5548 would matter for government cloud regions first.

## Limits

- The transceiver ban is not yet law; numbers in the demo are **illustrative assumptions** where marked.
- Sayari name searches for shipments are noisy (searching "INNOLIGHT" also returned Foxconn and Amphenol factories), so the demo uses entity-level facts, not raw shipment counts.
- The tool shows exposure; it does not judge whether a security concern is valid.

## Questions for Sibilla Grenon

1. How does a company like Microsoft plan around a rule that is only *reported*, such as the transceiver ban?
2. If the FCC defines "Chinese" by production location, does moving assembly to Thailand or Vietnam solve the problem, or does the 1260H list follow the parent company?
3. How would a company prove a module meets a 65% US-BOM threshold? Who audits it?
4. Does FCC 26-50 change how you write supplier contracts (component disclosure, bills of materials)?
