---
name: "integrite-ecr"
description: "Évaluer l'intégrité méthodologique d'un ECR confirmatoire : analyse de la chronologie de l'essai (dates protocole, SAP, amendement, inclusion, DCO, analyse, gel de base, levée d'aveugle, publications secondaires), détection des incohérences et déviations au protocole/SAP ; interroge PubMed et ClinicalTrials.gov via les connecteurs."
---

# Chronologie et intégrité méthodologique d'un ECR confirmatoire

Objectif : reconstituer la chronologie d'un essai randomisé de confirmation à partir de ses dates jalons, synthétiser ses amendements, vérifier la cohérence des dates, identifier les déviations majeures de la réalisation et de l'analyse par rapport au protocole (amendements compris) et au SAP, et juger si la pré-spécification (question de recherche, critères, analyses) est crédible. Rapport en français.

Utiliser quand l'utilisateur demande : « chronologie de l'essai », « dates jalons », « timeline », « vérifier la pré-spécification / l'enregistrement prospectif », « amendements après inclusion », « synthèse des amendements », « cohérence des dates », « déviations au protocole / au SAP », ou téléverse un essai en demandant d'en évaluer l'intégrité temporelle.

## 1. Jalons à rechercher

| Code | Jalon | Sources prioritaires |
|---|---|---|
| PROTOCOLE | Date du protocole (v1 et versions) | Page de garde / tableau d'historique du protocole (supplément), EPAR |
| PUB_PROTOCOLE | Publication du protocole / design | Connecteur PubMed (type « Clinical Trial Protocol », titres « rationale and design »), BMJ Open, Trials, abstract TiP de congrès |
| SAP | SAP, chaque version | Historique des versions du SAP (supplément) |
| REG_INIT | Déclaration initiale au registre | ClinicalTrials.gov « First Submitted » / version 1 de l'historique ; EUCTR/CTIS, ISRCTN, ChiCTR, JRCT… |
| REG_MODIF | Modifications du registre | Historique des versions CT.gov (comparer v1 vs versions suivantes) |
| REG_PCD | Primary completion déclarée au registre | Connecteur ClinicalTrials.gov (`primary_completion_date`) |
| FPI | Inclusion du 1er patient | Article (méthodes/résultats), connecteur ClinicalTrials.gov (`start_date`), publication des caractéristiques initiales, EPAR |
| LPI | Inclusion du dernier patient | Article, CONSORT, EPAR, CSR |
| LPV | Fin d'étude (dernière visite) | Connecteur ClinicalTrials.gov (`completion_date`), article |
| AMENDEMENT | Tous les amendements du protocole (numéro, date, version) | Résumé / historique des amendements du protocole (supplément), EPAR, FDA review |
| DCO_IA / DCO_FINAL | Data cut-off des analyses intermédiaires et finales, par critère | Article, supplément, EPAR, communiqués ; à défaut, pour un critère à date fixe, primary completion du registre (à signaler comme approximation) |
| DBL | Gel de la base de données (database lock) de l'analyse principale | CSR, EPAR, FDA review, supplément (« database lock »), SAP (procédure DBL) |
| UNBLIND | Levée de l'aveugle (accès du promoteur/des analystes aux données par bras) | CSR, EPAR, SAP (« release of treatment information », « unblinding »), supplément |
| PRESS | Communiqué annonçant les résultats | Site du promoteur, newswire |
| PRESENTATION | 1re présentation (congrès) | Programme du congrès (date de session, late-breaking) |
| SOUMISSION | Soumission du manuscrit | « Received » du journal |
| EPUB / PUB | Publication en ligne et définitive | Connecteur PubMed (`publication_date` = epub), page de l'article, date du numéro |
| PUB_SECONDAIRE | Publications secondaires : caractéristiques initiales de la population incluse, sous-études, analyses post hoc, articles méthodologiques | Connecteur PubMed (recherche par NCT et acronyme, `find_related_articles`) |

Règles :
- N'inventer aucune date. Absente → `null` (NR). Indiquer la précision réelle (jour `AAAA-MM-JJ`, mois `AAAA-MM`, année `AAAA`).
- Citer la source de chaque date (document + page/section, n° de version du registre).
- Si deux sources divergent pour un même jalon, saisir les deux entrées et signaler la discordance (anomalie analyste).
- Gel de base et levée de l'aveugle sont rarement publiés : les chercher dans le CSR, SAP et l'EPAR. S'ils restent introuvables, laisser `null` ; le script le signale et adapte la gravité du contrôle SAP (voir C20/C63).

### Publications secondaires
Pour chaque publication secondaire (y compris la publication du design/protocole, saisie en `PUB_PROTOCOLE`) :
- dater l'epub, indiquer PMID et DOI, la `nature` (design/protocole, caractéristiques initiales, sous-étude, post hoc, méthodologie) ;
- extraire ce qu'elle apporte à la chronologie (`apport`) : dates de recrutement, effectif, critères et analyses annoncés ;
- comparer ce qu'elle annonce (critères principaux, effectif, population d'analyse, estimand) au protocole et à l'article principal ; toute divergence → champ `discordance` (gravité par défaut : majeure) ;
- signaler `"resultats_comparatifs": true` si elle rapporte des résultats par bras avant la publication principale.

### Amendements
Saisir **chaque** amendement identifié (y compris administratifs ou antérieurs à la 1re inclusion), avec son numéro, sa date, la version du protocole qui en résulte et la liste de ses changements méthodologiques ou statistiques.

- Amendement **majeur** (`"majeur": true`, défaut) = modifie la question de recherche ou la méthodologie. Amendement purement administratif, logistique ou de sécurité → `"majeur": false` et `changements` vide.
- Rédiger `changements` comme une liste de puces courtes, une par modification, au format **« domaine : avant → après (justification déclarée si disponible) »**. Domaines à couvrir :
  - critère principal (nature, définition, délai, co-primaires, hiérarchie) ;
  - critères secondaires hiérarchisés / procédure de test ;
  - population (critères d'inclusion/exclusion, sous-population de l'analyse principale, stratification) ;
  - taille d'échantillon, nombre d'événements, hypothèses (effet attendu, puissance) ;
  - analyses intermédiaires, fraction d'information, règles d'arrêt (efficacité/futilité), fonction de dépense d'alpha ;
  - risque alpha et multiplicité (allocation, graphe de test) ;
  - méthode d'analyse principale, estimand, gestion des événements intercurrents et des données manquantes, population d'analyse (ITT, mITT, PP) ;
  - bras, comparateur, dose, schéma de randomisation ;
  - durée de suivi, calendrier des évaluations, mode d'évaluation (investigateur vs revue centralisée).
- Ne rapporter que ce qui est documenté ; si le contenu d'un amendement n'est pas disponible, mettre `"changements": ["Contenu non disponible"]`.

### Nombre de patients inclus au moment d'un amendement
Pour tout amendement postérieur à la 1re inclusion :
- inclusions terminées (date ≥ LPI) → calculé automatiquement ;
- sinon, renseigner `n_inclus` par ordre de préférence : valeur rapportée (protocole, EPAR, CSR, article) ; effectif « Actual » du registre à une version contemporaine ; à défaut estimation par interpolation linéaire entre FPI et LPI (N × (date − FPI)/(LPI − FPI)), explicitement étiquetée « estimation linéaire » dans `n_source`. Si rien n'est possible : laisser `null` (le script affiche « NON RAPPORTÉ »).

### Déviations au protocole et au SAP
Identifier les **déviations majeures** : écarts entre ce qui a été réalisé, analysé ou rapporté et ce qui était prévu.

**Référentiel** : la version du protocole en vigueur au moment de l'action (amendements compris) et la version du SAP finalisée avant la levée d'aveugle ou le 1er DCO. Un changement introduit par un amendement formel avant tout accès aux données comparatives n'est pas une déviation : il figure dans le tableau des amendements. En revanche, un changement introduit par amendement ou par révision du SAP après un DCO est traité à la fois comme amendement et comme déviation.

**Sources** : publication et suppléments (CONSORT, « protocol deviations », « changes to planned analyses »), CSR, EPAR (« GCP », « important protocol deviations », questions majeures), FDA review (« statistical review », « clinical review »), résultats CT.gov, avis HAS. Comparer ligne à ligne le plan de référence et ce qui est rapporté.

Domaines à examiner :

1. **Conduite (niveau patient)** : éligibilité non respectée, erreurs de randomisation ou de stratification, mauvais traitement reçu, cross-over non prévu, levée d'aveugle prématurée, traitements interdits, évaluations manquantes ou hors fenêtre (en particulier pour le critère principal), perdus de vue, retraits de consentement, problèmes BPC sur un site (exclusion de site), impact COVID. Donner n (%) par bras, et signaler tout déséquilibre entre bras.
2. **Conduite (niveau essai)** : effectif ou nombre d'événements atteint ≠ prévu, arrêt prématuré, analyse intermédiaire non prévue ou réalisée à une autre fraction d'information, durée de suivi ≠ prévue, mode d'évaluation modifié (investigateur vs BICR), recrutement prolongé ou étendu à d'autres pays.
3. **Analyse et rapport (vs SAP)** :
   - critère principal analysé autrement (définition, délai, composantes) ;
   - population d'analyse (ITT → mITT/PP, exclusions post-randomisation) ;
   - méthode, modèle, covariables, stratification ;
   - gestion des données manquantes, des événements intercurrents et de la censure ; estimand ;
   - hiérarchie de tests ou multiplicité non respectée ;
   - analyse principale remplacée par une analyse de sensibilité ;
   - sous-groupes ou critères non pré-spécifiés mis en avant ;
   - critères secondaires pré-spécifiés non rapportés (rapport sélectif).

Pour **chaque déviation**, documenter :
- **Prévu / réalisé**, avec les sources précises (document, version, page).
- **Ampleur** : n (%) par bras, ou nature de l'écart.
- **Reconnue par les auteurs ?** *Oui* (déclarée explicitement), *Partiellement* (mentionnée sans en donner l'ampleur ou l'impact), ou *Non* (détectée par l'analyste).
- **Explication fournie par les auteurs** : citer ou paraphraser le motif ; sinon, « aucune ».
- **Appréciation**, si une explication est donnée, à partir de cinq critères :
  1. **Indépendance vis-à-vis des données** : décision prise sans connaissance des résultats comparatifs (avant DCO ou levée d'aveugle, données agrégées en aveugle) ?
  2. **Motif** : externe et documenté (demande d'une agence, sécurité, faisabilité, nouvelle donnée externe, pandémie), ou interne et non documenté ?
  3. **Sens de l'impact** : la déviation est-elle conservatrice ou peut-elle favoriser le bras expérimental ?
  4. **Analyse de sensibilité** : une analyse selon le plan initial est-elle fournie, et concorde-t-elle avec l'analyse retenue ?
  5. **Ampleur** : nombre de patients concernés et équilibre entre bras.

  Verdict : **Justifiée et acceptable** / **Justification plausible, impact non exclu** / **Non justifiée ou inacceptable** / **Non évaluable** (information insuffisante). Une déviation non expliquée reçoit « Non évaluable – non expliquée par les auteurs ».
- **Gravité** :
  - *critique* : touche le critère ou l'analyse principale, peut modifier la conclusion, et n'est pas justifiée de façon acceptable ;
  - *majeure* : peut biaiser l'estimation ou l'inférence ;
  - *mineure* : écart sans impact plausible.

Ne retenir dans le tableau que les déviations majeures ou critiques ; résumer les déviations mineures en une phrase.

### Interrogation des sources externes (connecteurs)
Utiliser en priorité les connecteurs **ClinicalTrials.gov** et **PubMed** (outils `mcp__Clinical_Trials__*` et `mcp__PubMed__*`, à charger via ToolSearch s'ils sont différés). Ne pas tenter `curl`/WebFetch sur clinicaltrials.gov : le site bloque les robots et le proxy.

**ClinicalTrials.gov**
1. Identifier le NCT (article, protocole) ; à défaut `search_trials` (acronyme, molécule, promoteur, `phase=["PHASE3"]`).
2. `get_trial_details(nct_id)` → renseigner : `start_date` (FPI, Study Start Actual), `primary_completion_date` (REG_PCD), `completion_date` (LPV), `enrollment` (N), critères principaux et secondaires de la **version courante**, `has_results` (résultats déposés ?).
3. Si le connecteur **ne fournit ni la date de First Submitted ni l'historique des versions**. Pour REG_INIT et REG_MODIF : tenter WebSearch (« NCTxxxxxxxx first posted »), sinon demander à l'utilisateur un PDF/export de l'onglet « History of Changes » (ou la version 1 du registre). Si rien n'est obtenu : `null` et anomalie « information » expliquant ce qui n'a pas pu être vérifié.
4. Comparer les critères de la version courante du registre avec le protocole v1 et l'article (outcome switching) ; comparer `enrollment` au N randomisé.

**PubMed**
1. `search_articles` avec le NCT (`"NCTxxxxxxxx"`), puis l'acronyme et la molécule (`acronyme AND molécule`), sans filtre de date.
2. `get_article_metadata(pmids)` → `publication_date` (date d'epub), `article_types` (« Clinical Trial Protocol » → PUB_PROTOCOLE ; « Randomized Controlled Trial » avec résultats → article principal ou publication secondaire), DOI, journal, volume/pages (date du numéro → PUB).
3. Si un PMCID existe (`convert_article_ids`), `get_full_text_article` pour lire les dates *Received/Accepted* (SOUMISSION de l'article principal si disponible) et les informations de recrutement, gel de base ou levée d'aveugle.
4. `find_related_articles` sur le PMID principal pour repérer d'autres publications secondaires (sous-études, post hoc).
5. Obligation d'attribution : citer PubMed et donner les liens DOI de chaque article utilisé, dans le rapport et dans la réponse.

**Si un connecteur est absent** : le signaler, proposer de le connecter (SearchMcpRegistry → SuggestConnectors), puis se rabattre sur WebSearch/WebFetch et sur les documents fournis par l'utilisateur.

## 2. Saisie et exécution du script

Créer `jalons.json` :

```json
{"etude": "NOM (NCTxxxxxxxx)", "n_randomises": 800, "double_aveugle": true,
 "jalons": [
  {"type": "PROTOCOLE", "date": "2017-11-02", "version": "1.0", "source": "Protocole p.1"},
  {"type": "AMENDEMENT", "numero": "1", "date": "2018-01-10", "version": "2.0", "majeur": false, "source": "Historique du protocole"},
  {"type": "REG_INIT", "date": "2018-05-20", "source": "CT.gov v1 (export History of Changes fourni par l'utilisateur)"},
  {"type": "FPI", "date": "2018-03-12", "source": "CT.gov ; article p.3"},
  {"type": "SAP", "date": "2018-09", "version": "1.0", "source": "Suppl. SAP"},
  {"type": "AMENDEMENT", "numero": "3", "date": "2019-06-15", "version": "4.0",
   "changements": ["Critère principal : SSP seule → SSP et SG co-principaux",
                   "Alpha : 0,05 sur SSP → 0,01 SSP / 0,04 SG",
                   "Effectif : 600 → 800 patients (justification : puissance pour la SG)",
                   "Analyses intermédiaires : ajout d'une AI de SG à 60 % des événements (O'Brien-Fleming)"],
   "n_inclus": 412, "n_source": "résumé des amendements", "source": "Suppl. protocole p.12"},
  {"type": "REG_MODIF", "date": "2019-07-01", "libelle": "Critère principal modifié", "majeur": true, "source": "CT.gov v7"},
  {"type": "LPI", "date": "2020-02-28"},
  {"type": "DCO_IA", "date": "2020-11-30", "court": "DCO AI (SSP)", "critere": "SSP"},
  {"type": "DCO_FINAL", "date": "2022-08-01", "critere": "SG", "critere_principal": true},
  {"type": "REG_PCD", "date": "2022-08-01", "source": "Connecteur ClinicalTrials.gov"},
  {"type": "DBL", "date": "2022-09-15", "source": "EPAR p.40"},
  {"type": "UNBLIND", "date": "2022-09-20", "source": "CSR §9.4.6"},
  {"type": "PUB_PROTOCOLE", "date": "2019-09-11", "libelle": "Dupont et al., BMJ Open (design)", "pmid": "30000000", "doi": "10.xxxx/yyyy",
   "apport": "Critère principal SSP seul ; N = 600", "discordance": "Publié après l'amendement 3 mais décrit la SSP comme seul critère principal"},
  {"type": "PUB_SECONDAIRE", "date": "2021-03-02", "libelle": "Martin et al. (caractéristiques initiales)", "nature": "description de la population incluse",
   "pmid": "31111111", "doi": "10.xxxx/zzzz", "apport": "Inclusions mars 2018 – février 2020 ; N = 800", "resultats_comparatifs": false},
  {"type": "LPV", "date": null}
 ],
 "deviations": [
  {"domaine": "Analyse – population", "prevu": "Analyse principale en ITT (tous randomisés)", "source_prevu": "SAP v2.0 §6.1",
   "realise": "Analyse en mITT excluant les patients sans évaluation post-inclusion", "source_realise": "Article, Méthodes ; Fig. S1",
   "ampleur": "37 patients exclus : 25 (6,3 %) bras expérimental vs 12 (3,0 %) contrôle",
   "reconnue": "Partiellement", "explication": "Patients sans évaluation tumorale exploitable",
   "appreciation": "Non justifiée ou inacceptable",
   "commentaire": "Décision après DCO, exclusions déséquilibrées ; analyse ITT non fournie", "gravite": "critique"}
 ],
 "anomalies_analyste": [
  {"severite": "majeure", "controle": "A1-discordance", "message": "...", "jalons": ["J3"]}
 ]}
```

Champs : `type` (code ci-dessus, obligatoire), `date`, `libelle`, `court` (étiquette du graphique), `source`, `version`, `note`, `id` (sinon J1, J2… selon l'ordre), `majeur` (REG_MODIF, AMENDEMENT), `numero`/`changements`/`n_inclus`/`n_source` (AMENDEMENT), `critere`/`critere_principal` (DCO), `pmid`/`doi`/`nature`/`apport`/`discordance`/`gravite_discordance`/`resultats_comparatifs` (PUB_PROTOCOLE, PUB_SECONDAIRE). Champ d'étude `double_aveugle` (true/false) : module la gravité d'un SAP postérieur au DCO quand la levée d'aveugle n'est pas datée. Liste `deviations` : `domaine`, `prevu`, `source_prevu`, `realise`, `source_realise`, `ampleur`, `reconnue` (Oui / Partiellement / Non), `explication` (texte des auteurs ou `null`), `appreciation` (Justifiée et acceptable / Justification plausible, impact non exclu / Non justifiée ou inacceptable / Non évaluable), `commentaire`, `gravite` (critique / majeure / mineure).

Écrire le script ci-dessous dans `chronologie.py`, puis :

```bash
python3 chronologie.py jalons.json sortie/
```

Sorties : `timeline.png`/`.svg` (couloirs Documents, Registre, Conduite, Analyses, Diffusion ; bande d'inclusion FPI→LPI, puis bande de suivi LPI→LPV en aplat très clair, sans hachures, pour garder lisibles les symboles qui s'y trouvent ; ligne pointillée au 1er DCO ; ligne mixte rouge à la levée de l'aveugle ; jalons en anomalie critique en rouge, majeure en orange ; dates imprécises en barre grisée), `tableau_jalons.md` (chronologique, avec renvoi aux n° d'anomalies), `tableau_amendements.md` (synthèse des amendements), `tableau_deviations.md` (déviations majeures et critiques, avec un décompte), `tableau_publications.md` (publication du design et publications secondaires, position par rapport à la fin des inclusions, au gel de base et à la levée d'aveugle), `controles_auto.md/.json` (les déviations critiques et majeures y sont reprises sous le code D). Regarder l'image (Read) avant de la livrer ; si des étiquettes se chevauchent, raccourcir les `court`.

```python
#!/usr/bin/env python3
"""Chronologie d'un ECR : contrôles automatiques de cohérence + tableaux + timeline.
Usage : python3 chronologie.py jalons.json [dossier_sortie]
Produit : timeline.png/.svg, tableau_jalons.md, tableau_amendements.md, tableau_deviations.md,
          tableau_publications.md, controles_auto.md/.json
"""
import json, sys, calendar, os
from datetime import date

TYPES = {  # code: (libellé par défaut, couloir, marqueur)
    "PROTOCOLE": ("Protocole", "Documents", "s"),
    "PUB_PROTOCOLE": ("Publication du protocole/design", "Documents", "D"),
    "SAP": ("SAP", "Documents", "^"),
    "REG_INIT": ("Enregistrement initial au registre", "Registre", "o"),
    "REG_MODIF": ("Modification du registre", "Registre", "|"),
    "REG_PCD": ("Primary completion déclarée au registre", "Registre", "1"),
    "FPI": ("Inclusion du 1er patient", "Conduite", ">"),
    "LPI": ("Inclusion du dernier patient", "Conduite", "<"),
    "LPV": ("Fin d'étude (dernière visite)", "Conduite", "X"),
    "AMENDEMENT": ("Amendement", "Conduite", "*"),
    "DCO_IA": ("DCO analyse intermédiaire", "Analyses", "v"),
    "DCO_FINAL": ("DCO analyse finale", "Analyses", "P"),
    "DBL": ("Gel de la base de données", "Analyses", "s"),
    "UNBLIND": ("Levée de l'aveugle", "Analyses", "o"),
    "PRESS": ("Communiqué de presse", "Diffusion", "p"),
    "PRESENTATION": ("1re présentation (congrès)", "Diffusion", "h"),
    "SOUMISSION": ("Soumission du manuscrit", "Diffusion", "d"),
    "EPUB": ("Publication en ligne (epub)", "Diffusion", "8"),
    "PUB": ("Publication définitive", "Diffusion", "H"),
    "PUB_SECONDAIRE": ("Publication secondaire", "Diffusion", "^"),
}
COURT = {"PUB_PROTOCOLE": "Pub. protocole", "REG_INIT": "Enregistrement", "REG_MODIF": "Modif. registre",
         "FPI": "1re inclusion", "LPI": "Dernière inclusion", "LPV": "Dernière visite", "DCO_IA": "DCO AI",
         "DCO_FINAL": "DCO final", "PRESS": "Communiqué", "PRESENTATION": "Présentation", "SOUMISSION": "Soumission",
         "EPUB": "Epub", "PUB": "Publication",
         "REG_PCD": "PCD registre", "DBL": "Gel de base", "UNBLIND": "Levée aveugle", "PUB_SECONDAIRE": "Pub. secondaire"}
LANES = ["Documents", "Registre", "Conduite", "Analyses", "Diffusion"]
SEV_ORDER = {"critique": 0, "majeure": 1, "mineure": 2, "information": 3, "indéterminé": 4}


def interval(s):
    """'2019-03-12' | '2019-03' | '2019' -> (lo, hi, precision)."""
    p = [int(x) for x in str(s).split("-")]
    if len(p) == 3:
        d = date(*p); return d, d, "jour"
    if len(p) == 2:
        last = calendar.monthrange(p[0], p[1])[1]
        return date(p[0], p[1], 1), date(p[0], p[1], last), "mois"
    return date(p[0], 1, 1), date(p[0], 12, 31), "année"


def mid(j):
    return j["_lo"] + (j["_hi"] - j["_lo"]) / 2


def before(a, b):
    """True si a<=b certain, False si a>b certain, None si indéterminé (précision)."""
    if a["_hi"] <= b["_lo"]: return True
    if a["_lo"] > b["_hi"]: return False
    return None


def gap(a, b):
    return (mid(b) - mid(a)).days


def fmt(j):
    return f'{j["libelle"]} ({j["date"]})'


def load(path):
    data = json.load(open(path, encoding="utf-8"))
    js = []
    for i, j in enumerate(data["jalons"], 1):
        j = dict(j); j.setdefault("id", f"J{i}")
        if j["type"] not in TYPES: raise SystemExit(f"type inconnu : {j['type']}")
        if j["type"] == "AMENDEMENT" and j.get("numero"):
            j.setdefault("libelle", f"Amendement {j['numero']}"); j.setdefault("court", f"Amdt {j['numero']}")
        j.setdefault("libelle", TYPES[j["type"]][0])
        if not j.get("date"):
            j["_missing"] = True; js.append(j); continue
        j["_lo"], j["_hi"], j["_prec"] = interval(j["date"])
        js.append(j)
    return data, js


def checks(data, js):
    dated = [j for j in js if not j.get("_missing")]
    by = lambda t: sorted([j for j in dated if j["type"] == t], key=mid)
    first = lambda t: (by(t) or [None])[0]
    out = []

    def add(sev, code, msg, ids):
        out.append({"severite": sev, "controle": code, "message": msg, "jalons": ids})

    def order(a, b, sev, code, msg_bad):
        if not a or not b: return
        r = before(a, b)
        if r is False:
            add(sev, code, f"{msg_bad} : {fmt(a)} postérieur à {fmt(b)} (+{-gap(a, b)} j).", [a["id"], b["id"]])
        elif r is None:
            add("indéterminé", code, f"Ordre non vérifiable (précision des dates) : {fmt(a)} vs {fmt(b)}.", [a["id"], b["id"]])

    fpi, lpi, lpv = first("FPI"), first("LPI"), first("LPV")
    reg, proto = first("REG_INIT"), first("PROTOCOLE")
    dcos = by("DCO_IA") + by("DCO_FINAL"); dcos.sort(key=mid)
    dco1 = dcos[0] if dcos else None
    press, pres = first("PRESS"), first("PRESENTATION")
    soum, epub, pub = first("SOUMISSION"), first("EPUB"), first("PUB")
    dbl, unb, pcd = first("DBL"), first("UNBLIND"), first("REG_PCD")

    # 1. Impossibilités chronologiques de la conduite
    order(fpi, lpi, "critique", "C01-conduite", "Dernière inclusion avant la première")
    order(lpi, lpv, "critique", "C02-conduite", "Dernière visite avant la dernière inclusion")
    order(fpi, dco1, "critique", "C03-DCO", "DCO antérieur à la première inclusion")
    for ia in by("DCO_IA"):
        for fi in by("DCO_FINAL"):
            if ia.get("critere") and fi.get("critere") and ia["critere"] != fi["critere"]: continue
            order(ia, fi, "majeure", "C04-DCO", "DCO intermédiaire postérieur au DCO final")
    for fi in by("DCO_FINAL"):
        if lpv and before(lpv, fi) is False and fi.get("critere_principal"):
            add("information", "C05-DCO",
                f"DCO final du critère principal ({fi['date']}) antérieur à la fin d'étude ({lpv['date']}) : "
                "attendu pour un critère événementiel, vérifier qu'il s'agit bien de l'analyse prévue.", [fi["id"], lpv["id"]])

    # 2. Enregistrement prospectif
    if reg and fpi:
        r = before(reg, fpi)
        if r is False:
            d = -gap(reg, fpi)
            sev = "majeure" if d > 21 else "mineure"
            add(sev, "C10-registre",
                f"Enregistrement rétrospectif : {d} j après la 1re inclusion (ICMJE/OMS : au plus tard à la 1re inclusion ; FDAAA : ≤ 21 j).",
                [reg["id"], fpi["id"]])
        elif r is None:
            add("indéterminé", "C10-registre", "Caractère prospectif de l'enregistrement non vérifiable (précision).", [reg["id"], fpi["id"]])
    order(proto, fpi, "majeure", "C11-protocole", "Protocole (1re version) daté après la 1re inclusion")

    # 3. Modifications du registre
    for m in by("REG_MODIF"):
        if not m.get("majeur"): continue
        ctx = []
        for ref, lab in ((fpi, "1re inclusion"), (lpi, "fin des inclusions"), (dco1, "1er DCO"), (unb, "levée de l'aveugle"), (press, "communiqué")):
            if ref and before(ref, m) is True: ctx.append(lab)
        if not ctx: continue
        sev = "critique" if ({"1er DCO", "levée de l'aveugle", "communiqué"} & set(ctx)) else "majeure"
        add(sev, "C12-registre", f"Modification majeure du registre ({m['date']} – {m['libelle']}) postérieure à : {', '.join(ctx)}.", [m["id"]])

    # 4. SAP
    saps = by("SAP")
    unb0 = first("UNBLIND")
    if saps and dco1 and not unb0:  # si la levée d'aveugle est datée, c'est C63 qui s'applique
        aveugle = data.get("double_aveugle", False)
        for s in saps:
            if before(dco1, s) is True:
                sev = "majeure" if (aveugle or s is not saps[-1]) else "critique"
                add(sev, "C20-SAP",
                    f"Version du SAP ({s.get('version', '?')}, {s['date']}) postérieure au 1er DCO ({dco1['date']}) ; levée d'aveugle non datée : "
                    + ("essai en double aveugle, acceptable seulement si le SAP précède la levée d'aveugle (à vérifier)." if aveugle
                       else "vérifier l'absence d'accès aux données comparatives avant finalisation."), [s["id"], dco1["id"]])
    if saps and fpi and before(fpi, saps[0]) is True:
        add("information", "C21-SAP", f"1re version du SAP postérieure à la 1re inclusion ({saps[0]['date']}) – fréquent, acceptable si finalisé avant toute analyse.", [saps[0]["id"]])
    if not saps:
        add("majeure", "C22-SAP", "Aucune date de SAP disponible : finalisation avant analyse non vérifiable.", [])

    # 5. Publication du protocole
    pp = first("PUB_PROTOCOLE")
    if pp:
        if dco1 and before(dco1, pp) is True:
            add("majeure", "C30-pubproto", "Protocole/design publié après le 1er DCO : n'apporte pas de garantie de pré-spécification.", [pp["id"], dco1["id"]])
        elif lpi and before(lpi, pp) is True:
            add("mineure", "C30-pubproto", "Protocole/design publié après la fin des inclusions.", [pp["id"], lpi["id"]])

    # 6. Amendements
    n_tot = data.get("n_randomises")
    for a in by("AMENDEMENT"):
        if fpi and before(a, fpi) is True:
            a["_statut"] = "avant 1re inclusion"; a["_sev"] = "–"; continue
        if lpi and before(lpi, a) is True:
            a["_statut"] = "inclusions terminées"
        elif lpi and before(lpi, a) is None:
            a["_statut"] = "fin des inclusions : indéterminé (précision)"
        else:
            n = a.get("n_inclus")
            txt = f"{n} patients inclus" if n is not None else "nombre d'inclus NON RAPPORTÉ"
            if n is not None and n_tot: txt += f" ({100 * n / n_tot:.0f} % de {n_tot})"
            if a.get("n_source"): txt += f" [{a['n_source']}]"
            a["_statut"] = "inclusions en cours – " + txt
        if a.get("majeur") is False: continue
        sev = "majeure"
        post = [lab for ref, lab in ((dco1, "1er DCO"), (dbl, "gel de base"), (unb, "levée de l'aveugle")) if ref and before(ref, a) is True]
        if post: sev = "critique"
        a["_sev"] = sev
        add(sev, "C40-amendement", f"Amendement majeur après la 1re inclusion ({a['date']} – {a['libelle']}) : {a['_statut']}"
            + (f" ; POSTÉRIEUR à : {', '.join(post)}" if post else "") + ".", [a["id"]])

    # 6b. Gel de base, levée d'aveugle, primary completion du registre
    order(lpv, dbl, "majeure", "C60-DBL", "Gel de base antérieur à la dernière visite")
    for d in by("DCO_IA") + by("DCO_FINAL"):
        if dbl and d.get("critere_principal"): order(d, dbl, "majeure", "C61-DBL", "Gel de base antérieur au DCO de l'analyse principale")
    order(dbl, unb, "critique", "C62-aveugle", "Levée de l'aveugle antérieure au gel de base")
    if saps and unb:
        s = saps[-1]; r = before(s, unb)
        if r is False:
            add("critique", "C63-SAP", f"Version finale du SAP ({s.get('version', '?')}, {s['date']}) postérieure à la levée de l'aveugle ({unb['date']}).", [s["id"], unb["id"]])
        elif r is None:
            add("indéterminé", "C63-SAP", f"Ordre SAP final / levée de l'aveugle non vérifiable (précision) : {s['date']} vs {unb['date']}.", [s["id"], unb["id"]])
        elif dbl and before(dbl, s) is True:
            add("mineure", "C64-SAP", f"SAP final ({s['date']}) postérieur au gel de base ({dbl['date']}) mais antérieur à la levée de l'aveugle.", [s["id"], dbl["id"]])
    if saps and not unb:
        add("information", "C65-aveugle", "Date de levée de l'aveugle non rapportée : antériorité du SAP final vérifiée seulement par rapport au DCO.", [saps[-1]["id"]])
    for x, lab in ((press, "Communiqué"), (pres, "Présentation"), (soum, "Soumission")):
        if x and unb: order(unb, x, "critique", "C66-aveugle", f"{lab} antérieur(e) à la levée de l'aveugle")
    ref_pc = next((d for d in sorted(by("DCO_FINAL"), key=mid) if d.get("critere_principal")), None) or first("DCO_FINAL")
    if pcd and ref_pc:
        g = abs(gap(pcd, ref_pc))
        if g > 31:
            add("mineure", "C67-registre", f"Primary completion du registre ({pcd['date']}) ≠ DCO de l'analyse principale ({ref_pc['date']}) : écart {g} j.", [pcd["id"], ref_pc["id"]])

    # 6c. Publications secondaires
    for ps in by("PUB_SECONDAIRE") + by("PUB_PROTOCOLE"):
        pos = [lab for ref, lab in ((lpi, "fin des inclusions"), (dbl, "gel de base"), (unb, "levée de l'aveugle"), (epub or pub, "publication principale")) if ref and before(ref, ps) is True]
        ps["_statut"] = ("après " + ", ".join(pos)) if pos else "avant la fin des inclusions"
        if ps.get("resultats_comparatifs") and (epub or pub) and before(ps, epub or pub) is True:
            add("majeure", "C70-pubsec", f"Publication secondaire avec résultats comparatifs ({ps['date']} – {ps['libelle']}) antérieure à la publication principale.", [ps["id"]])
        if ps.get("discordance"):
            add(ps.get("gravite_discordance", "majeure"), "C71-pubsec", f"Discordance publication secondaire ({ps['libelle']}) vs protocole/article : {ps['discordance']}", [ps["id"]])

    # 7. Diffusion
    for x, lab in ((press, "Communiqué"), (pres, "Présentation"), (soum, "Soumission"), (epub, "Epub"), (pub, "Publication")):
        if x and dco1: order(dco1, x, "critique", "C50-diffusion", f"{lab} antérieur(e) au 1er DCO")
    order(soum, epub, "critique", "C51-diffusion", "Epub antérieur à la soumission")
    order(epub, pub, "majeure", "C52-diffusion", "Publication définitive antérieure à l'epub")
    if press and dcos:
        ref = max([d for d in dcos if before(d, press) is not False], key=mid, default=None)
        if ref:
            g = gap(ref, press)
            add("information", "C53-diffusion", f"Délai DCO ({ref['date']}) → communiqué : {g} j" +
                (" (très court : résultats annoncés avant analyse complète ?)" if g < 30 else "") + ".", [ref["id"], press["id"]])
    if soum and epub:
        add("information", "C54-diffusion", f"Délai soumission → epub : {gap(soum, epub)} j.", [soum["id"], epub["id"]])
    if pres and epub and before(pres, epub) is True:
        add("information", "C55-diffusion", f"Délai présentation → epub : {gap(pres, epub)} j.", [pres["id"], epub["id"]])

    # 8. Jalons manquants
    miss = [j["libelle"] for j in js if j.get("_missing")]
    present = {j["type"] for j in dated}
    for t in ("PROTOCOLE", "SAP", "REG_INIT", "FPI", "LPI", "DCO_FINAL", "DBL", "UNBLIND"):
        if t not in present and TYPES[t][0] not in miss: miss.append(TYPES[t][0])
    if miss:
        add("information", "C90-manquants", "Jalons non datés / non retrouvés : " + "; ".join(miss) + ".", [])

    # 9. Déviations critiques et majeures
    for k, dv in enumerate(data.get("deviations", []), 1):
        if dv.get("gravite") in ("critique", "majeure"):
            add(dv["gravite"], f"D{k:02d}-déviation", f"{dv.get('domaine', '')} : prévu « {dv.get('prevu', '')} » → réalisé « {dv.get('realise', '')} » ; "
                f"reconnue : {dv.get('reconnue', 'NR')} ; appréciation : {dv.get('appreciation') or ('Non évaluable – non expliquée par les auteurs' if not dv.get('explication') else 'NR')}.", [])

    # 10. Anomalies ajoutées par l'analyste
    for a in data.get("anomalies_analyste", []):
        add(a.get("severite", "majeure"), a.get("controle", "A-analyste"), a["message"], a.get("jalons", []))

    out.sort(key=lambda c: SEV_ORDER.get(c["severite"], 9))
    for i, c in enumerate(out, 1): c["n"] = i
    return out


def table(js, chks):
    flag = {}
    for c in chks:
        if c["severite"] in ("critique", "majeure", "mineure"):
            for i in c["jalons"]: flag.setdefault(i, []).append(str(c["n"]))
    rows = ["| # | Jalon | Date | Précision | Source(s) | Statut / remarque | Anomalies |", "|---|---|---|---|---|---|---|"]
    dated = sorted([j for j in js if not j.get("_missing")], key=mid) + [j for j in js if j.get("_missing")]
    for k, j in enumerate(dated, 1):
        rem = j.get("_statut") or j.get("note", "")
        rows.append(f"| {k} | {j['libelle']} | {j.get('date') or 'NR'} | {j.get('_prec', '–')} | {j.get('source', '')} | {rem} | {', '.join(flag.get(j['id'], []))} |")
    return "\n".join(rows)


def table_amendements(js, data):
    am = sorted([j for j in js if j["type"] == "AMENDEMENT"], key=lambda j: mid(j) if not j.get("_missing") else date.max)
    if not am:
        return "_Aucun amendement identifié._"
    rows = ["| N° | Date | Version du protocole | Inclusions au moment de l'amendement | Changements méthodologiques / statistiques | Gravité | Source |",
            "|---|---|---|---|---|---|---|"]
    for a in am:
        ch = a.get("changements") or []
        cell = "<br>".join(f"• {c}" for c in ch) if ch else "Aucun changement méthodologique ou statistique (administratif / sécurité)"
        sev = a.get("_sev", "–") if a.get("majeur") is not False else "non majeur"
        rows.append(f"| {a.get('numero', '?')} | {a.get('date') or 'NR'} | {a.get('version', '')} | {a.get('_statut', '–')} | {cell} | {sev} | {a.get('source', '')} |")
    return "\n".join(rows)


def table_publications(js):
    pubs = sorted([j for j in js if j["type"] in ("PUB_PROTOCOLE", "PUB_SECONDAIRE") and not j.get("_missing")], key=mid)
    if not pubs:
        return "_Aucune publication secondaire identifiée._"
    rows = ["| Date (epub) | Publication | Nature | Position chronologique | Informations utiles / cohérence avec le protocole | Référence |",
            "|---|---|---|---|---|---|"]
    for p in pubs:
        nat = p.get("nature") or ("design / protocole" if p["type"] == "PUB_PROTOCOLE" else "NR")
        info = p.get("apport", "")
        if p.get("discordance"): info += f"<br>**Discordance** : {p['discordance']}"
        ref = " ; ".join(x for x in (f"PMID {p['pmid']}" if p.get("pmid") else "", f"[doi:{p['doi']}](https://doi.org/{p['doi']})" if p.get("doi") else "", p.get("source", "")) if x)
        rows.append(f"| {p['date']} | {p['libelle']} | {nat} | {p.get('_statut', '')} | {info} | {ref} |")
    return "\n".join(rows)


def table_deviations(data):
    devs = [d for d in data.get("deviations", []) if d.get("gravite") in ("critique", "majeure")]
    devs.sort(key=lambda d: SEV_ORDER.get(d.get("gravite"), 9))
    if not devs:
        return "_Aucune déviation majeure ou critique identifiée._"
    rows = ["| N° | Domaine | Prévu (source) | Réalisé (source) | Ampleur | Reconnue par les auteurs | Explication des auteurs | Appréciation | Gravité |",
            "|---|---|---|---|---|---|---|---|---|"]
    for k, d in enumerate(devs, 1):
        expl = d.get("explication") or "Aucune"
        appr = d.get("appreciation") or ("Non évaluable – non expliquée par les auteurs" if not d.get("explication") else "NR")
        if d.get("commentaire"): appr += f"<br>_{d['commentaire']}_"
        rows.append(f"| {k} | {d.get('domaine', '')} | {d.get('prevu', '')} ({d.get('source_prevu', 'NR')}) | {d.get('realise', '')} ({d.get('source_realise', 'NR')}) | "
                    f"{d.get('ampleur', 'NR')} | {d.get('reconnue', 'NR')} | {expl} | {appr} | {d.get('gravite')} |")
    n = len(devs); rec = sum(d.get("reconnue") == "Oui" for d in devs)
    part = sum(d.get("reconnue") == "Partiellement" for d in devs); expl = sum(bool(d.get("explication")) for d in devs)
    acc = sum(d.get("appreciation") == "Justifiée et acceptable" for d in devs)
    rows.append("")
    rows.append(f"**Décompte** : {n} déviation(s) majeure(s)/critique(s) ; reconnues : {rec} totalement, {part} partiellement, {n - rec - part} non reconnues ; "
                f"expliquées : {expl} ; jugées justifiées et acceptables : {acc}.")
    return "\n".join(rows)


def plot(data, js, chks, outdir):
    import matplotlib; matplotlib.use("Agg")
    import matplotlib.pyplot as plt, matplotlib.dates as md
    from matplotlib.lines import Line2D
    dated = [j for j in js if not j.get("_missing")]
    bad = {}
    for c in chks:
        if c["severite"] in ("critique", "majeure"):
            for i in c["jalons"]: bad.setdefault(i, c["severite"])
    col = {"Documents": "#4C72B0", "Registre": "#8172B2", "Conduite": "#55A868", "Analyses": "#8C564B", "Diffusion": "#64B5CD"}
    fig, ax = plt.subplots(figsize=(15, 7.5))
    y = {l: len(LANES) - i for i, l in enumerate(LANES)}
    by = lambda t: sorted([j for j in dated if j["type"] == t], key=mid)
    fpi, lpi, lpv = (by("FPI") or [None])[0], (by("LPI") or [None])[0], (by("LPV") or [None])[0]
    if fpi and lpi:
        ax.barh(y["Conduite"], (mid(lpi) - mid(fpi)).days, left=mid(fpi), height=0.35, color="#55A868", alpha=.25, label="Période d'inclusion")
    if lpi and lpv:
        ax.barh(y["Conduite"], (mid(lpv) - mid(lpi)).days, left=mid(lpi), height=0.35, color="#55A868", alpha=.07, label="Suivi après fin des inclusions")
    for x in (fpi, lpi):
        if x: ax.axvline(mid(x), color="#55A868", ls="--", lw=1, alpha=.6)
    dco1 = min(by("DCO_IA") + by("DCO_FINAL"), key=mid, default=None)
    if dco1: ax.axvline(mid(dco1), color="#8C564B", ls=":", lw=1.2, alpha=.8)
    unb = (by("UNBLIND") or [None])[0]
    if unb: ax.axvline(mid(unb), color="#C0392B", ls="-.", lw=1.2, alpha=.7, label="Levée de l'aveugle")
    counters = {l: 0 for l in LANES}; last = {}
    for j in sorted(dated, key=mid):
        lab, lane, mk = TYPES[j["type"]]
        yy = y[lane]
        c = {"critique": "#C0392B", "majeure": "#E67E22"}.get(bad.get(j["id"]), col[lane])
        ec = "black" if j["id"] in bad else "white"
        if j["_prec"] != "jour":
            ax.plot([j["_lo"], j["_hi"]], [yy, yy], color=c, lw=4, alpha=.35, solid_capstyle="butt")
        ax.scatter(mid(j), yy, marker=mk, s=110, color=c, edgecolors=(None if mk in "|_1" else ec), linewidths=(2 if mk in "|_1" else 1), zorder=5)
        prev = last.get(lane)
        k = counters[lane] + 1 if (prev is not None and (mid(j) - prev).days < 120) else 0
        counters[lane] = k; last[lane] = mid(j)
        off = [0.25, -0.30, 0.55, -0.60][k % 4]
        txt = j.get("court") or COURT.get(j["type"], j["libelle"])
        ax.annotate(f"{txt}\n{j['date']}", (mid(j), yy), xytext=(0, off * 60), textcoords="offset points",
                    ha="center", va="bottom" if off > 0 else "top", fontsize=7, color=c,
                    arrowprops=dict(arrowstyle="-", color="#999", lw=.5))
    ax.set_yticks(list(y.values())); ax.set_yticklabels(list(y.keys()), fontsize=10)
    ax.set_ylim(0.3, len(LANES) + 0.8)
    ax.xaxis.set_major_locator(md.YearLocator()); ax.xaxis.set_minor_locator(md.MonthLocator((1, 4, 7, 10)))
    ax.xaxis.set_major_formatter(md.DateFormatter("%Y"))
    ax.grid(axis="x", which="major", alpha=.3)
    for s in ("top", "right", "left"): ax.spines[s].set_visible(False)
    ax.set_title(f"Chronologie des jalons – {data.get('etude', '')}", fontsize=13, loc="left")
    h = [Line2D([], [], marker="o", ls="", color="#C0392B", label="Anomalie critique"),
         Line2D([], [], marker="o", ls="", color="#E67E22", label="Anomalie majeure"),
         Line2D([], [], color="grey", lw=4, alpha=.35, label="Date imprécise (mois/année)")]
    hh, _ = ax.get_legend_handles_labels()
    ax.legend(handles=hh + h, loc="upper left", fontsize=8, frameon=False, ncol=5, bbox_to_anchor=(0, -0.06))
    fig.tight_layout()
    for ext in ("png", "svg"):
        fig.savefig(os.path.join(outdir, f"timeline.{ext}"), dpi=160)


def main():
    path = sys.argv[1]; outdir = sys.argv[2] if len(sys.argv) > 2 else "."
    os.makedirs(outdir, exist_ok=True)
    data, js = load(path)
    chks = checks(data, js)
    open(os.path.join(outdir, "tableau_jalons.md"), "w").write(table(js, chks) + "\n")
    open(os.path.join(outdir, "tableau_amendements.md"), "w").write(table_amendements(js, data) + "\n")
    open(os.path.join(outdir, "tableau_deviations.md"), "w").write(table_deviations(data) + "\n")
    open(os.path.join(outdir, "tableau_publications.md"), "w").write(table_publications(js) + "\n")
    lines = ["| N° | Sévérité | Contrôle | Constat |", "|---|---|---|---|"]
    lines += [f"| {c['n']} | {c['severite']} | {c['controle']} | {c['message']} |" for c in chks]
    open(os.path.join(outdir, "controles_auto.md"), "w").write("\n".join(lines) + "\n")
    json.dump(chks, open(os.path.join(outdir, "controles_auto.json"), "w"), ensure_ascii=False, indent=1)
    plot(data, js, chks, outdir)
    print(table(js, chks)); print(); print(table_amendements(js, data)); print(); print(table_deviations(data)); print(); print(table_publications(js)); print(); print("\n".join(lines))


if __name__ == "__main__":
    main()
```

## 3. Contrôles de cohérence

### Automatisés par le script
- C01–C05 : impossibilités chronologiques (LPI < FPI, LPV < LPI, DCO < FPI, DCO intermédiaire > DCO final pour un même critère).
- C10 : enregistrement rétrospectif (> 0 j = non conforme ICMJE/OMS ; > 21 j = non conforme FDAAA → majeure).
- C11 : protocole v1 daté après la 1re inclusion.
- C12 : modification majeure du registre après FPI (majeure), après le 1er DCO ou le communiqué (critique).
- C20–C22 : si la levée d'aveugle n'est pas datée, SAP postérieur au 1er DCO (majeure en double aveugle, critique en ouvert) ; SAP postérieur à la 1re inclusion (information) ; SAP absent.
- C30 : protocole publié après la fin des inclusions / après le 1er DCO.
- C40 : amendements majeurs après la 1re inclusion, avec statut des inclusions et nombre d'inclus ; critique si après un DCO, le gel de base ou la levée d'aveugle.
- C60–C67 : gel de base avant la dernière visite ou avant le DCO principal ; levée d'aveugle avant le gel de base (critique) ; SAP final après la levée d'aveugle (critique) ou après le gel de base (mineure) ; levée d'aveugle non datée (information) ; communiqué/présentation/soumission avant la levée d'aveugle (critique) ; primary completion du registre ≠ DCO principal de plus de 31 j (mineure).
- C70–C71 : publication secondaire avec résultats comparatifs avant la publication principale (majeure) ; discordance d'une publication secondaire avec le protocole ou l'article.
- C50–C55 : diffusion antérieure au DCO, epub avant soumission, délais DCO → communiqué, soumission → epub.
- C90 : jalons manquants.
- D01… : déviations critiques et majeures saisies dans `deviations`.

### À vérifier par l'analyste (à saisir dans `anomalies_analyste`)
1. Outcome switching : critère principal/secondaires du registre v1 et du protocole v1 vs publication (nature, délai d'évaluation, hiérarchie, co-primaires). Toute divergence non expliquée = majeure ; après DCO = critique.
2. Effectif : N prévu au registre v1 / protocole vs N randomisé ; augmentation de N ou du nombre d'événements en cours d'essai (justification ? à l'aveugle ?).
3. Dates du registre vs article : « Primary Completion » vs DCO de l'analyse principale ; « Study Start » vs FPI ; « Study Completion » vs LPV. Écart > 1 mois = discordance à signaler.
4. Proximité suspecte : amendement ou modification du registre dans les 3 mois suivant une analyse intermédiaire, une revue de données « à l'aveugle » ou la publication de résultats d'un essai concurrent.
5. Cohérence amendements ↔ registre ↔ SAP : chaque changement méthodologique d'un amendement doit se retrouver dans le registre et dans une version du SAP datée après l'amendement ; une modification du registre sans amendement correspondant est à signaler.
6. Analyses intermédiaires : DCO compatible avec la fraction d'information prévue ; arrêt précoce ; recommandations du DMC datées.
7. Suivi : médiane de suivi rapportée compatible avec (DCO − FPI) et (DCO − LPI).
8. Délais DCO → gel de base → levée d'aveugle → communiqué : un délai très court entre levée d'aveugle et communiqué est attendu ; un communiqué sans levée d'aveugle datée est à signaler.
9. Résultats publiés sur CT.gov ≤ 12 mois après Primary Completion (FDAAA 801) ; publication > 2 ans après DCO.
10. Discordances de date d'un même jalon entre sources (article, supplément, registre, EPAR, communiqué, publications secondaires).
11. Présentation/communiqué rapportant un DCO, un critère ou une analyse différents de la publication.
12. Déviations : voir § « Déviations au protocole et au SAP » ; les saisir dans `deviations` (pas dans `anomalies_analyste`).

### Gradation
- **Critique** : met directement en doute la pré-spécification (choix d'analyse/critère pouvant être informé par les données).
- **Majeure** : affaiblit la garantie de pré-spécification ou la transparence, sans preuve d'accès aux données.
- **Mineure** : écart formel sans impact plausible.
- **Information / indéterminé** : délais, données manquantes, précision insuffisante.
Toujours distinguer « anomalie démontrée » et « non vérifiable faute de données ».

## 4. Rapport (markdown, français)

```
# Chronologie et intégrité méthodologique – <Essai> (<NCT>)
## Synthèse (5–8 lignes) : verdict global (Intégrité préservée / Réserves mineures / Réserves majeures / Intégrité compromise), 3 constats clés, nombre de déviations majeures/critiques et part non reconnues ou non justifiées
## 1. Sources consultées (documents fournis ; connecteurs utilisés et ce qu'ils ont renvoyé ; versions du registre ; documents non disponibles)
## 2. Tableau des jalons (tableau_jalons.md)
## 3. Timeline (timeline.png)
## 4. Tableau de synthèse des amendements (tableau_amendements.md)
   Une ligne par amendement : N° | Date | Version du protocole | Inclusions au moment de l'amendement (terminées, ou n inclus et % de N) | Changements méthodologiques / statistiques en liste à puces | Gravité | Source.
   Suivi de 2–4 lignes de commentaire sur les amendements les plus lourds de conséquences (moment, justification, impact sur la pré-spécification).
## 5. Publications secondaires (tableau_publications.md)
   Une ligne par publication : date d'epub | référence (PMID, lien DOI) | nature | position (avant/après fin des inclusions, gel de base, levée d'aveugle, publication principale) | apport à la chronologie | cohérence avec le protocole et l'article.
## 6. Déviations majeures au protocole et au SAP (tableau_deviations.md)
   Tableau : N° | Domaine | Prévu (source) | Réalisé (source) | Ampleur | Reconnue par les auteurs | Explication des auteurs | Appréciation | Gravité, suivi du décompte.
   Puis, pour chaque déviation critique, un court paragraphe : en quoi elle peut modifier l'estimation ou la conclusion ; si elle est expliquée, analyse de la justification selon les 5 critères (indépendance vis-à-vis des données, motif, sens de l'impact, analyse de sensibilité, ampleur) ; verdict d'acceptabilité.
   Une phrase pour les déviations mineures.
## 7. Incohérences et points d'attention (controles_auto.md + analyse, du plus grave au moins grave ; pour chacune : constat, dates, sources, impact sur l'interprétation)
## 8. Évolution du registre (tableau : version | date | champ modifié | ancien → nouveau | position vs FPI/LPI/DCO | amendement correspondant ; à défaut d'historique, comparaison version courante du registre ↔ protocole v1 ↔ article)
## 9. Conclusion : crédibilité de la pré-spécification du critère principal et des analyses ; fidélité de la réalisation et de l'analyse au plan ; conséquences pour l'interprétation des résultats
```

Terminer le rapport par une liste « Sources » avec les liens DOI des articles PubMed et le lien de la fiche ClinicalTrials.gov.

Livrer le rapport et `timeline.png` dans `/mnt/user-data/outputs/` (ou dans le dossier connecté de l'utilisateur), plus `jalons.json` pour traçabilité.