---
title: "Module 6 — Atelier : APM et traçage distribué"
subtitle: "Document participant autonome"
lang: fr-FR
---

# Analyser une trace et formuler une hypothèse

## Objectif

Parcourir les principales vues APM sur un service réel, vérifier les pivots réellement proposés et lire le chemin critique d'une trace.

## Livrable

Vous produisez :

- une vue du contexte APM réel ;
- une comparaison de trois observations comparables ;
- une représentation du chemin critique d'une trace ;
- un audit de l'instrumentation PHP et de la corrélation logs-traces ;
- un inventaire des attributs réellement présents ;
- un constat sur les pivots réellement proposés par Datadog ;
- une analyse datée des traces réellement disponibles ;
- une conclusion structurée et un test suivant.

## Place dans la progression

Cet atelier vient après l'exploration des logs du module 5. Il observe directement les dimensions `env`, `service` et `version`, ainsi que les pivots proposés par Datadog. Les saisies nécessaires dans les modules suivants seront indiquées au moment de leur utilisation :

- le module 7, en vérifiant sans l'imposer l'existence d'un pivot RUM–APM ;
- le module 9, qui donnera directement les paramètres du widget APM ;
- le module 10, en conservant une conclusion sous la forme fait, hypothèse, limite et test suivant.

## Règle de sécurité

Utilisez les filtres et requêtes temporaires nécessaires à l'exploration. Ne observez aucun `trace_id` réel, paramètre sensible, URL complète, requête SQL, nom d'hôte ou donnée personnelle.

## Prérequis

- Chrome connecté à Datadog ;
- accès à **APM**, aux services et au Trace Explorer ;
- convention `env/service/version` du module 4 ;
- vue de logs `[TRAINING] M05 Logs API - <participant> - 20260720` créée au module 5, si ce parcours a été réalisé ;
- accès au service réel `eu-interfaces`.

## Comment utiliser ce document

Effectuez chaque manipulation avant de lire la réponse de référence qui suit la question. Les métriques et traces changent selon la période, les droits, l'ingestion et la rétention : utilisez les valeurs visibles. Toutes les étapes utilisent `eu-interfaces` et l'opération réelle `console`.

Une recherche de traces vide ne signifie pas nécessairement qu'il n'y a eu aucun trafic. Les statistiques APM agrégées et les spans conservés pour la recherche ne représentent pas exactement le même ensemble. De plus, logs et traces peuvent être échantillonnés et conservés indépendamment.

## Contexte réel vérifié sur la plateforme

Le parcours a été vérifié le 20 juillet 2026 dans l'organisation Datadog de formation :

| Élément | Implémentation observée |
|---|---|
| Entrée APM | **APM** ouvre directement **APM Home** |
| Navigation supérieure | **Service**, **Traces**, **Profiles**, puis **Settings** |
| Service d'exercice | `eu-interfaces` |
| Opération principale | `console` |
| Page du service | **Service Summary**, **Resources**, **Deployments**, **Dependencies**, **Traces**, **Errors**, **Live Debugger**, **Infrastructure**, **Logs**, **Security** et **Costs** |
| Santé du service | aucun Service Health Monitor ni SLO associé au moment de la vérification |
| Contexte de déploiement | sélecteurs `operation`, `env` et `version` présents ; version visible ou absente |
| Trace Explorer | ouverture en mode **Spans** ; bascule manuelle vers **Traces** nécessaire pour afficher une trace complète et son waterfall |

`eu-interfaces` peut produire peu de traces sur une période courte. Le Trace Explorer s'ouvre actuellement sur **Past 15 Minutes**. Si vous ne trouvez pas trois observations comparables, élargissez progressivement à **Past 1 Hour**, **Past 4 Hours**, puis **Past 1 Day**. Ce changement de période est temporaire et ne modifie aucune ressource.

# Partie 1 — Comprendre l'organisation de l'APM

## Étape 1 — Ouvrir APM Home

1. Dans le menu gauche, ouvrez **APM**, puis **Home** si cette page est proposée.
2. Dans l'en-tête APM, repérez **Service**, **Traces**, **Profiles** et **Settings**.
3. Dans **Your system at a glance**, repérez la liste **Services** et l'onglet **Endpoints**.
4. Repérez la période et le filtre `env` visibles.
5. Observez la date, l'heure et la période.

### Quel est le rôle d'APM Home ?

**Réponse :** APM Home fournit un point d'entrée vers les services instrumentés, leur topologie, leurs ressources et leurs traces.

**Pourquoi :** l'APM organise la télémétrie autour du parcours des requêtes, et non autour d'une simple liste de métriques.

**Limite :** les menus visibles dépendent des fonctionnalités activées et des droits.

## Étape 2 — Parcourir la liste des services

1. Restez dans **APM Home**, section **Your system at a glance > Services**.
2. Repérez la recherche **Search services** et le filtre `env`.
3. Recherchez `eu-interfaces`.
4. Observez, sans les interpréter isolément, le trafic, le taux d'erreur et la latence affichés.
5. Si le résultat est absent sur **Past 1 Hour**, élargissez la période avant de demander au formateur un service de substitution.

### Que décrivent trafic, erreurs et latence ?

**Réponse :** ils décrivent la population agrégée des requêtes observées dans le périmètre et la période.

**Pourquoi :** ils permettent de confirmer un symptôme avant d'ouvrir un cas individuel.

**Limite :** ces mesures localisent un problème possible ; elles ne prouvent pas sa cause.

## Étape 3 — Ouvrir la cartographie des dépendances

1. Ouvrez `eu-interfaces` depuis la liste des services.
2. Dans sa page, ouvrez la section **Dependencies**.
3. Repérez la **Dependency Map** et le bouton **Open Fullscreen View**, sans ouvrir les paramètres.
4. Repérez les nœuds et les relations avec des composants techniques.
5. Observez deux dépendances ou services voisins sans conclure à leur responsabilité.

### La Dependency Map est-elle une CMDB exhaustive ?

**Réponse :** non. Elle représente les services instrumentés et les dépendances observées dans les données disponibles.

**Pourquoi :** une relation absente peut résulter de la période, de l'échantillonnage, de l'instrumentation ou des droits.

**Limite :** un lien visible prouve un appel observé, pas que la dépendance est défaillante.

## Étape 4 — Ouvrir la page du service réel retenu

1. Si nécessaire, revenez à la page de `eu-interfaces` depuis **APM Home > Services**.
2. Repérez **Service Summary**, **Resources**, **Deployments**, **Dependencies**, **Traces**, **Errors**, **Live Debugger**, **Infrastructure**, **Logs**, **Security** et **Costs**, selon vos droits.
3. Observez les filtres **operation**, **env**, **version** et la période.
4. Observez la version visible ou **non observée**.
5. Repérez **Service Config**.

### Pourquoi `env` et `version` doivent-ils accompagner `service` ?

**Réponse :** ils définissent le contexte du déploiement que l'on compare.

**Pourquoi :** une évolution de latence proche d'une version constitue une hypothèse de déploiement à tester.

**Limite :** la proximité d'une version et d'une dégradation ne démontre pas la causalité.

# Partie 2 — Passer du service à une ressource

## Étape 5 — Examiner Service Summary

1. Dans **Service Summary**, observez les courbes de requêtes, erreurs et latence.
2. Repérez les unités et la période.
3. Vérifiez si une erreur est visible sur la période.
4. Repérez les cartes **SERVICE HEALTH MONITORS** et **SLOs**.
5. Observez **aucun configuré** si les cartes indiquent respectivement **NONE**.

### L'absence de Service Health Monitor ou de SLO signifie-t-elle que le service n'est pas observé ?

**Réponse :** non. Les traces et statistiques APM restent disponibles. Un Service Health Monitor formalise une condition d'alerte sur la santé du service ; un SLO formalise un objectif mesurable de fiabilité. Leur absence signifie qu'aucun de ces objets n'est associé au service dans la vue actuelle, pas que la télémétrie est absente.

**Contexte vérifié :** `eu-interfaces` ne présentait ni Service Health Monitor ni SLO lors de la vérification. Leur création n'est pas autorisée dans cet atelier.

### Pourquoi ne faut-il pas ouvrir immédiatement la trace la plus lente ?

**Réponse :** il faut d'abord connaître le comportement agrégé et choisir une ressource comparable.

**Pourquoi :** une trace extrême peut être isolée et non représentative de l'impact global.

## Étape 6 — Parcourir Resources

1. Ouvrez **Resources**.
2. Repérez les colonnes ou mesures de requêtes, erreurs et latence.
3. Identifiez une ressource possédant plusieurs observations.
4. Observez son nom de manière anonymisée si sa valeur est sensible.

### Quelle différence existe entre un service et une ressource ?

**Réponse :** le service est un composant stable instrumenté ; la ressource est une action stable exécutée par ce service, par exemple une route HTTP.

**Application réelle :** `eu-interfaces` est le service et `console` l'opération retenue.

## Étape 7 — Ouvrir une ressource

1. Ouvrez la ressource sélectionnée si le lien est disponible.
2. Repérez ses signaux agrégés et les accès aux traces associées.
3. Comparez son périmètre avec celui du service.
4. Revenez à la page précédente.

### Pourquoi analyser une ressource avant une trace ?

**Réponse :** elle rassemble des opérations comparables et réduit le risque de comparer des actions différentes.

**Pourquoi :** plusieurs traces de la même ressource permettent d'étudier la variabilité, les versions et les populations.

## Étape 8 — Examiner Dependencies

1. Ouvrez **Dependencies** ou la section équivalente.
2. Observez deux composants appelés, s'ils sont visibles.
3. Observez les mesures proposées sans conclure à une saturation.

### Une dépendance lente dans cette vue est-elle automatiquement la cause racine ?

**Réponse :** non. Elle constitue une zone suspecte à confronter aux traces, métriques, logs et autres observations.

# Partie 3 — Utiliser le Trace Explorer

## Étape 9 — Reprendre le contrat de contexte des logs

1. Ouvrez **Logs > Explorer** dans un nouvel onglet.
2. Si elle existe et vous est accessible, sélectionnez votre vue `[TRAINING] M05 Logs API - <participant> - 20260720` créée au module 5.
3. Cette vue cible les logs du module 5 et sert uniquement à retrouver la navigation. Elle ne prouve pas une corrélation avec `eu-interfaces`.
4. Remplacez temporairement la requête par `service:eu-interfaces`.
5. Observez uniquement la présence ou l'absence des attributs `service`, `env`, `version`, `dd.trace_id` et `dd.span_id`.
6. Revenez à l'onglet APM.

### Pourquoi reprendre ces attributs avant d'explorer les traces ?

**Réponse :** ils forment le contrat de contexte commun entre logs et APM. `service`, `env` et `version` cadrent la recherche ; les identifiants de trace et de span permettent une corrélation précise lorsqu'ils sont injectés, parsés et conservés.

**Limite :** l'absence d'un pivot visible ne prouve pas à elle seule une mauvaise configuration. La période, les droits, le parsing et l'échantillonnage indépendant des logs et des traces doivent aussi être vérifiés.

## Étape 10 — Ouvrir le Trace Explorer

1. Dans l'en-tête APM, ouvrez **Traces**.
2. Dans **Search for**, conservez le mode **Spans** pour commencer.
3. Repérez la barre de requête, la période, la liste des spans et le panneau de facettes.
4. Vérifiez qu'aucune vue sauvegardée ou configuration n'est en cours.
5. La navigation depuis `eu-interfaces` préremplit normalement le service et l'opération. Elle peut également ajouter des exclusions techniques sur `@span.kind`. Si nécessaire, saisissez temporairement la requête minimale suivante, puis ne la sauvegardez pas :

```text
service:eu-interfaces operation_name:console
```

6. Si moins de trois spans sont disponibles, élargissez la période de **Past 15 Minutes** à **Past 1 Hour**, puis **Past 4 Hours** et **Past 1 Day**.

### Que contient principalement le Trace Explorer ?

**Réponse :** des spans recherchables, c'est-à-dire des opérations temporisées appartenant à des traces.

**Pourquoi :** une trace complète relie plusieurs spans par un identifiant commun et des relations parent-enfant.

## Étape 11 — Parcourir les facettes APM

1. Repérez **Service**, **Resource**, **Operation Name**, **Duration**, **Status**, **Env**, **Version** et **Peer Service**, lorsqu'elles existent.
2. Dépliez plusieurs facettes sans recopier de valeur sensible.
3. Observez leur usage.

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

## Étape 12 — Ajouter une ressource à la recherche

1. Sélectionnez une ressource suffisamment représentée depuis la facette.
2. Vérifiez que la requête combine le service et la ressource.
3. Observez sa forme générique sans recopier de valeur sensible.

```text
service:<service> resource_name:<ressource_proposée>
```

### Que faut-il maintenir identique pour comparer plusieurs observations ?

**Réponse :** au minimum le service, la ressource, l'environnement et une période pertinente ; la version est conservée ou volontairement comparée.

**Pourquoi :** des ressources différentes peuvent illustrer une diversité, mais pas prouver qu'une implémentation est plus lente qu'une autre.

## Étape 13 — Comparer trois spans

1. Choisissez trois spans du même service et de la même ressource.
2. Comparez leur durée, statut, environnement et version.
3. Identifiez les attributs absents.

### Trois spans suffisent-ils à mesurer l'ampleur d'un incident ?

**Réponse :** non. Ils permettent d'apprendre la comparaison et de formuler une hypothèse, mais l'ampleur doit être évaluée avec les mesures agrégées et un échantillon approprié.

## Étape 14 — Examiner la distribution sans conclure sur un seul cas

1. Repérez, si disponible, la distribution ou l'histogramme de durée.
2. Identifiez la zone habituelle et les valeurs plus lentes.
3. Choisissez une observation représentative de la question étudiée, pas nécessairement l'extrême absolu.

### Pourquoi une trace représentative peut-elle être préférable à la plus lente ?

**Réponse :** l'extrême peut correspondre à un cas rare, une instrumentation particulière ou une population différente.

**Pourquoi :** une trace représentative d'un groupe dégradé aide à expliquer un motif reproductible.

# Partie 4 — Lire une trace

## Étape 15 — Ouvrir une trace

1. En haut du Trace Explorer, dans **Search for**, sélectionnez **Traces** à la place de **Spans**.
2. Vérifiez que l'intitulé de la source devient **All Ingested Traces (Live Search)** et que la liste affiche notamment **Root service**, **Span Duration**, **Spans** et **Resource**.
3. Conservez la requête sur `service:eu-interfaces` et `operation_name:console`.
4. Cliquez sur une ligne de trace. Le panneau de détail s'ouvre à droite.
5. Repérez le span racine, la durée totale, le statut et les services traversés.
6. N'inscrivez aucun identifiant réel dans le livrable.
7. Le panneau s'ouvre actuellement sur **Flame Graph**. Sélectionnez l'onglet **Waterfall** ; les onglets **Span List** et **Map** peuvent également être proposés.
8. Repérez les onglets de détail du span, notamment **Overview**, **Infrastructure**, **Metrics** et **Logs**, lorsqu'ils sont disponibles.

**Résultat attendu :** une trace complète contient plusieurs spans ; lors de la vérification, les traces visibles de `eu-interfaces` contenaient respectivement plusieurs opérations, alors que le mode **Spans** présentait une ligne par opération recherchable.

### Quelle différence existe entre une trace et un span ?

**Réponse :** une trace représente le parcours complet d'une requête distribuée ; un span représente une opération délimitée dans ce parcours.

## Étape 16 — Lire le waterfall

1. Suivez les relations parent-enfant depuis le span racine.
2. Repérez les opérations imbriquées et les branches parallèles.
3. Identifiez les derniers achèvements qui déterminent la durée de bout en bout.
4. Observez les spans feuilles ou dépendances consommant effectivement du temps.

### Pourquoi la somme des durées des spans peut-elle dépasser la durée de la trace ?

**Réponse :** parce que les spans peuvent être imbriqués ou parallèles et que le temps d'un parent inclut souvent celui de ses enfants.

**Pourquoi :** additionner toutes les durées compte plusieurs fois certains intervalles.

## Étape 17 — Reconstituer le chemin critique

```text
[span racine] -> [span intermédiaire] -> [span feuille ou dépendance dominante]
```

### Le span le plus long prouve-t-il la cause racine ?

**Réponse :** non. Un parent long peut principalement attendre ses enfants, et une dépendance longue peut refléter une saturation située plus loin.

**Pourquoi :** le chemin critique localise le temps consommé ; la cause exige une preuve complémentaire.

## Étape 18 — Examiner une erreur

1. Si un span rouge est visible, repérez où l'erreur est enregistrée et si elle apparaît aussi sur ses parents.
2. Si aucune erreur n'est visible, observez **aucune erreur observée sur cette trace**.
3. Observez le type d'erreur uniquement s'il n'est pas sensible.

### Le premier span rouge est-il nécessairement l'origine de l'erreur ?

**Réponse :** non. Une erreur peut être capturée sur un enfant puis propagée vers ses parents, ou enregistrée après l'échec initial.

**Pourquoi :** il faut examiner la chronologie, les attributs et les événements associés.

## Étape 19 — Repérer les pivots de corrélation

1. Dans le détail, repérez les accès vers les logs, les métriques de l'hôte, le profilage ou la dépendance, lorsqu'ils existent.
2. Observez le pivot qui serait le plus utile pour tester votre hypothèse.

### Pourquoi faut-il demander un second signal ?

**Réponse :** parce qu'une trace illustre un parcours individuel ; des logs corrélés ou une métrique de dépendance permettent de tester la reproductibilité et le mécanisme supposé.

## Étape 20 — Vérifier le pivot réel entre trace et logs

1. Dans le détail de la trace, recherchez **Logs**, **View related logs**, **Correlated logs** ou un accès équivalent.
2. Si le pivot est disponible, ouvrez-le.
3. Vérifiez que le filtre conserve le service et la période de la trace.
4. Observez si des événements sont retournés, sans copier leur contenu ni les identifiants techniques.
5. Revenez à la trace.

| Contrôle | Résultat attendu |
|---|---|
| Accès aux logs proposé | oui / non observé |
| Événements retournés | oui / aucun sur la période |
| Contexte cohérent | oui / partiel / non vérifiable |
| Conclusion | corrélation observée / corrélation non observée |

### Que conclure si le pivot ne retourne aucun log ?

**Réponse :** vérifiez **corrélation non observée dans ce contexte**. Ne concluez pas immédiatement à une panne de configuration.

**Explication :** les logs doivent contenir des identifiants correctement parsés et remappés, mais les logs et les traces sont aussi échantillonnés indépendamment. Une trace peut avoir été conservée sans son log, ou inversement.

## Étape 21 — Auditer l'instrumentation PHP existante

1. Dans une trace de `eu-interfaces`, repérez le span `console`, les spans enfants et les attributs de commande non sensibles.
2. Dans **APM Home > Services**, constatez si des services techniques PHP tels que `mysqli`, `pdo`, `phpredis` ou `web.request` sont visibles.
3. Ne déduisez pas qu'ils appartiennent tous à la même trace : seule la hiérarchie du waterfall peut prouver leur participation à l'observation ouverte.
4. Dans les détails du service ou de la trace, cherchez le langage ou le runtime uniquement si cette information est affichée.
6. Vérifiez les éléments avec **observé**, **non observé** ou **non vérifiable avec mes droits**.

### Que fournit l'instrumentation automatique PHP ?

**Réponse :** lorsque le traceur PHP est installé et que les bibliothèques sont compatibles, il crée automatiquement des spans techniques et collecte notamment des durées, des informations de requête web, des accès SQL et des exceptions non gérées.

**Limite :** l'instrumentation automatique ne connaît pas spontanément les concepts métier. Observez les attributs réellement présents.

## Étape 22 — Inventorier les attributs réellement présents

1. Dans les traces ouvertes, observez uniquement les noms d'attributs non sensibles réellement visibles.
2. Pour chacun, indiquez son type, quelques valeurs agrégées proposées par Datadog et son usage possible comme filtre.
3. Marquez **absent** pour `env`, `version`, `team` ou tout attribut attendu mais non disponible.

**Réponse expliquée :** cet inventaire décrit l'instrumentation actuelle. Une absence devient une limite documentée, pas une donnée de remplacement.

## Étape 23 — Vérifier les pivots proposés par Datadog

1. Depuis la trace réelle ouverte, repérez les liens proposés vers Logs ou RUM.
2. Testez un pivot et revenez à la trace.
3. Si aucun pivot n'est proposé, vérifiez **pivot non disponible sur cette trace**.
4. Ne recherchez pas un service de remplacement et ne spécifiez pas de widget : les valeurs à saisir seront données directement dans le module concerné.

**Réponse expliquée :** un pivot visible dépend de l'instrumentation, de la propagation du contexte, de l'ingestion et de la rétention. Son absence ne doit pas être compensée par une préparation théorique.

# Partie 5 — Consolider les observations réelles

## Étape 24 — Comparer plusieurs traces disponibles

1. Conservez le filtre `service:eu-interfaces operation_name:console` et le mode **Traces**.
2. Étendez la période de 15 minutes à 1 heure, 4 heures puis 1 jour si nécessaire.
3. Comparez jusqu'à trois traces réellement disponibles.
4. Observez durée, statut, ressource et dépendances visibles.

**Réponse expliquée :** trois traces ne suffisent pas à établir une tendance ; elles permettent seulement d'apprendre la lecture du waterfall et de formuler une prochaine vérification.
