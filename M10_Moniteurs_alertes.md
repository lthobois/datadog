---
title: "Module 10 — Atelier : concevoir un monitor actionnable"
subtitle: "Document participant autonome"
lang: fr-FR
---

# Concevoir un monitor utile sans générer de bruit

## Objectif

Parcourir les vues de surveillance existantes en lecture seule, comprendre comment un monitor est évalué, puis produire hors plateforme la fiche complète d'un monitor PeopleShop actionnable.

## Livrable

Vous produisez :

- l'audit daté de trois monitors réels ;
- une décision argumentée pour chacun ;
- la fiche du monitor cible ;
- les messages d'alerte et de rétablissement ;
- le résultat des scénarios de test ;
- la liste des paramètres restant à calibrer.

## Règle de sécurité

Travaillez uniquement en lecture. N'utilisez pas **New Monitor**, **Edit**, **Clone**, **Mute**, **Resolve**, **Delete**, **Downtime** ou une commande d'enregistrement. Ne cochez aucun monitor et ne modifiez pas de filtre partagé. Les seuils et délais de cet atelier sont des hypothèses pédagogiques, jamais des valeurs de production validées.

## Prérequis

- convention `env/service/version/team` du module 4 ;
- dashboard PeopleShop conçu au module 8 ;
- journal d'investigation et symptômes vérifiés du module 9 ;
- accès en lecture à la liste et au détail des monitors ;
- scénario fictif `orders-api`, `env:training`.

## Comment utiliser ce document

Observez d'abord la plateforme, puis comparez votre analyse aux réponses placées après chaque question. Les nombres et états réels évoluent : votre relevé daté prévaut sur l'instantané historique. La première partie concerne les monitors réels ; la conception cible concerne exclusivement PeopleShop et doit dériver d'un symptôme confirmé pendant l'investigation du module 9.

# Partie 1 — Comprendre le portefeuille de monitors

## Étape 1 — Ouvrir la liste des monitors

1. Dans le menu gauche, ouvrez **Monitors**, puis **Monitor List** ou **Manage Monitors**.
2. Repérez la recherche et les filtres de statut, priorité, type, équipe et tags.
3. Repérez les colonnes de nom, statut, priorité, type et tags.
4. Notez la date, l'heure et le chemin de navigation.
5. Ne sélectionnez aucune ligne par case à cocher.

| Élément | Observation |
|---|---|
| Date et heure |  |
| Chemin de navigation |  |
| Filtres visibles |  |
| Colonnes visibles |  |

### À quoi sert la liste des monitors ?

**Réponse :** elle permet de rechercher, filtrer et gouverner le portefeuille de règles de surveillance.

**Pourquoi :** avant d'ajouter une alerte, il faut vérifier qu'un risque équivalent n'est pas déjà couvert et identifier les monitors bruyants ou orphelins.

## Étape 2 — Qualifier le portefeuille visible

1. Relevez le total affiché.
2. Relevez les nombres par état lorsqu'ils sont visibles : **OK**, **Alert**, **Warn** et **No Data**.
3. Relevez les types dominants.
4. Relevez le nombre de monitors sans priorité si cette information peut être obtenue sans modifier un filtre partagé.

| Indicateur | Valeur observée |
|---|---:|
| Total |  |
| OK |  |
| Alert |  |
| Warn |  |
| No Data |  |
| Sans priorité |  |
| Types dominants |  |

### Quel était l'état historique lors de la conception ?

**Réponse :** 9 monitors étaient visibles : 7 en **OK**, 2 en **No Data**, aucun en **Alert** ou **Warning** ; 6 n'avaient pas de priorité.

**Pourquoi :** cet instantané aide à comprendre le type de relevé attendu.

**Limite :** il date du 19 juillet 2026 et ne doit jamais remplacer votre observation actuelle.

## Étape 3 — Comprendre les états

### Que signifient les états OK, Warning, Alert et No Data ?

**Réponse :** **OK** signifie que la condition d'alerte n'est pas satisfaite ; **Warning** et **Alert** correspondent à des niveaux définis par les seuils ; **No Data** indique que le monitor ne reçoit pas les données attendues selon sa configuration.

**Pourquoi :** No Data n'est pas automatiquement une panne applicative : il peut correspondre à une absence normale de trafic, une perte de collecte ou la disparition d'un groupe.

## Étape 4 — Observer les types de monitors

1. Relevez les types visibles dans la liste.
2. Associez-les aux usages suivants.

| Type | Usage de référence |
|---|---|
| Metric | évaluer une mesure ou une formule |
| APM | évaluer requêtes, erreurs, latence ou spans |
| Logs | évaluer des événements répondant à une recherche |
| Anomaly | comparer une valeur à un comportement attendu |
| Integration | surveiller un signal fourni par une intégration |
| Error Tracking | surveiller des groupes d'erreurs |
| Watchdog | exploiter une détection automatique Datadog |
| SLO | surveiller la consommation d'un objectif de fiabilité |
| Composite | combiner les états de plusieurs monitors |

### Le type rend-il à lui seul un monitor actionnable ?

**Réponse :** non. L'actionnabilité dépend surtout du risque, du signal, du périmètre, de l'évaluation, du groupement, de l'owner et du message.

## Étape 5 — Sélectionner trois monitors

Choisissez sans les modifier :

1. un nom qui paraît actionnable ;
2. un monitor en **No Data** ou lié à l'absence de données ;
3. un monitor dont la priorité, les tags ou le périmètre semblent incomplets.

| Monitor anonymisé | Statut | Priorité | Type | Tags visibles |
|---|---|---|---|---|
| A |  |  |  |  |
| B |  |  |  |  |
| C |  |  |  |  |

### Pourquoi anonymiser les noms dans le livrable ?

**Réponse :** l'objectif est d'auditer des propriétés de conception sans diffuser des informations internes sur les services ou l'organisation.

## Étape 6 — Ouvrir le détail en lecture seule

1. Ouvrez le monitor A en cliquant sur son nom.
2. Vérifiez que la page affiche son statut et son contexte sans entrer en édition.
3. Repérez le nom, le type, la requête ou le signal, les groupes, les tags, la priorité et le message lorsqu'ils sont visibles.
4. Repérez les accès à l'historique, au graphe d'évaluation ou aux événements.
5. Revenez à la liste avec la navigation du navigateur.

### Pourquoi le détail est-il nécessaire après la liste ?

**Réponse :** la liste permet de trier le portefeuille, mais le détail montre comment le signal est évalué et quelle action la notification prépare.

**Limite :** une information non visible avec vos droits doit être notée **inconnue**, jamais inventée.

## Étape 7 — Examiner l'historique

1. Sur un monitor consultable, ouvrez **History**, **Status & History**, **Events** ou la vue équivalente.
2. Repérez les transitions d'état et les éventuelles récupérations.
3. Notez si le monitor change souvent d'état ou reste durablement dans le même état.
4. Ne déclenchez aucune résolution ou mise en sourdine.

### Que peut révéler un historique avec de fréquents allers-retours ?

**Réponse :** un risque de flapping causé par un seuil trop proche du niveau nominal, une fenêtre trop courte ou une récupération sans hystérésis.

**Limite :** une fréquence élevée ne suffit pas à supprimer le monitor ; il faut vérifier si les notifications ont conduit à des actions utiles.

## Étape 8 — Examiner le graphe d'évaluation

1. Repérez le graphe d'évaluation, les seuils et la fenêtre, lorsqu'ils sont visibles.
2. Identifiez la série évaluée et les changements d'état.
3. Notez si le signal dépasse brièvement ou durablement le seuil.

### Pourquoi le graphe d'évaluation est-il plus utile que le seul état courant ?

**Réponse :** il montre la relation entre le signal, les seuils, la fenêtre et les transitions d'état.

**Pourquoi :** il aide à distinguer un pic transitoire d'une dégradation durable et prépare le calibrage.

## Étape 9 — Auditer nom, périmètre et ownership

Pour chaque monitor, complétez **oui**, **non** ou **inconnu**.

| Question | A | B | C |
|---|:---:|:---:|:---:|
| La priorité est explicite ? |  |  |  |
| L'environnement est identifiable ? |  |  |  |
| Le service ou composant est identifiable ? |  |  |  |
| Le symptôme est compréhensible ? |  |  |  |
| Le nom reste stable malgré un changement de pod ? |  |  |  |
| Une équipe responsable est identifiable ? |  |  |  |
| Une première action ou un runbook est visible ? |  |  |  |

### Quelle réponse faut-il donner lorsqu'une information n'est pas affichée ?

**Réponse :** **inconnu dans la vue consultée**.

**Pourquoi :** l'absence visible peut conduire à proposer de compléter ou valider le monitor, mais pas à inventer sa configuration.

## Étape 10 — Proposer une décision de gestion

| Décision | Quand la proposer |
|---|---|
| conserver | risque distinct, owner clair et action utile |
| fusionner | même risque et même unité d'action qu'un doublon |
| transformer | signal, cardinalité ou logique inadéquats |
| supprimer | risque obsolète ou couvert ailleurs, après validation |
| compléter | priorité, tags, owner, runbook ou message manquants |

| Monitor | Décision proposée | Justification | Validation nécessaire |
|---|---|---|---|
| A |  |  |  |
| B |  |  |  |
| C |  |  |  |

### Une décision proposée doit-elle être exécutée pendant l'atelier ?

**Réponse :** non. Toute action exige la validation du propriétaire et reste hors du périmètre en lecture seule.

# Partie 2 — Diagnostiquer un monitor bruité

## Étape 11 — Lire la définition pédagogique

```text
Nom : Errors are high
Condition : nombre d'erreurs > 1 sur 1 minute
Regroupement : pod
Destinataires : toute l'équipe
Message : Errors are high
```

### Quels défauts expliquent le bruit ?

**Réponse :** le nom omet priorité, environnement, service et impact ; le compte brut dépend du trafic ; la fenêtre réagit aux pics ; le pod est éphémère ; le routage dilue la responsabilité ; le message n'indique ni valeur, seuil, périmètre, lien, runbook ni owner.

**Conclusion :** ce monitor risque d'alerter souvent sans permettre de prioriser ni d'agir rapidement.

## Étape 12 — Comparer compte et taux

| Situation | Erreurs | Requêtes | Taux |
|---|---:|---:|---:|
| A | 5 | 10 | 50 % |
| B | 5 | 10 000 | 0,05 % |

### Pourquoi un compte brut est-il trompeur ?

**Réponse :** le même nombre d'erreurs n'a pas le même impact relatif selon le trafic.

**Limite :** un taux seul est aussi trompeur à très faible trafic ; il faut lui associer un volume minimal.

## Étape 13 — Comparer les groupements

### Quel risque présente un regroupement par pod ?

**Réponse :** il peut créer une alerte par instance éphémère, provoquer une tempête et répartir un incident de service en groupes peu actionnables.

**Pourquoi :** le groupement doit suivre l'unité sur laquelle une équipe peut agir, généralement `env` et `service` ici.

# Partie 3 — Concevoir le monitor PeopleShop

## Étape 14 — Définir impact, owner et unité d'action

| Élément | Réponse de référence |
|---|---|
| Impact | validations de commande lentes ou en échec |
| Owner | `@team-orders` |
| Unité d'action | service et environnement |
| Dimensions de diagnostic | ressource et version |
| Dimensions de notification | `env` et `service` |

### Pourquoi `version` reste-t-elle d'abord une dimension de diagnostic ?

**Réponse :** l'équipe agit sur le service affecté et compare les versions pendant l'investigation.

**Limite :** une notification par version est acceptable seulement si les groupes ont des actions et propriétaires distincts.

## Étape 15 — Nommer le monitor

```text
[P2][training][orders-api] Elevated validation error rate
```

### Le niveau P2 est-il validé pour la production ?

**Réponse :** non. Il s'agit d'une hypothèse pédagogique à confirmer avec la matrice d'impact et d'urgence du client.

## Étape 16 — Choisir le type et le signal

Référence conceptuelle : monitor APM ou métrique, selon le signal réellement disponible.

```text
error_rate = validation_errors / validation_requests * 100

alerter si :
error_rate > seuil_à_calibrer
ET validation_requests > volume_minimal_à_calibrer
```

### Pourquoi ne pas imposer le type avant de vérifier les données ?

**Réponse :** le type dépend de la disponibilité et de la qualité des métriques APM, spans ou métriques métier dans l'organisation.

## Étape 17 — Définir le périmètre

| Élément | Réponse de référence |
|---|---|
| environnement | `env:training` |
| service | `service:orders-api` |
| ressource | `POST /api/orders/validate` |
| numérateur | validations en erreur selon une définition explicite |
| dénominateur | toutes les validations du même périmètre |
| exclusions | uniquement des cas fonctionnels documentés |

### Quelle erreur critique faut-il éviter dans la formule ?

**Réponse :** diviser des séries ayant des environnements, services, ressources, périodes ou regroupements différents.

## Étape 18 — Choisir agrégation, fenêtre et groupement

| Paramètre | Réponse de référence |
|---|---|
| Agrégation | ratio cohérent entre volumes compatibles |
| Fenêtre | plusieurs minutes, à calibrer avec l'historique |
| Groupement | `env,service` |
| `version` | diagnostic, sauf action distincte par version |
| `pod` | exclu par défaut |

### Pourquoi une fenêtre plus longue qu'une minute ?

**Réponse :** elle absorbe davantage les pics isolés et réduit les changements d'état sans intervention.

**Limite :** une fenêtre trop longue retarde la détection ; la valeur doit être calibrée avec le SLO, l'historique et le coût des faux positifs.

## Étape 19 — Définir Warning, Alert et Recovery

Exemple exclusivement pédagogique :

| État | Hypothèse à calibrer | Action distincte |
|---|---|---|
| Warning | taux supérieur à 3 % | surveiller et préparer l'analyse |
| Alert | taux supérieur à 5 % avec volume minimal | engager la réponse incident |
| Recovery | taux inférieur à 2 % pendant une période stable | confirmer le retour utilisateur |

### Pourquoi le seuil de Recovery est-il inférieur au seuil Alert ?

**Réponse :** cette hystérésis évite que le monitor alterne entre Alert et OK lorsque le signal oscille près du seuil.

**Limite :** 3 %, 5 %, 2 % et toute durée de stabilité sont des exemples à calibrer, pas des valeurs client.

## Étape 20 — Traiter les données manquantes et retardées

| Cas | Réponse de référence |
|---|---|
| absence normale de trafic | ne pas créer automatiquement un incident applicatif |
| perte inattendue de télémétrie | utiliser un monitor de collecte séparé, owner plateforme |
| nouveau groupe | appliquer un délai de démarrage adapté |
| données en retard | utiliser un délai d'évaluation adapté à la source |
| maintenance planifiée | appliquer un downtime borné et ciblé |

### Quelle différence existe entre délai d'évaluation et délai de nouveau groupe ?

**Réponse :** le délai d'évaluation attend l'arrivée des données ; le délai de nouveau groupe laisse une nouvelle entité démarrer avant de l'évaluer.

### Pourquoi séparer perte de télémétrie et incident applicatif ?

**Réponse :** les significations, owners et premières actions diffèrent : l'équipe plateforme rétablit la collecte, tandis que l'équipe Orders traite l'expérience utilisateur.

## Étape 21 — Définir routage et escalade

| Élément | Réponse de référence |
|---|---|
| Priorité | P2 à confirmer |
| Owner | `@team-orders` |
| Destinataire initial | canal ou astreinte Orders |
| Escalade | selon durée, étendue et absence de prise en charge |
| Maintenance | downtime ciblé, borné et planifié |

### Un downtime corrige-t-il un monitor bruyant ?

**Réponse :** non. Il supprime temporairement les notifications ; la logique du monitor doit ensuite être corrigée, transformée ou supprimée après validation.

### Quand une re-notification est-elle utile ?

**Réponse :** lorsque l'alerte reste ouverte et qu'une escalade ou une nouvelle action est attendue.

**Limite :** répéter le même message aux mêmes destinataires sans limite ne fait qu'ajouter du bruit.

## Étape 22 — Rédiger le message d'alerte

```text
Impact probable : des validations de commande échouent sur orders-api
en training.

Le taux d'erreur observé est {{value}} %, au-dessus du seuil
{{threshold}} % sur la fenêtre d'évaluation, avec un volume significatif.

Périmètre : env={{env.name}}, service={{service.name}}.
Vérifier d'abord la ressource de validation et comparer les versions.

Dashboard : <lien-dashboard>
APM / diagnostic : <lien-diagnostic>
Runbook : RB-ORDERS-001
Owner : @team-orders
```

### Pourquoi ce message est-il actionnable ?

**Réponse :** il fournit impact, preuve, périmètre, première action, liens, runbook et owner sans reconstruire le contexte.

**Limite :** les variables et liens exacts doivent être testés dans un environnement autorisé avant publication.

## Étape 23 — Rédiger le message de rétablissement

```text
Recovery : le taux d'erreur de validation est revenu sous le seuil de
rétablissement pendant la période de stabilité définie.

Confirmer le retour de l'expérience utilisateur, puis consulter le
dashboard et RB-ORDERS-001 pour la clôture et le suivi.
```

### Recovery signifie-t-il que l'incident est résolu ?

**Réponse :** non. Le signal technique est revenu sous sa condition de récupération ; l'équipe doit encore confirmer l'expérience utilisateur et le mécanisme traité.

# Partie 4 — Tester et améliorer

## Étape 24 — Exécuter les scénarios sur papier

| Scénario | Réponse de référence |
|---|---|
| pic bref puis retour normal | pas d'Alert si la fenêtre l'absorbe |
| taux élevé durable et volume significatif | Alert |
| deux erreurs sur très faible trafic | pas d'Alert de taux grâce au volume minimal |
| aucun trafic à une heure normalement creuse | pas d'incident applicatif |
| perte de collecte pendant une période active | monitor de collecte séparé |
| une seule version touchée | alerte service, version utilisée pour diagnostiquer |
| oscillation autour du seuil | hystérésis et stabilité limitent le flapping |
| maintenance planifiée | downtime borné et ciblé |
| retour durable au niveau normal | Recovery puis confirmation utilisateur |

### Pourquoi faut-il tester avant publication ?

**Réponse :** pour vérifier le comportement sur pics, faible trafic, No Data, nouveaux groupes, maintenance et récupération avant de créer du bruit réel.

## Étape 25 — Identifier les paramètres à calibrer

| Paramètre | Sources de calibrage |
|---|---|
| seuils Warning/Alert/Recovery | historique, SLO, incidents et faux positifs |
| volume minimal | trafic nominal et confiance statistique |
| fenêtre | compromis vitesse/stabilité |
| priorité | matrice d'impact client |
| délais | cadence et retard des données |
| routage/escalade | organisation d'astreinte réelle |

### Pourquoi aucun seuil universel n'est-il fourni ?

**Réponse :** la valeur utile dépend du trafic, de la variabilité, de l'objectif de fiabilité et du coût métier d'une détection tardive ou d'un faux positif.

## Étape 26 — Effectuer la revue autonome

Vérifiez puis corrigez deux points précis de votre fiche.

- [ ] Le monitor correspond à un risque utilisateur.
- [ ] Le numérateur et le dénominateur ont le même périmètre.
- [ ] Le taux possède un volume minimal.
- [ ] Le groupement suit l'unité d'action.
- [ ] Warning, Alert et Recovery déclenchent des comportements distincts.
- [ ] No Data et perte de collecte ne sont pas confondus.
- [ ] Le message fournit une première action.
- [ ] Tous les chiffres sont marqués **à calibrer**.

| Amélioration précise | Justification |
|---|---|
| 1 |  |
| 2 |  |

## Questions de synthèse corrigées

### Quand un signal doit-il rester sur un dashboard plutôt que devenir une alerte ?

**Réponse :** lorsqu'il informe sans demander une action urgente, distincte et attribuée à un owner.

### Pourquoi une alerte est-elle un contrat d'action ?

**Réponse :** elle définit un risque, un signal, un périmètre, une condition, un destinataire et la première action attendue.

### Comment réduire le bruit sans perdre la couverture ?

**Réponse :** corriger les doublons et signaux sans action, utiliser un taux avec volume minimal, regrouper au bon niveau, traiter No Data et nouveaux groupes, définir Recovery et tester les scénarios critiques.

## Aide au diagnostic

| Difficulté | Interprétation | Action en lecture seule |
|---|---|---|
| Aucun monitor visible | droits ou portefeuille vide | utiliser la définition bruitée et une capture anonymisée |
| État différent de l'historique | évolution normale | relever l'état courant sans chercher les anciens nombres |
| Détail non accessible | droits insuffisants | auditer les éléments visibles et noter **inconnu** |
| Confusion compte/taux | trafic ignoré | comparer 5/10 et 5/10 000 |
| Désaccord sur le seuil | absence de baseline | marquer **à calibrer** et citer les données nécessaires |
| No Data toujours en Alert | sémantique et owner confondus | séparer absence de trafic et perte de collecte |

## Validation finale

- [ ] Trois monitors ont été audités sans modification.
- [ ] Les observations réelles sont datées et anonymisées.
- [ ] Chaque décision de gestion est justifiée et non exécutée.
- [ ] Le signal cible est un taux avec volume minimal.
- [ ] Numérateur et dénominateur partagent le même périmètre.
- [ ] Le groupement correspond à une unité d'action stable.
- [ ] Warning, Alert et Recovery sont distincts.
- [ ] No Data, nouveaux groupes et retard sont traités explicitement.
- [ ] Routage, escalade et maintenance possèdent une règle.
- [ ] Les messages indiquent impact, contexte, liens, runbook et owner.
- [ ] Les scénarios critiques ont été testés.
- [ ] Tous les seuils et délais sont présentés comme hypothèses à calibrer.
- [ ] Aucune ressource Datadog n'a été créée ou modifiée.
