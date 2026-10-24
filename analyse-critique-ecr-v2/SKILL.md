---
name: analyse-critique-ecr-v2
description: Réaliser une analyse critique complète d'un essai clinique randomisé (ECR) en français, orientée HTA et décision réglementaire. Utilise cette compétence quand l'utilisateur demande une "analyse critique", une "lecture critique d'article (LCA)", une "évaluation méthodologique", "analyser cet essai", "appréciation critique", "review d'un RCT", ou téléverse un PDF d'essai clinique randomisé.
metadata:
  author: "Michel Cucherat"
  version: "0.2.0"
  url: "https://github.com/cucheratmi/spin-off"
---

# Analyse critique d'un essai clinique randomisé

## Objectif

Produire un rapport d'analyse critique d'un ECR qui répond à la question : **quels sont les bénéfices cliniques démontrés et cliniquement pertinent
qui pourraient éventuellement conduire à intégrer le traitement évalué dans la
stratégie thérapeutique ?**

Suivre scrupuleusement la démarche et les critères mentionnés dans cette skill.
Le rapport doit être concis tout en étant suffisamment transparent dans la logique suivie pour l'évaluation des différents critères.    

Si l'article rapporte simultanément 2 essais de phase 3 identiques voir `references/essais-jumeaux`.


## Principe axiomatique

Il faut des **preuves au-delà de tout doute raisonnable d'un bénéfice
clinique pertinent** pour intégrer un nouveau traitement dans la
stratégie thérapeutique. Le doute profite au statu quo, pas au
traitement évalué. Les tendances favorable non démontrée, même de forte 
ampleur ou cohérentes avec des d'autres arguments ne sont pas à
discuter car purement exploratoires.

## Workflow

### Étape 1 — Lire intégralement l'article

Lire le PDF de l'essai (et le supplément si fourni). Si l'article n'est
pas téléversé, demander à l'utilisateur de le fournir avant de
continuer.

Identifier en passant : nom de l'essai, sponsor, registre (NCT…), revue,
année, phase.

**Classer les documents fournis par ordre chronologique de conception**
(protocole initial, amendements datés, plan d'analyse statistique/SAP,
design paper publié avant les résultats, puis article de résultats).
Le document le plus ancien disponible est la seule référence valable
pour qualifier ce qui a été décidé « a priori » ; l'article de résultats,
même dans sa section Méthodes, décrit ce qui a été fait — pas forcément
ce qui avait été prévu avant le verrouillage de la base.

### Étape 2 — Décrire l'étude (PICOT-S)

Extraire de manière concise : - **P** Population : critères d'inclusion
/ exclusion, n randomisés, caractéristiques de base - **I** Intervention
: molécule, dose, schéma, durée - **C** Comparateur : nature (placebo,
traitement actif, soins standards), pertinence - **O** Outcomes :
critère principal, critères secondaires avec controles de la
multiplicité, critères exploratoires, sécurité - **T** Timing : durée de
suivi, dates de l'essai - **S** Setting : pays, type de centres,
contexte de soin - **Méthodologie** : design (parallèle, cross-over,
factoriel, adaptatif), nature de l'hypothèse principale déclarée a
priori (supériorité / non-infériorité), randomisation, aveugle, plan de contrôle du
risque alpha, analyses intermédiaires, arrêt prématuré de l'essai, population d'analyse principale
(ITT, mITT, per-protocole) ou méthode de gestion des événements
intercurrents, gestion des données manquantes

### Étape 3 — Classer chaque résultat

Pour chaque résultat mis en avant dans l'essai, appliquer la grille de classification
(voir `references/classification-resultats.md`). Les catégories sont :

1.  **Suffisamment probant** — démontré, pertinent, balance B/R
    favorable → peut justifier un changement de stratégie
2.  **Démontré mais balance B/R défavorable** — bénéfice statistiquement
    démontré mais contrebalancé par des effets indésirables
3.  **Insuffisamment probant** — suggéré mais non formellement démontré
4.  **Non concluant (NS)** — non statistiquement significatif
5.  **Effet délétère susceptible de contrebalancer le bénéfice** —
    invalide la possibilité d'introduction
6.  **Effet délétère certain mais ne contrebalançant par le bénéfice** —
    à signaler en sécurité

Pour chaque résultat, détailler : - **Critère de jugement** (et
population concernée si sous-groupe) - **Taille de l'effet** (effet
relatif + effet absolu) - **Contrôle du risque alpha global**
(signification statistique en terme de risque alpha global) - **Risque
de biais** (voir `references/risque-de-biais.md`) - **Pertinence
clinique** (cliniquement signifiant ? MCID franchi ? critère pertinent
pour patient/régulateur ?)

Ne pas considérer tous les résultats mais seulement ceux qui peuvent être décisifs pour la décision. 
Pour les sous-groupes ne pas tenir compte à ce niveau des résultats des analyses en sous-groupes ordinaires mais seulement ceux avec contrôle de la multiplicité.


### Étape 4 — Conclusion factuelle

Rédiger une conclusion synthétique, uniquement en utilisant les éléments analysés précédemment, qui répond explicitement : 
- Quels résultats sont **suffisamment probants** pour garantir l'intérêt du
traitement évalué ?
- Quels effets indésirables menacent la balance B/R ? 
- Quelles **incertitudes résiduelles** justifient de ne pas inclure le traitement évalué dans la
stratégie thérapeutique ?

### Étape 6 — Produire un rapport en markdown

Suivre la structure définie dans `references/structure-rapport.md`.

## Règles non négociables

- **ne pas tenir compte** de la discussion et de la conclusion des
  auteurs, ni de commentaires externes. N'utiliser que les informations
  disponibles dans les documents fournis.
- **ne pas mentionner d'autres interprétations des résultats** que celle effectuées avec les instructions et les critères mentionnés dans cette skill   
- **Ne jamais surestimer un résultat** : un résultat post-hoc, sans
  controle de la multiplicité, ou de sous-groupe non pré-spécifié reste
  « insuffisamment probant », même si p \< 0,05.
- **Vérifier systématiquement la nature a priori de l'hypothèse testée**
  en remontant au document de conception ou au protocole le plus ancien disponible — jamais à la
  seule section Méthodes de l'article de résultats. Une bascule entre
  types d'hypothèses (ex. non-infériorité → supériorité après échec de la
  non-infériorité) n'est recevable comme hypothético-déductive que si un
  document antérieur au verrouillage de la base la mentionne
  explicitement ; à défaut, le résultat qui en découle est
  « insuffisamment probant », quels que soient sa significativité
  statistique et son absence de biais.
- **Toujours quantifier** : taille d'effet relatif (RR: risk ratio, HR: hazard ratio, OR: odds ratio) et taille d'effet absolue (différence de
  risque (RD), différence de médianes, delta RMST).
- **Toujours examiner la sécurité** : un essai sans analyse approfondie
  des effets indésirables est incomplet. Une décision d'inclusion exige
  une balance B/R explicite.
- **Citer les sources** : pour chaque chiffre, indiquer la table ou
  figure de l'article.
- **Ne pas combler les manques** : si une donnée n'est pas dans
  l'article, l'écrire explicitement (« non rapporté ») plutôt que
  d'inventer ou d'extrapoler.


## Ressources de référence

- [Guide SFPT de lecture critique des essais
  thérapeutiques](https://sfpt-fr.org/livreblancmethodo/source/lecturecritique.pdf)
