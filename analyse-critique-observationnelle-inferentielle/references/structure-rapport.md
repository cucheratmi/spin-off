# Structure du rapport markdown — interprétation d'une étude observationnelle

Format cible : `.md`. Style sobre, orienté décideur (HTA / réglementaire).

## Page de garde

- Titre : « Interprétation de l'étude observationnelle [NOM / 1ᵉʳ auteur, année] »
- Sous-titre : référence bibliographique complète (auteurs, revue, année, DOI) + source(s) de données
- Question causale évaluée (1 phrase)
- Date de l'analyse
- Mention : « Document généré par IA »

## 1. Synthèse exécutive (1 page max)

À placer en premier — c'est ce que liront les décideurs pressés.

- **Question causale évaluée** : 1 phrase (PICO).
- **Verdict global** : les résultats sont-ils assez fiables pour fonder une décision / un changement de stratégie ? (Oui / Non / Pas en l'état.)
- **Une conclusion causale est-elle licite ?** Ou une réserve « confounding / no causality » reste-t-elle justifiée ?
- **Critères critiques non satisfaits** (les ❌) — les nommer.
- **Recommandation** (3-5 lignes) : usage décisionnel possible ou non, pour quelle population/place, sous quelles conditions.
- **Tableau de statut** synthétique des 13 critères (voir §6).

## 2. Description de l'étude

### 2.1 Contexte et question causale
- Pathologie, besoin médical, mécanisme d'action, place attendue dans la stratégie.
- Question causale explicite (PICOT) et **estimand visé** (ATE/ATT/ATC ; ITT « treatment policy » vs traitement reçu).

### 2.2 Source de données et design
- Source(s) : base administrative / DME / registre / données primaires ; période ; complétude ; caractère **prospectif ou rétrospectif**.
- Design comparatif (cohorte, cas-témoins, autocontrôlé, DiD, ITS).
- Sponsor / PI ; enregistrement du protocole / du SAP ; registre d'étude.

### 2.3 Stratégie d'analyse
- Méthode de prise en compte du confusion (ajustement, score de propension : matching/IPTW/stratification, g-computation, doublement robuste).
- Gestion de la multiplicité et du risque alpha global.
- Gestion des événements intercurrents et des données manquantes.
- Contrôles négatifs/positifs, analyses quantitatives de biais, analyses de sensibilité prévus.

## 3. Évaluation critère par critère (la checklist)

Reprendre les **critères** de `checklist-criteres-acceptabilite.md`. Pour chacun : **statut** (✅ / ⚠️ / ❌ / ❔) + justification (1 court paragraphe, renvoi table/figure/section), et les **drapeaux rouges** repérés.

## 4. Résultats et pertinence clinique

Pour chaque résultat servant potentiellement à la décision, tableau standardisé :

| Critère de jugement | Estimand & effet (relatif + absolu, IC95 %) | Significativité (risque alpha global) | Risque de biais résiduel | Pertinence clinique |
|---|---|---|---|---|
| … | … | … | … | … |

- Distinguer clairement les résultats **inférentiels** (pré-spécifiés, dans le plan d'alpha) des résultats **exploratoires / préparatoires**.
- Un résultat NS n'est **pas** une preuve d'absence d'effet.

## 5. Synthèse et conclusion orientée décision

Réponse explicite à :
1. Une **conclusion causale** est-elle licite pour cette étude, ou la réserve « results should be interpreted with caution because of confounding/selection bias » reste-t-elle justifiée (⇒ résultats non utilisables) ?
2. Les résultats sont-ils assez fiables pour **justifier un changement de stratégie thérapeutique** ? Pour quelle population, à quelle place ?
3. Quels **critères manquants/incertains** font obstacle, et que faudrait-il pour les lever (protocole/SAP enregistrés a priori, DAG issu d'une revue systématique, contrôles négatifs, E-value, émulation documentée point par point, synchronisation des t0, contrôle de l'alpha global, étude multibases / recherche des études similaires) ?
4. Si plusieurs études similaires existent : la décision doit s'appuyer sur leur **synthèse**, pas sur cette étude isolée.

## 6. Tableau récapitulatif de la checklist

| Critère | Statut | Point déterminant |
|---|---|---|---|
| Étude de confirmation | | |
| Absence de HARKing | | |
| Absence de p-hacking | | |
| Hypothèses d'inférence causale vérifiées | | |
| Raisonnement contrefactuel (design comparatif) | | |
| Émulation d'un essai cible | | |
| Estimand approprié (ATE + ITT) | | |
| Prise en compte de tous les FdC (DAG) | | |
| Biais de confusion résiduel négligeable | | |
| Risque de biais faible/modéré (ROBINS-I) | | |
| Contrôle strict du risque alpha global | | |
| Pertinence clinique | | |
| Absence de biais de publication / reporting | | |

**Verdict global** : … (rappel : un seul critère critique ❌ ⇒ résultats non utilisables à but décisionnel).

---

## Style éditorial

- Phrases courtes, vocabulaire précis. Pas de marketing ni d'emphase superflue.
- Chaque chiffre cité renvoie à une table/figure/section de l'étude.
- Les résultats NS sont « non démontrés », **pas** « équivalents ».
- Quand une donnée manque : « Non rapporté dans les documents fournis » — ne jamais combler.
- Ne pas reprendre la discussion/conclusion des auteurs comme argument ; s'appuyer sur les faits méthodologiques.
