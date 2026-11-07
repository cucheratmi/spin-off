---
name: "rob2-itt"
description: "Assess risk of bias of a parallel-group RCT with Cochrane RoB 2.0 (ITT effect) from uploaded article and supplement PDFs; sourced detailed report + JSON via a Python algorithm."
metadata:
  author: "Michel Cucherat and Claude"
  version: "1.0.0"
  url: "https://github.com/cucheratmi/spin-off"
---

# RoB 2.0 - effect of assignment to intervention (ITT) - parallel-group randomized trial

This skill assesses the risk of bias of **one result** of an individually randomized parallel-group trial with the Cochrane **RoB 2.0** tool (cribsheet of 22 August 2019), from the uploaded PDFs (article, supplementary appendix, protocol, SAP, etc.). All outputs are in **English**.

Strict scope:

- **Only the 5 risk-of-bias domains** + the overall judgement. The form's "Preliminary considerations" (study design, effect of interest, list of sources obtained…) are **not** completed.
- **Domain 2: only the "effect of assignment to intervention" (intention-to-treat effect) version.** Never use the "effect of adhering to intervention" version.
- Judgements are computed **by the Python script** below (official algorithms), never by hand.
- Deliverables: `rob2_report_<TRIAL>.md` (detailed report) and `rob2_result_<TRIAL>.json`.

## Workflow

### 1. Extract the PDF text with page markers

For each uploaded PDF, extract text page by page so that pages can be cited:

```bash
for f in /path/to/uploads/*.pdf; do
  n=$(pdfinfo "$f" | awk '/^Pages:/{print $2}')
  out="$(basename "${f%.pdf}").txt"; : > "$out"
  for p in $(seq 1 $n); do echo "===== PAGE $p =====" >> "$out"; pdftotext -layout -f $p -l $p "$f" - >> "$out"; done
done
```

Then read the text files in full (Read/grep). For tables, the CONSORT diagram and figures that extract poorly, **look at the page as an image** (Read with `pages`). Give each document a short reference (A = main article, S = supplementary appendix, P = protocol, SAP = statistical analysis plan…).

Useful searches (grep -i): `random`, `allocat`, `conceal`, `IWRS|IVRS|interactive`, `stratif`, `block`, `blind|mask|open-label`, `placebo`, `intention|ITT|full analysis|modified`, `per.protocol`, `exclu`, `withdr|discontinu|lost to follow|censor`, `missing|imput|sensitivity`, `independent|central review|adjudicat|BICR`, `statistical analysis plan|SAP|amend|protocol version`, `crossover|cross-over|switch|subsequent therap`.

### 2. Identify the result being assessed

RoB 2 assesses a **specific numerical result**. Default: the **primary outcome**, main (ITT) analysis, main comparison. If the user specified nothing and the trial has several primary outcomes or comparisons, ask which one to assess (AskUserQuestion); if the user is unavailable, assess the primary outcome and say so. Record: outcome, comparison, numerical result (e.g. HR 0.62 [95% CI 0.50-0.77]) and its location.

### 3. Answer each signalling question

For each applicable question, choose **Y / PY / PN / N / NI** (see guidance below) with:

- `justification`: explicit rationale linking the facts to the response criteria;
- `sources`: list of `{document, location, quote}` - `location` as precise as possible (page, section, table/figure, paragraph); `quote` = **verbatim** excerpt in the original language (short, 1-3 sentences). Never paraphrase inside `quote`; never invent a quote or a page.

Rules:

- **NI** only if the information is truly absent from all documents; then state in the rationale what was searched for.
- **PY / PN** when a reasonable inference is needed (say so explicitly).
- Respect the filtering: a conditional question that is not triggered is **NA** (do not answer it; the script checks). 3.2 has no NI option.
- Information from one domain must not contaminate answers in another (e.g. 1.3 does not influence 1.1/1.2).
- Optional: predicted direction of bias per domain (`NA`, `Favours experimental`, `Favours comparator`, `Towards null`, `Away from null`, `Unpredictable`).

### 4. Build the input JSON and run the script

Write `rob2_input.json`:

```json
{
  "study": {"title": "...", "acronym": "...", "authors": "...", "journal": "Journal year;vol:pages", "doi": "...", "registration": "NCT...",
            "documents": [{"id": "A", "name": "article.pdf", "type": "Main article"}, {"id": "S", "name": "suppl.pdf", "type": "Supplementary appendix"}]},
  "result_assessed": {"outcome": "...", "comparison": "... vs ...", "result": "HR ... (95% CI ...)", "location": "[A] Figure 2"},
  "answers": {
    "1.1": {"response": "Y", "justification": "...", "sources": [{"document": "A", "location": "p. 3, Methods, Randomization", "quote": "..."}]},
    "...": {}
  },
  "directions": {"D1": "NA", "overall": "Unpredictable"},
  "overrides": {},
  "comments": "Optional general remarks."
}
```

`overrides` (exceptional use only, justification mandatory): `{"D3": {"judgement": "High", "justification": "..."}}`; for the overall judgement, `{"overall": {"judgement": "High", "justification": "..."}}` (only more severe than the algorithm - typically when several "some concerns" domains substantially lower confidence in the result; the script flags this case).

Write the script (section "Script") to the working directory as `rob2_itt.py`, then:

```bash
python3 rob2_itt.py rob2_input.json --out-json rob2_result_<ACRONYM>.json --out-md rob2_report_<ACRONYM>.md
```

Exit code 1 = validation errors (missing response, invalid option, missing justification…): fix the JSON and rerun. Also resolve the WARNINGS (response without source, forced NA) before delivering.

### 5. Check and deliver

- Re-read the report: is each response consistent with its rationale and quotes? Use grep on the extracted text to confirm that each `quote` actually exists (at least a meaningful fragment) on the page cited.
- Put both files in `/mnt/user-data/outputs/` (or the user's connected folder) and deliver them.
- Short final reply: judgement per domain + overall, and the 1-2 points driving the judgement. Do not rewrite the report in the chat.

## Guidance for answering the signalling questions (based on the RoB 2 cribsheet, 22 Aug 2019)

Brackets give the filtering condition.

### Domain 1 - Randomization process

**1.1 Was the allocation sequence random?** Y if a random component was used: computer-generated random numbers, random number table, coin tossing, shuffled cards or envelopes, dice, drawing lots. Minimization is generally considered random. N if no random element or the sequence is predictable: alternation, dates (birth, admission), record numbers, clinician or participant decision, availability of the intervention, any systematic or haphazard method. NI if the only information is that the study is "randomized". PY may be justified (e.g. large trial run by an experienced clinical trials unit, journal with strict word limits).

**1.2 Was the allocation sequence concealed until participants were enrolled and assigned?** Y if a remote or centrally administered method independent of enrolment staff was used (central pharmacy, telephone or web-based randomization - IWRS/IVRS). Y if envelopes were opaque, sequentially numbered, sealed and opened only after irreversible assignment; or drug containers sequentially numbered, identical in appearance, dispensed only after irreversible assignment. N if there is reason to suspect the enrolling investigator or participant knew the forthcoming allocation. PY/PN judgement often needed.

**1.3 Did baseline differences suggest a problem with the randomization process?** Differences compatible with chance do not cause bias; a few "significant" differences at the 5% level are compatible with chance. N if no imbalance or imbalances compatible with chance. Y if: (1) substantial differences in group sizes relative to the intended allocation ratio; (2) substantial excess of significant baseline differences beyond chance; (3) imbalance in a key prognostic factor or baseline outcome measure that is very unlikely due to chance and large enough to bias the estimate; (4) excessive similarity incompatible with chance. NI if no useful baseline information (e.g. only characteristics of analysed participants). Do not change 1.1/1.2 because of 1.3.

### Domain 2 - Deviations from intended interventions (effect of assignment)

**2.1 Were participants aware of their assigned intervention?** Placebo/sham → usually N/PN. If participants experienced side effects or toxicities they knew to be specific to one arm → Y/PY. Open-label → Y.

**2.2 Were carers / people delivering the interventions aware?** Same logic; if allocation was not concealed, they were likely aware.

**2.3 [If Y/PY/NI to 2.1 or 2.2] Deviations that arose because of the trial context?** "Trial context" = effects of recruitment/engagement that make the protocol be implemented in ways that would not happen outside the trial (e.g. control participants feeling unlucky and seeking the experimental intervention or other treatments). Y/PY **only** if there is evidence or strong reason to believe the trial context led to failure to implement protocol interventions or to non-protocol interventions. N/PN for protocol-inconsistent deviations that could also occur outside the trial (e.g. non-adherence), and for protocol-consistent changes (stopping for acute toxicity, treating consequences of an intervention). Unblinding through specific side effects: Y/PY only if it led to protocol-inconsistent deviations arising from the trial context. NI is common (not reported).

**2.4 [If Y/PY to 2.3] Were these deviations likely to have affected the outcome?** They affect the estimate only if they affect the outcome.

**2.5 [If Y/PY/NI to 2.4] Were these deviations balanced between groups?** More impact if unbalanced. (Here Y is favourable.)

**2.6 Was an appropriate analysis used to estimate the effect of assignment?** Appropriate: ITT, and modified ITT excluding participants with missing outcome data; post-randomization exclusion of ineligible participants when eligibility was confirmed only after randomization and could not be influenced by group assignment. Inappropriate: naïve per-protocol (excluding participants who did not receive their assigned intervention), "as treated" (grouped by intervention received), post-randomization exclusion of eligible participants.

**2.7 [If N/PN/NI to 2.6] Potential for substantial impact of failing to analyse participants in their randomized group?** Is the number of misclassified/excluded participants enough to change the result substantially? No fixed rule: substantial impact possible even with < 5% if the outcome is rare or exclusions are strongly related to prognosis.

### Domain 3 - Missing outcome data

**3.1 Data available for all or nearly all randomized participants?** Reference population = all randomized. "Nearly all" = missing data few enough that their values, whatever they were, could not importantly change the estimate. Continuous: ≥ 95% often sufficient. Dichotomous: depends on event risk; if observed events greatly outnumber missing participants, bias is necessarily small. Imputed data count as missing. Time-to-event: participants censored early (withdrawal, lost to follow-up) have missing data even if CONSORT shows them as analysed. NI only if no information on the extent of missing data.

**3.2 [If N/PN/NI to 3.1] Evidence that the result was not biased by missing data?** Yes if bias-correcting methods, or sensitivity analyses showing little change under a range of plausible assumptions about missingness vs true value. LOCF, or multiple imputation based only on group, should not be assumed to correct bias. (No NI option.)

**3.3 [If N/PN to 3.2] Could missingness depend on its true value?** Possible if loss to follow-up/withdrawal could relate to health status. Low if all missing data have documented reasons unrelated to the outcome (device failure, interruption of routine data collection).

**3.4 [If Y/PY/NI to 3.3] Is it likely that missingness depended on its true value?** Y if: (1) proportions of missing data (or censoring rates) differ between groups; (2) reported reasons indicate a link with the true value; (3) reasons differ between groups; (4) trial circumstances make it likely; (5) in time-to-event analyses, censoring when participants stop or change assigned intervention (toxicity, switch to second-line therapy in oncology). N if the analysis accounted for participant characteristics likely to explain the relationship between missingness and true value.

### Domain 4 - Measurement of the outcome

**4.1 Was the method of measuring the outcome inappropriate?** Concerns the measurement method, not the choice of outcome (surrogate). Usually N/PN for a pre-specified outcome. Y/PY if insensitive to plausible effects (values outside detectable range) or instrument with demonstrated poor validity.

**4.2 Could measurement or ascertainment have differed between groups?** Same methods, thresholds and time points? Y/PY with diagnostic detection bias (passive data collection) or if one intervention involves extra visits, hence more opportunities to detect events.

**4.3 [If N/PN/NI to 4.1 and 4.2] Were outcome assessors aware of the intervention received?** N if assessors blinded. For participant-reported outcomes, the assessor is the participant (linked to 2.1).

**4.4 [If Y/PY/NI to 4.3] Could assessment have been influenced by this knowledge?** Possible for participant-reported outcomes (pain), observer-reported outcomes involving judgement, intervention-provider decision outcomes. Unlikely for outcomes without judgement (all-cause mortality).

**4.5 [If Y/PY/NI to 4.4] Is it likely that assessment was influenced?** Distinguishes "could" (some concerns) from "likely" (high risk); more likely when strong beliefs exist about benefit or harm (e.g. self-reported symptoms in homeopathy trials, recovery of function assessed by the physiotherapist who delivered the intervention).

### Domain 5 - Selection of the reported result

**5.1 Analysed according to a pre-specified plan finalized before unblinded outcome data were available?** Compare protocol/SAP/registry with the publication. Changes made before unblinded data were available, or clearly unrelated to the results, raise no concern. Check dates and versions of protocol and SAP.

**5.2 Result selected from multiple eligible outcome measurements (scales, definitions, time points)?** Y/PY if clear (protocol/SAP) that the domain was measured in multiple eligible ways but only some are fully reported without justification, likely on the basis of the results. N/PN if all eligible reported results correspond to all intended measurements, or only one way of measuring exists, or inconsistencies across reports are explained and unrelated to results. NI if analysis intentions are unavailable or insufficiently detailed and more than one measurement was possible.

**5.3 Result selected from multiple eligible analyses of the data?** (unadjusted vs adjusted models, final value vs change vs ANCOVA, transformations, composite-outcome definitions, dichotomization cut-points, covariate sets, missing-data strategies). Same Y/PY, N/PN, NI rules as 5.2.

## Algorithms implemented by the script

- **D1**: 1.2 N/PN → high. 1.2 Y/PY: 1.1 N/PN → some concerns; otherwise 1.3 Y/PY → some concerns, 1.3 N/PN/NI → low. 1.2 NI: 1.3 Y/PY → high, otherwise some concerns.
- **D2**: part 1 - 2.1 and 2.2 N/PN → low; otherwise 2.3 N/PN → low, 2.3 NI → some concerns, 2.3 Y/PY → (2.4 N/PN → some concerns; 2.4 Y/PY/NI → 2.5 Y/PY → some concerns, 2.5 N/PN/NI → high). Part 2 - 2.6 Y/PY → low; otherwise 2.7 N/PN → some concerns, 2.7 Y/PY/NI → high. Domain = worse of the two parts.
- **D3**: 3.1 Y/PY → low; 3.2 Y/PY → low; 3.3 N/PN → low; 3.4 N/PN → some concerns; 3.4 Y/PY/NI → high.
- **D4**: 4.1 Y/PY → high; 4.2 Y/PY → high. 4.2 N/PN: 4.3 N/PN or 4.4 N/PN → low; 4.5 N/PN → some concerns; 4.5 Y/PY/NI → high. 4.2 NI: 4.3 N/PN or 4.4 N/PN or 4.5 N/PN → some concerns; 4.5 Y/PY/NI → high.
- **D5**: 5.2 or 5.3 Y/PY → high. 5.2 and 5.3 N/PN: 5.1 Y/PY → low, otherwise some concerns. At least one NI, neither Y/PY → some concerns.
- **Overall**: ≥ 1 domain high → high; all low → low; otherwise some concerns. If ≥ 2 domains have "some concerns", the script flags it: the assessor may judge overall risk high (justified `overall` override) if these concerns substantially lower confidence in the result.

## Script

Write exactly this content to `rob2_itt.py`:

```python
#!/usr/bin/env python3
"""
RoB 2.0 (Cochrane, version of 22 August 2019) - individually randomized parallel-group trial,
effect of assignment to intervention (the 'intention-to-treat' effect).

Usage:
    python3 rob2_itt.py assessment.json --out-json rob2_result.json --out-md rob2_report.md

The input file contains the answers to the signalling questions (with rationale and
sources). The script:
  1. checks response options and the conditional (filtering) logic / NA;
  2. applies the RoB 2 algorithm of each domain and the overall judgement;
  3. writes a structured JSON file and a detailed Markdown report.
Exit code: 0 = OK, 1 = validation errors (nothing written), 2 = usage error.
"""
import argparse
import datetime as _dt
import json
import sys

YES = {"Y", "PY"}
NO = {"N", "PN"}
NI = "NI"
NA = "NA"
ALL5 = ["Y", "PY", "PN", "N", "NI"]

LABEL_RESP = {
    "Y": "Yes (Y)", "PY": "Probably yes (PY)", "PN": "Probably no (PN)",
    "N": "No (N)", "NI": "No information (NI)", "NA": "Not applicable (NA)",
}
LOW, SOME, HIGH = "Low", "Some concerns", "High"
RANK = {LOW: 0, SOME: 1, HIGH: 2}
LABEL_JUDG = {LOW: "Low risk", SOME: "Some concerns", HIGH: "High risk"}
EMOJI = {LOW: "🟢", SOME: "🟡", HIGH: "🔴"}
DIRECTIONS = ["NA", "Favours experimental", "Favours comparator", "Towards null",
              "Away from null", "Unpredictable"]

# ---------------------------------------------------------------------------
# Signalling questions
#   options : allowed responses when the question is applicable
#   cond    : function(answers) -> True if the question is applicable
# ---------------------------------------------------------------------------
def r(a, q):
    return a.get(q)

DOMAINS = [
    {
        "id": "D1",
        "name": "Domain 1 - Risk of bias arising from the randomization process",
        "questions": [
            ("1.1", "Was the allocation sequence random?", ALL5, None),
            ("1.2", "Was the allocation sequence concealed until participants were enrolled and assigned to interventions?", ALL5, None),
            ("1.3", "Did baseline differences between intervention groups suggest a problem with the randomization process?", ALL5, None),
        ],
    },
    {
        "id": "D2",
        "name": "Domain 2 - Risk of bias due to deviations from the intended interventions (effect of assignment to intervention)",
        "questions": [
            ("2.1", "Were participants aware of their assigned intervention during the trial?", ALL5, None),
            ("2.2", "Were carers and people delivering the interventions aware of participants' assigned intervention during the trial?", ALL5, None),
            ("2.3", "[If Y/PY/NI to 2.1 or 2.2] Were there deviations from the intended intervention that arose because of the trial context?",
             ALL5, lambda a: r(a, "2.1") in YES | {NI} or r(a, "2.2") in YES | {NI}),
            ("2.4", "[If Y/PY to 2.3] Were these deviations likely to have affected the outcome?",
             ALL5, lambda a: r(a, "2.3") in YES),
            ("2.5", "[If Y/PY/NI to 2.4] Were these deviations from intended intervention balanced between groups?",
             ALL5, lambda a: r(a, "2.4") in YES | {NI}),
            ("2.6", "Was an appropriate analysis used to estimate the effect of assignment to intervention?", ALL5, None),
            ("2.7", "[If N/PN/NI to 2.6] Was there potential for a substantial impact (on the result) of the failure to analyse participants in the group to which they were randomized?",
             ALL5, lambda a: r(a, "2.6") in NO | {NI}),
        ],
    },
    {
        "id": "D3",
        "name": "Domain 3 - Risk of bias due to missing outcome data",
        "questions": [
            ("3.1", "Were data for this outcome available for all, or nearly all, participants randomized?", ALL5, None),
            ("3.2", "[If N/PN/NI to 3.1] Is there evidence that the result was not biased by missing outcome data?",
             ["Y", "PY", "PN", "N"], lambda a: r(a, "3.1") in NO | {NI}),
            ("3.3", "[If N/PN to 3.2] Could missingness in the outcome depend on its true value?",
             ALL5, lambda a: r(a, "3.2") in NO),
            ("3.4", "[If Y/PY/NI to 3.3] Is it likely that missingness in the outcome depended on its true value?",
             ALL5, lambda a: r(a, "3.3") in YES | {NI}),
        ],
    },
    {
        "id": "D4",
        "name": "Domain 4 - Risk of bias in measurement of the outcome",
        "questions": [
            ("4.1", "Was the method of measuring the outcome inappropriate?", ALL5, None),
            ("4.2", "Could measurement or ascertainment of the outcome have differed between intervention groups?", ALL5, None),
            ("4.3", "[If N/PN/NI to 4.1 and 4.2] Were outcome assessors aware of the intervention received by study participants?",
             ALL5, lambda a: r(a, "4.1") in NO | {NI} and r(a, "4.2") in NO | {NI}),
            ("4.4", "[If Y/PY/NI to 4.3] Could assessment of the outcome have been influenced by knowledge of intervention received?",
             ALL5, lambda a: r(a, "4.3") in YES | {NI}),
            ("4.5", "[If Y/PY/NI to 4.4] Is it likely that assessment of the outcome was influenced by knowledge of intervention received?",
             ALL5, lambda a: r(a, "4.4") in YES | {NI}),
        ],
    },
    {
        "id": "D5",
        "name": "Domain 5 - Risk of bias in selection of the reported result",
        "questions": [
            ("5.1", "Were the data that produced this result analysed in accordance with a pre-specified analysis plan that was finalized before unblinded outcome data were available for analysis?", ALL5, None),
            ("5.2", "Is the numerical result being assessed likely to have been selected, on the basis of the results, from multiple eligible outcome measurements (e.g. scales, definitions, time points) within the outcome domain?", ALL5, None),
            ("5.3", "Is the numerical result being assessed likely to have been selected, on the basis of the results, from multiple eligible analyses of the data?", ALL5, None),
        ],
    },
]

# ---------------------------------------------------------------------------
# Algorithms (RoB 2 cribsheet figures, 22 Aug 2019)
# ---------------------------------------------------------------------------
def algo_d1(a):
    q11, q12, q13 = a["1.1"], a["1.2"], a["1.3"]
    if q12 in NO:
        return HIGH, "1.2 = N/PN (allocation not concealed) → high risk."
    if q12 in YES:
        if q11 in NO:
            return SOME, "1.2 = Y/PY but 1.1 = N/PN (non-random sequence) → some concerns."
        if q13 in YES:
            return SOME, "1.2 = Y/PY, 1.1 = Y/PY/NI, 1.3 = Y/PY (baseline imbalances suggest a problem) → some concerns."
        return LOW, "1.2 = Y/PY, 1.1 = Y/PY/NI, 1.3 = N/PN/NI → low risk."
    # q12 == NI
    if q13 in YES:
        return HIGH, "1.2 = NI and 1.3 = Y/PY (baseline imbalances suggest a problem) → high risk."
    return SOME, "1.2 = NI and 1.3 = N/PN/NI → some concerns."


def algo_d2(a):
    # Part 1: questions 2.1 to 2.5
    if a["2.1"] in NO and a["2.2"] in NO:
        p1, e1 = LOW, "2.1 and 2.2 = N/PN (participants and personnel blinded) → part 1: low risk."
    elif a["2.3"] in NO:
        p1, e1 = LOW, "2.1 or 2.2 = Y/PY/NI; 2.3 = N/PN (no deviations arising from the trial context) → part 1: low risk."
    elif a["2.3"] == NI:
        p1, e1 = SOME, "2.1 or 2.2 = Y/PY/NI; 2.3 = NI → part 1: some concerns."
    elif a["2.4"] in NO:
        p1, e1 = SOME, "2.3 = Y/PY; 2.4 = N/PN (deviations unlikely to affect the outcome) → part 1: some concerns."
    elif a["2.5"] in YES:
        p1, e1 = SOME, "2.3 = Y/PY; 2.4 = Y/PY/NI; 2.5 = Y/PY (deviations balanced) → part 1: some concerns."
    else:
        p1, e1 = HIGH, "2.3 = Y/PY; 2.4 = Y/PY/NI; 2.5 = N/PN/NI (deviations not balanced) → part 1: high risk."
    # Part 2: questions 2.6 and 2.7
    if a["2.6"] in YES:
        p2, e2 = LOW, "2.6 = Y/PY (appropriate analysis) → part 2: low risk."
    elif a["2.7"] in NO:
        p2, e2 = SOME, "2.6 = N/PN/NI; 2.7 = N/PN (no substantial impact) → part 2: some concerns."
    else:
        p2, e2 = HIGH, "2.6 = N/PN/NI; 2.7 = Y/PY/NI → part 2: high risk."
    j = max(p1, p2, key=lambda x: RANK[x])
    return j, f"{e1} {e2} Domain judgement = worse of the two parts → {LABEL_JUDG[j].lower()}.", {"part1": p1, "part2": p2}


def algo_d3(a):
    if a["3.1"] in YES:
        return LOW, "3.1 = Y/PY (data available for (nearly) all) → low risk."
    if a["3.2"] in YES:
        return LOW, "3.1 = N/PN/NI; 3.2 = Y/PY (evidence of no bias) → low risk."
    if a["3.3"] in NO:
        return LOW, "3.2 = N/PN; 3.3 = N/PN (missingness could not depend on true value) → low risk."
    if a["3.4"] in NO:
        return SOME, "3.3 = Y/PY/NI; 3.4 = N/PN → some concerns."
    return HIGH, "3.3 = Y/PY/NI; 3.4 = Y/PY/NI (missingness likely depended on true value) → high risk."


def algo_d4(a):
    if a["4.1"] in YES:
        return HIGH, "4.1 = Y/PY (inappropriate measurement method) → high risk."
    if a["4.2"] in YES:
        return HIGH, "4.2 = Y/PY (measurement differed between groups) → high risk."
    base = LOW if a["4.2"] in NO else SOME
    pre = "4.1 = N/PN/NI; 4.2 = N/PN" if base == LOW else "4.1 = N/PN/NI; 4.2 = NI"
    if a["4.3"] in NO:
        return base, f"{pre}; 4.3 = N/PN (assessors blinded) → {LABEL_JUDG[base].lower()}."
    if a["4.4"] in NO:
        return base, f"{pre}; 4.3 = Y/PY/NI; 4.4 = N/PN (assessment could not be influenced) → {LABEL_JUDG[base].lower()}."
    if a["4.5"] in NO:
        return SOME, f"{pre}; 4.4 = Y/PY/NI; 4.5 = N/PN → some concerns."
    return HIGH, f"{pre}; 4.4 = Y/PY/NI; 4.5 = Y/PY/NI (assessment likely influenced) → high risk."


def algo_d5(a):
    q51, q52, q53 = a["5.1"], a["5.2"], a["5.3"]
    if q52 in YES or q53 in YES:
        return HIGH, "5.2 or 5.3 = Y/PY (result likely selected) → high risk."
    if q52 in NO and q53 in NO:
        if q51 in YES:
            return LOW, "5.2 and 5.3 = N/PN; 5.1 = Y/PY (analysed according to pre-specified plan) → low risk."
        return SOME, "5.2 and 5.3 = N/PN; 5.1 = N/PN/NI → some concerns."
    return SOME, "At least one NI in 5.2/5.3, neither Y/PY → some concerns."


ALGOS = {"D1": algo_d1, "D2": algo_d2, "D3": algo_d3, "D4": algo_d4, "D5": algo_d5}

# ---------------------------------------------------------------------------
def norm_resp(x):
    if x is None:
        return None
    s = str(x).strip().upper().replace(" ", "")
    return {"YES": "Y", "NO": "N", "PROBABLYYES": "PY", "PROBABLYNO": "PN",
            "NOINFORMATION": "NI", "NOTAPPLICABLE": "NA"}.get(s, s)


def validate_and_resolve(data):
    errors, warnings = [], []
    raw = data.get("answers", {})
    resolved = {}      # qid -> response (incl. NA)
    details = {}       # qid -> dict
    known = {q[0] for d in DOMAINS for q in d["questions"]}
    for k in raw:
        if k not in known:
            warnings.append(f"Unknown question ignored: {k}")
    for d in DOMAINS:
        for qid, text, options, cond in d["questions"]:
            item = raw.get(qid) or {}
            if isinstance(item, str):
                item = {"response": item}
            resp = norm_resp(item.get("response"))
            applicable = True if cond is None else bool(cond(resolved))
            if not applicable:
                if resp not in (None, NA):
                    warnings.append(f"{qid}: not applicable according to the filtering logic; response '{resp}' replaced by NA.")
                resolved[qid] = NA
            else:
                if resp is None:
                    errors.append(f"{qid}: applicable question without a response.")
                    resolved[qid] = None
                elif resp not in options:
                    errors.append(f"{qid}: invalid response '{resp}' (allowed: {'/'.join(options)}).")
                    resolved[qid] = None
                else:
                    resolved[qid] = resp
                    if not str(item.get("justification", "")).strip():
                        errors.append(f"{qid}: missing justification.")
                    srcs = item.get("sources") or []
                    if resp != NI and not srcs:
                        warnings.append(f"{qid}: response '{resp}' without any cited source.")
                    for s in srcs:
                        if not s.get("location"):
                            warnings.append(f"{qid}: a source has no location (page/section/table).")
            details[qid] = {
                "question": text, "applicable": applicable,
                "response": resolved[qid],
                "justification": item.get("justification", "") if applicable else "Not applicable (algorithm filtering).",
                "sources": item.get("sources", []) if applicable else [],
            }
    return resolved, details, errors, warnings


def run(data):
    resolved, details, errors, warnings = validate_and_resolve(data)
    if errors:
        return None, errors, warnings
    overrides = data.get("overrides", {}) or {}
    directions = data.get("directions", {}) or {}
    domains_out = []
    for d in DOMAINS:
        res = ALGOS[d["id"]](resolved)
        algo_j, expl = res[0], res[1]
        extra = res[2] if len(res) > 2 else None
        final_j, ov = algo_j, None
        o = overrides.get(d["id"])
        if o:
            oj = o.get("judgement")
            if oj not in RANK:
                errors.append(f"Override {d['id']}: invalid judgement '{oj}' (Low/Some concerns/High).")
            elif not str(o.get("justification", "")).strip():
                errors.append(f"Override {d['id']}: justification required.")
            elif oj != algo_j:
                final_j, ov = oj, o["justification"]
        dirn = directions.get(d["id"])
        if dirn and dirn not in DIRECTIONS:
            warnings.append(f"Direction {d['id']} '{dirn}' is not a standard option.")
        domains_out.append({
            "id": d["id"], "name": d["name"],
            "signalling_questions": [dict(id=q[0], **details[q[0]]) for q in d["questions"]],
            "algorithm_judgement": algo_j, "algorithm_explanation": expl,
            "algorithm_parts": extra,
            "override_justification": ov, "judgement": final_j,
            "predicted_direction": dirn,
        })
    if errors:
        return None, errors, warnings
    js = [x["judgement"] for x in domains_out]
    n_some = js.count(SOME)
    if HIGH in js:
        oj, oexp = HIGH, "At least one domain at high risk → overall high risk."
    elif n_some == 0:
        oj, oexp = LOW, "Low risk in all domains → overall low risk."
    else:
        oj, oexp = SOME, f"Some concerns in {n_some} domain(s), no domain at high risk → some concerns."
    flag = None
    if oj == SOME and n_some >= 2:
        flag = (f"{n_some} domains with 'some concerns': according to RoB 2, the overall risk may be judged high "
                "if multiple concerns substantially lower confidence in the result. Assessor judgement required.")
    final_o, ovo = oj, None
    o = overrides.get("overall")
    if o:
        if o.get("judgement") not in RANK or not str(o.get("justification", "")).strip():
            return None, ["Overall override: valid judgement and justification required."], warnings
        if RANK[o["judgement"]] < RANK[oj]:
            return None, ["Overall override: the overall judgement cannot be more favourable than the algorithm's."], warnings
        if o["judgement"] != oj:
            final_o, ovo = o["judgement"], o["justification"]
    out = {
        "tool": "Cochrane RoB 2.0 (cribsheet of 22 Aug 2019), individually randomized parallel-group trial",
        "effect_of_interest": "Effect of assignment to intervention (intention-to-treat effect)",
        "generated": _dt.datetime.now().isoformat(timespec="seconds"),
        "study": data.get("study", {}),
        "result_assessed": data.get("result_assessed", {}),
        "domains": domains_out,
        "overall": {
            "algorithm_judgement": oj, "algorithm_explanation": oexp,
            "multiple_some_concerns_flag": flag,
            "override_justification": ovo, "judgement": final_o,
            "predicted_direction": directions.get("overall"),
        },
        "summary": {x["id"]: x["judgement"] for x in domains_out} | {"overall": final_o},
        "comments": data.get("comments", ""),
        "validation_warnings": warnings,
    }
    return out, [], warnings

# ---------------------------------------------------------------------------
def md_escape(s):
    return str(s).replace("|", "\\|").replace("\n", " ")


def render_md(o):
    st, ra = o["study"], o["result_assessed"]
    L = []
    L.append("# Risk-of-bias assessment - RoB 2.0 (effect of assignment to intervention)\n")
    L.append(f"**Trial:** {st.get('title', 'N/R')}  ")
    for k, lab in [("acronym", "Acronym"), ("authors", "Authors"), ("journal", "Reference"),
                   ("doi", "DOI"), ("registration", "Registration")]:
        if st.get(k):
            L.append(f"**{lab}:** {st[k]}  ")
    L.append(f"**Tool:** {o['tool']}  ")
    L.append(f"**Assessment date:** {o['generated'][:10]}\n")
    if st.get("documents"):
        L.append("## Documents assessed\n")
        L.append("| Ref. | Document | Type |\n|---|---|---|")
        for doc in st["documents"]:
            L.append(f"| {md_escape(doc.get('id',''))} | {md_escape(doc.get('name',''))} | {md_escape(doc.get('type',''))} |")
        L.append("")
    L.append("## Result assessed\n")
    for k, lab in [("outcome", "Outcome"), ("comparison", "Comparison"),
                   ("result", "Numerical result"), ("location", "Location")]:
        if ra.get(k):
            L.append(f"- **{lab}:** {ra[k]}")
    L.append("")
    L.append("## Summary\n")
    L.append("| Domain | Judgement | Predicted direction of bias |\n|---|---|---|")
    for d in o["domains"]:
        j = d["judgement"]
        star = " *(overridden)*" if d["override_justification"] else ""
        L.append(f"| {d['id']} - {md_escape(d['name'].split(' - ',1)[1])} | {EMOJI[j]} {LABEL_JUDG[j]}{star} | {d['predicted_direction'] or '-'} |")
    ov = o["overall"]
    L.append(f"| **Overall risk of bias** | **{EMOJI[ov['judgement']]} {LABEL_JUDG[ov['judgement']]}** | {ov['predicted_direction'] or '-'} |\n")
    L.append(f"*Rule applied:* {ov['algorithm_explanation']}")
    if ov["multiple_some_concerns_flag"]:
        L.append(f"\n> ⚠️ {ov['multiple_some_concerns_flag']}")
    if ov["override_justification"]:
        L.append(f"\n> **Overall judgement changed by the assessor** ({LABEL_JUDG[ov['algorithm_judgement']]} → {LABEL_JUDG[ov['judgement']]}): {ov['override_justification']}")
    L.append("")
    L.append("## Detailed assessment by domain\n")
    for d in o["domains"]:
        L.append(f"### {d['name']}\n")
        for q in d["signalling_questions"]:
            L.append(f"#### {q['id']} {q['question']}\n")
            L.append(f"**Response: {LABEL_RESP.get(q['response'], q['response'])}**\n")
            if q["applicable"]:
                L.append(f"**Rationale:** {q['justification']}\n")
                if q["sources"]:
                    L.append("**Sources:**\n")
                    for s in q["sources"]:
                        loc = f"[{s.get('document','?')}] {s.get('location','location not specified')}"
                        quote = s.get("quote")
                        L.append(f"- {loc}" + (f": \"{quote}\"" if quote else ""))
                    L.append("")
                elif q["response"] == NI:
                    L.append("**Sources:** no relevant information found in the documents assessed.\n")
            else:
                L.append("*Question not asked (conditional filtering of the algorithm).*\n")
        L.append(f"**Algorithm:** {d['algorithm_explanation']}\n")
        j = d["judgement"]
        if d["override_justification"]:
            L.append(f"**Judgement proposed by the algorithm:** {LABEL_JUDG[d['algorithm_judgement']]}  ")
            L.append(f"**Judgement retained by the assessor:** {EMOJI[j]} **{LABEL_JUDG[j]}** - {d['override_justification']}\n")
        else:
            L.append(f"**Domain judgement: {EMOJI[j]} {LABEL_JUDG[j]}**\n")
        if d["predicted_direction"]:
            L.append(f"**Predicted direction of bias:** {d['predicted_direction']}\n")
    if o.get("comments"):
        L.append("## Assessor comments\n")
        L.append(o["comments"] + "\n")
    if o["validation_warnings"]:
        L.append("## Validation warnings\n")
        for w in o["validation_warnings"]:
            L.append(f"- {w}")
        L.append("")
    L.append("---\n*Report generated by rob2_itt.py - RoB 2 algorithms (Higgins, Savović, Page, Sterne; RoB 2 Development Group, version of 22 August 2019). "
             "Judgements proposed by the algorithm from the signalling-question answers; to be confirmed by a human assessor.*")
    return "\n".join(L)


def main():
    ap = argparse.ArgumentParser(description="RoB 2.0 - ITT effect - judgement algorithm and report")
    ap.add_argument("input")
    ap.add_argument("--out-json", default="rob2_result.json")
    ap.add_argument("--out-md", default="rob2_report.md")
    args = ap.parse_args()
    try:
        with open(args.input, encoding="utf-8") as f:
            data = json.load(f)
    except Exception as e:
        print(f"Cannot read input file: {e}", file=sys.stderr)
        sys.exit(2)
    out, errors, warnings = run(data)
    for w in warnings:
        print(f"WARNING: {w}")
    if errors:
        for e in errors:
            print(f"ERROR: {e}", file=sys.stderr)
        sys.exit(1)
    with open(args.out_json, "w", encoding="utf-8") as f:
        json.dump(out, f, ensure_ascii=False, indent=2)
    with open(args.out_md, "w", encoding="utf-8") as f:
        f.write(render_md(out))
    print("Judgements:", json.dumps(out["summary"]))
    print(f"Written: {args.out_json}, {args.out_md}")


if __name__ == "__main__":
    main()
```