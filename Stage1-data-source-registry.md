# Stage 1 — NASA Data Source Registry
## Lunar Trajectory & Propellant Efficiency: Apollo 11 vs Apollo 13 vs Artemis II

**Purpose:** Before any ingestion/cleaning code is written, this document fixes exactly which NASA sources feed the project, what each one actually contains, and which specific numbers are already confirmed straight from an official document vs. still need to be extracted in Stage 2. Nothing below is invented — anything not yet verified against a primary document is marked `UNVERIFIED` and excluded from the notebook until Stage 2 confirms it.

Terminology is locked in as you specified: the mechanism is a **free-return trajectory**, not a "slingshot." NASA's own material describes both Apollo 11 and Artemis II this way, and the diagram/framing in the project should use that term throughout.

---

## 1. Apollo 11

| Document | Type | Source | What it contains |
|---|---|---|---|
| **Apollo 11 Mission Report** (NASA-TM-X-62633 / MSC-00171) | Primary, official post-flight report | NTRS record: https://ntrs.nasa.gov/citations/19700008096 — direct PDF: https://ntrs.nasa.gov/api/citations/19700008096/downloads/19700008096.pdf | Preflight/flight/postflight data, actual trajectory and maneuver tables, **actual SPS propellant consumption** (loaded/consumed/remaining, fuel/oxidizer/total) |
| **Apollo 11 Flight Plan, Final** (July 1, 1969) | Primary, official pre-flight planning document | https://www.nasa.gov/wp-content/uploads/2025/11/a11fltpln-final-reformat.pdf (mirror: https://www.hq.nasa.gov/alsj/a11/a11fltpln_final_reformat.pdf) | **Planned** propellant budgets and Δv for individual maneuvers (TLI correction, LOI, lunar-orbit maneuvers, TEI) — this is the "planned" half of the actual-vs-planned comparison |
| Apollo 11 Preliminary Science Report (NASA SP-214) | Primary, official | https://ntrs.nasa.gov/citations/19700000726 | Lunar surface science only — **not used** for propulsion/trajectory numbers, listed for completeness |

**Already confirmed (from prior extraction, to be re-verified against the PDF directly in Stage 2 before use):**

SPS propellant, Apollo 11 (lb):

| | Fuel | Oxidizer | Total |
|---|---|---|---|
| Loaded | 15,712 | 25,091 | 40,803 |
| Consumed | 13,754 | 21,985 | 35,739 |
| Remaining | 1,958 | 3,106 | 5,064 |

Status: `TO BE RE-VERIFIED` — this table must be located on the actual page of the Mission Report PDF and the page number recorded in the pipeline's provenance field before it's treated as final.

---

## 2. Apollo 13

| Document | Type | Source | What it contains |
|---|---|---|---|
| **Apollo 13 Mission Report** (MSC-02680) | Primary, official post-flight report | NTRS record: https://ntrs.nasa.gov/citations/19710003598 — PDF: https://ntrs.nasa.gov/archive/nasa/casi.ntrs.nasa.gov/19710003598.pdf | Full trajectory section, CSM/LM propulsion performance, actual maneuver log for the abort and return |
| **"Apollo 13 Guidance, Navigation, and Control Challenges"** (Goodman, AIAA/NTRS 20090026451) | Primary-adjacent technical paper (NASA contractor, published via NTRS) | https://ntrs.nasa.gov/api/citations/20090026451/downloads/20090026451.pdf | Confirms **four** post-abort trajectory maneuvers: (1) return-to-free-return burn, (2) PC+2 speed-up/splashdown-relocation burn, (3)-(4) small entry-interface trim burns |
| NASA.gov "Apollo 13: Mission Details" | Official NASA public-facing summary (not primary telemetry) | https://www.nasa.gov/missions/apollo/apollo-13-mission-details/ | Narrative confirmation: ~35-second burn to restore free return (~5 hrs after the accident), a ~5-minute burn ~2 hrs after rounding the Moon to speed up return |

**Sources explicitly excluded from numeric use** (fine for narrative color, not for any number that ends up in a chart or table): Spaceflight Now transcripts, AmericaSpace retrospective, Quora answers, Orbiter-Forum posts. These are consistent with each other and with NASA's own summary (e.g., "35-second burn," "PC+2," "5-minute burn") but they're secondary — the notebook should pull the authoritative timestamps/Δv/propellant figures from the Mission Report PDF and the Goodman paper, not from these.

**Partially confirmed, needs primary-source page reference in Stage 2:**
- Free-return restoration burn: ~35 seconds, ~61h 30m mission elapsed time (MET) — consistent across NASA.gov and the Goodman paper's description, but the Mission Report's own maneuver table should be the cited number.
- PC+2 burn: ~5 minutes, ~2 hours after pericynthion.
- Closest lunar approach changed from a planned ~69 miles to an actual ~156 miles after the free-return restoration burn — this specific pair of numbers still needs a primary citation; flagged `UNVERIFIED` until found in the Mission Report or Goodman paper text itself.

---

## 3. Artemis II

| Document | Type | Source | What it contains |
|---|---|---|---|
| **Artemis II flight-derived ephemeris** (OEM/CCSDS format, zipped) | Primary — actual flight telemetry, not a simulation | https://www.nasa.gov/wp-content/uploads/2026/03/all-artemis-ii-oem-files.zip (linked from https://www.nasa.gov/missions/artemis/artemis-2/track-nasas-artemis-ii-mission-in-real-time/) | State vectors (position/velocity vs. time) for the actual flown trajectory — this is the modern-era equivalent of the Apollo mission-report trajectory tables, and it's the strongest dataset in the whole project because it's raw flight data, not a report summary |
| NASA SVS "Artemis II mission trajectory" visualization page | Primary, official — explicitly built from flight-derived ephemeris | https://svs.gsfc.nasa.gov/5632 | Confirms the visualization (and by extension the ephemeris) is flight-derived, not nominal |
| NASA SVS "Nominal (reference) Artemis II mission trajectory" | Official, but explicitly a **planned/nominal** trajectory, separate dataset | https://svs.gsfc.nasa.gov/5610 | Must be kept in its own column/tag in the schema — never merged with the flight-derived rows. NASA's own page states the actual trajectory may differ from this nominal one. |
| NASA blog: "Artemis II Flight Day 2: Orion Completes TLI Burn" | Primary, official mission-day report | https://www.nasa.gov/?p=980389 | Actual TLI burn data (see below). Note the post carries an editor's note that the propellant figures were updated after initial publication — use the current published values, and record the retrieval date in provenance. |

**Confirmed TLI burn values (Artemis II, actual/flight):**
- Burn start: ~7:49 p.m. EDT, Flight Day 2 (April 2, 2026)
- Burn duration: 5 minutes 50 seconds
- Orion mass at time of burn: ~58,000 lb
- Propellant consumed during burn: ~1,000 lb
- Engine thrust: up to ~6,700 lb
- Resulting Δv: ~1,274 ft/s (secondary source — Starlust; flag `TO BE CROSS-CHECKED` against a NASA-published Δv figure before use, since it wasn't in the NASA blog post itself)

Status: TLI mass/duration/propellant/thrust figures are `CONFIRMED — OFFICIAL` (NASA.gov mission blog). The Δv figure is `UNVERIFIED` pending a NASA-sourced number.

---

## 4. Propulsion system-boundary map (for the "don't compare apples to oranges" rule)

```
MISSION PROPULSION
    │
    ├── Launch vehicle (Saturn V S-IC/S-II/S-IVB for Apollo; SLS core+boosters+ICPS for Artemis II)
    │
    ├── Upper stage / TLI stage (S-IVB restart for Apollo; ICPS for Artemis II)
    │
    ├── Spacecraft main engine (SPS for Apollo CSM; Orion European Service Module main engine for Artemis II)
    │
    └── RCS / auxiliary propulsion (CSM RCS, LM RCS/DPS for Apollo; Orion RCS for Artemis II)
```

Every number pulled into the comparison table gets tagged with which box it came from. The Apollo 11 SPS total (35,739 lb consumed) and the Artemis II TLI figure (~1,000 lb) sit in *different* boxes (SPS = whole-mission service-propulsion consumption; the Artemis figure = fuel for one specific burn on the spacecraft main engine), so they will be presented separately, never as a single efficiency ratio, per your instruction.

---

## 5. What's ready vs. what Stage 2 needs to do

**Ready to ingest now (confirmed, official, numeric):**
- Artemis II flight-derived ephemeris (ZIP/OEM) — full trajectory reconstruction
- Artemis II TLI burn actuals (mass, duration, propellant, thrust)
- Apollo 11 SPS propellant loaded/consumed/remaining table (pending page-level re-verification)

**Needs PDF/document extraction in Stage 2 before it's usable:**
- Apollo 11 Flight Plan planned propellant budget per maneuver (TLI correction, LOI, LOPC, TEI)
- Apollo 11 Mission Report's own trajectory/maneuver tables (TLI conditions, actual Δv per maneuver)
- Apollo 13 Mission Report's maneuver table (exact Δv, burn duration, timestamps, propellant used for all four post-abort burns)
- Apollo 13 closest-approach-before-vs-after-restoration-burn figures (69 mi → 156 mi) — need a primary citation
- Artemis II Δv for TLI (currently only sourced from a secondary outlet)

**Explicitly out of scope / will not be estimated:**
- Any propellant or Δv figure we can't trace to a NASA document, report, or NASA-published mission-day update. If Stage 2 can't find a number in a primary source, that cell in the final comparison table stays blank rather than being calculated from the rocket equation or backed into from other numbers — matching your "no dummy values" rule.

---

## 6. Proposed Stage 2 task list
1. Download and parse the Artemis II OEM ephemeris ZIP; confirm format (CCSDS OEM) and build the ingestion function.
2. Pull the Apollo 11 Mission Report PDF and the Flight Plan PDF; locate and extract the specific maneuver/propellant tables (page references recorded).
3. Pull the Apollo 13 Mission Report PDF and the Goodman NTRS paper; extract the four-maneuver table with timestamps, Δv, and duration.
4. Build the normalized schema (original_value, original_unit, normalized_value, normalized_unit, source_doc, page/section, retrieval_date) described in your pipeline.
5. Flag anything that can't be found in a primary source and leave it blank rather than estimating it.

No analysis or chart code should be written until step 2 above is done, since that's what determines which cells in the final comparison table can be filled with real data.