---
name: analyse-critique-etude-observationnelle
description: Interpréter et évaluer une étude observationnelle (non randomisée, non interventionnelle) qui prétend démontrer l'efficacité ou le bénéfice clinique d'un traitement (technologie de santé), en vue d'une décision (HTA, réglementaire, remboursement, recommandations, stratégie thérapeutique)ou conclure si les résultats sont assez fiables pour justifier un changement de stratégie thérapeutique.
version: "0.1.0"
metadata:
  author: "Initiative SPIN-OFF"
  version: "1.0.0"
  url: "https://github.com/cucheratmi/_skills"
---

# Interprétation d'une étude observationnelle évaluant l'efficacité d'un traitement

## Objectif

Produire un rapport structuré qui répond à une seule question : **les résultats de cette étude observationnelle sont-ils suffisamment fiables — d'un niveau comparable à celui d'un essai randomisé — pour être pris en considération dans une décision** (accès au marché, remboursement/prix, recommandations, construction de la stratégie thérapeutique) ?

Le rapport applique **la checklist des critères d'acceptabilité méthodologiques** (voir `references/checklist-criteres-acceptabilite.md`), qui décline les attentes pour qu'une étude non randomisée, non interventionnelle, puisse servir d'étude **confirmatoire** de l'effet traitement.

## Principe axiomatique

Trois principes commandent toute l'évaluation :

1. **Preuve au-delà de tout doute raisonnable.** L'ouverture des agences aux études observationnelles n'abaisse pas le niveau de preuve exigé. Le doute profite au statu quo, pas au traitement évalué.

2. **Obligation de résultat, pas seulement de moyens.** Contrairement à l'ECR — qui évite la plupart des biais *par design* et dont la seule réalisation correcte garantit la fiabilité — dans l'étude observationnelle les biais sont présents à la source et on tente de les corriger par l'analyse. Il ne suffit donc pas de vérifier qu'une démarche de correction a été *mise en œuvre* : il faut démontrer qu'elle a **effectivement atteint son but** (p. ex. un biais de confusion résiduel négligeable, et non « seulement » une méthode de correction du confusion). Le succès dépend de l'adéquation des données aux **hypothèses de validité** des méthodes employées ; la justification de la plausibilité de ces hypothèses (analyse d'identification) est primordiale.

3. **Le critère des réserves.** Si une réserve du type « *causality cannot be affirmed* / *results should be interpreted with caution because of confounding* » est **licite** pour cette étude, alors l'étude n'est **pas** utilisable à but décisionnel. Seuls sont recevables les travaux où l'on peut, de façon légitime, s'abstenir de ce type de réserve et conclure à la causalité.

> **Nuance terminologique** — proscrire l'appellation « données de vie réelle / vraie vie ». Ce qui distingue fondamentalement ces études de l'ECR n'est pas la nature des données mais leur **caractère observationnel** (non randomisé). Préférer « données collectées en pratique courante ».

## Périmètre

- **Concerné** : études **comparatives** évaluant le bénéfice clinique et la sécurité d'une technologie de santé (médicament, dispositif, intervention), à visée confirmatoire/décisionnelle, avec **groupe de comparaison interne** (cohorte, cas-témoins, autocontrôlée, différence des différences, série temporelle interrompue…).
- **Hors périmètre** : études descriptives ou exploratoires ; études à **groupe contrôle externe** / comparaisons mono-bras (autre problématique). Le signaler si l'étude fournie relève de ces cas.

## Workflow

### Étape 1 — Lire intégralement l'étude

Lire le PDF de l'étude, son protocole, son plan d'analyse statistique (SAP) et ses suppléments s'ils sont fournis. Si rien n'est téléversé, demander les documents avant de continuer. Repérer au passage : source(s) de données, sponsor/PI, enregistrement (protocole, registre), revue, année, caractère prospectif ou **rétrospectif** (données préexistante au moment de la formulation de l'objectif — point critique pour le HARKing).

### Étape 2 — Reformuler la question et décrire l'étude

Expliciter la **question causale** (PICO(T)) puis décrire :
- **Source de données** : nature (base administrative, DME/EHR, registre maladie/traitement, données primaires), période, complétude.
- **Design** : cohorte / cas-témoins / autocontrôlé / DiD / ITS ; prospectif vs rétrospectif.
- **Estimand visé** : population cible (ATE / ATT / ATC), gestion des événements intercurrents (ITT « treatment policy » vs traitement reçu).
- **Méthode d'analyse** : ajustement multivariable, score de propension (matching / IPTW / stratification), g-computation / standardisation, doublement robuste, etc.
- **Contexte** : besoin médical, place attendue du traitement dans la stratégie.

### Étape 3 — Appliquer la checklist des critères d'acceptabilité

Lire `references/checklist-criteres-acceptabilite.md` et **statuer sur chacun des critères**, :

Pour chaque critère, attribuer un **statut** et le **justifier par les éléments observés** (avec renvoi table/figure/section) :

| Statut | Signification |
|---|---|
| ✅ **Satisfait** | L'attente est remplie et documentée. |
| ⚠️ **Partiel / incertain** | Démarche amorcée mais incomplète, non documentée, ou dont l'atteinte de l'objectif n'est pas démontrée. |
| ❌ **Non satisfait** | Attente non remplie, ou drapeau rouge présent. |
| ❔ **Non évaluable** | Information manquante dans les documents fournis (l'écrire ; ne pas combler). |

### Étape 4 — Synthèse et conclusion orientée décision

Les critères d'acceptabilité sont des **conditions nécessaires** : chaque recommandation du texte source dit de « ne prendre en considération pour la décision que des études pour lesquelles… ». En conséquence :

- **Un seul critère critique non satisfait** (❌) rend les résultats **inutilisables à but décisionnel**, quelle que soit la significativité affichée.
- Les critères ⚠️ ou ❔ appellent des documents complémentaires avant toute conclusion.

Rédiger une conclusion qui répond explicitement :
1. Les résultats permettent-ils une **conclusion causale** licite, ou une réserve type « confounding / no causality » reste-t-elle justifiée ?
2. Les résultats sont-ils assez fiables pour **justifier un changement de stratégie thérapeutique** ? Pour quelle population, à quelle place ?
3. Quels **critères manquants ou incertains** font obstacle, et que faudrait-il pour les lever (protocole/SAP enregistrés, DAG, contrôles négatifs, analyse quantitative de biais, émulation documentée, étude multibases, etc.) ?

### Étape 5 — Produire le rapport markdown

Suivre `references/structure-rapport.md`. Format cible `.md`, style sobre orienté décideur. Terminer par le **tableau récapitulatif de la checklist**.

## Règles non négociables

- **Juger le résultat, pas seulement la méthode.** Une méthode de correction correcte peut échouer (FdC non mesuré, modèle mal spécifié…). Exiger la **démonstration** que l'objectif est atteint (équilibre des covariables par SMD < 0,10 — *pas* la p-value —, contrôles négatifs plats, E-value suffisante…).
- **Ne pas se laisser convaincre par le vocabulaire.** « Émulation d'un essai cible », « inférence causale », « score de propension » sont souvent des **justifications de façade**. Vérifier la substance : synopsis de l'essai cible + colonne d'émulation point par point ; DAG ayant réellement servi à sélectionner les FdC (et non dessiné après coup) ; hypothèses d'identification argumentées.
- **HARKing / p-hacking = rédhibitoires pour une étude confirmatoire.** Exiger objectif, protocole et SAP **établis et enregistrés avant toute analyse inférentielle**, cohérence des dates, attestation des auteurs. Distinguer les analyses **préparatoires** (qualification de la source) de l'analyse **inférentielle**.
- **Rétrospectif = vigilance maximale** sur HARKing, p-hacking et biais de publication (plusieurs sources de données ⇒ plusieurs études possibles).
- **Comparaison contrefactuelle valide obligatoire.** Récuser les comparaisons **avant-après** / *change from baseline* et les études purement descriptives : elles n'isolent pas l'effet traitement.
- **Estimand.** Privilégier l'**ATE** (IPTW à poids appropriés, ou régression + standardisation/g-computation) plutôt que le matching (qui donne un ATT) ; **analyse en ITT** « treatment policy » pour la supériorité.
- **Biais de sélection ≠ représentativité.** Le point clé est la **synchronisation des t0** entre groupes et l'absence de **temps immortel** (à confirmer sur les courbes de survie).
- **Significativité = risque alpha global**, pas nominale. Multiplicité (critères, comparaisons, sous-groupes, analyses intermédiaires) strictement contrôlée.
- **Ne pas combler les manques** : écrire « non rapporté » plutôt qu'extrapoler. **Ne pas se fonder sur la discussion/conclusion des auteurs** ; n'utiliser que les faits méthodologiques des documents fournis.
- **Recalibration** des résultats sur des contrôles : **difficilement acceptable** — le signaler.

## Ressources de référence

- `references/checklist-criteres-acceptabilite.md` — la checklist opérationnelle, critère par critère.
- `references/structure-rapport.md` — structure et style du rapport.
- Guides cités par le texte source : PRINCIPLED (Desai *et al.*, *BMJ* 2024), ISPOR/ISPE (HETE studies), ROBINS-I (Sterne *et al.*, *BMJ* 2016), FDA RWE guidance (2024), EMA RWD reflection paper.
