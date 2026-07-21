---
title: "Atelier 10 — Monitors et alerting sur l'environnement réel"
lang: fr-FR
---

# Auditer les monitors sans modifier la production

## Objectif

Parcourir les monitors réellement configurés, comprendre leur évaluation et produire une fiche d'amélioration fondée uniquement sur leur configuration et leur historique visibles.

## Règles de sécurité

- L'atelier est intégralement en lecture seule.
- Aucun monitor, seuil, message, routage, downtime ou notification n'est créé ou modifié.
- N'ouvrez qu'un monitor dont le contenu est autorisé pour la formation.
- Aucun seuil de remplacement n'est inventé.

# Partie 1 — Comprendre le portefeuille réel

## Étape 1 — Ouvrir la liste des monitors

1. Dans le menu de gauche, ouvrez **Monitors** puis **Manage Monitors**.
2. Conservez la période proposée.
3. Repérez les filtres d'état, de type, de service, d'équipe et de tag réellement disponibles.

## Étape 2 — Qualifier le portefeuille visible

Relevez uniquement des nombres agrégés : total visible, états principaux et types représentés. Les nombres changent ; votre relevé daté prévaut.

## Étape 3 — Comprendre les états

Associez chaque état visible à sa signification :

- **OK** : condition d'alerte non satisfaite ;
- **Warn** : seuil d'avertissement atteint si configuré ;
- **Alert** : condition d'alerte satisfaite ;
- **No Data** : données insuffisantes selon la configuration ;
- **Muted** : notification suspendue, sans changer nécessairement l'évaluation.

## Étape 4 — Sélectionner un monitor autorisé

1. Choisissez un monitor réel avec historique lisible.
2. Notez son nom, son type et ses tags sans recopier de destinataire sensible.
3. Ouvrez son détail en lecture seule.

# Partie 2 — Lire la définition

## Étape 5 — Identifier le signal et le périmètre

Relevez : source, requête, filtres, agrégation, fenêtre et groupement. Si un élément n'est pas visible avec vos droits, indiquez **non visible**.

## Étape 6 — Examiner les seuils existants

1. Notez les seuils réellement configurés.
2. Identifiez leur unité.
3. Vérifiez s'ils concernent un compte, un taux, une durée ou une anomalie.
4. Ne proposez pas encore de nouvelle valeur.

## Étape 7 — Examiner les données manquantes

Relevez la politique réelle concernant No Data, les délais d'évaluation et les nouveaux groupes. Expliquez comment cette politique peut produire du bruit ou masquer un défaut de collecte.

## Étape 8 — Examiner le message

Vérifiez si le message contient : impact compréhensible, périmètre, owner, lien dashboard, lien runbook et conditions de rétablissement. Ne copiez pas les adresses ou mentions sensibles.

# Partie 3 — Examiner l'historique

## Étape 9 — Lire le graphe d'évaluation

1. Comparez la courbe au seuil réellement configuré.
2. Repérez les changements d'état.
3. Notez si les transitions semblent ponctuelles ou persistantes.
4. N'attribuez aucune cause à partir de ce seul graphe.

## Étape 10 — Relever les transitions

Sur la période disponible, comptez les transitions visibles entre OK, Warn, Alert et No Data. Si l'historique est insuffisant, écrivez **historique insuffisant**.

## Étape 11 — Évaluer le bruit

Répondez avec les preuves visibles :

- les alertes se rétablissent-elles rapidement ?
- plusieurs groupes changent-ils d'état indépendamment ?
- No Data déclenche-t-il des notifications ?
- le message permet-il une action claire ?

# Partie 4 — Produire une fiche d'amélioration sans appliquer de changement

## Étape 12 — Décrire le monitor actuel

| Champ | Valeur réelle ou constat |
|---|---|
| Nom | |
| Signal | |
| Périmètre | |
| Fenêtre | |
| Groupement | |
| Warning | |
| Alert | |
| No Data | |
| Owner visible | |
| Historique disponible | |

## Étape 13 — Proposer une amélioration argumentée

Choisissez au maximum deux axes : nommage, périmètre, fenêtre, groupement, No Data, message, ownership ou runbook. Chaque proposition doit citer une observation réelle.

**Exemple de formulation autorisée :** « Plusieurs transitions brèves sont visibles ; il faut tester une fenêtre plus persistante sur un environnement de validation avant toute modification. »

## Étape 14 — Définir la validation préalable

Pour chaque proposition, précisez :

1. données historiques nécessaires ;
2. environnement non productif ou mode de test ;
3. propriétaire de la décision ;
4. critère de succès ;
5. procédure de retour arrière.

## Étape 15 — Restituer la recommandation

Présentez le monitor actuel, le problème démontré, l'amélioration proposée, les éléments encore inconnus et le test préalable. Ne présentez jamais la proposition comme déjà validée.

## Validation finale

- [ ] Le monitor étudié existe réellement.
- [ ] Les seuils cités sont ceux réellement affichés.
- [ ] Aucun monitor ni routage n'a été modifié.
- [ ] Les propositions découlent d'observations réelles.
- [ ] Les inconnues sont signalées.
- [ ] Toute modification future est conditionnée à une validation hors production.
