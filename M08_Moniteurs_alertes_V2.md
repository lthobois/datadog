---
title: "Atelier 8 — Créer et tester un monitor de logs pédagogique"
subtitle: "Document participant autonome"
lang: fr-FR
---

# Créer et tester un monitor de logs pédagogique

## Objectif

Créer un monitor de logs strictement limité à votre service pédagogique, déclencher son évaluation avec un événement contrôlé, observer son cycle d'état, puis préparer son intégration au dashboard du module 9.

## Contexte

Vous travaillez dans une organisation Datadog partagée correspondant à la production. Le service de formation créé au module 4 et alimenté au module 5 permet de pratiquer sans modifier les services existants :

```text
training-<participant>-20260720-svc
```

Le monitor recherche uniquement un log portant le scénario :

```text
monitor-check
```

Le test ne représente pas un incident de production. Il sert à observer la relation entre une requête, une fenêtre d'évaluation, un seuil et un état Datadog.

## Livrables

À la fin de l'atelier, vous disposez :

- d'un monitor unique `[TRAINING] M08 Log errors - <participant> - 20260720` ;
- d'un monitor sans destinataire ni intégration de notification ;
- d'un log d'erreur pédagogique ayant déclenché le monitor ;
- d'une observation des états `OK`, `Alert`, puis `OK` après expiration de la fenêtre ;
- des tags et du nom exact permettant au dashboard du module 9 d'afficher ce monitor ;
- du nom de la ressource à supprimer en fin de formation.

## Règles de sécurité

Les seules écritures autorisées sont :

1. la création de votre propre monitor préfixé `[TRAINING] M08` ;
2. l'envoi d'un unique log de test dans votre service `training-*` ;
3. la suppression de votre monitor en fin de formation, uniquement avec l'autorisation du formateur.

Ne modifiez, ne dupliquez, ne désactivez, ne mutez et ne supprimez aucun monitor existant. Ne configurez aucune adresse, mention `@`, intégration, workflow, case management, webhook ou notification externe. Ne créez aucun downtime.

La clé API autorisée reste secrète. Ne la communiquez pas, ne faites aucune capture de sa valeur et ne sauvegardez pas le script qui la contient.

## Prérequis

- Chrome connecté à l'organisation Datadog de la formation ;
- accès en lecture à **Logs > Log Explorer** et **Monitors > Manage Monitors** ;
- droit de créer et supprimer son propre monitor ;
- PowerShell ISE disponible ;
- service `training-<participant>-20260720-svc` utilisé aux modules 4 et 5 ;
- clé API autorisée par le formateur, identifiée au module 5 par le Key ID `650b6239-78e6-46c1-8233-749ae21ae904`.

Si vous ne disposez pas du droit de créer un monitor, ne demandez pas d'élévation de privilèges pendant l'atelier. Préparez la fiche de configuration avec les valeurs ci-dessous, puis observez le monitor créé par le formateur pour le groupe.

# Partie 1 — Préparer la détection

## Étape 1 — Vérifier que le périmètre est vide

1. Ouvrez **Logs > Log Explorer**.
2. Sélectionnez **Past 15 Minutes**.
3. Remplacez `<participant>` et saisissez :

   ```text
   service:training-<participant>-20260720-svc source:powershell-training @training_scenario:monitor-check status:error
   ```

4. Vérifiez le nom du service.
5. Notez le nombre d'événements retournés.

**Résultat attendu :** aucun événement récent ne correspond avant le test.

**Vérification :** la liste est vide ou le compteur vaut zéro.

**Interprétation :** la requête est isolée par le service unique, la source, le scénario et le statut. Si un ancien test est visible, réduisez la période à une fenêtre ne contenant aucun événement ; ne supprimez aucun log.

## Étape 2 — Définir la question du monitor

Le monitor répond à la question :

> Au moins un log d'erreur du scénario `monitor-check` a-t-il été indexé pour mon service pédagogique pendant les cinq dernières minutes ?

La configuration attendue est :

| Élément | Valeur |
|---|---|
| Source | Logs indexés |
| Requête | service, source, scénario et statut du participant |
| Mesure | Count |
| Groupement | aucun |
| Stratégie | un état global, obtenu sans groupement |
| Fenêtre | cinq dernières minutes |
| Condition | au-dessus de `0` |
| Données absentes | évaluer comme zéro |
| Destinataire | aucun |

**Résultat attendu :** la condition est comprise avant l'ouverture de l'éditeur.

**Interprétation :** un comptage supérieur à zéro déclenche l'alerte. L'absence de log est interprétée comme zéro et conduit donc à l'état `OK`.

# Partie 2 — Créer le monitor

## Étape 3 — Ouvrir l'éditeur de monitor Logs

1. Revenez dans **Logs > Log Explorer** avec la requête exacte validée à l'étape 1.
2. Vérifiez une dernière fois que la barre de recherche contient votre service, la source `powershell-training`, le scénario `monitor-check` et `status:error`.
3. Ouvrez le menu **More internal actions** associé à la recherche.
4. Cliquez sur **Create monitor**.
5. Vérifiez que l'éditeur indique une source Logs et non Metrics, APM ou RUM.

**Résultat attendu :** la page de création d'un Log Monitor est ouverte.

**Vérification :** la zone de requête reprend automatiquement les quatre filtres du Log Explorer. Si elle affiche une recherche générale telle que `*`, revenez au Log Explorer et recommencez cette étape ; ne publiez pas le monitor.

**Interprétation :** un Log Monitor évalue uniquement les logs indexés correspondant à sa requête.

## Étape 4 — Configurer la requête

1. Dans la zone de recherche, vérifiez la requête préremplie en remplaçant mentalement `<participant>` par votre identifiant :

   ```text
   service:training-<participant>-20260720-svc source:powershell-training @training_scenario:monitor-check status:error
   ```

2. Ne modifiez pas la requête si elle correspond exactement à cette valeur.
3. Conservez **Show Count of all logs**.
4. Ne sélectionnez aucune facette, mesure ou valeur unique.
5. Conservez **by (everything)** et ne cliquez pas sur **Add Group By**.

**Résultat attendu :** l'aperçu ne contient aucune donnée récente et la requête cible uniquement votre service.

**Vérification :** le nom d'aucun service de production n'apparaît dans la requête.

**Interprétation :** l'absence de groupement produit un seul état global pour votre monitor et évite de multiplier les groupes d'alerte.

## Étape 5 — Configurer la condition d'alerte

1. Choisissez une fenêtre glissante couvrant les **5 dernières minutes**.
2. Choisissez la condition **above** ou **au-dessus de**.
3. Saisissez le seuil d'alerte :

   ```text
   0
   ```

4. Ne configurez pas de seuil Warning.
5. Ne configurez pas de seuil de récupération spécifique.
6. Dans le traitement des données absentes, choisissez **Evaluate as zero** ou **Évaluer comme zéro**.
7. Ne demandez pas de notification No Data.

**Résultat attendu :** un événement correspondant suffit à placer le monitor en `Alert` ; zéro événement correspond à `OK`.

**Vérification :** la condition affichée est équivalente à « count over last 5 minutes above 0 ».

**Interprétation :** sans seuil de récupération distinct, le monitor revient à `OK` lorsque le comptage repasse à zéro après la sortie de l'événement de la fenêtre.

## Étape 6 — Saisir le nom, le message et les tags

1. Saisissez le nom suivant :

   ```text
   [TRAINING] M08 Log errors - <participant> - 20260720
   ```

2. Remplacez `<participant>` par votre identifiant.
3. Saisissez le message :

   ```markdown
   Monitor pédagogique ORSYS limité au service training-<participant>-20260720-svc.

   Signal : présence d'au moins un log error du scénario monitor-check sur cinq minutes.
   Action : ouvrir le Log Explorer avec la requête du monitor et vérifier le log de test.
   Cette alerte ne correspond pas à un incident de production.
   Ressource à supprimer en fin de formation.
   ```

4. Vérifiez que le message ne contient aucun caractère `@` destiné à une personne ou à une intégration.
5. Ajoutez exactement les tags de monitor suivants :

   ```text
   training:true
   training_module:08
   training_session:20260720
   participant:<participant>
   service:training-<participant>-20260720-svc
   ```

6. Remplacez les deux occurrences de `<participant>`.

**Résultat attendu :** le monitor est identifiable par son nom, sa session, son participant et son service.

**Vérification :** le tag `training_module:08` et le nom `[TRAINING] M08` sont visibles avant l'enregistrement.

**Interprétation :** ces tags permettront au widget Monitor Summary du module 9 de sélectionner le monitor sans afficher ceux des autres participants.

## Étape 7 — Désactiver les sorties non nécessaires

Dans la section **Configure notifications & automations** :

1. ne saisissez aucun destinataire ;
2. ne sélectionnez aucune intégration ;
3. n'ajoutez aucun workflow, case ou automation ;
4. désactivez l'inclusion des échantillons de logs si cette option est proposée ;
5. ne configurez aucune escalade ni renotification.

**Résultat attendu :** le monitor évaluera la condition sans envoyer de notification externe.

**Vérification :** le résumé indique **No recipients in monitor message and no matching rules** et aucune adresse, mention, intégration ou automation n'apparaît.

**Interprétation :** l'état du monitor reste visible dans Datadog et dans un dashboard, même sans routage vers un canal externe.

## Étape 8 — Effectuer la revue avant création

Contrôlez chaque ligne :

| Contrôle | Valeur attendue |
|---|---|
| Nom | `[TRAINING] M08 Log errors - <participant> - 20260720` |
| Service | uniquement votre service `training-*` |
| Scénario | `monitor-check` |
| Statut recherché | `error` |
| Fenêtre | 5 minutes |
| Seuil | `> 0` |
| Groupement | aucun |
| No Data | Evaluate as zero |
| Destinataire | aucun |
| Tags | `training:true`, module, session, participant, service |

Si un contrôle n'est pas conforme, corrigez-le avant de poursuivre.

## Étape 9 — Créer le monitor

1. Cliquez une seule fois sur **Create and Publish**. N'utilisez pas **Save as Draft**.
2. Attendez l'ouverture de la page du monitor.
3. Copiez son nom exact dans votre document de travail.
4. Ne modifiez aucun autre monitor.

**Résultat attendu :** votre monitor existe et son nom commence par `[TRAINING] M08`.

**Vérification :** dans **Manage Monitors**, recherchez son nom exact et vérifiez qu'une seule ligne correspond.

**Interprétation :** avant l'envoi du log, le comptage vaut zéro. Après la première évaluation, l'état attendu est `OK`.

# Partie 3 — Déclencher le monitor avec un log contrôlé

## Étape 10 — Préparer le script

1. Ouvrez **Windows PowerShell ISE** (Sous Windows) ou une **invite de commande shell** (Sous Linux).
2. Collez le script :

```powershell
$apiKey = "COLLER_LA_VALEUR_SECRETE_ICI"
$participant = "REMPLACER_PAR_VOTRE_IDENTIFIANT"
$serviceName = "training-$participant-20260720-svc"

$payload = @{
    message           = "TRAINING monitor-check participant=$participant result=failure"
    service           = $serviceName
    ddsource          = "powershell-training"
    ddtags            = "env:training,training:true,training_session:20260720,participant:$participant"
    status            = "error"
    training_scenario = "monitor-check"
    operation         = "monitor-test"
    result            = "failure"
    participant       = $participant
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
Write-Host "Scénario : monitor-check"
```

```bash
curl -X POST "https://http-intake.logs.datadoghq.eu/api/v2/logs" \
  -H "DD-API-KEY: COLLER_LA_VALEUR_SECRETE_ICI" \
  -H "Content-Type: application/json" \
  -d '{
    "message":"TRAINING monitor-check participant=REMPLACER_PAR_VOTRE_IDENTIFIANT result=failure",
    "service":"training-REMPLACER_PAR_VOTRE_IDENTIFIANT-20260720-svc",
    "ddsource":"powershell-training",
    "ddtags":"env:training,training:true,training_session:20260720,participant:REMPLACER_PAR_VOTRE_IDENTIFIANT",
    "status":"error",
    "training_scenario":"monitor-check",
    "operation":"monitor-test",
    "result":"failure",
    "participant":"REMPLACER_PAR_VOTRE_IDENTIFIANT"
  }'
```

3. Collez la valeur secrète de la clé API autorisée.
4. Remplacez l'identifiant participant.
5. Vérifiez le nom construit dans `$serviceName`.

**Résultat attendu :** le script cible le même service et le même scénario que le monitor.

**Vérification :** aucune valeur `COLLER_...` ou `REMPLACER_...` ne subsiste.

**Interprétation :** l'égalité entre les attributs du log et la requête du monitor est indispensable au déclenchement.

## Étape 11 — Envoyer le log de test

1. Exécutez le script une seule fois.
2. Vérifiez la sortie :

   ```text
   Code HTTP : 202
   Service : training-<participant>-20260720-svc
   Scénario : monitor-check
   ```

3. Fermez le script sans l'enregistrer.

**Résultat attendu :** l'API accepte le log avec le code `202`.

**Vérification :** aucun fichier `.ps1` contenant la clé n'est conservé.

**Interprétation :** l'acceptation HTTP ne prouve pas encore l'indexation ni l'évaluation du monitor.

## Étape 12 — Vérifier le log dans le Log Explorer

1. Revenez au Log Explorer.
2. Sélectionnez **Past 15 Minutes**.
3. Utilisez la requête exacte du monitor.
4. Ouvrez l'événement et vérifiez `service`, `source`, `status` et `training_scenario`.

**Résultat attendu :** un log récent correspond à la requête.

**Vérification :** `status` vaut `error` et `training_scenario` vaut `monitor-check`.

**Interprétation :** le log est indexé et devient éligible à l'évaluation par le Log Monitor.

# Partie 4 — Observer le cycle d'état

## Étape 13 — Observer le passage en Alert

1. Ouvrez **Monitors > Manage Monitors**.
2. Recherchez le nom exact de votre monitor.
3. Ouvrez-le.
4. Actualisez la page jusqu'à la prochaine évaluation, sans modifier la configuration.
5. Relevez l'heure du changement d'état et la valeur évaluée.

**Résultat attendu :** le monitor passe en `Alert` lorsque le comptage sur cinq minutes devient supérieur à zéro.

**Vérification :** le graphe ou l'historique montre au moins un événement correspondant et la condition `> 0`.

**Interprétation :** l'évaluation est périodique ; le passage en `Alert` peut intervenir après l'indexation du log et non exactement au moment de l'appel HTTP.

## Étape 14 — Examiner l'événement du monitor

1. Repérez l'historique ou les événements d'état du monitor.
2. Vérifiez que le message pédagogique est affiché.
3. Vérifiez qu'aucun destinataire ou canal externe n'est mentionné.
4. Utilisez le lien vers les logs si l'interface le propose.

**Résultat attendu :** l'état, la requête et le message permettent de retrouver le log concerné.

**Vérification :** le pivot conserve votre service et le scénario `monitor-check`.

**Interprétation :** un monitor actionnable doit permettre de rejoindre rapidement la preuve détaillée sans exposer de donnée sensible dans son message.

## Étape 15 — Observer la récupération

1. N'envoyez aucun autre log de test.
2. Lorsque l'événement n'appartient plus aux cinq dernières minutes, actualisez la page du monitor.
3. Relevez l'état et l'heure de récupération.

**Résultat attendu :** le comptage revient à zéro et le monitor repasse en `OK` après une nouvelle évaluation.

**Vérification :** l'historique montre une transition `Alert` vers `OK` sans modification manuelle.

**Interprétation :** la récupération est produite par la fenêtre glissante et la condition d'alerte. Elle ne signifie pas qu'une action corrective réelle a été exécutée.

## Étape 16 — Analyser le risque de bruit

Répondez aux questions suivantes :

### Pourquoi ce seuil est-il acceptable dans cet atelier ?

**Réponse :** le scénario est isolé, un seul événement est envoyé volontairement et aucune notification externe n'est configurée. Le seuil permet d'observer rapidement le cycle d'état.

### Pourquoi ce seuil serait-il souvent trop sensible en production ?

**Réponse :** un seul log peut être transitoire, sans impact utilisateur ou déjà pris en charge. Une alerte de production doit être calibrée avec le volume normal, la persistance, la proportion d'erreurs et l'action attendue.

### Pourquoi ne configurer aucun groupement ?

**Réponse :** le monitor couvre un seul service pédagogique et doit produire un seul état global. Un groupement créerait plusieurs états sans bénéfice ici.

### Que signifie `Evaluate as zero` ?

**Réponse :** lorsqu'aucun log ne correspond, Datadog remplace le résultat vide par zéro et le compare au seuil. Avec la condition `> 0`, le résultat est `OK`.

# Partie 5 — Préparer le dashboard du module 9

## Étape 17 — Vérifier les tags du monitor

1. Ouvrez la page de votre monitor.
2. Vérifiez la présence de :

   ```text
   training:true
   training_module:08
   training_session:20260720
   participant:<participant>
   service:training-<participant>-20260720-svc
   ```

3. Corrigez uniquement votre monitor si un tag manque.
4. N'ajoutez aucun tag à un monitor existant.

**Résultat attendu :** les tags permettent de distinguer votre monitor de tous les autres.

**Vérification :** une recherche dans **Manage Monitors** avec votre nom exact ne retourne qu'un monitor.

**Interprétation :** le dashboard pourra filtrer la liste des monitors sans dépendre de leur position dans l'interface.

## Étape 18 — Conserver les paramètres du widget Monitor Summary

Pour le module 9, conservez :

| Paramètre | Valeur |
|---|---|
| Type de widget | Monitor Summary |
| Type de résumé | Monitor |
| Affichage | Counts and List |
| Monitor recherché | nom exact `[TRAINING] M08 Log errors - <participant> - 20260720` |
| Titre du widget | `Monitors — état de ma détection pédagogique` |

**Résultat attendu :** vous disposez de toutes les valeurs nécessaires pour ajouter le monitor au dashboard sans afficher les monitors des autres participants.

**Interprétation :** le widget présentera l'état courant. Il pourra donc être `OK` au moment du module 9 même si l'historique conserve le passage antérieur en `Alert`.

# Partie 6 — Validation et nettoyage

## Étape 19 — Effectuer la validation finale

- [ ] Le monitor commence par `[TRAINING] M08` et contient votre identifiant.
- [ ] La requête cible uniquement votre service et `monitor-check`.
- [ ] Le monitor n'a aucun groupement.
- [ ] La condition est `count over last 5 minutes > 0`.
- [ ] Les données absentes sont évaluées comme zéro.
- [ ] Aucun destinataire, workflow ou canal externe n'est configuré.
- [ ] Le log de test a reçu une réponse HTTP `202`.
- [ ] Le passage en `Alert` a été observé ou documenté.
- [ ] Le retour en `OK` a été observé ou reste attendu après expiration de la fenêtre.
- [ ] Le nom exact du monitor est conservé pour le module 9.

## Étape 20 — Nettoyer à la fin de la formation

Ne supprimez pas le monitor avant l'atelier du module 9 : son widget Monitor Summary en dépend.

À la fin de la formation, et uniquement avec l'autorisation du formateur :

1. recherchez le nom exact de votre monitor ;
2. vérifiez le préfixe `[TRAINING] M08` et votre identifiant ;
3. supprimez uniquement ce monitor ;
4. ne supprimez aucun autre monitor.

Le log de test suit la politique de rétention de l'organisation et ne doit pas être supprimé individuellement.

## Aide au diagnostic

### Le monitor reste OK après l'envoi

- vérifiez que le log est visible dans le Log Explorer ;
- comparez exactement le service, la source, le scénario et le statut ;
- vérifiez que le log est indexé dans le niveau pris en charge par les Log Monitors ;
- attendez la prochaine évaluation ;
- n'élargissez pas la requête à la production.

### Le monitor affiche No Data

- vérifiez que **Evaluate as zero** a été sélectionné ;
- vérifiez que le monitor utilise un comptage ;
- ne sélectionnez pas **Show NO DATA and notify**.

### Plusieurs monitors sont trouvés

- utilisez le nom exact contenant votre identifiant ;
- vérifiez le tag `participant:<participant>` ;
- ne modifiez pas les monitors des autres participants.

### Le monitor reste Alert

- vérifiez qu'aucun autre participant n'utilise votre identifiant ;
- recherchez de nouveaux logs `monitor-check` dans les cinq dernières minutes ;
- attendez que le dernier événement sorte de la fenêtre et qu'une nouvelle évaluation ait lieu ;
- ne forcez pas la récupération et ne mutez pas le monitor.

## Références officielles

- [Datadog — Log Monitor](https://docs.datadoghq.com/monitors/types/log/)
- [Datadog — Configure Monitors](https://docs.datadoghq.com/monitors/configuration/)
- [Datadog — Recovery Thresholds](https://docs.datadoghq.com/monitors/guide/recovery-thresholds/)
- [Datadog — Monitor Summary Widget](https://docs.datadoghq.com/dashboards/widgets/monitor_summary/)
- [Datadog — Send Logs API](https://docs.datadoghq.com/api/latest/logs/send-logs/)
