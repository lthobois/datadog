---
title: "Module 8 — Atelier V2 : construire un dashboard à partir des signaux explorés"
subtitle: "Document participant autonome"
lang: fr-FR
---

# Construire un dashboard pédagogique contrôlé

## Objectif

Auditer un dashboard réel, puis créer un dashboard pédagogique unique à partir des logs, traces et événements RUM réellement présents sur la plateforme.

## Livrable

Vous produisez :

- l'audit daté d'un dashboard existant ;
- un dashboard réel `[TRAINING] M08 - <participant> - <date>` ;
- trois widgets agrégés fondés sur `sigman`, `eu-interfaces` et `peopulse` ;
- cinq questions opérationnelles et les actions associées ;
- la définition de cinq requêtes et de cinq widgets au maximum ;
- les variables du dashboard ;
- une disposition allant de l'impact vers le diagnostic ;
- une grille de recette.

## Place dans la progression

Le module 5 a exploré les logs `sigman`. Le module 6 a qualifié l'APM de `eu-interfaces` avec l'opération `console`. Le module 7 a validé les agrégats RUM de `peopulse`, tout en interdisant la copie des noms d'actions et des URL paramétrées. Les valeurs exactes à saisir sont données ci-dessous lors de la création de chaque widget.

L'atelier n'utilise aucun service, métrique ou incident fictif. Les trois sources retenues (`sigman`, `eu-interfaces` et `peopulse`) sont réelles mais indépendantes : leur juxtaposition sert à apprendre les widgets et ne démontre aucune corrélation technique.

## Règle d'écriture contrôlée

Vous êtes autorisé à créer et modifier uniquement votre dashboard :

```text
[TRAINING] M08 - <identifiant-participant> - <AAAAMMJJ>
```

L'identifiant participant doit être professionnel, court et non sensible, par exemple `loic-thobois`. N'éditez, ne clonez, ne partagez et ne supprimez aucun dashboard existant. Ne créez ni monitor, SLO, vue enregistrée, ressource applicative ou configuration. N'utilisez aucune donnée personnelle, nom d'action RUM, URL détaillée, message de log ou identifiant réel dans les titres, filtres et descriptions.

Si vous ne disposez pas du droit de créer un dashboard, exécutez les étapes de construction comme une recette documentée et utilisez les captures fournies.

## Prérequis

- Chrome connecté à Datadog ;
- accès en lecture aux dashboards, logs, APM et RUM ;
- droit de créer un dashboard, ou parcours de repli sans écriture ;
- convention de services et de tags du module 4 ;
- requêtes et observations issues des logs du module 5 ;
- hypothèse et indicateurs APM du module 6 ;
- indicateurs d'expérience utilisateur retenus au module 7 ;
- un document dans lequel consigner les réponses.

## Comment utiliser ce document

Effectuez chaque observation avant de lire la réponse de référence qui la suit. Les nombres, périodes et widgets de la plateforme peuvent évoluer : votre relevé daté prévaut sur l'état historique indiqué. Toutes les parties utilisent uniquement les données réelles sous forme agrégée. Ne déduisez aucune corrélation entre les trois sources sans preuve technique.

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

1. Saisissez `Monitoring EU-Interfaces` dans **Search dashboards**.
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

### Quels widgets étaient visibles lors de la vérification ?

**Réponse :** le dashboard **Monitoring EU-Interfaces** présentait notamment **Nombre d'interfaces (cron) uniques**, **Nombre d'exécutions d'interfaces (cron)**, **Nombre de cron en erreur**, **Nombre de CG exécutant des interfaces**, **Exécutions par Interfaces**, **Exécutions par CG**, **Statuts d'Exécution** et **Dernières exécutions d'interfaces**.

**Pourquoi :** cette référence aide à vérifier que vous êtes sur la bonne vue, mais votre inventaire actuel reste la source de vérité.

**Limite :** le titre décrit parfois la donnée sans expliquer la décision ni l'action attendue.

## Étape 5 — Examiner un widget sans l'éditer

1. Choisissez une série temporelle visible.
2. Repérez son titre, sa légende, ses unités et les séries affichées.
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

# Partie 2 — Comprendre la navigation depuis un dashboard

## Étape 8 — Examiner la variable réelle

1. Relevez la question rendue possible par la variable actuelle.
2. Déterminez si son libellé explique clairement son usage.
3. Notez les dimensions absentes qui seraient utiles pour comparer des déploiements.

### Que permettent les variables observées dans `Monitoring EU-Interfaces` ?

**Réponse :** `managementCenter`, `CmdName` et `version` étaient visibles avec la valeur `*`. Elles permettent de réduire respectivement le centre de gestion, la commande et la version lorsque les widgets les utilisent.

**Pourquoi :** ce filtre peut être pertinent pour isoler des réponses, mais il ne remplace pas `env`, `service` et `version` pour contextualiser un déploiement.

**Limite :** seule l'inspection des requêtes ou un test autorisé permet de confirmer son application à tous les widgets.

## Étape 9 — Passer du dashboard à la page APM

1. Ouvrez **APM Home**.
2. Recherchez `eu-interfaces` dans la liste des services.
3. Ouvrez sa page de service en lecture seule.
4. Repérez les filtres **env** et **version** ainsi que la période.
5. Revenez au dashboard avec l'onglet précédent du navigateur.

### Pourquoi effectuer ce détour par APM ?

**Réponse :** pour vérifier que les dimensions prévues pour le dashboard correspondent à des dimensions de navigation réelles du service.

**Pourquoi :** le module 4 a défini un contrat commun ; le dashboard doit le réutiliser pour faciliter le passage vers APM.

**Limite :** `eu-interfaces` sert à apprendre la navigation et à alimenter un widget agrégé de formation. Ses valeurs ne prouvent aucune relation avec les données Logs ou RUM retenues dans les autres widgets.

# Partie 3 — Construire le dashboard réel de formation

Les trois widgets suivants utilisent des sources réelles différentes. Ils servent à apprendre la construction et la lecture croisée ; ils ne décrivent pas un même incident et ne doivent pas être corrélés entre eux.

## Étape 10 — Créer le dashboard et saisir son identité

1. Dans **Dashboards > Dashboard List**, cliquez sur **New Dashboard**.
2. Dans le champ du titre, saisissez directement :

```text
[TRAINING] M08 - <identifiant-participant> - <AAAAMMJJ>
```

3. Remplacez les deux valeurs entre chevrons par votre identifiant autorisé et la date du jour.
4. Avant de valider, vérifiez dans la liste qu'aucun dashboard ne porte déjà ce titre ; ajoutez uniquement le suffixe fourni par le formateur en cas de collision.
5. Choisissez le format **Dashboard** ou la disposition libre proposée.
6. N'activez aucun partage public.
7. Si une description est proposée, saisissez :

```text
Atelier ORSYS Datadog — agrégats de formation issus des modules 5 à 7.
Ne pas utiliser comme dashboard de production. Nettoyage en fin de formation.
```

8. Validez la création.
9. Lorsque le dashboard s'affiche, vérifiez que son titre contient bien votre identifiant.

**Réponse expliquée :** le dashboard interroge les données existantes ; il ne modifie ni les logs, ni l'instrumentation APM, ni le RUM. Le préfixe et votre identifiant permettent de reconnaître la ressource à supprimer en fin de formation.
## Étape 11 — Ajouter le cadre de lecture

1. Cliquez sur **Add Widgets**.
2. Ajoutez un widget **Note & Links** ou **Note**.
3. Saisissez le contenu suivant :

```markdown
## Télémétrie explorée pendant la formation

- Logs : `sigman`
- APM : `eu-interfaces`, opération `console`
- RUM : application `peopulse`, agrégats uniquement

Ces trois sources ne décrivent pas un même incident.
Le dashboard ne contient aucun jeu de données de démonstration.
```

4. N'ajoutez aucun lien externe, identifiant ou donnée personnelle.

### Pourquoi commencer par une note ?

**Réponse :** elle empêche le lecteur de supposer une corrélation entre trois sources choisies pour leur valeur pédagogique et non parce qu'elles appartiennent au même parcours.

## Étape 12 — Ajouter le widget Logs

1. Cliquez sur **Add Widgets**, puis choisissez **Timeseries**.
2. Choisissez la source **Log Events** ou **Logs**.
3. Sélectionnez l'agrégation **Count**.
4. Dans le champ de requête, saisissez directement :

```text
service:sigman status:error
```

5. Ne regroupez pas par message, utilisateur, hôte ou identifiant.
6. Donnez au widget le titre :

```text
Logs — erreurs sigman
```


### Que signifie une courbe vide ?

**Réponse :** aucun événement correspondant n'est retourné pour la période et le périmètre actuels. Cela ne prouve ni l'absence historique d'erreur ni l'absence de collecte.

**Plan de repli :** conservez le widget et ajoutez dans sa description **aucun événement sur la période de recette** ; n'élargissez pas la requête à tous les logs de production.

## Étape 13 — Ajouter le widget APM

1. Ajoutez un widget **Timeseries**.
2. Choisissez la source **APM Metrics**. Si ce libellé n'est pas proposé, choisissez **Indexed Spans** et restez sur un comptage.
3. Sélectionnez le service `eu-interfaces`.
4. Sélectionnez l'opération `console` lorsqu'elle est proposée.
5. Choisissez une mesure de requêtes, hits, spans ou traces comptées par Datadog. N'inventez pas de nom de métrique.
6. Ne regroupez pas par ressource de commande, hôte ou identifiant métier.
7. Donnez au widget le titre :

```text
APM — activité eu-interfaces / console
```

8. Si l'aperçu est vide sur une heure, utilisez **Past 4 Hours**, puis **Past 1 Day** pour la recette.

### Pourquoi utiliser un volume avant une latence p95 ?

**Réponse :** le faible débit de `eu-interfaces` rend un percentile instable sur une fenêtre courte. Le volume confirme d'abord que la source alimente le widget ; la latence p95 reste une amélioration possible lorsque l'échantillon est suffisant.

## Étape 14 — Ajouter le widget RUM

1. Ajoutez un widget **Timeseries** ou **Query Value**.
2. Choisissez la source **RUM**.
3. Sélectionnez le type d'événement **Views**.
4. Filtrez sur l'application `peopulse` au moyen du sélecteur d'application ou de la facette proposée par l'interface.
5. Choisissez l'agrégation **Count**.
6. Ne regroupez pas par `view.name`, action, utilisateur ou URL.
7. Donnez au widget le titre :

```text
RUM — vues peopulse
```


### Pourquoi ne pas afficher les actions ou les URL réelles ?

**Réponse :** la vérification du module 7 a montré que des noms d'actions et URL peuvent contenir du texte métier, des paramètres ou des données personnelles. Le comptage agrégé des vues suffit pour apprendre la source RUM sans exposer ces valeurs.


1. Placez la note en haut du dashboard.
2. Placez les trois séries dans l'ordre **Logs**, **APM**, **RUM**.
3. Choisissez une période commune permettant d'afficher des données, sans dépasser **Past 1 Day** pour la recette sauf consigne du formateur.
4. Vérifiez que les titres ne contiennent aucune valeur sensible.
5. Ajoutez une variable `env` uniquement si elle est proposée et applicable aux widgets concernés ; conservez `*` par défaut.
6. N'ajoutez pas de variable `service` globale : les trois widgets ciblent volontairement trois services ou applications différents.
7. Vérifiez que le dashboard est enregistré et que son titre est exact.

| Contrôle | Résultat attendu |
|---|---|
| Nom unique et préfixé | conforme |
| Note de non-corrélation | visible |
| Widget Logs | enregistré, donnée ou absence expliquée |
| Widget APM | enregistré, périmètre `eu-interfaces` / `console` |
| Widget RUM | agrégé, sans action, URL ou utilisateur |
| Partage public | désactivé |

### Pourquoi limiter le dashboard à ces trois widgets ?

**Réponse :** cette première version valide une source par produit et la sécurité des périmètres. Les autres spécifications constituent un backlog ; elles ne sont ajoutées qu'après revue de leur question, coût, cardinalité et confidentialité.

# Partie 4 — Exploiter le dashboard sans inventer de corrélation

## Étape 15 — Comparer les comportements des widgets

1. Placez le dashboard sur **Past 1 Hour**.
2. Notez quels widgets contiennent des données.
3. Étendez à **Past 4 Hours**, puis **Past 1 Day** si nécessaire.
4. Pour chaque widget, relevez la source, le filtre, la période minimale utile et la question à laquelle il répond.

| Widget | Source réelle | Question autorisée |
|---|---|---|
| Logs — erreurs sigman | logs `service:sigman status:error` | Des erreurs `sigman` sont-elles indexées sur la période ? |
| APM — activité eu-interfaces / console | APM `service:eu-interfaces`, opération `console` | Une activité APM est-elle visible pour cette opération ? |
| RUM — vues peopulse | vues RUM de l'application `peopulse` | Des vues RUM agrégées sont-elles reçues sur la période ? |

**Réponse expliquée :** les périodes de disponibilité peuvent différer en raison du trafic, de la rétention et de l'échantillonnage. Une variation simultanée ne suffit pas à relier ces trois sources.

## Étape 16 — Tester les filtres sans perdre le contexte

1. Modifiez temporairement la période globale.
2. Si la variable `env` existe, testez une valeur réellement proposée par Datadog puis revenez à `*`.
3. Vérifiez que chaque widget reste compréhensible lorsqu'il est vide.
4. N'ajoutez pas de variable globale `service` : elle masquerait deux des trois sources.

**Résultat attendu :** le dashboard reste lisible sans suggérer que `sigman`, `eu-interfaces` et `peopulse` appartiennent à une même chaîne de service.

## Étape 17 — Examiner une requête en mode édition

Pour chacun de vos trois widgets :

1. Ouvrez son mode d'édition.
2. Identifiez la source, l'agrégation, les filtres et l'unité.
3. Vérifiez que le titre cite explicitement la source réelle.
4. Fermez ou annulez sans ajouter de nouvelle source.

**Interprétation :** un widget fiable rend visibles sa portée et ses limites. Les titres génériques tels que « erreurs » ou « trafic » sont insuffisants dans un environnement partagé.

## Étape 18 — Utiliser les pivots de navigation en lecture seule

1. Depuis le widget Logs, ouvrez le détail dans le Log Explorer si le lien est proposé.
3. Depuis le widget APM, ouvrez le détail APM si le lien est proposé.
4. Pour le RUM, restez sur les agrégats ; n'ouvrez ni nom d'action sensible, ni session, ni replay.

**Réponse expliquée :** le dashboard sert de point d'entrée. Le diagnostic détaillé se poursuit dans l'explorateur correspondant, avec les mêmes filtres et la même période lorsque Datadog sait les transmettre.

# Partie 5 — Recette et nettoyage

## Étape 19 — Effectuer la recette fonctionnelle

Vérifiez les points suivants :

- le nom commence par `[TRAINING] M08` et contient votre identifiant et la date ;
- la note supérieure indique que les trois sources ne représentent pas un incident commun ;
- les widgets utilisent uniquement `sigman`, `eu-interfaces` et `peopulse` ;
- aucune donnée nominative, URL détaillée ou action RUM sensible n'est affichée ;
- aucun partage public ni notification n'est configuré ;
- aucun dashboard existant n'a été modifié.

## Étape 20 — Restituer sans surinterpréter

Présentez le dashboard en répondant aux trois questions suivantes :

1. Quel widget sert à apprendre la recherche de logs ?
2. Quel widget sert à apprendre une requête APM ?
3. Quel widget sert à apprendre une agrégation RUM respectueuse de la confidentialité ?

**Réponse de référence :** chaque widget illustre une famille de données distincte. Le dashboard est un support de navigation et de requêtage, pas la preuve d'un incident transversal.

## Étape 21 — Identifier le dashboard à supprimer

1. Vérifiez que le titre commence par `[TRAINING] M08` et contient votre identifiant.
2. À la fin de la formation, supprimez uniquement ce dashboard si le formateur vous l'autorise.
3. Si vous ne disposez pas du droit de suppression, communiquez simplement son titre exact au formateur.

Ne supprimez aucune autre ressource.

## Questions de synthèse corrigées

### Peut-on conclure que les erreurs `sigman` affectent `peopulse` ?

**Réponse :** non. Aucun identifiant de corrélation ni aucune relation de service n'a été validé entre ces sources.

### Pourquoi conserver néanmoins trois sources sur le même dashboard ?

**Réponse :** pour apprendre à sélectionner plusieurs types de données, configurer des widgets et comprendre leurs différences dans l'interface Datadog.

### Que faut-il faire avant de construire un dashboard d'incident corrélé ?

**Réponse :** choisir un service réel disposant de signaux reliés par des attributs vérifiés, puis confirmer la chaîne de corrélation dans les explorateurs avant de l'afficher.

## Validation finale

- [ ] Seules des données réellement présentes sur la plateforme sont utilisées.
- [ ] Aucun service, métrique, seuil ou incident fictif n'est mentionné.
- [ ] Les trois sources réelles ne sont jamais présentées comme corrélées.
- [ ] Une seule ressource a été créée : le dashboard du participant.
- [ ] La confidentialité RUM est respectée.
- [ ] Le titre exact du dashboard à supprimer est conservé.

## Références officielles

- [Dashboards](https://docs.datadoghq.com/dashboards/)
- [Widgets](https://docs.datadoghq.com/dashboards/widgets/)
- [Timeseries Widget](https://docs.datadoghq.com/dashboards/widgets/timeseries/)
- [Template Variables](https://docs.datadoghq.com/dashboards/template_variables/)
- [Widget Configuration](https://docs.datadoghq.com/dashboards/widgets/configuration/)
