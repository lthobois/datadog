---
title: "Module 6 — Atelier : corrélation et investigation d'incidents"
subtitle: "Document participant autonome"
lang: fr-FR
---

# Mener une investigation corrélée

## Objectif

Qualifier un incident, naviguer entre les vues utiles, construire une chronologie, tester des hypothèses concurrentes avec des signaux indépendants, expliquer un mécanisme causal et proposer des actions adaptées.

## Livrable

Vous produisez :

- un journal d'investigation ;
- une chronologie distinguant faits et estimations ;
- au moins trois hypothèses concurrentes ;
- une fausse piste écartée par une preuve ;
- une cause probable avec un niveau de confiance ;
- une mitigation, une correction durable et des actions de prévention ;
- une restitution synthétique du raisonnement.

## Règle de sécurité

Travaillez uniquement en lecture. Vous pouvez effectuer des recherches temporaires, utiliser des facettes et ouvrir des vues. Ne sauvegardez rien et ne créez, ne modifiez, n'acquittez ou ne déclarez aucun monitor, incident, dashboard, pipeline, index, SLO, règle APM ou paramètre.

Ne recopiez aucun identifiant réel, nom d'hôte, adresse IP ou donnée personnelle. Tous les identifiants conservés dans le dossier sont fictifs.

## Prérequis

- convention `env/service/version/team` du module 2 ;
- conception du dashboard du module 3 ;
- pratique des logs du module 4 ;
- fiche APM du module 5 ;
- accès en lecture à APM et aux logs pour répéter les gestes de navigation.

## Comment utiliser ce document

Pour chaque preuve PeopleShop, écrivez d'abord la question et la prédiction demandées, puis lisez la carte et sa réponse expliquée. Les données réelles de `app-api` servent exclusivement à maîtriser les pivots de navigation. Toutes les conclusions de l'incident reposent exclusivement sur les preuves fictives PeopleShop incluses ici.

# Dossier initial

## Ticket support

```text
10:18 — Certains clients ne parviennent plus à valider leur commande.
D'autres constatent une attente de plusieurs secondes.
Le support estime que le problème a commencé vers 10:15.
```

## Notification du monitor

```text
10:22 — Errors are high on orders-api.
```

## Informations encore inconnues

- environnement précis ;
- versions concernées ;
- population affectée ;
- composant responsable ;
- mécanisme ;
- action de mitigation.

# Partie 1 — Qualifier avant de naviguer

## Étape 1 — Reformuler l'impact

| Élément | Réponse de référence |
|---|---|
| Fonctionnalité | validation de commande |
| Population connue | certains clients, sans précision supplémentaire |
| Début supposé | vers 10:15 |
| Symptômes | attente de plusieurs secondes et impossibilité de valider |
| Inconnues | environnement, version, étendue et cause |

```text
Depuis environ 10:15, certains utilisateurs rencontrent une lenteur ou
un échec de validation de commande ; l'environnement, la version et
l'étendue restent à confirmer.
```

### Pourquoi ne faut-il pas mentionner immédiatement `enterprise` ou PostgreSQL ?

**Réponse :** aucune information initiale ne prouve encore cette population ni ce composant.

**Pourquoi :** une formulation d'impact décrit le service rendu, la population connue et la période sans introduire une cause supposée.

## Étape 2 — Fixer les fenêtres d'analyse

Proposez une fenêtre comprenant une période nominale avant 10:15 et une marge après l'alerte.

| Fenêtre | Proposition de référence | Usage |
|---|---|---|
| Incident | 10:10 à 10:40 | capturer le début supposé et la propagation |
| Référence | 09:40 à 10:10 | comparer une période proche avant incident |

### Pourquoi 10:15 reste-t-il une estimation ?

**Réponse :** cette heure vient du support et doit être confrontée aux signaux techniques et utilisateurs.

**Limite :** une autre fenêtre est acceptable si elle contient un contraste normal et une marge suffisante.

## Étape 3 — Initialiser le journal

| Heure | Source | Fait observé | Hypothèse | Test suivant | Résultat | Décision |
|---:|---|---|---|---|---|---|
| 10:18 | ticket | lenteur et échecs signalés sur la validation | aucune à ce stade | confirmer périmètre et chronologie |  |  |
| 10:22 | monitor | alerte d'erreurs sur `orders-api` | incident API possible | comparer erreurs, trafic et versions |  |  |
|  |  |  |  |  |  |  |
|  |  |  |  |  |  |  |
|  |  |  |  |  |  |  |

### À quoi sert le journal ?

**Réponse :** il relie chaque fait à sa source et chaque hypothèse au test qui doit la discriminer.

**Pourquoi :** il évite les recherches circulaires et conserve aussi les résultats négatifs.

## Étape 4 — Formuler cinq hypothèses concurrentes

| Hypothèse | Observation qui la renforcerait | Observation qui l'affaiblirait |
|---|---|---|
| régression frontend | déploiement récent et délai avant l'entrée backend | requêtes atteignant normalement l'API |
| régression `orders-api` | dégradation concentrée sur une nouvelle version | comportement identique entre versions |
| paiement | spans paiement dominants ou hausse concomitante d'erreurs | erreurs antérieures stables et spans non dominants |
| PostgreSQL ou pool | spans SQL longs et attente du pool élevée | SQL nominal et pool stable |
| infrastructure | saturation cohérente sur les hôtes exécutant le service | anomalie hors périmètre ou décalée |

### Pourquoi faut-il écrire ce qui affaiblirait l'hypothèse favorite ?

**Réponse :** pour rechercher activement une preuve discriminante au lieu d'accumuler uniquement les éléments favorables.

# Partie 2 — Apprendre les pivots dans l'environnement réel

Cette partie valide les gestes sur `app-api`. Aucune valeur observée ici ne rejoint la chronologie PeopleShop.

## Étape 5 — Du service vers les traces

1. Ouvrez **APM > Services**.
2. Recherchez `app-api` ou choisissez un service applicatif actif de substitution.
3. Ouvrez sa page et repérez **Resources** et **Traces**.
4. Choisissez une ressource suffisamment représentée.
5. Ouvrez la liste des traces associées sans enregistrer de vue.

### Quelle question ce pivot permet-il de traiter ?

**Réponse :** quelles requêtes individuelles expliquent la latence ou les erreurs observées sur une ressource donnée ?

**Limite :** une trace illustre un cas et ne mesure pas seule l'ampleur de l'incident.

## Étape 6 — Du Trace Explorer vers une trace

1. Ouvrez **APM > Traces** ou **Trace Explorer**.
2. Saisissez temporairement `service:app-api`.
3. Repérez les facettes **Resource**, **Version**, **Status**, **Env** et **Peer Service**.
4. Sélectionnez une valeur proposée par l'interface.
5. Ouvrez une trace en lecture seule.
6. Repérez le waterfall, le span racine et le chemin critique.

### Pourquoi faut-il conserver service, ressource, environnement et période ?

**Réponse :** ces dimensions assurent que les observations comparées appartiennent au même périmètre opérationnel.

## Étape 7 — De la trace vers les logs

1. Dans le détail de la trace, repérez l'identifiant de trace sans le recopier.
2. Repérez un accès **View related logs**, **Logs** ou équivalent.
3. Ouvrez-le seulement s'il s'agit d'une navigation en lecture.
4. Vérifiez que le Log Explorer conserve la période et le contexte.
5. Revenez à la trace.

### Que prouve un `trace_id` commun entre une trace et un log ?

**Réponse :** ils appartiennent au même parcours instrumenté.

**Limite :** cela ne prouve pas que le message du log décrit la cause de l'incident.

## Étape 8 — Des logs vers le contexte

1. Ouvrez **Logs > Explorer**.
2. Repérez les facettes `service`, `env`, `version`, `status` et les attributs de trace, lorsqu'ils sont disponibles.
3. Ouvrez un événement et repérez les actions de filtrage et de corrélation.
4. Ne recopiez aucune valeur réelle.

### Que faut-il vérifier si les logs corrélés n'apparaissent pas ?

**Réponse :** l'injection et la collecte des identifiants, leur parsing/remapping, la cohérence de `service/env/version`, la période, la rétention et les droits.

## Étape 9 — Repérer les autres pivots utiles

1. Depuis une trace ou une page de service, repérez les accès vers **Infrastructure**, **Dependencies**, métriques d'hôte ou événements de déploiement.
2. Notez le chemin générique sans ouvrir de configuration.

```text
Service -> Resource -> Traces -> Trace -> Logs / Dependency / Infrastructure
```

### Pourquoi naviguer par question plutôt que par produit ?

**Réponse :** chaque pivot doit tester une hypothèse précise ; ouvrir successivement tous les modules produit du bruit et des recherches circulaires.

# Partie 3 — Construire la chronologie PeopleShop

## Étape 10 — Formuler la première question

Écrivez d'abord votre question. La formulation de référence est :

```text
La dégradation coïncide-t-elle avec un changement et se concentre-t-elle
sur une version ou une ressource ?
```

### Quel signal faut-il consulter en premier ?

**Réponse :** le dashboard de service et les événements de déploiement, car ils donnent impact, chronologie, ressource et comparaison de version.

## Étape 11 — Lire la preuve Dashboard et déploiement

```text
09:55 — p95 validation : 420 ms ; taux d'erreur : 0,4 %
10:15 — déploiement orders-api:2.4.0
10:17 — hausse du p95 sur POST /api/orders/validate
10:20 — erreurs applicatives en hausse
10:22 — monitor orders-api en alerte
10:28 — premières réponses 504 plus largement observées

Comparaison : la hausse est concentrée sur version:2.4.0.
```

### Que prouve cette carte ?

**Réponse :** elle confirme la ressource, la version affectée et l'ordre temporel entre déploiement, latence, erreurs et alerte.

**Limite :** la proximité du déploiement renforce une hypothèse de régression mais ne prouve pas encore son mécanisme.

## Étape 12 — Lire la preuve d'impact utilisateur

```text
10:18 — hausse du temps de validation et abandons.
Premiers cas surtout sur tenant_tier:enterprise.
À 10:28, certains cas standard sont également affectés.
```

### Quel périmètre est maintenant confirmé ?

**Réponse :** `service:orders-api`, ressource `POST /api/orders/validate`, version `2.4.0`, population d'abord `enterprise` puis impact élargi. L'environnement reste à confirmer par une preuve ultérieure.

## Étape 13 — Compléter la chronologie

| Heure | Événement | Source | Statut |
|---:|---|---|---|
| 09:55 | référence p95 et erreurs | dashboard | confirmé |
| 10:15 | déploiement `2.4.0` | événement | confirmé |
| 10:17 | hausse du p95 validation | dashboard | confirmé |
| 10:18 | signalement et abandons | ticket/impact | confirmé ; début support estimé |
| 10:20 | hausse des erreurs | dashboard | confirmé |
| 10:22 | monitor en alerte | monitor | confirmé |
| 10:28 | extension des 504 | dashboard/impact | confirmé |

### Quelle hypothèse devient prioritaire ?

**Réponse :** une régression de `orders-api:2.4.0` sur la validation, à localiser par comparaison de traces et de dépendances.

**Limite :** frontend, paiement, base et infrastructure restent encore des mécanismes concurrents.

# Partie 4 — Corréler APM et logs

## Étape 14 — Formuler la question APM

```text
Où le temps est-il consommé sur la même ressource, et qu'est-ce qui
diffère entre une trace normale et une trace lente ?
```

### Quelles observations doivent rester comparables ?

**Réponse :** service, ressource et environnement ; version et population sont les dimensions volontairement comparées.

## Étape 15 — Comparer les preuves APM

| Élément | Trace normale | Trace lente |
|---|---|---|
| service | `orders-api` | `orders-api` |
| env | non fourni sur la carte normale | `training` |
| ressource | `POST /api/orders/validate` | identique |
| version | `2.3.7` | `2.4.0` |
| population | `standard` | `enterprise` |
| durée totale | 420 ms | 2 800 ms |
| validation | 90 ms | 150 ms |
| PostgreSQL | 120 ms | 2 200 ms |
| paiement | 140 ms | 300 ms |
| trace fictive | `training-trace-normal` | `training-trace-slow` |

### Quel fait principal ressort de la trace lente ?

**Réponse :** PostgreSQL occupe 2 200 ms sur 2 800 ms et domine le chemin critique, alors que le paiement n'est pas dominant.

**Limite :** ce fait localise l'attente sur une trace ; il ne prouve pas encore pourquoi PostgreSQL attend ni la fréquence générale.

## Étape 16 — Préparer les requêtes de logs

```text
service:orders-api env:training version:2.4.0
@tenant_tier:enterprise
```

```text
@dd.trace_id:training-trace-slow
```

### Pourquoi utiliser à la fois contexte et identifiant de trace ?

**Réponse :** le contexte vérifie le périmètre du déploiement ; le `trace_id` isole les événements appartenant au parcours étudié.

## Étape 17 — Lire le log corrélé

```json
{
  "timestamp": "2026-07-16T10:31:14.520Z",
  "service": "orders-api",
  "env": "training",
  "version": "2.4.0",
  "dd.trace_id": "training-trace-slow",
  "tenant_tier": "enterprise",
  "feature": "extended_catalog",
  "db.pool.wait_ms": 1820,
  "http.status_code": 504,
  "message": "Order validation timed out while waiting for database"
}
```

### Que confirme ce log ?

**Réponse :** le même parcours `2.4.0`, `training`, `enterprise` attend 1 820 ms sur le pool et se termine par un 504.

**Pourquoi :** il fournit un mécanisme d'attente cohérent avec le span PostgreSQL long.

**Limite :** il faut encore montrer que le pool est effectivement saturé à l'échelle de l'incident.

# Partie 5 — Tester la dépendance et les fausses pistes

## Étape 18 — Prédire la preuve de dépendance

Avant de lire la carte, complétez :

```text
Si le pool PostgreSQL explique la lenteur, son utilisation et son attente
doivent augmenter pendant l'incident, même si ________________________.
```

### Quelle réponse est attendue ?

**Réponse :** même si le CPU et la mémoire de `orders-api` restent stables.

## Étape 19 — Lire la preuve Infrastructure et base

```text
09:55 — CPU orders-api stable ; mémoire stable ; pool PostgreSQL 42 %
10:20 — CPU orders-api stable ; mémoire stable ; pool PostgreSQL 90 %
10:35 — connexions occupées et attente du pool au maximum observé
```

### Que démontre ce contraste ?

**Réponse :** la saturation et l'attente se concentrent sur le pool PostgreSQL tandis que CPU et mémoire applicatifs restent stables.

**Pourquoi :** une investigation limitée aux ressources générales aurait manqué le mécanisme spécifique de dépendance.

## Étape 20 — Tester la piste paiement

Prédiction : si le paiement explique l'incident principal, ses erreurs ou sa durée doivent augmenter au même moment et dominer les traces lentes.

```text
Quelques erreurs payment-service existent avant 10:15.
Leur volume ne change pas significativement pendant l'incident.
Sur les traces lentes, le span payment-service n'est pas dominant.
```

### Quelle décision prendre ?

**Réponse :** la piste paiement est affaiblie pour l'incident principal.

**Pourquoi :** elle ne respecte ni le contraste temporel ni le mécanisme dominant, même si quelques erreurs réelles existent.

## Étape 21 — Tester la piste Kubernetes

```text
Un pic CPU mineur apparaît sur un nœud Kubernetes à 10:27.
orders-api s'exécute sur VMware dans ce scénario.
Le pic ne coïncide ni avec le début à 10:17 ni avec toutes les requêtes lentes.
```

### Pourquoi cette piste est-elle écartée ?

**Réponse :** le signal concerne le mauvais périmètre d'exécution et apparaît après le début de la dégradation.

## Étape 22 — Tester la piste frontend

```text
Le dernier déploiement frontend date de la veille.
Les requêtes de validation atteignent orders-api pendant l'incident.
Le temps dominant est observé après l'entrée backend.
```

### Pourquoi cette piste est-elle affaiblie ?

**Réponse :** aucun changement frontend concomitant n'est observé et la latence se situe après l'arrivée dans le backend.

# Partie 6 — Construire et évaluer la conclusion

## Étape 23 — Construire la chaîne causale

```text
orders-api:2.4.0 active le catalogue étendu pour tenant_tier:enterprise
  -> requête SELECT catalog_rules non indexée
  -> requêtes longues
  -> connexions PostgreSQL occupées
  -> attente et saturation du pool
  -> latence et timeouts de validation
  -> réponses 504 et abandons
```

### Quels maillons sont directement prouvés et lequel reste à confirmer ?

**Réponse :** la version, la population, le span PostgreSQL long, l'attente du pool, la saturation et les 504 sont soutenus par les cartes. La nature exacte de la requête **non indexée** et son plan d'exécution restent à confirmer par l'analyse SQL ou DBA.

**Pourquoi :** la cause doit décrire un mécanisme, pas seulement dire « la base est lente ».

## Étape 24 — Évaluer le niveau de confiance

| Critère | État | Preuve |
|---|---|---|
| temporalité cohérente | oui | déploiement puis p95, erreurs et 504 |
| même périmètre | oui | `orders-api`, validation, `2.4.0` |
| signaux indépendants | oui | APM, logs et métriques de pool |
| mécanisme expliqué | largement | attente et saturation du pool |
| comparaison normale | oui | trace `2.3.7` et pool à 42 % |
| effet d'une mitigation | pas encore | preuve suivante |

### Quel niveau de confiance retenir avant mitigation ?

**Réponse :** élevé, mais pas confirmé.

**Pourquoi :** plusieurs signaux indépendants soutiennent le mécanisme, mais la requête et son plan ne sont pas encore analysés.

## Étape 25 — Proposer puis évaluer la mitigation

Proposition de référence : rollback contrôlé vers `orders-api:2.3.7`, autorisé par le responsable de changement, avec surveillance du p95, des erreurs, des 504 et du pool.

Preuve après action, fournie dans le dossier :

```text
10:42 — rollback vers orders-api:2.3.7
10:47 — p95, erreurs et attente du pool reviennent progressivement
        au niveau normal
```

### Le retour à la normale prouve-t-il à lui seul la cause ?

**Réponse :** non. Il renforce fortement l'hypothèse liée à `2.4.0`, mais ne remplace pas l'analyse de la requête et du plan d'exécution.

**Limite :** dans l'atelier, la mitigation est une preuve fictive ; aucune action n'est exécutée.

## Étape 26 — Distinguer mitigation, correction et prévention

| Type | Réponse de référence | Owner possible | Preuve attendue |
|---|---|---|---|
| Mitigation | rollback `2.3.7` | responsable applicatif/changement | retour p95, erreurs et pool |
| Correction | corriger la requête ou ajouter un index validé | équipe applicative et DBA | plan d'exécution et test de performance |
| Validation | test du catalogue étendu par `tenant_tier` | QA/performance | résultats comparatifs avant déploiement |
| Données | maintenir attributs APM et logs corrélés | équipe applicative | trace et log reliés sans donnée sensible |
| Détection | dashboard par version et monitor actionnable | exploitation/team-orders | recette d'alerte et runbook |
| Processus | déploiement progressif et revue post-incident | produit/engineering | procédure et actions suivies |

### Quelle différence existe entre mitigation et correction ?

**Réponse :** la mitigation réduit rapidement l'impact ; la correction supprime durablement le mécanisme fautif.

## Étape 27 — Finaliser le journal

Vérifiez que chaque ligne contient une source, que chaque hypothèse possède un test, que les résultats négatifs sont conservés et que les identifiants sont fictifs.

| Heure | Source | Fait | Hypothèse/test | Résultat/décision |
|---:|---|---|---|---|
| 10:15 | déploiement | `2.4.0` déployée | comparer versions | hausse concentrée sur `2.4.0` |
| 10:17 | dashboard | p95 validation augmente | localiser dans APM | PostgreSQL domine la trace lente |
| 10:31 | log corrélé | attente pool 1 820 ms, 504 | vérifier saturation | pool à 90 %, puis attente maximale |
|  | paiement | erreurs antérieures stables | piste paiement | affaiblie |
|  | Kubernetes | pic tardif hors périmètre | piste infrastructure | écartée |
| 10:47 | mitigation fictive | retour progressif au nominal | effet du rollback | hypothèse fortement renforcée |

## Étape 28 — Préparer la restitution

Présentez dans cet ordre :

1. impact et périmètre ;
2. chronologie courte ;
3. preuves APM, logs et pool ;
4. cause probable et mécanisme ;
5. pistes paiement, Kubernetes et frontend ;
6. niveau de confiance ;
7. mitigation ;
8. correction, prévention et incertitudes restantes.

### Quelle formulation faut-il éviter ?

**Réponse :** « Datadog montre que la base est lente ».

**Pourquoi :** elle ne précise ni la version, la population, la ressource, le mécanisme d'attente, les preuves ni la limite restante.

## Questions de synthèse corrigées

### Pourquoi conserver une hypothèse écartée dans le journal ?

**Réponse :** pour éviter de la retester sans raison et montrer quelles preuves ont réduit le périmètre.

### Combien de signaux soutiennent ici la cause probable ?

**Réponse :** au moins trois familles indépendantes : le chemin critique APM, le log corrélé d'attente et les métriques du pool, renforcées ensuite par le rollback.

### Quelle incertitude principale reste ouverte ?

**Réponse :** la requête SQL exacte, son absence d'index et son plan d'exécution doivent être confirmés avant la correction durable.

## Aide au diagnostic

| Difficulté | Interprétation | Action |
|---|---|---|
| `app-api` absent | période ou activité différente | choisir un service actif pour le geste et noter la substitution |
| Trace réelle indisponible | rétention, échantillonnage ou droits | utiliser les preuves PeopleShop intégrées |
| Pivot logs absent | corrélation ou droits | appliquer la checklist de l'étape 8 |
| Recherche circulaire | question non formulée | écrire hypothèse, prédiction et signal avant le pivot |
| Cause trouvée immédiatement | biais de confirmation | tester au moins une fausse piste et un contraste normal |
| Confusion réel/fictif | périmètres mélangés | retirer toute donnée réelle du journal PeopleShop |

## Validation finale

- [ ] L'impact est formulé sans extrapolation.
- [ ] La chronologie distingue faits et estimations.
- [ ] La navigation réelle reste séparée des preuves PeopleShop.
- [ ] Le périmètre est réduit par service, ressource, version et population.
- [ ] Au moins trois hypothèses concurrentes sont testées.
- [ ] Une fausse piste est explicitement écartée.
- [ ] Le `trace_id` sert de pivot, pas de preuve causale suffisante.
- [ ] La cause probable repose sur APM, logs et métriques de dépendance.
- [ ] Le mécanisme est expliqué en chaîne.
- [ ] Le niveau de confiance est justifié.
- [ ] L'incertitude SQL restante est explicite.
- [ ] Mitigation, correction et prévention sont distinguées.
- [ ] Les résultats négatifs figurent dans le journal.
- [ ] Aucun identifiant réel ou sensible n'est conservé.
- [ ] Aucune ressource réelle n'a été modifiée.
