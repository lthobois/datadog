---
title: "Module 5 — Atelier : ingérer, structurer et retrouver un log pédagogique"
subtitle: "Document participant autonome"
lang: fr-FR
---

# Ingérer, structurer et retrouver un log pédagogique

## Objectif

Envoyer un événement pédagogique dans Datadog avec PowerShell ou Bash/cURL, vérifier son ingestion, extraire ses valeurs avec une règle Grok exécutée à la requête, distinguer une facette qualitative d'une mesure, puis enregistrer une vue personnelle permettant de retrouver les logs du service créé au module 4.

## Résultat produit

À la fin de l'atelier, vous disposez :

- de logs pédagogiques isolés par le nom unique de votre service ;
- d'une preuve d'acceptation HTTP `202 Accepted` ;
- d'une extraction Grok temporaire faisant apparaître les valeurs du message ;
- d'une identification de `operation` comme dimension qualitative et de `duration_ms` comme mesure potentielle ;
- d'une vue `[TRAINING]` filtrée sur votre service et vos logs pédagogiques ;
- du nom exact de la vue à supprimer en fin de formation.

## Règles de sécurité

Vous travaillez dans une organisation Datadog partagée correspondant à la production. Les seules écritures autorisées dans cet atelier sont :

1. l'envoi de quelques logs pédagogiques via l'API HTTP ;
2. la création ou la correction de votre propre Saved View préfixée `[TRAINING]`.

La clé API dont l'identifiant est fourni reste un secret de production. Son utilisation doit avoir été autorisée par le formateur. Ne la communiquez à personne, ne la recopiez pas dans ce document, ne faites aucune capture de l'écran qui l'affiche et ne sauvegardez pas le script qui la contient.

L'extraction Grok est réalisée avec un **Calculated Field** personnel et temporaire dans le Log Explorer.

## Prérequis

- Chrome connecté à l'organisation Datadog de la formation ;
- accès à **Organization Settings > API Keys** avec le droit de consulter la clé autorisée ;
- accès à **Logs > Live Tail** et **Logs > Log Explorer** ;
- droit de créer une Saved View ;
- PowerShell ISE ou Bash avec cURL disponible sur le poste ;
- service `training-<participant>-<aaaammjj>-svc` créé au module 4 ;
- identifiant de participant autorisé, sans espace ni donnée personnelle inutile.

Si la valeur de la clé n'est pas accessible, demandez au formateur d'exécuter le script pour votre identifiant, puis poursuivez à partir de la recherche dans Live Tail.

## Comprendre le parcours

Le champ JSON `service` envoyé à Datadog doit être exactement égal au nom du service créé au module 4. Cette égalité permet à Datadog de rapprocher le log de la même identité de service.

Le message envoyé reste volontairement textuel :

```text
TRAINING participant=<participant> operation=login duration_ms=128 result=success
```

Grok transforme ensuite temporairement les parties du message en champs utilisables. Cette extraction ne modifie ni le log ingéré ni les pipelines de production.

# Partie 1 — Retrouver la clé API autorisée

## Étape 1 — Ouvrir la gestion des clés

1. Dans Datadog, cliquez sur votre profil en bas à gauche.
2. Ouvrez **Organization Settings**.
3. Dans la section **Access**, ouvrez **API Keys**.
4. Restez sur l'onglet **API Keys** : une Application Key, un Personal Access Token ou un Service Access Token ne remplace pas la clé nécessaire à l'ingestion de logs.

**Résultat attendu :** la liste des API Keys de l'organisation est visible.

**Vérification :** le titre de la page ou de l'onglet contient **API Keys**.

**Interprétation :** une API Key authentifie la soumission de télémétrie. Elle ne donne pas, à elle seule, les droits d'administration associés à un utilisateur.

## Étape 2 — Identifier la clé imposée

1. Recherchez la clé portant exactement le Key ID suivant :

   ```text
   650b6239-78e6-46c1-8233-749ae21ae904
   ```

2. Vérifiez visuellement l'identifiant complet avant de copier la valeur de la clé.
3. Utilisez l'action **Copy key** ou l'action équivalente proposée par Datadog.
4. Ne copiez jamais l'identifiant seul : le script attend la **valeur secrète** de la clé, pas son Key ID.

**Résultat attendu :** la valeur secrète de la clé autorisée est placée temporairement dans le presse-papiers.

**Vérification :** le Key ID affiché reste exactement celui fourni ci-dessus.

**Interprétation :** le Key ID permet d'identifier la bonne ressource ; il ne permet pas d'authentifier l'appel HTTP.

**Si la valeur n'est pas visible :** demandez au formateur d'exécuter l'envoi, puis passez à l'étape suivante.

# Partie 2 — Générer un log avec PowerShell ou Bash

## Étape 3 — Préparer l'envoi

Sous Windows, ouvrez **Windows PowerShell ISE** et exécutez le script suivant :

```powershell
$apiKey = "COLLER_LA_VALEUR_SECRETE_ICI"
$participant = "REMPLACER_PAR_VOTRE_IDENTIFIANT"
$serviceName = "training-$participant-20260720-svc"

$payload = @{
    message  = "TRAINING participant=$participant operation=login duration_ms=128 result=success"
    service  = $serviceName
    ddsource = "powershell-training"
    ddtags   = "env:training,training:true,training_session:20260720,participant:$participant"
    status   = "info"
    duration_ms = 128
} | ConvertTo-Json

$response = Invoke-WebRequest `
    -UseBasicParsing `
    -Method Post `
    -Uri "https://http-intake.logs.datadoghq.eu/api/v2/logs" `
    -Headers @{ "DD-API-KEY" = $apiKey } `
    -ContentType "application/json" `
    -Body $payload

Write-Host "Code HTTP :" $response.StatusCode
Write-Host "Service :" $serviceName
```

Sous Linux ou macOS, exécutez l'équivalent Bash/cURL :

```bash
api_key="COLLER_LA_VALEUR_SECRETE_ICI"
participant="REMPLACER_PAR_VOTRE_IDENTIFIANT"
service_name="training-${participant}-20260720-svc"

curl -sS -o /dev/null -w "Code HTTP : %{http_code}\n" \
  -X POST "https://http-intake.logs.datadoghq.eu/api/v2/logs" \
  -H "DD-API-KEY: ${api_key}" \
  -H "Content-Type: application/json" \
  --data "{\"message\":\"TRAINING participant=${participant} operation=login duration_ms=128 result=success\",\"service\":\"${service_name}\",\"ddsource\":\"powershell-training\",\"ddtags\":\"env:training,training:true,training_session:20260720,participant:${participant}\",\"status\":\"info\",\"duration_ms\":128}"

echo "Service : ${service_name}"
unset api_key
```

Remplacez les deux valeurs génériques et vérifiez que le nom du service correspond à celui créé au module 4.

Exemple de correspondance :

```text
participant : loic-thobois
service     : training-loic-thobois-20260720-svc
```

**Résultat attendu :** le script contient la clé secrète, votre identifiant et le nom exact de votre service.

**Vérification :** aucune valeur `COLLER_...` ou `REMPLACER_...` ne reste dans le script.

**Interprétation :** l'identifiant du participant dans le nom du service isole ses logs de ceux des autres participants. Plusieurs exécutions du même script produisent plusieurs événements dans ce même périmètre pédagogique.

## Étape 4 — Exécuter l'envoi

Exécutez une fois la version adaptée à votre environnement et vérifiez la sortie.

**Résultat attendu :**

```text
Code HTTP : 202
Service : training-<participant>-20260720-svc
```

**Vérification :** le code HTTP vaut `202`.

**Interprétation :** `202 Accepted` confirme que Datadog a accepté la requête pour traitement. Il ne prouve pas encore que le log a été indexé et conservé dans le Log Explorer.

## Étape 5 — Retirer immédiatement le secret

Fermez PowerShell ISE sans enregistrer le script. Sous Bash, la commande `unset api_key` efface la variable de la session.

**Résultat attendu :** la clé n'est plus présente dans le volet de script, dans la variable PowerShell.

**Vérification :** aucun fichier contenant la clé n'a été créé.

**Interprétation :** cette procédure limite l'exposition pédagogique du secret. En production, une clé doit normalement être injectée depuis un gestionnaire de secrets plutôt qu'inscrite dans un script.

# Partie 3 — Vérifier l'ingestion

## Étape 6 — Retrouver l'événement dans Live Tail

1. Dans Datadog, ouvrez **Logs > Live Tail**.
2. Saisissez la requête suivante en remplaçant les valeurs :

   ```text
   service:training-<participant>-20260720-svc source:powershell-training training:true
   ```

3. Attendez quelques secondes et actualisez si nécessaire.
4. Ouvrez l'événement le plus récent.
5. Vérifiez `message`, `service`, `source`, `status` et les tags de formation.

**Résultat attendu :** au moins un événement récent contient votre identifiant et le message commençant par `TRAINING`.

**Vérification :** l'attribut `service` est exactement égal au nom du service créé au module 4.

**Interprétation :** la présence dans Live Tail prouve que le flux est arrivé à l'ingestion. Elle ne garantit pas que l'événement est conservé dans un index.

## Étape 7 — Retrouver l'événement dans le Log Explorer

1. Ouvrez **Logs > Log Explorer**.
2. Choisissez une période relative couvrant l'envoi, par exemple **Past 15 Minutes** ou **Past 1 Hour**.
3. Saisissez la même requête :

   ```text
   service:training-<participant>-20260720-svc source:powershell-training training:true
   ```

4. Ouvrez le résultat le plus récent et comparez ses attributs avec ceux vus dans Live Tail.

**Résultat attendu :** le log est visible dans la liste des événements indexés.

**Vérification :** le message et le service correspondent à votre exécution. Plusieurs lignes identiques sont normales si le script a été exécuté plusieurs fois.

**Interprétation :** la présence dans Log Explorer confirme que l'événement est interrogeable dans la période et le stockage sélectionnés.

**Si le log apparaît dans Live Tail mais pas dans Log Explorer :** l'envoi fonctionne, mais un filtre d'index, une exclusion, un quota, une restriction d'accès ou la période sélectionnée peut empêcher son affichage. Conservez ce constat et prévenez le formateur.

# Partie 4 — Extraire les valeurs du message avec Grok

## Étape 8 — Comprendre la règle utilisée

Le log a été transmis en JSON, mais son champ `message` contient plusieurs valeurs dans une chaîne de caractères. La règle Grok suivante les extrait temporairement :

```grok
TRAINING participant=%{notSpace:participant} operation=%{word:operation} duration_ms=%{integer:duration_ms} result=%{word:result}
```

| Matcher | Valeur extraite | Pourquoi ce matcher ? |
|---|---|---|
| `notSpace` | participant | l'identifiant peut contenir des tirets |
| `word` | opération | `login` est un mot simple |
| `integer` | durée | la valeur doit être interprétée comme un entier |
| `word` | résultat | `success` est un mot simple |

**Résultat attendu :** vous savez quelles parties du message deviendront des champs calculés.

**Interprétation :** Grok est utile lorsque la source fournit du texte stable. Pour une journalisation durable, produire directement des attributs JSON structurés reste préférable.

## Étape 9 — Créer l'extraction Grok personnelle

La fonction **Calculated Fields Extractions** est une fonction Preview. Les libellés peuvent varier et elle peut ne pas être activée dans l'organisation.

1. Conservez dans le Log Explorer la requête portant sur votre service.
2. Cliquez sur **Add** à côté de la barre de recherche.
3. Sélectionnez **Calculated field**.
4. Choisissez le type **Extraction**.
5. Dans **Extract from**, sélectionnez le message complet ou `message`.
6. Utilisez votre log comme **Log sample**.
7. Dans **Parsing rule**, saisissez :

   ```grok
   TRAINING participant=%{notSpace:participant} operation=%{word:operation} duration_ms=%{integer:duration_ms} result=%{word:result}
   ```

8. Vérifiez dans l'aperçu que les quatre valeurs sont extraites.
9. Validez la création du champ calculé.

**Résultat attendu :** les champs `#participant`, `#operation`, `#duration_ms` et `#result` apparaissent dans la session du Log Explorer.

**Vérification :** ouvrez le log et repérez la section des champs calculés, ou observez les colonnes ajoutées automatiquement dans la vue en liste.

**Interprétation :** le préfixe `#` désigne un champ calculé à la requête. Il ne s'agit pas d'un attribut réécrit dans le log.

**Cas observé sur certaines versions de la Preview :** l'aperçu peut rester sur **No value** et le bouton d'enregistrement demeurer désactivé alors que l'échantillon et la règle sont cohérents. Vérifiez une fois que **Extract from** cible bien `message` et que le **Log sample** contient le message complet. Si le résultat reste inchangé, n'insistez pas et ne créez pas de pipeline partagé : notez la limitation de la Preview, passez l'étape 10 et poursuivez à l'étape 11 avec les attributs JSON déjà présents, notamment `@duration_ms`.

## Étape 10 — Filtrer avec le résultat Grok

Réalisez cette étape uniquement si le Calculated Field a pu être créé à l'étape 9.

1. Ajoutez temporairement à la requête :

   ```text
   #participant:<participant> #result:success
   ```

2. Vérifiez que votre événement reste visible.
3. Remplacez temporairement `success` par `failure`.
4. Constatez que l'événement disparaît.
5. Rétablissez `success`.

**Résultat attendu :** le filtre utilisant `#result:success` retrouve l'événement, tandis que `#result:failure` ne le retrouve pas.

**Vérification :** la valeur `#duration_ms` vaut `128` dans les champs calculés.

**Interprétation :** l'extraction rend le contenu textuel filtrable et exploitable sans retraitement à l'ingestion.

# Partie 5 — Distinguer une facette d'une mesure

## Étape 11 — Qualifier les attributs selon leur usage

Dans un log Datadog, une **facette qualitative**, également appelée dimension, sert principalement à filtrer, regrouper et compter des valeurs. Une **mesure** représente une grandeur numérique sur laquelle Datadog peut effectuer des agrégations telles que `avg`, `sum`, `min`, `max` ou un percentile.

Le fait qu'une valeur soit numérique ne suffit pas à en faire une mesure : son rôle dépend de l'usage attendu.

| Champ | Valeur observée | Rôle pertinent | Exploitation attendue |
|---|---:|---|---|
| `operation` ou `#operation` | `login` | Facette qualitative / dimension | filtrer ou regrouper par opération |
| `result` ou `#result` | `success` | Facette qualitative / dimension | compter les succès et les échecs |
| `duration_ms` ou `#duration_ms` | `128` | Mesure | calculer une moyenne, un maximum ou un percentile |
| `http.status_code` | `200` | Dimension numérique | regrouper les réponses 2xx, 4xx ou 5xx |

1. Ouvrez le log pédagogique dans le panneau latéral.
2. Dans **Attributes**, repérez `duration_ms`. La valeur doit être numérique et apparaître sans guillemets ni unité incorporée.
3. Cliquez sur `duration_ms` et observez les actions proposées par Datadog.
4. Si le champ n'est pas déjà configuré, un champ numérique peut proposer **Create facet** et **Create measure**.
5. Repérez ensuite `service`, `status` ou un autre champ qualitatif : il sert de dimension pour filtrer ou regrouper les logs.
6. N'utilisez pas l'action de création dans cet atelier : une facette ou une mesure persistante constitue une configuration partagée de l'organisation.

**Résultat attendu :** vous identifiez `duration_ms` comme une mesure potentielle et les champs catégoriels comme des dimensions.

**Vérification :** `duration_ms` vaut `128` sous la forme d'un nombre. La chaîne `"128 ms"` serait inadaptée à une agrégation numérique directe.

**Interprétation :** le log transporte un attribut et sa valeur ; la configuration Datadog lui attribue ensuite le rôle de dimension ou de mesure. Une dimension répond à « quelles catégories ? », tandis qu'une mesure répond à « combien ? » ou « quelle durée ? ».

> **Important :** une mesure de log n'est pas une métrique Datadog autonome. Elle permet d'agréger une valeur numérique présente dans les logs. La création d'une métrique à partir de logs est une opération distincte.

# Partie 6 — Analyser les filtres des pipelines

## Étape 12 — Lire la liste des pipelines

Un pipeline applique ses processeurs aux logs qui correspondent à son filtre. L'analyse de cette page permet de comprendre le traitement.

1. Dans le menu **Logs**, ouvrez **Configuration**.
2. Dans la section **Processing**, ouvrez **Pipelines**.
3. Repérez le nombre de pipelines actifs et désactivés affiché au-dessus de la liste.
4. Repérez les colonnes **Pipeline Name** et **Filters**.
5. Lisez les filtres de quelques pipelines sans ouvrir leur édition :

   | Pipeline | Filtre observé sur la plateforme |
   |---|---|
   | Nginx | `source:nginx` |
   | PHP | `source:php` |
   | Redis | `source:redis` |
   | N8N | `source:n8n` |
   | Datadog Agent | ensemble de sources Agent |


**Résultat attendu :** la liste des pipelines et leurs filtres sont visibles.

**Vérification :** la colonne **Filters** contient principalement des expressions commençant par `source:`.

**Interprétation :** `source` représente la technologie ou l'origine déclarée du log. Un pipeline d'intégration limite son périmètre aux sources qu'il sait traiter.

## Étape 13 — Déterminer le pipeline applicable au log pédagogique

1. Rappelez la source envoyée par le script PowerShell :

   ```text
   powershell-training
   ```

2. Dans la zone **Filter pipelines**, recherchez successivement :

   ```text
   powershell-training
   ```

   puis :

   ```text
   source:powershell-training
   ```

3. Si aucun pipeline n'est retourné, effacez le filtre pour restaurer la liste complète.
4. Comparez directement la source pédagogique avec les filtres affichés.
5. Vérifiez l'analyse :

   | Question | Réponse vérifiée |
   |---|---|
   | Quelle source porte le log ? | `powershell-training` |
   | Un pipeline personnalisé possède-t-il le filtre `source:powershell-training` ? | Non sur la plateforme vérifiée |
   | Le log est-il malgré tout ingéré et indexé ? | Oui, sa présence dans Live Tail et Log Explorer le prouve |
   | Le message est-il découpé par un pipeline personnalisé ? | Non, il reste textuel |
   | Où les valeurs sont-elles extraites dans l'atelier ? | Dans un Calculated Field Grok exécuté à la requête, si la fonction Preview est opérationnelle |

**Résultat attendu :** aucun des pipelines personnalisés visibles ne correspond à `source:powershell-training`.

**Vérification :** le message du log reste affiché sous la forme complète :

```text
TRAINING participant=<participant> operation=login duration_ms=128 result=success
```

**Interprétation :** l'ingestion, le prétraitement JSON et l'indexation ne nécessitent pas qu'un pipeline personnalisé corresponde au log. En revanche, les processeurs d'un pipeline filtré sur `source:nginx`, `source:php` ou une autre source ne s'appliquent pas à `powershell-training`.

### Pourquoi ne pas créer un pipeline pédagogique ?

**Réponse :** un pipeline est une configuration partagée qui affecte les nouveaux logs correspondant à son filtre. Le créer dans l'organisation de production introduirait une modification durable et un risque de périmètre.

**Explication :** le Calculated Field permet de démontrer Grok de manière personnelle, rétroactive et temporaire. Un pipeline serait pertinent seulement après validation du format, du filtre, des droits, des tests et du retour arrière dans un environnement autorisé.

# Partie 7 — Créer la vue personnelle

## Étape 14 — Préparer la requête durable

Retirez les filtres commençant par `#`. Utilisez pour la vue la requête suivante :

```text
service:training-<participant>-20260720-svc source:powershell-training training:true
```

Conservez une période relative, par exemple **Past 3 Days**, puis préparez l'affichage avec les libellés réellement utilisés dans cette version de Datadog :

1. Cliquez sur **Table Options** au-dessus de la liste des logs.
2. Activez **Date Column** : cette colonne affiche le timestamp du log sous le libellé **Date**.
3. Activez **Content Column** : cette colonne affiche le champ `message` sous le libellé **Content**.
4. Dans **Add a column**, ajoutez **Service** s'il n'est pas déjà présent.
5. Ouvrez de nouveau **Add a column** et ajoutez **Source**.
6. Ouvrez une troisième fois **Add a column** et ajoutez **Status**.
7. Supprimez également les colonnes Grok `#participant`, `#operation`, `#duration_ms` et `#result` si elles sont encore actives.

La table doit donc présenter au minimum :

- **Date**, qui correspond au timestamp réservé ;
- **Service** ;
- **Source** ;
- **Status** ;
- **Content**, qui correspond au message réservé ;

**Pourquoi retirer les champs `#` ?**

**Réponse :** les champs calculés sont temporaires, personnels et ne persistent pas au-delà de la session du Log Explorer. Une Saved View conserve la requête, la période et la présentation, mais ne transforme pas l'extraction Grok en configuration permanente.

## Étape 15 — Enregistrer la Saved View

1. Cliquez sur **Views**.
2. Cliquez sur **Save as new view**.
3. Saisissez le nom :

   ```text
   [TRAINING] M05 Logs API - <participant> - 20260720
   ```

4. Laissez **Team(s)** vide, sauf consigne explicite du formateur.
5. Vérifiez une dernière fois la requête et votre identifiant.
6. Cliquez une seule fois sur **Save**.
7. Attendez la confirmation de l'enregistrement.

**Résultat attendu :** votre vue devient la vue active et son nom commence par `[TRAINING]`.

**Vérification :** rouvrez **Views**, sélectionnez votre vue et contrôlez la présence de la requête sur votre service.

**Interprétation :** la vue mémorise un point d'entrée reproductible vers vos logs pédagogiques. Elle ne modifie ni les logs ni leur traitement.

## Étape 16 — Vérifier le rattachement au service

1. Ouvrez **Developer Portal > Catalog**.
2. Recherchez le nom exact de votre service :

   ```text
   training-<participant>-20260720-svc
   ```

3. Ouvrez sa 'Service Page', puis cliquez sur l'onglet 'Service' en haut à gauche de la page.
4. Consultez les informations de logs.
5. Modifiez l'environnement en haut à gauche pour supprimer les filtres d'environnements (et obtenir Env:All).
6. Choisissez une période relative couvrant l'envoi, par exemple **Past 15 Minutes** ou **Past 1 Hour**.

**Résultat attendu :** la définition du service et le log utilisent le même nom.

**Vérification :** la recherche `service:training-<participant>-20260720-svc` retrouve l'événement dans Log Explorer, même si la fiche Catalog met plus de temps à actualiser son état.

**Interprétation :** un log enrichit la télémétrie Logs du service. Il ne crée pas de traces APM, de dépendances, de latence ni de taux d'erreur APM.

# Partie 8 — Ingérer une métrique évolutive

## Étape 17 — Comprendre le scénario

La métrique `training.checkout.queue_depth` représente le nombre de commandes en attente. C'est un **gauge** : chaque point contient la valeur observée à un instant donné.

Le scénario envoie six points espacés d'une minute :

```text
2 → 4 → 6 → 8 → 5 → 3
```

Les points sont placés dans les cinq dernières minutes afin que la courbe soit immédiatement visible.

## Étape 18 — Envoyer la métrique

Sous Windows, exécutez ce script dans PowerShell ISE après avoir remplacé la clé et l'identifiant :

```powershell
$apiKey = "COLLER_LA_VALEUR_SECRETE_ICI"
$participant = "REMPLACER_PAR_VOTRE_IDENTIFIANT"
$serviceName = "training-$participant-20260720-svc"
$now = [DateTimeOffset]::UtcNow.ToUnixTimeSeconds()
$values = @(2, 4, 6, 8, 5, 3)

$points = for ($index = 0; $index -lt $values.Count; $index++) {
    @{
        timestamp = $now - (($values.Count - 1 - $index) * 60)
        value     = $values[$index]
    }
}

$payload = @{
    series = @(
        @{
            metric = "training.checkout.queue_depth"
            type   = 3
            points = $points
            tags   = @(
                "env:training"
                "service:$serviceName"
                "participant:$participant"
                "training_session:20260720"
                "scenario:checkout"
            )
        }
    )
} | ConvertTo-Json -Depth 6 -Compress

$response = Invoke-WebRequest `
    -UseBasicParsing `
    -Method Post `
    -Uri "https://api.datadoghq.eu/api/v2/series" `
    -Headers @{ "DD-API-KEY" = $apiKey } `
    -ContentType "application/json" `
    -Body $payload

Write-Host "Code HTTP :" $response.StatusCode
Write-Host "Métrique : training.checkout.queue_depth"
Write-Host "Service :" $serviceName
```

Sous Linux ou macOS, utilisez Bash/cURL :

```bash
api_key="COLLER_LA_VALEUR_SECRETE_ICI"
participant="REMPLACER_PAR_VOTRE_IDENTIFIANT"
service_name="training-${participant}-20260720-svc"
now=$(date +%s)

payload=$(printf '{"series":[{"metric":"training.checkout.queue_depth","type":3,"points":[{"timestamp":%s,"value":2},{"timestamp":%s,"value":4},{"timestamp":%s,"value":6},{"timestamp":%s,"value":8},{"timestamp":%s,"value":5},{"timestamp":%s,"value":3}],"tags":["env:training","service:%s","participant:%s","training_session:20260720","scenario:checkout"]}]}' \
  "$((now-300))" "$((now-240))" "$((now-180))" "$((now-120))" "$((now-60))" "$now" "$service_name" "$participant")

curl -sS -o /dev/null -w "Code HTTP : %{http_code}\n" \
  -X POST "https://api.datadoghq.eu/api/v2/series" \
  -H "DD-API-KEY: ${api_key}" \
  -H "Content-Type: application/json" \
  --data "$payload"

echo "Métrique : training.checkout.queue_depth"
echo "Service : ${service_name}"
unset api_key payload
```

**Résultat attendu :** l'API répond avec le code HTTP `202`.

## Étape 19 — Exploiter la métrique

1. Ouvrez **Metrics > Explorer**.
2. Recherchez `training.checkout.queue_depth`.
3. Conservez l'agrégation spatiale **avg by** et le regroupement **(everything)**.
4. Dans **Filters**, ajoutez successivement les tags suivants en validant chaque valeur proposée par Datadog :

   ```text
   env:training
   service:training-<participant>-20260720-svc
   participant:<participant>
   ```

5. Choisissez **Past 15 Minutes**.
6. Observez la progression jusqu'à `8`, puis le retour à `3`.

**Résultat attendu :** la courbe affiche six points et reste sous le seuil critique `10` qui sera utilisé au module 8.

**Interprétation :** la métrique décrit une évolution numérique. Les tags isolent le service et le participant sans créer un nom de métrique différent pour chacun.

# Références officielles

- [Datadog — Send Logs API](https://docs.datadoghq.com/api/latest/logs/send-logs/)
- [Datadog — Calculated Fields](https://docs.datadoghq.com/logs/explorer/calculated_fields/)
- [Datadog — Grok Extractions](https://docs.datadoghq.com/logs/explorer/calculated_fields/extractions/)
- [Datadog — Log Facets and Measures](https://docs.datadoghq.com/logs/explorer/facets/)
- [Datadog — Saved Views](https://docs.datadoghq.com/logs/explorer/saved_views/)
- [Datadog — Set Up Catalog](https://docs.datadoghq.com/internal_developer_portal/catalog/set_up/)
- [Datadog — Submit Metrics](https://docs.datadoghq.com/api/latest/metrics/submit-metrics/)
- [Datadog — Custom Metrics](https://docs.datadoghq.com/metrics/custom_metrics/)
