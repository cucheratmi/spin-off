# Checklist des critères d'acceptabilité — étude observationnelle

Liste de vérification qu'une étude observationnelle satisfait les attentes méthodologiques nécessaires pour justifier un éventuel changement de pratique. **Appliquer chaque critère**, lui attribuer un statut (✅ Satisfait / ⚠️ Partiel-incertain / ❌ Non satisfait / ❔ Non évaluable) et le justifier.

**Logique d'ensemble** : ce sont des *conditions nécessaires*. Un critère critique ❌ suffit à rendre les résultats inutilisables pour la décision.





## L'étude est une étude de confirmation

Respect de la démarche hypothético-déductive, comme un essai de phase 3 pivot (« Hypothesis Evaluating Treatment Effectiveness », HETE study, ISPOR/ISPE). Juger sur :
- Spécification d'un **objectif / d'une hypothèse explicite et préspécifiée**, établie avant l'analyse des données.

**Récuser** : étude exploratoire présentée après coup comme confirmatoire ; hypothèse générée à partir des données.

## Possibilité d'écarter le HARKing

*(HARKing = Hypothesizing After the Results are Known ; risque majeur des études **rétrospectives**, où la source de données préexiste à la formulation de l'objectif.)*
Éléments attestant que l'hypothèse a été formulée **indépendamment des données**, avant toute analyse inférentielle :
- Justification de l'hypothèse par des études exploratoires antérieures ou des connaissances fondamentales (cohérence des dates).
- **Attestation explicite** des investigateurs (protocole, rapport, publication) que l'hypothèse a été générée indépendamment du jeu de données.
- Cohérence chronologique : dates du protocole / d'accès aux données / d'analyse.
- Enregistrement / publication du protocole.

**Rappel** : aucun de ces moyens n'est suffisant à lui seul (surtout quand les investigateurs gèrent aussi les données, ex. registres). L'attente finale est l'**engagement explicite** des auteurs.

## Possibilité d'écarter le p-hacking

Satisfait si l'analyse n'a **pas pu être adaptée en fonction des résultats** (pas d'analyses cachées). Juger sur :
- Existence d'un **plan d'analyse statistique (SAP)** (éventuellement inclus dans le protocole).
- Éléments attestant que le SAP a été **établi a priori, avant toute analyse inférentielle** : attestation explicite des auteurs ; cohérence des dates ; enregistrement/publication du protocole/SAP.
- **Séparation nette** entre les analyses de données **préparatoires** (qualification de la/des sources) et l'**analyse inférentielle** sur le(s) critère(s) de jugement.

**Drapeau rouge** : « torture the data long enough and they will confess to anything » — un même jeu de données peut produire quantité de résultats. Le p-hacking est légitime en exploratoire, **rédhibitoire** en confirmatoire.



## Les hypothèses de l'inférence causale sont vérifiées

Satisfait si l'estimand statistique permet l'**identification** de l'estimand causal avec les données utilisées. Les 4 hypothèses fondamentales doivent être **argumentées comme plausibles** (certaines ne sont pas testables) :
- **Positivité** — tout patient de la population cible peut recevoir chacun des traitements comparés. *(Contre-exemple : nouveau traitement systématiquement utilisé après mise à disposition ⇒ probabilité quasi nulle de recevoir le comparateur ⇒ positivité violée.)*
- **Consistance / régularité** — le devenir observé sous un traitement correspond au devenir potentiel sous cette intervention (patients d'un groupe traités selon une même version bien définie du traitement).
- **Non-interférence** — le traitement d'un patient n'influence pas le devenir d'un autre. *(Contre-exemple : vaccins — la couverture protège les non-vaccinés ⇒ comparaison vaccinés/non-vaccinés potentiellement faussée.)*
- **Échangeabilité conditionnelle** (hypothèse **NUC**, *no uncontrolled confounding* / ignorabilité) — = absence de biais de confusion (voir critère 8). Plausibilité établie par analyses de sensibilité et compréhension du contexte clinique.

**Rappel** : dans un ECR correctement conduit, aucune de ces hypothèses n'est nécessaire pour conclure causalement. Ici, l'attente est une **justification forte** que les données et leur analyse les vérifient (sinon : « biais d'identification »).

## Raisonnement contrefactuel pour isoler l'effet du traitement

Satisfait quand l'effet spécifique du traitement est isolé par comparaison à un **contrefait valide** (ce qui serait advenu aux mêmes patients sans le traitement). Exige un **design comparatif** :
- cohorte, cas-témoins, transversale, autocontrôlée (ou DiD, série temporelle interrompue).

**Récuser** : comparaison **avant-après** / *change from baseline* ; étude purement descriptive — elles n'isolent pas l'effet traitement en approche observationnelle.

## Émulation d'un essai cible

Juger sur :
- **Protocole ou synopsis de l'essai cible émulé** disponible.
- Protocole de l'essai cible **satisfaisant** et **correspondant bien à la question causale** de l'étude.
- Description **point par point** de la façon dont chaque item du protocole a été émulé (la « troisième colonne » du tableau PRINCIPLED).
- Analyse statistique appropriée au design.

**Drapeaux rouges** : le terme « émulation d'essai cible » est **souvent une justification de façade** ; la méta-épidémiologie montre que la plupart des revendications d'émulation sont **abusives**. L'émulation ne garantit pas à elle seule la fiabilité (projet DUPLICATE-RCT : concordance ~75 %, corrélation des effets ~0,82), mais elle est un élément contributif de plus en plus incontournable. Elle révèle notamment les décalages de suivi (temps immortel — voir critère 10) et aligne la question sur le **bénéfice clinique** (et non sur l'« effectiveness » d'usage en pratique).

## Estimand approprié : ATE + effet de l'initiation (ITT)

Juger sur :
- Méthode permettant d'identifier l'**ATE** (*Average Treatment Effect*, population totale) : **IPTW** à poids appropriés, ou régression multivariable suivie de **standardisation / g-computation**. → **Récuser** le matching simple, qui produit un **ATT** et non un ATE.
- **Analyse en intention de traiter (ITT)**, estimand « **treatment policy** » (effet de l'**initiation** du traitement), et non analyse en traitement reçu.

*Repère estimands (population cible) :*
| Estimand | Population | Question causale |
|---|---|---|
| **ATE** | Toute la population | Recommander le traitement à tous les futurs patients comparables ? |
| **ATT** | Patients traités | Le bénéfice existe-t-il chez ceux déjà traités ? |
| **ATC** | Patients contrôles | Étendre le traitement à ceux qui ne le reçoivent pas ? |

Pour une décision d'évaluation d'une technologie de santé, l'estimand attendu est l'**ATE** (sens de l'effet mesuré dans l'ECR, estimand naturel de l'émulation d'essai cible).


## Prise en compte de tous les facteurs de confusion (hypothèse NUC vérifiée)

Prise en compte de **tous les facteurs de confusion (FdC)** affectant l'étude, pour chaque critère de jugement (blocage de tous les chemins de confusion). Juger sur :
- **Détermination formalisée** des FdC par un **graphique de causalité (DAG)** :
  - identification des déterminants des critères de jugement à partir des connaissances, **idéalement par revue systématique** (l'avis d'experts seul — et *a fortiori* les données de l'étude — ne suffisent pas) ;
  - le DAG a **réellement servi** à sélectionner les FdC (et n'est pas une simple illustration dessinée après avoir choisi les covariables) ;
  - identification d'un **ensemble d'ajustement suffisant et minimal** ;
  - **pas de surajustement** sur des **collisionneurs** (colliders) ni des **médiateurs**.
- **Tous les FdC mesurés / disponibles**.
- **FdC mesurés sans erreur** (une erreur de mesure sur les FdC rend caduque l'analyse conditionnée).
- **La méthode a atteint son but** : équilibre des distributions des FdC entre groupes, documenté selon la méthode (chevauchement des scores de propension, distributions post-appariement/pondération, **SMD** — la **p-value est inadaptée** pour juger l'équilibre).
- **Conformité de l'analyse au SAP**.
- **Robustesse** du résultat (analyses de sensibilité).

**Rappel** : toutes les méthodes validées se valent quand le modèle causal correspond aux données ; **aucune méthode magique** (IA comprise) ne garantit la suppression du confusion. On ne juge donc pas l'exactitude du résultat par la méthode, mais par les **diagnostics** (critère 9).

## Biais de confusion résiduel négligeable

Justifier que le biais de confusion résiduel est négligeable (ou n'explique pas la totalité du résultat) — dû à un FdC non identifié, un modèle mal spécifié, etc. Juger sur :
- **Contrôles négatifs** (ou **positifs** si le résultat attendu est une absence d'association) appropriés, montrant l'absence de biais.
- **Analyse quantitative de biais** (p. ex. **E-value**) bien conduite, montrant la robustesse au confusion non mesuré.

**Drapeau rouge** : la **recalibration** des résultats est difficilement acceptable.


## Risque de biais faible ou modéré (outil ROBINS-I)

Risque de biais global **faible ou modéré**. Outre le confusion (blocs C), examiner en particulier :

**Biais de sélection** *(≠ représentativité ; ≠ acception « essai randomisé »)* — survient quand des patients (ou des périodes de suivi) éligibles ne sont pas inclus pour une raison dépendant **à la fois du traitement et du critère de jugement** :
- **Absence de temps immortel**, assurée par le design ou l'analyse (p. ex. *clone-censor-weight*), **confirmée par les résultats** (courbes de survie).
- **Synchronisation correcte des t0** de début de suivi entre les groupes (équivalent de la randomisation ; hormis études autocontrôlées). *Exemple type : CAR-T cells — le délai entre décision et injection exclut les décès précoces du groupe traité ⇒ définir les groupes par l'intention de traiter et démarrer le suivi à la décision.*

**Biais de mesure et de classification (qualité des données)** :
- **Validation des données** (exposition et critère de jugement) par vérification cas par cas ou par sondage : exactitude **et** complétude (données manquantes).
- Validation par **contrôle positif** (retrouver des résultats d'ECR connus) ou **benchmarking** préalable de la source.
- Attention aux **erreurs asymétriques** (dépendant du traitement) — biaisent dans les deux sens. *Exemple : AOD vs AVK, hospitalisation pour hémorragie sur-déclarée sous AOD par précaution des médecins ⇒ faux positifs asymétriques non détectés par la validation du motif d'hospitalisation.*
- **Erreurs symétriques** : ne créent pas de fausse différence mais biaisent **vers l'absence de différence** (« bias toward the null ») ⇒ **critiques pour les conclusions d'absence de différence** (non-infériorité, sécurité).

*Les 5 familles de biais couvertes par ROBINS-I au-delà du confusion : mesure, déviation des traitements, classification, données manquantes, rapport sélectif des résultats.*


## Contrôle strict du risque alpha global

La multiplicité (critères de jugement, traitements comparés, populations/sous-groupes, **analyses intermédiaires** lors du peuplement d'un registre) gonfle le risque alpha. Attente d'un contrôle strict, mêmes méthodes que l'ECR :
- **Répartition** (co-primary endpoints, Bonferroni…).
- **Hiérarchisation**.
- **Réallocation** (Holm, Hochberg, méthode graphique de Bretz…).
- Analyses intermédiaires anticipées avec méthode appropriée (Haybittle-Peto, O'Brien-Fleming…).

Les résultats servant à inférer l'effet doivent être significatifs **en risque alpha global**, pas seulement **nominalement**.

## Pertinence clinique des résultats

Juger sur :
- Pertinence des **critères de jugement** (cliniquement pertinents, pas de substitut non validé).
- Pertinence de la **taille des effets** (effet absolu signifiant, MCID franchi).
- Pertinence du **comparateur** (loyal, standard de soin actuel, dose/durée appropriées).
- Pertinence de la **balance bénéfice / risque**.



---

## Réserve générale

Même lorsque **tous** les critères sont satisfaits, rien ne garantit que les résultats soient propices à une décision : au cas par cas ils peuvent rester insuffisants (notamment sur **petit effectif**). L'inverse n'est jamais vrai : un critère critique non satisfait suffit à disqualifier l'usage décisionnel.
