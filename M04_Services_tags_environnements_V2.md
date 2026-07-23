---
title: "Module 4 — Atelier V2 : services, tags et environnements"
subtitle: "Document participant autonome — création contrôlée dans le Catalog"
lang: fr-FR
---

# Créer et qualifier un service pédagogique unique

## Objectif

Auditer le Catalog réel, créer une définition de service strictement pédagogique portant un identifiant dérivé du nom du participant, l'enrichir avec des métadonnées contrôlées, puis vérifier ce que cette déclaration apporte et ce qu'elle ne crée pas.

## Livrable

Vous produisez :

- un identifiant de service unique et normalisé ;
- une définition de service pédagogique enregistrée dans le Catalog ;
- une vérification avant et après création ;
- une convention minimale de nommage et de tags ;
- une analyse distinguant métadonnées déclarées et télémétrie observée ;

Cette définition reste volontairement sans télémétrie. Elle ne reprend le nom d'aucun service existant et ne reçoit aucune donnée des modules 5 à 7.

## Règle de sécurité

Cet atelier autorise une seule écriture : la création puis la modification de **votre propre définition de service pédagogique**.

Vous ne devez pas :

- modifier, renommer, fusionner ou supprimer un service existant ;
- créer ou modifier une Team Datadog ;
- associer PagerDuty, une astreinte, un canal réel ou un dépôt privé ;
- utiliser `env:prod`, `env:production` ou un nom ressemblant à un service réel ;
- ajouter une adresse email, un numéro, un identifiant client ou une autre donnée personnelle ;
- instrumenter une application ou envoyer de la télémétrie ;
- exécuter vous-même le nettoyage final sauf autorisation explicite du formateur.


## Prérequis

- Chrome connecté à l'organisation Datadog ;
- droit **Service Catalog Write** limité à l'activité pédagogique ;
- accès à **Developer Portal > Catalog** ;
- une équipe pédagogique Datadog existante, si l'ownership est obligatoire ;
- accord du participant pour utiliser un identifiant dérivé de son nom.

Si le participant ne souhaite pas afficher son nom dans l'organisation partagée, utilisez le pseudonyme fourni par le formateur. Le principe d'unicité reste identique.

## Convention de l'atelier

Le nom technique suit ce modèle :

```text
training-<prenom-nom>-svc
```

Exemple de nom unique :

```text
training-loic-thobois-svc
```

Règles de normalisation :

- minuscules ;
- caractères ASCII uniquement ;
- accents retirés ;
- espaces et apostrophes remplacés par `-` ;
- tirets consécutifs réduits à un seul ;
- aucun titre, société, email ou identifiant RH.

Si deux participants produisent encore le même nom, ajoutez un suffixe fourni par le formateur : `-a`, `-b`, etc.

## Comment utiliser ce document

Chaque question est suivie de sa réponse. Effectuez d'abord l'observation ou rédigez votre proposition, puis comparez-la à la référence. Les libellés de l'interface peuvent varier ; n'utilisez jamais une commande qui affecte une autre entité.

# Partie 1 — Créer la définition dans Datadog

## Étape 1 — Vérifier que le futur nom est disponible

1. Dans **Developer Portal > Catalog > Services**, ouvrez l'onglet **Ownership**.
2. Conservez **Env = \***.
3. Construisez votre nom sous la forme `training-<participant>-svc`.
4. Utilisez uniquement votre identifiant pédagogique autorisé, en minuscules et avec des tirets.
5. Recherchez ce nom exact dans **Search by name or tags**.
6. Si le nom existe, ajoutez le suffixe fourni par le formateur et recommencez la recherche.

**Résultat attendu :** aucune entité existante ne porte le nom retenu.

## Étape 2 — Ouvrir le formulaire de création

1. Ouvrez **Developer Portal > Settings**.
2. Dans **Entities Sources**, cliquez sur **Create More Entities**.
3. Cliquez sur **Add manually**.
4. Dans **Add a new entry in Software Catalog**, choisissez **Kind = Service**.
5. Conservez **Schema Version = v3**.
6. Vérifiez qu'il s'agit d'une nouvelle entrée et non de l'édition d'un service existant.

**Droit nécessaire :** **Service Catalog Write**. Si vous ne le possédez pas, arrêtez avant toute saisie et suivez la démonstration du formateur.

## Étape 3 — Saisir immédiatement l'identité

Dans le formulaire ouvert, saisissez maintenant :

| Champ affiché | Valeur à saisir |
|---|---|
| Name | `training-<participant>-svc` |
| Display name | `[TRAINING] Service de <participant>` |
| Description | `Service pédagogique créé pour la formation Datadog ; aucune télémétrie de production.` |

Remplacez `<participant>` par votre valeur. Ne saisissez aucune équipe, application ou donnée métier réelle dans le nom ou la description.

## Étape 4 — Saisir les caractéristiques

Dans le même formulaire :

1. Dans **Lifecycle**, sélectionnez `experimental`.
2. Dans **Type**, sélectionnez `Custom`.
3. Dans **Tier**, sélectionnez `4`.
4. Dans **Languages**, sélectionnez `Python`.

Si une valeur n'est pas proposée, laissez uniquement ce champ vide et observez l'écart. N'utilisez pas une valeur de production en remplacement.

**Explication :** ces valeurs signalent une ressource pédagogique de faible criticité. Elles ne déploient aucun service et ne créent aucune télémétrie.

## Étape 5 — Saisir les tags

Dans **Custom Tags**, saisissez un tag, appuyez sur **Entrée**, puis passez au suivant :

```text
managed_by:training
purpose:datadog-course
training_participant:<participant>
```

Après le troisième tag, appuyez sur **Échap** et vérifiez que les trois tags apparaissent comme sélectionnés.

N'ajoutez pas `env:training`, `service:...` ou `version:...` : ces clés relèvent de l'Unified Service Tagging et aucune télémétrie n'est créée ici.

## Étape 6 — Traiter l'owner, les contacts et les liens

1. Si **Owner** est obligatoire, sélectionnez uniquement la Team pédagogique déjà validée.
2. Si aucune Team pédagogique n'est proposée, laissez le champ vide si le formulaire l'autorise.
3. Laissez vides les contacts, PagerDuty, Slack, Teams, dépôts, runbooks et liens internes.
4. Laissez vides **Depends On**, **Component Of**, On-Call, pipelines et emplacements de code.


Si l'interface propose un aperçu YAML ou JSON, ouvrez-le sans utiliser le cURL généré. Vérifiez :

- le nom unique et le préfixe `training-` ;
- `kind: service` et le schéma v3 ;
- la description pédagogique ;
- `experimental`, `Custom`, `4` et `Python` lorsqu'ils sont disponibles ;
- les trois tags ;
- l'absence de contact, relation ou intégration réelle.

Corrigez directement le champ concerné dans le formulaire.


1. Relisez une dernière fois le champ **Name**.
2. Cliquez une seule fois sur **Save Entry**.
3. Attendez la fin du traitement sans recharger la page.
4. Recherchez le nom exact dans **Catalog > Services > Ownership** avant toute seconde tentative.

En cas de conflit, annulez et recommencez à l'étape 1 avec un suffixe validé. N'écrasez aucune entrée existante.
# Partie 2 — Vérifier le résultat

## Étape 7 — Retrouver le service

1. Revenez au **Catalog**.
2. Sélectionnez **Services**, `Env = *` et l'onglet **Ownership**.
3. Recherchez le nom technique exact et attendez la fin du chargement.
4. Vérifiez qu'une seule ligne correspond.
5. Vérifiez que la ligne indique la source **UI** et le schéma **v3**.
6. Ouvrez **Service Page**.

### Quel résultat est attendu ?

**Réponse :** une seule définition portant le nom unique et les métadonnées pédagogiques validées.

**Limite :** si plusieurs lignes ou une fusion inattendue apparaissent, prévenez le formateur.

## Étape 8 — Auditer les métadonnées enregistrées

### Pourquoi comparer attendu et observé ?

**Réponse :** pour détecter immédiatement une valeur manquante ou imprévue avant que la définition reste dans l'organisation partagée.

## Étape 9 — Vérifier la recherche par métadonnées

1. Revenez à **Developer Portal > Catalog > Services > Ownership**.
2. Dans **Search by name or tags**, recherchez `training_participant:<identifiant-normalisé>`.
3. Vérifiez que votre service est le seul résultat attendu pour cet identifiant.
4. Remplacez la recherche par `purpose:datadog-course`.
5. Observez que cette recherche peut retourner plusieurs services pédagogiques : le tag décrit un usage commun, il n'assure pas l'unicité.
6. Recherchez enfin le nom technique exact, puis rouvrez sa **Service Page**.

### Que démontre cette recherche ?

**Réponse :** les tags enrichissent la classification et la recherche dans le Catalog. Le tag participant permet de retrouver une ressource individuelle ; le tag de finalité permet de regrouper les ressources d'une même formation.

**Limite :** cette capacité de recherche repose sur les métadonnées du Catalog. Elle ne prouve toujours pas l'existence de traces, métriques ou logs.

## Étape 10 — Examiner Setup Guidance et la télémétrie

1. Sur **Service Page**, vérifiez que l'environnement affiché est `none` et que **Service Summary** indique **No APM or USM metrics**.
2. Ouvrez **Setup Guidance** et observez les contrôles détectés et non détectés.
3. Vérifiez que **Log Patterns** est désactivé en l'absence de logs.

| Élément | Observation attendue |
|---|---|
| Distributed Tracing / APM | `Not Detected` |
| Universal Service Monitoring | `Not Detected` |
| Infrastructure Monitoring | `Not Detected` |
| Log Management | `Not Detected` ; **Log Patterns** désactivé |
| Version Tagging | `Not Detected` |
| Error Tracking | `Not Detected` |
| Monitors et SLOs | `Not Detected` |
| Synthetics Tests | `Not Detected` |
| Cloud Network | `Not Detected` |

### Pourquoi l'absence de télémétrie est-elle normale ?

**Réponse :** la création a uniquement ajouté une définition d'entité. Aucune application n'émet de signaux avec ce nom de service.

**Pourquoi :** le Catalog distingue les métadonnées déclarées de la télémétrie automatiquement découverte.

## Étape 11 — Distinguer service déclaré et service observé

| Propriété | Service déclaré dans cet atelier | Service APM observé |
|---|---|---|
| Origine | définition manuelle | traces ou autre télémétrie |
| Métadonnées | fournies par le formulaire | détectées et éventuellement enrichies |
| Trafic | aucun par défaut | requêtes mesurées |
| Latence/erreurs | absentes | calculées depuis les signaux |
| Dépendances | non observées | déduites des appels instrumentés |

### La présence dans le Catalog prouve-t-elle que le service fonctionne ?

**Réponse :** non. Elle prouve ici qu'une définition existe, pas qu'une application est déployée ou instrumentée.

# Partie 3 — Modifier uniquement sa définition

## Étape 12 — Modifier directement sa description

1. Vérifiez que le nom affiché commence par `training-` et contient votre identifiant.
2. Si l'un de ces contrôles échoue, prévenez le formateur.
3. Sur **Service Page**, cliquez sur **Service Config**, puis ouvrez **Entity Metadata > Edit**.
4. À la fin de la description existante, saisissez exactement :

```text
Convention vérifiée pendant l'atelier du module 4.
```

5. Vérifiez qu'aucun autre champ n'a changé et que le schéma reste **v3**.
6. Cliquez une seule fois sur **Save Entry**.
7. Rouvrez **Entity Metadata > Edit** pour vérifier la persistance de la phrase, puis cliquez sur **Cancel**.

**Réponse expliquée :** cette modification démontre le cycle de mise à jour sans toucher à l'identité, l'owner, le lifecycle ou les relations.
# Partie 4 — Produire la convention

## Étape 13 — Formaliser les règles apprises

```text
Convention V2 — Service pédagogique

- nom stable, en minuscules et tirets ;
- préfixe training et identifiant participant autorisé ;
- environnement, version et instance hors du nom métier en production ;
- lifecycle explicitement non productif ;
- owner durable pour chaque ressource ;
- métadonnées déclarées distinguées de la télémétrie observée ;
- aucun contact, secret ou workflow opérationnel réel ;
- préfixe et identifiant participant obligatoires pour toute création pédagogique.
```

## Étape 14 — Identifier la ressource à supprimer en fin de formation

1. Conservez le nom exact de votre service affiché dans **Service Page**.
2. À la fin de la formation, recherchez ce nom exact.
3. Avec l'autorisation du formateur, supprimez la ressource portant votre préfixe et votre identifiant.
4. Sinon, communiquez simplement le nom exact au formateur.

**Réponse expliquée :** le préfixe `[TRAINING]` et l'identifiant participant suffisent à reconnaître la ressource avec un processus simple.

## Questions de synthèse corrigées

### Qu'a réellement créé l'atelier ?

**Réponse :** une définition d'entité `Service` et ses métadonnées dans le Catalog.

### Qu'est-ce que l'atelier n'a pas créé ?

**Réponse :** aucune application, trace, métrique, dépendance, alerte, SLO, log ou infrastructure.

### Pourquoi le nom du participant ne suffit-il pas pour l'unicité ?

**Réponse :** des homonymes restent possibles ; le contrôle par recherche et, si nécessaire, un suffixe fourni par le formateur garantissent l'unicité.

### Pourquoi le nom du participant est-il traité comme une donnée contrôlée ?

**Réponse :** il permet l'unicité mais reste une information personnelle. Son usage nécessite l'accord du participant et peut être remplacé par un pseudonyme fourni par le formateur.

### Quelle est la différence entre tags de métadonnées et Unified Service Tagging observé ?

**Réponse :** les tags enregistrés décrivent l'entité du Catalog ; l'Unified Service Tagging porté par la télémétrie relie effectivement métriques, traces et logs d'un déploiement.


## Références officielles

- [Datadog — Create Entities](https://docs.datadoghq.com/internal_developer_portal/catalog/set_up/create_entities/)
- [Datadog — Set Up Catalog](https://docs.datadoghq.com/internal_developer_portal/catalog/set_up/)
- [Datadog — Entity Model](https://docs.datadoghq.com/internal_developer_portal/catalog/entity_model/)
