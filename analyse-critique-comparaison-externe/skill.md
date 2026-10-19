---
name: analyse-critique-comparaison-externe
description: Réaliser une analyse critique méthodologique d'une étude de comparaison à un groupe contrôle externe (external control arm/ECA, externally controlled trial).
metadata:
  author: "Initiative SPIN-OFF"
  version: "1.0.0"
  url: "https://github.com/cucheratmi/_skills"
---

# Analyse critique d'une comparaison à un groupe contrôle externe

## Objectif

Produire un rapport structuré qui répond point par point aux critères méthodologiques déterminant si le résultat d'une étude de **comparaison à un groupe contrôle externe** (external control arm, ECA — le groupe traité provenant d'une étude monobras ou d'un essai randomisé, le groupe contrôle provenant d'une source de données indépendante : cohorte historique, registre, données de vraie vie) peut être retenu pour modifier une stratégie thérapeutique. Cette compétence ne refait pas une analyse critique complète de l'essai qui fournit le groupe traité (rôle de `analyse-critique-ecr` pour un essai randomisé) ni une évaluation générique d'étude observationnelle (rôle de `analyse-critique-etude-observationnelle`) : elle se concentre sur ce qui est **spécifique à la comparaison externe elle-même** — la planification a priori, l'émulation d'un essai cible, la synchronisation des deux échantillonnages indépendants, et la démonstration que l'hypothèse d'absence de facteur de confusion non contrôlé est plausible.

## Principe axiomatique

Une comparaison à un groupe contrôle externe reste, par nature, une **étude observationnelle** : le groupe traité et le groupe contrôle proviennent de deux échantillonnages indépendants, non issus d'une randomisation commune. Elle en hérite donc l'ensemble des limites (biais de confusion, biais de sélection), mais avec une difficulté supplémentaire propre à sa construction : les deux groupes n'ayant pas été recrutés par la même étude, un facteur pronostique inconnu peut être distribué différemment dans les deux populations sources sans qu'aucune méthode statistique ne puisse le détecter ni le corriger (« effet étude » non réductible).

Pour prétendre au même niveau de preuve qu'un essai randomisé de confirmation, une comparaison externe doit :

1. avoir été **conçue a priori**, avant la connaissance des résultats du groupe traité, avec un protocole et un plan d'analyse statistique explicites ;
2. s'inscrire explicitement dans une démarche d'**inférence causale**, en émulant un essai cible et en vérifiant que ses hypothèses fondamentales (positivité, cohérence, non-interférence, échangeabilité conditionnelle) sont plausibles ;
3. démontrer, données à l'appui, que le **biais de confusion résiduel** ne peut pas expliquer la totalité du résultat observé.

Faute de quoi, la comparaison ne peut produire que des hypothèses, jamais une preuve du bénéfice clinique permettant, à elle seule, de faire évoluer la stratégie thérapeutique. Le doute sur la validité de ces hypothèses profite au statu quo, jamais au traitement évalué — c'est particulièrement vrai ici car, contrairement à un essai randomisé, aucune de ces hypothèses n'est directement vérifiable par les données : leur plausibilité ne peut être qu'indirectement argumentée.

## Prérequis

Rassembler avant de commencer : la publication ou le rapport technique de la comparaison externe ; si l'étude fournissant le groupe traité est distincte (étude monobras ou essai randomisé), sa publication ; le protocole et le plan d'analyse statistique (SAP) de la comparaison externe si disponibles, avec leurs dates ; la description de la source de données utilisée pour le groupe contrôle externe (registre, base médico-administrative, essai antérieur, etc.). Si ces documents ne sont pas tous fournis, le signaler et poursuivre avec ce qui est disponible, en écrivant explicitement « non disponible » ou « non rapporté » pour ce qui manque plutôt que de l'inférer ou de l'halluciner.

## Workflow

### Étape 1 — Identifier le contexte d'usage et le design

Déterminer dans quel cadre la comparaison externe est utilisée : (a) pour fournir le contrefait d'une étude monobras (usage le plus fréquent) ; (b) en complément d'un essai randomisé dont le comparateur n'est pas ou plus approprié ; (c) comme groupe contrôle hybride associé à un bras randomisé. Déterminer aussi si le design est un **essai contrôlé à contrôle externe** (*externally controlled trial* selon ICH E10 : la comparaison externe est prévue d'emblée dans le protocole de l'étude expérimentale) ou une **comparaison indirecte post hoc** (réalisée après coup, indépendamment de la monobras et de la disponibilité de ses résultats). Rappeler que quelle que soit la variante, ces études restent des études observationnelles et ne constituent pas une méthode standard de remplacement de l'essai randomisé : leur acceptabilité doit être jugée sur la base des critères ci-dessous, pas présumée.

### Étape 2 — Étude de confirmation planifiée a priori : écarter HARKing et p-hacking

Vérifier que l'étude est explicitement une étude de **confirmation**, avec une hypothèse thérapeutique clairement définie a priori, et non une simple étude exploratoire. Rechercher les éléments attestant que l'objectif et le protocole ont été formulés **avant** toute connaissance des résultats du groupe traité (cohérence des dates entre protocole, accès aux données et analyses ; attestation explicite des investigateurs ; enregistrement ou publication préalable du protocole ; justification par des études exploratoires antérieures ou des connaissances fondamentales). Vérifier de la même façon qu'un plan d'analyse statistique (SAP) daté a été établi avant toute analyse inférentielle, ce qui permet d'écarter le p-hacking (adaptation de l'analyse en fonction des résultats obtenus). L'absence de ces garanties de transparence, en particulier pour une comparaison post hoc, est une limite méthodologique majeure : l'écrire explicitement plutôt que de la minimiser, car elle expose au choix, guidé par le résultat souhaité, de la source de données, de la population, des covariables d'ajustement ou de la méthode d'analyse.

### Étape 3 — Vérifier les hypothèses de l'inférence causale

L'objectif de la comparaison externe étant de suppléer l'absence de randomisation, vérifier que l'étude s'inscrit explicitement dans une démarche d'inférence causale et que ses hypothèses fondamentales sont discutées et rendues plausibles par les données :

- **Positivité** : dans les strates définies par les covariables d'ajustement, il existe des patients traités et des patients contrôles (chevauchement suffisant, à documenter par exemple par la distribution des scores de propension).
- **Cohérence** (consistency/SUTVA) : le traitement reçu est suffisamment bien défini et homogène pour que l'effet estimé ait un sens univoque.
- **Non-interférence** : le résultat d'un patient ne dépend pas du traitement reçu par un autre patient.
- **Échangeabilité conditionnelle** (hypothèse NUC, *no uncontrolled confounding*) : une fois ajusté sur les covariables retenues, il n'existe plus de facteur de confusion non pris en compte (cf. étapes 6-8).

Une simple comparaison avant-après, ou une comparaison qui ne discute aucune de ces hypothèses, n'est pas une comparaison contrefactuelle acceptable : le signaler explicitement si c'est le cas.

### Étape 4 — L'étude émule-t-elle un essai cible satisfaisant ?

Vérifier que la comparaison externe a été construite selon le cadre de l'**émulation d'un essai cible** (*target trial emulation*), destiné à prévenir les erreurs de conception à l'origine des biais de sélection propres aux études observationnelles. Rechercher : un protocole ou synopsis explicite de l'essai cible émulé ; l'adéquation de ce protocole à la question causale posée (bénéfice clinique du nouveau traitement par rapport au comparateur, avec le standard de robustesse attendu pour construire la stratégie thérapeutique) ; une description point par point de la façon dont chaque composante de cet essai cible a été émulée (population, traitements comparés, t0, critères de jugement, plan d'analyse) ; idéalement, un rapport structuré selon le format TARGET. L'absence de cette démarche d'émulation, ou des adaptations de l'essai cible visant à coller aux données disponibles plutôt qu'à répondre à la question causale posée, sont des signaux d'alerte à documenter.

### Étape 5 — Estimand et stratégie d'analyse

Vérifier que l'estimand est clairement défini et cohérent avec la question causale (effet de l'assignement au traitement, généralement estimé en **ATT** — *average treatment effect among treated* — par une méthode de pondération de type IPTW à poids appropriés) et que l'analyse principale est conduite en **intention de traiter** (« as started »/policy treatment) et non en traitement reçu (« as treated »), sauf justification explicite. Vérifier la cohérence entre l'analyse en intention de traiter et une éventuelle analyse per-protocole ; une discordance importante entre les deux est un signal d'alerte à mentionner.

### Étape 6 — Recherche formalisée des facteurs de confusion

Vérifier qu'une démarche **formalisée et prospective** d'identification des facteurs de confusion potentiels a été conduite, pour chaque critère de jugement retenu, combinant typiquement : une revue systématique satisfaisante des facteurs pronostiques du critère de jugement, et l'établissement d'un graphique de causalité (DAG) utilisé comme **support de la détermination** des covariables d'ajustement — et non construit après coup pour habiller une liste de covariables déjà arrêtée sur d'autres critères (par exemple la seule significativité statistique, méthode à proscrire). Rappeler que, dans une comparaison externe, les facteurs de confusion potentiels sont soit des facteurs pronostiques du critère de jugement, soit des modificateurs de l'effet du traitement, et que ces derniers doivent aussi être recherchés (analyses en sous-groupes des essais disponibles, mécanismes pharmacologiques connus). Vérifier qu'un ajustement sur des collisionneurs ou des médiateurs a bien été évité. Signaler explicitement l'absence de discussion sur les facteurs pronostiques potentiellement inconnus, qui restent un point de confusion non réductible propre au fait que les deux groupes sont issus de deux échantillonnages indépendants.

### Étape 7 — L'ajustement réalisé est-il complet et conforme ?

Confronter la liste des facteurs de confusion identifiés comme nécessaires (étape 6) à l'ajustement effectivement réalisé : tous ces facteurs étaient-ils disponibles dans la source de données du groupe contrôle externe ? Ont-ils été mesurés sans erreur significative ? La méthode de prise en compte (pondération, appariement, régression) a-t-elle atteint son but, attesté par la comparabilité des distributions entre les deux groupes après ajustement (différences de moyennes standardisées, SMD, ou représentation graphique des distributions) ? L'analyse est-elle conforme au plan d'analyse statistique prévu a priori ? Toute covariable pertinente identifiée à l'étape 6 mais absente de l'ajustement final, sans discussion du biais résiduel attendu de son omission, est une limite à signaler explicitement plutôt qu'à combler par une hypothèse non fondée sur les documents fournis.

### Étape 8 — Le biais de confusion résiduel est-il négligeable ?

Rechercher si les auteurs apportent une justification, données à l'appui, que le biais de confusion résiduel est négligeable ou n'explique pas la totalité du résultat observé, par au moins une des deux approches suivantes : des **contrôles négatifs** (ou positifs selon le sens attendu du résultat) couvrant collectivement l'ensemble des facteurs de confusion potentiels affectant la comparaison, avec une précision d'estimation suffisante pour conclure raisonnablement à l'absence d'association attendue ; ou une **analyse quantitative de biais** (E-value notamment), dont il faut vérifier que l'hypothèse de biais nécessaire pour expliquer le résultat observé est suffisamment extrême pour ne pas être plausible. Ces deux approches, bien qu'astucieuses, reposent elles-mêmes sur des hypothèses et ne fournissent jamais une certitude absolue : le rappeler dans la conclusion. Signaler si une recalibration des résultats a été utilisée pour tenter de corriger un biais de confusion résiduel détecté a posteriori — une pratique difficilement acceptable, car elle traduit un ajustement construit après avoir vu les résultats plutôt qu'a priori.

### Étape 9 — Le biais de sélection est-il exclu ?

Vérifier qu'un biais de sélection peut être exclu, c'est-à-dire que l'inclusion (ou la non-inclusion) des patients ou des périodes d'observation par patient dans le groupe contrôle ne pouvait pas dépendre du critère de jugement. Vérifier en particulier : la **définition et la synchronisation correctes du t0** (temps zéro de début de suivi) entre les deux groupes, à un moment où l'éligibilité est vérifiée et le traitement débuté dans les deux bras ; en cas de multiplicité des t0 possibles (pathologie chronique, comparaison à un groupe non traité), l'utilisation d'une approche de clonage/duplication des patients avec une analyse statistique adaptée plutôt que le choix arbitraire d'un t0 (par exemple le premier disponible, ce qui favoriserait systématiquement le traitement étudié) ; l'absence de **biais de temps d'immortalité**, à confirmer par les courbes de survie (Kaplan-Meier) qui ne doivent pas présenter de période initiale non évocatrice de ce biais ; l'utilisation d'un **groupe contrôle de patients incidents** (*new users design*) plutôt que prévalents, protection classique contre le biais de déplétion des susceptibles ; l'absence de **censure informative** liée à des facteurs de risque du critère de jugement, condition nécessaire à une véritable analyse en intention de traiter.

### Étape 10 — Qualité des données et évaluation formalisée du risque de biais global

Vérifier que les données utilisées pour constituer le groupe contrôle externe sont pertinentes (rapportent bien les critères de jugement, critères de sélection, facteurs pronostiques et modificateurs d'effet nécessaires au protocole préétabli), que leur origine est identifiable et traçable, et que leur exactitude a été évaluée (sensibilité, spécificité, VPP, VPN vis-à-vis des besoins de l'étude) — idéalement confirmée par une approche de benchmarking montrant que la méthodologie prévue permet de retrouver des résultats déjà connus. Vérifier l'absence d'erreur de classification asymétrique entre les deux groupes (une exactitude moindre dans le groupe contrôle, obtenu par des moyens différents du groupe traité issu d'une monobras ou d'un essai, est plausible et doit être discutée), en particulier pour les critères favorisant à tort la supériorité du traitement étudié. Vérifier enfin si le **risque de biais global** a été évalué par un outil formalisé et adapté à ce contexte (**ROBINS-I** ou **APPRAISE**), portant à la fois sur le biais de sélection et sur les biais de mesure/classification, et concluant à un niveau de risque faible ou modéré.

### Étape 11 — La multiplicité des comparaisons et le risque alpha global sont-ils contrôlés ?

Vérifier que la multiplicité des comparaisons statistiques est correctement gérée, soit par l'utilisation d'un **critère de jugement principal unique**, soit par une méthode de contrôle du risque alpha global adaptée aux comparaisons multiples (répartition du risque, hiérarchisation, réallocation). Rappeler que des comparaisons réalisées en dehors de ce plan de contrôle ne permettent pas d'inférer l'effet du traitement et ne peuvent pas, à elles seules, justifier l'adoption du nouveau traitement dans la stratégie thérapeutique — cette exigence est identique à celle d'un essai randomisé de confirmation.

### Étape 12 — Les résultats sont-ils cliniquement pertinents ?

Évaluer, comme pour un essai clinique classique, la pertinence clinique du critère de jugement retenu, de la taille de l'effet observé, du choix du comparateur et de la balance bénéfice-risque. Rappeler explicitement que la pertinence clinique ne peut jamais se substituer à la fiabilité méthodologique : un résultat sur la mortalité, aussi pertinent cliniquement soit-il, peut n'être que le reflet d'un biais non contrôlé.

### Étape 13 — Transparence, biais de publication et assurance qualité

Vérifier qu'un biais de publication ou de *selective reporting* peut être écarté : l'unicité de l'étude réalisée est-elle attestée (registres, publications), ou toutes les études similaires sur le même sujet sont-elles rapportées avec leurs résultats (le cas échéant sous forme de méta-analyse) ? Plusieurs groupes contrôles externes ont-ils été envisagés d'emblée, avec un choix motivé et non guidé par le résultat le plus favorable ? Vérifier enfin qu'un système d'assurance qualité couvre le recueil primaire des données, l'extraction du groupe contrôle externe et la réalisation de la comparaison elle-même (traçabilité, auditabilité, conformité réglementaire — RGPD notamment), condition nécessaire pour que les données puissent, le cas échéant, être mises à disposition d'une autorité de régulation (le guide FDA sur les *externally controlled trials* exige explicitement l'accès aux données patient par patient des deux bras et aux documents source pour inspection).

### Étape 14 — Produire un rapport en markdown

Structure cible :

1. **En-tête** — traitement évalué, comparateur, source du groupe traité (monobras/essai randomisé), source du groupe contrôle externe, référence, indication.
2. **Cadre de l'étude** — usage (étape 1), design (essai contrôlé à contrôle externe/comparaison post hoc), planification a priori ou post hoc avec verdict explicite sur HARKing/p-hacking (étape 2).
3. **Cadre causal** — hypothèses de l'inférence causale (étape 3), qualité de l'émulation de l'essai cible (étape 4), estimand et stratégie d'analyse (étape 5).
4. **Biais de confusion** — recherche des déterminants (étape 6), adéquation de l'ajustement réalisé avec **tableau récapitulatif** (covariable, statut pronostique/modificateur d'effet, disponibilité, SMD post-ajustement) (étape 7), évaluation du biais résiduel (étape 8).
5. **Biais de sélection** — t0, synchronisation, temps d'immortalité, design, censure (étape 9).
6. **Qualité des données et risque de biais global** — exactitude, benchmarking, ROBINS-I/APPRAISE (étape 10).
7. **Multiplicité et pertinence clinique** — contrôle du risque alpha (étape 11), pertinence des résultats et balance bénéfice-risque (étape 12).
8. **Transparence** — biais de publication, assurance qualité, accessibilité des données (étape 13).
9. **Conclusion orientée décision** — synthèse explicite du niveau de confiance à accorder au résultat (fiable / à interpréter avec réserve / non fiable), rappel qu'une comparaison externe ne doit pas être considérée comme une méthode standard de remplacement de l'essai randomisé, et implication pour la construction de la stratégie thérapeutique.

## Règles non négociables

- **Une comparaison à un groupe contrôle externe reste une étude observationnelle.** Ne jamais laisser la sophistication des méthodes statistiques employées (score de propension, IPTW, émulation d'essai cible, inférence causale) masquer le fait que sa fiabilité repose entièrement sur des hypothèses non directement vérifiables par les données.
- **Distinguer systématiquement l'ajustement nécessaire (théorique, étape 6) de l'ajustement réalisé (étape 7)** : un ajustement techniquement bien exécuté sur une liste de covariables incomplète reste une comparaison biaisée.
- **Planification a priori = critère transversal.** Une comparaison externe conçue ou analysée post hoc, sans garanties de transparence (dates, protocole, SAP), expose à HARKing et p-hacking : le signaler comme une limite majeure affectant la lecture de tous les critères suivants, pas comme un simple détail administratif.
- **Ne jamais conclure à l'absence de biais de confusion résiduel sur la seule base de l'ajustement réalisé** : cette conclusion nécessite une démonstration spécifique (contrôles négatifs, analyse quantitative de biais), elle-même de certitude toujours limitée.
- **Le t0 (temps de début de suivi) et sa synchronisation entre les deux groupes sont un point de vigilance systématique** : la plupart des biais de sélection propres à ces études (temps d'immortalité, déplétion des susceptibles) en découlent directement.
- **Une comparaison externe n'est pas une méthode standard de remplacement de l'essai randomisé.** Même rigoureusement conduite, elle ne peut être retenue pour modifier une stratégie thérapeutique que si l'ensemble des critères ci-dessus sont satisfaits ; en cas de doute substantiel sur un ou plusieurs critères majeurs (planification a priori, hypothèses causales, confusion résiduelle), conclure explicitement que le niveau de preuve est insuffisant plutôt que de trancher par accommodement.
- **Ne jamais inventer** une justification de covariable, un DAG, une donnée de sous-groupe, une valeur de SMD/E-value ou un résultat d'outil de risque de biais qui ne serait pas rapporté dans les documents fournis : écrire explicitement « non rapporté » ou « non disponible dans les documents fournis ».
- **Toujours citer la source** (section méthodes, protocole, SAP, tableau) de chaque élément factuel rapporté dans le rapport.
- **Ne jamais se baser** sur la discussion ou la conclusion des auteurs ou sur des commentaires ou analyses externes
- **Toujours mentionner** que le rapport a été élaboré par une IA.

## Ressources de référence

- Société Française de Pharmacologie et de Thérapeutique (SFPT), Groupe de Travail Méthodologie. *Comparaisons à un groupe contrôle externe* — Document de synthèse, version 1.0, avril 2026 (livre blanc méthodologique, source principale de cette compétence).
- FDA/CDER/CBER. *Considerations for the Design and Conduct of Externally Controlled Trials for Drug and Biological Products* — Guidance for Industry. [PDF](https://www.fda.gov/media/164960/download)
- ICH E10. *Choice of Control Group in Clinical Trials* — définition de référence de l'*externally controlled trial*.
- Cashin AG, Hansford HJ, Hernán MA, et al. Transparent Reporting of Observational Studies Emulating a Target Trial — The **TARGET** Statement. *JAMA* 2025.
- Sterne JA, Hernán MA, Reeves BC, et al. **ROBINS-I** : a tool for assessing risk of bias in non-randomised studies of interventions. *BMJ* 2016;355:i4919.
- Bykov K, Jaksa A, Lund JL, et al. **APPRAISE** : A Tool for Appraising Potential for Bias in Real-world Evidence Studies on Medication Effectiveness or Safety. *Value Health* 2025.
- VanderWeele TJ, Ding P. Sensitivity Analysis in Observational Research: Introducing the **E-Value**. *Ann Intern Med* 2017;167:268–74.
- Desai RJ, Wang SV, Sreedhara SK, et al. Process guide for inferential studies using healthcare data from routine clinical practice to evaluate causal effects of drugs (**PRINCIPLED**). *BMJ* 2024;384:e076460.
- Pour une analyse critique complète de l'essai fournissant le groupe traité au-delà de la seule comparaison externe, s'appuyer sur la compétence `analyse-critique-ecr` (essai randomisé) ; pour une étude observationnelle qui n'est pas une comparaison à groupe contrôle externe, s'appuyer sur `analyse-critique-etude-observationnelle`.