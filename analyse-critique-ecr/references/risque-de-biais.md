# Évaluation du risque de biais 

> Référence de fond pour l'analyse critique d'un essai clinique randomisé de **supériorité**.
> Elle expose la **logique** de l'évaluation du risque de biais : les 4 mécanismes de biais,
> les 4 principes méthodologiques qui les préviennent, et la sortie **binaire** attendue
> (résultat *à l'abri des biais* / *à risque de biais*).


---

## 1. Ce qu'est un biais dans un essai de supériorité

Un essai de supériorité est **biaisé** lorsqu'il existe un facteur, **autre que le traitement
étudié**, qui induit une différence en faveur du nouveau traitement sur le(s) critère(s) de
jugement.

> *Exemple.* Antiagrégant vs placebo pour prévenir l'AVC dans la FA. Si tous les patients du
> groupe traité reçoivent en plus un AVK et aucun dans le groupe placebo, il y aura moins d'AVC
> dans le groupe traité — que le traitement prévienne réellement l'AVC ou non.

**Un biais est donc une cause de résultat faux positif.**

Condition nécessaire pour qu'un biais fausse un essai de supériorité : un facteur qui
**conditionne le critère de jugement** doit être **asymétrique** entre les deux groupes **et
favoriser le groupe traité**. En conséquence :

- un facteur qui **n'influence pas** le critère de jugement ne peut pas induire de biais ;
- un facteur **réparti symétriquement** (y compris en moyenne) entre les groupes ne peut pas
  induire de biais.

> ⚠️ *Non-infériorité* : la logique s'inverse. Les biais problématiques sont ceux qui
> **réduisent** la différence entre traitements et peuvent faire apparaître non inférieur un
> traitement en réalité très inférieur au standard. Ce document traite l'essai de **supériorité**.

---

## 2. Principe fondamental : on ne diagnostique pas un biais, on vérifie une protection

Sauf cas exceptionnel, il est **impossible de déterminer a posteriori** si un résultat donné est
effectivement biaisé (on ne connaît pas le véritable effet du traitement). Chercher « des signes
de biais » tous azimuts est une démarche **inductive, subjective**, exposée soit au *procès à
charge*, soit à la *cécité élective* selon l'opinion du lecteur.

La méthodologie contourne ce problème :

1. On a identifié **toutes** les causes de biais possibles dans un essai (→ Tableau 1).
2. On a conçu des **principes méthodologiques** qui **empêchent leur survenue** :
   randomisation imprévisible, double insu, analyse en intention de traiter avec remplacement
   conservateur des données manquantes.
3. Si ces principes sont **correctement mis en œuvre**, les résultats « positifs » **ne peuvent
   pas** provenir d'un biais (il reste l'erreur aléatoire, gérée par le test statistique).

La lecture critique **au niveau des biais** consiste donc à **vérifier si l'essai est protégé
contre les biais** (correctement conçu *et* réalisé), et non à débusquer un biais :

- **Protégé** → un résultat positif ne peut pas être un faux positif dû à un biais ; il peut être
  accepté (sous réserve de la robustesse statistique et de la pertinence clinique).
- **Non (ou partiellement) protégé** → l'essai est **à risque de biais**. Ses résultats
  « positifs » peuvent être **entièrement dus aux biais** : ils ne peuvent pas fonder un
  changement de pratique (risque de recommander à tort ce changement).

La validité interne est remise en cause **non pas parce qu'on a la preuve d'un biais**, mais parce
que l'essai est **insuffisamment protégé** contre les biais.

> Il est donc **abusif** d'affirmer qu'un essai « est biaisé » : la seule conclusion objective
> possible est qu'il est **protégé contre les biais ou non**.

### Sortie attendue (binaire)

| Résultat | Interprétation |
|---|---|
| **À l'abri des biais** | Peut faire changer les pratiques *s'il* est statistiquement démontré (risque α global) *et* cliniquement pertinent. |
| **À risque de biais** | Insuffisamment solide pour faire changer les pratiques, quel que soit le p. |

---

## 3. « Biais » ≠ tout problème d'un essai

Dans le discours courant, « biais » est souvent employé pour désigner **tout** défaut perçu. Or le
biais est **un type précis et bien défini** de réserve. Ne pas confondre :

- **Erreur aléatoire** (risque α, différences dues au hasard) → ce **n'est pas** un biais.
  « Biais statistique » pour parler d'un problème de risque α est un abus de langage.
- **Défaut de représentativité / validité externe** → ce **n'est pas** un biais. Si un essai
  visant des sujets âgés en inclut très peu, le problème survient **en amont de l'inclusion** :
  c'est un problème de **pertinence clinique / validité externe**, pas de validité interne.

> Un **biais** fait que le résultat obtenu dans l'étude **diffère de celui qu'il aurait dû être
> compte tenu des patients réellement inclus** (validité interne). Un problème de **validité
> externe** fait qu'un résultat pourtant intrinsèquement correct ne reflète pas le bénéfice
> attendu chez les patients à traiter en vraie vie.

---

## Tableau 1 — Les 4 biais de l'essai de supériorité et leur principe de prévention

| Biais (mécanisme prévenu) | Principe méthodologique qui le prévient | Nom usuel du biais |
|---|---|---|
| Le groupe traité est favorisé par la **sélection** de patients moins graves que le groupe contrôle | **Randomisation imprévisible** | Biais de sélection* |
| La **mesure** du critère de jugement favorise le groupe traité | **Double insu** (au niveau de la mesure) | Biais de mesure (détection) |
| La **prise en charge / le suivi** favorise le groupe traité | **Double insu** (au niveau de la réalisation) | Biais de suivi (réalisation / performance) |
| Le groupe traité est favorisé par la **sortie de l'analyse** de certains patients | **Analyse en ITT** avec remplacement des données manquantes | Biais d'attrition |

\* *Attention : ne correspond pas en totalité au « biais de sélection » de l'épidémiologie.*

> Les noms varient beaucoup d'un auteur à l'autre (synonymes parfois ambigus entre monde de
> l'essai clinique et épidémiologie). **L'important n'est pas le nom** mais de comprendre le
> **mécanisme** et **en quoi le principe méthodologique l'évite**.

---

## 4. Biais prévenu par la randomisation imprévisible *(biais de sélection)*

### But réel de la randomisation

Le but de la randomisation **n'est pas** de créer deux groupes identiques. Un processus purement
aléatoire ne garantit pas le même nombre de femmes, de diabétiques, etc. dans les deux groupes.
Les différences résiduelles sont **dues au hasard**, **non systématiques** → **erreur aléatoire**
(gérée par le test statistique), **pas un biais** (re-randomiser les mêmes patients ne
reproduirait pas les mêmes différences).

> La randomisation assure que **la nature du traitement reçu ne dépend en rien des
> caractéristiques du patient**. Elle ne garantit pas que les deux groupes seront identiques.

« Groupes **comparables** » = aptes à une **comparaison loyale**, non systématiquement influencée
par autre chose que le traitement — **pas** « groupes identiques ».

### Conséquence pratique pour l'évaluation critique d'un essai

- **Inutile de vérifier** que les groupes de la *table 1* (caractéristiques à la baseline) sont
  identiques. La comparabilité est **garantie par l'allocation aléatoire** (et sa non-perversion).
- **Inutile de tester** les différences baseline : par construction, ~5 % des caractéristiques
  ressortiraient significatives au seuil de 5 %. Ces tests ne se font pas.
- **À vérifier** : que l'allocation était **réellement aléatoire et imprévisible**.

### Randomisation imprévisible

Imprévisible = l'investigateur **ne peut pas connaître** le traitement qu'un nouveau patient
recevrait **avant de l'avoir effectivement inclus**.

- *Contre-exemple (prévisible)* : randomisation par **enveloppes** en **ouvert**. Rien n'empêche
  d'ouvrir l'enveloppe **avant** de formaliser l'inclusion et de n'inclure que si le traitement
  alloué « convient » au patient → perversion de l'allocation.
- *Solution* : **attribution centralisée** (Web / téléphone, IWRS / IVRS), le traitement n'étant
  communiqué **qu'après** l'inclusion effective. Si l'investigateur décide ensuite de ne pas
  donner ce traitement, le patient **reste dans son groupe** (analyse en ITT) → pas de
  déséquilibre.
- En **double insu**, **tout** type de randomisation est imprévisible (les procédures
  centralisées restent largement utilisées).

*Exemples de formulation dans les publications :* « interactive voice/Web response system to
determine treatment assignment » ; à l'inverse, un « sealed envelope system » dans un essai en
ouvert non masqué doit alerter.

### Exemples de décision

| Situation | Jugement |
|---|---|
| Ouvert + randomisation **prévisible** (ex. enveloppes non centralisées) | **Risque de biais** |
| Ouvert + randomisation **imprévisible** (centralisée : téléphone / Web) | À l'abri des biais |
| **Double insu** (quelle que soit la méthode de randomisation) | À l'abri des biais |

---

## 5. Biais prévenu par le double insu — mesure du critère de jugement *(biais de mesure)*

Le double insu (*double blind / double masked*) empêche que la **mesure** du critère de jugement
soit influencée par la connaissance du traitement reçu et favorise systématiquement le traitement
évalué.

> *Exemple.* Essai **ouvert** HBPM vs HNF, critère = TVP suspectée cliniquement **confirmée par
> phlébographie**. Les deux produits sont en réalité équivalents (TVP réelle 5 %). Convaincus de
> la supériorité des HBPM, les investigateurs demandent plus facilement la phlébographie de
> confirmation dans le groupe contrôle. Si 95 % des TVP sont détectées dans le contrôle contre
> 60 % dans le traité : fréquence observée 5 %×60 % = **3 %** vs 5 %×95 % = **4,75 %** → fausse
> supériorité des HBPM.

**Le double insu est en réalité un « quadruple insu »** : ni le patient, ni le médecin qui
applique le traitement, ni celui qui prend en charge le patient, ni celui qui mesure le critère ne
connaissent le traitement. **Si un seul maillon connaît le traitement, la possibilité de biais
réapparaît.**

### Techniques d'insu

- **Placebo identique** au traitement évalué (*matching placebo*).
- Galéniques très différentes (ex. orale vs IV) → **double placebo** (*double-dummy*) : chaque
  groupe reçoit le verum d'une forme et le placebo de l'autre.
- Cas complexe (ROCKET-AF, rivaroxaban vs warfarine, `10.1056/NEJMoa1009638`) : la warfarine exige
  un ajustement de dose sur l'INR, pas le rivaroxaban. Le double-dummy ne suffit pas ; il faut en
  plus ajuster la « warfarine » (verum **ou** placebo) dans les deux groupes. Le groupe
  rivaroxaban est ajusté sur des **INR factices** (*sham INR*) générés par un algorithme validé et
  transmis via un dispositif chiffré et un moniteur indépendant, pour préserver l'insu.

### Essais en ouvert : limiter le biais de mesure

Quand le double insu est impossible (ex. chirurgie conservatrice vs amputation), le biais de
mesure n'est évité **que** si le critère est **parfaitement objectif** (non sujet à
interprétation).

- Seule la **mortalité totale** est vraiment parfaitement objective. Même la détermination de la
  **cause** du décès peut être sujette à interprétation (comorbidités, absence d'autopsie).
- Un **comité d'adjudication en aveugle** ne fait que **limiter partiellement** le biais pour un
  critère partiellement subjectif — il **ne remplace pas** le double insu, car : (1) le **biais de
  suivi** subsiste (cf. §6) ; (2) la documentation médicale est transmise au comité **par les
  investigateurs**, et la connaissance du traitement peut influencer la quantité/précision de
  l'information transmise.

### Quand l'insu semble « impossible » : le rendre possible

Le double insu est devenu le standard **même en chirurgie et pour les dispositifs** : le groupe
contrôle reçoit une **intervention factice** (*sham intervention / sham surgery*).

> *Exemple.* Greffe de cellules souches dans la maladie de Parkinson sévère : des essais
> **ouverts** (contrôle seulement observé) montraient une amélioration. Un essai en **double insu
> avec sham surgery** (`10.1056/NEJM200103083441002`) **n'a pas confirmé** le bénéfice, évitant
> d'engager la prise en charge dans une voie lourde, risquée et coûteuse sans bénéfice réel.

> **Éthique.** La *sham intervention* semble poser problème (anesthésie, incision), mais l'enjeu
> éthique majeur d'un essai est de **ne pas valider à tort un traitement sans intérêt**. Rien
> n'est moins éthique qu'un essai de faible qualité méthodologique. (Patients informés et
> volontaires, bien entendu.)

### Exemples de décision

| Situation | Jugement |
|---|---|
| Double insu | À l'abri des biais |
| Ouvert, mais critère **parfaitement objectif** | À l'abri des biais |
| Ouvert + comité d'adjudication en aveugle | **Risque de biais** |
| Ouvert + critère subjectif | **Risque de biais** |

---

## 6. Biais prévenu par le double insu — réalisation / suivi de l'essai *(biais de suivi)*

Le double insu empêche aussi que la **prise en charge** diffère entre les groupes : soins
complémentaires, traitements concomitants ou de secours (*rescue*), décision d'arrêt des
traitements curatifs, passage en soins palliatifs, etc.

En double insu, si un investigateur ajoute un traitement actif, il le fera **de la même façon dans
les deux groupes** (indistinguables). Au pire, cela peut rendre l'essai « négatif » (les deux
groupes traités identiquement) — mais **cela ne peut pas favoriser sélectivement le groupe
évalué**, puisqu'on ne peut pas cibler uniquement ces patients.

> *Point oncologie (à vérifier pour la survie globale)* : pas de différence entre groupes dans
> l'utilisation des **traitements post-progression**, qui peut sinon introduire un biais de suivi.

### Exemples de décision

| Situation | Jugement |
|---|---|
| Double insu | À l'abri des biais |
| Ouvert | **Risque de biais** |

---

## 7. Biais prévenu par l'analyse en intention de traiter *(biais d'attrition)*

### Définition

L'analyse en **intention de traiter (ITT)** inclut **tous les patients randomisés**, **dans leur
groupe de randomisation**, **sans tenir compte** des événements intercurrents : erreur de
traitement, arrêt prématuré, retrait (*withdrawal*), recours au traitement de l'autre groupe,
inclusion à tort, perdu de vue (*lost to follow-up*), etc. Elle empêche de **conditionner** le
résultat en **sortant** des patients de l'analyse.

### Le problème des données manquantes

Un patient **perdu de vue** (absent à la visite où le critère est mesuré) a une **valeur
manquante** : il ne peut **pas** contribuer à l'analyse en l'état.

> *Exemple (critère continu).* Critère = FEVG à la dernière visite ; effet = différence de
> moyennes (150 traités vs 148 contrôles). 12 patients/groupe absents à la dernière visite : les
> compter **seulement au dénominateur** sous-estime la moyenne. Pour les faire contribuer, il faut
> **remplacer** la valeur manquante par une valeur arbitraire, choisie pour **ne pas favoriser le
> groupe traité**.

Les données manquantes sont un **risque de biais** car elles peuvent **dépendre du traitement reçu
et de l'évolution** du patient :

> *Exemple (critère binaire).* Antidépresseur vs placebo, critère = échec à S12. Les patients avec
> effets indésirables abandonnent davantage, surtout sans amélioration ressentie. Comme les EI
> sont plus fréquents sous traitement, les futurs « échecs » quittent **plus** le groupe traité →
> **moins** d'échecs observés dans le groupe traité → **fausse** efficacité.

> **Analyses de survie (*time-to-event*).** Traiter les perdus de vue comme des **censures**
> **ne protège pas** contre le biais : il n'y a **pas de remplacement conservateur**. (« data on
> patients … lost to follow-up were censored at the last available follow-up time » ne garantit
> pas l'absence de biais d'attrition.)

### Remplacement conservateur

Remplacer les données manquantes sur le critère par une valeur arbitraire **choisie pour handicaper
l'apparition de la supériorité** (méthode **conservatrice**). Si la supériorité **persiste** après
remplacement conservateur, le résultat est **robuste** vis-à-vis des données manquantes.

**Critère continu :**
- **LOCF** (*last observation carried forward*) — dernière valeur connue reportée.
- **BOCF** (*baseline observation carried forward*) — valeur à la baseline.

> *À noter* : LOCF **réduit** l'écart et peut faire perdre la significativité → révèle un résultat
> **non robuste** vis-à-vis des perdus de vue (l'« observed case analysis » ne démontrait alors
> pas l'effet). Un remplacement laissant encore des patients hors analyse **n'est pas** une vraie
> ITT.

**Critère binaire :**
- **Biais maximum** (*worst-case scenario*) : perdus de vue du groupe **traité** comptés comme
  **ayant fait** l'événement, ceux du contrôle comme **ne l'ayant pas fait**.

> *Exemple.* AVC comme critère : 25/200 (traité) vs 30/200 (contrôle) ; 5 vs 6 perdus de vue. Si
> les 5 perdus de vue du groupe traité avaient en réalité fait un AVC : 25+5 = **30** vs **30** →
> le résultat brut **n'est pas à l'abri** d'un biais d'attrition.

**Autres :** imputation multiple (technique sophistiquée ; juger de sa pertinence dépasse le cadre
de ce document).

> *Repère `grille-rob2.md`* : < 5 % de perdus de vue → risque faible ; 5–20 % → zone grise
> exigeant une analyse de sensibilité ; > 20 % → risque élevé sauf justification. **LOCF seul**
> est le plus souvent **insuffisant** dans les référentiels modernes ; les méthodes préférées sont
> l'imputation multiple, les modèles mixtes (MMRM) et l'analyse *tipping point*.

### Vérifications en évaluation critique

- Il est bien mentionné que l'analyse est en **ITT** (ou porte sur la population *full analysis
  set*).
- Dans le **flow chart**, l'effectif **analysé** = effectif **randomisé** dans chaque groupe.
- **Pas de perdu de vue** sur le critère ; sinon, données manquantes remplacées par une méthode
  **conservatrice** (BOCF, biais moyen ou biais maximum).
- Si non remplacées, leur **nombre ne remet pas en cause la robustesse** du résultat.

> *Population d'analyse* : sous-ensemble effectivement analysé. La **population ITT** = totalité
> des inclus (moins les retraits de consentement à être suivis).

### Exemples de décision

| Situation | Jugement |
|---|---|
| ITT + remplacement des données manquantes par méthode **conservatrice** | À l'abri des biais |
| ITT sans remplacement conservateur, mais nombre de perdus de vue **ne remettant pas en cause** la robustesse | À l'abri des biais |
| ITT sans remplacement conservateur, robustesse **non assurée** vu le nombre de perdus de vue | **Risque de biais** |
| Analyse **per-protocole** ; **mauvaise définition** de l'ITT | **Risque de biais** |

---

## 8. Évaluation globale du risque de biais

À l'issue de l'évaluation, **chaque résultat** est classé en **2 catégories** :

- **À l'abri des biais** — peut potentiellement faire changer les pratiques *s'il* conclut de
  manière statistiquement significative (au risque α global) *et* qu'il est cliniquement pertinent.
- **Non à l'abri des biais** — insuffisamment solide pour faire changer les pratiques.

L'évaluation se fait **par résultat**, pas globalement par étude : un même essai peut être à l'abri
des biais pour un critère (ex. mortalité totale, objectif) et à risque pour un autre (ex. critère
subjectif en ouvert).

---

## 9. Rappels transversaux

- **Biais = cause de faux positif** dans l'essai de supériorité. Les biais qui empêchent de
  conclure à l'effet (faux négatif) concernent surtout le promoteur, pas le clinicien qui juge la
  fiabilité d'un résultat « positif ». La LCA des essais **« négatifs »** obéit à une logique
  différente et n'est pas couverte ici.
- On **ne diagnostique pas** un biais a posteriori : on **vérifie la protection** (conception
  *et* réalisation).
- **Comparable ≠ identique.** Ne pas tester la table 1 des caractéristiques baseline.
- **Erreur aléatoire ≠ biais** ; **validité externe ≠ validité interne.**
- Le **double insu est un quadruple insu** : un seul maillon non aveugle rouvre la porte au biais.
- **Insu impossible** → exiger un critère **parfaitement objectif** ; sinon, risque de biais
  résiduel (l'adjudication en aveugle ne remplace pas l'insu).

---

## Sources

- Guide SFPT de lecture critique des essais thérapeutiques —
  <https://sfpt-fr.org/livreblancmethodo/source/lecturecritique.pdf>
