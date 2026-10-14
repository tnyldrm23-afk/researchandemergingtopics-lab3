# AIDAMS Lab 3 — Bottom-Up Steel Emissions with Uncertainty

Bottom-up estimate of China's iron & steel CO2 emissions from plant-level data
(Global Energy Monitor, Global Iron and Steel Tracker), plus an uncertainty
quantification of that estimate (coverage check, bootstrap, Monte-Carlo
simulation, scenarios).

**Group:** `aidams-lab3-yildirim-oktay`

| Name | Student ID |
|---|---|
| Tan YILDIRIM | B00822334 |
| Can OKTAY | B00818187 |

## Contents

| File | What it is |
|---|---|
| `lab3.ipynb` | The notebook, all cells run, outputs visible |
| `Plant-level_data_Global_Iron_and_Steel_Tracker_June_2026_V1.xlsx` | Input dataset (see note below) |
| `requirements.txt` | Python packages needed |

## Data

The notebook reads three sheets — `Plant data`, `Plant capacities and status`,
`Plant production` — from the Global Energy Monitor **Global Iron and Steel
Tracker** workbook.

> **Note on the release.** The lab handout names the *September 2025 (V1)*
> workbook. Global Energy Monitor no longer distributes that file, so this
> notebook runs on the **June 2026 (V1)** release, which has the same three
> sheets. Two consequences, both handled in the code and flagged in the answers:
>
> * some columns are spelled differently (`GEM plant ID`, `Country/area`,
>   `Parent (English)`) — they are renamed once at the top of task A1;
> * the units in `Main production equipment` are listed in a different order
>   (`DRI; EAF` instead of `EAF; DRI`), so `map_emission_factor` falls back to a
>   match on the *set* of units before applying the 1.6 default.
>
> Numbers therefore differ from the figures quoted in the handout (e.g. the naive
> BF-BOF utilisation falls from 14.0% to 4.2% between 2020 and 2021, where the
> handout quotes 57% to 8%). The qualitative result is identical.

The setup cell picks up whichever GIST workbook sits next to the notebook. To
point it at a different release instead:

```bash
export GIST_PATH="/path/to/Plant-level-data-Global-Iron-and-Steel-Tracker-<release>.xlsx"
```

## How to run

Python 3.10+ is required (developed on 3.12).

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook lab3.ipynb
```

Then run **Kernel → Restart & Run All**. The whole notebook executes in well
under a minute.

To re-execute it without opening Jupyter:

```bash
jupyter nbconvert --to notebook --execute --inplace lab3.ipynb
```

Everything is seeded (`SEED = 42`), so the bootstrap and the Monte-Carlo results
reproduce exactly from one run to the next.

## Structure

**Part A — emissions pipeline.** A1 operating plants 2020-2030 · A2 China
production and capacity · A3 utilisation by technology · A4 plant-level
production · A5 emission factors and plant emissions · A6 company emissions ·
A7 projection to 2030. Questions Q1-Q3.

**Part B — uncertainty.** B0 kinds of uncertainty (Q4) · B1 reality check
against worldsteel (Q5) · B2 matched utilisation (Q6) · B3 bootstrap intervals
(Q7, Q7b) · B4 Monte-Carlo simulation (Q8-Q10) · B5 calibration test (Q11) ·
B6 scenarios (Q12) · B7 communication (Q13).

## Headline results

* The Part A pipeline covers only **9.5%** of China's official 2023 crude steel
  production, because production is reported by ~10% of plants but divided by the
  capacity of all of them — it under-estimates emissions by a factor of **13**.
* Using a **matched** utilisation (same plants in numerator and denominator) gives
  0.766 for BF-BOF in 2023, against 0.736 implied by the official statistics.
* Simulated 2030 emissions: median **1,984 MtCO2**, 90% interval
  **1,762-2,243 MtCO2**. The **emission factor**, not utilisation, drives the
  uncertainty (11.3% vs 4.2% relative half-width).
* The interval **fails** its calibration test — official 2023 production falls
  just outside it — because the model ignores structural error (21-29% of the
  "operating" capacity is not operating).
* Retiring 30% of BF-BOF capacity moves the median by **574 MtCO2**, more than the
  full width of the 90% interval; a 10% demand drop (198 MtCO2) does not.

## Sources

* Global Energy Monitor, *Global Iron and Steel Tracker*, June 2026 (V1) release.
* World Steel Association, *World Steel in Figures 2024 / 2025* — China crude
  steel production: 1,064.8 Mt (2020), 1,035.2 (2021), 1,018.0 (2022), 1,019.1
  (2023).
* Hasanbeigi et al. (2019) — emission factors by production route.
