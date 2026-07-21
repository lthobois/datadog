---
title: "Atelier 10 — Construire et investiguer un scénario de logs contrôlé"
subtitle: "Document participant autonome"
lang: fr-FR
---

# Construire et investiguer un scénario de logs contrôlé

## Objectif

Créer un petit jeu de logs structuré dans votre service pédagogique, vérifier qu'il alimente le dashboard construit au module 9, puis l'utiliser pour conduire une investigation factuelle.

## Contexte

Vous devez vérifier pourquoi une opération de validation présente quelques événements lents et en erreur. Le scénario est explicitement pédagogique : il ne représente aucun incident de production et ne doit jamais être rapproché des données APM ou RUM réelles sans identifiant commun vérifié.

Les logs sont envoyés dans le service unique créé au module 4 :

```text
training-<participant>-20260720-svc
```

Ils portent le scénario :

```text
training_scenario:checkout-investigation
```

## Livrables

À la fin de l'atelier, vous disposez :

- de neuf événements pédagogiques structurés et isolés par votre service ;
- d'une preuve d'acceptation HTTP `202` ;
- d'une vue enregistrée `[TRAINING] M10 Investigation - <participant> - 20260720` ;
- d'un journal d'investigation distinguant faits, hypothèses, tests et conclusion ;
- de la preuve que le widget Logs du dashboard du module 9 est alimenté.

## Règles de sécurité

L'organisation Datadog est partagée et correspond à la production. Les seules écritures autorisées sont :

1. l'envoi des neuf logs pédagogiques vers votre service `training-*` ;
2. la création de votre propre Saved View préfixée `[TRAINING] M10`.

Ne créez et ne modifiez aucun pipeline, index, filtre d'exclusion, facette globale, métrique, monitor, dashboard, incident ou ressource applicative. Ne modifiez aucun log existant.

La clé API autorisée est un secret. Ne l'inscrivez pas dans ce document, ne la communiquez pas et ne sauvegardez pas le script qui la contient.

## Prérequis

- Chrome connecté à l'organisation Datadog de la formation ;
- accès à **Logs > Live Tail** et **Logs > Log Explorer** ;
- droit de créer une Saved View ;
- PowerShell ISE disponible ;
- service `training-<participant>-20260720-svc` utilisé aux modules 4 et 5 ;
- clé API autorisée par le formateur, identifiée au module 5 par le Key ID `650b6239-78e6-46c1-8233-749ae21ae904`.

Si la valeur de la clé n'est pas accessible, ne créez pas de clé. Le formateur exécute l'envoi avec votre identifiant, puis vous poursuivez dans le Log Explorer.

# Partie 1 — Produire le jeu de données pédagogique

## Étape 1 — Comprendre les données envoyées

Le lot contient neuf opérations réparties comme suit :

| Résultat | Statut Datadog | Nombre | Durées en millisecondes |
|---|---|---:|---|
| `success` | `info` | 5 | 110 à 155 |
| `slow` | `warn` | 2 | 820 et 940 |
| `failure` | `error` | 2 | 1200 et 1450 |

Chaque événement contient également :

- `training_scenario` : identifie le scénario ;
- `operation` : vaut `checkout` ;
- `duration_ms` : durée numérique ;
- `result` : résultat fonctionnel ;
- `sequence` : ordre pédagogique de 1 à 9 ;
- `participant` : identifiant utilisé dans le nom du service.

**Résultat attendu :** vous savez que le scénario comporte cinq succès rapides, deux opérations lentes et deux échecs.

**Vérification :** le total attendu est `5 + 2 + 2 = 9` événements.

**Interprétation :** les valeurs sont structurées dès l'émission. Aucun pipeline Grok n'est nécessaire pour les exploiter.

## Étape 2 — Préparer le script PowerShell

1. Ouvrez **Windows PowerShell ISE**.
2. Créez un nouveau script sans l'enregistrer.
3. Collez le script suivant :

```powershell
$apiKey = "COLLER_LA_VALEUR_SECRETE_ICI"
$participant = "REMPLACER_PAR_VOTRE_IDENTIFIANT"
$serviceName = "training-$participant-20260720-svc"

$events = @(
    @{ sequence = 1; duration_ms = 110;  result = "success"; status = "info"  }
    @{ sequence = 2; duration_ms = 125;  result = "success"; status = "info"  }
    @{ sequence = 3; duration_ms = 820;  result = "slow";    status = "warn"  }
    @{ sequence = 4; duration_ms = 140;  result = "success"; status = "info"  }
    @{ sequence = 5; duration_ms = 1200; result = "failure"; status = "error" }
    @{ sequence = 6; duration_ms = 155;  result = "success"; status = "info"  }
    @{ sequence = 7; duration_ms = 940;  result = "slow";    status = "warn"  }
    @{ sequence = 8; duration_ms = 1450; result = "failure"; status = "error" }
    @{ sequence = 9; duration_ms = 118;  result = "success"; status = "info"  }
)

$payload = @(
    foreach ($event in $events) {
        @{
            message           = "TRAINING checkout sequence=$($event.sequence) result=$($event.result) duration_ms=$($event.duration_ms)"
            service           = $serviceName
            ddsource          = "powershell-training"
            ddtags            = "env:training,training:true,training_session:20260720,participant:$participant"
            status            = $event.status
            training_scenario = "checkout-investigation"
            operation         = "checkout"
            duration_ms       = $event.duration_ms
            result            = $event.result
            sequence          = $event.sequence
            participant       = $participant
        }
    }
) | ConvertTo-Json -Depth 4

$response = Invoke-WebRequest `
    -UseBasicParsing `
    -Method Post `
    -Uri "https://http-intake.logs.datadoghq.eu/api/v2/logs" `
    -Headers @{ "DD-API-KEY" = $apiKey } `
    -ContentType "application/json" `
    -Body $payload

Write-Host "Code HTTP :" $response.StatusCode
Write-Host "Service :" $serviceName
Write-Host "Événements envoyés :" $events.Count
```

4. Remplacez `COLLER_LA_VALEUR_SECRETE_ICI` par la valeur secrète autorisée.
5. Remplacez `REMPLACER_PAR_VOTRE_IDENTIFIANT` par l'identifiant utilisé depuis le module 4.
6. Vérifiez la valeur de `$serviceName` avant l'exécution.

**Résultat attendu :** le script cible exactement votre service pédagogique et contient neuf événements.

**Vérification :** aucune valeur `COLLER_...` ou `REMPLACER_...` ne subsiste.

**Interprétation :** le nom unique du service et le tag `participant` isolent votre lot de celui des autres participants.

## Étape 3 — Envoyer les événements

1. Exécutez le script une seule fois avec `F5`.
2. Lisez les trois lignes affichées dans la console.

**Résultat attendu :**

```text
Code HTTP : 202
Service : training-<participant>-20260720-svc
Événements envoyés : 9
```

**Vérification :** le code HTTP vaut `202` et le nombre annoncé vaut `9`.

**Interprétation :** `202 Accepted` confirme l'acceptation du lot par l'API. La présence dans le Log Explorer doit encore être vérifiée.

## Étape 4 — Retirer le secret

1. Fermez le script.
2. À la demande d'enregistrement, choisissez **Ne pas enregistrer**.
3. Vérifiez qu'aucun fichier `.ps1` n'a été créé.

**Résultat attendu :** la clé n'est conservée dans aucun fichier pédagogique.

**Interprétation :** dans un usage de production, la clé devrait être injectée depuis un gestionnaire de secrets.

# Partie 2 — Retrouver et qualifier le scénario

## Étape 5 — Vérifier l'arrivée dans Live Tail

1. Ouvrez **Logs > Live Tail**.
2. Saisissez la requête suivante en remplaçant `<participant>` :

   ```text
   service:training-<participant>-20260720-svc source:powershell-training @training_scenario:checkout-investigation
   ```

3. Attendez quelques secondes.
4. Ouvrez un événement et vérifiez `service`, `status`, `training_scenario`, `operation`, `duration_ms`, `result` et `sequence`.

**Résultat attendu :** les événements les plus récents appartiennent à votre service et au scénario `checkout-investigation`.

**Vérification :** l'un des événements porte `sequence:9`.

**Interprétation :** Live Tail confirme l'arrivée du flux, mais pas sa conservation dans un index.

## Étape 6 — Vérifier l'indexation dans le Log Explorer

1. Ouvrez **Logs > Log Explorer**.
2. Sélectionnez **Past 15 Minutes**, puis **Past 1 Hour** si nécessaire.
3. Utilisez la même requête :

   ```text
   service:training-<participant>-20260720-svc source:powershell-training @training_scenario:checkout-investigation
   ```

4. Affichez les colonnes **Date**, **Service**, **Source**, **Status** et **Content**.
5. Ouvrez plusieurs lignes pour vérifier les attributs structurés.

**Résultat attendu :** neuf événements sont visibles si le script n'a été exécuté qu'une fois.

**Vérification :** filtrez temporairement avec `@sequence:1`, puis `@sequence:9`. Chaque filtre doit retrouver l'événement correspondant.

**Interprétation :** un total supérieur à neuf indique que le script a été exécuté plusieurs fois. Ce n'est pas une erreur d'ingestion ; utilisez la période la plus courte couvrant votre dernier envoi.

# Partie 3 — Conduire l'investigation

## Étape 7 — Établir le fait de départ

Conservez la requête générale et complétez :

| Élément | Observation |
|---|---|
| Période exacte |  |
| Nombre total d'événements |  |
| Nombre d'événements `error` |  |
| Nombre d'événements `warn` |  |
| Nombre d'événements `info` |  |

Utilisez successivement les filtres suivants pour compter les états :

```text
status:error
```

```text
status:warn
```

```text
status:info
```

**Réponse de référence pour une exécution :** deux erreurs, deux avertissements et cinq événements informatifs.

**Interprétation :** le fait démontré concerne la répartition des logs pédagogiques. Il ne démontre pas une dégradation APM ou un impact utilisateur réel.

## Étape 8 — Tester l'hypothèse de durée

1. Revenez à la requête générale.
2. Ajoutez `@result:failure`.
3. Relevez les deux valeurs de `duration_ms`.
4. Remplacez le filtre par `@result:slow` et relevez les valeurs.
5. Remplacez-le par `@result:success` et comparez les valeurs.

| Résultat | Durées attendues |
|---|---|
| `success` | 110, 118, 125, 140 et 155 ms |
| `slow` | 820 et 940 ms |
| `failure` | 1200 et 1450 ms |

**Résultat attendu :** les événements en erreur sont les plus lents du lot ; les avertissements sont intermédiaires.

**Vérification :** la plus grande durée vaut `1450` et appartient à `result:failure`.

**Interprétation :** le jeu de données démontre une association construite entre durée et résultat. Il ne permet pas d'établir une causalité technique, car aucune dépendance ni trace n'est présente dans ces logs.

## Étape 9 — Tenir le journal d'investigation

Complétez le journal :

| Fait vérifié | Hypothèse | Test | Résultat | Conclusion |
|---|---|---|---|---|
| Deux événements portent `status:error` | Les échecs sont associés aux durées les plus élevées | Comparer `duration_ms` par `result` | Les échecs valent 1200 et 1450 ms | Hypothèse conservée dans le scénario |
| Deux événements portent `status:warn` | Ils représentent une zone intermédiaire | Comparer avec succès et échecs | 820 et 940 ms | Hypothèse conservée |
| Aucun identifiant de trace n'est fourni | Le pivot vers une trace réelle n'est pas démontrable | Rechercher `trace_id` et les pivots proposés | Aucun identifiant commun prévu | Corrélation APM non démontrée |

**Pourquoi la conclusion reste-t-elle limitée ?**

**Réponse :** les logs démontrent ce qu'ils contiennent : résultat, durée et ordre. Ils ne prouvent ni la cause de la durée, ni un impact RUM, ni une relation avec `eu-interfaces` ou `peopulse`.

# Partie 4 — Alimenter le dashboard du module 9

## Étape 10 — Vérifier le dashboard construit au module 9

1. Ouvrez votre dashboard :

   ```text
   [TRAINING] M09 - <participant> - <AAAAMMJJ>
   ```

2. Choisissez une période couvrant l'envoi, par exemple **Past 15 Minutes** ou **Past 1 Hour**.
3. Repérez le widget **Logs — activité du scénario d'investigation**.
4. Vérifiez que le widget n'est plus vide.
5. Utilisez le pivot du widget vers le Log Explorer si l'interface le propose.

**Résultat attendu :** le widget affiche les événements envoyés, sans qu'il soit nécessaire de modifier sa requête.

**Vérification :** la requête transmise au Log Explorer contient votre service et `@training_scenario:checkout-investigation`.

**Interprétation :** le dashboard interroge les logs au moment de l'affichage. Une requête peut donc être configurée avant l'arrivée des données puis se peupler automatiquement lorsque la télémétrie correspondante est indexée.

## Étape 11 — Enregistrer la vue d'investigation

1. Revenez à la requête générale sans filtre de statut ou de résultat :

   ```text
   service:training-<participant>-20260720-svc source:powershell-training @training_scenario:checkout-investigation
   ```

2. Choisissez une période relative couvrant l'envoi.
3. Ouvrez **Views**, puis **Save as new view**.
4. Saisissez :

   ```text
   [TRAINING] M10 Investigation - <participant> - 20260720
   ```

5. Laissez le champ d'équipe vide, sauf consigne du formateur.
6. Enregistrez une seule fois.

**Résultat attendu :** votre Saved View retrouve uniquement les logs du scénario et de votre service.

**Vérification :** rouvrez la vue et contrôlez la requête complète.

**Interprétation :** la vue mémorise le point d'entrée de l'investigation. Elle ne transforme pas les logs et ne modifie aucune configuration partagée.

## Étape 12 — Interpréter les widgets Logs

Comparez les lectures suivantes dans le dashboard ou le Log Explorer :

| Widget | Requête | Agrégation | Question |
|---|---|---|---|
| Activité du scénario | requête générale | Count en Timeseries | Quand les événements pédagogiques ont-ils été reçus ? |
| Échecs du scénario | requête générale + `status:error` | Count en Query Value | Combien d'échecs sont visibles sur la période ? |
| Répartition par statut | requête générale | Count groupé par Status | Quelle est la répartition info, warn et error ? |

**Réponse de référence pour une seule exécution :** neuf événements au total, dont deux erreurs, deux avertissements et cinq événements informatifs.

**Limite :** si plusieurs envois sont présents dans la période, les valeurs sont multipliées. Réduisez la période ou indiquez explicitement le nombre d'exécutions ; ne supprimez aucun log.

# Partie 5 — Contrôler et nettoyer

## Étape 13 — Effectuer la validation finale

- [ ] Le service commence par `training-` et contient votre identifiant.
- [ ] Le scénario vaut `checkout-investigation`.
- [ ] L'API a répondu `202`.
- [ ] Les événements sont visibles dans le Log Explorer.
- [ ] Les faits, hypothèses et conclusions sont distingués.
- [ ] Aucune corrélation avec l'APM ou le RUM réel n'est affirmée.
- [ ] La vue commence par `[TRAINING] M10`.
- [ ] Le widget Logs du dashboard du module 9 affiche le scénario.
- [ ] Aucun pipeline, index, monitor ou dashboard n'a été modifié.

## Étape 14 — Identifier la ressource à supprimer

À la fin de la formation, supprimez uniquement votre Saved View si le formateur vous l'autorise :

```text
[TRAINING] M10 Investigation - <participant> - 20260720
```

Les logs suivent la politique de rétention de l'organisation et ne doivent pas être supprimés individuellement.

## Aide au diagnostic

### Le code HTTP n'est pas `202`

- vérifiez que la valeur secrète, et non le Key ID, a été utilisée ;
- vérifiez l'URL EU `https://http-intake.logs.datadoghq.eu/api/v2/logs` ;
- vérifiez que le JSON est produit avec `ConvertTo-Json -Depth 4` ;
- ne créez pas une nouvelle clé.

### Aucun log n'est visible

- commencez par **Live Tail** ;
- vérifiez le nom exact du service et l'identifiant participant ;
- retirez temporairement `@training_scenario:checkout-investigation` pour diagnostiquer un attribut absent ;
- élargissez la période à **Past 1 Hour** ;
- ne modifiez aucun index ou filtre d'exclusion.

### Plus de neuf logs sont visibles

Le script a probablement été exécuté plusieurs fois ou la période inclut un envoi antérieur. La plateforme fonctionne correctement. Réduisez la période et notez le nombre d'exécutions dans votre conclusion.

## Références officielles

- [Datadog — Send Logs API](https://docs.datadoghq.com/api/latest/logs/send-logs/)
- [Datadog — Log Search Syntax](https://docs.datadoghq.com/logs/explorer/search_syntax/)
- [Datadog — Saved Views](https://docs.datadoghq.com/logs/explorer/saved_views/)
- [Datadog — Monitor Logs with Dashboards](https://docs.datadoghq.com/logs/explorer/analytics/visualize/)
