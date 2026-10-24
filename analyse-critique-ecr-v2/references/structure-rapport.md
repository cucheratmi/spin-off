# Structure du rapport MarkDown d'analyse critique

Format cible : `.md`. Style sobre, orienté décideur HTA.

## Page de garde

- Titre : « Analyse critique de l'essai [NOM_ESSAI] »
- Sous-titre : référence bibliographique complète (auteurs, journal, année, DOI)
- Date de l'analyse
- Mention : « Document généré par IA »
- Références : mentionner impérativement que l'analyse a été effectuée suivant une démarche rigoureuse présentée dans le document [analyse des essais cliniques](https://sfpt-fr.org/livreblancmethodo/part4/file_0.htm) de la Société Française de Pharmacologie et de Thérapeutique (SFPT). Les concepts statistiques et les principes méthodologiques pris en compte dans cette analyse sont présentés avec leur justification dans le [livre blanc de méthodologie](https://sfpt-fr.org/livreblancmethodo/index.htm) de la SFPT.
  

## 1. Synthèse exécutive (1 page max)

À mettre en premier après la page de garde — c'est ce que liront les décideurs pressés.

- **Question évaluée** : 1 phrase
- **Réponse synthétique** : le traitement doit-il être inclus dans la stratégie thérapeutique ? Pour qui, à quelle place ?
- **Principaux résultats démontrés**
- **Principales limites / incertitudes résiduelles**
- **Recommandation** (3-5 lignes)


## 2. Description de l'étude

### 2.1 Contexte et rationnel
- Pathologie, besoin médical non couvert, mécanisme d'action
- Place attendue du traitement dans la stratégie thérapeutique

### 2.2 Méthodologie (PICOT-S)
- Population (critères I/E, n randomisés, n analysés)
- Intervention (molécule, dose, schéma, durée)
- Comparateur (nature, justification, dose, durée)
- Critères de jugement (principal, secondaires hiérarchisés, exploratoires, sécurité)
- Timing (suivi, dates)
- Setting (pays, centres, contexte)

### 2.3 Analyse statistique
- Hypothèses (taille d'effet attendue, puissance, alpha)
- Gestion de la multiplicité, liste des critères avec gestion de la multiplicité (mention que les autres critères ne permettant pas d'inférer l'effet du traitement)
- Population d'analyse principale (ITT, mITT), gestion des événements intercurrents
- Gestion des analyses intermédiaire (contrôle du risque alpha global, préservation de l’intégrité scientifique de l'essai) 
- Gestion des données manquantes


## 3. Évaluation du risque de biais 

- Justification 1-2 phrases par domaine
- Jugement global

## 4. Evaluation de la pertinence clinique
- pertinence du comparateur (est-ce un comparateur loyal, correspondant ua meilleur traitement disponible à la date d'aujourd'hui ?)
- pertinence des critères de jugement (sont-ce des critères cliniques et non pas intermédiaires ?)
- pertinence de la taille de l'effet (la magnitude de l'effet est-elle suffisante pour être cliniquement pertinente ? en particulier vis à vis de la lourdeur du traitement, de sa safety et de la pertinence clinique du critère) 
- pertinence des patients étudiés (la population inclus n'est pas hyper-sélectionnée par rapport à la population visée ? En cas de run-in avant randomisation, quel est son retentissement sur la pertinence clinique des patients inclus ?)

## 5. Résultats par catégorie

Présenter les résultats par catégorie selon `classification-resultats.md`.

Pour chaque catégorie, tableau standardisé :

|  Critère de jugement | Résultat | Signification statistique | Risque de biais | Pertinence clinique |
|---|---|---|---|---|
| … | … | … | … | … |

Définition des colonnes:
- colonne 'Résultat' : ne mentionner que la taille de l'effet et son intervalle de confiance
- colonne 'Signification statistique' : indiquer si le résultat est statistiquement significatif en terme de risque alpha global, préciser al p value et le seuil de la signification statistique compte tenu de la méthode de gestion de la multiplicité utilisé. En cas de résultat que nominalement significatif, mentionner explicitement : non statistiquement significatif en terme de risque alpha global.
- colonne 'Pertinence clinique' : mentionner la pertinence du critère, du comparateur, de la taille de l'effet traitement et la balance bénéfice risque.

## 6. Sécurité et balance bénéfice/risque

- Effets indésirables susceptible de contrebalancer le bénéfice quantitativement ou qualitativement
- Effets indésirables d'intérêt particulier
- Décès liés au traitement
- Calcul explicite de la balance B/R sur les principaux critères

## 7. Limites et points de vigilance

- Validité externe (généralisation à la population française)
- Comparateur (loyal vs dépassé)
- Homogénéité de l'effet pour tous les patients inclus (avec une vigilance particulière pour l'inclusion de patients pour lesquels le bénéfice attendu était spéculatif)
- Conflits d'intérêt et rôle du sponsor
- Cohérence protocole / SAP / publication
- Données manquantes
- Suivi insuffisant pour des outcomes à long terme

## 8. Conclusion factuelle


Réponse explicite à : Quels résultats sont **suffisamment probants** pour démontrer l’intérêt clinique du traitement étudié ?

Ne pas se positionner sur l’intérêt ou pas du traitement dans la stratégie thérapeutique. 

---

## Style éditorial

- Phrases courtes, vocabulaire précis
- Pas de marketing, pas d'emphase superflue
- Chaque chiffre cité doit pointer vers une table/figure de l'article
- Les résultats NS sont présentés comme « non démontrés », **pas** comme « équivalents »
- Quand une donnée manque, l'écrire : « Non rapporté dans l'article »
