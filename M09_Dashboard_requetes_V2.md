---
title: "Module 9 — Atelier : construire un dashboard à partir des signaux et monitors explorés"
subtitle: "Document participant autonome"
lang: fr-FR
---

# Construire un dashboard pédagogique contrôlé

## Objectif

Auditer un dashboard réel, puis créer un dashboard pédagogique unique à partir des logs, métriques, traces, événements RUM et monitors présents sur la plateforme.

## Livrable

Vous produisez :

- l'audit daté d'un dashboard existant ;
- un dashboard réel `[TRAINING] M09 - <participant> - <date>` ;
- onze widgets obligatoires fondés sur la métrique et les logs pédagogiques, `eu-interfaces`, `peopulse` et les monitors créés au module 8 ;
- une question opérationnelle et une action associée à chaque widget ;
- les variables du dashboard ;
- une disposition allant de l'impact vers le diagnostic ;
- une grille de recette.

## Place dans la progression

Le module 5 a ingéré les logs et la métrique `training.checkout.queue_depth` du service pédagogique. Le module 6 a qualifié l'APM de `eu-interfaces` avec l'opération `console`. Le module 7 a validé les agrégats RUM de `peopulse`. Le module 8 a créé et testé un Log Monitor et un Metric Monitor. Les valeurs à saisir sont données ci-dessous lors de la création de chaque widget.

L'atelier utilise la métrique du service pédagogique, l'APM de `eu-interfaces`, les agrégats RUM de `peopulse` et l'état des deux monitors créés au module 8. Il prépare également la requête du scénario de logs qui sera envoyé au module 10. Ces sources sont indépendantes : leur juxtaposition sert à apprendre les widgets et ne démontre aucune corrélation technique.

## Règle d'écriture contrôlée

Créez et modifiez votre dashboard :

```text
[TRAINING] M09 - <identifiant-participant> - <AAAAMMJJ>
```

L'identifiant participant doit être professionnel, court et non sensible, par exemple `loic-thobois`. N'utilisez aucune donnée personnelle, nom d'action RUM, URL détaillée, message de log ou identifiant réel dans les titres, filtres et descriptions.

Si vous ne disposez pas du droit de créer un dashboard, exécutez les étapes de construction comme une recette documentée et utilisez les captures fournies.

## Prérequis

- Chrome connecté à Datadog ;
- accès aux dashboards, métriques, logs, APM et RUM ;
- droit de créer un dashboard, ou parcours de repli sans écriture ;
- convention de services et de tags du module 4 ;
- requêtes et observations issues des logs et métriques du module 5 ;
- hypothèse et indicateurs APM du module 6 ;
- indicateurs d'expérience utilisateur retenus au module 7 ;
- Log Monitor et Metric Monitor créés et testés au module 8 ;

## Comment utiliser ce document

Effectuez chaque observation avant de lire la réponse de référence qui la suit. Les nombres, périodes et widgets peuvent évoluer : utilisez les valeurs visibles. Toutes les parties utilisent les données réelles sous forme agrégée.

# Partie 1 — Parcourir les dashboards

## Étape 1 — Ouvrir la liste des dashboards

1. Dans le menu gauche, ouvrez **Dashboards**.
2. Sélectionnez **Dashboard List** ou **List**, selon le libellé visible.
3. Repérez le champ de recherche, les listes ou filtres disponibles et le nombre total de dashboards.
4. Observez la date, l'heure et le nombre affiché.

### À quoi sert la liste des dashboards ?

**Réponse :** elle sert à retrouver, filtrer et ouvrir les vues partagées de l'organisation.

**Pourquoi :** un dashboard est une ressource organisée pour un lecteur et un usage ; la liste permet d'abord d'identifier la vue pertinente avant d'analyser ses widgets.

**Limite :** le nombre et les vues visibles dépendent des droits, des équipes et des filtres. La plateforme affichait 231 dashboards lors de la vérification ; observez toujours la valeur visible au moment de l'atelier.

## Étape 2 — Rechercher le dashboard de référence

1. Saisissez `Monitoring EU-Interfaces` dans **Search dashboards**.
2. Attendez la mise à jour de la liste.
3. Ouvrez la ligne portant exactement ce titre.
4. Vérifiez qu'aucun mode d'édition n'est actif.
5. Si le dashboard est absent, choisissez un dashboard API ou service lisible et observez son titre comme substitution.

### Pourquoi auditer une vue existante avant d'en concevoir une ?

**Réponse :** pour observer les conventions réelles, les types de widgets, les filtres et l'ordre de lecture déjà utilisés dans l'organisation.

**Pourquoi :** l'objectif n'est pas de copier la vue, mais d'identifier ce qui facilite ou ralentit une décision.

**Limite :** un titre ou une mise en page ne révèle pas forcément l'intention de son auteur.

## Étape 3 — Lire le bandeau de contexte

1. Repérez la période active et observez-la.
2. Repérez **Filter by** ou la zone des variables.
3. Observez le nom et la valeur de chaque variable visible.
4. Repérez les commandes de pause, actualisation ou sélection temporelle sans les utiliser.

### Pourquoi la période et les variables doivent-elles être lues avant les courbes ?

**Réponse :** elles définissent les données incluses dans la vue.

**Pourquoi :** une même courbe peut produire une interprétation différente sur une heure ou une semaine, ou avec un filtre spécifique.

**Limite :** une variable affichée dans le bandeau n'est pas nécessairement appliquée à chaque widget ; cela doit être vérifié dans la conception ou par un test autorisé.

## Étape 4 — Inventorier les widgets

1. Parcourez le dashboard de haut en bas.
2. Observez le titre et le type apparent de chaque widget visible.
3. Reformulez la question à laquelle il semble répondre.
4. Vérifiez **question ambiguë** lorsque le titre ne suffit pas.

### Quels widgets étaient visibles lors de la vérification ?

**Réponse :** le dashboard **Monitoring EU-Interfaces** présentait notamment **Nombre d'interfaces (cron) uniques**, **Nombre d'exécutions d'interfaces (cron)**, **Nombre de cron en erreur**, **Nombre de CG exécutant des interfaces**, **Exécutions par Interfaces**, **Exécutions par CG**, **Statuts d'Exécution** et **Dernières exécutions d'interfaces**.

**Pourquoi :** cette référence aide à vérifier que vous êtes sur la bonne vue, mais votre inventaire actuel reste la source de vérité.

**Limite :** le titre décrit parfois la donnée sans expliquer la décision ni l'action attendue.

## Étape 5 — Examiner un widget sans l'éditer

1. Choisissez une série temporelle visible.
2. Repérez son titre, sa légende, ses unités et les séries affichées.
4. Observez ce que l'infobulle apporte : horodatage, valeur et série.
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
5. Vérifiez **absent** lorsqu'un niveau n'est pas clair.

### Quel ordre facilite une qualification d'incident ?

**Réponse :** impact, chronologie, périmètre, puis diagnostic.

**Pourquoi :** le lecteur confirme d'abord que le service rendu est affecté, date la rupture, isole la population concernée puis choisit la vue détaillée.

**Limite :** l'absence d'un niveau est un constat de conception, pas un jugement sur tous les usages du dashboard.

## Étape 7 — Examiner la navigation vers le détail

1. Repérez les menus contextuels, liens ou actions de survol d'un widget sans les activer s'ils ouvrent une édition.
2. Identifiez les destinations de lecture proposées : APM, traces, métriques, logs ou plein écran, selon la vue.
3. Observez la destination qui serait utile après une anomalie.

### Un dashboard doit-il contenir tout le diagnostic ?

**Réponse :** non. Il doit qualifier la situation et orienter vers la vue détaillée adaptée.

**Pourquoi :** accumuler toutes les métriques rend la lecture lente et duplique les outils d'investigation spécialisés.

# Partie 2 — Comprendre la navigation depuis un dashboard

## Étape 8 — Examiner la variable réelle

1. Observez la question rendue possible par la variable actuelle.
2. Déterminez si son libellé explique clairement son usage.
3. Observez les dimensions absentes qui seraient utiles pour comparer des déploiements.

### Que permettent les variables observées dans `Monitoring EU-Interfaces` ?

**Réponse :** `managementCenter`, `CmdName` et `version` étaient visibles avec la valeur `*`. Elles permettent de réduire respectivement le centre de gestion, la commande et la version lorsque les widgets les utilisent.

**Pourquoi :** ce filtre peut être pertinent pour isoler des réponses, mais il ne remplace pas `env`, `service` et `version` pour contextualiser un déploiement.

**Limite :** seule l'inspection des requêtes ou un test autorisé permet de confirmer son application à tous les widgets.

## Étape 9 — Passer du dashboard à la page APM

1. Ouvrez **APM Home**.
2. Recherchez `eu-interfaces` dans la liste des services.
3. Ouvrez sa page de service.
4. Repérez les filtres **env** et **version** ainsi que la période.
5. Revenez au dashboard avec l'onglet précédent du navigateur.

### Pourquoi effectuer ce détour par APM ?

**Réponse :** pour vérifier que les dimensions prévues pour le dashboard correspondent à des dimensions de navigation réelles du service.

**Pourquoi :** le module 4 a défini un contrat commun ; le dashboard doit le réutiliser pour faciliter le passage vers APM.

**Limite :** `eu-interfaces` sert à apprendre la navigation et à alimenter un widget agrégé de formation. Ses valeurs ne prouvent aucune relation avec les données Logs ou RUM retenues dans les autres widgets.

# Partie 3 — Construire le dashboard réel de formation

Les widgets suivants utilisent des sources réelles différentes. Ils servent à apprendre la construction, la comparaison et la navigation ; ils ne décrivent pas un même incident et ne doivent pas être corrélés entre eux.

## Étape 10 — Créer le dashboard et saisir son identité

1. Dans **Dashboards > List**, cliquez sur **New Dashboard**.
2. Sur la page **Build a Dashboard with Bits**, n'utilisez ni le champ de génération assistée ni les suggestions.
3. Choisissez **Blank Dashboard — Responsive layout**. Utilisez **Blank Screenboard — Free-form grid** uniquement si le formateur demande explicitement une disposition libre.
4. Lorsque le dashboard vierge s'ouvre, modifiez son titre et saisissez :

```text
[TRAINING] M09 - <identifiant-participant> - <AAAAMMJJ>
```

5. Remplacez les deux valeurs entre chevrons par votre identifiant autorisé et la date du jour.
6. Avant de valider le titre, vérifiez qu'aucun dashboard ne porte déjà ce nom ; ajoutez uniquement le suffixe fourni par le formateur en cas de collision.
7. N'activez aucun partage public.
8. Dans la description du dashboard, saisissez :

```text
Atelier ORSYS Datadog — synthèse des signaux et monitors explorés aux modules 5 à 8.
Ne pas utiliser comme dashboard de production. Nettoyage en fin de formation.
```

9. Validez le titre et la description.
10. Vérifiez que le titre affiché contient bien votre identifiant.

**Réponse expliquée :** le dashboard interroge les données existantes ; il ne modifie ni les logs, ni l'instrumentation APM, ni le RUM. Le préfixe et votre identifiant permettent de reconnaître la ressource à supprimer en fin de formation.
## Étape 11 — Ajouter le cadre de lecture

1. Cliquez sur **Add Widgets**.
2. Ajoutez un widget **Note & Links** ou **Note**.
3. Saisissez le contenu suivant :

```markdown
## Télémétrie explorée pendant la formation

- Logs : scénario `checkout-investigation` du service `training-<participant>-20260720-svc`
- APM : `eu-interfaces`, opération `console`
- RUM : application `peopulse`, agrégats uniquement
- Monitors : monitor `[TRAINING] M08 Log errors - <participant> - 20260720`

Ces sources ne décrivent pas un même incident.
Le scénario Logs sera envoyé au module 10 : ses widgets peuvent être vides pendant leur création.
Le seul scénario pédagogique est `checkout-investigation` ; il sera envoyé au module 10 dans le service du participant.
```

4. N'ajoutez aucun lien externe, identifiant ou donnée personnelle.

### Pourquoi commencer par une note ?

**Réponse :** elle empêche le lecteur de supposer une corrélation entre des sources choisies pour leur valeur pédagogique et non parce qu'elles appartiennent au même parcours.

## Étape 12 — Ajouter les trois widgets Logs

Les trois widgets utilisent la même requête de base :

```text
service:training-<participant>-20260720-svc source:powershell-training @training_scenario:checkout-investigation
```

Le scénario sera envoyé au module 10. Les widgets sont volontairement préparés avant l'arrivée des événements.

### Widget 1 — Activité du scénario

1. Cliquez sur **Add Widgets**, puis choisissez **Timeseries**.
2. Choisissez la source **Log Events** ou **Logs**.
3. Sélectionnez l'agrégation **Count**.
4. Remplacez `<participant>` par l'identifiant utilisé pour votre service pédagogique, puis saisissez directement :

```text
service:training-<participant>-20260720-svc source:powershell-training @training_scenario:checkout-investigation
```

5. Ne regroupez pas par message, utilisateur, hôte ou identifiant.
6. Donnez au widget le titre :

```text
Logs — activité du scénario d'investigation
```

**Question :** quand les événements du scénario sont-ils reçus ?

**Action :** ouvrir le Log Explorer sur la période où une activité apparaît.

### Widget 2 — Nombre d'échecs

1. Ajoutez un widget **Query Value**.
2. Choisissez **Logs**, puis **Count**.
3. Utilisez la requête de base complétée par :

   ```text
   status:error
   ```

4. Donnez au widget le titre :

   ```text
   Logs — échecs du scénario
   ```

5. Conservez une unité de comptage simple.

**Question :** combien d'événements en erreur sont visibles sur la période ?

**Action :** ouvrir les événements en erreur pour vérifier leur message et leurs attributs.

### Widget 3 — Répartition par statut

1. Ajoutez un widget **Pie Chart**, **Top List** ou **Table**, selon les choix proposés.
2. Choisissez **Logs**, puis **Count**.
3. Utilisez la requête de base sans filtre de statut.
4. Ajoutez un regroupement par **Status**.
5. Donnez au widget le titre :

   ```text
   Logs — répartition par statut
   ```

**Question :** quelle part du scénario correspond à `info`, `warn` et `error` ?

**Action :** sélectionner un statut avant d'ouvrir le Log Explorer.

### Que signifie un widget Logs vide ?

**Réponse :** avant l'atelier du module 10, un résultat vide est attendu si le scénario `checkout-investigation` n'a pas encore été envoyé. Après l'envoi, un résultat vide peut provenir de la période, de la rétention ou d'un identifiant participant différent.

**Plan de repli :** conservez le widget avec la description **ce widget sera alimenté par le scénario contrôlé du module 10**. N'élargissez pas la requête à tous les logs de production.

## Étape 13 — Ajouter les trois widgets APM

Les widgets APM utilisent `service:eu-interfaces` et l'opération `console`. Ils restent indépendants du scénario Logs.

### Widget 4 — Activité APM

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

**Question :** une activité APM est-elle visible pour le service et l'opération ?

**Action :** ouvrir le Trace Explorer sur la même période.

### Widget 5 — Erreurs APM

1. Ajoutez un widget **Query Value**.
2. Choisissez **Indexed Spans** ou la source APM équivalente.
3. Sélectionnez `eu-interfaces`, puis l'opération `console` lorsqu'elle est proposée.
4. Filtrez sur le statut **Error** au moyen du sélecteur proposé par l'éditeur.
5. Choisissez **Count**.
6. Donnez au widget le titre :

   ```text
   APM — spans en erreur eu-interfaces / console
   ```

**Question :** combien de spans en erreur sont indexés sur la période ?

**Action :** ouvrir les traces correspondantes uniquement si le compteur est supérieur à zéro.

### Widget 6 — Latence p95 APM

1. Ajoutez un widget **Timeseries**.
2. Choisissez **Indexed Spans** ou la source APM équivalente.
3. Reprenez `eu-interfaces` et l'opération `console`.
4. Sélectionnez la mesure de durée proposée par Datadog.
5. Choisissez l'agrégation **p95**.
6. Conservez l'unité de durée proposée automatiquement.
7. Donnez au widget le titre :

   ```text
   APM — latence p95 eu-interfaces / console
   ```

**Question :** quelle durée est dépassée par les 5 % de spans les plus lents ?

**Action :** ouvrir les traces lentes avant d'attribuer une cause.

### Pourquoi utiliser un volume avant une latence p95 ?

**Réponse :** le faible débit de `eu-interfaces` peut rendre le percentile instable ou vide sur une fenêtre courte. Le widget d'activité permet de vérifier d'abord la taille de l'échantillon. Ne concluez pas sur le p95 lorsque très peu de spans sont disponibles.

## Étape 14 — Ajouter les widgets RUM agrégés

### Widget 7 — Nombre de vues

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

**Question :** combien de vues RUM sont reçues sur la période ?

**Action :** ouvrir le RUM Explorer en restant sur des agrégats.

### Widget 8 — Erreurs frontend

1. Ajoutez un widget **Timeseries** ou **Query Value**.
2. Choisissez la source **RUM**.
3. Sélectionnez le type d'événement **Errors**.
4. Filtrez sur l'application `peopulse`.
5. Choisissez **Count**.
6. Ne regroupez pas par utilisateur, session, URL ou message d'erreur.
7. Donnez au widget le titre :

   ```text
   RUM — erreurs frontend peopulse
   ```

**Question :** combien d'événements d'erreur frontend sont reçus ?

**Action :** ouvrir uniquement la vue agrégée des erreurs ; ne consultez aucune session ni donnée personnelle.


### Pourquoi ne pas afficher les actions ou les URL réelles ?

**Réponse :** la vérification du module 7 a montré que des noms d'actions et URL peuvent contenir du texte métier, des paramètres ou des données personnelles. Le comptage agrégé des vues suffit pour apprendre la source RUM sans exposer ces valeurs.

## Étape 15 — Ajouter le neuvième widget : la synthèse des monitors

1. Cliquez sur **Add Widgets**, puis choisissez **Monitor Summary** ou **Résumé des monitors**.
2. Sélectionnez le type de résumé **Monitor** afin d'afficher une ligne par monitor et son état global.
3. Dans la requête, recherchez uniquement le monitor que vous avez créé au module 8 au moyen de son nom exact :

   ```text
   [TRAINING] M08 Log errors - <participant> - 20260720
   ```

4. Vérifiez que le résultat ne contient qu'un seul monitor et qu'il porte votre identifiant.
5. Choisissez l'affichage **Counts and List** ou **Nombre et liste**.
7. Donnez au widget le titre :

```text
Monitors — état de ma détection pédagogique
```

8. Si la requête retourne d'autres monitors, affinez-la avant d'enregistrer.

### Pourquoi afficher le monitor après les signaux ?

**Réponse :** le widget relie la situation observée à son mécanisme de détection. Il permet de voir l'état du monitor et d'ouvrir son détail, sans remplacer l'analyse des logs, traces et données RUM.

**Limite :** l'état **OK** signifie que la condition configurée n'est pas satisfaite ; il ne garantit pas à lui seul l'absence de problème. Un état **No Data** doit être interprété avec la politique de données manquantes étudiée au module 8.

## Étape 16 — Ajouter la métrique et son monitor

### Widget 10 — Évolution de la file de commandes

1. Ajoutez un widget **Timeseries**.
2. Choisissez la source **Metrics**.
3. Recherchez :

   ```text
   training.checkout.queue_depth
   ```

4. Utilisez l'agrégation **avg**.
5. Filtrez sur :

   ```text
   env:training
   service:training-<participant>-20260720-svc
   participant:<participant>
   ```

6. Ajoutez un marqueur Warning à `8` et un marqueur Alert à `10`.
7. Donnez au widget le titre :

   ```text
   File de commandes — profondeur
   ```

**Question :** quand la file atteint-elle son maximum et revient-elle à un niveau normal ?

**Action :** ouvrir Metrics Explorer sur le pic avant d'examiner les logs du même scénario pédagogique.

### Widget 11 — État du Metric Monitor

1. Ajoutez un widget **Monitor Summary**.
2. Sélectionnez le type de résumé **Monitor**.
3. Recherchez le nom exact :

   ```text
   [TRAINING] M08 Metric Queue - <participant> - 20260720
   ```

4. Choisissez l'affichage **Counts and List**.
5. Donnez au widget le titre :

   ```text
   File de commandes — état de l'alerte
   ```

**Résultat attendu :** la courbe montre la séquence normale du module 5, le pic `15` et le retour à `3` du module 8. Le Monitor Summary présente l'état courant du Metric Monitor.

**Interprétation :** le widget Timeseries montre le signal ; le Monitor Summary montre le résultat de la règle de détection.

## Étape 17 — Organiser et enregistrer le dashboard

1. Placez la note en haut du dashboard.
2. Placez les deux Monitor Summary immédiatement sous la note de contexte.
3. Créez ensuite trois zones de lecture au moyen de groupes ou de titres de section : **Expérience utilisateur**, **Comportement applicatif** et **Diagnostic technique**.
4. Placez les widgets RUM dans **Expérience utilisateur**.
5. Placez l'activité, les erreurs et la latence APM dans **Comportement applicatif**.
6. Placez la métrique de file, l'activité, les échecs et les statuts Logs dans **Diagnostic technique**.
7. Choisissez une période commune permettant d'afficher des données, sans dépasser **Past 1 Day** pour la recette.
8. Vérifiez que les titres ne contiennent aucune valeur sensible.
9. Ajoutez une variable `env` uniquement si elle est proposée et applicable aux widgets concernés ; conservez `*` par défaut.
10. N'ajoutez pas de variable `service` globale : les widgets ciblent volontairement des périmètres différents.
11. Vérifiez que le dashboard est enregistré et que son titre est exact.

| Contrôle | Résultat attendu |
|---|---|
| Nom unique et préfixé | conforme |
| Note de non-corrélation | visible |
| Trois widgets Logs | enregistrés, donnée ou absence expliquée |
| Métrique pédagogique | courbe filtrée sur le service et le participant |
| Trois widgets APM | enregistrés, périmètre `eu-interfaces` / `console` |
| Deux widgets RUM | agrégés, sans action, URL ou utilisateur |
| Deux widgets Monitors | limités aux deux monitors `[TRAINING] M08` du participant |
| Partage public | désactivé |

### Pourquoi limiter le dashboard à onze widgets obligatoires ?

**Réponse :** le dashboard couvre le volume, l'erreur, la performance et la détection sans dupliquer les explorateurs. Chaque widget doit conduire à une question et à une action ; les détails événementiels restent dans Logs, APM, RUM ou le monitor.

# Partie 4 — Exploiter le dashboard sans inventer de corrélation

## Étape 18 — Comparer les comportements des widgets

1. Placez le dashboard sur **Past 1 Hour**.
2. Observez quels widgets contiennent des données.
3. Étendez à **Past 4 Hours**, puis **Past 1 Day** si nécessaire.
4. Pour chaque widget, observez la source, le filtre, la période minimale utile et la question à laquelle il répond.

| Widget | Source réelle | Question autorisée |
|---|---|---|
| Logs — activité du scénario d'investigation | requête préparée au module 9, logs envoyés au module 10 | Les événements du scénario sont-ils indexés sur la période ? |
| Logs — échecs du scénario | même scénario, filtre `status:error` | Combien d'échecs sont visibles ? |
| Logs — répartition par statut | même scénario, regroupement par Status | Comment se répartissent les statuts ? |
| APM — activité eu-interfaces / console | APM `service:eu-interfaces`, opération `console` | Une activité APM est-elle visible pour cette opération ? |
| APM — spans en erreur | Indexed Spans filtrés sur Error | Combien de spans en erreur sont indexés ? |
| APM — latence p95 | durée des spans indexés | Quelle est la latence p95 lorsque l'échantillon est suffisant ? |
| RUM — vues peopulse | vues RUM de l'application `peopulse` | Des vues RUM agrégées sont-elles reçues sur la période ? |
| RUM — erreurs frontend peopulse | erreurs RUM agrégées | Combien d'erreurs frontend sont reçues ? |
| Monitors — état de ma détection pédagogique | monitor créé au module 8 | Quel est l'état actuel du monitor testé ? |

**Réponse expliquée :** les périodes de disponibilité peuvent différer en raison du trafic, de la rétention, de l'échantillonnage et de la fenêtre d'évaluation du monitor. Une variation simultanée ne suffit pas à relier ces sources.

## Étape 19 — Tester les filtres sans perdre le contexte

1. Modifiez temporairement la période globale.
2. Si la variable `env` existe, testez une valeur réellement proposée par Datadog puis revenez à `*`.
3. Vérifiez que chaque widget reste compréhensible lorsqu'il est vide.
4. N'ajoutez pas de variable globale `service` : elle masquerait une partie des sources.

**Résultat attendu :** le dashboard reste lisible sans suggérer que le service pédagogique, `eu-interfaces` et `peopulse` appartiennent à une même chaîne de service.

## Étape 20 — Examiner une requête en mode édition

Pour chacun des onze widgets obligatoires :

1. Ouvrez son mode d'édition.
2. Identifiez la source, l'agrégation, les filtres et l'unité.
3. Vérifiez que le titre cite explicitement la source réelle.
4. Fermez ou annulez sans ajouter de nouvelle source.

**Interprétation :** un widget fiable rend visibles sa portée et ses limites. Les titres génériques tels que « erreurs » ou « trafic » sont insuffisants dans un environnement partagé.

## Étape 21 — Utiliser les pivots de navigation

1. Depuis un widget Logs, ouvrez le détail dans le Log Explorer si le lien est proposé.
2. Vérifiez que la période et la requête du scénario sont transmises.
3. Depuis un widget APM, ouvrez le Trace Explorer ou le service si le lien est proposé.
4. Vérifiez `eu-interfaces`, l'opération `console` et la période.
5. Pour le RUM, consultez les agrégats.
6. Depuis chacun des widgets Monitors, ouvrez le détail du monitor `[TRAINING] M08` correspondant, puis revenez au dashboard.
7. Depuis la métrique de file, ouvrez Metrics Explorer et conservez les mêmes tags et la même période.

**Réponse expliquée :** le dashboard sert de point d'entrée. Le diagnostic détaillé se poursuit dans l'explorateur correspondant, avec les mêmes filtres et la même période lorsque Datadog sait les transmettre.

# Partie 5 — Recette et nettoyage

## Étape 22 — Effectuer la recette fonctionnelle

Vérifiez les points suivants :

- le nom commence par `[TRAINING] M09` et contient votre identifiant et la date ;
- la note supérieure indique que les sources ne représentent pas un incident commun ;
- les widgets utilisent le service pédagogique du participant, `eu-interfaces`, `peopulse` et les deux monitors `[TRAINING] M08` ;
- les trois widgets Logs partagent exactement la requête de base du scénario ;
- les trois widgets APM restent limités à `eu-interfaces` et `console` ;
- les deux widgets RUM sont agrégés et n'exposent aucune session, action ou URL ;
- la métrique `training.checkout.queue_depth` est filtrée sur le service et le participant ;
- aucune donnée nominative, URL détaillée ou action RUM sensible n'est affichée ;
- aucun partage public ni notification n'est configuré ;
- les deux widgets Monitor Summary montrent uniquement vos monitors `[TRAINING] M08` ;
- aucun dashboard existant n'a été modifié.

## Étape 23 — Restituer sans surinterpréter

Présentez le dashboard en répondant aux questions suivantes :

1. Quels widgets distinguent activité, erreurs et statuts des logs ?
2. Quels widgets distinguent activité, erreurs et latence APM ?
3. Quels widgets qualifient l'activité et les erreurs RUM sans exposer les utilisateurs ?
4. Quels widgets permettent de lire l'état des deux monitors pédagogiques ?
5. Quelle différence existe entre la courbe de file et l'état du Metric Monitor ?
5. Quel pivot ouvre l'explorateur adapté à chaque signal ?

**Réponse de référence :** chaque groupe de widgets qualifie une dimension différente avant d'orienter vers l'explorateur adapté. Le dashboard est un support de synthèse et de navigation, pas la preuve d'un incident transversal.

## Questions de synthèse

### Peut-on conclure que les logs pédagogiques sont liés à `peopulse` ?

**Réponse :** non. Le nom du service pédagogique est appliqué aux logs de formation ; aucun identifiant de corrélation ni aucune relation avec l'application RUM n'a été validé.

### Pourquoi conserver néanmoins plusieurs sources sur le même dashboard ?

**Réponse :** pour apprendre à sélectionner plusieurs types de données, configurer des widgets et comprendre leurs différences dans l'interface Datadog.

### Que faut-il faire avant de construire un dashboard d'incident corrélé ?

**Réponse :** choisir un service réel disposant de signaux reliés par des attributs vérifiés, puis confirmer la chaîne de corrélation dans les explorateurs avant de l'afficher.

## Références officielles

- [Dashboards](https://docs.datadoghq.com/dashboards/)
- [Widgets](https://docs.datadoghq.com/dashboards/widgets/)
- [Timeseries Widget](https://docs.datadoghq.com/dashboards/widgets/timeseries/)
- [Template Variables](https://docs.datadoghq.com/dashboards/template_variables/)
- [Widget Configuration](https://docs.datadoghq.com/dashboards/widgets/configuration/)
- [Monitor Summary Widget](https://docs.datadoghq.com/dashboards/widgets/monitor_summary/)
- [Log Analytics](https://docs.datadoghq.com/logs/explorer/analytics/)
