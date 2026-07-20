---
title: "Module 5 — Atelier : APM et traçage distribué"
subtitle: "Document participant autonome"
lang: fr-FR
---

# Analyser une trace et formuler une hypothèse

## Objectif

Parcourir les principales vues APM sur un service réel, comparer plusieurs observations, lire le chemin critique d'une trace, puis produire une fiche PeopleShop séparant fait, hypothèse, limite et test suivant.

## Livrable

Vous produisez :

- un relevé daté du contexte APM réel ;
- une comparaison de trois observations comparables ;
- une représentation du chemin critique d'une trace ;
- une analyse des preuves pédagogiques PeopleShop ;
- deux attributs métier maîtrisés ;
- une conclusion structurée et un test suivant.

## Règle de sécurité

Travaillez uniquement en lecture. Ne créez ni règle APM, service, monitor, dashboard, vue sauvegardée, instrumentation, règle d'échantillonnage ou configuration. Ne recopiez aucun `trace_id` réel, paramètre sensible, URL complète, requête SQL, nom d'hôte ou donnée personnelle.

## Prérequis

- Chrome connecté à Datadog ;
- accès en lecture à **APM**, aux services et au Trace Explorer ;
- convention `env/service/version` du module 2 ;
- captures ou fiches PeopleShop fournies avec l'atelier.

## Comment utiliser ce document

Effectuez chaque manipulation avant de lire la réponse de référence qui suit la question. Les métriques et traces changent selon la période, les droits et l'échantillonnage : votre relevé daté prévaut sur les exemples historiques. Les étapes réelles utilisent `app-api` pour apprendre la navigation ; les étapes PeopleShop utilisent `orders-api` pour raisonner sur le fil rouge. Ne mélangez jamais leurs preuves.

# Partie 1 — Comprendre l'organisation de l'APM

## Étape 1 — Ouvrir APM Home

1. Dans le menu gauche, ouvrez **APM**, puis **Home** si cette page est proposée.
2. Repérez les accès aux **Services**, à la **Service Map**, aux **Traces** ou au **Trace Explorer**.
3. Repérez la période et les filtres globaux visibles.
4. Notez la date, l'heure et la période.

| Élément | Observation |
|---|---|
| Date et heure |  |
| Période |  |
| Entrées de navigation visibles |  |

### Quel est le rôle d'APM Home ?

**Réponse :** APM Home fournit un point d'entrée vers les services instrumentés, leur topologie, leurs ressources et leurs traces.

**Pourquoi :** l'APM organise la télémétrie autour du parcours des requêtes, et non autour d'une simple liste de métriques.

**Limite :** les menus visibles dépendent des fonctionnalités activées et des droits.

## Étape 2 — Parcourir la liste des services

1. Ouvrez **APM > Services**.
2. Repérez la recherche, les filtres `env`, `service`, `team` ou autres dimensions proposées.
3. Recherchez `app-api`.
4. Relevez, sans les interpréter isolément, le trafic, le taux d'erreur et la latence affichés.
5. Si `app-api` est absent, choisissez un service applicatif actif et notez la substitution.

| Élément | Observation |
|---|---|
| Service |  |
| Environnement |  |
| Trafic |  |
| Taux d'erreur |  |
| Latence |  |

### Que décrivent trafic, erreurs et latence ?

**Réponse :** ils décrivent la population agrégée des requêtes observées dans le périmètre et la période.

**Pourquoi :** ils permettent de confirmer un symptôme avant d'ouvrir un cas individuel.

**Limite :** ces mesures localisent un problème possible ; elles ne prouvent pas sa cause.

## Étape 3 — Ouvrir la Service Map

1. Ouvrez **APM > Service Map**.
2. Recherchez ou filtrez `app-api` si la vue le permet en lecture seule.
3. Repérez les nœuds et les relations avec des composants techniques.
4. Notez deux dépendances ou services voisins sans conclure à leur responsabilité.

| Relation observée | Interprétation prudente |
|---|---|
|  | appel ou relation observée dans la télémétrie |
|  | appel ou relation observée dans la télémétrie |

### La Service Map est-elle une CMDB exhaustive ?

**Réponse :** non. Elle représente les services instrumentés et les dépendances observées dans les données disponibles.

**Pourquoi :** une relation absente peut résulter de la période, de l'échantillonnage, de l'instrumentation ou des droits.

**Limite :** un lien visible prouve un appel observé, pas que la dépendance est défaillante.

## Étape 4 — Ouvrir la page de `app-api`

1. Depuis **Services** ou la Service Map, ouvrez la page du service.
2. Repérez **Service Summary**, **Resources**, **Traces**, **Dependencies**, **Deployments** ou leurs équivalents.
3. Relevez les filtres **operation**, **env**, **version** et la période.
4. Notez la version visible ou **non observée**.

### Pourquoi `env` et `version` doivent-ils accompagner `service` ?

**Réponse :** ils définissent le contexte du déploiement que l'on compare.

**Pourquoi :** une évolution de latence proche d'une version constitue une hypothèse de déploiement à tester.

**Limite :** la proximité d'une version et d'une dégradation ne démontre pas la causalité.

# Partie 2 — Passer du service à une ressource

## Étape 5 — Examiner Service Summary

1. Dans **Service Summary**, observez les courbes de requêtes, erreurs et latence.
2. Repérez les unités et la période.
3. Notez si une erreur est visible ou écrivez **aucune erreur observée sur la période**.

### Pourquoi ne faut-il pas ouvrir immédiatement la trace la plus lente ?

**Réponse :** il faut d'abord connaître le comportement agrégé et choisir une ressource comparable.

**Pourquoi :** une trace extrême peut être isolée et non représentative de l'impact global.

## Étape 6 — Parcourir Resources

1. Ouvrez **Resources**.
2. Repérez les colonnes ou mesures de requêtes, erreurs et latence.
3. Identifiez une ressource possédant plusieurs observations.
4. Notez son nom de manière anonymisée si sa valeur est sensible.

| Élément | Observation |
|---|---|
| Ressource choisie |  |
| Nombre ou volume visible |  |
| Latence |  |
| Erreurs |  |

### Quelle différence existe entre un service et une ressource ?

**Réponse :** le service est un composant stable instrumenté ; la ressource est une action stable exécutée par ce service, par exemple une route HTTP.

**Exemple PeopleShop :** `orders-api` est le service et `POST /api/orders/validate` une ressource.

## Étape 7 — Ouvrir une ressource

1. Ouvrez la ressource sélectionnée si le lien est disponible.
2. Repérez ses signaux agrégés et les accès aux traces associées.
3. Comparez son périmètre avec celui du service.
4. Revenez à la page précédente sans enregistrer de vue.

### Pourquoi analyser une ressource avant une trace ?

**Réponse :** elle rassemble des opérations comparables et réduit le risque de comparer des actions différentes.

**Pourquoi :** plusieurs traces de la même ressource permettent d'étudier la variabilité, les versions et les populations.

## Étape 8 — Examiner Dependencies

1. Ouvrez **Dependencies** ou la section équivalente.
2. Relevez deux composants appelés, s'ils sont visibles.
3. Notez les mesures proposées sans conclure à une saturation.

### Une dépendance lente dans cette vue est-elle automatiquement la cause racine ?

**Réponse :** non. Elle constitue une zone suspecte à confronter aux traces, métriques, logs et autres observations.

# Partie 3 — Utiliser le Trace Explorer

## Étape 9 — Ouvrir le Trace Explorer

1. Ouvrez **APM > Traces** ou **Trace Explorer**.
2. Repérez la barre de requête, la période, la liste des spans et le panneau de facettes.
3. Vérifiez qu'aucune vue sauvegardée ou configuration n'est en cours.
4. Saisissez temporairement la requête suivante, puis ne la sauvegardez pas :

```text
service:app-api
```

### Que contient principalement le Trace Explorer ?

**Réponse :** des spans recherchables, c'est-à-dire des opérations temporisées appartenant à des traces.

**Pourquoi :** une trace complète relie plusieurs spans par un identifiant commun et des relations parent-enfant.

## Étape 10 — Parcourir les facettes APM

1. Repérez **Service**, **Resource**, **Operation Name**, **Duration**, **Status**, **Env**, **Version** et **Peer Service**, lorsqu'elles existent.
2. Dépliez plusieurs facettes sans recopier de valeur sensible.
3. Notez leur usage.

| Facette | Usage de référence |
|---|---|
| Duration | isoler les opérations lentes |
| Status | distinguer `Ok` et `Error` |
| Env | cadrer le contexte de déploiement |
| Service | sélectionner le producteur du span |
| Resource | comparer une action stable |
| Operation Name | distinguer le type d'opération instrumentée |
| Version | comparer des déploiements |
| Peer Service | qualifier une dépendance appelée |

### Pourquoi choisir une valeur proposée par la facette Resource ?

**Réponse :** elle garantit que la valeur existe dans la période et évite d'inventer une syntaxe pour un nom contenant des caractères spéciaux.

## Étape 11 — Ajouter une ressource à la recherche

1. Sélectionnez une ressource suffisamment représentée depuis la facette.
2. Vérifiez que la requête combine le service et la ressource.
3. Notez sa forme générique sans recopier de valeur sensible.

```text
service:<service> resource_name:<ressource_proposée>
```

### Que faut-il maintenir identique pour comparer plusieurs observations ?

**Réponse :** au minimum le service, la ressource, l'environnement et une période pertinente ; la version est conservée ou volontairement comparée.

**Pourquoi :** des ressources différentes peuvent illustrer une diversité, mais pas prouver qu'une implémentation est plus lente qu'une autre.

## Étape 12 — Comparer trois spans

1. Choisissez trois spans du même service et de la même ressource.
2. Comparez leur durée, statut, environnement et version.
3. Écrivez **non observé** pour un attribut absent.

| Observation | Durée | Statut | Env | Version | Différence utile |
|---:|---:|---|---|---|---|
| 1 |  |  |  |  |  |
| 2 |  |  |  |  |  |
| 3 |  |  |  |  |  |

### Trois spans suffisent-ils à mesurer l'ampleur d'un incident ?

**Réponse :** non. Ils permettent d'apprendre la comparaison et de formuler une hypothèse, mais l'ampleur doit être évaluée avec les mesures agrégées et un échantillon approprié.

## Étape 13 — Examiner la distribution sans conclure sur un seul cas

1. Repérez, si disponible, la distribution ou l'histogramme de durée.
2. Identifiez la zone habituelle et les valeurs plus lentes.
3. Choisissez une observation représentative de la question étudiée, pas nécessairement l'extrême absolu.

### Pourquoi une trace représentative peut-elle être préférable à la plus lente ?

**Réponse :** l'extrême peut correspondre à un cas rare, une instrumentation particulière ou une population différente.

**Pourquoi :** une trace représentative d'un groupe dégradé aide à expliquer un motif reproductible.

# Partie 4 — Lire une trace

## Étape 14 — Ouvrir une trace

1. Ouvrez le détail d'un span, puis la trace associée si cette navigation est proposée.
2. Repérez le span racine, la durée totale, le statut et les services traversés.
3. N'inscrivez aucun identifiant réel dans le livrable.
4. Repérez la chronologie ou le waterfall.

### Quelle différence existe entre une trace et un span ?

**Réponse :** une trace représente le parcours complet d'une requête distribuée ; un span représente une opération délimitée dans ce parcours.

## Étape 15 — Lire le waterfall

1. Suivez les relations parent-enfant depuis le span racine.
2. Repérez les opérations imbriquées et les branches parallèles.
3. Identifiez les derniers achèvements qui déterminent la durée de bout en bout.
4. Notez les spans feuilles ou dépendances consommant effectivement du temps.

### Pourquoi la somme des durées des spans peut-elle dépasser la durée de la trace ?

**Réponse :** parce que les spans peuvent être imbriqués ou parallèles et que le temps d'un parent inclut souvent celui de ses enfants.

**Pourquoi :** additionner toutes les durées compte plusieurs fois certains intervalles.

## Étape 16 — Reconstituer le chemin critique

```text
[span racine] -> [span intermédiaire] -> [span feuille ou dépendance dominante]
```

| Élément | Observation |
|---|---|
| Durée totale |  |
| Chemin critique |  |
| Span ou dépendance dominant |  |
| Durée contributrice |  |
| Erreur visible |  |

### Le span le plus long prouve-t-il la cause racine ?

**Réponse :** non. Un parent long peut principalement attendre ses enfants, et une dépendance longue peut refléter une saturation située plus loin.

**Pourquoi :** le chemin critique localise le temps consommé ; la cause exige une preuve complémentaire.

## Étape 17 — Examiner une erreur

1. Si un span rouge est visible, repérez où l'erreur est enregistrée et si elle apparaît aussi sur ses parents.
2. Si aucune erreur n'est visible, notez **aucune erreur observée sur cette trace**.
3. Relevez le type d'erreur uniquement s'il n'est pas sensible.

### Le premier span rouge est-il nécessairement l'origine de l'erreur ?

**Réponse :** non. Une erreur peut être capturée sur un enfant puis propagée vers ses parents, ou enregistrée après l'échec initial.

**Pourquoi :** il faut examiner la chronologie, les attributs et les événements associés.

## Étape 18 — Repérer les pivots de corrélation

1. Dans le détail, repérez les accès vers les logs, les métriques de l'hôte, le profilage ou la dépendance, lorsqu'ils existent.
2. Notez le pivot qui serait le plus utile pour tester votre hypothèse.

### Pourquoi faut-il demander un second signal ?

**Réponse :** parce qu'une trace illustre un parcours individuel ; des logs corrélés ou une métrique de dépendance permettent de tester la reproductibilité et le mécanisme supposé.

# Partie 5 — Analyser les preuves PeopleShop

À partir d'ici, utilisez uniquement les observations pédagogiques suivantes.

## Étape 19 — Comparer les trois cas

| Observation | Référence normale | Cas lent | Fausse piste |
|---|---:|---:|---:|
| Version | `2.3.7` | `2.4.0` | `2.4.0` |
| Durée totale | 420 ms | 2 800 ms | 650 ms |
| Span validation | 90 ms | 150 ms | 110 ms |
| Span PostgreSQL | 120 ms | 2 200 ms | 130 ms |
| Paiement | 140 ms | 300 ms | 380 ms en erreur |
| `tenant_tier` | `standard` | `enterprise` | `standard` |

### Que montrent directement ces preuves ?

**Réponse :** dans le cas lent `2.4.0` et `enterprise`, le span PostgreSQL occupe la majeure partie des 2 800 ms ; dans la fausse piste, le paiement est en erreur mais la trace reste nettement plus courte.

**Pourquoi :** la durée dominante oriente le test vers PostgreSQL pour le cas lent, tandis que la couleur rouge du paiement ne suffit pas à expliquer la dégradation principale.

**Limite :** trois observations pédagogiques ne prouvent ni la fréquence générale ni le mécanisme précis de la lenteur.

## Étape 20 — Distinguer ce qui change et ce qui reste stable

| Dimension | Observation de référence |
|---|---|
| Ressource | même parcours de validation supposé |
| Version | `2.3.7` puis `2.4.0` |
| Population | `standard` puis `enterprise` |
| Contribution PostgreSQL | 120 ms puis 2 200 ms |
| Erreur paiement | présente sur la fausse piste, sans latence équivalente |

### Quelle hypothèse est justifiée ?

**Réponse :** la latence observée pourrait être liée au comportement de la dépendance PostgreSQL pour la population `enterprise` sur la version `2.4.0`.

**Limite :** les données ne permettent pas encore de choisir entre requête lente, attente de pool, saturation d'infrastructure ou autre mécanisme.

## Étape 21 — Proposer deux attributs métier

| Attribut | Valeurs contrôlées | Question permise |
|---|---|---|
| `tenant_tier` | `standard`, `enterprise` | une catégorie est-elle davantage affectée ? |
| `checkout_step` | `cart`, `validation`, `payment`, `confirmation` | quelle étape contribue à la dégradation ? |

### Pourquoi ces attributs sont-ils acceptables ?

**Réponse :** ils ont un vocabulaire borné, répondent à une question opérationnelle et ne désignent pas une personne précise.

**Limite :** email, contenu du panier, donnée de paiement et identifiant client presque unique sont refusés comme dimensions d'agrégation par défaut.

## Étape 22 — Rédiger la fiche de conclusion

```text
Périmètre : env:training, service:orders-api,
            resource_name:"POST /api/orders/validate"

Fait : sur la trace lente étudiée, le span PostgreSQL occupe 2,2 s
       sur une durée totale de 2,8 s.

Hypothèse : la latence du service pourrait être liée à la dépendance
            PostgreSQL pour la population enterprise observée.

Limite : les exemples étudiés ne démontrent ni l'ampleur générale,
         ni le mécanisme exact, ni une causalité liée à la version.

Test suivant : comparer davantage de traces de la même ressource par
               version et tenant_tier, rechercher les logs corrélés,
               puis examiner l'attente et l'utilisation du pool.
```

### Pourquoi séparer fait, hypothèse, limite et test ?

**Réponse :** cette structure empêche de transformer une observation locale en certitude et rend l'investigation reproductible.

## Questions de synthèse corrigées

### L'instrumentation automatique connaît-elle spontanément le sens métier ?

**Réponse :** non. Elle crée des spans pour les frameworks et bibliothèques compatibles, mais ne connaît pas nécessairement les étapes fonctionnelles propres à PeopleShop.

**Explication :** une instrumentation personnalisée n'est justifiée que lorsqu'une question utile reste sans réponse, avec des attributs bornés et non sensibles.

### Pourquoi plusieurs traces doivent-elles être comparées ?

**Réponse :** pour distinguer un cas isolé d'un motif reproductible et comparer versions, populations ou périodes équivalentes.

### Une recherche vide prouve-t-elle l'absence d'incident ?

**Réponse :** non. Elle peut résulter de la période, des filtres, de la rétention, des droits ou de l'échantillonnage.

## Aide au diagnostic

| Difficulté | Interprétation | Action en lecture seule |
|---|---|---|
| `app-api` absent | période ou activité différente | choisir un service applicatif actif et noter la substitution |
| Aucun span | période, droits ou échantillonnage | utiliser les fiches pédagogiques fournies |
| Aucune erreur | flux réel sain sur la période | analyser une trace lente puis la fausse piste fournie |
| Trace impossible à ouvrir | données non conservées ou droits | basculer vers les captures ou fiches préparées |
| Version absente | contexte non injecté ou non visible | noter **non observé** comme limite |
| Nombreux spans Symfony | instrumentation automatique détaillée | recentrer sur ressource, hiérarchie et chemin critique |
| Aucun pivot vers les logs | corrélation absente ou droits | noter le test conceptuel sans inventer de lien |

## Validation finale

- [ ] Le relevé réel est daté et sa période est explicite.
- [ ] L'analyse commence par les signaux agrégés du service.
- [ ] Service, ressource, trace et span sont distingués.
- [ ] La Service Map n'est pas interprétée comme une CMDB exhaustive.
- [ ] Trois observations comparables précèdent la conclusion.
- [ ] Le waterfall et les branches parallèles sont pris en compte.
- [ ] Le chemin critique est distingué du simple span le plus long.
- [ ] Une erreur observée n'est pas automatiquement appelée cause racine.
- [ ] `app-api` réel et `orders-api` fictif ne sont jamais mélangés.
- [ ] Deux attributs métier bornés et non sensibles sont retenus.
- [ ] Fait, hypothèse, limite et test suivant sont séparés.
- [ ] Le test suivant demande au moins une preuve complémentaire.
- [ ] Aucun identifiant ou contenu sensible réel n'est conservé.
- [ ] Aucune ressource Datadog n'a été modifiée.
