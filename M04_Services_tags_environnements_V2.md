---
title: "Module 4 — Atelier V2 : services, tags et environnements"
subtitle: "Document participant autonome — création contrôlée dans le Catalog"
lang: fr-FR
---

# Créer et qualifier un service fictif unique

## Objectif

Auditer le Catalog réel, créer une définition de service strictement pédagogique portant un identifiant dérivé du nom du participant, l'enrichir avec des métadonnées contrôlées, puis vérifier ce que cette déclaration apporte et ce qu'elle ne crée pas.

## Livrable

Vous produisez :

- un identifiant de service unique et normalisé ;
- une définition de service fictive enregistrée dans le Catalog ;
- une fiche de contrôle avant et après création ;
- une convention minimale de nommage et de tags ;
- une analyse distinguant métadonnées déclarées et télémétrie observée ;
- une entrée dans le registre de nettoyage.

## Règle de sécurité

Cet atelier autorise une seule écriture : la création puis la modification de **votre propre définition de service fictive**.

Vous ne devez pas :

- modifier, renommer, fusionner ou supprimer un service existant ;
- créer ou modifier une Team Datadog ;
- associer PagerDuty, une astreinte, un canal réel ou un dépôt privé ;
- utiliser `env:prod`, `env:production` ou un nom ressemblant à un service réel ;
- ajouter une adresse email, un numéro, un identifiant client ou une autre donnée personnelle ;
- instrumenter une application ou envoyer de la télémétrie ;
- exécuter vous-même le nettoyage final sauf autorisation explicite du coordinateur.

En cas de doute, arrêtez la création et conservez uniquement la fiche préparatoire.

## Prérequis

- Chrome connecté à l'organisation Datadog ;
- droit **Service Catalog Write** limité à l'activité pédagogique ;
- accès à **Developer Portal > Catalog** ;
- une équipe pédagogique Datadog existante, si l'ownership est obligatoire ;
- un registre de créations tenu par le coordinateur ;
- accord du participant pour utiliser un identifiant dérivé de son nom.

Si le participant ne souhaite pas afficher son nom dans l'organisation partagée, utilisez le pseudonyme fourni par le coordinateur. Le principe d'unicité reste identique.

## Convention de l'atelier

Le nom technique suit ce modèle :

```text
training-<prenom-nom>-<aaaammjj>-svc
```

Exemple fictif :

```text
training-alex-martin-20260720-svc
```

Règles de normalisation :

- minuscules ;
- caractères ASCII uniquement ;
- accents retirés ;
- espaces et apostrophes remplacés par `-` ;
- tirets consécutifs réduits à un seul ;
- aucun titre, société, email ou identifiant RH ;
- date de la session ajoutée pour limiter les collisions.

Si deux participants produisent encore le même nom, ajoutez un suffixe fourni par le coordinateur : `-a`, `-b`, etc.

## Comment utiliser ce document

Chaque question est suivie de sa réponse. Effectuez d'abord l'observation ou rédigez votre proposition, puis comparez-la à la référence. Les libellés de l'interface peuvent varier ; n'utilisez jamais une commande qui affecte une autre entité.

# Partie 1 — Préparer la création

## Étape 1 — Ouvrir le Catalog en lecture

1. Dans le menu gauche, ouvrez **Developer Portal**.
2. Dans la barre supérieure de l'Internal Developer Portal, ouvrez **Catalog**.
3. Dans le panneau gauche, sélectionnez **Services**.
4. Conservez **Env = \***, puis sélectionnez l'onglet **Ownership** : une définition manuelle sans télémétrie peut ne pas être visible de manière pertinente dans la vue **Performance**.
5. Repérez la recherche **Search by name or tags**, les filtres et la colonne du nom.
6. N'ouvrez encore aucune commande de création.

### Pourquoi commencer en lecture ?

**Réponse :** pour comprendre le catalogue existant et vérifier que le futur nom ne chevauche aucune entité réelle.

**Pourquoi :** un nom en conflit pourrait enrichir ou remplacer les métadonnées d'un service existant au lieu de créer une entité isolée.

## Étape 2 — Construire l'identifiant participant

Complétez uniquement avec le nom ou pseudonyme autorisé :

| Élément | Valeur |
|---|---|
| Prénom ou pseudonyme normalisé |  |
| Nom normalisé |  |
| Date `aaaammjj` |  |
| Suffixe éventuel |  |
| Nom technique final |  |

### Quel résultat est conforme ?

**Réponse :** un nom de la forme `training-<participant>-<date>-svc`, en minuscules et tirets, ne contenant aucune donnée autre que l'identifiant pédagogique autorisé.

**Limite :** le préfixe et la date réduisent les collisions mais ne les éliminent pas ; la recherche du Catalog reste obligatoire.

## Étape 3 — Vérifier l'unicité

1. Dans **Catalog > Services > Ownership**, recherchez le nom technique exact.
2. Conservez `Env = *` afin de ne masquer aucune occurrence.
3. Attendez la fin du chargement de la table avant de conclure.
4. Vérifiez également le nom sans le suffixe final.
5. Notez le résultat.

| Recherche | Résultat |
|---|---|
| Nom exact |  |
| Nom sans suffixe |  |
| Collision détectée ? |  |

### Que faire si le nom exact existe déjà ?

**Réponse :** ne l'ouvrez pas en édition et ne le réutilisez pas. Ajoutez le suffixe validé par le coordinateur, puis recommencez les recherches.

**Pourquoi :** l'atelier ne doit jamais prendre possession d'une définition existante, même si son nom semble pédagogique.

## Étape 4 — Enregistrer la réservation

Avant la création, ajoutez une ligne au registre partagé :

| Champ | Valeur à enregistrer |
|---|---|
| Nom technique | nom final validé |
| Participant | nom ou pseudonyme autorisé |
| Date de création | date de la session |
| Type | `service` |
| Owner de nettoyage | coordinateur de la formation |
| Échéance de nettoyage | date fixée par le coordinateur |
| État | `réservé` |

### Pourquoi réserver le nom avant de créer ?

**Réponse :** pour éviter que deux participants créent simultanément la même entité et pour garantir qu'une ressource pédagogique ne devienne pas orpheline.

# Partie 2 — Préparer les métadonnées

## Étape 5 — Définir l'identité du service fictif

Utilisez cette référence :

| Champ | Valeur de référence |
|---|---|
| Kind | `Service` |
| Name | nom technique unique |
| Display name | `[TRAINING] Service de <participant>` |
| Description | `Service fictif créé pour la formation Datadog ; aucune télémétrie de production. Il sert à expérimenter le Catalog, les métadonnées, le lifecycle, le type, les tags personnalisés et les fonctions de recherche.` |
| Lifecycle | `experimental` |
| Type | `Custom` |
| Tier | `4` |
| Languages | `Python` |

### Pourquoi choisir un cycle de vie expérimental ?

**Réponse :** pour signaler que l'entité n'est pas un service exploité en production.

**Pourquoi le tier 4 ?** Il matérialise le niveau de criticité le plus faible retenu pour cette ressource pédagogique. Il ne transforme pas le service en ressource de production et ne configure aucune alerte.

**Pourquoi Python ?** Le langage donne un exemple de métadonnée technique consultable et filtrable. Il ne signifie pas qu'une application Python est déployée ou instrumentée.

**Limite :** si `experimental`, `4` ou `Python` ne sont pas proposés par l'interface, n'improvisez pas une valeur de production ; laissez le champ concerné vide et notez l'écart.

## Étape 6 — Définir les tags

Dans **Custom Tags**, ajoutez uniquement les tags personnalisés acceptés par le formulaire et la convention locale :

```text
managed_by:training
purpose:datadog-course
training_participant:<identifiant-normalisé>
expires_on:<aaaammjj>
```

N'ajoutez pas `env:training`, `service:...` ou `version:...` dans **Custom Tags**. L'interface v3 indique que ces tags sont réservés à l'Unified Service Tagging et doivent rester distincts des tags personnalisés de l'entité.

### Le tag `env:training` crée-t-il un environnement contenant de la télémétrie ?

**Réponse :** non. Cet atelier ne renseigne pas `env:training` dans **Custom Tags**. Sans télémétrie corrélée, la page du service affiche `env:none` ; elle ne génère ni métrique, ni log, ni trace.

**Pourquoi :** le Catalog peut contenir une définition manuelle même lorsqu'aucun signal n'est détecté pour le service.

## Étape 7 — Définir l'ownership

1. Si le formulaire exige un owner, sélectionnez uniquement l'équipe pédagogique déjà créée et validée.
2. N'ajoutez pas votre équipe métier réelle par défaut.
3. Ne créez pas de Team pendant cet atelier.

### Pourquoi ne pas utiliser le participant comme owner ?

**Réponse :** l'ownership doit représenter une responsabilité durable, tandis que la ressource pédagogique sera nettoyée après la session.

**Pourquoi :** le nom du participant assure ici l'unicité ; l'équipe pédagogique assure le suivi et le nettoyage.

## Étape 8 — Définir les contacts et liens

Référence :

| Élément | Décision |
|---|---|
| PagerDuty/astreinte | interdit |
| Email personnel | interdit |
| Slack/Teams réel | interdit sauf canal pédagogique validé |
| Dépôt privé | interdit |
| Runbook de production | interdit |
| Documentation publique Datadog | facultative si le coordinateur l'autorise |

### Pourquoi limiter les liens ?

**Réponse :** une entité fictive ne doit ni déclencher un workflow réel ni exposer une ressource interne à des participants qui n'en ont pas besoin.

## Étape 9 — Effectuer le contrôle avant écriture

- [ ] Le nom commence par `training-`.
- [ ] Le nom contient l'identifiant participant autorisé.
- [ ] La date ou le suffixe garantit l'unicité.
- [ ] La recherche exacte ne retourne aucune entité.
- [ ] Le lifecycle n'est pas `production`.
- [ ] Aucun contact ou lien opérationnel réel n'est présent.
- [ ] Les tags indiquent `training` et une expiration.
- [ ] Le registre contient une réservation.
- [ ] L'owner de nettoyage est identifié.

### Peut-on créer si une case reste non validée ?

**Réponse :** non. Corrigez la fiche ou demandez une validation avant d'ouvrir le formulaire de création.

# Partie 3 — Créer la définition dans Datadog

## Étape 10 — Ouvrir le workflow de création

1. Dans **Developer Portal**, ouvrez **Settings**.
2. Dans **Entities Sources**, cliquez sur **Create More Entities**.
3. Cliquez sur **Add manually**.
4. Dans la fenêtre **Add a new entry in Software Catalog**, vérifiez **Kind = Service**.
5. Conservez le schéma **v3**, sélectionné par défaut sur la plateforme vérifiée.
6. Vérifiez que le formulaire concerne une nouvelle entrée et non un service existant.

### Quel droit permet d'enregistrer cette définition ?

**Réponse :** le droit **Service Catalog Write**.

**Pourquoi :** les utilisateurs en lecture peuvent consulter le Catalog, mais l'enregistrement d'une définition exige une permission d'écriture dédiée.

## Étape 11 — Saisir les métadonnées contrôlées

1. Recopiez le nom technique final.
2. Ajoutez le display name et la description pédagogiques exactement comme préparés à l'étape 5.
3. Saisissez `experimental` dans **Lifecycle**.
4. Saisissez `4` dans **Tier**.
5. Sélectionnez **Custom** dans **Type**.
6. Dans **Languages**, sélectionnez `Python`.
7. Dans **Custom Tags**, saisissez successivement les quatre tags préparés à l'étape 6 et validez chaque tag avec **Entrée**.
8. Fermez la liste de saisie des tags avec **Échap**, puis vérifiez que les quatre valeurs apparaissent comme des éléments sélectionnés.
9. Ajoutez l'owner pédagogique uniquement si une Team validée est proposée ; le champ peut rester vide.
10. Laissez vides les contacts, liens, relations **Depends On/Component Of**, intégrations On-Call, pipelines et emplacements de code.

### Pourquoi ne faut-il pas improviser une valeur dans le formulaire ?

**Réponse :** chaque métadonnée devient visible dans l'organisation partagée et peut influencer les recherches, regroupements ou contrôles du Catalog.

## Étape 12 — Examiner la définition générée

1. Si l'interface propose **YAML** ou **JSON**, ouvrez cet aperçu sans utiliser le cURL généré.
2. Vérifiez le nom, le kind, la description, le lifecycle, le tier, le type, le langage et les tags.
3. Repérez les éventuelles erreurs de validation et confirmez que **Schema Version = v3**.
4. Revenez au formulaire guidé si nécessaire.

### Pourquoi examiner le YAML ou JSON sans l'exécuter ?

**Réponse :** pour comprendre qu'une entité du Catalog est une définition structurée et vérifier précisément ce qui sera enregistré.

**Limite :** n'utilisez ni API, ni cURL, ni Terraform dans cet atelier ; ils élargiraient les moyens d'écriture au-delà du formulaire contrôlé.

## Étape 13 — Enregistrer l'entrée

1. Relisez le nom une dernière fois.
2. Cliquez une seule fois sur **Save Entry**.
3. Attendez la fin du traitement sans recharger ni soumettre à nouveau. La plateforme peut revenir directement à **Entities Sources** sans afficher durablement de confirmation.
4. En cas d'erreur ou de résultat incertain, recherchez d'abord le nom exact dans **Catalog > Services > Ownership** avant toute nouvelle tentative.

### Que faire si Datadog signale un conflit ?

**Réponse :** annulez la création, retournez à la recherche d'unicité et choisissez un nouveau suffixe. Ne tentez jamais d'écraser l'entrée existante.

## Étape 14 — Mettre à jour le registre

Après confirmation :

| Champ | Nouvelle valeur |
|---|---|
| État | `créé` |
| Heure de création |  |
| Créateur | participant autorisé |
| Nom final vérifié |  |
| Nettoyage requis | `oui` |

# Partie 4 — Vérifier le résultat

## Étape 15 — Retrouver le service

1. Revenez au **Catalog**.
2. Sélectionnez **Services**, `Env = *` et l'onglet **Ownership**.
3. Recherchez le nom technique exact et attendez la fin du chargement.
4. Vérifiez qu'une seule ligne correspond.
5. Vérifiez que la ligne indique la source **UI** et le schéma **v3**.
6. Ouvrez **Service Page**.

### Quel résultat est attendu ?

**Réponse :** une seule définition portant le nom unique et les métadonnées pédagogiques validées.

**Limite :** si plusieurs lignes ou une fusion inattendue apparaissent, n'effectuez aucune autre modification et prévenez le coordinateur.

## Étape 16 — Auditer les métadonnées enregistrées

| Métadonnée | Valeur attendue | Valeur observée |
|---|---|---|
| Name | nom technique unique |  |
| Display name | `[TRAINING] ...` |  |
| Description | texte complet de l'étape 5 |  |
| Lifecycle | `experimental` |  |
| Tier | `4` |  |
| Owner | équipe pédagogique ou `Not Provided` |  |
| Type | `Custom` |  |
| Languages | `Python` |  |
| Source de métadonnées | `UI` |  |
| Schéma | `v3` |  |
| Custom Tags | les quatre tags de l'étape 6 |  |
| Contacts/liens | aucun |  |
| Relations/On-Call | aucun |  |

### Pourquoi comparer attendu et observé ?

**Réponse :** pour détecter immédiatement une valeur manquante ou imprévue avant que la définition reste dans l'organisation partagée.

## Étape 17 — Vérifier la recherche par métadonnées

1. Revenez à **Developer Portal > Catalog > Services > Ownership**.
2. Dans **Search by name or tags**, recherchez `training_participant:<identifiant-normalisé>`.
3. Vérifiez que votre service est le seul résultat attendu pour cet identifiant et cette session.
4. Remplacez la recherche par `purpose:datadog-course`.
5. Observez que cette recherche peut retourner plusieurs services pédagogiques : le tag décrit un usage commun, il n'assure pas l'unicité.
6. Recherchez enfin le nom technique exact, puis rouvrez sa **Service Page**.

### Que démontre cette recherche ?

**Réponse :** les tags enrichissent la classification et la recherche dans le Catalog. Le tag participant permet de retrouver une ressource individuelle ; le tag de finalité permet de regrouper les ressources d'une même formation.

**Limite :** cette capacité de recherche repose sur les métadonnées du Catalog. Elle ne prouve toujours pas l'existence de traces, métriques ou logs.

## Étape 18 — Examiner Setup Guidance et la télémétrie

1. Sur **Service Page**, vérifiez que l'environnement affiché est `none` et que **Service Summary** indique **No APM or USM metrics**.
2. Ouvrez **Setup Guidance** et relevez les contrôles détectés et non détectés.
3. Vérifiez que **Log Patterns** est désactivé en l'absence de logs.
4. N'essayez pas de rendre les contrôles verts en instrumentant une application.
5. Ne cliquez pas sur **New Monitor**, **New SLO** ou **New API Test** : ces liens ouvrent des workflows de création hors périmètre de cet atelier.

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

## Étape 19 — Distinguer service déclaré et service observé

| Propriété | Service déclaré dans cet atelier | Service APM observé |
|---|---|---|
| Origine | définition manuelle | traces ou autre télémétrie |
| Métadonnées | fournies par le formulaire | détectées et éventuellement enrichies |
| Trafic | aucun par défaut | requêtes mesurées |
| Latence/erreurs | absentes | calculées depuis les signaux |
| Dépendances | non observées | déduites des appels instrumentés |

### La présence dans le Catalog prouve-t-elle que le service fonctionne ?

**Réponse :** non. Elle prouve ici qu'une définition existe, pas qu'une application est déployée ou instrumentée.

# Partie 5 — Modifier uniquement sa définition

## Étape 20 — Préparer une modification pédagogique

La seule modification demandée est :

```text
Ajouter à la description :
"Convention vérifiée pendant l'atelier du module 4."
```

### Pourquoi limiter la modification à la description ?

**Réponse :** elle démontre le cycle de mise à jour sans modifier l'identité, l'owner, le lifecycle ou les relations de l'entité.

## Étape 21 — Vérifier à nouveau l'identité

Avant d'ouvrir l'édition :

1. vérifiez le nom technique complet ;
2. vérifiez qu'il contient votre identifiant participant ;
3. vérifiez le préfixe `training-` ;
4. vérifiez sa présence dans le registre avec l'état `créé`.

### Que faire si l'une des vérifications échoue ?

**Réponse :** ne cliquez pas sur **Edit** et prévenez le coordinateur.

## Étape 22 — Modifier et enregistrer

1. Sur **Service Page**, repérez la section **Entity Metadata**.
2. Dans cette section, cliquez sur **Edit**. N'utilisez pas le bouton **Edit** placé près du sélecteur `env`, qui ne cible pas explicitement les métadonnées du Catalog.
3. Ajoutez la phrase prévue à la description.
4. Vérifiez qu'aucun autre champ n'a changé et que le schéma reste **v3**.
5. Enregistrez une seule fois avec **Save Entry**.
6. Revenez en lecture, puis rouvrez **Entity Metadata > Edit** afin de contrôler la persistance de la description, du lifecycle `experimental`, du tier `4`, du type `Custom`, du langage `Python` et des quatre tags.
7. Cliquez sur **Cancel** après ce contrôle : aucune nouvelle modification n'est nécessaire.

### Quelle preuve faut-il conserver ?

**Réponse :** le nom du service, la description finale et l'heure de contrôle, sans capture contenant d'autres services ou informations sensibles.

## Étape 23 — Mettre à jour le registre après modification

| Champ | Valeur |
|---|---|
| État | `créé et vérifié` |
| Modification autorisée | `description uniquement` |
| Contrôle effectué | `oui` |
| Incident ou anomalie | `aucun` ou description factuelle |

# Partie 6 — Produire la convention et préparer le nettoyage

## Étape 24 — Formaliser les règles apprises

```text
Convention V2 — Service pédagogique

- nom stable, en minuscules et tirets ;
- préfixe training et identifiant participant autorisé ;
- environnement, version et instance hors du nom métier en production ;
- lifecycle explicitement non productif ;
- owner durable pour chaque ressource ;
- métadonnées déclarées distinguées de la télémétrie observée ;
- aucun contact, secret ou workflow opérationnel réel ;
- registre et expiration obligatoires pour toute création pédagogique.
```

### Pourquoi la date est-elle acceptée dans ce nom alors qu'une version ou un environnement est normalement exclu ?

**Réponse :** il s'agit d'un identifiant temporaire de ressource pédagogique destiné à garantir l'unicité et le nettoyage, pas du nom d'un véritable service applicatif durable.

**Limite :** cette exception ne doit pas être transposée à la convention de nommage des services de production.

## Étape 25 — Préparer le nettoyage contrôlé

Complétez le registre :

| Élément | Valeur |
|---|---|
| Nom exact |  |
| État final de l'atelier | `à nettoyer` |
| Date cible |  |
| Responsable | coordinateur |
| Vérification préalable | nom préfixé et créateur confirmé |
| Vérification après nettoyage | recherche exacte sans résultat |

### Pourquoi le participant ne supprime-t-il pas immédiatement le service ?

**Réponse :** la suppression est une opération destructive. Le coordinateur doit vérifier le registre, le nom exact et l'absence de dépendance avant de suivre la procédure de nettoyage autorisée.

**Pourquoi :** ce contrôle centralisé évite qu'un participant supprime une entité homonyme ou une ressource qui ne lui appartient pas.

## Questions de synthèse corrigées

### Qu'a réellement créé l'atelier ?

**Réponse :** une définition d'entité `Service` et ses métadonnées dans le Catalog.

### Qu'est-ce que l'atelier n'a pas créé ?

**Réponse :** aucune application, trace, métrique, dépendance, alerte, SLO, log ou infrastructure.

### Pourquoi le nom du participant ne suffit-il pas pour l'unicité ?

**Réponse :** des homonymes et des sessions répétées restent possibles ; la date, le contrôle par recherche et éventuellement un suffixe sont également nécessaires.

### Pourquoi le nom du participant est-il traité comme une donnée contrôlée ?

**Réponse :** il permet l'unicité mais reste une information personnelle. Son usage nécessite l'accord du participant et peut être remplacé par un pseudonyme fourni par le coordinateur.

### Quelle est la différence entre tags de métadonnées et Unified Service Tagging observé ?

**Réponse :** les tags enregistrés décrivent l'entité du Catalog ; l'Unified Service Tagging porté par la télémétrie relie effectivement métriques, traces et logs d'un déploiement.

## Aide au diagnostic

| Difficulté | Interprétation | Action sûre |
|---|---|---|
| **Create a New Entry** absent | droit insuffisant ou interface différente | rester en lecture et remettre la fiche préparatoire |
| nom déjà présent | collision | ajouter un suffixe validé et recommencer la recherche |
| owner pédagogique absent | Team non préparée | laisser vide si autorisé ou arrêter ; ne pas créer de Team |
| valeur `experimental` absente | schéma différent | utiliser la valeur validée ou laisser vide ; jamais `production` par défaut |
| tier ou langage absent après sauvegarde | valeur non persistée ou formulaire incomplet | rouvrir **Entity Metadata > Edit**, corriger uniquement le champ manquant et enregistrer une fois |
| tags absents après sauvegarde | saisie non validée ou liste restée ouverte | ressaisir chaque tag avec **Entrée**, fermer la liste avec **Échap**, puis enregistrer une fois |
| validation YAML/JSON en erreur | champ invalide | revenir au formulaire et corriger sans API |
| plusieurs services après création | conflit ou corrélation inattendue | arrêter toute modification et prévenir le coordinateur |
| service absent de la vue Performance | aucune télémétrie à afficher | utiliser **Services > Ownership** |
| retour immédiat à Entities Sources après Save | comportement observé | rechercher le nom exact avant toute nouvelle soumission |
| `env:none` sur Service Page | aucun environnement corrélé par télémétrie | ne pas ajouter `env` dans Custom Tags pour masquer ce constat |
| télémétrie et Setup Guidance `Not Detected` | comportement normal | ne pas cliquer sur les workflows de création proposés |
| erreur après Save | résultat incertain | rechercher le nom exact avant toute nouvelle soumission |

## Validation finale

- [ ] L'utilisation du nom ou pseudonyme est autorisée.
- [ ] Le nom est normalisé et commence par `training-`.
- [ ] La date et le contrôle par recherche garantissent l'unicité.
- [ ] Le registre a été complété avant la création.
- [ ] Une seule définition de service a été créée.
- [ ] Le lifecycle n'est pas productif.
- [ ] Le tier `4` et le langage `Python` sont visibles après réouverture de l'éditeur.
- [ ] Les quatre tags personnalisés sont visibles et permettent de retrouver le service.
- [ ] Les tags et la description identifient clairement l'usage pédagogique.
- [ ] Aucun contact, secret, dépôt ou workflow réel n'est associé.
- [ ] Aucune Team n'a été créée ou modifiée.
- [ ] L'absence de télémétrie est correctement interprétée.
- [ ] La seule modification porte sur la description du service créé.
- [ ] Les métadonnées déclarées sont distinguées des signaux observés.
- [ ] Le registre indique que le nettoyage reste à effectuer.
- [ ] Aucun service existant n'a été modifié ou supprimé.

## Références officielles

- [Datadog — Create Entities](https://docs.datadoghq.com/internal_developer_portal/catalog/set_up/create_entities/)
- [Datadog — Set Up Catalog](https://docs.datadoghq.com/internal_developer_portal/catalog/set_up/)
- [Datadog — Entity Model](https://docs.datadoghq.com/internal_developer_portal/catalog/entity_model/)
