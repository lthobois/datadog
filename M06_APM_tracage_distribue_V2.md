---
title: "Module 6 — Atelier V2 : APM et traçage distribué"
subtitle: "Document participant autonome"
lang: fr-FR
---

# Analyser une trace et formuler une hypothèse

## Objectif

Parcourir les principales vues APM sur un service réel, vérifier la continuité avec les logs étudiés au module 5, lire le chemin critique d'une trace, puis préparer les pivots nécessaires aux modules RUM, dashboards et investigation.

## Livrable

Vous produisez :

- un relevé daté du contexte APM réel ;
- une comparaison de trois observations comparables ;
- une représentation du chemin critique d'une trace ;
- un audit de l'instrumentation PHP et de la corrélation logs-traces ;
- un contrat d'enrichissement métier proposé, sans l'appliquer ;
- trois spécifications de widgets APM pour le dashboard du module 8 ;
- un pivot backend à rechercher depuis le RUM au module 7 ;
- une analyse des preuves pédagogiques PeopleShop ;
- une conclusion structurée et un test suivant.

## Place dans la progression

Cet atelier vient après l'exploration des logs du module 5. Il réutilise les dimensions `env`, `service` et `version`, ainsi que la vue enregistrée créée dans ce module si elle est accessible. Il prépare :

- le module 7, en identifiant le service backend que le RUM pourrait relier à une requête frontend ;
- le module 8, en spécifiant les widgets APM à construire une fois les signaux explorés ;
- le module 9, en conservant une conclusion sous la forme fait, hypothèse, limite et test suivant.

## Règle de sécurité

Travaillez uniquement en lecture. Ne créez ni règle APM, service, monitor, dashboard, vue sauvegardée, instrumentation, attribut, règle d'échantillonnage ou configuration. N'utilisez pas **Save**, **Create**, **Edit**, **Configure**, **Add filter** ou une commande équivalente. Ne recopiez aucun `trace_id` réel, paramètre sensible, URL complète, requête SQL, nom d'hôte ou donnée personnelle.

## Prérequis

- Chrome connecté à Datadog ;
- accès en lecture à **APM**, aux services et au Trace Explorer ;
- convention `env/service/version` du module 4 ;
- vue de logs `[TRAINING] M05 Logs - <participant> - <date>` créée au module 5 V2, si ce parcours a été réalisé ;
- captures ou fiches PeopleShop fournies avec l'atelier.

## Comment utiliser ce document

Effectuez chaque manipulation avant de lire la réponse de référence qui suit la question. Les métriques et traces changent selon la période, les droits, l'ingestion et la rétention : votre relevé daté prévaut sur les exemples historiques. Les étapes réelles utilisent `eu-interfaces`, service actif vérifié sur la plateforme, pour apprendre la navigation. Les étapes PeopleShop utilisent `orders-api` pour raisonner sur le fil rouge. Ne mélangez jamais leurs preuves.

Une recherche de traces vide ne signifie pas nécessairement qu'il n'y a eu aucun trafic. Les statistiques APM agrégées et les spans conservés pour la recherche ne représentent pas exactement le même ensemble. De plus, logs et traces peuvent être échantillonnés et conservés indépendamment.

## Contexte réel vérifié sur la plateforme

Le parcours a été vérifié le 20 juillet 2026 dans l'organisation Datadog de formation :

| Élément | Implémentation observée |
|---|---|
| Entrée APM | **APM** ouvre directement **APM Home** |
| Navigation supérieure | **Service**, **Traces**, **Profiles**, puis **Settings** |
| Service d'exercice | `eu-interfaces` |
| Opération principale | `console` |
| Page du service | **Service Summary**, **Resources**, **Deployments**, **Dependencies**, **Traces**, **Errors**, **Live Debugger**, **Infrastructure** |
| Santé du service | aucun Service Health Monitor ni SLO associé au moment de la vérification |
| Contexte de déploiement | sélecteurs `operation`, `env` et `version` présents ; valeur de version à relever ou déclarer non observée |

`eu-interfaces` peut produire peu de traces sur une période courte. Si vous ne trouvez pas trois observations comparables dans **Past 1 Hour**, élargissez progressivement à **Past 4 Hours**, puis **Past 1 Day**. Ce changement de période est temporaire et ne modifie aucune ressource.

# Partie 1 — Comprendre l'organisation de l'APM

## Étape 1 — Ouvrir APM Home

1. Dans le menu gauche, ouvrez **APM**, puis **Home** si cette page est proposée.
2. Dans l'en-tête APM, repérez **Service**, **Traces**, **Profiles** et **Settings**.
3. Dans **Your system at a glance**, repérez la liste **Services** et l'onglet **Endpoints**.
4. Repérez la période et le filtre `env` visibles.
5. Notez la date, l'heure et la période.

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

1. Restez dans **APM Home**, section **Your system at a glance > Services**.
2. Repérez la recherche **Search services** et le filtre `env`.
3. Recherchez `eu-interfaces`.
4. Relevez, sans les interpréter isolément, le trafic, le taux d'erreur et la latence affichés.
5. Si le résultat est absent sur **Past 1 Hour**, élargissez la période avant de demander au formateur un service de substitution.

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

## Étape 3 — Ouvrir la cartographie des dépendances

1. Ouvrez `eu-interfaces` depuis la liste des services.
2. Dans sa page, ouvrez la section **Dependencies**.
3. Repérez la **Dependency Map** et le bouton **Open Fullscreen View**, sans ouvrir les paramètres.
4. Repérez les nœuds et les relations avec des composants techniques.
5. Notez deux dépendances ou services voisins sans conclure à leur responsabilité. Si aucune relation n'est visible sur la période, écrivez **aucune dépendance observée**.

| Relation observée | Interprétation prudente |
|---|---|
|  | appel ou relation observée dans la télémétrie |
|  | appel ou relation observée dans la télémétrie |

### La Dependency Map est-elle une CMDB exhaustive ?

**Réponse :** non. Elle représente les services instrumentés et les dépendances observées dans les données disponibles.

**Pourquoi :** une relation absente peut résulter de la période, de l'échantillonnage, de l'instrumentation ou des droits.

**Limite :** un lien visible prouve un appel observé, pas que la dépendance est défaillante.

## Étape 4 — Ouvrir la page du service réel retenu

1. Si nécessaire, revenez à la page de `eu-interfaces` depuis **APM Home > Services**.
2. Repérez **Service Summary**, **Resources**, **Traces**, **Dependencies**, **Deployments** ou leurs équivalents.
3. Relevez les filtres **operation**, **env**, **version** et la période.
4. Notez la version visible ou **non observée**.
5. Repérez **Service Config**, mais ne l'ouvrez pas et ne cliquez pas sur **Edit**.

### Pourquoi `env` et `version` doivent-ils accompagner `service` ?

**Réponse :** ils définissent le contexte du déploiement que l'on compare.

**Pourquoi :** une évolution de latence proche d'une version constitue une hypothèse de déploiement à tester.

**Limite :** la proximité d'une version et d'une dégradation ne démontre pas la causalité.

# Partie 2 — Passer du service à une ressource

## Étape 5 — Examiner Service Summary

1. Dans **Service Summary**, observez les courbes de requêtes, erreurs et latence.
2. Repérez les unités et la période.
3. Notez si une erreur est visible ou écrivez **aucune erreur observée sur la période**.
4. Repérez les cartes **SERVICE HEALTH MONITORS** et **SLOs**.
5. Notez **aucun configuré** si les cartes indiquent respectivement **NONE**. Ne cliquez ni sur **Configure** ni sur **Create**.

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

## Étape 9 — Reprendre le contrat de contexte des logs

1. Ouvrez **Logs > Explorer** dans un nouvel onglet.
2. Si elle existe et vous est accessible, sélectionnez votre vue `[TRAINING] M05 Logs - <participant> - <date>` créée au module 5 V2.
3. Cette vue cible les logs du module 5 et sert uniquement à retrouver la navigation. Elle ne prouve pas une corrélation avec `eu-interfaces`.
4. Remplacez temporairement la requête par `service:eu-interfaces`, sans enregistrer la modification.
5. Relevez uniquement la présence ou l'absence des attributs `service`, `env`, `version`, `dd.trace_id` et `dd.span_id`.
6. Revenez à l'onglet APM. Ne modifiez pas la vue enregistrée.

| Attribut | Présent dans les logs ? | Utilité pour le pivot APM |
|---|---|---|
| `service` |  | retrouver le même producteur |
| `env` |  | conserver le même environnement |
| `version` |  | comparer les déploiements |
| `dd.trace_id` |  | relier un log à une trace |
| `dd.span_id` |  | relier un log à une opération |

### Pourquoi reprendre ces attributs avant d'explorer les traces ?

**Réponse :** ils forment le contrat de contexte commun entre logs et APM. `service`, `env` et `version` cadrent la recherche ; les identifiants de trace et de span permettent une corrélation précise lorsqu'ils sont injectés, parsés et conservés.

**Limite :** l'absence d'un pivot visible ne prouve pas à elle seule une mauvaise configuration. La période, les droits, le parsing et l'échantillonnage indépendant des logs et des traces doivent aussi être vérifiés.

## Étape 10 — Ouvrir le Trace Explorer

1. Dans l'en-tête APM, ouvrez **Traces**.
2. Repérez la barre de requête, la période, la liste des spans et le panneau de facettes.
3. Vérifiez qu'aucune vue sauvegardée ou configuration n'est en cours.
4. La navigation depuis `eu-interfaces` préremplit normalement le service et l'opération. Si nécessaire, saisissez temporairement la requête suivante, puis ne la sauvegardez pas :

```text
service:eu-interfaces operation_name:console
```

5. Si moins de trois spans sont disponibles, élargissez la période à **Past 4 Hours**, puis **Past 1 Day**.

### Que contient principalement le Trace Explorer ?

**Réponse :** des spans recherchables, c'est-à-dire des opérations temporisées appartenant à des traces.

**Pourquoi :** une trace complète relie plusieurs spans par un identifiant commun et des relations parent-enfant.

## Étape 11 — Parcourir les facettes APM

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

## Étape 12 — Ajouter une ressource à la recherche

1. Sélectionnez une ressource suffisamment représentée depuis la facette.
2. Vérifiez que la requête combine le service et la ressource.
3. Notez sa forme générique sans recopier de valeur sensible.

```text
service:<service> resource_name:<ressource_proposée>
```

### Que faut-il maintenir identique pour comparer plusieurs observations ?

**Réponse :** au minimum le service, la ressource, l'environnement et une période pertinente ; la version est conservée ou volontairement comparée.

**Pourquoi :** des ressources différentes peuvent illustrer une diversité, mais pas prouver qu'une implémentation est plus lente qu'une autre.

## Étape 13 — Comparer trois spans

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

## Étape 14 — Examiner la distribution sans conclure sur un seul cas

1. Repérez, si disponible, la distribution ou l'histogramme de durée.
2. Identifiez la zone habituelle et les valeurs plus lentes.
3. Choisissez une observation représentative de la question étudiée, pas nécessairement l'extrême absolu.

### Pourquoi une trace représentative peut-elle être préférable à la plus lente ?

**Réponse :** l'extrême peut correspondre à un cas rare, une instrumentation particulière ou une population différente.

**Pourquoi :** une trace représentative d'un groupe dégradé aide à expliquer un motif reproductible.

# Partie 4 — Lire une trace

## Étape 15 — Ouvrir une trace

1. Ouvrez le détail d'un span, puis la trace associée si cette navigation est proposée.
2. Repérez le span racine, la durée totale, le statut et les services traversés.
3. N'inscrivez aucun identifiant réel dans le livrable.
4. Repérez la chronologie ou le waterfall.

### Quelle différence existe entre une trace et un span ?

**Réponse :** une trace représente le parcours complet d'une requête distribuée ; un span représente une opération délimitée dans ce parcours.

## Étape 16 — Lire le waterfall

1. Suivez les relations parent-enfant depuis le span racine.
2. Repérez les opérations imbriquées et les branches parallèles.
3. Identifiez les derniers achèvements qui déterminent la durée de bout en bout.
4. Notez les spans feuilles ou dépendances consommant effectivement du temps.

### Pourquoi la somme des durées des spans peut-elle dépasser la durée de la trace ?

**Réponse :** parce que les spans peuvent être imbriqués ou parallèles et que le temps d'un parent inclut souvent celui de ses enfants.

**Pourquoi :** additionner toutes les durées compte plusieurs fois certains intervalles.

## Étape 17 — Reconstituer le chemin critique

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

## Étape 18 — Examiner une erreur

1. Si un span rouge est visible, repérez où l'erreur est enregistrée et si elle apparaît aussi sur ses parents.
2. Si aucune erreur n'est visible, notez **aucune erreur observée sur cette trace**.
3. Relevez le type d'erreur uniquement s'il n'est pas sensible.

### Le premier span rouge est-il nécessairement l'origine de l'erreur ?

**Réponse :** non. Une erreur peut être capturée sur un enfant puis propagée vers ses parents, ou enregistrée après l'échec initial.

**Pourquoi :** il faut examiner la chronologie, les attributs et les événements associés.

## Étape 19 — Repérer les pivots de corrélation

1. Dans le détail, repérez les accès vers les logs, les métriques de l'hôte, le profilage ou la dépendance, lorsqu'ils existent.
2. Notez le pivot qui serait le plus utile pour tester votre hypothèse.

### Pourquoi faut-il demander un second signal ?

**Réponse :** parce qu'une trace illustre un parcours individuel ; des logs corrélés ou une métrique de dépendance permettent de tester la reproductibilité et le mécanisme supposé.

## Étape 20 — Vérifier le pivot réel entre trace et logs

1. Dans le détail de la trace, recherchez **Logs**, **View related logs**, **Correlated logs** ou un accès équivalent.
2. Si le pivot est disponible, ouvrez-le en lecture seule.
3. Vérifiez que le filtre conserve le service et la période de la trace.
4. Observez si des événements sont retournés, sans copier leur contenu ni les identifiants techniques.
5. Revenez à la trace sans enregistrer de vue.

| Contrôle | Résultat à noter |
|---|---|
| Accès aux logs proposé | oui / non observé |
| Événements retournés | oui / aucun sur la période |
| Contexte cohérent | oui / partiel / non vérifiable |
| Conclusion | corrélation observée / corrélation non observée |

### Que conclure si le pivot ne retourne aucun log ?

**Réponse :** écrivez **corrélation non observée dans ce contexte**. Ne concluez pas immédiatement à une panne de configuration.

**Explication :** les logs doivent contenir des identifiants correctement parsés et remappés, mais les logs et les traces sont aussi échantillonnés indépendamment. Une trace peut avoir été conservée sans son log, ou inversement.

## Étape 21 — Auditer l'instrumentation PHP existante

1. Dans une trace de `eu-interfaces`, repérez le span `console`, les spans enfants et les attributs de commande non sensibles.
2. Dans **APM Home > Services**, constatez si des services techniques PHP tels que `mysqli`, `pdo`, `phpredis` ou `web.request` sont visibles.
3. Ne déduisez pas qu'ils appartiennent tous à la même trace : seule la hiérarchie du waterfall peut prouver leur participation à l'observation ouverte.
4. Dans les détails du service ou de la trace, cherchez le langage ou le runtime uniquement si cette information est affichée.
5. Ne modifiez pas **Service Config**, le SDK, l'Agent ou les variables d'environnement.
6. Complétez le tableau avec **observé**, **non observé** ou **non vérifiable avec mes droits**.

| Capacité attendue de l'instrumentation automatique PHP | Constat réel |
|---|---|
| requête web et code de statut |  |
| durée des opérations instrumentées |  |
| appels SQL ou accès base de données |  |
| appels à des bibliothèques compatibles |  |
| exception non gérée, si un cas existe |  |
| `env`, `service`, `version` |  |

### Que fournit l'instrumentation automatique PHP ?

**Réponse :** lorsque le traceur PHP est installé et que les bibliothèques sont compatibles, il crée automatiquement des spans techniques et collecte notamment des durées, des informations de requête web, des accès SQL et des exceptions non gérées.

**Limite :** elle ne connaît pas spontanément les concepts métier de PeopleShop. Une étape de commande, une catégorie de client ou une fonctionnalité activée doivent être décrites par des attributs maîtrisés ou une instrumentation adaptée. Cet atelier propose ces enrichissements mais ne les applique pas en production.

## Étape 22 — Concevoir un contrat d'enrichissement métier

1. À partir des questions d'exploitation, proposez trois attributs de faible cardinalité.
2. Définissez leur type et leurs valeurs autorisées.
3. Écartez toute donnée personnelle, valeur libre ou identifiant quasi unique.
4. Indiquez si l'attribut doit servir à filtrer, agréger ou seulement expliquer un cas.
5. Ne créez aucun tag, facet, span personnalisé ou règle Datadog.

| Attribut proposé | Type et valeurs autorisées | Sensibilité | Question opérationnelle | Usage prévu |
|---|---|---|---|---|
| `tenant_tier` | chaîne : `standard`, `enterprise` | non personnelle | une catégorie est-elle davantage affectée ? | filtre et regroupement |
| `checkout_step` | chaîne : `cart`, `validation`, `payment`, `confirmation` | non personnelle | quelle étape contribue à la dégradation ? | filtre et regroupement |
| `feature` | chaîne issue d'une liste versionnée | vérifier qu'elle ne porte aucun identifiant | une fonctionnalité activée modifie-t-elle le comportement ? | comparaison contrôlée |

### Pourquoi faut-il définir les valeurs avant d'instrumenter ?

**Réponse :** un vocabulaire borné rend les comparaisons stables, limite la cardinalité et empêche l'apparition de données sensibles ou inutilisables.

**Pratique de production :** ce contrat doit être validé avec les équipes applicatives, sécurité et exploitation avant tout changement de code ou de configuration.

## Étape 23 — Préparer les modules RUM et dashboard

### 23.1 — Définir le pivot backend à rechercher depuis le RUM

1. Ne retenez pas automatiquement `eu-interfaces` : son opération `console` correspond à un traitement en ligne de commande et ne constitue pas un backend RUM pertinent.
2. Recherchez en lecture seule un service web actif, par exemple un service portant l'opération `web.request`, puis choisissez une ressource HTTP non sensible.
3. Notez son service, son environnement et son nom anonymisé si nécessaire.
4. Formulez la vérification à réaliser au module 7. Si aucun service web corrélable n'est vérifiable, inscrivez **pivot RUM-APM non vérifiable sur les données réelles** et utilisez le cas PeopleShop.

| Élément | Préparation |
|---|---|
| Service backend |  |
| Ressource ou famille de ressources |  |
| Environnement |  |
| Vérification RUM | rechercher si une requête frontend permet d'ouvrir une trace de ce backend |

### La relation RUM-APM est-elle déjà prouvée ?

**Réponse :** non. Vous avez seulement défini le pivot à rechercher. La relation exige que le RUM et l'APM soient configurés pour propager le contexte et que les données correspondantes aient été ingérées et conservées.

### 23.2 — Spécifier trois widgets APM sans les créer

Complétez le périmètre avec le service réel retenu. Conservez ces spécifications pour le module 8.

| Widget à construire au module 8 | Source | Périmètre | Mesure et agrégation | Regroupement | Représentation | Interprétation et limite |
|---|---|---|---|---|---|---|
| Débit par version | métriques APM | `env:<env> service:<service>` | requêtes par seconde ou volume | `version` | série temporelle | compare l'activité ; une version absente peut refléter un tag manquant |
| Taux d'erreur par ressource | métriques APM | même service et environnement | taux d'erreur | `resource_name`, puis `version` si lisible | série ou toplist | localise une ressource ; ne prouve pas la cause |
| Latence p95 par ressource | métriques APM | même service et environnement | p95 de latence | `resource_name`, puis `version` si lisible | série temporelle | suit la queue de distribution ; dépend du périmètre et du trafic |

### Pourquoi ne pas créer le dashboard maintenant ?

**Réponse :** l'ordre pédagogique fait d'abord explorer et qualifier les données. Le module 8 pourra ensuite construire un dashboard avec des requêtes justifiées par les constats des modules logs, APM et RUM.

# Partie 5 — Analyser les preuves PeopleShop

À partir d'ici, utilisez uniquement les observations pédagogiques suivantes.

## Étape 24 — Comparer les trois cas

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

## Étape 25 — Distinguer ce qui change et ce qui reste stable

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

## Étape 26 — Vérifier les attributs métier proposés

| Attribut | Valeurs contrôlées | Question permise |
|---|---|---|
| `tenant_tier` | `standard`, `enterprise` | une catégorie est-elle davantage affectée ? |
| `checkout_step` | `cart`, `validation`, `payment`, `confirmation` | quelle étape contribue à la dégradation ? |
| `feature` | liste contrôlée à définir avec l'équipe produit | une fonctionnalité activée modifie-t-elle le comportement ? |

### Pourquoi ces attributs sont-ils acceptables ?

**Réponse :** ils ont un vocabulaire borné, répondent à une question opérationnelle et ne désignent pas une personne précise.

**Limite :** email, contenu du panier, donnée de paiement et identifiant client presque unique sont refusés comme dimensions d'agrégation par défaut.

## Étape 27 — Rédiger la fiche de conclusion

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
| `eu-interfaces` absent | période trop courte ou activité différente | élargir jusqu'à **Past 1 Day**, puis demander un service validé |
| Aucun span | période, droits ou échantillonnage | utiliser les fiches pédagogiques fournies |
| Aucune erreur | flux réel sain sur la période | analyser une trace lente puis la fausse piste fournie |
| Trace impossible à ouvrir | données non conservées ou droits | basculer vers les captures ou fiches préparées |
| Version absente | contexte non injecté ou non visible | noter **non observé** comme limite |
| Nombreux spans Symfony | instrumentation automatique détaillée | recentrer sur ressource, hiérarchie et chemin critique |
| Aucun pivot vers les logs | parsing, corrélation, période, droits ou échantillonnage | noter **corrélation non observée** sans inventer de lien |
| Vue du module 5 absente | parcours V2 non réalisé ou vue inaccessible | saisir une requête temporaire en lecture seule, sans la sauvegarder |
| Service réel autre que `eu-interfaces` | activité réelle différente | faire valider un service applicatif actif et noter la substitution |
| Aucun attribut métier | instrumentation technique seulement | compléter le contrat proposé sans l'appliquer |

## Validation finale

- [ ] Le relevé réel est daté et sa période est explicite.
- [ ] L'analyse commence par les signaux agrégés du service.
- [ ] Service, ressource, trace et span sont distingués.
- [ ] La Dependency Map n'est pas interprétée comme une CMDB exhaustive.
- [ ] Trois observations comparables précèdent la conclusion.
- [ ] Le waterfall et les branches parallèles sont pris en compte.
- [ ] Le chemin critique est distingué du simple span le plus long.
- [ ] Une erreur observée n'est pas automatiquement appelée cause racine.
- [ ] `eu-interfaces` réel et `orders-api` fictif ne sont jamais mélangés.
- [ ] Le contrat logs-APM du module 5 est vérifié sans modifier sa vue.
- [ ] L'instrumentation PHP est auditée sans changement de configuration.
- [ ] Le résultat du pivot logs-traces est formulé sans surinterprétation.
- [ ] Trois attributs métier bornés et non sensibles sont proposés.
- [ ] Un pivot backend est préparé pour le module 7 sans prétendre que la corrélation RUM-APM existe.
- [ ] Trois widgets APM sont spécifiés pour le module 8 sans créer de dashboard.
- [ ] Fait, hypothèse, limite et test suivant sont séparés.
- [ ] Le test suivant demande au moins une preuve complémentaire.
- [ ] Aucun identifiant ou contenu sensible réel n'est conservé.
- [ ] Aucune ressource Datadog n'a été modifiée.

## Références officielles

- [Tracing PHP Applications](https://docs.datadoghq.com/tracing/trace_collection/dd_libraries/php/)
- [Service Page](https://docs.datadoghq.com/tracing/services/service_page/)
- [Correlate Logs and Traces](https://docs.datadoghq.com/tracing/other_telemetry/connect_logs_and_traces/)
- [Correlating PHP Logs and Traces](https://docs.datadoghq.com/tracing/other_telemetry/connect_logs_and_traces/php/)
- [Trace Retention](https://docs.datadoghq.com/tracing/trace_pipeline/trace_retention/)
