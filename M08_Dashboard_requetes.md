---
title: "Module 8 — Atelier : dashboards et requêtes"
subtitle: "Document participant autonome"
lang: fr-FR
---

# Concevoir un dashboard opérationnel à partir des données existantes

## Objectif

Parcourir un dashboard réel en lecture seule pour comprendre sa logique, puis concevoir hors plateforme le dashboard PeopleShop **Validation de commande** pour `orders-api`.

## Livrable

Vous produisez un dossier de conception comprenant :

- l'audit daté d'un dashboard existant ;
- cinq questions opérationnelles et les actions associées ;
- la définition de cinq requêtes et de cinq widgets au maximum ;
- les variables du dashboard ;
- une disposition allant de l'impact vers le diagnostic ;
- une grille de recette.

## Règle de sécurité

Travaillez uniquement en lecture dans Datadog. N'utilisez aucune commande **New Dashboard**, **Add Widgets**, **Configure**, **Edit**, **Clone**, **Save**, **Share** ou autre commande susceptible d'enregistrer une modification. Concevez le dashboard cible uniquement dans ce document.

## Prérequis

- Chrome connecté à Datadog ;
- accès en lecture aux dashboards et à APM ;
- convention de services et de tags du module 4 ;
- requêtes et observations issues des logs du module 5 ;
- hypothèse et indicateurs APM du module 6 ;
- indicateurs d'expérience utilisateur retenus au module 7 ;
- un document dans lequel consigner les réponses.

## Comment utiliser ce document

Effectuez chaque observation avant de lire la réponse de référence qui la suit. Les nombres, périodes et widgets de la plateforme peuvent évoluer : votre relevé daté prévaut sur l'état historique indiqué. À partir de la partie 3, vous quittez les données réelles de `app-api` pour concevoir le cas fictif PeopleShop ; ne mélangez jamais ces deux sources.

# Partie 1 — Parcourir les dashboards

## Étape 1 — Ouvrir la liste des dashboards

1. Dans le menu gauche, ouvrez **Dashboards**.
2. Sélectionnez **Dashboard List** ou **List**, selon le libellé visible.
3. Repérez le champ de recherche, les listes ou filtres disponibles et le nombre total de dashboards.
4. Notez la date, l'heure et le nombre affiché.
5. N'utilisez pas **New Dashboard**.

| Information | Valeur observée |
|---|---|
| Date et heure |  |
| Nombre de dashboards |  |
| Filtres ou listes disponibles |  |

### À quoi sert la liste des dashboards ?

**Réponse :** elle sert à retrouver, filtrer et ouvrir les vues partagées de l'organisation.

**Pourquoi :** un dashboard est une ressource organisée pour un lecteur et un usage ; la liste permet d'abord d'identifier la vue pertinente avant d'analyser ses widgets.

**Limite :** le nombre et les vues visibles dépendent des droits, des équipes et des filtres. Lors de la conception, 231 dashboards étaient visibles ; ce nombre historique n'est pas une valeur attendue.

## Étape 2 — Rechercher le dashboard de référence

1. Saisissez `Monitoring API` dans **Search dashboards**.
2. Attendez la mise à jour de la liste.
3. Ouvrez la ligne portant exactement ce titre.
4. Vérifiez qu'aucun mode d'édition n'est actif.
5. Si le dashboard est absent, choisissez un dashboard API ou service lisible et notez son titre comme substitution.

| Élément | Observation |
|---|---|
| Dashboard ouvert |  |
| Dashboard de substitution ? |  |
| Mode lecture confirmé ? |  |

### Pourquoi auditer une vue existante avant d'en concevoir une ?

**Réponse :** pour observer les conventions réelles, les types de widgets, les filtres et l'ordre de lecture déjà utilisés dans l'organisation.

**Pourquoi :** l'objectif n'est pas de copier la vue, mais d'identifier ce qui facilite ou ralentit une décision.

**Limite :** un titre ou une mise en page ne révèle pas forcément l'intention historique de son auteur.

## Étape 3 — Lire le bandeau de contexte

1. Repérez la période active et notez-la.
2. Repérez **Filter by** ou la zone des variables.
3. Notez le nom et la valeur de chaque variable visible.
4. Repérez les commandes de pause, actualisation ou sélection temporelle sans les utiliser.
5. Ne changez ni période ni variable.

| Élément | Valeur observée |
|---|---|
| Période |  |
| Variable(s) |  |
| Valeur(s) active(s) |  |

### Pourquoi la période et les variables doivent-elles être lues avant les courbes ?

**Réponse :** elles définissent les données incluses dans la vue.

**Pourquoi :** une même courbe peut produire une interprétation différente sur une heure ou une semaine, ou avec un filtre spécifique.

**Limite :** une variable affichée dans le bandeau n'est pas nécessairement appliquée à chaque widget ; cela doit être vérifié dans la conception ou par un test autorisé.

## Étape 4 — Inventorier les widgets

1. Parcourez le dashboard de haut en bas.
2. Relevez le titre et le type apparent de chaque widget visible.
3. Reformulez la question à laquelle il semble répondre.
4. Écrivez **question ambiguë** lorsque le titre ne suffit pas.
5. N'ouvrez pas l'éditeur du widget.

| N° | Titre observé | Type apparent | Question supposée |
|---:|---|---|---|
| 1 |  |  |  |
| 2 |  |  |  |
| 3 |  |  |  |
| 4 |  |  |  |
| 5 |  |  |  |
| 6 |  |  |  |
| 7 |  |  |  |

### Quels widgets étaient visibles lors de la conception ?

**Réponse :** **Appels par resource**, **Appels par status http**, **Appels par centre de gestion**, **Appels `/api/token`**, **Signatures Crit**, **Toutes les traces** et **Temps d'execution**.

**Pourquoi :** cette référence aide à vérifier que vous êtes sur la bonne vue, mais votre inventaire actuel reste la source de vérité.

**Limite :** le titre décrit parfois la donnée sans expliquer la décision ni l'action attendue.

## Étape 5 — Examiner un widget sans l'éditer

1. Choisissez une série temporelle visible.
2. Repérez son titre, sa légende, ses unités et les séries affichées.
3. Survolez un point si l'interface le permet sans enregistrer d'action.
4. Notez ce que l'infobulle apporte : horodatage, valeur et série.
5. Répétez l'observation sur un widget de classement ou de valeur, s'il existe.

### Pourquoi le type de widget doit-il suivre la question ?

**Réponse :** une **Timeseries** montre quand une valeur évolue, une **Query Value** résume une valeur clé et une **Top List** classe les principaux contributeurs.

**Pourquoi :** choisir une visualisation adaptée réduit le temps nécessaire pour décider.

**Limite :** une Query Value masque la chronologie et une Top List montre mal l'évolution dans le temps.

## Étape 6 — Évaluer l'ordre de lecture

1. Identifiez le premier widget qui qualifie l'impact global.
2. Identifiez le premier qui montre une chronologie.
3. Identifiez le premier qui réduit le périmètre.
4. Identifiez le premier qui oriente vers un diagnostic.
5. Écrivez **absent** lorsqu'un niveau n'est pas clair.

| Niveau | Widget correspondant | Position |
|---|---|---:|
| Impact |  |  |
| Chronologie |  |  |
| Périmètre |  |  |
| Diagnostic |  |  |

### Quel ordre facilite une qualification d'incident ?

**Réponse :** impact, chronologie, périmètre, puis diagnostic.

**Pourquoi :** le lecteur confirme d'abord que le service rendu est affecté, date la rupture, isole la population concernée puis choisit la vue détaillée.

**Limite :** l'absence d'un niveau est un constat de conception, pas un jugement sur tous les usages historiques du dashboard.

## Étape 7 — Examiner la navigation vers le détail

1. Repérez les menus contextuels, liens ou actions de survol d'un widget sans les activer s'ils ouvrent une édition.
2. Identifiez les destinations de lecture proposées : APM, traces, métriques, logs ou plein écran, selon la vue.
3. Notez la destination qui serait utile après une anomalie.

### Un dashboard doit-il contenir tout le diagnostic ?

**Réponse :** non. Il doit qualifier la situation et orienter vers la vue détaillée adaptée.

**Pourquoi :** accumuler toutes les métriques rend la lecture lente et duplique les outils d'investigation spécialisés.

# Partie 2 — Revoir les dimensions de contexte

## Étape 8 — Examiner la variable réelle

1. Relevez la question rendue possible par la variable actuelle.
2. Déterminez si son libellé explique clairement son usage.
3. Notez les dimensions absentes qui seraient utiles pour comparer des déploiements.

### Que permettait la variable historique de `Monitoring API` ?

**Réponse :** lors de la conception, `status_code` était visible avec la valeur `*` ; elle permettait une lecture par statut HTTP.

**Pourquoi :** ce filtre peut être pertinent pour isoler des réponses, mais il ne remplace pas `env`, `service` et `version` pour contextualiser un déploiement.

**Limite :** seule l'inspection des requêtes ou un test autorisé permet de confirmer son application à tous les widgets.

## Étape 9 — Passer du dashboard à la page APM

1. Ouvrez **APM**, puis **Services** dans le menu gauche.
2. Recherchez `app-api`.
3. Ouvrez sa page de service en lecture seule.
4. Repérez les filtres **env** et **version** ainsi que la période.
5. Revenez au dashboard avec l'onglet précédent du navigateur.

### Pourquoi effectuer ce détour par APM ?

**Réponse :** pour vérifier que les dimensions prévues pour le dashboard correspondent à des dimensions de navigation réelles du service.

**Pourquoi :** le module 4 a défini un contrat commun ; le dashboard doit le réutiliser pour faciliter le passage vers APM.

**Limite :** `app-api` sert uniquement à apprendre la navigation. Ses valeurs et seuils ne constituent aucune preuve pour PeopleShop.

## Étape 10 — Définir les variables du dashboard cible

Comparez votre proposition à la référence.

| Variable | Valeur par défaut de référence | Usage |
|---|---|---|
| `env` | `training` | éviter de mélanger les contextes |
| `service` | `orders-api` | conserver le service cible explicite |
| `version` | `*` | comparer puis isoler une version |

### Pourquoi limiter les variables à trois ?

**Réponse :** chaque variable ajoute de la flexibilité mais aussi une charge cognitive et un risque de combinaison incohérente.

**Pourquoi :** ces trois dimensions répondent aux usages récurrents du dashboard et reprennent la convention du module 4.

# Partie 3 — Concevoir les décisions PeopleShop

À partir de cette partie, toutes les valeurs concernent le scénario fictif PeopleShop.

## Étape 11 — Définir le lecteur et la situation

| Élément | Réponse de référence |
|---|---|
| Lecteur principal | équipe applicative propriétaire |
| Situation | qualification d'incident |
| Décision attendue | confirmer l'impact, dater la rupture, isoler une version et choisir la prochaine vue |
| Service | `orders-api` |
| Environnement par défaut | `training` |

### Pourquoi définir le lecteur avant les métriques ?

**Réponse :** parce qu'une équipe applicative, l'exploitation et un Product Owner ne prennent pas les mêmes décisions à partir d'une vue.

**Pourquoi :** le lecteur et la situation déterminent le niveau de détail, l'ordre et les actions attendues.

## Étape 12 — Formuler cinq questions

| Niveau | Question de référence | Action si dégradation |
|---|---|---|
| Impact | Le service présente-t-il plus d'échecs ? | confirmer l'incident ou poursuivre la surveillance |
| Chronologie | Quand la dégradation commence-t-elle ? | fixer la fenêtre d'investigation |
| Version | Une version concentre-t-elle la latence ou les erreurs ? | comparer le déploiement |
| Ressource | Quelle opération contribue le plus ? | ouvrir l'endpoint ou les traces |
| Dépendance | Quel composant aval devient lent ? | ouvrir APM ou Infrastructure sur la dépendance |

### Pourquoi commencer par des questions plutôt que par les métriques disponibles ?

**Réponse :** la décision détermine la question, puis la source, la requête, le widget et l'action.

**Pourquoi :** l'ordre inverse produit facilement des widgets décoratifs ajoutés seulement parce que la donnée existe.

# Partie 4 — Spécifier les cinq widgets

Les noms exacts des métriques seront validés avec les données réellement disponibles. Une source conceptuelle clairement décrite vaut mieux qu'un nom de métrique inventé.

## Étape 13 — Widget 1 : impact immédiat

| Propriété | Réponse de référence |
|---|---|
| Question | Le taux d'erreur de `orders-api` est-il anormal ? |
| Source | métriques APM de requêtes et d'erreurs |
| Filtre | `service:$service`, `env:$env` |
| Calcul | erreurs / requêtes totales × 100 |
| Widget | Query Value |
| Unité | `%` |
| Titre | `Taux d'erreur — $service — env:$env` |
| Action | ouvrir la chronologie si la valeur se dégrade |

### Pourquoi préférer un taux au seul nombre d'erreurs ?

**Réponse :** le taux rapporte les erreurs au trafic et rend deux périodes comparables.

**Limite :** le numérateur et le dénominateur doivent avoir le même périmètre, et le cas d'absence de trafic doit être traité.

## Étape 14 — Widget 2 : chronologie

| Propriété | Réponse de référence |
|---|---|
| Question | Quand le trafic et les erreurs changent-ils ? |
| Source | métriques APM |
| Filtre | `service:$service`, `env:$env` |
| Agrégation | volume ou taux selon la série |
| Regroupement | aucun par défaut |
| Événement | déploiement, s'il est disponible |
| Widget | Timeseries |
| Titre | `Trafic et erreurs dans le temps — $service` |
| Action | fixer la fenêtre et rechercher un changement concomitant |

### Un déploiement proche d'une hausse prouve-t-il la causalité ?

**Réponse :** non. La proximité temporelle produit une hypothèse à tester.

**Pourquoi :** une dépendance, une variation de trafic ou un autre changement peut expliquer la même chronologie.

## Étape 15 — Widget 3 : comparaison des versions

| Propriété | Réponse de référence |
|---|---|
| Question | Une version concentre-t-elle la latence ? |
| Source | distribution de latence APM |
| Filtre | `service:$service`, `env:$env`, toutes les versions |
| Mesure | p95 |
| Regroupement | `version` |
| Widget | Timeseries |
| Titre | `Latence p95 par version — $service` |
| Action | filtrer les traces de la version concernée |

### Quelle différence existe entre filtrer et regrouper par version ?

**Réponse :** filtrer conserve une ou plusieurs valeurs choisies ; regrouper crée une série par valeur.

**Pourquoi :** pour comparer les versions, il faut conserver plusieurs versions puis grouper par `version`.

## Étape 16 — Widget 4 : ressources prioritaires

| Propriété | Réponse de référence |
|---|---|
| Question | Quelle ressource est la plus lente ou la plus erronée ? |
| Source | APM Resources ou Endpoints |
| Filtre | `service:$service`, `env:$env` |
| Regroupement | ressource ou opération |
| Classement | p95 ou taux d'erreur |
| Limite | quelques éléments actionnables |
| Widget | Top List ou Table |
| Titre | `Top ressources par p95 — $service` |
| Action | ouvrir la ressource dans APM |

### Pourquoi limiter le nombre de ressources affichées ?

**Réponse :** une liste courte fait ressortir les principaux contributeurs et prépare une action.

**Limite :** un Top N peut masquer une ressource moins fréquente ; la vue APM détaillée reste nécessaire.

## Étape 17 — Widget 5 : dépendance PostgreSQL

| Propriété | Réponse de référence |
|---|---|
| Question | Le pool PostgreSQL contribue-t-il à la latence ? |
| Source pédagogique | `peopleshop.db.pool.used` ou attente du pool |
| Filtre | contexte PeopleShop validé |
| Widget | Timeseries |
| Titre | `Utilisation ou attente du pool PostgreSQL` |
| Vue suivante | APM Dependencies, traces ou Infrastructure |
| Action | corréler saturation, appels SQL et latence de validation |

### Pourquoi cette métrique doit-elle être validée avant construction ?

**Réponse :** son nom est pédagogique et sa présence réelle dans l'organisation n'est pas garantie.

**Pourquoi :** si elle n'existe pas, il faut choisir un signal réellement disponible ou utiliser le jeu de données préparé, sans inventer une requête exécutable.

**Limite :** dans l'environnement réel, une dépendance SQL, Redis ou HTTP visible peut servir à apprendre la navigation, mais elle ne prouve rien sur PostgreSQL dans PeopleShop.

# Partie 5 — Contrôler les requêtes

## Étape 18 — Vérifier filtres, regroupements et variables

Complétez cette recette pour vos cinq widgets.

| Widget | Filtre | Regroupement | Variables appliquées ? | Cardinalité maîtrisée ? |
|---:|---|---|:---:|:---:|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  | `version` |  |  |
| 4 |  | ressource |  |  |
| 5 |  |  |  |  |

### Quels regroupements doivent être évités sans besoin explicite ?

**Réponse :** pod, `order_id`, `tenant_id` et toute dimension presque unique ou éphémère.

**Pourquoi :** ils produisent trop de séries, surchargent la légende et rendent la lecture instable.

## Étape 19 — Vérifier agrégations et rollup

1. Justifiez une somme pour un volume additif.
2. Utilisez un percentile adapté pour une distribution de latence.
3. Vérifiez la compatibilité des séries d'un taux.
4. Notez l'effet possible d'une période plus longue.
5. N'imposez un rollup explicite que si son effet est compris.

### Pourquoi le p95 complète-t-il mieux la moyenne pour une latence dégradée ?

**Réponse :** la moyenne peut masquer la partie lente de la distribution ; le p95 rend visible une population d'observations lentes.

**Limite :** le p95 ne signifie pas que 95 % des requêtes ont exactement cette valeur, mais que 95 % sont inférieures ou égales à cette valeur.

### Que peut provoquer l'élargissement de la période ?

**Réponse :** Datadog peut utiliser des intervalles de rollup plus larges, ce qui lisse des pics courts.

**Pourquoi :** le nombre de points affichables est limité ; les données sont agrégées dans le temps.

## Étape 20 — Vérifier la formule du taux

```text
a = requêtes en erreur sur service:$service et env:$env
b = requêtes totales sur service:$service et env:$env
taux = 100 × a / b
```

### Quelles conditions rendent cette formule interprétable ?

**Réponse :** mêmes service, environnement, période, regroupements et définition de requête pour `a` et `b`, avec un traitement explicite de `b = 0`.

**Pourquoi :** diviser des séries de périmètres différents donne un résultat mathématique mais trompeur.

# Partie 6 — Organiser et recetter le dashboard

## Étape 21 — Dessiner la disposition

```text
+----------------------+--------------------------------------+
| Widget 1             | Widget 2                             |
| Impact immédiat      | Chronologie                          |
+----------------------+--------------------------------------+
| Widget 3                                                    |
| Comparaison des versions                                    |
+--------------------------------+----------------------------+
| Widget 4                       | Widget 5                   |
| Ressources prioritaires        | Dépendance                 |
+--------------------------------+----------------------------+
```

1. Recopiez les titres finaux dans les cadres.
2. Nommez le dashboard `[TRAINING] Service — Orders API`.
3. Indiquez l'owner `team-orders`.
4. Vérifiez que l'impact précède le diagnostic.

### Pourquoi le dashboard contient-il cinq widgets au maximum ?

**Réponse :** chacun répond à l'une des cinq décisions du scénario ; cette limite force à retirer les vues sans action associée.

## Étape 22 — Tester cinq scénarios

| Scénario | Réponse de référence |
|---|---|
| Taux d'erreur élevé, trafic normal | confirmer l'impact, puis identifier la ressource en erreur |
| p95 élevé sur une seule version | isoler la version dans APM et examiner le déploiement |
| Latence élevée sans erreur | rechercher une dépendance lente ou une saturation |
| Une ressource domine la Top List | ouvrir Endpoints ou Traces sur cette ressource |
| La dépendance devient lente | corréler trace, métrique de dépendance et infrastructure |

### Que démontre cette recette sur papier ?

**Réponse :** elle vérifie que chaque widget conduit à une décision et à une vue suivante avant toute construction dans Datadog.

**Limite :** elle ne valide ni la disponibilité des sources ni la syntaxe exacte des requêtes ; une recette technique sera nécessaire dans un environnement autorisé.

## Questions de synthèse corrigées

### Comment vérifier qu'une variable visible filtre réellement les widgets concernés ?

**Réponse :** inspecter la requête de chaque widget dans un environnement où cette lecture est autorisée, ou tester des valeurs contrastées sans enregistrer de modification.

**Explication :** la présence de la variable dans le bandeau ne garantit pas son utilisation par toutes les sources.

### Pourquoi un dashboard n'est-il pas un catalogue de métriques ?

**Réponse :** sa valeur vient de l'enchaînement décision, question, source, requête, widget et action, pas du nombre de courbes affichées.

### Quelle est la prochaine vue après une ressource lente ?

**Réponse :** la ressource ou l'endpoint APM, puis les traces correspondantes afin d'examiner les spans et dépendances contributrices.

## Aide au diagnostic

| Difficulté | Interprétation | Action en lecture seule |
|---|---|---|
| `Monitoring API` absent | renommage, suppression ou droits | choisir une vue API lisible et noter la substitution |
| Dashboard sans données | période, filtres ou source inactive | analyser la structure sans inventer de valeurs |
| `app-api` absent d'APM | activité ou période différente | choisir un service applicatif actif et noter la substitution |
| Source exacte inconnue | métrique non exposée | conserver une spécification conceptuelle et noter **à valider** |
| Métrique PostgreSQL absente | jeu PeopleShop non chargé | utiliser le support de repli ou une source préparée |
| Trop de widgets proposés | logique de catalogue | supprimer tout widget sans question et action distinctes |
| Variables trop nombreuses | flexibilité non justifiée | conserver `env`, `service` et `version` |

## Validation finale

- [ ] Le dashboard réel a été audité sans modification.
- [ ] La période, les variables et les observations ont été datées.
- [ ] `app-api` réel et `orders-api` fictif ne sont jamais confondus.
- [ ] Le dashboard cible est nommé `[TRAINING] Service — Orders API`.
- [ ] Il contient exactement cinq widgets répondant à cinq questions.
- [ ] Chaque widget prépare une action ou une navigation détaillée.
- [ ] `service`, `env` et `version` sont utilisés de manière cohérente.
- [ ] La comparaison des versions utilise un regroupement par `version`.
- [ ] Le taux utilise un numérateur et un dénominateur compatibles.
- [ ] Les unités, périodes et agrégations sont explicites.
- [ ] L'effet du rollup est pris en compte.
- [ ] Aucun regroupement à forte cardinalité n'est injustifié.
- [ ] La vue va de l'impact vers le diagnostic.
- [ ] Les sources pédagogiques non disponibles sont marquées **à valider**.
- [ ] Aucune ressource Datadog n'a été modifiée.
