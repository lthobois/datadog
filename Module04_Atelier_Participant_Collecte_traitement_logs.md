---
title: "Module 4 — Atelier : collecte et traitement des logs"
subtitle: "Document participant autonome"
lang: fr-FR
---

# Structurer, rechercher et maîtriser les logs

## Objectif

Parcourir les logs réels en lecture seule, comprendre l'organisation du Log Explorer, puis concevoir pour PeopleShop un événement JSON corrélable, un traitement ordonné et une stratégie contrôlée de réduction du bruit.

## Livrable

Vous produisez :

- un relevé daté et une recherche temporaire anonymisée ;
- un événement JSON cible ;
- une règle de regroupement multiligne ;
- un pipeline logique ordonné ;
- une matrice facette, mesure, attribut ou exclusion ;
- une décision de réduction de volumétrie avec preuve et retour arrière.

## Règle de sécurité

Travaillez uniquement en lecture. Ne sauvegardez aucune vue et ne créez ni pipeline, index, archive, métrique, facette, mesure ou règle d'exclusion. N'ouvrez pas **Log Configuration**. Ne recopiez aucun identifiant réel, nom d'hôte, adresse IP, secret ou donnée personnelle.

## Prérequis

- Chrome connecté à Datadog ;
- accès en lecture au **Log Explorer** ;
- convention `env/service/version/team` du module 2 ;
- scénario PeopleShop et un document de réponse.

## Comment utiliser ce document

Réalisez d'abord l'observation demandée, puis comparez-la à la réponse placée immédiatement après la question. Les volumes, services et facettes changent avec la période : votre relevé daté prévaut sur les exemples historiques. À partir de la partie 3, travaillez uniquement sur PeopleShop et n'utilisez plus de donnée réelle.

# Partie 1 — Comprendre le Log Explorer

## Étape 1 — Ouvrir le Log Explorer

1. Dans le menu gauche, ouvrez **Logs**, puis **Explorer** ou **Search**, selon le libellé disponible.
2. Repérez la barre de requête, la période, la liste des événements et le panneau de facettes.
3. Notez la date, l'heure, la période et la requête initiale.
4. Vérifiez qu'aucun mode de configuration ou d'enregistrement n'est ouvert.

| Élément | Observation |
|---|---|
| Date et heure |  |
| Période |  |
| Requête initiale |  |
| Nombre de résultats, si affiché |  |

### Pourquoi noter la période avant d'interpréter les résultats ?

**Réponse :** parce que la présence des logs, les valeurs des facettes et les volumes dépendent de la fenêtre consultée.

**Pourquoi :** zéro résultat sur une courte période ne signifie pas qu'aucun événement n'existe dans l'historique.

**Limite :** lors de la conception, la période était **Past 15 Minutes** ; ce n'est pas une valeur attendue aujourd'hui.

## Étape 2 — Parcourir les facettes

1. Dans le panneau gauche, recherchez les groupes **Service**, **Status**, **Host**, **Source**, `env` et `version`, lorsqu'ils sont disponibles.
2. Dépliez une facette sans sélectionner de valeur.
3. Observez les valeurs et les volumes proposés.
4. Refermez-la et recommencez sur une autre facette.
5. Ne recopiez aucune valeur sensible.

### Qu'est-ce qu'une facette ?

**Réponse :** une facette rend un attribut utilisable pour filtrer, regrouper et explorer les valeurs présentes dans les logs.

**Pourquoi :** elle transforme une dimension utile, comme `service` ou `status`, en axe de navigation.

**Limite :** tous les attributs ne doivent pas devenir des facettes. Une facette doit répondre à un usage réel et sa liste dépend des données, de la période et des droits.

## Étape 3 — Observer la diversité des sources

1. Parcourez plusieurs événements sans recopier leur contenu.
2. Identifiez, si disponibles, un accès HTTP, un message applicatif, un log d'Agent, un log Kubernetes et une stack trace.
3. Notez seulement le type et le niveau de structure.

| Type observé | Texte, clé-valeur ou JSON | Champs directement exploitables |
|---|---|---|
|  |  |  |
|  |  |  |
|  |  |  |

### Un message lisible est-il forcément un événement bien structuré ?

**Réponse :** non. Un humain peut comprendre une phrase alors que Datadog ne dispose pas de champs stables pour filtrer, agréger et corréler.

**Pourquoi :** la structure explicite évite de reparcourir le texte avec des parsers fragiles.

## Étape 4 — Ouvrir un événement

1. Cliquez sur un événement non sensible pour ouvrir son panneau de détail.
2. Repérez le message, le timestamp et les attributs.
3. Cherchez `service`, `status`, `env`, `version`, `trace_id` et `span_id`.
4. Écrivez **non observé** pour chaque information absente de cette vue.
5. Fermez le détail sans action de configuration.

| Champ | État observé | Usage attendu |
|---|---|---|
| timestamp |  | ordonner et corréler dans le temps |
| message |  | expliquer l'événement |
| service |  | isoler la capacité concernée |
| status |  | qualifier la sévérité |
| env |  | isoler le contexte |
| version |  | comparer les déploiements |
| trace/span |  | naviguer entre logs et traces |

### Un champ non visible dans un événement est-il absent de tous les logs ?

**Réponse :** non. L'observation ne concerne que cet événement, cette source et cette vue.

**Limite :** ne généralisez jamais à partir de quelques exemples et n'inventez aucune valeur manquante.

## Étape 5 — Construire une recherche temporaire par facette

1. Choisissez un service proposé par la facette **Service**.
2. Cliquez sur sa valeur pour ajouter le filtre, ou saisissez `service:<valeur>`.
3. Vérifiez que la requête et la liste sont mises à jour.
4. Ajoutez un statut d'erreur proposé par la facette, si disponible.
5. Notez une version générique et anonymisée de la requête.
6. Retirez les filtres pour revenir à la requête initiale, sans sauvegarder.

```text
service:<service> status:error
```

### Que signifie une recherche sans résultat ?

**Réponse :** aucun événement ne correspond aux critères dans la période et le périmètre actuels.

**Pourquoi :** cela ne prouve ni l'absence historique d'erreurs ni l'absence de logs sous un autre statut ou service.

## Étape 6 — Utiliser la chronologie des résultats

1. Repérez l'histogramme ou la série de volume au-dessus des événements.
2. Identifiez un intervalle plus dense, sans changer la période globale.
3. Comparez visuellement la chronologie avec la liste des événements.
4. Notez ce que cette vue permettrait de décider pendant un incident.

### Quelle information la chronologie ajoute-t-elle à la liste ?

**Réponse :** elle montre quand le volume apparaît, augmente ou disparaît et aide à fixer une fenêtre d'investigation.

**Limite :** une hausse de volume ne signifie pas automatiquement une hausse d'erreurs ; elle peut refléter davantage de trafic ou de verbosité.

## Étape 7 — Repérer les fonctions de navigation détaillée

1. Rouvrez un événement.
2. Repérez, sans les déclencher si elles modifient l'état, les actions permettant de filtrer sur une valeur, exclure une valeur, afficher le contexte ou ouvrir une trace associée.
3. Si un identifiant de trace est visible, notez seulement **corrélation disponible** ; ne recopiez pas l'identifiant.

### Pourquoi la corrélation logs-traces est-elle utile ?

**Réponse :** elle permet de passer du symptôme textuel au chemin d'exécution distribué qui a produit l'événement.

**Pourquoi :** `service`, `env`, `version`, `dd.trace_id` et `dd.span_id` cohérents relient les signaux sans recherche manuelle fragile.

## Étape 8 — Situer Live Tail sans l'utiliser comme historique

1. Repérez **Live Tail** dans la navigation des logs, s'il est accessible.
2. N'y entrez pas si cela expose des données sensibles supplémentaires.
3. Notez sa place par rapport au Log Explorer.

### Quelle différence existe entre Live Tail et une recherche indexée ?

**Réponse :** Live Tail sert à observer le flux entrant en temps quasi réel, tandis que le Log Explorer interroge les événements disponibles pour la recherche selon les mécanismes d'indexation et de rétention.

**Limite :** la visibilité exacte d'un événement exclu dépend de la configuration. Live Tail ne remplace ni un historique indexé ni un archivage.

# Partie 2 — Auditer la qualité d'événements réels

## Étape 9 — Comparer trois événements homogènes

1. Choisissez trois événements du même service ou type de source.
2. Vérifiez sans recopier leur contenu la présence et l'homogénéité des champs.

| Champ | Présent et homogène ? | Amélioration potentielle |
|---|:---:|---|
| timestamp |  |  |
| message |  |  |
| service |  |  |
| status |  |  |
| env |  |  |
| version |  |  |
| trace/span |  |  |

### Quelle observation est acceptable si `env` ou `version` manque ?

**Réponse :** **non observé dans les trois événements et la vue consultés**.

**Pourquoi :** c'est un écart potentiel de corrélation à confirmer, pas la preuve d'une mauvaise configuration générale.

## Étape 10 — Identifier un motif répétitif

1. Recherchez visuellement un message court et répétitif, sans recopier son contenu réel.
2. Notez sa catégorie probable : health check, accès nominal, message d'Agent ou autre.
3. Listez les usages qu'il pourrait encore servir.

### Peut-on supprimer immédiatement un motif répétitif ?

**Réponse :** non. Il faut mesurer son volume, identifier ses consommateurs, préserver les erreurs et prévoir un retour arrière.

**Pourquoi :** un message bruyant peut encore alimenter une détection de disponibilité, un audit ou une investigation rare.

# Partie 3 — Concevoir le format PeopleShop

À partir d'ici, utilisez uniquement `orders-api`, `env:training` et les identifiants fictifs fournis.

## Étape 11 — Transformer le log legacy

Événement source :

```text
2026-07-16 10:31:14 ERROR Order validation timed out tenant_tier=enterprise db_pool_wait_ms=1820 status=504
```

Produisez puis comparez votre résultat à la référence.

```json
{
  "timestamp": "2026-07-16T10:31:14.000Z",
  "status": "error",
  "message": "Order validation timed out while waiting for database",
  "service": "orders-api",
  "env": "training",
  "version": "2.4.0",
  "dd.trace_id": "training-trace-slow",
  "dd.span_id": "training-span-db",
  "tenant_tier": "enterprise",
  "db.pool.wait_ms": 1820,
  "http.status_code": 504
}
```

### Pourquoi privilégier JSON à la source ?

**Réponse :** JSON fournit des noms et types explicites, réduit les parsers fragiles et facilite une convention commune entre services.

**Limite :** JSON ne garantit pas à lui seul la qualité : les clés, types, données sensibles et valeurs doivent rester gouvernés.

## Étape 12 — Vérifier le rôle des champs

| Champ | Réponse de référence |
|---|---|
| `service/env/version` | filtrer et comparer le même déploiement entre signaux |
| `dd.trace_id/dd.span_id` | ouvrir la trace et le span associés |
| `tenant_tier` | comparer des catégories métier bornées |
| `db.pool.wait_ms` | mesurer et agréger l'attente du pool |
| `http.status_code` | filtrer et regrouper les réponses HTTP |

### Pourquoi `1820` et `504` doivent-ils rester numériques ?

**Réponse :** un type numérique permet des comparaisons, seuils et agrégations cohérents.

**Pourquoi :** une chaîne de caractères est principalement recherchable comme texte et peut produire des tris incorrects.

## Étape 13 — Exclure les données sensibles

1. Vérifiez l'absence de nom, email, adresse, secret, token et donnée de paiement.
2. Vérifiez que le message ne contient pas de contenu libre utilisateur inutile.
3. Classez `order_id` comme attribut de recherche seulement si l'usage et la protection sont validés.

### Pourquoi `customer_email` ne doit-il pas devenir une facette ?

**Réponse :** c'est une donnée personnelle et de forte cardinalité qui n'est pas nécessaire à l'analyse opérationnelle proposée.

**Pourquoi :** la collecte minimale réduit le risque de confidentialité et évite une dimension coûteuse et peu agrégable.

## Étape 14 — Préserver une stack trace

Considérez des événements dont chaque première ligne commence par un timestamp ISO, suivie éventuellement de lignes `#0`, `#1`, etc.

```text
Début d'événement : ligne commençant par le motif de timestamp validé
Lignes suivantes : rattachées à l'événement précédent jusqu'au prochain timestamp
```

### Pourquoi faut-il regrouper les lignes d'une stack trace ?

**Réponse :** pour conserver l'exception, son message et sa pile dans un seul événement logique.

**Pourquoi :** sans regroupement, le volume est artificiellement multiplié, les recherches sont fragmentées et une exception peut déclencher plusieurs alertes.

**Limite :** le motif doit être testé sur les vrais formats ; une regex trop large ou trop restrictive fusionne ou découpe incorrectement les événements.

# Partie 4 — Concevoir le pipeline logique

## Étape 15 — Choisir le filtre d'entrée

```text
service:orders-api
```

### Le filtre peut-il utiliser un champ créé par un parser du même pipeline ?

**Réponse :** non. Le filtre est évalué avant les processeurs.

**Pourquoi :** il doit utiliser un contexte déjà présent à l'ingestion, par exemple `service` ou `source`.

## Étape 16 — Ordonner les processeurs

| Ordre | Opération | Résultat attendu |
|---:|---|---|
| 1 | parser JSON ou texte | extraire les champs et identifier les échecs |
| 2 | remapper la date | définir le timestamp officiel et maîtriser le fuseau |
| 3 | remapper le statut | convertir `ERROR` vers `error` |
| 4 | normaliser les attributs réservés | aligner service, message, trace et types |
| 5 | masquer les séquences sensibles | éviter leur exposition après traitement |
| 6 | enrichir ou catégoriser | ajouter seulement des catégories bornées utiles |

### Pourquoi l'ordre des processeurs est-il important ?

**Réponse :** chaque processeur ne peut utiliser que les champs déjà présents ou créés par les étapes précédentes.

**Limite :** le masquage peut devoir intervenir plus tôt selon l'architecture ; aucune donnée sensible ne doit attendre inutilement avant d'être protégée.

## Étape 17 — Tester conceptuellement le pipeline

| Cas de test | Résultat attendu |
|---|---|
| événement d'un autre service | non traité par ce pipeline |
| JSON PeopleShop valide | champs normalisés et typés |
| texte legacy conforme | champs extraits puis remappés |
| format non reconnu | échec identifiable, événement non silencieusement corrompu |
| séquence sensible | valeur masquée selon la règle validée |

### Pourquoi conserver un cas de format non reconnu ?

**Réponse :** pour détecter les régressions de format plutôt que perdre ou mal classer silencieusement des événements.

# Partie 5 — Choisir facettes, mesures et attributs

## Étape 18 — Classer les attributs

| Attribut | Classement de référence | Justification |
|---|---|---|
| `service` | attribut standard et facette | filtrage commun |
| `env` | attribut standard et facette | séparation des contextes |
| `version` | facette si comparaison nécessaire | comparaison de déploiements |
| `tenant_tier` | facette bornée | segmentation métier contrôlée |
| `http.status_code` | facette | regroupement des réponses |
| `db.pool.wait_ms` | mesure numérique | agrégation de l'attente |
| `order_id` | attribut de recherche si autorisé | presque unique, pas facette par défaut |
| `customer_email` | exclu | donnée personnelle |

### Quelle différence existe entre une facette et une mesure ?

**Réponse :** une facette sert à filtrer et regrouper des catégories ; une mesure permet d'agréger une valeur numérique.

**Pourquoi :** `tenant_tier` crée quelques groupes interprétables, tandis que `db.pool.wait_ms` peut être moyenné, classé ou analysé par percentile selon les capacités disponibles.

## Étape 19 — Vérifier la cardinalité et l'usage

1. Pour chaque facette proposée, écrivez la question qu'elle permet de traiter.
2. Retirez toute facette sans usage défini.
3. Vérifiez que les valeurs attendues sont bornées.

### Pourquoi ne faut-il pas créer une facette pour chaque champ ?

**Réponse :** parce que les facettes augmentent la surface de gouvernance et doivent servir des recherches ou agrégations réelles.

# Partie 6 — Maîtriser le cycle économique du log

## Étape 20 — Remettre les étapes dans l'ordre

```text
source → collecte/ingestion → traitement → indexation
       → recherche et analyse
       → archivage éventuel / métrique dérivée selon la configuration
```

### Une exclusion d'index empêche-t-elle l'ingestion ?

**Réponse :** non. L'événement a déjà été reçu ; l'exclusion agit sur son indexation selon la règle configurée.

**Pourquoi :** réduire l'indexation ne supprime pas automatiquement le coût, le risque ou les autres usages liés à l'ingestion.

**Limite :** les possibilités de Live Tail, d'archivage ou de métriques dérivées dépendent de la configuration réelle.

## Étape 21 — Réduire les health checks

Complétez la décision en utilisant la référence suivante.

| Décision | Réponse de référence |
|---|---|
| Motif | succès nominaux répétitifs de health checks |
| Point préféré | réduire à la source ou éviter une collecte en double |
| Événements conservés | tous les échecs, changements d'état et échantillon de validation |
| Remplacement possible | métrique de disponibilité et compteur d'échecs |
| Preuve | comparaison des volumes et validation des recherches/monitors dépendants |
| Retour arrière | désactiver la règle et vérifier le retour des événements |

### Quel est l'ordre de préférence pour réduire le bruit ?

**Réponse :** réduire l'inutile à la source, supprimer les doublons de collecte, filtrer ou échantillonner avant envoi si autorisé, puis seulement agir sur l'indexation selon les usages.

**Pourquoi :** une action plus proche de la source évite de transporter et traiter un événement inutile.

**Limite :** n'échantillonnez jamais aveuglément une erreur rare, un changement d'état ou un événement de sécurité.

## Étape 22 — Préparer la recherche d'une trace lente

```text
service:orders-api env:training @dd.trace_id:<trace_id_fictif>
```

### Comment retrouver les logs d'une trace lente sans conclure trop vite à la cause ?

**Réponse :** partir de l'identifiant de trace, conserver le même service, environnement et intervalle, examiner la séquence des logs puis revenir aux spans et dépendances contributrices.

**Explication :** un message d'erreur proche de la trace est un indice. La cause doit être confirmée en confrontant chronologie, spans, métriques et comportement de la dépendance.

## Questions de synthèse corrigées

### Pourquoi JSON ne remplace-t-il pas une convention de journalisation ?

**Réponse :** JSON fournit une structure, mais la convention définit les clés, types, valeurs, données interdites et responsabilités communes.

### Quelle information doit exister avant l'entrée dans un pipeline ?

**Réponse :** toute information utilisée par son filtre, par exemple `service` ou `source`.

### Que faut-il préserver lors d'une réduction de logs ?

**Réponse :** les usages démontrés, les erreurs, les transitions d'état, les obligations d'audit, une preuve avant/après et un retour arrière.

## Aide au diagnostic

| Difficulté | Interprétation | Action en lecture seule |
|---|---|---|
| Aucun log | période, droits ou source inactive | vérifier la période ou utiliser les captures fournies |
| Service de référence absent | évolution du flux | choisir une valeur proposée par la facette et l'anonymiser |
| Zéro erreur | période sans événement correspondant | noter le résultat sans généraliser |
| Facette absente | attribut non facetté, droits ou données absentes | écrire **non observé** et poursuivre conceptuellement |
| Log difficile à lire | format texte hétérogène | identifier uniquement les champs visibles |
| Trace non liée | identifiants absents ou corrélation non configurée | noter l'écart potentiel sans inventer de lien |
| Donnée sensible visible | télémétrie réelle | ne pas la recopier et suivre la procédure client de signalement |

## Validation finale

- [ ] Le relevé du Log Explorer est daté et sa période est indiquée.
- [ ] Les recherches réelles sont temporaires et anonymisées.
- [ ] Les absences sont formulées **non observé**, sans généralisation.
- [ ] Le livrable fictif utilise `orders-api`, `training` et `2.4.0`.
- [ ] Le JSON est valide, typé, corrélable et non sensible.
- [ ] La stack trace reste un seul événement logique.
- [ ] Le filtre du pipeline repose sur un champ préexistant.
- [ ] Les processeurs sont ordonnés et testables.
- [ ] Facettes et mesures répondent à un usage réel.
- [ ] Les données personnelles et secrets sont exclus.
- [ ] Ingestion, traitement, indexation et archivage sont distingués.
- [ ] La réduction du bruit préserve les erreurs et possède une preuve et un retour arrière.
- [ ] La corrélation d'une trace ne vaut pas preuve automatique de causalité.
- [ ] Aucune ressource Datadog n'a été modifiée.
