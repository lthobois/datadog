---
title: "Module 7 — Atelier : naviguer dans le RUM réel en sécurité"
subtitle: "Document participant autonome"
lang: fr-FR
---

# Relier une expérience RUM au diagnostic backend

## Objectif

Parcourir l'organisation RUM réellement disponible en lecture seule, comprendre les types d'événements et qualifier les pivots accessibles sans exposer de donnée sensible.

## Livrable

Vous produisez :

- un relevé daté de l'organisation RUM visible ;
- un relevé des types d'événements et facettes utilisables en sécurité ;
- une analyse des risques liés aux actions, URL et sessions ;
- un constat sur le pivot éventuel vers l'APM ;
- une conclusion séparant constat réel, hypothèse et limite ;
- une checklist de confidentialité.

## Place dans la progression

Au module 6, vous avez étudié `eu-interfaces`, dont l'opération `console` ne correspond pas à une requête issue d'un navigateur. Cet atelier ne force donc aucune relation entre ce service et le RUM. Il recherche un service web corrélable uniquement si l'interface en fournit la preuve.

Les observations servent ensuite à :

- le module 9, qui donnera directement les paramètres du widget RUM agrégé ;
- le module 10, avec un chemin d'investigation allant de l'impact utilisateur à une ressource puis, si disponible, à une trace backend et à ses logs.

## Règle de sécurité

Travaillez uniquement en lecture. Ne créez ni application, vue sauvegardée, mesure, funnel, monitor, dashboard ou configuration. Ne cliquez pas sur un bouton d'enregistrement ou de modification. N'ouvrez aucun Session Replay réel non explicitement autorisé. Ne recopiez aucun nom d'application sensible, nom, email, identifiant d'application ou de session, token client, URL détaillée, saisie ou contenu utilisateur réel.

## Prérequis

- Chrome connecté à Datadog ;
- accès en lecture à **Digital Experience** et au **RUM Explorer**, ou captures anonymisées ;
- application RUM réelle `peopulse` ;
- vocabulaire `env/service/version` ;
- accès en lecture aux agrégats RUM autorisés.
- constat de disponibilité des pivots APM réalisé au module 6.

## Comment utiliser ce document

Effectuez d'abord l'observation demandée, puis lisez la réponse qui suit la question. Les applications, sessions et volumes évoluent avec la période, le consentement et l'échantillonnage : votre relevé daté prévaut. Toutes les étapes utilisent uniquement les agrégats réels de `peopulse`.

## Contexte réel vérifié sur la plateforme

Le parcours a été vérifié le 21 juillet 2026 sur l'application Browser JavaScript autorisée `peopulse` :

| Élément | Implémentation observée |
|---|---|
| Accès depuis le menu | **Digital Experience** ouvre d'abord Synthetic Monitoring |
| Accès direct recommandé | **Search Datadog > Real User Monitoring** ou **Explorer > RUM** |
| Application | `peopulse`, événements reçus par le Browser SDK |
| Navigation RUM | **Summary**, **Optimization**, **Feature Flag Tracking**, **Profiling**, **Session Replay**, **Explorer**, **Error Tracking**, **Product Analytics** |
| Filtres Summary | `env`, `service`, `version`, pays et navigateur |
| Types d'événements Explorer | **Sessions**, **Views**, **Actions**, **Errors**, **Resources**, **Long tasks**, **Vitals** ; **Vitals** ne retournait aucun événement sur la période vérifiée |
| Période affichée lors du contrôle | **Past 1 Week** |
| Mode de recherche de l'Explorer | **Advanced Search** à l'ouverture ; **Simple Search** reste disponible et doit être sélectionné pour l'atelier |
| Pivot RUM vers APM | aucun lien **View Trace** sûr n'était visible dans la liste agrégée des ressources ; corrélation non démontrée |

L'application contient un volume important de sessions, vues, actions et ressources. Les nombres changent en continu et ne sont pas des résultats attendus. La page **SDK Configuration** confirme que des événements sont reçus, mais elle peut afficher des identifiants techniques nécessaires à l'installation : ne copiez ni identifiant, ni token, ni extrait d'initialisation.


# Partie 1 — Comprendre l'organisation du RUM

## Étape 1 — Ouvrir le RUM depuis la recherche Datadog

1. Cliquez sur **Search Datadog** ou utilisez `Ctrl+K`.
2. Dans la liste des produits, ouvrez **Real User Monitoring** sous **Digital Experience**.
3. Si vous souhaitez aller directement aux événements, l'entrée **Explorer** sous **RUM** est également disponible.
4. Ne choisissez pas **Create RUM Application**.
5. Vérifiez que l'application sélectionnée est `peopulse`.
6. Notez la date, l'heure, la période et les onglets visibles.

| Élément | Observation |
|---|---|
| Date et heure |  |
| Modules visibles |  |
| Application sélectionnée | `peopulse` |

### Quel est le rôle du RUM ?

**Réponse :** le Real User Monitoring collecte depuis le navigateur ou l'application mobile des signaux sur les parcours, interactions, ressources, erreurs et performances réellement rencontrés.

**Pourquoi :** il qualifie l'impact utilisateur et les populations affectées, tandis que l'APM explique le traitement backend lorsque la corrélation existe.

## Étape 2 — Vérifier l'application autorisée

1. Vérifiez que l'en-tête indique l'application `peopulse`.
2. Si vous arrivez sur **Application Management**, ouvrez uniquement `peopulse`.
3. Vérifiez son type **JavaScript** et la présence du message **Events received**, sans développer les étapes d'installation.
4. Ouvrez **Real User Monitoring — Optimize app performance**.
5. Ne cliquez pas sur **Edit Application** et ne copiez ni `applicationId`, ni `clientToken`, ni extrait d'initialisation.

### Quel était l'état observé lors de la conception ?

**Réponse :** l'application `peopulse` utilise le Browser SDK, envoie des événements et donne accès au RUM ainsi qu'à Product Analytics.

**Limite :** ce constat décrit l'état de la plateforme pendant la vérification ; il ne garantit ni la configuration complète du SDK ni la représentativité des données.

## Étape 3 — Parcourir Summary

1. Ouvrez **Summary** ou la page de synthèse de l'application autorisée.
2. Repérez les onglets **Optimization**, **Feature Flag Tracking**, **Profiling**, **Session Replay**, **Explorer**, **Error Tracking** et **Product Analytics**.
3. Repérez la période et les filtres `env`, `service`, `version`, pays et navigateur.
4. Dans la navigation de la page, repérez **Overview**, **Optimize Vitals**, **Frontend Errors**, **Deployments** et **Resources**.
5. Observez uniquement les agrégats de vues, performance et erreurs. Ne changez pas la configuration de l'application.

| Élément | Observation non sensible |
|---|---|
| Type d'application | Browser JavaScript |
| Période |  |
| Vues visibles |  |
| Session Replay disponible | oui |
| Product Analytics disponible | oui |
| Core Web Vitals visibles | oui / non observé |

### Pourquoi commencer par une synthèse agrégée ?

**Réponse :** elle permet d'estimer fréquence, tendance et périmètre avant d'examiner un événement ou une session individuelle.

**Pourquoi :** une session spectaculaire illustre un cas mais ne mesure jamais seule l'ampleur.

## Étape 4 — Ouvrir le RUM Explorer

1. Dans l'en-tête RUM, ouvrez **Explorer**.
2. Repérez **Views**, **My View**, le sélecteur de type d'événement et les modes **Simple Search** et **Advanced Search**.
3. Conservez **Simple Search** pour l'atelier.
4. Repérez les visualisations disponibles : **List**, **Timeseries**, **Top List**, **Bar Chart**, **Table**, **Distribution**, **Geomap**, **Funnel**, **Tree Map** et **Pie Chart**. Certaines options dépendent du type d'événement.
5. Vérifiez qu'aucun filtre **User Email** ou identifiant utilisateur n'est appliqué.
6. Ne sauvegardez aucune vue et n'utilisez pas **Open in Sheets**.

### À quoi sert le RUM Explorer ?

**Réponse :** il permet de rechercher et segmenter les événements RUM par application, vue, action, ressource, erreur, contexte technique et autres attributs autorisés.

## Étape 5 — Associer les objets RUM aux questions

| Objet | Question de référence |
|---|---|
| Session | quel parcours global a été vécu ? |
| Vue | dans quelle page ou quel état de navigation ? |
| Action | quelle interaction utilisateur a eu lieu ? |
| Ressource | quel appel réseau ou chargement a été effectué ? |
| Erreur | quelle erreur frontend a été collectée ? |
| Tâche longue | le thread principal a-t-il bloqué l'interface ? |

### Comment ces objets sont-ils reliés ?

**Réponse :** une session contient des vues ; les actions, ressources, erreurs et tâches longues héritent du contexte de leur vue.

## Étape 6 — Examiner les facettes

1. Repérez les filtres proposés pour le type courant. Pour **Sessions**, l'interface présente notamment `Env`, **User Email**, **Initial View Name**, **Session Type** et **Error Count**.
2. Dépliez une facette sans sélectionner de valeur sensible.
3. Ne dépliez pas **User Email**.
4. Notez quelles dimensions non nominatives permettraient de comparer des populations ou versions.

### Pourquoi une facette utilisateur nominative n'est-elle pas nécessaire ici ?

**Réponse :** la question porte sur des catégories et un parcours, pas sur l'identité d'une personne.

**Pourquoi :** des dimensions non nominatives réellement proposées, comme `env`, `service`, `version`, le pays ou le navigateur, suffisent pour qualifier de nombreux périmètres avec moins de risque de confidentialité.

## Étape 7 — Examiner la liste des sessions sans ouvrir de replay

1. Sélectionnez le type **Sessions** si la vue le permet.
2. Repérez durée, nombre de vues, erreurs, actions, frustration, vue initiale, vue finale et disponibilité du replay.
3. Ne recopiez aucun identifiant ou attribut nominatif.
4. N'ouvrez pas le replay.

### Quels critères rendent une session intéressante pour une analyse ciblée ?

**Réponse :** vue concernée, action ou erreur présente, durée cohérente, segment pertinent et replay disponible et autorisé.

## Étape 8 — Examiner les vues

1. Sélectionnez le type **Views**.
2. Repérez les noms de vues, durées, erreurs, actions et ressources associées.
3. Identifiez une vue générique de parcours sans recopier une URL détaillée.

### Qu'est-ce qu'une vue ?

**Réponse :** une page ou un état de navigation suivi pendant une durée logique, auquel sont reliés les autres événements RUM.

**Limite :** chaque clic ou ouverture de composant ne mérite pas une nouvelle vue.

## Étape 9 — Examiner les actions

1. Sélectionnez le type **Actions**.
2. Repérez uniquement les colonnes **Action Type**, **Action Name**, **Action Frustration Type** et **View Name**.
3. Ne lisez pas les valeurs d'**Action Name** à voix haute et ne les copiez pas : la vérification a montré que certaines reprennent du texte contenant des informations personnelles ou métier.
4. Constatez simplement si les noms sont techniques, génériques ou dépendants du texte de l'interface.
5. N'ouvrez aucun détail d'action réel.

### Pourquoi une action automatique peut-elle être mal nommée sur une modale legacy ?

**Réponse :** son nom peut dépendre du texte, d'un label, d'un attribut d'accessibilité ou du DOM, éléments qui peuvent changer avec la langue ou l'implémentation.

**Pourquoi :** une action personnalisée nomme directement l'intention métier et reste plus stable.

## Étape 10 — Examiner les ressources

1. Sélectionnez le type **Resources**.
2. Repérez les colonnes **View Name**, **Resource Url**, **Duration**, **Size**, **Status Code** et **Type**.
3. Utilisez le filtre **Resource Type** pour isoler `xhr` ou `fetch` si nécessaire.
4. Ne copiez aucune URL : la plateforme contient des chemins paramétrés, identifiants et chaînes de requête.
5. Si une ressource générique et manifestement non sensible peut être ouverte, recherchez **View Trace**, **Trace**, **APM** ou un accès équivalent sans recopier d'identifiant.
6. Si aucun lien **View Trace** sûr n'est visible dans la liste, notez **pivot APM non vérifiable en sécurité sur les données réelles** et n'inventez aucune ressource de remplacement.

### Que représente une ressource RUM ?

**Réponse :** un chargement ou appel réseau, notamment XHR ou Fetch, observé depuis le frontend.

**Pourquoi :** lorsqu'elle existe réellement et sans donnée sensible, une ressource RUM peut constituer un pivot vers une trace backend. Ce lien n'est pas supposé pour `peopulse`.

**Contexte du module 6 :** ne recherchez pas `eu-interfaces` comme cible de cette corrélation. Son opération `console` n'est pas une requête web déclenchée par une ressource navigateur.

## Étape 11 — Examiner erreurs et tâches longues

1. Sélectionnez successivement **Errors**, **Long tasks** puis **Vitals**.
2. Repérez les agrégats sans ouvrir de contenu sensible.
3. Si **Vitals** affiche zéro événement, notez **aucun événement Vital observé sur la période** ; le type reste néanmoins disponible dans l'Explorer.
4. Notez la question que chaque type permet de tester.

### Quelle différence existe entre une erreur, une tâche longue et un Vital ?

**Réponse :** une erreur décrit une exception ou un échec frontend collecté ; une tâche longue indique un blocage prolongé du thread principal ; un événement Vital porte une mesure d'expérience telle que LCP, CLS ou INP.

**Limite :** ni l'une ni l'autre ne prouve automatiquement que la requête backend a échoué.

# Partie 2 — Exploiter les agrégats réels en sécurité

## Étape 12 — Comparer les types d'événements

1. Dans RUM Explorer, sélectionnez successivement **Sessions**, **Views**, **Actions**, **Errors**, **Resources**, **Long tasks** et **Vitals**.
2. Pour chaque type, notez uniquement le volume agrégé et les facettes non sensibles disponibles.
3. Ne lisez pas à voix haute et ne recopiez pas les noms d'actions, URL, identifiants ou contenus métier.

## Étape 13 — Étudier les vues

1. Revenez sur **Views**.
2. Filtrez l'application `peopulse` avec le sélecteur proposé.
3. Comparez deux périodes.
4. Relevez les Web Vitals agrégés disponibles.

**Réponse expliquée :** une vue représente une page ou un état de navigation suivi par le SDK. Les volumes et performances varient avec le trafic et l'échantillonnage.

## Étape 14 — Étudier les actions sans exposer leur contenu

1. Ouvrez **Actions**.
2. Observez la structure des colonnes et les agrégations.
3. Notez les risques de confidentialité constatés au niveau des noms automatiques.

## Étape 15 — Étudier les ressources

1. Ouvrez **Resources**.
2. Utilisez des facettes agrégées telles que type, statut ou domaine si elles sont disponibles et autorisées.
3. Ne copiez aucune URL contenant un identifiant ou une chaîne de requête.
4. Vérifiez si un pivot APM est proposé sans l'ouvrir sur une donnée sensible.

## Questions de synthèse

### Peut-on ouvrir un Session Replay pendant cet atelier ?

**Réponse :** non. L'environnement est partagé et peut contenir des données personnelles ou métier.

### Peut-on conclure que `peopulse` appelle `eu-interfaces` ?

**Réponse :** non, sauf si un pivot technique réel et sûr le démontre. Cette relation n'a pas été validée.

### Quelle donnée RUM peut être utilisée sans exposer de détail sensible ?

**Réponse :** le nombre agrégé de vues de l'application réelle `peopulse`, sans regroupement par utilisateur, action, URL ou session. La saisie correspondante sera donnée directement lors de la création du widget.
