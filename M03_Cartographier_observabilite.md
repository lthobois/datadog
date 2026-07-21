---
title: "Module 3 — Atelier : cartographier l'observabilité"
subtitle: "Document participant autonome"
lang: fr-FR
---

# Naviguer dans Datadog et cartographier l'observabilité d'un service

## Objectif opérationnel

Parcourir les principales vues d'exploitation de Datadog en lecture seule, comprendre la fonction de chacune et construire un chemin d'investigation autour du service réel `eu-interfaces`.

## Résultats produits

À l'issue de l'atelier, vous disposez :

- d'un relevé factuel et daté de `eu-interfaces` ;
- d'une carte simplifiée de ses dépendances ;
- d'une cartographie des principales portes d'entrée Datadog ;
- d'une matrice « question → signal → vue → réponse » ;
- d'un parcours d'investigation réutilisable.

## Règle de sécurité

Cet atelier est intégralement réalisé en lecture seule sur une plateforme réelle et partagée.

Ne cliquez pas sur les commandes suivantes :

- `Edit` ;
- `Configure` ;
- `Create` ;
- `New` ;
- `Declare Incident` ;
- `Mute` ;
- `Delete` ;
- `Service Config` ;
- `Settings`.

Ne créez et ne modifiez aucun service, dashboard, monitor, SLO, incident, pipeline, index, intégration ou paramètre.

Si une commande de modification est ouverte par erreur, fermez-la sans enregistrer.

## Prérequis

- navigateur connecté à l'organisation Datadog ;
- accès en lecture aux domaines utilisés ;
- période contenant des données pour `eu-interfaces` ;
- possibilité de prendre des notes dans ce document ou dans un éditeur de texte.

## Comment utiliser ce document

Les valeurs de la plateforme évoluent. Vous devez donc relever les valeurs visibles au moment de l'atelier.

Chaque question pédagogique est immédiatement suivie de trois éléments :

- **Réponse** : la réponse de référence ;
- **Pourquoi** : ce que cette réponse permet de comprendre ;
- **Limite** : ce que l'observation ne permet pas encore de conclure.

Lorsque l'interface ne contient pas une information attendue, écrivez `non observé`. Une absence de donnée constitue une observation ; elle ne doit pas être remplacée par une valeur inventée.

# Partie 1 — Se repérer dans l'interface

## Étape 1 — Identifier les repères permanents

1. Observez le menu gauche sans ouvrir de commande de configuration.
2. Repérez **Home**.
3. Repérez **Search Datadog**.
4. Repérez **Bits AI**.
5. Repérez les commandes permettant de réduire ou de masquer le menu.
6. Repérez **Integrations**, **Profile** et **Help** en bas du menu.
7. Relevez les domaines visibles parmi :
   - Dashboards ;
   - Monitoring ;
   - Developer Portal ;
   - Incident Response ;
   - Automation ;
   - Infrastructure ;
   - Cloud Cost ;
   - APM ;
   - Digital Experience ;
   - Software Delivery ;
   - Security ;
   - Data Observability ;
   - AI Observability ;
   - Errors ;
   - Metrics ;
   - Logs.

| Repère | Observation sur votre interface |
|---|---|
| Page ouverte par Home |  |
| Raccourci de Search Datadog |  |
| Bits AI visible | oui / non |
| Menu complet ou réduit |  |
| Domaines absents ou désactivés |  |

### Question — Pourquoi le menu ne doit-il pas être parcouru de haut en bas pendant une investigation ?

**Réponse :** parce que le menu regroupe des domaines d'usage et ne décrit pas une procédure. Il faut ouvrir un domaine pour répondre à une question précise.

**Pourquoi :** une lenteur utilisateur peut conduire vers RUM, APM, Metrics ou Logs. Ces portes d'entrée sont complémentaires, mais elles ne répondent pas à la même question.

**Limite :** un menu visible ne prouve pas que le produit est configuré, alimenté, souscrit pour tous les usages ou adopté par les équipes.

## Étape 2 — Utiliser la recherche globale

1. Activez **Search Datadog** avec `Ctrl+K` sous Windows ou Linux, ou `Cmd+K` sous macOS.
2. Saisissez `Monitoring API`.
3. Ne sélectionnez pas une commande de création.
4. Si le dashboard apparaît dans les résultats, ouvrez-le.
5. S'il n'apparaît pas, fermez la recherche et passez par **Dashboards > Dashboard Lists**.
6. Dans la liste, utilisez le champ de recherche pour retrouver `Monitoring API`.

### Question — Quelle différence existe entre la recherche globale et un Explorer ?

**Réponse :** la recherche globale retrouve rapidement une page ou un objet connu ; un Explorer analyse une population de données sur une période avec une requête, des filtres et des facettes.

**Pourquoi :** `Ctrl+K` convient pour atteindre un dashboard ou un monitor dont le nom est connu. Le Log Explorer ou le Trace Explorer convient pour rechercher des événements répondant à des critères.

**Limite :** retrouver un objet par son nom ne garantit pas que sa période, sa requête ou ses variables correspondent à votre investigation.

# Partie 2 — Partir d'un dashboard puis rejoindre APM

## Étape 3 — Lire le dashboard `Monitoring API`

1. Vérifiez le titre du dashboard.
2. Repérez le sélecteur de période.
3. Repérez le fuseau horaire affiché.
4. Repérez les variables de template éventuellement disponibles.
5. Observez les widgets sans les modifier.
6. Choisissez un widget contenant une métrique de volume, d'erreur ou de latence.
7. Relevez son titre et la question à laquelle il peut répondre.

| Élément | Observation |
|---|---|
| Titre du dashboard |  |
| Période |  |
| Fuseau horaire |  |
| Variables visibles |  |
| Widget choisi |  |
| Signal représenté | métrique / log / trace / autre |

### Question — À quoi sert un dashboard dans une investigation ?

**Réponse :** il fournit une vue synthétique et partagée de plusieurs indicateurs afin de repérer une rupture, une tendance ou un périmètre affecté.

**Pourquoi :** le dashboard est une bonne porte d'entrée pour répondre à « que se passe-t-il ? » et « depuis quand ? » avant de descendre vers une donnée individuelle.

**Limite :** un widget agrégé ne démontre généralement pas la cause. Il faut lire sa requête et poursuivre vers le service, l'Explorer ou l'événement individuel adapté.

## Étape 4 — Vérifier systématiquement la période

1. Notez la période affichée dans le dashboard.
2. Dans les étapes suivantes, comparez la période de chaque vue avec cette période de départ.
3. Si une vue est vide sur une période courte, élargissez uniquement le sélecteur temporel, par exemple vers les dernières 24 heures.
4. Ne modifiez aucune requête enregistrée ni aucun widget.

### Question — Pourquoi une vue vide ne prouve-t-elle pas une absence de données ?

**Réponse :** parce que la période, le fuseau, la requête, l'environnement, les droits ou la rétention peuvent exclure les données recherchées.

**Pourquoi :** dans l'organisation de démonstration, le Log Explorer peut contenir très peu de résultats sur 15 minutes et plusieurs millions d'événements sur 24 heures.

**Limite :** élargir la période augmente aussi le bruit. Une fois les données retrouvées, il faut réduire la portée avec `service`, `env`, `version` ou d'autres attributs pertinents.

## Étape 5 — Ouvrir APM Home

1. Dans le menu gauche, cliquez sur **APM**.
2. Vérifiez que **APM Home** est affiché.
3. Repérez le filtre **Env**.
4. Notez sa valeur sans la modifier.
5. Repérez le sélecteur de période.
6. Notez la période.
7. Repérez la synthèse ou la liste des services.

| Élément | Observation |
|---|---|
| Environnement affiché |  |
| Période affichée |  |
| Nombre de services, si visible |  |
| État global ou message principal |  |

### Question — Pourquoi faut-il noter l'environnement et la période avant de lire une valeur ?

**Réponse :** parce qu'une métrique n'a de sens que dans le périmètre où elle a été calculée.

**Pourquoi :** un taux d'erreur nul en `dev` sur 15 minutes ne décrit pas nécessairement le comportement de `prod` sur une heure.

**Limite :** même avec la bonne période et le bon environnement, une agrégation peut masquer une version, une opération ou une population particulière.

## Étape 6 — Lire la liste des services

1. Repérez la liste ou le catalogue des services.
2. Recherchez `eu-interfaces`.
3. Repérez les colonnes disponibles parmi **Requests**, **Error Rate**, **p95 Latency**, **Last Deploy**, **Env**, **Dashboards** et **Monitors**.
4. Recopiez uniquement les valeurs visibles.
5. Utilisez `non observé` lorsqu'une valeur manque.

| Élément | Observation datée |
|---|---|
| Service | `eu-interfaces` |
| Requests |  |
| Error Rate |  |
| p95 Latency |  |
| Last Deploy |  |
| Env |  |
| Dashboards associés |  |
| Monitors associés |  |

### Question — Un taux d'erreur nul prouve-t-il que le service rendu est sain ?

**Réponse :** non. Un service peut répondre sans erreur tout en étant trop lent, incomplet ou inutilisable pour une partie des utilisateurs.

**Pourquoi :** le taux d'erreur décrit une dimension technique. La latence, le volume, les actions RUM et les indicateurs métier complètent l'évaluation.

**Limite :** une valeur p95 ou un taux d'erreur n'explique pas seul où le temps est consommé ni pourquoi le comportement apparaît.

## Étape 7 — Comparer des composants techniques

1. Dans la liste, repérez deux entités parmi `phpredis`, `pdo`, `mysqli`, `curl`, `guzzle`, `memcached` ou une autre entité visible.
2. Relevez leur environnement, leur latence p95 et leur taux d'erreur lorsque ces données sont affichées.
3. N'inventez pas leur rôle.

| Entité | Env | p95 Latency | Error Rate | Qualification correcte |
|---|---|---:|---:|---|
|  |  |  |  | fait observé / rôle à confirmer |
|  |  |  |  | fait observé / rôle à confirmer |

### Question — Le nom `pdo` ou `phpredis` suffit-il à prouver le rôle exact du composant ?

**Réponse :** non. Le nom suggère une technologie ou une bibliothèque, mais la relation exacte doit être confirmée par la carte des dépendances, une trace ou la configuration connue du système.

**Pourquoi :** transformer trop vite un nom technique en cause racine produit une conclusion prématurée.

**Limite :** la vue peut présenter des services instrumentés et des services inférés. Leur granularité dépend du nommage et de l'instrumentation.

# Partie 3 — Naviguer dans la page du service

## Étape 8 — Ouvrir `eu-interfaces`

1. Cliquez sur le lien `eu-interfaces`.
2. Vérifiez que le service apparaît dans le titre ou le sélecteur.
3. Repérez les filtres disponibles parmi **operation**, **env**, **version** et la période.
4. Conservez leurs valeurs.
5. Repérez les onglets ou sections disponibles.

### Question — Quelle est l'unité d'analyse d'une Service Page ?

**Réponse :** une population de requêtes et de ressources rattachées à un service dans un périmètre temporel et contextuel donné.

**Pourquoi :** la Service Page permet de comparer volume, erreurs, latence, opérations, versions et dépendances avant de choisir une trace individuelle.

**Limite :** elle agrège plusieurs requêtes. Le détail d'un cas précis nécessite le Trace Explorer puis l'ouverture d'une trace.

## Étape 9 — Parcourir les vues internes du service

Repérez chaque vue. Ouvrez-la uniquement si elle est disponible et revenez ensuite à la page du service.

### Service Summary

1. Ouvrez **Service Summary**.
2. Repérez les courbes de requêtes, d'erreurs et de latence.

**Réponse — Ce que cette vue apporte :** l'évolution globale de la charge et de la performance du service.

**Pourquoi :** elle répond d'abord à « le service se dégrade-t-il ? » et « depuis quand ? ».

**Limite :** une courbe ne localise pas nécessairement l'opération ou la dépendance responsable.

### Endpoints ou Resources

1. Ouvrez **Endpoints** ou **Resources** selon le libellé disponible.
2. Repérez les opérations et leurs indicateurs.

**Réponse — Ce que cette vue apporte :** la comparaison des opérations ou ressources qui composent le trafic du service.

**Pourquoi :** elle permet d'identifier une route lente ou plus sujette aux erreurs que les autres.

**Limite :** un endpoint lent est un périmètre d'analyse, pas encore une cause.

### Deployments

1. Ouvrez **Deployments**.
2. Relevez la version visible, **First Seen**, **Last Seen** ou le statut affiché.
3. Relevez Requests/s, Error Rate et p95 Latency lorsqu'ils sont disponibles.

| Version | First Seen | Last Seen ou statut | Requests/s | Error Rate | p95 Latency |
|---|---|---|---:|---:|---:|
|  |  |  |  |  |  |

**Réponse — Ce que cette vue apporte :** un contexte de changement et une comparaison possible entre versions.

**Pourquoi :** si une rupture commence après l'apparition d'une version et concerne surtout cette version, le déploiement devient une hypothèse à tester.

**Limite :** la proximité temporelle ne démontre pas la causalité. Un autre changement ou une hausse de charge peut coïncider.

### Dependencies

1. Ouvrez **Dependencies** ou repérez la carte équivalente.
2. Identifiez `eu-interfaces`.
3. Relevez jusqu'à quatre dépendances visibles.
4. Notez le sens de la relation uniquement s'il est lisible.

**Réponse — Ce que cette vue apporte :** les relations observées ou inférées entre le service et des composants en amont ou en aval.

**Pourquoi :** elle aide à choisir la dépendance à vérifier lorsqu'une requête passe par plusieurs composants.

**Limite :** une relation ne prouve pas que la dépendance cause la dégradation. Il faut comparer ses métriques et examiner les spans.

### Traces

1. Ouvrez **Traces** ou **Trace Explorer**.
2. Vérifiez que le filtre `service:eu-interfaces` est présent ou que le service est bien sélectionné.
3. Repérez les facettes **Duration**, **Status**, **Env**, **Service** et **Resource**.
4. Ouvrez une trace représentative uniquement en lecture.
5. Repérez son waterfall et ses spans.

**Réponse — Ce que cette vue apporte :** le parcours d'une requête individuelle et le temps consommé par chaque span.

**Pourquoi :** une trace répond à « où cette requête a-t-elle passé son temps ? ».

**Limite :** le span le plus long peut être un symptôme ou une attente causée ailleurs. Il faut recouper avec les métriques et les logs.

### Errors

1. Ouvrez **Errors** si la vue est disponible.
2. Repérez les types d'erreurs, leur fréquence et les versions concernées.
3. Si aucune erreur n'est visible, notez `aucune erreur observée sur la période`.

**Réponse — Ce que cette vue apporte :** le regroupement d'occurrences similaires en problèmes exploitables.

**Pourquoi :** elle évite d'analyser séparément des milliers d'occurrences identiques et aide à mesurer leur impact.

**Limite :** l'absence d'erreur regroupée ne prouve pas l'absence de lenteur, d'erreur métier ou de problème non instrumenté.

### Infrastructure

1. Ouvrez **Infrastructure** ou le lien disponible vers Docker, conteneurs ou hôtes.
2. Repérez les ressources d'exécution reliées au service.
3. N'interprétez que les métriques effectivement affichées.

**Réponse — Ce que cette vue apporte :** le contexte d'exécution, par exemple CPU, mémoire, conteneurs, hôtes ou processus selon la collecte activée.

**Pourquoi :** elle permet de tester l'hypothèse d'une saturation ou d'un problème localisé sur une ressource.

**Limite :** un CPU élevé peut être une conséquence de la charge et non la cause initiale. Une infrastructure saine n'exclut pas un problème applicatif.

### Logs

1. Ouvrez **Logs** depuis le service si le lien est disponible.
2. Vérifiez que la requête conserve `service:eu-interfaces` ou un filtre équivalent.
3. Vérifiez la période.
4. Repérez les facettes **Service**, **Status**, **Host** et **Source**.
5. Ouvrez un log représentatif sans modifier la configuration.

**Réponse — Ce que cette vue apporte :** le détail textuel ou structuré d'un événement avec son contexte.

**Pourquoi :** un log peut fournir un message, une exception, une ressource, un identifiant de trace ou un attribut métier utile.

**Limite :** un log isolé ne démontre pas sa fréquence ni son impact. Il faut le replacer dans la chronologie et la population.

## Étape 10 — Construire la carte des dépendances

À partir des relations visibles, complétez la carte. Utilisez `?` lorsque le sens ou le rôle n'est pas confirmé.

```text
                  +------------------+
                  |                  |
                  v                  v
              [         ]        [         ]
                  ^                  ^
                  |                  |
               eu-interfaces ----------> [         ]
                  |
                  v
              [         ]
```

### Question — Quelle carte de référence est acceptable si les relations sont difficiles à lire ?

**Réponse :** une carte peut placer `eu-interfaces` au centre et proposer, sous réserve de confirmation :

```text
eu-interfaces
  |-- accès SQL --------> pdo / mysqli ?
  |-- cache ------------> phpredis / memcached ?
  +-- appel HTTP -------> curl / guzzle ?
```

**Pourquoi :** cette représentation distingue les entités réellement observées de l'interprétation technique suggérée par leur nom.

**Limite :** il s'agit d'une hypothèse et non d'une description validée de l'architecture de production.

# Partie 4 — Explorer les autres portes d'entrée

## Étape 11 — Ouvrir Developer Portal

1. Dans le menu gauche, cliquez sur **Developer Portal**.
2. Repérez les onglets **Home**, **Catalog**, **Scorecards**, **Reports** et **Actions** lorsqu'ils sont visibles.
3. Ouvrez **Catalog**.
4. Repérez les catégories disponibles : services, infrastructure, files, datastores, frontends, dépôts ou endpoints.
5. Recherchez `eu-interfaces` si le champ de recherche est disponible.
6. Repérez les angles **Ownership**, **Reliability**, **Performance**, **Security**, **Costs** et **Delivery** lorsqu'ils sont visibles.

### Question — À quoi sert Developer Portal ?

**Réponse :** il organise les entités logicielles, leur ownership, leurs métadonnées et des indicateurs provenant de plusieurs produits Datadog.

**Pourquoi :** il répond à « quel est ce service ? », « qui le possède ? » et « quelles ressources, règles ou vues lui sont associées ? ».

**Limite :** un catalogue alimenté ne garantit pas que toutes les métadonnées sont fiables. `env:none`, un ownership absent ou des noms incohérents indiquent un travail de gouvernance à réaliser.

### Question — Que sont les Scorecards et pourquoi peuvent-elles ne pas être configurées ?

**Réponse :** les Scorecards évaluent les entités du catalogue selon des règles ou standards, par exemple la présence d'un owner, d'un runbook, d'un monitor ou d'un SLO.

**Pourquoi :** elles rendent mesurable l'adoption de pratiques communes et permettent de suivre les écarts par équipe ou service.

**Pourquoi elles peuvent être absentes :** elles nécessitent un catalogue suffisamment fiable, des règles décidées collectivement et des responsabilités d'entretien. Une organisation peut alimenter le catalogue sans avoir encore défini ces standards.

**Limite :** un bon score mesure la conformité aux règles définies, pas automatiquement la qualité réelle du service.

## Étape 12 — Ouvrir Metrics

1. Cliquez sur **Metrics** dans le menu gauche.
2. Repérez **Overview**, **Explorer**, **Summary** et **Volume**.
3. Dans **Overview**, repérez les sources de métriques, le nombre d'Agents actifs et les volumes disponibles.
4. Ouvrez **Explorer** sans enregistrer de changement.
5. Repérez le champ de requête, l'agrégation, les regroupements et le sélecteur temporel.
6. Revenez à **Summary** et repérez la recherche d'une métrique et de ses tags.
7. Repérez **Volume** sans modifier la configuration.

### Question — Quelle différence existe entre Metrics Explorer et Metrics Summary ?

**Réponse :** Metrics Explorer sert à construire et visualiser une requête temporelle ; Metrics Summary sert à inventorier une métrique, ses métadonnées, ses tags et son activité récente.

**Pourquoi :** l'Explorer répond à une question d'analyse ; Summary aide à comprendre si la métrique existe, comment elle est taguée et si elle est exploitable.

**Limite :** une métrique disponible peut présenter une cardinalité excessive, des tags incohérents ou une fréquence inadaptée.

### Question — À quoi sert Metrics Volume ?

**Réponse :** cette vue aide à analyser le nombre de séries et la contribution des métriques ou tags aux volumes.

**Pourquoi :** la cardinalité influence la lisibilité, les performances et le coût. Identifier les dimensions inutiles permet de gouverner la collecte.

**Limite :** ne modifiez pas les tags indexés ou les réglages de métriques dans cet atelier ; ces changements peuvent modifier les analyses et la facturation.

## Étape 13 — Ouvrir Logs

1. Cliquez sur **Logs**.
2. Repérez **Log Explorer**, **Live Tail**, **Notebooks**, **Archive Search**, **Reports** et **Log Configuration** lorsque ces entrées sont visibles.
3. Restez dans **Log Explorer**.
4. Vérifiez le sélecteur de période.
5. Si les données sont insuffisantes, sélectionnez les dernières 24 heures.
6. Repérez la barre de requête.
7. Repérez les facettes **Service**, **Status**, **Host** et **Source**.
8. Filtrez en lecture avec `service:eu-interfaces` uniquement si des logs correspondants sont disponibles.
9. Retirez le filtre si aucun résultat n'existe et observez les services disponibles.

### Question — Quelle différence existe entre Log Explorer et Log Configuration ?

**Réponse :** Log Explorer recherche et analyse les logs collectés ; Log Configuration détermine comment ils sont traités, parsés, indexés, exclus, archivés ou transformés en métriques.

**Pourquoi :** l'exploitation répond à une question avec les données existantes. La configuration change la manière dont les données futures seront interprétées ou conservées.

**Limite :** une mauvaise configuration peut masquer des données, casser des attributs, augmenter les volumes ou modifier la rétention. Elle ne doit pas être changée sur une plateforme partagée pendant cet atelier.

### Question — À quoi sert Live Tail ?

**Réponse :** Live Tail affiche un flux de logs en temps réel, y compris avant leur éventuelle indexation selon l'architecture de collecte.

**Pourquoi :** il aide à vérifier qu'une source émet et que des événements arrivent actuellement.

**Limite :** il n'est pas adapté à l'analyse historique ni à la mesure d'une fréquence sur une longue période.

## Étape 14 — Ouvrir Monitoring et les monitors

1. Cliquez sur **Monitoring**.
2. Ouvrez la liste des monitors.
3. Repérez le champ de recherche et les filtres.
4. Repérez les états disponibles, notamment `OK`, `Alert`, `Warn` ou `No Data`.
5. Choisissez un monitor en lecture seule.
6. Ouvrez son détail.
7. Repérez son type, sa requête, sa fenêtre d'évaluation, son regroupement, ses seuils, son message et sa chronologie.
8. Revenez à la liste sans modifier le monitor.

### Question — À quoi sert un monitor ?

**Réponse :** un monitor évalue une condition sur des données Datadog et produit un état pouvant déclencher une notification et une action attendue.

**Pourquoi :** il transforme une observation continue en décision opérationnelle : ignorer, surveiller, diagnostiquer ou intervenir.

**Limite :** un monitor mal ciblé peut produire du bruit, masquer des populations dans une agrégation ou rester en `No Data` lorsque la source ne publie plus.

### Question — Que signifie `No Data` ?

**Réponse :** Datadog ne reçoit pas suffisamment de points correspondant à la requête pendant la fenêtre attendue.

**Pourquoi :** cela peut révéler une panne de collecte, un service arrêté, un filtre trop restrictif, une métrique intermittente ou un choix de configuration inadapté.

**Limite :** `No Data` ne signifie pas automatiquement que le service métier est indisponible. Il faut vérifier la source, la requête et la fenêtre.

## Étape 15 — Rechercher les Service Health Monitors

1. Dans la liste des monitors, recherchez `service health`.
2. Si aucun résultat n'apparaît, retirez le filtre et vérifiez les types de monitors existants.
3. Ne créez aucun monitor.

### Question — Qu'est-ce qu'un Service Health Monitor ?

**Réponse :** dans le contexte de l'APM, un Service Health Monitor surveille la santé d'un service à partir d'indicateurs comme le taux d'erreur, la latence ou d'autres métriques de service.

**Pourquoi :** il permet de détecter automatiquement qu'un service dépasse une condition jugée anormale et de notifier l'équipe responsable.

**Pourquoi il peut ne pas être configuré :** l'équipe peut utiliser des monitors de métriques personnalisés, des SLO, un autre outil d'alerte ou ne pas avoir encore défini les seuils et responsabilités nécessaires. L'absence de Service Health Monitor ne signifie donc pas que le service n'est pas observé.

**Ce qu'il apporte lorsqu'il est bien configuré :** une détection centrée sur le service, un périmètre explicite avec `service` et `env`, une notification actionnable et une chronologie de récupération.

**Limite :** un seuil statique ou une fenêtre mal adaptée peut générer du bruit. Le monitor doit préciser l'unité d'action, le destinataire et la conduite à tenir.

## Étape 16 — Rechercher les SLO

1. Dans **Monitoring**, repérez l'entrée **SLOs** ou **Service Level Objectives** si elle est visible.
2. Ouvrez la liste en lecture seule.
3. Recherchez `eu-interfaces`.
4. Si aucun SLO n'est trouvé, notez `aucun SLO eu-interfaces observé`.
5. Si un SLO existe, repérez son objectif, sa période, son indicateur et son budget d'erreur.

### Question — Qu'est-ce qu'un SLO ?

**Réponse :** un Service Level Objective exprime un niveau de fiabilité attendu sur une période, par exemple 99,9 % de requêtes réussies ou suffisamment rapides sur 30 jours.

**Pourquoi :** le SLO relie une mesure technique à une attente de service. Il permet de suivre un budget d'erreur et d'arbitrer entre fiabilité, changements et priorités produit.

**Pourquoi il peut ne pas être configuré :** il faut d'abord choisir un indicateur fiable, définir une cible réaliste, convenir du périmètre et attribuer la responsabilité. Une équipe peut disposer de monitors sans avoir encore formalisé de SLO.

**Ce qu'il apporte lorsqu'il est bien configuré :** une vision de tendance moins volatile qu'une alerte instantanée, un langage commun entre produit et technique, et une mesure de la fiabilité réellement délivrée.

**Limite :** un SLO mal choisi peut être respecté alors que l'utilisateur rencontre encore un problème. L'indicateur doit représenter une expérience ou une capacité réellement importante.

### Question — Quelle différence existe entre un monitor et un SLO ?

**Réponse :** un monitor détecte une condition exigeant une réaction ; un SLO mesure l'atteinte d'un objectif de fiabilité sur une période.

**Pourquoi :** le monitor soutient l'exploitation immédiate, tandis que le SLO soutient le pilotage de la fiabilité et du budget d'erreur.

**Limite :** les deux peuvent partager des données, mais ils ne doivent pas être confondus. Un service peut respecter son SLO tout en déclenchant ponctuellement un monitor.

## Étape 17 — Ouvrir Digital Experience et RUM

1. Cliquez sur **Digital Experience**.
2. Repérez les entrées liées à **Synthetic Monitoring**, **RUM** et **Session Replay**.
3. Ouvrez le RUM Explorer ou la liste des sessions.
4. Vérifiez la période ; utilisez les dernières 24 heures si nécessaire.
5. Repérez les sessions, vues, actions, erreurs et ressources disponibles.
6. N'ouvrez pas un Session Replay non préalablement autorisé.

### Question — Quelle différence existe entre RUM et Synthetic Monitoring ?

**Réponse :** RUM observe les sessions réellement vécues par les utilisateurs instrumentés ; Synthetic Monitoring exécute des tests contrôlés depuis des localisations et selon des scénarios définis.

**Pourquoi :** RUM révèle la diversité réelle des navigateurs, versions et parcours. Synthetic vérifie de manière répétable qu'un parcours critique fonctionne même lorsqu'aucun utilisateur réel ne l'exécute.

**Pourquoi Synthetic peut ne pas être configuré :** il faut choisir les parcours critiques, gérer les données de test, les localisations, l'authentification, la maintenance et le coût des exécutions.

**Limite :** RUM dépend de l'instrumentation et du consentement ; Synthetic dépend de la représentativité du scénario. Ils se complètent sans se remplacer.

# Partie 5 — Relier les questions aux vues et aux réponses

## Étape 18 — Utiliser la matrice de référence

La matrice suivante contient directement les réponses attendues. Utilisez-la pour justifier le prochain menu à ouvrir pendant une investigation.

| Question | Signal principal | Première vue | Réponse recherchée | Recoupement | Explication |
|---|---|---|---|---|---|
| Le service se dégrade-t-il ? | métrique | Dashboard ou Service Summary | rupture de latence, d'erreur ou de volume | RUM ou traces | une tendance qualifie l'existence et la période du problème |
| Depuis quand ? | métrique et événement | Dashboard, Service Summary, Deployments | début de la rupture et changements proches | versions et logs | la chronologie construit une hypothèse sans prouver la cause |
| Quelle opération est lente ? | métrique APM | Endpoints ou Resources | ressource présentant un p95 différencié | traces | l'opération réduit le périmètre avant l'analyse individuelle |
| Où le temps est-il consommé ? | trace | Trace Explorer puis trace | span dominant et chemin de la requête | métriques de dépendance | la trace décompose un parcours individuel |
| Quelle dépendance participe ? | trace et métrique | Dependencies, trace, Infrastructure | appel aval lent ou saturé | logs | le recoupement évite de confondre attente et cause |
| Quelle erreur précise s'est produite ? | log ou erreur APM | Logs ou Errors | message, type, pile et contexte | span en erreur | le détail décrit l'événement ; la population mesure sa fréquence |
| Une version est-elle concernée ? | version et événement | Deployments | concentration de la rupture sur une version | traces filtrées | la comparaison teste l'hypothèse du changement |
| Une ressource est-elle saturée ? | métrique infrastructure | Infrastructure | CPU, mémoire, pool ou autre ressource | traces et logs | la saturation doit être corrélée à la chronologie applicative |
| Qui subit le problème ? | RUM | RUM Explorer | sessions, vues, actions ou erreurs affectées | APM et logs | l'expérience réelle qualifie le périmètre utilisateur |
| Faut-il notifier une équipe ? | condition de monitor | Monitoring | état, seuil, groupe et destinataire | dashboard et runbook | l'alerte doit conduire à une action explicite |
| L'objectif de fiabilité est-il tenu ? | indicateur de niveau de service | SLO | objectif, période et budget d'erreur | RUM ou métriques métier | le SLO mesure la fiabilité délivrée dans la durée |

## Étape 19 — Distinguer fait, hypothèse et conclusion prématurée

| Affirmation | Réponse | Explication |
|---|---|---|
| La latence p95 affichée vaut la valeur relevée pour la période. | Fait, si réellement observé | la phrase décrit une valeur, son périmètre et sa période |
| Une dépendance lente contribue peut-être à la latence. | Hypothèse | « peut-être » indique une explication à tester par trace et métriques |
| Le dernier déploiement est forcément la cause. | Conclusion prématurée | la proximité temporelle ne suffit pas à démontrer la causalité |
| Le taux d'erreur affiché est nul pour la période. | Fait, si réellement observé | l'affirmation reste limitée à la requête et à la période |
| Le service est sain parce que le taux d'erreur est nul. | Conclusion prématurée | la latence, les erreurs métier et l'expérience utilisateur restent inconnues |
| Une trace lente peut localiser le temps consommé. | Proposition de test | la trace est le signal adapté, mais son résultat n'est pas encore observé |
| `No Data` signifie que le service est arrêté. | Conclusion prématurée | la collecte, la requête et la fenêtre doivent d'abord être vérifiées |
| Aucun SLO n'est visible pour `eu-interfaces`. | Fait d'interface | cela ne prouve ni l'absence de suivi de fiabilité ni l'absence de contrat externe |

## Étape 20 — Suivre le parcours d'investigation de référence

1. **Dashboard ou Service Summary** — vérifier si une rupture existe.
2. **Période, environnement et périmètre** — dater et segmenter la rupture.
3. **Endpoints ou Resources** — identifier l'opération affectée.
4. **Trace Explorer** — sélectionner une requête représentative.
5. **Trace** — repérer le span ou la dépendance dominante.
6. **Logs, Infrastructure et Deployments** — recouper l'hypothèse.
7. **RUM** — qualifier l'impact utilisateur lorsque les données sont disponibles.
8. **Monitoring et SLO** — vérifier la détection, l'action attendue et l'objectif de fiabilité.

### Question — Pourquoi cet ordre commence-t-il par une vue agrégée ?

**Réponse :** parce qu'il faut d'abord confirmer l'existence, la période et le périmètre du problème avant de choisir un événement individuel.

**Pourquoi :** ouvrir immédiatement une trace ou un log spectaculaire peut conduire à analyser un cas non représentatif.

**Limite :** cet ordre est une référence, pas une règle rigide. Une alerte, un identifiant de trace fourni par le support ou une session RUM précise peut justifier une autre porte d'entrée.

## Étape 21 — Vérifier l'autonomie de votre livrable

Votre document est complet lorsque vous avez :

- relevé la période et l'environnement ;
- décrit `eu-interfaces` sans extrapolation ;
- cartographié au moins deux dépendances ;
- identifié le rôle des principales vues APM ;
- parcouru Dashboards, Developer Portal, Metrics, Logs, Monitoring et RUM ;
- expliqué Service Health Monitor, `No Data` et SLO ;
- distingué exploitation et configuration ;
- séparé faits, hypothèses et conclusions prématurées ;
- conservé une posture strictement en lecture seule.

# Synthèse — Questions et réponses

## Quelle vue répond le mieux à « quand la dégradation commence-t-elle ? »

**Réponse :** une série temporelle dans un dashboard ou Service Summary.

**Explication :** la courbe permet de dater une rupture. Deployments et les événements apportent ensuite un contexte de changement.

## Quel signal localise le temps consommé par une requête individuelle ?

**Réponse :** une trace APM et ses spans.

**Explication :** le waterfall décompose le parcours et la durée de chaque opération.

## Quel signal décrit précisément une erreur ?

**Réponse :** un log structuré ou une occurrence Error Tracking, recoupé avec un span en erreur.

**Explication :** le log fournit le message et le contexte ; la trace situe l'erreur dans le parcours.

## Pourquoi un déploiement proche d'une dégradation ne prouve-t-il pas la causalité ?

**Réponse :** parce que deux événements proches peuvent être seulement corrélés.

**Explication :** il faut comparer les versions, le périmètre affecté et les autres changements, puis vérifier si le retour arrière ou une correction modifie le signal.

## Pourquoi faut-il recouper au moins deux signaux ?

**Réponse :** pour réduire le risque de confondre symptôme, corrélation et cause.

**Explication :** une trace lente, un log d'erreur et une métrique de saturation décrivent des dimensions différentes du même comportement.

## Quelle différence existe entre exploitation et configuration ?

**Réponse :** l'exploitation observe et interprète les données existantes ; la configuration change la collecte, le calcul, la conservation, la notification, les droits ou une ressource partagée.

**Explication :** filtrer des traces relève de l'exploitation. Modifier un pipeline, un seuil de monitor ou une intégration relève de la configuration.

## Pourquoi un produit visible dans le menu peut-il ne pas être utilisé ?

**Réponse :** parce que le menu peut être affiché alors que l'instrumentation, les données, la licence, les droits ou le processus d'équipe ne sont pas en place.

**Explication :** l'usage doit être démontré par des objets, des données récentes et un workflow opérationnel, pas par le seul libellé du menu.

# Aide au diagnostic

| Difficulté | Cause probable | Action en lecture seule |
|---|---|---|
| `eu-interfaces` absent | période ou instrumentation modifiée | élargir la période, puis choisir un service actif et noter la substitution |
| Vue vide | période, droits, filtre ou absence de données | vérifier période, requête, env et droits ; noter `non observé` |
| Dépendances illisibles | carte dense ou données insuffisantes | utiliser les entités de la liste sans inventer le sens des liens |
| Version absente | instrumentation ou période | noter `non observé` et poursuivre |
| Aucun Service Health Monitor | autre stratégie d'alerte ou absence de configuration | expliquer l'apport attendu et constater l'absence sans créer |
| Aucun SLO | indicateur ou cible non formalisés | expliquer la différence monitor/SLO et noter l'absence |
| Aucun log `eu-interfaces` | collecte non activée ou nom différent | retirer le filtre et identifier les services réellement disponibles |
| Aucun résultat RUM | période, application ou instrumentation | passer à 24 heures et noter la limite |
| Commande d'édition ouverte | confusion de navigation | fermer sans enregistrer |
| Interface différente | évolution ou droits Datadog | chercher la fonction par son objectif et la recherche globale |

# Point de contrôle final

Vous maîtrisez les fondamentaux de navigation si vous pouvez expliquer la chaîne suivante :

```text
question
  -> porte d'entrée
  -> période et périmètre
  -> vue agrégée
  -> événement représentatif
  -> signal de recoupement
  -> fait
  -> hypothèse testée
  -> décision
```

La maîtrise de Datadog ne consiste pas à ouvrir tous les menus. Elle consiste à choisir la vue qui répond à la question présente, à conserver le contexte pendant les pivots et à savoir ce que chaque observation permet — ou ne permet pas — de conclure.
