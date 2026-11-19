---
name: analyse-censures
description: Analyser de façon critique les règles de censure d’un essai clinique à partir de la publication, des suppléments, du protocole et/ou du plan d’analyse statistique (SAP). Extraire et sourcer les règles, repérer les censures susceptibles de biaiser les résultats, et reconstruire l’estimand implicite, notamment treatment policy, hypothetical ou mixte. Utiliser pour la PFS/SSP et la DFS en oncologie, ne s'applique pas aux autres domaines.

metadata:
  author: "Michel Cucherat"
  version: "1.0.0"
  url: "https://github.com/cucheratmi/spin-off"
---

# Analyse critique des règles de censure et de l’estimand

## Objectif et ressources

Produire une analyse traçable permettant de répondre séparément à trois questions :

1. Quelles observations sont comptées comme événements, conservées, censurées ou exclues ?
2. Ces décisions peuvent-elles biaiser l’estimation de l’effet visé ?
3. Quelle question clinique est effectivement compatible avec ces décisions, sous quelles hypothèses ?

Prendre comme référence principale l’appendice EMA PFS/DFS de 2012 fourni par l’utilisateur, EMA/CHMP/27994/2008/Rev.1, applicable depuis le 1er juillet 2013. Utiliser ICH E9(R1) pour la terminologie des estimands : l’appendice EMA est antérieur à cette formalisation. Identifier explicitement les interprétations rétrospectives faites avec ce cadre. Ne pas attribuer les termes modernes au texte de 2012.

Consulter les références à la fin de cette compétence. Vérifier leur version sur les sites officiels lorsque leur actualité est pertinente. Distinguer recommandation réglementaire, interprétation méthodologique et jugement propre sur l’essai. 

## Principes obligatoires

- Distinguer estimand déclaré, stratégie suggérée par les règles, et validité de l’estimation. Une stratégie hypothetical peut être pertinente tout en étant mal estimée.
- Employer « treatment policy » et « hypothetical ». Accepter « policy treatment » dans une demande comme désignant treatment policy.
- Ne jamais déduire une stratégie treatment policy de la seule mention « ITT », de l’inclusion de tous les randomisés ou de l’emploi d’un Cox.
- Ne jamais déduire une stratégie hypothetical de toute censure. Une censure administrative ou une perte de suivi n’est pas, en soi, une stratégie d’estimand.
- Ne pas forcer une classification binaire : autoriser mixte, autre stratégie, ambiguë et non déterminable. Classer la stratégie séparément pour chaque événement intercurrent.
- Distinguer événement intercurrent, événement constitutif du critère, observation manquante et arrêt d’observation. Une interruption de traitement et une perte de suivi concomitante restent deux faits distincts.
- Éviter « le biais est prouvé » lorsque seules les règles sont connues. Distinguer risque plausible, indices observés et biais établi par des analyses suffisamment probantes.
- Ne jamais conclure à l’absence de biais parce que les proportions globales de censure sont similaires ou parce qu’une analyse reste statistiquement significative.
- Ne pas inventer de règle, de résultat, de page ou de référence. Écrire « non rapporté dans les documents disponibles » si nécessaire.

## 1. Constituer et dater le dossier

Inventorier publication principale, annexes, protocole, amendements, SAP, versions du SAP, charte de revue centrale et, si utile, registre ou rapport réglementaire. Ne pas exiger tous ces documents pour commencer. Si un document déterminant manque, poursuivre les extractions possibles puis le signaler comme limite précise.

Pour chaque document, relever titre, version, date, accès et pages pertinentes. Vérifier la chronologie par rapport au recrutement, aux analyses intermédiaires, à la levée de l’aveugle, à la connaissance possible des résultats et à la date de gel des données. Ne pas assimiler automatiquement une date tardive à une décision guidée par les données.

Rechercher les termes : censor/censoring, estimand, intercurrent, progression, death, discontinuation, withdrawal, missing assessment, adequate assessment, new anticancer therapy, subsequent therapy, crossover, loss to follow-up, last assessment, BICR, independent review et leurs équivalents français. Lire également les notes des tableaux et les sections sur les visites après arrêt de traitement.

Comparer toutes les sources. Utiliser le protocole pour l’objectif et les procédures prévues, le SAP pour l’algorithme d’analyse, la publication et les résultats pour l’application rapportée. Ne pas résoudre une contradiction en choisissant mécaniquement le document le plus récent. Décrire la discordance, sa chronologie, sa justification et son importance possible. Ne pas affirmer que la mise en œuvre réelle est vérifiée sans données ou code d’analyse accessibles.

Identifier le critère principal et les critères de délai jusqu’à événement pertinents. En présence de plusieurs estimands ou analyses, analyser chacun séparément en précisant son rôle : principal, secondaire, sensibilité ou supplémentaire.

## 2. Reconstruire le critère et le suivi

Extraire : origine des temps, population d’analyse, définition complète des événements, méthode d’évaluation, calendrier prévu, fenêtres autorisées, définition d’une évaluation adéquate, date de l’événement, date de censure et règles de priorité en cas de situations concurrentes.

Pour la PFS/SSP, vérifier notamment progression objective et décès toutes causes, lecture locale ou centrale, prise en compte des progressions cliniques, examens non programmés, confirmations éventuelles et décès sans progression antérieure. Décrire toute restriction au décès (fenêtre après dernière évaluation, visites manquées, traitement ultérieur). Ne pas appeler PFS traditionnelle un critère qui exclut une partie de ces décès sans en expliquer la modification.

Vérifier si les évaluations continuent selon le même calendrier après arrêt du médicament, toxicité, changement de traitement ou progression locale non confirmée par la revue centrale. Distinguer poursuite du suivi vital et poursuite des évaluations nécessaires à la PFS : connaître le statut vital ne documente pas l’absence de progression.

Pour la DFS, l’EFS, la survie spécifique, le temps jusqu’à progression ou d’autres critères, reprendre leur définition réelle et les événements concurrents. Ne pas appliquer automatiquement les règles de la PFS ou considérer tout décès comme un événement si le critère étudié n’est pas défini ainsi. Pour la survie globale, le décès est l’événement du critère et un changement après progression peut affecter l’estimand d’OS sans affecter la PFS déjà observée.

## 3. Extraire toutes les règles dans un tableau auditable

Créer une ligne par situation et par variante importante. Si le tableau est large, séparer extraction factuelle et jugement méthodologique en conservant les mêmes identifiants R1, R2, etc.

| ID | Situation déclenchante | Règle exacte et priorité | Événement retenu ou écarté | Date retenue | Données après la situation collectées/utilisées ? | Source, version, page/table |
|---|---|---|---|---|---|---|
| R… | Situation documentée | Description fidèle | Progression, décès, autre ou censure | Date définie dans le document | Oui/non/non rapporté, séparément | Référence vérifiable |

Examiner systématiquement, sans présumer qu’une règle existe :

- Absence d’événement au gel des données et date de dernière évaluation adéquate avant ce gel.
- Absence d’évaluation initiale, absence de suivi après randomisation, exclusion après randomisation, censure à J1 et décès ou événement précoce malgré cette absence.
- Une ou plusieurs visites manquées, seuil de visites, fenêtre temporelle, retour ultérieur au suivi, progression ou décès après cet intervalle.
- Arrêt définitif du traitement pour toxicité, manque d’efficacité, choix du patient ou autre raison ; interruption temporaire et réduction de dose si pertinentes.
- Nouveau traitement anticancéreux, sauvetage, changement de bras, intervention locale, chirurgie ou greffe avant l’événement du critère.
- Retrait du consentement au traitement, retrait du consentement aux évaluations, retrait complet et perte de suivi : ne pas les regrouper sans détail.
- Progression locale non confirmée par la revue centrale et arrêt éventuel des examens.
- Décès avant progression, après arrêt, après nouveau traitement ou après visites manquées ; décès de cause particulière si une restriction existe.
- Examens non programmés, évaluation incomplète, progression non confirmée et situations propres à la pathologie ou au traitement.

Quand des règles se chevauchent, restituer l’algorithme de priorité ou signaler son absence. Exemple : un décès après nouveau traitement est-il retenu comme événement ou écarté par la règle de censure antérieure ? Ne pas confondre date de dernière imagerie, dernière visite, dernier contact vital et date d’arrêt du traitement.

## 4. Juger le risque de biais, règle par règle

Appliquer quatre axes indépendants : A) sélection par censure informative ; B) modification/exclusion des événements ou changement de question ; C) détection et fréquence des évaluations ; D) sélection des méthodes et insuffisance de documentation.

Pour chaque règle, formuler une chaîne explicite : déclencheur → lien possible avec le pronostic et/ou le traitement → information supprimée ou retardée → conséquence possible sur la comparaison. Évaluer la plausibilité clinique, l’ampleur observable, le moment et la distribution entre bras.

### A. Censure informative

Pour Kaplan–Meier non ajusté par bras, examiner si, à un temps donné parmi les patients encore à risque, la censure peut dépendre de leur avenir pronostique. L’indépendance pertinente dépend de l’estimateur et des variables sur lesquelles il conditionne ; un ajustement de Cox ne résout pas automatiquement ce problème.

Considérer comme signaux importants : arrêt pour toxicité ou aggravation, nouveau traitement motivé par une suspicion de progression, visites manquées liées à une détérioration, arrêt d’imagerie après progression locale, retrait de suivi lié à la tolérance. Une censure administrative est généralement moins préoccupante si elle reflète uniquement le gel et la durée d’inclusion ; vérifier néanmoins d’éventuels effets du calendrier, du centre ou des changements de pratique. Une dernière imagerie très ancienne avant le gel peut révéler un défaut de suivi.

Ne pas déduire le sens du biais du seul nombre de censures. Retirer préférentiellement des patients à haut risque peut surestimer la survie sans événement du bras concerné ; l’effet sur le contraste entre bras dépend des mécanismes dans les deux bras, du calendrier et de l’estimateur. Présenter le sens comme conditionnel ou indéterminable. Ne pas affirmer systématiquement une faveur au traitement expérimental.

### B. Définition des événements et question clinique

Examiner l’exclusion d’événements documentés après visites manquées, après arrêt ou après changement de traitement. Pour la PFS traditionnelle, signaler particulièrement les décès toutes causes écartés en raison d’une fenêtre ou d’un traitement ultérieur. Décrire le changement de définition et le risque de biais pour la cible annoncée, sans présumer qu’un autre estimand serait intrinsèquement illégitime.

Une censure rétrospective à la dernière évaluation antérieure au déclencheur supprime une période supplémentaire : relever sa durée si disponible. Une censure à J1 conserve nominalement un patient dans le fichier sans nécessairement conserver une information utile pour la comparaison. Ne pas l’assimiler à un suivi ITT complet.

### C. Détection et revue centrale

Comparer calendrier, fréquence effective, retards, examens non programmés, modalités et poursuite du suivi dans les deux bras. Examiner l’aveugle, son éventuelle rupture et le lien entre symptômes, toxicité et décisions d’imagerie. Distinguer retard de détection, censure par intervalle et censure à droite.

Évaluer si une revue centrale aveugle dispose des examens après progression locale. Elle peut limiter un biais de lecture mais ne recrée pas des images jamais collectées. Comparer résultats locaux et centraux, discordances directionnelles et censures dues à l’arrêt de collecte. Ne pas attribuer automatiquement toute différence à un biais de l’investigateur.

### D. Préspecification et transparence

Examiner règles non justifiées, amendements tardifs, différences entre documents et résultats, absence des motifs ou nombres de censure, analyse principale modifiée et analyses annoncées non rapportées. Distinguer documentation insuffisante et preuve d’une sélection des analyses.

### Jugement par règle

Utiliser « peu préoccupante sur les éléments disponibles », « préoccupante », « très préoccupante » ou « non évaluable », avec justification. Ne pas sommer ces jugements en score numérique ni les présenter comme un outil validé de risque de biais. Une règle conditionnelle jamais appliquée ne doit pas recevoir la même portée empirique qu’une règle fréquemment appliquée ; si sa fréquence manque, le dire.

## 5. Reconstruire l’estimand

Décrire population cible, traitements comparés, variable avec horizon, stratégie pour chaque événement intercurrent, et mesure de contraste (HR, différence de survie à un temps fixé, RMST, etc.). Marquer chaque attribut comme explicite, inféré ou non rapporté. Une mesure HR ne précise pas à elle seule la question clinique et ne garantit pas une interprétation causale simple.

Appliquer la grille suivante en tenant compte de l’objectif formulé, de la collecte et de l’estimateur :

| Traitement de la situation | Interprétation compatible | Vérification nécessaire |
|---|---|---|
| Maintien des événements après arrêt/changement, indépendamment de ce fait | Treatment policy pour cet événement intercurrent | Suivi pertinent réellement poursuivi, données utilisées et gestion des données manquantes |
| Exclusion du devenir après changement avec objectif de survie si le changement n’avait pas eu lieu | Hypothetical pour ce changement | Scénario contrefactuel précis et hypothèses d’identification/estimation |
| Censure au changement puis KM/Cox sans scénario expliqué | Estimand implicite possiblement hypothetical, sous hypothèses ; ambiguïté à expliciter | Ne pas déclarer que la cible est identifiée par la seule censure ; examiner aussi l’éventuel objectif avant événement intercurrent |
| Question expressément limitée à la période avant arrêt ou autre événement intercurrent | While on treatment / while before intercurrent event | Vérifier variable et méthode : un KM censuré à l’arrêt n’est pas automatiquement le risque observé avant arrêt |
| Arrêt, sauvetage ou changement compté comme échec | Composite pour cette situation | Définition et sens clinique du nouvel événement ; ne pas appeler cela une simple censure |
| Cible définie par ce qui arriverait aux mêmes patients sous plusieurs traitements | Principal stratum éventuel | Définition contrefactuelle et méthode ; un sous-groupe d’observants observés ne suffit pas |
| Censure administrative ou perte de suivi | Problème d’observation, sans détermination de stratégie à lui seul | Estimand visé, mécanisme de données manquantes et hypothèses de l’estimateur |

Ne pas transformer « sans nouveau traitement » en « sans besoin d’un nouveau traitement » : ces scénarios sont différents. De même, distinguer continuer malgré une toxicité, empêcher cette toxicité et interrompre le médicament sans traitement ultérieur. Si le scénario n’est pas défini, conclure à une interprétation hypothetical plausible mais insuffisamment spécifiée.

Pour un estimand hypothetical, examiner les hypothèses nécessaires : cohérence du scénario, absence de facteurs pronostiques non mesurés liés au changement, indépendance conditionnelle de censure pertinente, positivité et spécification des modèles selon la méthode. La randomisation initiale ne garantit pas l’échangeabilité des patients restant suivis après sélection post-randomisation.

Si l’estimateur est un KM/Cox naïf avec censure au changement, expliquer qu’il utilise le devenir des sujets restant observés pour représenter le devenir non observé, sous ses hypothèses ; il ne démontre pas ce qui serait arrivé sans changement. Ne pas confondre ce scénario avec une analyse per protocole automatiquement valide.

Pour un objectif de risque d’événement avant un événement concurrent, distinguer fonction d’incidence cumulée et survie nette obtenue par KM en censurant les événements concurrents. Une analyse de hazard cause-spécifique peut censurer l’événement concurrent dans son calcul sans identifier, à elle seule, un effet causal dans un monde où ce dernier serait supprimé. Ne pas lui attribuer automatiquement une stratégie hypothetical.

Dans une PFS incluant progression ou décès, le décès est déjà un composant de la variable ; ne pas l’étiqueter treatment policy « pour le décès ». L’estimand peut simultanément traiter le décès dans une variable composite, l’arrêt en treatment policy et un nouveau traitement en hypothetical. Réserver « mixte » aux stratégies différentes appliquées à des événements intercurrents distincts ; préciser à quoi se rapporte chaque étiquette.

Conclure avec trois énoncés distincts :

1. **Estimand annoncé** : reprendre la cible revendiquée ou indiquer qu’elle n’est pas explicitée.
2. **Estimand compatible avec les règles** : expliciter la question et les stratégies par événement ; donner un niveau de certitude motivé.
3. **Estimabilité et cohérence** : indiquer si la collecte et l’estimateur permettent de soutenir cette cible, et les hypothèses ou informations qui manquent.

Une règle incompatible avec une cible treatment policy annoncée peut être une estimation inadéquate de cette cible, plutôt qu’un changement formel d’estimand. Formuler les deux possibilités lorsque les documents ne permettent pas de trancher.

## 6. Examiner les résultats et la robustesse

Rechercher par bras : effectifs randomisés/analysés, événements par composant, censures administratives et prématurées par motif, temps de censure, nombre et durée des visites manquées, imagerie après arrêt, initiations de traitements ultérieurs, données absentes dès le départ et discordances locale/centrale. Employer des dénominateurs explicites. Ne pas sommer des motifs qui peuvent se chevaucher et ne pas calculer des catégories distinctes par différence sans justification.

Examiner courbes KM et nombres à risque, suivi médian correctement défini, maturité, extrémité des courbes, profils de censure et association aux facteurs pronostiques disponibles. Les courbes de délai jusqu’à censure et les comparaisons de motifs peuvent révéler des signaux ; elles ne prouvent pas l’indépendance, surtout si l’événement d’intérêt termine l’observation. Une reconstruction à partir d’une courbe KM ne restitue pas les motifs des censures.

Pour chaque analyse de robustesse rapportée ou proposée, préciser : question, hypothèse examinée, estimand conservé ou changé, données requises et résultat disponible. Distinguer :

- **Sensibilité au sens ICH E9(R1)** : même estimand, hypothèses différentes sur le devenir manquant ou le mécanisme de censure.
- **Analyse supplémentaire** : autre stratégie pour un événement intercurrent, autre population ou autre définition du critère. Par exemple, compter un sauvetage comme événement modifie la variable ; inclure les événements après un traitement ultérieur change la cible si l’analyse principale était hypothetical.

En fonction du problème, envisager : scénarios de risque après censure et tipping point avec plage cliniquement justifiée ; imputation adaptée aux temps d’événement et au scénario ; IPCW avec covariables pertinentes, diagnostics des poids et positivité ; analyse par intervalle si les temps d’évaluation sont déterminants ; comparaison locale/centrale ; analyse conservant les décès et progressions tardifs ; autre définition composite explicitement annoncée comme cible différente.

Ne pas présenter IPCW, imputation ou revue centrale comme une correction garantie. L’IPCW exige notamment des facteurs pertinents mesurés, une positivité suffisante et des modèles adaptés.  Ne pas exécuter de réanalyse sans données nécessaires ni prétendre qu’un résultat hypothétique a été observé.

Évaluer les écarts d’effets, d’IC et de conclusions cliniques, pas seulement p < 0,05. Des analyses concordantes partageant la même hypothèse fragile apportent une assurance limitée. La robustesse à une hypothèse ne prouve pas la pertinence clinique de l’estimand.

## 7. Livrable obligatoire

Rédiger en français, sauf demande contraire, avec les rubriques suivantes :

1. **Conclusion décisionnelle** : synthèse du risque principal, de la cible effectivement compatible et de la solidité de cette interprétation ; mentionner d’emblée les documents déterminants absents.
2. **Dossier et critère** : inventaire daté, définition du critère et schéma de suivi utile à la compréhension.
3. **Tableau des règles de censure** : extraction factuelle exhaustive selon la section 3.
4. **Tableau critique** : ID ; mécanisme de biais ; indices observés ; direction conditionnelle/indéterminable ; portée possible ; jugement motivé ; source.
5. **Tableau des stratégies d’estimand** : événement intercurrent ; stratégie annoncée ; données conservées/écartées ; stratégie compatible ; hypothèses ; degré de certitude. Ajouter la reconstruction des attributs et la question clinique en une phrase.
6. **Robustesse** : analyses effectuées et résultats, hypothèses communes, cible conservée/changée, analyses importantes manquantes.
7. **Conséquences et questions aux auteurs** : distinguer conséquences pour le biais, pour la question clinique et pour l’interprétation du bénéfice. Limiter les demandes aux informations qui changeraient le jugement.
8. **Références** : sources de l’essai avec version et page/section, puis sources méthodologiques officielles.

Dans les tableaux, préserver les faits avant le jugement. Attribuer une référence précise à chaque règle et aux résultats quantitatifs ; réserver les citations verbatim à de courts passages décisifs. Indiquer page du PDF et page imprimée si elles diffèrent. Une référence à un document entier ne remplace pas la localisation d’une règle.

Utiliser au besoin cette formulation finale : « Les règles sont compatibles avec une stratégie [X] vis-à-vis de [événement], tandis que [autre événement] relève de [Y]. Cette interprétation est [explicite/inférée/incertaine]. La validité de l’estimation dépend de [hypothèses]. Les éléments disponibles [soutiennent/ne permettent pas d’établir] ces hypothèses. Le risque principal pour l’interprétation du résultat est [conséquence]. »

## Vérification finale

Avant de rendre le rapport, vérifier que chaque critique renvoie à une règle réellement documentée, que décès/arrêt/perte de suivi sont distingués, que l’usage des données après événement intercurrent est explicite ou signalé inconnu, que « ITT » n’a pas servi de raccourci, que les risques ne sont pas présentés comme des biais prouvés, et que les conclusions sur l’estimand restent conditionnelles lorsque l’identification n’est pas établie. Ne pas inventer un seuil universel de proportion de censures acceptable.

## Références méthodologiques intégrées

**Source de départ — EMA.** *Appendix 1 to the guideline on the evaluation of anticancer medicinal products in man: Methodological consideration for using progression-free survival (PFS) or disease-free survival (DFS) in confirmatory trials.* 13 décembre 2012. EMA/CHMP/27994/2008/Rev.1. Application : 1er juillet 2013.

- Pages 2–3 : définition et dates des événements, évaluations non programmées et censure informative.
- Page 4 : poursuite des évaluations après arrêt ou changement, gestion des données manquantes et préspecification.
- Pages 5–6 : calendrier d’évaluation ; pages 6–7 : revue centrale et limites lorsque la collecte s’arrête.
- [PDF officiel EMA](https://www.ema.europa.eu/en/documents/scientific-guideline/appendix-1-guideline-evaluation-anticancer-medicinal-products-man-methodological-consideration-using-progression-free-survival-or-disease-free-survival-confirmatory-trials_en.pdf).

**Cadre des estimands — ICH E9(R1).** *Addendum on estimands and sensitivity analysis in clinical trials to the guideline on statistical principles for clinical trials.* Version EMA Step 5, 17 février 2020, EMA/CHMP/ICH/436221/2017 ; application : 30 juillet 2020. Texte ICH Step 4 daté du 20 novembre 2019.

- A.3.1–A.3.3, pages 7–11 du PDF : événements intercurrents, stratégies et attributs.
- A.4–A.5, pages 13–17 : collecte, estimation, sensibilité et analyses supplémentaires.
- A.6, page 18 : documentation des estimands et analyses.
- [PDF officiel EMA/ICH](https://www.ema.europa.eu/en/documents/scientific-guideline/ich-e9-r1-addendum-estimands-and-sensitivity-analysis-clinical-trials-guideline-statistical-principles-clinical-trials-step-5_en.pdf).

Adapter les principes de l’appendice EMA hors oncologie en justifiant l’analogie ; ne pas présenter ce texte spécifique à la PFS/DFS comme une exigence applicable indistinctement à tous les critères.
