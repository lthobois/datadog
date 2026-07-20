---
title: "Module 2 — Atelier : services, tags et environnements"
subtitle: "Document participant autonome"
lang: fr-FR
---

# Auditer les services et définir une convention de tags

## Objectif

Parcourir les vues réelles de Datadog pour comprendre comment `service`, `env`, `version` et les autres tags organisent la télémétrie, puis produire une convention réutilisable dans les ateliers suivants.

## Livrable

Vous produisez une convention d'une page comprenant :

- la définition et les règles de nommage d'un service ;
- les valeurs autorisées pour `env`, `service`, `version` et `team` ;
- le classement des attributs techniques et métier ;
- une estimation de cardinalité ;
- trois améliorations fondées sur des observations Datadog.

## Règle de sécurité

Travaillez uniquement en lecture. N'utilisez aucune commande **Edit**, **Configure**, **Create**, **Declare**, **Service Config** ou **Settings**. Ne modifiez aucun service, tag, monitor, SLO, dashboard ou paramètre.

## Prérequis

- Chrome connecté à l'organisation Datadog ;
- accès en lecture à **Developer Portal** et **APM** ;
- un document dans lequel consigner vos observations et votre convention.

## Comment utiliser ce document

Chaque question est immédiatement suivie d'une réponse de référence. Commencez par observer l'interface et écrire votre réponse, puis comparez-la à la référence. Les nombres et les valeurs visibles dans Datadog évoluent : votre relevé daté prévaut sur l'exemple historique.

# Partie 1 — Comprendre le catalogue réel

## Étape 1 — Ouvrir le catalogue des services

1. Dans le menu gauche, ouvrez **Software Delivery**, puis **Developer Portal**. Selon la configuration de l'interface, **Developer Portal** peut aussi apparaître directement dans le menu.
2. Ouvrez l'onglet **Catalog**.
3. Dans la colonne des types d'entités, sélectionnez **Services**.
4. Repérez la recherche, les filtres et le filtre **Env**.
5. Sélectionnez `*` pour observer tous les environnements, sans enregistrer de vue.
6. Repérez le nombre de services, le nombre d'environnements et la période indiquée.

| Information | Valeur observée |
|---|---|
| Date et heure du relevé |  |
| Nombre de services |  |
| Nombre d'environnements |  |
| Période |  |

### Pourquoi commencer par le catalogue ?

**Réponse :** le catalogue fournit une entrée orientée entités : il rassemble les services connus et leur contexte avant d'ouvrir leurs signaux détaillés.

**Pourquoi :** il aide à raisonner en capacités exploitables et en ownership, alors qu'une liste de traces ou de métriques commence par des données techniques.

**Limite :** un service affiché peut être déclaré, inféré depuis la télémétrie ou imparfaitement qualifié. Sa présence ne prouve pas à elle seule qu'il correspond à un service métier gouverné.

## Étape 2 — Examiner les colonnes et les filtres

1. Parcourez les en-têtes du tableau sans ouvrir de commande d'édition.
2. Repérez les informations disponibles : nom, type, environnement, équipe ou autres métadonnées selon vos droits.
3. Utilisez la recherche pour retrouver `app-api`.
4. Effacez ensuite la recherche afin de revenir à la liste complète.
5. Ouvrez le filtre **Env**, observez ses valeurs, puis revenez à `*`.

### Que démontre le filtre `Env` ?

**Réponse :** l'environnement est une dimension indépendante du nom du service et peut servir à filtrer une même identité logique.

**Pourquoi :** conserver un nom stable permet de comparer `dev`, `staging` et `prod` sans créer artificiellement plusieurs services.

**Limite :** la liste des valeurs reflète les données et la période consultées. Une valeur absente de la liste n'est pas la preuve que l'environnement n'existe jamais.

## Étape 3 — Relever huit entités représentatives

1. Recherchez `app-api`.
2. Relevez deux composants parmi `phpredis`, `pdo`, `mysqli`, `curl`, `guzzle` et `memcached`, s'ils sont présents.
3. Relevez deux noms terminés par `.php`, s'ils sont présents.
4. Relevez un nom générique comme `console`, `symfony` ou `web.request`, s'il est présent.
5. Complétez avec deux autres entités.
6. Pour chaque ligne, notez le nom, l'environnement et une classification provisoire.
7. Écrivez **hypothèse** lorsque la vue ne permet pas de prouver la classification.

| Entité | Env affiché | Classification provisoire | Fait ou hypothèse ? |
|---|---|---|---|
| `app-api` |  | service applicatif probable | hypothèse à confirmer |
|  |  |  |  |
|  |  |  |  |
|  |  |  |  |
|  |  |  |  |
|  |  |  |  |
|  |  |  |  |
|  |  |  |  |

### Peut-on décider qu'une entité est un service métier à partir de son seul nom ?

**Réponse :** non. Un nom comme `pdo` ou `curl` évoque une dépendance technique, mais il faut confirmer sa provenance, son périmètre et son propriétaire.

**Pourquoi :** Datadog peut faire apparaître des composants détectés automatiquement. Ils sont utiles au diagnostic sans nécessairement constituer des capacités métier à gouverner.

**Limite :** ne proposez aucun renommage réel sans validation de l'équipe propriétaire.

## Étape 4 — Analyser `env:none`

1. Parcourez le catalogue ou utilisez le filtre **Env** pour rechercher `none`, si cette valeur est proposée.
2. Relevez deux entités affichant `env:none`. Si aucune n'est visible, écrivez **non observé dans la vue et la période consultées**.
3. Notez la conséquence opérationnelle possible.

| Entité | Observation | Conséquence opérationnelle |
|---|---|---|
|  |  |  |
|  |  |  |

### `env:none` indique-t-il une panne ?

**Réponse :** non. Il indique que le contexte d'environnement n'est pas disponible ou pas exploitable dans cette vue.

**Pourquoi :** le service peut fonctionner tout en étant difficile à filtrer, comparer et corréler avec les autres signaux.

**Limite :** l'absence dans cette vue ne révèle ni la cause ni la source fautive. Il faudrait ensuite auditer la configuration de déploiement, l'Agent et l'instrumentation, ce qui reste hors du périmètre en lecture seule.

## Étape 5 — Identifier un service multi-environnement

1. Recherchez une ligne dont l'environnement est suivi de `+1`, `+4` ou d'une autre valeur positive.
2. Cliquez sur ce badge pour afficher les différentes valeurs `env:`.
3. Notez le service, les environnements visibles et la présence éventuelle d'une version ou d'un environnement dans son nom.

| Élément | Observation |
|---|---|
| Service |  |
| Environnements visibles |  |
| Environnement présent dans le nom ? |  |
| Version présente dans le nom ? |  |

### Pourquoi le nom doit-il rester identique entre les environnements ?

**Réponse :** parce que le nom représente la capacité stable ; `env` et `version` décrivent ses déploiements.

**Pourquoi :** cette séparation rend possibles les comparaisons et les dashboards communs.

**Limite :** deux applications réellement différentes ne doivent pas être fusionnées sous un même nom uniquement pour simplifier l'affichage.

## Étape 6 — Relever trois ambiguïtés de nommage

1. Choisissez un nom de technologie ou de bibliothèque.
2. Choisissez un nom de script ou de fichier.
3. Choisissez un nom trop générique.
4. Pour chacun, formulez la question à poser au propriétaire avant toute correction.

| Nom observé | Ambiguïté | Question préalable |
|---|---|---|
|  | technologie ou bibliothèque | Cette entité est-elle gouvernée comme un service ou seulement détectée comme dépendance ? |
|  | script ou fichier | Quelle capacité stable ce traitement rend-il ? |
|  | nom générique | Quel domaine, périmètre et owner ce nom recouvre-t-il ? |

### Quelle correction peut-on appliquer immédiatement ?

**Réponse :** aucune dans cet atelier. On documente l'ambiguïté et on prépare une proposition à valider.

**Pourquoi :** un renommage peut fragmenter l'historique, les recherches, les dashboards et les monitors.

# Partie 2 — Naviguer dans la page APM de `app-api`

## Étape 7 — Passer du catalogue à la page de service

1. Recherchez `app-api` dans le catalogue.
2. Ouvrez sa **Service Page**.
3. Vérifiez que le nom du service est `app-api`.
4. Repérez les onglets ou sections disponibles : **Service Summary**, **Resources**, **Traces**, **Deployments**, **Errors** ou équivalents selon vos droits.
5. Repérez les sélecteurs **operation**, **env**, **version** et la période.
6. Ne changez pas les valeurs ; observez seulement les choix proposés.

### À quoi servent `operation`, `env` et `version` ?

**Réponse :** `operation` choisit le type d'activité analysé, `env` le contexte de déploiement et `version` la révision logicielle.

**Pourquoi :** ces dimensions permettent d'isoler un comportement sans changer l'identité du service.

**Limite :** une valeur disponible dans un sélecteur dépend des traces présentes sur la période choisie.

## Étape 8 — Examiner les déploiements

1. Ouvrez la section ou l'onglet **Deployments**.
2. Relevez la version visible, **First Seen**, **Last Seen** ou le statut disponible.
3. Revenez à **Service Summary** en utilisant la navigation interne de la page, pas le bouton d'édition.

| Dimension | Valeur ou état observé |
|---|---|
| `service` |  |
| `env` |  |
| `version` |  |
| First Seen |  |
| Last Seen ou statut |  |

### Quelle réponse de référence comparer à votre observation ?

**Réponse :** lors de la conception, le service était `app-api`, plusieurs environnements étaient disponibles et la version `1.0.0` était visible.

**Pourquoi :** la version permet notamment de relier une variation de comportement à un déploiement.

**Limite :** ces valeurs historiques ne sont pas un résultat imposé. Notez les valeurs actuelles ou **non observé dans la vue et la période consultées**.

## Étape 9 — Explorer les ressources du service

1. Ouvrez **Resources** ou la section équivalente.
2. Repérez une ressource ou une opération, par exemple une route HTTP.
3. Notez son nom sans l'interpréter comme un nouveau service.
4. Revenez à **Service Summary**.

### Une route comme `POST /api/orders/validate` est-elle un service ?

**Réponse :** non. C'est une ressource ou une opération exécutée par un service.

**Pourquoi :** le service est la capacité stable ; les routes décrivent les unités de travail que l'on analyse à l'intérieur de cette capacité.

## Étape 10 — Examiner exploitation et gouvernance

1. Dans **Service Summary**, repérez **Service Health Monitors**.
2. Notez ce qui est affiché.
3. Repérez **SLOs** et notez ce qui est affiché.
4. Cherchez l'équipe propriétaire ou **Owner**, sans ouvrir de commande de configuration.

| Élément | Observation |
|---|---|
| Service Health Monitors |  |
| SLOs |  |
| Owner |  |

### Que sont les Service Health Monitors et pourquoi peuvent-ils ne pas être configurés ?

**Réponse :** ce sont des monitors associés à la santé du service afin de rendre ses alertes visibles depuis son contexte. Lors de la conception, aucun n'était visible pour `app-api`.

**Pourquoi :** ils rapprochent télémétrie et réaction opérationnelle. Leur absence visible peut signifier qu'ils ne sont pas configurés, pas associés au service, hors droits ou hors contexte de la vue.

**Limite :** écrivez **aucun visible dans la vue consultée**, et non **aucun monitor n'existe**.

### Que sont les SLO et pourquoi peuvent-ils ne pas être configurés ?

**Réponse :** un SLO formalise un objectif mesurable de fiabilité, souvent calculé à partir d'indicateurs de disponibilité ou de latence. Aucun SLO n'était visible pour `app-api` lors de la conception.

**Pourquoi :** un SLO relie la santé technique à un engagement explicite et facilite le pilotage de la fiabilité. Il peut être absent si le service n'a pas encore d'objectif formalisé, si le SLO n'est pas lié au service ou si les droits le masquent.

**Limite :** l'absence visible ne prouve pas l'absence de tout objectif ou dispositif équivalent ailleurs.

### Pourquoi un owner est-il utile ?

**Réponse :** il indique qui qualifie le service, valide sa convention et prend en charge les actions opérationnelles.

**Pourquoi :** un signal sans responsabilité claire ralentit le diagnostic et laisse les incohérences de tags sans arbitre.

# Partie 3 — Concevoir la convention

## Étape 11 — Distinguer service, ressource, instance et dépendance

Complétez la justification, puis comparez-la à la réponse.

| Élément | Classification de référence | Votre justification |
|---|---|---|
| `app-api` | service stable probable |  |
| `POST /api/orders/validate` | ressource ou opération |  |
| `app-api-7b9f4c6d8-x2k9p` | instance ou pod éphémère |  |
| `pdo` | dépendance technique à qualifier |  |
| `curl` | dépendance technique à qualifier |  |

### Pourquoi un pod ne doit-il pas être utilisé comme nom de service ?

**Réponse :** un pod est une instance éphémère, remplacée lors des redéploiements ; le service doit conserver une identité stable.

## Étape 12 — Définir les règles de nommage

Appliquez les règles suivantes : minuscules, mots séparés par des tirets, capacité stable, aucun environnement, version, hôte, pod ou identifiant dans le nom, owner identifiable.

| Nom initial | Proposition de référence | Explication |
|---|---|---|
| `Orders_API_PROD` | `orders-api` | `prod` est porté par `env` |
| `api-v2-final` | nom de capacité à préciser | la version doit être séparée |
| `container-7841` | nom du service exécuté | l'instance doit être séparée |
| `send-mail-error-notifications.php` | `error-notification-worker` | proposition à valider avec l'équipe |
| `backend` | nom de domaine ou capacité plus précis | le périmètre actuel est ambigu |

### Pourquoi `app-api-prod` est-il un mauvais nom ?

**Réponse :** il mélange l'identité `app-api` et l'environnement `prod`, ce qui fragmente les vues et les comparaisons.

## Étape 13 — Normaliser les valeurs

À partir des variantes ci-dessous, complétez votre politique puis comparez-la à la référence.

```text
environment: prod, prd, production
service: order, orders-api, api_orders
version: latest, v41, 2.4.0
team: checkout, commerce, équipe-commerce
```

| Dimension | Clé de référence | Valeur de référence | Valeurs à retirer progressivement |
|---|---|---|---|
| environnement | `env` | `prod` | `prd`, `production` |
| service | `service` | `orders-api` | `order`, `api_orders` |
| version | `version` | `2.4.0` ou format validé | `latest`, formats concurrents |
| équipe | `team` | identifiant stable contrôlé | synonymes et libellés libres |

### Une autre valeur canonique est-elle acceptable ?

**Réponse :** oui, si elle est unique, documentée, contrôlée et compatible avec le référentiel de l'organisation.

**Pourquoi :** la cohérence importe davantage que le vocabulaire particulier choisi ici.

## Étape 14 — Classer les attributs

Comparez chaque attribut au classement proposé et notez toute adaptation nécessaire dans votre contexte.

| Attribut | Classement de référence | Justification ou précaution |
|---|---|---|
| `env` | tag unifié | liste bornée |
| `service` | tag unifié | identité stable |
| `version` | tag unifié | version réellement déployée |
| `team` | tag borné | ownership contrôlé |
| `region` | tag borné | valeurs d'hébergement maîtrisées |
| `runtime` | tag borné | valeurs techniques maîtrisées |
| `tenant_tier` | tag borné si usage explicite | vocabulaire contrôlé |
| `checkout_step` | tag borné ou attribut de span/log | dépend de l'usage analytique |
| `tenant_id` | attribut de span/log si autorisé | forte cardinalité, anonymisation possible |
| `order_id` | attribut de span/log si nécessaire | presque unique, pseudonymisation |
| `customer_email` | donnée exclue | donnée personnelle |
| `credit_card_number` | donnée exclue | donnée financière sensible |

### Pourquoi les identifiants presque uniques ne sont-ils pas des tags de métriques par défaut ?

**Réponse :** ils multiplient le nombre de séries et peuvent rendre l'agrégation coûteuse et difficile à exploiter.

**Limite :** ils peuvent rester recherchables dans des traces ou des logs si le besoin, la confidentialité et la conservation ont été validés.

## Étape 15 — Calculer la cardinalité théorique

Une métrique porte 4 environnements, 6 services, 3 versions actives et 2 niveaux de tenant.

1. Calculez le nombre maximal théorique de combinaisons.
2. Ajoutez `tenant_id` avec 20 000 valeurs.
3. Décidez si `tenant_id` doit devenir un tag de métrique.

### Quel est le résultat ?

**Réponse :** `4 × 6 × 3 × 2 = 144` combinaisons théoriques. Avec `tenant_id`, `144 × 20 000 = 2 880 000` combinaisons théoriques.

**Interprétation :** l'effet multiplicatif justifie de ne pas utiliser `tenant_id` comme tag de métrique par défaut.

**Limite :** ce maximum théorique n'est pas nécessairement le nombre réellement émis ; il sert à mesurer le risque avant instrumentation.

## Étape 16 — Définir une source d'autorité

Complétez ou adaptez ce modèle.

| Clé | Source possible | Propriétaire | Contrôle |
|---|---|---|---|
| `env` | plateforme de déploiement | équipe plateforme | dictionnaire des valeurs et audit |
| `service` | configuration applicative/APM | équipe applicative avec la plateforme | comparaison APM, logs et métriques |
| `version` | pipeline CI/CD | équipe applicative | correspondance artefact-déploiement |
| `team` | catalogue de services | gouvernance ou plateforme | liste d'équipes contrôlée |

### Que risque-t-il de se passer si plusieurs sources définissent `service` différemment ?

**Réponse :** les signaux peuvent porter des valeurs concurrentes, ce qui fragmente recherches, dashboards, monitors et corrélations.

**Pourquoi :** une source d'autorité explicite permet de corriger la valeur à l'origine plutôt que de compenser dans chaque vue.

# Partie 4 — Produire le livrable

## Étape 17 — Prioriser trois écarts

1. Relisez vos observations des étapes 1 à 10.
2. Choisissez uniquement des faits visibles et datés.
3. Associez à chaque fait un impact, une action proposée et un validateur.

| Priorité | Fait observé | Impact | Action proposée | Validation par |
|---:|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |

### Quelles améliorations sont recevables ?

**Réponse :** compléter `env` à la source, qualifier les composants techniques, remplacer progressivement des noms de scripts, normaliser les environnements, propager `version`, associer un owner ou auditer les sources concurrentes.

**Limite :** chaque action doit reposer sur une preuve actuelle et être validée par son propriétaire ; aucune n'est exécutée dans cet atelier.

## Étape 18 — Rédiger la convention V1

Utilisez cette structure et adaptez les valeurs à votre organisation.

```text
Convention de services et de tags — V1

Service
- capacité stable, exploitable et possédée ;
- nom en minuscules et tirets ;
- aucun environnement, version, hôte, pod ou identifiant dans le nom.

Tags obligatoires
- env : valeur contrôlée ;
- service : nom stable ;
- version : version déployée ;
- team : équipe propriétaire.

Tags bornés possibles
- region, runtime, tenant_tier, checkout_step.

Attributs de spans ou logs
- tenant_id et order_id seulement si usage et confidentialité validés.

Données exclues
- customer_email, credit_card_number, secrets et contenu libre sensible.

Gouvernance
- une source d'autorité par clé ;
- revue des nouvelles dimensions ;
- audit des valeurs concurrentes ;
- test sur un périmètre pilote avant généralisation.

Améliorations prioritaires
- trois actions issues des observations datées de l'atelier.
```

## Questions de synthèse corrigées

### Quels tags relient les signaux d'un même déploiement ?

**Réponse :** `env`, `service` et `version`, appelés ensemble **Unified Service Tagging**.

**Explication :** ils donnent un contexte commun aux signaux afin de naviguer entre métriques, traces et logs lorsque l'instrumentation et la propagation sont cohérentes.

### Pourquoi une dépendance technique visible reste-t-elle utile ?

**Réponse :** elle aide à comprendre les appels et les causes techniques, même si elle n'est pas une capacité métier possédée comme un service.

### Pourquoi faut-il distinguer fait et hypothèse ?

**Réponse :** l'interface prouve ce qui est affiché dans une vue et une période ; elle ne prouve pas automatiquement la nature métier, la cause d'une absence ou l'état de toute la plateforme.