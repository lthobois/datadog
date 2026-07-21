---
title: "Atelier 9 — Corrélation et investigation sur les données réelles"
lang: fr-FR
---

# Corréler sans inventer d'incident

## Objectif

Appliquer une méthode d'investigation aux données réellement présentes dans Datadog, tester les pivots disponibles et distinguer une observation, une hypothèse et une corrélation démontrée.

## Règles de sécurité

- Toutes les manipulations sont en lecture seule.
- Les sources connues sont `sigman` pour les logs, `eu-interfaces` pour l'APM et `peopulse` pour le RUM.
- Ces sources sont indépendantes tant qu'un identifiant ou un pivot réel ne démontre pas leur relation.
- Aucun incident, horaire, déploiement, seuil, identifiant ou résultat n'est inventé.
- Aucun replay, détail RUM sensible ou donnée personnelle n'est ouvert.

# Partie 1 — Initialiser une investigation réelle

## Étape 1 — Choisir un point de départ observable

1. Ouvrez le dashboard `[TRAINING] M08` créé au module précédent.
2. Choisissez un widget contenant réellement des données.
3. Relevez la période exacte, la source, le filtre et l'agrégation.
4. Formulez uniquement la question permise par ce widget.

**Réponse expliquée :** un comptage de logs répond à une question de présence et de volume. Il ne prouve ni l'impact utilisateur ni une cause racine.

## Étape 2 — Ouvrir le journal d'investigation

Créez la table suivante dans votre document de travail :

| Heure d'observation | Source | Fait vérifié | Hypothèse | Test suivant | Résultat | Statut |
|---|---|---|---|---|---|---|

Utilisez l'heure de votre observation, pas une chronologie fictive.

## Étape 3 — Formuler des hypothèses prudentes

Pour le fait choisi, écrivez :

- une hypothèse compatible ;
- une explication concurrente ;
- une condition qui invaliderait chaque hypothèse.

**Exemple de forme :** « Une hausse est visible sur la période ; elle peut correspondre à davantage de trafic ou à davantage d'erreurs. Je compare le volume total et la proportion d'erreurs. »

# Partie 2 — Tester les pivots dans la même source

## Étape 4 — Du widget Logs au Log Explorer

1. Ouvrez le détail du widget `Logs — erreurs sigman`.
2. Vérifiez que le filtre `service:sigman status:error` et la période sont transmis.
3. Relevez les facettes réellement présentes.
4. N'enregistrez aucune nouvelle vue.

**Résultat attendu :** le journal distingue la valeur agrégée du dashboard des événements détaillés du Log Explorer.

## Étape 5 — Du widget APM au service

1. Revenez au dashboard.
2. Ouvrez le détail du widget `APM — activité eu-interfaces / console`.
3. Vérifiez le service `eu-interfaces`, l'opération `console` et la période.
4. Si aucune trace n'est disponible, étendez à 4 heures puis 1 jour.
5. Notez **aucune trace disponible** si le résultat reste vide.

## Étape 6 — Lire une trace sans surinterpréter

1. Ouvrez une trace uniquement si elle est disponible.
2. Relevez sa durée, son statut et les spans visibles.
3. Identifiez le chemin critique observé.
4. N'appelez pas automatiquement le span le plus long « cause racine ».

# Partie 3 — Tester les corrélations entre sources

## Étape 7 — Rechercher un pivot APM vers Logs

1. Dans la trace réelle, cherchez un lien vers les logs ou les attributs `trace_id` et `span_id`.
2. Dans les logs `sigman`, vérifiez si les mêmes champs existent réellement.
3. Ne copiez pas un identifiant dans une autre source s'il n'est pas proposé ou présent.

**Réponse de référence :** en l'absence d'identifiant commun vérifié, écrivez **corrélation `sigman`–`eu-interfaces` non démontrée**.

## Étape 8 — Rechercher un pivot RUM vers APM

1. Ouvrez les agrégats RUM de `peopulse`.
2. Restez sur les vues et ressources agrégées autorisées.
3. Vérifiez seulement si Datadog propose un pivot APM sûr.
4. N'ouvrez aucune session, action sensible ou URL détaillée.

**Réponse de référence :** en l'absence de pivot réel vérifié, écrivez **corrélation `peopulse`–`eu-interfaces` non démontrée**.

## Étape 9 — Classer le niveau de preuve

Pour chaque relation examinée, choisissez un seul niveau :

| Niveau | Critère |
|---|---|
| observation | valeur visible dans une vue |
| coïncidence temporelle | variation sur une période proche, sans identifiant commun |
| corrélation technique | pivot ou identifiant commun vérifié |
| causalité | mécanisme confirmé par plusieurs preuves et un test |

Une coïncidence temporelle n'est pas une corrélation technique.

# Partie 4 — Construire une conclusion honnête

## Étape 10 — Mettre à jour le journal

Pour chaque test, inscrivez le résultat réel et classez l'hypothèse : conservée, affaiblie, rejetée ou non testable.

## Étape 11 — Rédiger la conclusion

Utilisez ce canevas :

> Sur la période observée, [fait vérifié] est visible dans [source]. Les tests [liste] ont été réalisés. [Corrélation] est démontrée / n'est pas démontrée parce que [preuve ou absence]. Les limites sont [rétention, droits, échantillonnage, attribut absent]. Le prochain test recommandé est [action en lecture seule].

## Étape 12 — Restituer l'investigation en trois minutes

Présentez dans cet ordre :

1. question de départ ;
2. faits datés ;
3. hypothèses testées ;
4. pivots disponibles ou absents ;
5. conclusion et niveau de confiance ;
6. prochaine vérification.

## Validation finale

- [ ] Toutes les valeurs proviennent de la plateforme réelle.
- [ ] Aucun incident, déploiement ou seuil n'a été inventé.
- [ ] Les trois sources ne sont pas présentées comme un même système.
- [ ] Les faits, hypothèses et conclusions sont séparés.
- [ ] Les corrélations absentes sont explicitement signalées.
- [ ] Aucune donnée sensible n'a été ouverte ou copiée.
