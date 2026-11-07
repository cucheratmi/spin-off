---
name: "robins-i"
description: "Assess risk of bias of a result from a non-randomized (cohort/follow-up) study with ROBINS-I V2 (Nov 2025) from uploaded article and supplement PDFs; sourced report + JSON via a Python algorithm."
metadata:
  author: "Michel Cucherat and Claude"
  version: "1.0.0"
  url: "https://github.com/cucheratmi/spin-off"
---

# ROBINS-I V2 - risk of bias in a non-randomized study of an intervention (follow-up / cohort study)

This skill assesses the risk of bias of **one specific numerical result** of a non-randomized follow-up (cohort) study comparing an intervention strategy with a comparator strategy, using **ROBINS-I V2** (cribsheet of **20 November 2025**), from the PDFs uploaded by the user (article, supplementary appendix, protocol, SAP, registry record...). All outputs are in **English**.

Scope and principles:

- ROBINS-I V2 has **six domains**: D1 confounding, D2 classification of interventions, D3 selection of participants into the study (or analysis), D4 missing data, D5 measurement of the outcome, D6 selection of the reported result. There is **no "deviations from intended interventions" domain**: protocol deviations are handled in D1 Variant B.
- The **preliminary considerations (parts A-D)**, the **screening questions (B1-B3)** and the **evaluation of confounding factors** table are completed, because the domain answers depend on them.
- Judgement scale: **Low** / **Moderate** / **Serious** / **Critical**. For D1 (and overall), "Low" is always **"Low risk of bias (except for concerns about uncontrolled confounding)"**.
- Assessments concern risk of **material** bias (bias large enough to cause an important change in the effect estimate), not any bias.
- Judgements are computed **by the Python script** below (official ROBINS-I V2 algorithms), never by hand. Overrides are allowed only with explicit justification.
- Deliverables: `robins_i_report_<STUDY>.md` (detailed, sourced report) and `robins_i_result_<STUDY>.json` (structured result).

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

Read the text files in full. For tables (baseline characteristics, covariate lists, results tables), the flow chart and figures that extract poorly, **look at the page as an image** (Read with `pages`). Give each document a short reference (A = main article, S = supplementary appendix, P = protocol, SAP = statistical analysis plan, R = registry record...).

Useful searches (grep -i): `confound|adjust|covariat|propensity|IPTW|IPW|weight|matching|match|standardi|stratif|high-dimensional|hdPS|doubly robust`, `new.user|incident user|prevalent|index date|time zero|cohort entry|washout|look-back|lookback|landmark|immortal|grace period|clone|target trial|emulat`, `as.treated|on.treatment|per.protocol|intention|censor|switch|discontinu|augment|time-varying|marginal structural|g-formula|g-method|IPCW`, `exclu|eligib|restrict|flow|survivor|at least .* days`, `missing|complete.case|imput|MICE|MAR`, `code|ICD|ATC|algorithm|validat|misclassif|positive predictive|PPV|sensitivity of`, `outcome|ascertain|adjudicat|blind|mask|registry|linkage|death certificate`, `negative control|falsification|E-value|quantitative bias|tipping|sensitivity analys`, `protocol|pre-?specified|register|EU PAS|ENCePP|HMA-EMA catalogue|OSF|clinicaltrials|statistical analysis plan|post hoc|exploratory`, `subgroup|stratified by|interaction`.

### 2. Preliminary considerations (parts A-D)

**A. Result being assessed.** ROBINS-I assesses one specific numerical result. Default: the **primary outcome, main (adjusted) analysis, main comparison**. If the user specified nothing and several candidate results exist (several outcomes, comparisons, or alternative analyses), ask which one (AskUserQuestion); if the user is unavailable, assess the primary outcome / main adjusted analysis and say so. Record A1 (numerical result, e.g. `HR 0.80 (95% CI 0.70-0.91)`), A2 (location, reason chosen), A3 (outcome).

**C. Target trial specific to the study.** The hypothetical pragmatic randomized trial (ethics and feasibility irrelevant) whose result would equal the study's in the absence of bias, "reverse-engineered" from the analysis actually done (TARGET statement, Cashin 2025; Hernán & Robins 2016). Specify: C1 eligible participants (the eligibility criteria of the study *and analysis*), C2 intervention strategy, C3 comparator strategy, plus start of follow-up (time zero), end of follow-up and outcome. Differences between this target trial and the user's review question are applicability issues, not bias.

**C4 - ITT or per-protocol effect** (drives the D1 variant):
- `C4 = "N"` → the analysis estimates the **intention-to-treat effect** (effect of assignment/initiation, regardless of later changes; follow-up is *not* censored or partitioned because of post-baseline treatment changes; "as-started" / "initial treatment" analyses) → **D1 Variant A** (baseline confounding only).
- `C4 = "Y"` → the analysis accounts for switches or other protocol deviations (follow-up partitioned by treatment received, "as-treated"/"on-treatment" time-varying exposure, censoring at discontinuation, switching or augmentation) → **per-protocol effect** → **D1 Variant B** (baseline + time-varying confounding).
- Stopping for toxicity or moving to second-line therapy after progression are *consistent* with most strategies (not protocol deviations). For point interventions (surgery, single vaccine dose) the distinction is usually irrelevant → C4 = N.

**D. Information sources**: list the documents actually obtained (journal article, supplementary appendix, protocol, SAP, registry record, conference abstract, regulatory document...).

**B. Screening (decide whether to proceed).** Answer after reading the documents:
- **B1** Did the authors make any attempt to control for confounding in the result assessed? (Y/PY/PN/N)
- **B2** [If N/PN to B1] Is there sufficient potential for confounding that this (unadjusted) result should not be considered further? (Y/PY/PN/N)
- **B3** Was the method of measuring the outcome inappropriate? (Y/PY/PN/N) - not about the choice of outcome (surrogate); Y/PY only if (1) important ranges of outcome values fall outside the detectable levels, (2) the instrument has demonstrated such poor reliability/validity that estimates are not useful, or (3) the measurement method differed substantially between groups so that differences are not interpretable. Usually N/PN for a pre-specified outcome.
- If **B2 or B3 = Y/PY → Critical risk of bias**, no further assessment (the script stops; do not answer the domains).

### 3. Evaluation of confounding factors (before Domain 1)

**List of important confounding factors (P1).** Use the list supplied by the user / the review protocol if there is one (→ `prespecified`). Otherwise **propose one from content knowledge** (prognostic factors for the outcome that also predict which strategy is received - indication, severity, comorbidities, frailty, prior treatments, healthcare utilization, socioeconomic factors, calendar time...), put it in `prespecified`, and state in `preliminary.confounders_note` that it was proposed by the assessor and should be validated by a content expert. Add in `additional` the factors relevant to this particular setting or cited by the study authors. Only factors whose omission could cause *material* bias. If C4 = Y, include **time-varying confounders** (prognostic factors that predict post-baseline protocol deviations).

For each factor record:
- `measured_variables`: variable(s) used to measure it, if any ("None" if not measured);
- `controlled`: **Y / N** (controlled for in the analysis by design or analysis: restriction, matching, stratification, regression, standardization, propensity score, IPW);
- `valid_reliable` (if controlled): **NA / Y / PY / PN / N / NI** - do the variables measure the factor accurately (validity) and precisely (reliability)? Claims-based proxies for severity or frailty are often PN;
- `control_unnecessary` (if not controlled): **NA / Y / PY / PN / N** - evidence that control was unnecessary: (a) validly measured and not associated with the outcome conditional on intervention (non-significance ≠ no association); (b) validly measured and not associated with intervention; (c) adjustment makes minimal difference; (d) addressed by design (restriction); (e) a negative control shows minimal confounding; (f) external evidence;
- `direction_if_unadjusted` (optional): `Upward bias` (overestimates the intervention effect) / `Downward bias` / `No information or unpredictable`. Positive confounding (factor associated in the same direction with intervention and outcome) biases upwards; negative confounding biases downwards;
- `comments` and `sources` (document + location).

The D1 answers must be traceable to this table.

### 4. Answer each signalling question

For each applicable question, give the response with:

- `justification`: explicit rationale linking the facts found to the response criteria of the cribsheet (see guidance below);
- `sources`: list of `{document, location, quote}` - `location` as precise as possible (page, section, table/figure, paragraph); `quote` = **verbatim** excerpt in the original language (short, 1-3 sentences). Never paraphrase inside `quote`; never invent a quote or a page.

Rules:

- **Response options differ by question** (Y/PY/PN/N/NI; some add **WN/SN** = weak/strong no or **WY/SY** = weak/strong yes; some have no NI). Use exactly the options listed in the guidance; the script rejects others.
- **NI** only if the information is truly absent from all documents; state in the rationale what was searched for.
- **PY / PN** when a reasonable inference is needed (say so explicitly).
- Respect the filtering: a conditional question that is not triggered is **NA** (leave it out; the script enforces it and warns if you answered it).
- Keep domains separate (e.g. selection issues belong in D3, missing-data exclusions in D4).
- Optional predicted direction of bias per domain: D1 `Upward bias (overestimate the effect)` / `Downward bias (underestimate the effect)` / `Unpredictable`; D2-D6 `Favours intervention` / `Favours comparator` / `Towards null` / `Away from null` / `Unpredictable`; overall: any of these.

### 5. Build the input JSON and run the script

Write `robins_input.json`:

```json
{
  "study": {"title": "...", "acronym": "short name", "authors": "...", "journal": "Journal year;vol:pages", "doi": "...",
            "registration": "EU PAS / NCT / OSF ... or none found", "design": "e.g. active-comparator new-user cohort, national claims database",
            "documents": [{"id": "A", "name": "article.pdf", "type": "Main article"}, {"id": "S", "name": "suppl.pdf", "type": "Supplementary appendix"}]},
  "preliminary": {
    "result": {"A1": "HR 0.80 (95% CI 0.70-0.91)", "A2": "[A] Table 2, primary IPTW-weighted Cox model; primary outcome", "A3": "Hospitalisation for heart failure"},
    "target_trial": {"C1": "...", "C2": "...", "C3": "...", "time_zero": "...", "follow_up_end": "...", "outcome": "...",
                     "C4": "N", "C4_justification": "...", "sources": [{"document": "A", "location": "p. 3, Methods", "quote": "..."}]},
    "information_sources": ["Journal article(s)", "Supplementary appendix"],
    "confounders_note": "No review protocol available: important confounders proposed by the assessor from content knowledge; to be validated."
  },
  "confounding_factors": {
    "prespecified": [{"factor": "Age", "measured_variables": "Age at index date", "controlled": "Y", "valid_reliable": "Y",
                      "control_unnecessary": "NA", "direction_if_unadjusted": "Downward bias", "comments": "...",
                      "sources": [{"document": "S", "location": "Table S2"}]}],
    "additional": []
  },
  "answers": {
    "B1": {"response": "Y", "justification": "...", "sources": [{"document": "A", "location": "p. 4, Statistical analysis", "quote": "..."}]},
    "1.1": {"response": "WN", "justification": "...", "sources": []},
    "...": {}
  },
  "directions": {"D1": "Upward bias (overestimate the effect)", "overall": "Favours intervention"},
  "overrides": {},
  "comments": "Optional general remarks (e.g. key limitations, what information would change the judgement)."
}
```

`overrides` (exceptional use only, justification mandatory): `{"D3": {"judgement": "Serious", "justification": "..."}}` (judgements: `Low`, `Low (except confounding)` [D1 only], `Moderate`, `Serious`, `Critical`). For the overall judgement, `{"overall": {"judgement": "Serious", "justification": "..."}}` - only equal or more severe than the algorithm; typically when **several domains are Moderate** (additive judgement of Serious) or **several are Serious** and likely to compound (additive judgement of Critical). The script flags these situations; decide explicitly and explain your decision in `comments` either way.

Write the script (section "Script") to the working directory as `robins_i_v2.py`, then:

```bash
python3 robins_i_v2.py robins_input.json --out-json robins_i_result_<STUDY>.json --out-md robins_i_report_<STUDY>.md
```

Exit code 1 = validation errors (missing response, invalid option, missing justification, C4 missing...): fix the JSON and rerun. Resolve the WARNINGS (response without source, forced NA) before delivering.

### 6. Check and deliver

- Re-read the report: is each response consistent with its rationale, quotes and the confounder table? grep the extracted text to confirm that each `quote` actually exists on the page cited.
- Put both files in `/mnt/user-data/outputs/` (or the user's connected folder) and deliver them.
- Short final reply: judgement per domain + overall, the 1-3 points driving the judgement, and the main uncertainty (e.g. confounder list proposed by the assessor). Do not rewrite the report in the chat.

## Guidance for answering the signalling questions (ROBINS-I V2 cribsheet, 20 Nov 2025)

Brackets give the filtering condition; the options follow each question.

### Domain 1 - Bias due to confounding

Uncontrolled confounding = confounding not controlled by design or analysis because factors were not (or could not be) measured, were insufficiently characterized by the variables, or were measured but not included. Appropriate methods for baseline confounding: stratification, regression, matching, standardization, IPW (on individual variables or propensity scores). Base the answers on the confounder table.

**Variant A (C4 = N, intention-to-treat effect)**

**1.1 Did the authors control for all the important confounding factors for which this was necessary?** `Y/PY/WN/SN/NI`. Y/PY if all important factors needing control were appropriately controlled (or, very rarely, there are no confounders and an unadjusted analysis is presented). **WN** if most were controlled and uncontrolled confounding was probably not substantial (e.g. omitted factors highly correlated with controlled ones). **SN** if at least one important factor was not controlled and this likely has a material impact.

**1.2 [If Y/PY/WN to 1.1] Were the controlled confounding factors measured validly and reliably by the variables available?** `Y/PY/WN/SN/NI`. Consider cited validation; if none, judge subjectivity of the measure. WN = measurement error probably not substantial; SN = probably substantial.

**1.3 [If Y/PY/WN to 1.1] Did the authors control for any post-intervention variables that could have been affected by the intervention?** `Y/PY/PN/N/NI`. Adjusting for mediators or colliders (variables measured after time zero and affected by treatment) is over-adjustment → Y/PY.

**1.4 Did negative controls, quantitative bias analysis, or other considerations suggest serious uncontrolled confounding?** `Y/PY/PN/N` (no NI). N if no negative control and nothing else suggests it. Y/PY if a negative control outcome/exposure shows an association similar to the result assessed, or other considerations (implausibly large effects, E-value analysis showing fragility plus a plausible strong unmeasured confounder, healthy-user signals) indicate material confounding.

**Variant B (C4 = Y, per-protocol effect)**

**1.1 Did the authors use an analysis method appropriate to control for time-varying as well as baseline confounding?** `Y/PY/PN/N/NI`. Appropriate: g-methods (IPW/IPCW with time-varying weights, marginal structural models, g-formula, g-estimation). Standard regression including time-varying confounders is problematic (treatment-confounder feedback). Censoring at deviation without IPCW → N/PN.

**1.2 [If Y/PY to 1.1] Did the authors control for all the important baseline and time-varying confounding factors for which this was necessary?** `Y/PY/WN/SN/NI` - same criteria as Variant A 1.1, including time-varying prognostic factors that predict deviations.

**1.3 [If Y/PY/WN to 1.2] Were controlled confounding factors measured validly and reliably?** `Y/PY/WN/SN/NI`.

**1.4 [If N/PN/NI to 1.1] Did the authors control for time-varying factors or other variables measured after the start of intervention?** `Y/PY/PN/N/NI`. Conditioning on post-baseline factors with an inappropriate method is likely to bias when they lie on the causal pathway.

**1.5 Did negative controls, or other considerations, suggest serious uncontrolled confounding?** `Y/PY/PN/N` - as Variant A 1.4.

### Domain 2 - Bias in classification of interventions

Questions 2.1-2.3 address **immortal time from misclassification** (strategies not distinguishable at time zero); 2.4 differential and 2.5 non-differential misclassification.

**2.1 Were the intervention strategies distinguishable at the time when follow-up would have started in the target trial?** `Y/PY/PN/N/NI`. Y for new-user designs classifying on treatment initiated at time zero. N when group membership depends on events after time zero (e.g. "surgery within 6 months of diagnosis" vs "delay", "treated at any time during follow-up" vs "never treated", exposure defined by cumulative duration/adherence): the treated group then has immortal time.

**2.2 [If N/PN/NI to 2.1] Did all or nearly all outcome events occur after the strategies could be distinguished?** `Y/PY/PN/N/NI`. Y if the indistinguishable period is short relative to follow-up, so few events occur in it.

**2.3 [If N/PN/NI to 2.2] Did the analysis avoid problems arising from strategies not distinguishable at the start of follow-up?** `SY/WY/PN/N/NI`. **SY** for clone-censor-weight or g-formula with predictors of treatment during follow-up measured and used appropriately for weights; SY also for a landmark analysis (but its selection problem goes to D3). **WY** if such a method was used but prognostic factors predicting treatment after time zero were probably not fully adjusted for. Time-varying exposure (Cox with time-dependent treatment) also avoids immortal time; judge SY/WY accordingly. N/PN if immortal time was misattributed (e.g. time before treatment counted as treated).

**2.4 Was classification of intervention status influenced by knowledge of the outcome or risk of the outcome?** `SY/WY/PN/N/NI`. Differential misclassification: e.g. recall of past exposure after the outcome, exposure information collected after follow-up started, records lost when the participant dies. Less likely when all classification information was recorded at or before time zero (prospective prescription/dispensing records). SY if impact likely substantial, WY if not substantial.

**2.5 Were further classification errors (not influenced by the outcome) likely?** `Y/PY/PN/N/NI`. Non-differential misclassification: intervention not recorded in the data source (over-the-counter drugs, samples, out-of-pocket purchases, hospital drugs missing from outpatient claims), dispensing ≠ taking, ill-defined strategies (type, setting, dose, frequency, timing). Correct classification of ~95% is often sufficient. **Note:** here Y/PY (errors likely) is unfavourable; ambiguous intervention definitions → Y/PY. Usually biases towards the null.

### Domain 3 - Bias in selection of participants into the study (or into the analysis)

Selection bias = conditioning on a common effect of intervention and outcome (e.g. restriction to live births in folic acid studies). This domain covers omission of participants, follow-up periods or events based on characteristics/events **after** the start of intervention; exclusions due to missing data go to D4.

*Part A - prevalent-user bias and immortal time*

**3.1 Did follow-up in the analysis begin at the start of the intervention strategies being compared?** `Y/PY/WN/SN/NI`. Y/PY if all events and follow-up after the start of the strategies were included (new-user design; with a "no intervention" comparator, follow-up may start at any point where participants remain eligible to start). Otherwise (prevalent users, landmark analyses, follow-up starting e.g. 90 days after initiation): **SN** if (i) the effect likely differs markedly between excluded and included periods (e.g. early adverse events, early benefit) or (ii) a substantial proportion of follow-up/outcomes after the start was excluded; **WN** if the risk of bias was not substantial.

**3.2 [If Y/PY to 3.1] Were outcome events during a period of follow-up after the start of the interventions excluded from the analysis?** `Y/PY/PN/N/NI`. E.g. events during the first 6 months excluded, or outcomes not ascertained during an initial period (immortal time by design, "lag"/induction periods), or requirement of ≥2 prescriptions with follow-up starting at the second.

*Part B - other selection*

**3.3 Was selection into the study (or analysis) based on participant characteristics observed after the start of intervention (beyond 3.1-3.2)?** `Y/PY/PN/N/NI`. E.g. requiring survival or continuous enrolment for a period after time zero, restricting to completers, to patients with a follow-up visit, to live births, or post-baseline eligibility criteria. N/PN if selection used only pre-baseline characteristics (then it is a confounding/applicability issue).

**3.4 [If Y/PY/NI to 3.3] Were the post-intervention variables that influenced selection likely to be associated with intervention?** `Y/PY/PN/N/NI`.

**3.5 [If Y/PY to 3.4] Were they likely to be influenced by the outcome or a cause of the outcome?** `Y/PY/PN/N/NI`. Selection bias needs selection related to both intervention and outcome.

*Part C - correction and severity*

**3.6 [If SN to 3.1 or Y/PY to 3.5] Is it likely that the analysis corrected for all the potential selection biases identified?** `Y/PY/PN/N/NI`. Y/PY only if appropriate methods were used (e.g. inverse probability of selection weights; modelling of missing early follow-up of prevalent users with missing-data methods) and their assumptions are likely justified. Impossible when data for a period of follow-up are completely unavailable → usually N/PN.

**3.7 [If N/PN/NI to 3.6] Did sensitivity analyses demonstrate that the likely impact of the potential selection biases was minimal?** `Y/PY/PN/N/NI` (e.g. results unchanged in a new-user or no-lag analysis).

**3.8 [If N/PN/NI to 3.7] Were the potential selection biases sufficiently severe that the result should not be included in a quantitative synthesis?** `Y/PY/PN/N/NI`. Distinguishes Serious from Critical: answer N/PN/NI **unless there is clear evidence that the biases were severe**.

### Domain 4 - Bias due to missing data

Compare with an analysis where all intended data were available. No single acceptable threshold. Assess unless complete data on intervention, outcome and confounders exist for (nearly) all participants. Imputed data count as missing for 4.1-4.3.

**4.1 Were complete data on intervention status available for all, or nearly all, participants?** `Y/PY/PN/N/NI`. **4.2 ... on the outcome?** `Y/PY/PN/N/NI`. **4.3 ... on important confounding variables?** `Y/PY/PN/N/NI`.
"Nearly all" = so few excluded that they could not importantly change the estimate. Continuous outcomes: 95% (possibly 90%) often enough; dichotomous: depends on event risk - if observed events greatly outnumber participants with missing data, bias is necessarily small. Time-to-event: loss to follow-up / administrative censoring vs informative censoring (death as competing event, disenrollment) - consider whether censoring is related to prognosis. Check the flow chart for exclusions due to missing covariates (e.g. BMI, smoking, lab values, HbA1c are frequently missing in EHR/claims). NI only if the reports give no information on the extent of missing data (usually leads to a high risk).

**4.4 [If N/PN/NI to 4.1, 4.2 or 4.3] Is the result based on a complete case analysis?** `Y/PY/PN/N/NI` (restricted to participants with complete data on intervention, outcome and confounders; "missing category" indicators are *not* complete-case - treat under 4.10).

**4.5 [If Y/PY/NI to 4.4] Was exclusion because of missing data likely to be related to the true value of the outcome?** `Y/PY/PN/N/NI`. Y/PY if: (1) proportions excluded for missing outcome differ between intervention groups or confounder levels (for time-to-event: censoring rates depend on intervention group); (2) proportions excluded for missing intervention/confounder data differ between outcome groups; (3) reported reasons show missingness depends on the true outcome or a cause of it; (4) circumstances make it likely (e.g. sicker patients missing visits). N/PN if missingness has documented reasons unrelated to the outcome.

**4.6 [If Y/PY/NI to 4.5] Is the relationship between the outcome and missingness likely to be explained by the variables in the analysis model?** `Y/PY/WN/SN/NI`. Y/PY if all variables plausibly explaining missingness-outcome relationships are in the model. WN = no, but not leading to substantial bias; SN = no, and bias likely substantial. (If a mediator drives missingness, multiple imputation should be used; adjusting for it raises D1 risk.)

**4.7 [If N/PN to 4.4] Was the analysis based on imputing missing values?** `Y/PY/PN/N/NI` (single or multiple imputation).

**4.8 [If Y/PY to 4.7] Is it reasonable to assume data were MAR or MCAR?** `Y/PY/PN/N/NI`. N/PN if there is reason to believe data are MNAR (missingness depends on the unobserved value itself even after accounting for observed data); otherwise Y/PY.

**4.9 [If Y/PY to 4.8] Was imputation performed appropriately?** `Y/PY/WN/SN/NI`. Y/PY for multiple imputation including (i) all predictors of missingness and (ii) all variables of the analysis model (including the outcome and, for survival, the event indicator and cumulative hazard). WN/SN for simple methods (LOCF, mean imputation) - severity depends on the proportion missing (SN = bias not substantially reduced).

**4.10 [If N/PN/NI to 4.7] Was an appropriate alternative method used to correct for bias due to missing data?** `Y/PY/WN/SN/NI`. E.g. inverse probability weighting (validity depends on a correctly specified weighting model), full-information maximum likelihood. Missing-indicator methods are generally not appropriate for confounders (WN/SN).

**4.11 [If PN/N/NI to 4.1-4.3 AND (Y/PY/NI to 4.5 OR WN/SN/NI to 4.9 OR WN/SN/NI to 4.10)] Is there evidence that the result was not biased by missing data?** `Y/PY/PN/N` (no NI). Evidence from (1) analysis methods unbiased under plausible missingness mechanisms, or (2) sensitivity analyses showing little change under plausible assumptions (e.g. tipping-point, delta-adjustment, best/worst case). Similarity between results with and without MI based only on outcome, intervention and confounders is **not** reassurance; weighting is not assumed to correct bias without such evidence.

### Domain 5 - Bias in measurement of the outcome

Non-differential error affects precision (or nothing if systematic); **differential** error (related to intervention received) = detection bias.

**5.1 Could measurement or ascertainment of the outcome have differed between intervention groups?** `Y/PY/PN/N/NI`. Same methods, thresholds and time points? Y/PY with diagnostic detection bias in passive data collection (routine care/claims), or when one intervention implies more visits, tests or monitoring (surveillance bias), e.g. drugs requiring laboratory monitoring, screening-detected outcomes. **Here Y/PY is unfavourable (→ Serious).**

**5.2 [If N/PN/NI to 5.1] Were outcome assessors aware of the intervention received?** `Y/PY/PN/N/NI`. N if assessors were blinded, or unaware without active blinding (e.g. outcome from national death registry or coded hospital discharge data recorded independently of the study question). For participant-reported outcomes the assessor is the participant → usually Y in observational studies.

**5.3 [If Y/PY/NI to 5.2] Could assessment of the outcome have been influenced by knowledge of the intervention received?** `SY/WY/PN/N/NI`. N/PN for outcomes without judgement (all-cause mortality, laboratory values). **WY**: could have been influenced but no reason to believe it was. **SY**: knowledge was likely to influence assessment (strong beliefs/preferences; patient-reported symptoms, e.g. homeopathy; physiotherapist-assessed recovery; clinician-decision outcomes such as hospital admission or diagnosis coding when the treatment is known to raise suspicion of the outcome).

### Domain 6 - Bias in selection of the reported result

Selective outcome reporting (multiple measures/time points), selective analysis reporting (unadjusted vs adjusted, covariate sets, definitions of exposure groups, cut-points, missing-data strategies, composites), selection of a subgroup from a larger cohort. Best evidence: a pre-specified, publicly available analysis plan (registered protocol - EU PAS/HMA-EMA catalogue, ClinicalTrials.gov, OSF - date-stamped before data access) consistent with the report. Otherwise compare Methods with Results.

**6.1 Was the result reported in accordance with an available, pre-determined analysis plan?** `Y/PY/PN/N/NI`. Analysis intentions must be finalized before outcome data were available to the authors. Rarely available for non-randomized studies, so low risk is uncommon. A statement "the protocol was pre-specified" without an accessible document is usually NI/PN.

Is the numerical result likely to have been selected, on the basis of the results, from... [each: If N/PN/NI to 6.1; `Y/PY/PN/N/NI`]

**6.2 ... multiple outcome measurements (scales, definitions, time points) within the outcome domain?** Y/PY if clear evidence (protocol/SAP) that the outcome was measured in multiple ways but only one/a subset is fully reported without justification, likely selected on the results. N/PN if all intended measurements are reported, or only one possible way to measure the outcome (e.g. all-cause death), or inconsistencies across reports are explained and unrelated to results. NI if analysis intentions are unavailable/insufficiently detailed and more than one way was possible.

**6.3 ... multiple analyses of the data?** (unadjusted/adjusted models, covariate sets, final value vs change vs ANCOVA, transformations, dichotomization cut-points, exposure definitions, missing-data strategies.) Same Y/PY, N/PN, NI rules. Several reported sensitivity analyses consistent with the main one argue against selection.

**6.4 ... multiple subgroups?** (large cohorts allow many subgroups or restrictions; unusual subgroup definitions suggest selection.) Y/PY if evidence that several subgroups were analysed but only some reported; N/PN if a date-stamped plan shows all reported subgroup results correspond to intended analyses, or inconsistencies are explained; NI otherwise when subgroups could have been analysed in more than one way. If the result assessed is the whole-cohort result with no subgroup restriction, PN is reasonable.

## Algorithms implemented by the script (cribsheet figures pp. 20, 24, 28, 32, 38, 41, 47-49)

- **Screening**: B2 or B3 = Y/PY → **Critical**, stop.
- **D1 Variant A**: 1.1 SN/NI → 1.4 N/PN Serious, Y/PY Critical. 1.1 Y/PY/WN and 1.3 Y/PY → 1.4 Y/PY Critical; else 1.2 Y/PY Serious, 1.2 WN/SN/NI Critical. 1.1 Y/PY, 1.3 N/PN/NI → 1.2 Y/PY → (1.4 N/PN Low-except-confounding, Y/PY Serious); 1.2 WN → (1.4 N/PN Moderate, Y/PY Serious); 1.2 SN/NI Serious. 1.1 WN, 1.3 N/PN/NI → 1.2 Y/PY/WN → (1.4 N/PN Moderate, Y/PY Serious); 1.2 SN/NI Serious.
- **D1 Variant B**: 1.1 N/PN/NI → 1.4 Y/PY Critical; 1.4 N/PN/NI → 1.5 N/PN Serious, Y/PY Critical. 1.1 Y/PY → 1.2 SN/NI → 1.5 N/PN Serious, Y/PY Critical; 1.3 SN/NI Serious; 1.2 and 1.3 both Y/PY → 1.5 N/PN Low-except-confounding, Y/PY Serious; otherwise (a WN) → 1.5 N/PN Moderate, Y/PY Serious.
- **D2**: starting level 0 if 2.1 Y/PY, or 2.2 Y/PY, or 2.3 SY; level 1 if 2.3 WY/NI; level 2 if 2.3 N/PN. Add 0 for 2.4 N/PN, 1 for WY/NI, 2 for SY; level ≥ 3 → Critical. Then 2.5 N/PN / Y/PY/NI: level 0 → Low / Moderate; level 1 → Moderate / Serious; level 2 → Serious / Critical.
- **D3**: Part A: 3.1 Y/PY → 3.2 N/PN/NI Low, Y/PY Moderate; 3.1 WN/NI Moderate; 3.1 SN Serious. Part B: 3.3 N/PN Low; 3.4 N/PN Low; 3.4 NI Moderate; 3.5 N/PN/NI Moderate; 3.5 Y/PY Serious. Across A and B: all Low → Low; worst Moderate → Moderate; ≥ 1 Serious → 3.6 Y/PY Moderate; 3.7 Y/PY Moderate; 3.8 Y/PY Critical, else Serious.
- **D4**: 4.1-4.3 all Y/PY → Low. Complete case (4.4 Y/PY/NI): 4.5 N/PN Low; 4.6 Y/PY → level 0, WN/NI level 1, SN level 2. Imputation: 4.8 N/PN/NI Serious; 4.9 Y/PY Low, WN/NI level 1, SN level 2. Other method: 4.10 Y/PY Low, WN/NI level 1, SN level 2. Then 4.11 Y/PY / N/PN: level 0 Low / Moderate; level 1 Moderate / Serious; level 2 Serious / Critical.
- **D5**: 5.1 Y/PY Serious. 5.1 N/PN: 5.2 N/PN Low; 5.3 N/PN Low, WY/NI Moderate, SY Serious. 5.1 NI: 5.2 N/PN Moderate; 5.3 SY Serious, otherwise Moderate.
- **D6**: 6.1 Y/PY Low. Otherwise over 6.2-6.4: all N/PN Low; ≥ 1 NI but none Y/PY Moderate; all NI or exactly one Y/PY Serious; two or more Y/PY Critical.
- **Overall**: worst domain judgement (all Low → "Low risk of bias except for concerns about uncontrolled confounding"). Flags: ≥ 2 Moderate (may be judged Serious) and ≥ 2 Serious (may be judged Critical if likely compounded) → assessor decides via `overrides.overall`.

## Script

Write exactly this content to `robins_i_v2.py`:

```python
#!/usr/bin/env python3
"""
ROBINS-I V2 (Risk Of Bias In Non-randomized Studies - of Interventions, Version 2,
cribsheet of 20 November 2025) - follow-up (cohort) studies.

Usage:
    python3 robins_i_v2.py assessment.json --out-json robins_result.json --out-md robins_report.md

The input file contains the preliminary considerations (A-D), the evaluation of
confounding factors and the answers to the signalling questions (with rationale and
sources). The script:
  1. checks response options and the conditional (filtering) logic / NA;
  2. applies the screening rule (part B), selects Domain 1 variant A or B (from C4),
     applies the algorithm of each domain and the overall judgement;
  3. writes a structured JSON file and a detailed Markdown report.
Exit code: 0 = OK, 1 = validation errors (nothing written), 2 = usage error.
"""
import argparse
import datetime as _dt
import json
import sys

# ---------------------------------------------------------------------------
# Response sets
# ---------------------------------------------------------------------------
Y = {"Y", "PY"}
N = {"N", "PN"}
NI, NA = "NI", "NA"
ALL5 = ["Y", "PY", "PN", "N", "NI"]
YN4 = ["Y", "PY", "PN", "N"]                    # no NI option
WSN = ["Y", "PY", "WN", "SN", "NI"]             # weak/strong no
WSY = ["SY", "WY", "PN", "N", "NI"]             # weak/strong yes

LABEL_RESP = {
    "Y": "Yes (Y)", "PY": "Probably yes (PY)", "PN": "Probably no (PN)", "N": "No (N)",
    "NI": "No information (NI)", "NA": "Not applicable (NA)",
    "WN": "Weak no (WN)", "SN": "Strong no (SN)",
    "WY": "Weak yes (WY)", "SY": "Strong yes (SY)",
}

LOWX, LOW, MOD, SER, CRIT = "Low (except confounding)", "Low", "Moderate", "Serious", "Critical"
RANK = {LOWX: 0, LOW: 0, MOD: 1, SER: 2, CRIT: 3}
LABEL_JUDG = {
    LOWX: "Low risk of bias (except for concerns about uncontrolled confounding)",
    LOW: "Low risk of bias", MOD: "Moderate risk of bias",
    SER: "Serious risk of bias", CRIT: "Critical risk of bias",
}
EMOJI = {LOWX: "🟢", LOW: "🟢", MOD: "🟡", SER: "🔴", CRIT: "⚫"}
JUDG_ALIASES = {
    "low": LOW, "low risk": LOW, "low (except confounding)": LOWX, "lowx": LOWX,
    "low except confounding": LOWX, "moderate": MOD, "serious": SER, "critical": CRIT,
}

DIR_CONF = ["Upward bias (overestimate the effect)", "Downward bias (underestimate the effect)", "Unpredictable"]
DIR_STD = ["Favours intervention", "Favours comparator", "Towards null", "Away from null", "Unpredictable"]
DIR_OVERALL = DIR_CONF[:2] + DIR_STD

CONF_CONTROLLED = ["Y", "N"]
CONF_VALID = ["NA", "Y", "PY", "PN", "N", "NI"]
CONF_UNNEC = ["NA", "Y", "PY", "PN", "N"]
CONF_DIR = ["Upward bias", "Downward bias", "No information or unpredictable"]


def r(a, q):
    return a.get(q)


# ---------------------------------------------------------------------------
# Signalling questions: (id, text, options, condition(answers)->bool or None)
# ---------------------------------------------------------------------------
SCREENING = [
    ("B1", "Did the authors make any attempt to control for confounding in the result being assessed?", YN4, None),
    ("B2", "[If N/PN to B1] Is there sufficient potential for confounding that this result should not be considered further?",
     YN4, lambda a: r(a, "B1") in N),
    ("B3", "Was the method of measuring the outcome inappropriate?", YN4, None),
]

D1A = {
    "id": "D1", "variant": "A",
    "name": "Domain 1 - Risk of bias due to confounding (Variant A: intention-to-treat effect, baseline confounding)",
    "directions": DIR_CONF,
    "questions": [
        ("1.1", "Did the authors control for all the important confounding factors for which this was necessary?", WSN, None),
        ("1.2", "[If Y/PY/WN to 1.1] Were confounding factors that were controlled for (and for which control was necessary) measured validly and reliably by the variables available in this study?",
         WSN, lambda a: r(a, "1.1") in Y | {"WN"}),
        ("1.3", "[If Y/PY/WN to 1.1] Did the authors control for any post-intervention variables that could have been affected by the intervention?",
         ALL5, lambda a: r(a, "1.1") in Y | {"WN"}),
        ("1.4", "Did the use of negative controls, quantitative bias analysis, or other considerations, suggest serious uncontrolled confounding?", YN4, None),
    ],
}

D1B = {
    "id": "D1", "variant": "B",
    "name": "Domain 1 - Risk of bias due to confounding (Variant B: per-protocol effect, baseline and time-varying confounding)",
    "directions": DIR_CONF,
    "questions": [
        ("1.1", "Did the authors use an analysis method that was appropriate to control for time-varying as well as baseline confounding?", ALL5, None),
        ("1.2", "[If Y/PY to 1.1] Did the authors control for all the important baseline and time-varying confounding factors for which this was necessary?",
         WSN, lambda a: r(a, "1.1") in Y),
        ("1.3", "[If Y/PY/WN to 1.2] Were confounding factors that were controlled for (and for which control was necessary) measured validly and reliably by the variables available in this study?",
         WSN, lambda a: r(a, "1.2") in Y | {"WN"}),
        ("1.4", "[If N/PN/NI to 1.1] Did the authors control for time-varying factors or other variables measured after the start of intervention?",
         ALL5, lambda a: r(a, "1.1") in N | {NI}),
        ("1.5", "Did the use of negative controls, or other considerations, suggest serious uncontrolled confounding?", YN4, None),
    ],
}

D2 = {
    "id": "D2", "name": "Domain 2 - Risk of bias in classification of interventions", "directions": DIR_STD,
    "questions": [
        ("2.1", "Were the intervention strategies distinguishable at the time when follow-up would have started in the target trial?", ALL5, None),
        ("2.2", "[If N/PN/NI to 2.1] Did all or nearly all outcome events occur after the intervention and comparator strategies could be distinguished?",
         ALL5, lambda a: r(a, "2.1") in N | {NI}),
        ("2.3", "[If N/PN/NI to 2.2] Did the analysis avoid problems arising from intervention strategies that are not distinguishable at the start of follow-up?",
         ["SY", "WY", "PN", "N", "NI"], lambda a: r(a, "2.2") in N | {NI}),
        ("2.4", "Was classification of intervention status influenced by knowledge of the outcome or risk of the outcome?", WSY, None),
        ("2.5", "Were further classification errors (not influenced by knowledge of the outcome or risk of the outcome) likely?", ALL5, None),
    ],
}

D3 = {
    "id": "D3", "name": "Domain 3 - Risk of bias in selection of participants into the study (or into the analysis)", "directions": DIR_STD,
    "questions": [
        ("3.1", "Did follow up in the analysis begin at the start of the intervention strategies being compared?", WSN, None),
        ("3.2", "[If Y/PY to 3.1] Were outcome events during a period of follow-up after the start of the interventions excluded from the analysis?",
         ALL5, lambda a: r(a, "3.1") in Y),
        ("3.3", "Was selection of participants into the study (or into the analysis) based on participant characteristics observed after the start of intervention (additional to the situations addressed in 3.1 and 3.2)?", ALL5, None),
        ("3.4", "[If Y/PY/NI to 3.3] Were the post-intervention variables that influenced selection likely to be associated with intervention?",
         ALL5, lambda a: r(a, "3.3") in Y | {NI}),
        ("3.5", "[If Y/PY to 3.4] Were the post-intervention variables that influenced selection likely to be influenced by the outcome or a cause of the outcome?",
         ALL5, lambda a: r(a, "3.4") in Y),
        ("3.6", "[If SN to 3.1 or Y/PY to 3.5] Is it likely that the analysis corrected for all of the potential selection biases identified above?",
         ALL5, lambda a: r(a, "3.1") == "SN" or r(a, "3.5") in Y),
        ("3.7", "[If N/PN/NI to 3.6] Did sensitivity analyses demonstrate that the likely impact of the potential selection biases identified above was minimal?",
         ALL5, lambda a: r(a, "3.6") in N | {NI}),
        ("3.8", "[If N/PN/NI to 3.7] Were potential selection biases identified above sufficiently severe that the result should not be included in a quantitative synthesis?",
         ALL5, lambda a: r(a, "3.7") in N | {NI}),
    ],
}


def _incomplete(a):
    return any(r(a, q) in N | {NI} for q in ("4.1", "4.2", "4.3"))


D4 = {
    "id": "D4", "name": "Domain 4 - Risk of bias due to missing data", "directions": DIR_STD,
    "questions": [
        ("4.1", "Were complete data on intervention status available for all, or nearly all, participants?", ALL5, None),
        ("4.2", "Were complete data on the outcome available for all, or nearly all, participants?", ALL5, None),
        ("4.3", "Were complete data on important confounding variables available for all, or nearly all, participants?", ALL5, None),
        ("4.4", "[If N/PN/NI to 4.1, 4.2 or 4.3] Is the result based on a complete case analysis?", ALL5, _incomplete),
        ("4.5", "[If Y/PY/NI to 4.4] Was exclusion from the analysis because of missing data (in intervention, confounders or the outcome) likely to be related to the true value of the outcome?",
         ALL5, lambda a: r(a, "4.4") in Y | {NI}),
        ("4.6", "[If Y/PY/NI to 4.5] Is the relationship between the outcome and missingness likely to be explained by the variables in the analysis model?",
         WSN, lambda a: r(a, "4.5") in Y | {NI}),
        ("4.7", "[If N/PN to 4.4] Was the analysis based on imputing missing values?",
         ["Y", "PY", "PN", "N", "NI"], lambda a: r(a, "4.4") in N),
        ("4.8", "[If Y/PY to 4.7] Is it reasonable to assume that data were 'missing at random' (MAR) or 'missing completely at random' (MCAR)?",
         ALL5, lambda a: r(a, "4.7") in Y),
        ("4.9", "[If Y/PY to 4.8] Was imputation performed appropriately?", WSN, lambda a: r(a, "4.8") in Y),
        ("4.10", "[If N/PN/NI to 4.7] Was an appropriate alternative method used to correct for bias due to missing data?",
         WSN, lambda a: r(a, "4.7") in N | {NI}),
        ("4.11", "[If PN/N/NI to 4.1, 4.2 or 4.3 AND (Y/PY/NI to 4.5 OR WN/SN/NI to 4.9 OR WN/SN/NI to 4.10)] Is there evidence that the result was not biased by missing data?",
         YN4, lambda a: _incomplete(a) and (r(a, "4.5") in Y | {NI}
                                             or r(a, "4.9") in {"WN", "SN", NI}
                                             or r(a, "4.10") in {"WN", "SN", NI})),
    ],
}

D5 = {
    "id": "D5", "name": "Domain 5 - Risk of bias in measurement of the outcome", "directions": DIR_STD,
    "questions": [
        ("5.1", "Could measurement or ascertainment of the outcome have differed between intervention groups?", ALL5, None),
        ("5.2", "[If N/PN/NI to 5.1] Were outcome assessors aware of the intervention received by study participants?",
         ALL5, lambda a: r(a, "5.1") in N | {NI}),
        ("5.3", "[If Y/PY/NI to 5.2] Could assessment of the outcome have been influenced by knowledge of the intervention received?",
         WSY, lambda a: r(a, "5.2") in Y | {NI}),
    ],
}

D6 = {
    "id": "D6", "name": "Domain 6 - Risk of bias in selection of the reported result", "directions": DIR_STD,
    "questions": [
        ("6.1", "Was the result reported in accordance with an available, pre-determined analysis plan?", ALL5, None),
        ("6.2", "[If N/PN/NI to 6.1] Is the numerical result being assessed likely to have been selected, on the basis of the results, from multiple outcome measurements (e.g. scales, definitions, time points) within the outcome domain?",
         ALL5, lambda a: r(a, "6.1") in N | {NI}),
        ("6.3", "[If N/PN/NI to 6.1] ... from multiple analyses of the data?", ALL5, lambda a: r(a, "6.1") in N | {NI}),
        ("6.4", "[If N/PN/NI to 6.1] ... from multiple subgroups?", ALL5, lambda a: r(a, "6.1") in N | {NI}),
    ],
}


# ---------------------------------------------------------------------------
# Algorithms (ROBINS-I V2 cribsheet figures, 20 Nov 2025)
# ---------------------------------------------------------------------------
def algo_d1a(a):
    q1, q2, q3, q4 = a["1.1"], a["1.2"], a["1.3"], a["1.4"]
    if q1 in {"SN", NI}:
        if q4 in Y:
            return CRIT, "1.1 = SN/NI; 1.4 = Y/PY (negative controls etc. suggest serious uncontrolled confounding) → critical."
        return SER, "1.1 = SN/NI (important confounding uncontrolled); 1.4 = N/PN → serious."
    if q3 in Y:  # adjustment for post-intervention variables
        if q4 in Y:
            return CRIT, "1.1 = Y/PY/WN; 1.3 = Y/PY (post-intervention variables adjusted for); 1.4 = Y/PY → critical."
        if q2 in Y:
            return SER, "1.1 = Y/PY/WN; 1.3 = Y/PY; 1.4 = N/PN; 1.2 = Y/PY → serious."
        return CRIT, "1.1 = Y/PY/WN; 1.3 = Y/PY; 1.4 = N/PN; 1.2 = WN/SN/NI → critical."
    # 1.3 = N/PN/NI
    if q1 in Y:
        if q2 in Y:
            if q4 in Y:
                return SER, "1.1 = Y/PY; 1.3 = N/PN/NI; 1.2 = Y/PY; 1.4 = Y/PY → serious."
            return LOWX, "1.1 = Y/PY; 1.3 = N/PN/NI; 1.2 = Y/PY; 1.4 = N/PN → low (except for concerns about uncontrolled confounding)."
        if q2 == "WN":
            if q4 in Y:
                return SER, "1.1 = Y/PY; 1.3 = N/PN/NI; 1.2 = WN; 1.4 = Y/PY → serious."
            return MOD, "1.1 = Y/PY; 1.3 = N/PN/NI; 1.2 = WN (measurement error probably not substantial); 1.4 = N/PN → moderate."
        return SER, "1.1 = Y/PY; 1.3 = N/PN/NI; 1.2 = SN/NI (confounders not measured validly/reliably) → serious."
    # 1.1 = WN
    if q2 in Y | {"WN"}:
        if q4 in Y:
            return SER, "1.1 = WN; 1.3 = N/PN/NI; 1.2 = Y/PY/WN; 1.4 = Y/PY → serious."
        return MOD, "1.1 = WN (uncontrolled confounding probably not substantial); 1.3 = N/PN/NI; 1.2 = Y/PY/WN; 1.4 = N/PN → moderate."
    return SER, "1.1 = WN; 1.3 = N/PN/NI; 1.2 = SN/NI → serious."


def algo_d1b(a):
    q1, q2, q3, q4, q5 = a["1.1"], a["1.2"], a["1.3"], a["1.4"], a["1.5"]
    if q1 in N | {NI}:
        if q4 in Y:
            return CRIT, "1.1 = N/PN/NI (inappropriate method for time-varying confounding); 1.4 = Y/PY (adjusted for post-baseline variables) → critical."
        if q5 in Y:
            return CRIT, "1.1 = N/PN/NI; 1.4 = N/PN/NI; 1.5 = Y/PY → critical."
        return SER, "1.1 = N/PN/NI; 1.4 = N/PN/NI; 1.5 = N/PN → serious."
    if q2 in {"SN", NI}:
        if q5 in Y:
            return CRIT, "1.1 = Y/PY; 1.2 = SN/NI; 1.5 = Y/PY → critical."
        return SER, "1.1 = Y/PY; 1.2 = SN/NI (important confounding uncontrolled); 1.5 = N/PN → serious."
    if q3 in {"SN", NI}:
        return SER, f"1.1 = Y/PY; 1.2 = {q2}; 1.3 = SN/NI (confounders not measured validly/reliably) → serious."
    if q2 in Y and q3 in Y:
        if q5 in Y:
            return SER, "1.1 = Y/PY; 1.2 = Y/PY; 1.3 = Y/PY; 1.5 = Y/PY → serious."
        return LOWX, "1.1 = Y/PY; 1.2 = Y/PY; 1.3 = Y/PY; 1.5 = N/PN → low (except for concerns about uncontrolled confounding)."
    if q5 in Y:
        return SER, f"1.1 = Y/PY; 1.2 = {q2}; 1.3 = {q3}; 1.5 = Y/PY → serious."
    return MOD, f"1.1 = Y/PY; 1.2 = {q2}; 1.3 = {q3} (at least one WN); 1.5 = N/PN → moderate."


LEVEL3 = {0: (LOW, MOD), 1: (MOD, SER), 2: (SER, CRIT)}


def algo_d2(a):
    q1, q2, q3, q4, q5 = a["2.1"], a["2.2"], a["2.3"], a["2.4"], a["2.5"]
    if q1 in Y:
        start, s = 0, "2.1 = Y/PY (strategies distinguishable at start of follow-up)"
    elif q2 in Y:
        start, s = 0, "2.1 = N/PN/NI; 2.2 = Y/PY (nearly all events after strategies distinguishable)"
    elif q3 == "SY":
        start, s = 0, "2.1/2.2 = N/PN/NI; 2.3 = SY (analysis fully avoided the problem)"
    elif q3 in {"WY", NI}:
        start, s = 1, f"2.1/2.2 = N/PN/NI; 2.3 = {q3}"
    else:
        start, s = 2, "2.1/2.2 = N/PN/NI; 2.3 = N/PN (immortal-time problem not addressed)"
    shift = {"N": 0, "PN": 0, "WY": 1, NI: 1, "SY": 2}[q4]
    lvl = start + shift
    s += f"; 2.4 = {q4}"
    if lvl >= 3:
        return CRIT, s + " → critical."
    good, bad = LEVEL3[lvl]
    if q5 in N:
        return good, s + f"; 2.5 = N/PN → {good.lower()}."
    return bad, s + f"; 2.5 = Y/PY/NI (further classification errors likely) → {bad.lower()}."


def algo_d3(a):
    q1 = a["3.1"]
    # Part A: prevalent-user bias / immortal time
    if q1 in Y:
        if a["3.2"] in Y:
            pa, ea = MOD, "Part A: 3.1 = Y/PY; 3.2 = Y/PY (early events excluded) → moderate."
        else:
            pa, ea = LOW, "Part A: 3.1 = Y/PY; 3.2 = N/PN/NI → low."
    elif q1 in {"WN", NI}:
        pa, ea = MOD, f"Part A: 3.1 = {q1} → moderate."
    else:
        pa, ea = SER, "Part A: 3.1 = SN (follow-up not starting at start of intervention, substantial) → serious."
    # Part B: other selection
    q3, q4, q5 = a["3.3"], a["3.4"], a["3.5"]
    if q3 in N:
        pb, eb = LOW, "Part B: 3.3 = N/PN → low."
    elif q4 in N:
        pb, eb = LOW, "Part B: 3.3 = Y/PY/NI; 3.4 = N/PN → low."
    elif q4 == NI:
        pb, eb = MOD, "Part B: 3.3 = Y/PY/NI; 3.4 = NI → moderate."
    elif q5 in Y:
        pb, eb = SER, "Part B: 3.4 = Y/PY; 3.5 = Y/PY (selection related to intervention and outcome) → serious."
    else:
        pb, eb = MOD, "Part B: 3.4 = Y/PY; 3.5 = N/PN/NI → moderate."
    parts = {"partA": pa, "partB": pb}
    e = f"{ea} {eb}"
    worst = max(pa, pb, key=lambda x: RANK[x])
    if worst == LOW:
        return LOW, e + " Across A and B: all low → low.", parts
    if worst == MOD:
        return MOD, e + " Across A and B: at worst moderate → moderate.", parts
    # at least one serious
    if a["3.6"] in Y:
        return MOD, e + " At least one serious; 3.6 = Y/PY (analysis corrected) → moderate.", parts
    if a["3.7"] in Y:
        return MOD, e + " At least one serious; 3.6 = N/PN/NI; 3.7 = Y/PY (sensitivity analyses show minimal impact) → moderate.", parts
    if a["3.8"] in Y:
        return CRIT, e + " At least one serious; 3.6/3.7 = N/PN/NI; 3.8 = Y/PY (severe) → critical.", parts
    return SER, e + " At least one serious; 3.6/3.7 = N/PN/NI; 3.8 = N/PN/NI → serious.", parts


def algo_d4(a):
    if not _incomplete(a):
        return LOW, "4.1-4.3 all Y/PY (complete data for (nearly) all participants) → low."
    q44 = a["4.4"]
    if q44 in Y | {NI}:
        if a["4.5"] in N:
            return LOW, "Complete-case analysis; 4.5 = N/PN (exclusion unrelated to true outcome) → low."
        q6 = a["4.6"]
        lvl = 0 if q6 in Y else (1 if q6 in {"WN", NI} else 2)
        s = f"Complete-case analysis; 4.5 = Y/PY/NI; 4.6 = {q6}"
    elif a["4.7"] in Y:
        if a["4.8"] in N | {NI}:
            return SER, "Imputation; 4.8 = N/PN/NI (MAR/MCAR not reasonable) → serious."
        q9 = a["4.9"]
        if q9 in Y:
            return LOW, "Imputation; 4.8 = Y/PY; 4.9 = Y/PY (appropriate imputation) → low."
        lvl = 1 if q9 in {"WN", NI} else 2
        s = f"Imputation; 4.8 = Y/PY; 4.9 = {q9}"
    else:
        q10 = a["4.10"]
        if q10 in Y:
            return LOW, "Neither complete-case nor imputation; 4.10 = Y/PY (appropriate alternative method) → low."
        lvl = 1 if q10 in {"WN", NI} else 2
        s = f"Other method; 4.10 = {q10}"
    good, bad = LEVEL3[lvl]
    if a["4.11"] in Y:
        return good, s + f"; 4.11 = Y/PY (evidence result not biased) → {good.lower()}."
    return bad, s + f"; 4.11 = N/PN → {bad.lower()}."


def algo_d5(a):
    q1, q2, q3 = a["5.1"], a["5.2"], a["5.3"]
    if q1 in Y:
        return SER, "5.1 = Y/PY (measurement/ascertainment could differ between groups) → serious."
    if q1 in N:
        if q2 in N:
            return LOW, "5.1 = N/PN; 5.2 = N/PN (assessors unaware) → low."
        if q3 in N:
            return LOW, "5.1 = N/PN; 5.2 = Y/PY/NI; 5.3 = N/PN → low."
        if q3 == "SY":
            return SER, "5.1 = N/PN; 5.2 = Y/PY/NI; 5.3 = SY → serious."
        return MOD, f"5.1 = N/PN; 5.2 = Y/PY/NI; 5.3 = {q3} → moderate."
    # 5.1 = NI
    if q2 in N:
        return MOD, "5.1 = NI; 5.2 = N/PN → moderate."
    if q3 == "SY":
        return SER, "5.1 = NI; 5.2 = Y/PY/NI; 5.3 = SY → serious."
    return MOD, f"5.1 = NI; 5.2 = Y/PY/NI; 5.3 = {q3} → moderate."


def algo_d6(a):
    if a["6.1"] in Y:
        return LOW, "6.1 = Y/PY (reported according to a pre-determined analysis plan) → low."
    rs = [a["6.2"], a["6.3"], a["6.4"]]
    ny = sum(x in Y for x in rs)
    nni = sum(x == NI for x in rs)
    if ny >= 2:
        return CRIT, "6.1 = N/PN/NI; two or more of 6.2-6.4 = Y/PY → critical."
    if ny == 1:
        return SER, "6.1 = N/PN/NI; one of 6.2-6.4 = Y/PY → serious."
    if nni == 3:
        return SER, "6.1 = N/PN/NI; 6.2-6.4 all NI → serious."
    if nni >= 1:
        return MOD, "6.1 = N/PN/NI; at least one NI in 6.2-6.4, none Y/PY → moderate."
    return LOW, "6.1 = N/PN/NI; 6.2-6.4 all N/PN → low."


ALGOS = {"D1A": algo_d1a, "D1B": algo_d1b, "D2": algo_d2, "D3": algo_d3,
         "D4": algo_d4, "D5": algo_d5, "D6": algo_d6}


# ---------------------------------------------------------------------------
def norm_resp(x):
    if x is None:
        return None
    s = str(x).strip().upper().replace(" ", "").replace("_", "")
    return {"YES": "Y", "NO": "N", "PROBABLYYES": "PY", "PROBABLYNO": "PN",
            "NOINFORMATION": "NI", "NOTAPPLICABLE": "NA", "WEAKNO": "WN", "STRONGNO": "SN",
            "WEAKYES": "WY", "STRONGYES": "SY"}.get(s, s)


def norm_judg(x):
    if x is None:
        return None
    if x in RANK:
        return x
    return JUDG_ALIASES.get(str(x).strip().lower())


def resolve_questions(questions, raw, resolved, details, errors, warnings):
    for qid, text, options, cond in questions:
        item = raw.get(qid) or {}
        if isinstance(item, str):
            item = {"response": item}
        resp = norm_resp(item.get("response"))
        applicable = True if cond is None else bool(cond(resolved))
        if not applicable:
            if resp not in (None, NA):
                warnings.append(f"{qid}: not applicable according to the filtering logic; response '{resp}' replaced by NA.")
            resolved[qid] = NA
        elif resp is None:
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
            "question": text, "applicable": applicable, "response": resolved[qid],
            "justification": item.get("justification", "") if applicable else "Not applicable (algorithm filtering).",
            "sources": item.get("sources", []) if applicable else [],
        }


def validate_confounders(data, errors, warnings):
    conf = data.get("confounding_factors") or {}
    out = {}
    for key in ("prespecified", "additional"):
        rows = conf.get(key) or []
        for i, row in enumerate(rows, 1):
            tag = f"confounding_factors.{key}[{i}] ({row.get('factor', '?')})"
            if not row.get("factor"):
                errors.append(f"{tag}: 'factor' missing.")
            c = norm_resp(row.get("controlled"))
            if c not in CONF_CONTROLLED:
                errors.append(f"{tag}: 'controlled' must be Y or N.")
            v = norm_resp(row.get("valid_reliable", "NA"))
            if v not in CONF_VALID:
                errors.append(f"{tag}: 'valid_reliable' must be one of {'/'.join(CONF_VALID)}.")
            u = norm_resp(row.get("control_unnecessary", "NA"))
            if u not in CONF_UNNEC:
                errors.append(f"{tag}: 'control_unnecessary' must be one of {'/'.join(CONF_UNNEC)}.")
            if c == "Y" and u not in ("NA",):
                warnings.append(f"{tag}: controlled = Y, so 'control_unnecessary' should be NA.")
            if c == "N" and v not in ("NA",):
                warnings.append(f"{tag}: controlled = N, so 'valid_reliable' should be NA.")
            d = row.get("direction_if_unadjusted")
            if d and d not in CONF_DIR:
                warnings.append(f"{tag}: direction '{d}' is not a standard option ({' / '.join(CONF_DIR)}).")
            row["controlled"], row["valid_reliable"], row["control_unnecessary"] = c, v, u
        out[key] = rows
    if not out.get("prespecified") and not out.get("additional"):
        warnings.append("No confounding factors listed: Domain 1 answers cannot be traced to the confounder evaluation table.")
    return out


def run(data):
    errors, warnings = [], []
    raw = data.get("answers", {}) or {}
    pc = data.get("preliminary", {}) or {}
    tt = pc.get("target_trial", {}) or {}

    c4 = norm_resp(tt.get("C4"))
    if c4 not in ("Y", "N"):
        errors.append("preliminary.target_trial.C4 must be 'N' (intention-to-treat effect → Domain 1 Variant A) or 'Y' (per-protocol effect → Variant B).")
    for k in ("A1", "A3"):
        if not str((pc.get("result") or {}).get(k, "")).strip():
            errors.append(f"preliminary.result.{k} is required.")
    for k in ("C1", "C2", "C3"):
        if not str(tt.get(k, "")).strip():
            errors.append(f"preliminary.target_trial.{k} is required.")
    conf = validate_confounders(data, errors, warnings)

    known = {q[0] for q in SCREENING}
    for d in (D1A, D1B, D2, D3, D4, D5, D6):
        known |= {q[0] for q in d["questions"]}
    for k in raw:
        if k not in known:
            warnings.append(f"Unknown question ignored: {k}")

    resolved, details = {}, {}
    resolve_questions(SCREENING, raw, resolved, details, errors, warnings)
    if errors:
        return None, errors, warnings

    screening_critical = resolved["B2"] in Y or resolved["B3"] in Y
    overrides = data.get("overrides", {}) or {}
    directions = data.get("directions", {}) or {}
    domains_out = []

    if not screening_critical:
        d1 = D1A if c4 == "N" else D1B
        for d in (d1, D2, D3, D4, D5, D6):
            resolve_questions(d["questions"], raw, resolved, details, errors, warnings)
        if errors:
            return None, errors, warnings
        for d in (d1, D2, D3, D4, D5, D6):
            key = "D1" + d["variant"] if d["id"] == "D1" else d["id"]
            res = ALGOS[key](resolved)
            algo_j, expl = res[0], res[1]
            extra = res[2] if len(res) > 2 else None
            final_j, ov = algo_j, None
            o = overrides.get(d["id"])
            if o:
                oj = norm_judg(o.get("judgement"))
                if oj is None:
                    errors.append(f"Override {d['id']}: invalid judgement '{o.get('judgement')}' (Low/Low (except confounding)/Moderate/Serious/Critical).")
                elif not str(o.get("justification", "")).strip():
                    errors.append(f"Override {d['id']}: justification required.")
                elif d["id"] == "D1" and oj == LOW:
                    errors.append("Override D1: 'Low' is not available for confounding; use 'Low (except confounding)'.")
                elif d["id"] != "D1" and oj == LOWX:
                    errors.append(f"Override {d['id']}: 'Low (except confounding)' is reserved for Domain 1.")
                elif oj != algo_j:
                    final_j, ov = oj, o["justification"]
            dirn = directions.get(d["id"])
            if dirn and dirn not in d["directions"]:
                warnings.append(f"Direction {d['id']} '{dirn}' is not a standard option ({' / '.join(d['directions'])}).")
            domains_out.append({
                "id": d["id"], "variant": d.get("variant"), "name": d["name"],
                "signalling_questions": [dict(id=q[0], **details[q[0]]) for q in d["questions"]],
                "algorithm_judgement": algo_j, "algorithm_explanation": expl, "algorithm_parts": extra,
                "override_justification": ov, "judgement": final_j, "predicted_direction": dirn,
            })
        if errors:
            return None, errors, warnings

    # Overall
    flag = None
    if screening_critical:
        oj = CRIT
        why = "B2 = Y/PY (unadjusted result with sufficient potential for confounding)" if resolved["B2"] in Y else \
              "B3 = Y/PY (inappropriate method of measuring the outcome)"
        oexp = f"Screening (part B): {why} → critical risk of bias; no further assessment required."
    else:
        js = [x["judgement"] for x in domains_out]
        worst = max(RANK[j] for j in js)
        n_mod, n_ser = js.count(MOD), js.count(SER)
        if worst == 3:
            oj, oexp = CRIT, "At least one domain at critical risk → critical."
        elif worst == 2:
            oj, oexp = SER, f"{n_ser} domain(s) at serious risk, none critical → serious."
            if n_ser >= 2:
                flag = (f"{n_ser} domains at serious risk: ROBINS-I V2 allows an additive judgement of CRITICAL risk overall "
                        "if these problems are likely to be compounded. Assessor judgement required (override 'overall').")
        elif worst == 1:
            oj, oexp = MOD, f"{n_mod} domain(s) at moderate risk, none serious or critical → moderate."
            if n_mod >= 2:
                flag = (f"{n_mod} domains at moderate risk: ROBINS-I V2 allows an additive judgement of SERIOUS risk overall. "
                        "Assessor judgement required (override 'overall').")
        else:
            oj, oexp = LOWX, "Domain 1 low (except for concerns about uncontrolled confounding) and all other domains low → low risk of bias except for concerns about uncontrolled confounding."
    final_o, ovo = oj, None
    o = overrides.get("overall")
    if o and not screening_critical:
        ojj = norm_judg(o.get("judgement"))
        if ojj is None or not str(o.get("justification", "")).strip():
            return None, ["Overall override: valid judgement and justification required."], warnings
        if RANK[ojj] < RANK[oj]:
            return None, ["Overall override: the overall judgement cannot be more favourable than the algorithm's."], warnings
        if ojj != oj:
            final_o, ovo = ojj, o["justification"]
    odir = directions.get("overall")
    if odir and odir not in DIR_OVERALL:
        warnings.append(f"Overall direction '{odir}' is not a standard option.")

    out = {
        "tool": "ROBINS-I V2 (Risk Of Bias In Non-randomized Studies - of Interventions, Version 2), follow-up studies - cribsheet of 20 November 2025",
        "generated": _dt.datetime.now().isoformat(timespec="seconds"),
        "study": data.get("study", {}),
        "preliminary": pc,
        "effect_of_interest": "Per-protocol effect (Domain 1 Variant B)" if c4 == "Y" else "Intention-to-treat effect (Domain 1 Variant A)",
        "confounding_factors": conf,
        "screening": {
            "questions": [dict(id=q[0], **details[q[0]]) for q in SCREENING],
            "critical": screening_critical,
        },
        "domains": domains_out,
        "overall": {
            "algorithm_judgement": oj, "algorithm_explanation": oexp, "additive_flag": flag,
            "override_justification": ovo, "judgement": final_o, "predicted_direction": odir,
        },
        "summary": ({x["id"]: x["judgement"] for x in domains_out} | {"overall": final_o}),
        "comments": data.get("comments", ""),
        "validation_warnings": warnings,
    }
    return out, [], warnings


# ---------------------------------------------------------------------------
def esc(s):
    return str(s if s is not None else "").replace("|", "\\|").replace("\n", " ")


def render_sources(L, srcs):
    L.append("_**Sources:**_\n")
    for s in srcs:
        loc = f"[{s.get('document', '?')}] {s.get('location', 'location not specified')}"
        q = s.get("quote")
        L.append(f"- {loc}" + (f": \"{q}\"" if q else ""))
    L.append("")


def render_question(L, q):
    L.append(f"#### Question {q['id']} - {q['question']}\n")
    L.append("<div style='margin-left: 1.5em;'>\n")
    L.append(f"_**Response:**_ {LABEL_RESP.get(q['response'], q['response'])}\n")
    if q["applicable"]:
        L.append(f"_**Rationale:**_ {q['justification']}\n")
        if q["sources"]:
            render_sources(L, q["sources"])
        elif q["response"] == NI:
            L.append("_**Sources:**_ no relevant information found in the documents assessed.\n")
    else:
        L.append("*Question not asked (conditional filtering of the algorithm).*\n")
    L.append("</div>\n")

def render_md(o):
    st, pc = o["study"], o["preliminary"]
    res, tt = pc.get("result", {}) or {}, pc.get("target_trial", {}) or {}
    L = ["# Risk-of-bias assessment - ROBINS-I V2 (follow-up studies)\n"]
    L.append(f"**Study:** {st.get('title', 'N/R')}  ")
    for k, lab in [("acronym", "Short name"), ("authors", "Authors"), ("journal", "Reference"),
                   ("doi", "DOI"), ("registration", "Registration"), ("design", "Design / data source")]:
        if st.get(k):
            L.append(f"**{lab}:** {st[k]}  ")
    L.append(f"**Tool:** {o['tool']}  ")
    L.append(f"**Assessment date:** {o['generated'][:10]}\n")
    if st.get("documents"):
        L.append("## Documents assessed\n")
        L.append("| Ref. | Document | Type |\n|---|---|---|")
        for d in st["documents"]:
            L.append(f"| {esc(d.get('id'))} | {esc(d.get('name'))} | {esc(d.get('type'))} |")
        L.append("")

    # Summary first
    L.append("## Summary\n")
    ov = o["overall"]
    if o["screening"]["critical"]:
        L.append(f"**Overall: {EMOJI[CRIT]} {LABEL_JUDG[CRIT]}** - {ov['algorithm_explanation']}\n")
    else:
        L.append("| Domain | Judgement | Predicted direction of bias |\n|---|---|---|")
        for d in o["domains"]:
            j = d["judgement"]
            star = " *(overridden)*" if d["override_justification"] else ""
            L.append(f"| {d['id']} - {esc(d['name'].split(' - ', 1)[1])} | {EMOJI[j]} {LABEL_JUDG[j]}{star} | {d['predicted_direction'] or '-'} |")
        L.append(f"| **Overall risk of bias** | **{EMOJI[ov['judgement']]} {LABEL_JUDG[ov['judgement']]}** | {ov['predicted_direction'] or '-'} |\n")
        L.append(f"*Rule applied:* {ov['algorithm_explanation']}")
        if ov["additive_flag"]:
            L.append(f"\n> ⚠️ {ov['additive_flag']}")
        if ov["override_justification"]:
            L.append(f"\n> **Overall judgement changed by the assessor** ({LABEL_JUDG[ov['algorithm_judgement']]} → {LABEL_JUDG[ov['judgement']]}): {ov['override_justification']}")
        L.append("")

    # Preliminary considerations
    L.append("## Preliminary considerations\n")
    L.append("### A. Result being assessed\n")
    for k, lab in [("A1", "A1. Numerical result"), ("A2", "A2. Further details (location, reason chosen)"), ("A3", "A3. Outcome")]:
        if res.get(k):
            L.append(f"- **{lab}:** {res[k]}")
    L.append("")
    L.append("### B. Decision to proceed (screening)\n")
    for q in o["screening"]["questions"]:
        render_question(L, q)
    L.append("### C. Target trial specific to the study\n")
    for k, lab in [("C1", "C1. Participants and eligibility criteria"), ("C2", "C2. Intervention strategy"),
                   ("C3", "C3. Comparator strategy"), ("time_zero", "Start of follow-up (time zero)"),
                   ("follow_up_end", "End of follow-up"), ("outcome", "Outcome"),
                   ("notes", "Notes on emulation")]:
        if tt.get(k):
            L.append(f"- **{lab}:** {tt[k]}")
    c4 = tt.get("C4")
    c4txt = ("Yes - the analysis estimates the per-protocol effect (Domain 1 Variant B)" if norm_resp(c4) == "Y"
             else "No - the analysis estimates the intention-to-treat effect (Domain 1 Variant A)")
    L.append(f"- **C4. Did the analysis account for switches or other protocol deviations during follow-up?** {c4txt}")
    if tt.get("C4_justification"):
        L.append(f"  - *Rationale:* {tt['C4_justification']}")
    if tt.get("sources"):
        L.append("")
        render_sources(L, tt["sources"])
    L.append("")
    if pc.get("information_sources"):
        L.append("### D. Information sources\n")
        for s in pc["information_sources"]:
            L.append(f"- {s}")
        L.append("")

    # Confounders
    cf = o["confounding_factors"]
    if cf.get("prespecified") or cf.get("additional"):
        L.append("## Evaluation of confounding factors\n")
        if (pc.get("confounders_note")):
            L.append(f"*{pc['confounders_note']}*\n")
        for key, title in [("prespecified", "(i) Important confounding factors listed in advance"),
                           ("additional", "(ii) Additional confounding factors relevant to this study or identified by the authors")]:
            rows = cf.get(key) or []
            if not rows:
                continue
            L.append(f"### {title}\n")
            L.append("| Confounding factor | Measured variable(s) | Controlled for? | Valid & reliable? | Evidence control unnecessary? | Bias if unadjusted | Comments / sources |")
            L.append("|---|---|---|---|---|---|---|")
            for rw in rows:
                srcs = "; ".join(f"[{s.get('document','?')}] {s.get('location','')}" for s in (rw.get("sources") or []))
                com = " ".join(x for x in [esc(rw.get("comments", "")), f"({esc(srcs)})" if srcs else ""] if x)
                L.append(f"| {esc(rw.get('factor'))} | {esc(rw.get('measured_variables', '-'))} | {rw['controlled']} | "
                         f"{rw['valid_reliable']} | {rw['control_unnecessary']} | {esc(rw.get('direction_if_unadjusted', '-'))} | {com} |")
            L.append("")
        L.append("---\n")

    # Domains
    if o["domains"]:
        L.append("## Detailed assessment by domain\n")
        for d in o["domains"]:
            L.append(f"### {d['name']}\n")
            for q in d["signalling_questions"]:
                render_question(L, q)

            L.append(f"**Algorithm:** {d['algorithm_explanation']}\n")
            j = d["judgement"]
            if d["override_justification"]:
                L.append(f"**Judgement proposed by the algorithm:** {LABEL_JUDG[d['algorithm_judgement']]}  ")
                L.append(f"**Judgement retained by the assessor:** {EMOJI[j]} **{LABEL_JUDG[j]}** - {d['override_justification']}\n")
            else:
                L.append(f"**Domain judgement: {EMOJI[j]} {LABEL_JUDG[j]}**\n")
            if d["predicted_direction"]:
                L.append(f"**Predicted direction of bias:** {d['predicted_direction']}\n")
            L.append("---\n")
    else:
        L.append("## Domains\n\n*Not assessed: the result was classified at critical risk of bias at the screening step (part B).*\n")

    if o.get("comments"):
        L.append("## Assessor comments\n")
        L.append(o["comments"] + "\n")
    if o["validation_warnings"]:
        L.append("## Validation warnings\n")
        for w in o["validation_warnings"]:
            L.append(f"- {w}")
        L.append("")
    L.append("---\n*Report generated by robins_i_v2.py - ROBINS-I V2 algorithms. "
             "Judgements proposed by the algorithms from the signalling-question "
             "answers; to be confirmed by a human assessor.*")
    return "\n".join(L)


def main():
    ap = argparse.ArgumentParser(description="ROBINS-I V2 - judgement algorithms and report")
    ap.add_argument("input")
    ap.add_argument("--out-json", default="robins_result.json")
    ap.add_argument("--out-md", default="robins_report.md")
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
    print("Judgements:", json.dumps(out["summary"], ensure_ascii=False))
    print(f"Written: {args.out_json}, {args.out_md}")


if __name__ == "__main__":
    main()
```