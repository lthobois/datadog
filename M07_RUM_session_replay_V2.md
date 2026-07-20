---
title: "Module 7 — Atelier V2 : analyser le RUM et concevoir l'observation d'une modale"
subtitle: "Document participant autonome"
lang: fr-FR
---

# Relier une expérience RUM au diagnostic backend

## Objectif

Parcourir l'organisation RUM réellement disponible en lecture seule, qualifier ce qui peut être relié à l'APM après le module 6, concevoir trois actions respectueuses de la confidentialité et préparer les indicateurs du dashboard du module 8.

## Livrable

Vous produisez :

- un relevé daté de l'organisation RUM visible ;
- trois actions personnalisées et leurs attributs bornés ;
- une matrice des données interdites et des protections ;
- un indicateur d'usage avec ses limites ;
- un chemin d'investigation RUM vers APM et logs ;
- trois spécifications de widgets RUM pour le module 8 ;
- une conclusion séparant constat réel, hypothèse et limite ;
- une checklist de validation avant instrumentation réelle.

## Place dans la progression

Au module 6, vous avez étudié `eu-interfaces`, dont l'opération `console` ne correspond pas à une requête issue d'un navigateur. Cet atelier ne force donc aucune relation entre ce service et le RUM. Il recherche un service web corrélable uniquement si l'interface en fournit la preuve.

Les résultats préparent :

- le module 8, avec trois widgets RUM spécifiés mais non créés ;
- le module 9, avec un chemin d'investigation allant de l'impact utilisateur à une ressource puis, si disponible, à une trace backend et à ses logs.

## Règle de sécurité

Travaillez uniquement en lecture. Ne créez ni application, vue sauvegardée, mesure, funnel, monitor, dashboard ou configuration. Ne cliquez pas sur un bouton d'enregistrement ou de modification. N'ouvrez aucun Session Replay réel non préparé et non validé. Ne recopiez aucun nom d'application sensible, nom, email, identifiant d'application ou de session, token client, URL détaillée, saisie ou contenu utilisateur réel.

## Prérequis

- Chrome connecté à Datadog ;
- accès en lecture à **Digital Experience** et au **RUM Explorer**, ou captures anonymisées ;
- scénario fictif PeopleShop ;
- vocabulaire `env/service/version` ;
- connaissance du parcours `POST /api/orders/validate`.
- fiche de pivot APM préparée au module 6.

## Comment utiliser ce document

Effectuez d'abord l'observation demandée, puis lisez la réponse qui suit la question. Les applications, sessions et volumes évoluent avec la période, le consentement et l'échantillonnage : votre relevé daté prévaut sur l'instantané historique. Les parties 1 et 2 utilisent une application RUM réelle autorisée uniquement pour apprendre la navigation ; les parties PeopleShop reposent exclusivement sur le scénario fictif. Ne mélangez jamais leurs preuves.

## Contexte de plateforme pris en compte

Lors de la dernière vérification, trois applications RUM actives étaient visibles : deux applications JavaScript et une application Flutter. Certaines proposaient Product Analytics. Une application RUM était déjà ouverte dans Chrome sur sa page d'installation SDK.

Cette page peut afficher des identifiants techniques nécessaires à l'installation. Ne les copiez pas dans le livrable. Pour l'atelier, choisissez une application **Browser JavaScript** autorisée depuis la liste des applications, puis restez dans les vues d'exploitation.

# Scénario PeopleShop

La modale legacy de validation ne change ni l'URL ni la route. Les clics automatiques peuvent être absents ou porter un nom instable. L'équipe veut mesurer l'ouverture, la tentative de validation et l'annulation, puis rejoindre la ressource `POST /api/orders/validate`.

# Partie 1 — Comprendre l'organisation du RUM

## Étape 1 — Ouvrir Digital Experience

1. Dans le menu gauche, ouvrez **Digital Experience**.
2. Repérez **RUM**, **Session Replay**, **Error Tracking** ou **Product Analytics**, selon les fonctionnalités visibles.
3. Ouvrez l'accueil RUM ou la liste des applications.
4. Notez la date, l'heure et les modules visibles.

| Élément | Observation |
|---|---|
| Date et heure |  |
| Modules visibles |  |
| Nombre d'applications, si affiché |  |

### Quel est le rôle du RUM ?

**Réponse :** le Real User Monitoring collecte depuis le navigateur ou l'application mobile des signaux sur les parcours, interactions, ressources, erreurs et performances réellement rencontrés.

**Pourquoi :** il qualifie l'impact utilisateur et les populations affectées, tandis que l'APM explique le traitement backend lorsque la corrélation existe.

## Étape 2 — Examiner la liste des applications

1. Repérez les applications sans ouvrir leur configuration.
2. Notez uniquement leur type générique : Browser JavaScript, Flutter, mobile ou autre.
3. Choisissez une application **Browser JavaScript** autorisée pour la suite, sans recopier son nom si celui-ci est sensible.
4. Si la navigation vous conduit à une page **SDK Installation**, revenez aux vues d'exploitation sans copier `applicationId`, `clientToken` ou extrait d'initialisation.

### Quel était l'état observé lors de la conception ?

**Réponse :** trois applications RUM actives étaient visibles : deux JavaScript et une Flutter ; certaines proposaient également Product Analytics.

**Limite :** cet instantané du 19 juillet 2026 ne constitue pas une métrique contractuelle ni un résultat attendu.

## Étape 3 — Parcourir Summary

1. Ouvrez **Summary** ou la page de synthèse de l'application autorisée.
2. Repérez la période, les filtres et les indicateurs disponibles.
3. Identifiez les accès vers sessions, vues, actions, erreurs et ressources.
4. Ne changez pas la configuration de l'application.

| Élément | Observation non sensible |
|---|---|
| Type d'application | Browser JavaScript |
| Période |  |
| Sessions ou vues visibles |  |
| Session Replay disponible | oui / non observé |
| Product Analytics disponible | oui / non observé |

### Pourquoi commencer par une synthèse agrégée ?

**Réponse :** elle permet d'estimer fréquence, tendance et périmètre avant d'examiner un événement ou une session individuelle.

**Pourquoi :** une session spectaculaire illustre un cas mais ne mesure jamais seule l'ampleur.

## Étape 4 — Ouvrir le RUM Explorer

1. Ouvrez **RUM > Explorer**.
2. Repérez la barre de recherche, la période, les facettes et la liste des événements.
3. Repérez le sélecteur de type d'événement.
4. Vérifiez qu'aucun filtre nominatif ou identifiant utilisateur n'est appliqué.
5. Ne sauvegardez aucune vue.

### À quoi sert le RUM Explorer ?

**Réponse :** il permet de rechercher et segmenter les événements RUM par application, vue, action, ressource, erreur, contexte technique et autres attributs autorisés.

## Étape 5 — Associer les objets RUM aux questions

| Objet | Question de référence |
|---|---|
| Session | quel parcours global a été vécu ? |
| Vue | dans quelle page ou quel état de navigation ? |
| Action | quelle interaction utilisateur a eu lieu ? |
| Ressource | quel appel réseau ou chargement a été effectué ? |
| Erreur | quelle erreur frontend a été collectée ? |
| Tâche longue | le thread principal a-t-il bloqué l'interface ? |

### Comment ces objets sont-ils reliés ?

**Réponse :** une session contient des vues ; les actions, ressources, erreurs et tâches longues héritent du contexte de leur vue.

## Étape 6 — Examiner les facettes

1. Repérez les facettes d'application, type d'événement, vue, navigateur, appareil, version, action, statut ou autres dimensions disponibles.
2. Dépliez une facette sans sélectionner de valeur sensible.
3. Notez quelles dimensions permettraient de comparer des populations ou versions.

### Pourquoi une facette utilisateur nominative n'est-elle pas nécessaire ici ?

**Réponse :** la question porte sur des catégories et un parcours, pas sur l'identité d'une personne.

**Pourquoi :** des dimensions bornées comme `tenant_tier` ou `frontend_version` suffisent pour qualifier l'impact avec moins de risque de confidentialité.

## Étape 7 — Examiner la liste des sessions sans ouvrir de replay

1. Sélectionnez le type **Sessions** si la vue le permet.
2. Repérez durée, nombre de vues, erreurs, actions, frustration, vue initiale, vue finale et disponibilité du replay.
3. Ne recopiez aucun identifiant ou attribut nominatif.
4. N'ouvrez pas le replay.

### Quels critères rendent une session intéressante pour une analyse ciblée ?

**Réponse :** vue concernée, action ou erreur présente, durée cohérente, segment pertinent et replay disponible et autorisé.

**Limite :** lors de la conception, 421 sessions étaient visibles sur un jour ; ce nombre dépend de la période, du trafic, du consentement et de l'échantillonnage.

## Étape 8 — Examiner les vues

1. Sélectionnez le type **Views**.
2. Repérez les noms de vues, durées, erreurs, actions et ressources associées.
3. Identifiez une vue générique de parcours sans recopier une URL détaillée.

### Qu'est-ce qu'une vue ?

**Réponse :** une page ou un état de navigation suivi pendant une durée logique, auquel sont reliés les autres événements RUM.

**Limite :** chaque clic ou ouverture de composant ne mérite pas une nouvelle vue.

## Étape 9 — Examiner les actions

1. Sélectionnez le type **Actions**.
2. Repérez les noms, durées et nombres de ressources, erreurs ou tâches longues associés.
3. Observez si les noms semblent stables ou dépendants du texte de l'interface.
4. Ouvrez un détail uniquement s'il ne révèle aucune donnée sensible.

### Pourquoi une action automatique peut-elle être mal nommée sur une modale legacy ?

**Réponse :** son nom peut dépendre du texte, d'un label, d'un attribut d'accessibilité ou du DOM, éléments qui peuvent changer avec la langue ou l'implémentation.

**Pourquoi :** une action personnalisée nomme directement l'intention métier et reste plus stable.

## Étape 10 — Examiner les ressources

1. Sélectionnez le type **Resources**.
2. Repérez méthode, type, URL normalisée ou ressource, durée et statut.
3. Privilégiez une ressource XHR ou Fetch dont le nom est générique et non sensible.
4. Ouvrez son détail et recherchez **View Trace**, **Trace**, **APM** ou un accès équivalent, sans recopier d'identifiant.
5. Notez **trace liée observée**, **aucune trace liée** ou **non vérifiable avec mes droits**, puis revenez à la liste.

### Que représente une ressource RUM ?

**Réponse :** un chargement ou appel réseau, notamment XHR ou Fetch, observé depuis le frontend.

**Pourquoi :** elle constitue le pont naturel entre l'action de soumission et la trace backend de `orders-api`.

**Contexte du module 6 :** ne recherchez pas `eu-interfaces` comme cible de cette corrélation. Son opération `console` n'est pas une requête web déclenchée par une ressource navigateur.

## Étape 11 — Examiner erreurs et tâches longues

1. Sélectionnez successivement **Errors** puis **Long Tasks**, si disponibles.
2. Repérez les agrégats sans ouvrir de contenu sensible.
3. Notez la question que chaque type permet de tester.

### Quelle différence existe entre une erreur et une tâche longue ?

**Réponse :** une erreur décrit une exception ou un échec frontend collecté ; une tâche longue indique un blocage prolongé du thread principal qui peut rendre l'interface peu réactive.

**Limite :** ni l'une ni l'autre ne prouve automatiquement que la requête backend a échoué.

# Partie 2 — Décider quoi instrumenter

À partir de cette partie, quittez les données réelles. Toutes les actions, valeurs, ressources et conclusions suivantes appartiennent au scénario fictif PeopleShop. Une absence d'action ou de trace dans l'application réelle peut justifier une proposition d'amélioration, mais ne constitue pas une preuve dans le scénario.

## Étape 12 — Choisir action ou vue pour la modale

```text
[ouverture] -> [tentative de validation]
            -> [ressource réseau ou annulation]
```

### Faut-il créer une vue manuelle pour cette modale ?

**Réponse :** non dans ce scénario. Elle ne change pas l'URL et ne représente pas un état de navigation durable autonome ; trois actions personnalisées sont plus adaptées.

**Limite :** une vue manuelle serait justifiable si la modale devenait un état logique durable que l'équipe souhaite analyser comme une page.

## Étape 13 — Définir les trois actions

| Interaction | Action de référence | Déclencheur exact |
|---|---|---|
| ouverture | `checkout_modal_open` | modale effectivement affichée |
| soumission | `checkout_modal_submit` | tentative de validation envoyée |
| annulation | `checkout_modal_cancel` | fermeture ou annulation explicite |

### Pourquoi utiliser des noms `snake_case` indépendants du DOM et de la langue ?

**Réponse :** ils restent stables malgré une traduction, un changement de texte ou une refonte HTML et facilitent les requêtes communes.

### Pourquoi ne pas ajouter une quatrième action `success` ?

**Réponse :** la tentative de soumission ne prouve pas le succès métier. Dans cet exercice, la réussite doit venir du statut de la ressource backend ou d'un événement métier fiable défini ultérieurement.

## Étape 14 — Choisir des attributs bornés

| Attribut | Valeurs contrôlées | Question traitée |
|---|---|---|
| `checkout_step` | `validation` | quelle étape est concernée ? |
| `tenant_tier` | `standard`, `enterprise` | quelle catégorie est affectée ? |
| `frontend_version` | versions déployées contrôlées | une version frontend diffère-t-elle ? |
| `modal_variant` | `legacy`, `new` | quelle variante est utilisée ? |

Choisissez deux ou trois attributs par action, pas nécessairement les quatre.

### Pourquoi exclure `tenant_id`, email et identifiant de commande brut ?

**Réponse :** ils sont personnels ou presque uniques, augmentent la cardinalité et ne sont pas nécessaires pour la segmentation proposée.

## Étape 15 — Écrire l'instrumentation conceptuelle

```javascript
datadogRum.addAction('checkout_modal_submit', {
  checkout_step: 'validation',
  tenant_tier: 'enterprise',
  frontend_version: '5.8.0'
})
```

### Ce code est-il directement prêt pour la production ?

**Réponse :** non. Il faut vérifier le mode d'import, la version du Browser SDK, son initialisation, le consentement, l'échantillonnage et la nomenclature des attributs.

**Pourquoi :** l'exemple décrit l'intention pédagogique, pas toute l'intégration technique.

# Partie 3 — Protéger les données avant collecte

## Étape 16 — Classer les zones et données

| Donnée ou zone | Protection de référence | Justification |
|---|---|---|
| nom, email et adresse | ne pas collecter ; masquer dans le replay | données personnelles |
| paiement | cacher ou exclure complètement | données financières sensibles |
| commentaire libre | ne pas collecter ; masquer | contenu imprévisible |
| résumé du panier | masquer par défaut | autorisation sélective seulement après validation |

### Quel niveau de protection faut-il choisir par défaut ?

**Réponse :** partir du niveau le plus protecteur, généralement `mask`, puis n'autoriser explicitement que les éléments nécessaires et validés.

**Limite :** la politique juridique et sécurité, le consentement, les rôles d'accès et la rétention du client prévalent sur l'exemple.

## Étape 17 — Distinguer masquer, cacher et autoriser

| Décision | Sens conceptuel |
|---|---|
| `mask` | remplacer le contenu par une représentation masquée |
| `mask-user-input` | protéger principalement les saisies utilisateur |
| `hidden` ou exclusion ciblée | ne pas rendre la zone exploitable dans le replay |
| `allow` | autoriser explicitement une zone après validation |

### Pourquoi `allow` ne doit-il pas être activé globalement par facilité ?

**Réponse :** il peut exposer des contenus qui n'ont pas été inventoriés ou validés.

**Pourquoi :** la protection doit être conçue avant l'envoi, car une donnée non collectée sous sa forme originale réduit durablement le risque d'exposition.

## Étape 18 — Définir les préconditions d'un replay

### Quand peut-on ouvrir un Session Replay ?

**Réponse :** après avoir mesuré l'ampleur par agrégats, choisi une session représentative, vérifié que le replay est autorisé et correctement masqué, puis formulé une question précise.

**Exemple de question :** la modale s'est-elle ouverte avant l'abandon ?

### Session Replay est-il une vidéo brute ?

**Réponse :** non. Il reconstruit l'expérience à partir du DOM, de ses mutations et des événements collectés.

**Limite :** il contextualise un cas individuel et ne constitue ni une mesure d'ampleur ni une preuve causale suffisante.

# Partie 4 — Mesurer le parcours

## Étape 19 — Définir le taux de soumission

```text
taux de soumission = checkout_modal_submit / checkout_modal_open
```

### Que mesure ce taux ?

**Réponse :** la proportion des ouvertures observées qui aboutissent à une tentative de validation observée.

### Que ne mesure-t-il pas ?

**Réponse :** ni le succès backend, ni la commande finalisée, ni l'expérience des sessions non collectées.

## Étape 20 — Documenter les limites de mesure

| Limite | Effet possible |
|---|---|
| échantillonnage | toutes les sessions ne sont pas observées |
| consentement refusé | population collectée différente de la population totale |
| blocage ou panne du SDK | événements manquants |
| double clic | plusieurs soumissions pour une ouverture |
| session interrompue | ouverture sans événement final |
| fenêtres différentes | numérateur et dénominateur non comparables |

### Pourquoi l'échantillonnage RUM et celui du replay doivent-ils être distingués ?

**Réponse :** une session peut contribuer aux agrégats RUM sans disposer d'un replay ; les deux taux répondent à des coûts et usages différents.

# Partie 5 — Relier expérience et backend

## Étape 21 — Construire le chemin d'investigation

```text
agrégats d'actions sur la vue checkout
  -> session représentative et autorisée
  -> action checkout_modal_submit
  -> ressource POST /api/orders/validate
  -> trace orders-api
  -> span de dépendance dominant
  -> logs corrélés
```

### Pourquoi commencer par les agrégats plutôt que par un replay ?

**Réponse :** les agrégats mesurent fréquence et segments ; le replay explique seulement le contexte d'un cas choisi.

### Ce chemin a-t-il été validé sur `eu-interfaces` au module 6 ?

**Réponse :** non. `eu-interfaces` a servi à apprendre l'APM avec une opération `console`. Le chemin RUM-APM doit partir d'une ressource navigateur XHR ou Fetch et rejoindre un service web distinct uniquement si un lien explicite est disponible.

## Étape 22 — Examiner la ressource frontend

Pour la ressource, vérifiez conceptuellement :

- méthode et nom normalisé ;
- durée ;
- statut HTTP ;
- action et vue parentes ;
- éventuelle trace backend liée.

### Une ressource sans trace liée prouve-t-elle l'absence de traitement backend ?

**Réponse :** non. La corrélation peut manquer à cause des origines autorisées, de CORS, de la propagation du contexte, de l'instrumentation APM, de l'échantillonnage ou de la rétention.

## Étape 23 — Vérifier la frontière entre versions frontend et backend

### `frontend_version:5.8.0` prouve-t-elle que `orders-api:2.4.0` a traité la requête ?

**Réponse :** non. La version frontend décrit le code du navigateur ; seule la ressource corrélée à la trace backend permet d'associer le parcours à la version du service.

## Étape 24 — Produire la checklist de corrélation

- [ ] L'origine backend est autorisée pour la propagation RUM-APM.
- [ ] CORS accepte les en-têtes nécessaires.
- [ ] Le backend est instrumenté par APM.
- [ ] La ressource et la trace partagent le bon contexte.
- [ ] Les périodes de rétention et d'échantillonnage sont compatibles.
- [ ] Les logs portent les identifiants de trace remappés.
- [ ] Aucune donnée sensible n'est ajoutée au contexte.

### Que permet une corrélation réussie ?

**Réponse :** passer d'une action utilisateur à l'appel réseau, puis au chemin backend, à la dépendance dominante et aux logs du même parcours.

## Étape 25 — Préparer trois widgets RUM pour le module 8

Ne créez aucun dashboard dans cet atelier. Complétez les spécifications et conservez-les pour le module 8.

| Widget à construire | Source | Périmètre | Mesure | Regroupement | Représentation | Interprétation et limite |
|---|---|---|---|---|---|---|
| Sessions avec erreur frontend | RUM Sessions | application et environnement autorisés | nombre ou taux de sessions avec erreur | `version` ou `browser.name` | série temporelle | mesure l'impact collecté ; dépend du consentement et de l'échantillonnage |
| Latence des vues principales | RUM Views | vues autorisées et normalisées | p75 ou p95 de `view.loading_time` | `view.name`, puis `version` | série temporelle ou toplist | compare les vues ; une vue mal nommée fragmente les résultats |
| Ressources XHR/Fetch lentes ou en erreur | RUM Resources | application, environnement et type XHR/Fetch | p95 de durée et taux d'erreur | ressource normalisée ou statut | toplist | prépare le pivot backend ; une ressource lente ne prouve pas la cause serveur |

### Pourquoi utiliser les données structurées plutôt que le nombre de replays ?

**Réponse :** les événements structurés permettent de compter, segmenter et suivre une tendance. Un replay contextualise un cas individuel et sa disponibilité dépend d'un échantillonnage spécifique.

## Étape 26 — Rédiger la conclusion de passage vers le module 8

Complétez le modèle sans ajouter de donnée réelle sensible :

```text
Constat réel : l'application Browser autorisée expose [types d'événements]
                sur la période [période].

Corrélation : une trace backend liée a été [observée / non observée /
              non vérifiable] depuis une ressource XHR ou Fetch.

Limite : ce constat dépend de la période, des droits, du consentement,
         de l'échantillonnage et de la rétention.

Conception PeopleShop : trois actions stables décrivent la modale et
                        la ressource POST porte la preuve du traitement.

Préparation dashboard : trois widgets RUM sont spécifiés ; ils seront
                        construits et confrontés aux logs et à l'APM
                        au module 8.
```

### Pourquoi séparer constat réel et conception PeopleShop ?

**Réponse :** le premier démontre seulement que vous maîtrisez la navigation sur l'organisation existante ; la seconde décrit un dispositif pédagogique fictif. Les fusionner produirait une conclusion non vérifiable sur la production partagée.

## Questions de synthèse corrigées

### Quelle différence existe entre une vue et une action ?

**Réponse :** une vue représente une page ou un état de navigation durable ; une action représente une interaction dans ce contexte.

### Quels sont les trois noms d'actions PeopleShop ?

**Réponse :** `checkout_modal_open`, `checkout_modal_submit` et `checkout_modal_cancel`.

### Une action submit prouve-t-elle la réussite de la commande ?

**Réponse :** non. Elle prouve une tentative observée ; la réussite doit être confirmée par la ressource ou un événement métier fiable.

### Quel est le rôle respectif des agrégats et du replay ?

**Réponse :** les agrégats mesurent l'ampleur et les segments ; le replay contextualise un cas autorisé et masqué.

## Aide au diagnostic

| Difficulté | Interprétation | Action en lecture seule |
|---|---|---|
| RUM indisponible | droits ou fonctionnalité non activée | utiliser les captures anonymisées |
| Page SDK Installation ouverte | navigation restée dans la configuration | revenir à Summary ou Explorer sans copier d'identifiant |
| Aucune action visible | suivi absent ou période différente | utiliser ce manque pour justifier les actions personnalisées |
| Nom d'action instable | dépendance au texte ou DOM | définir un nom métier explicite et stable |
| Confusion vue/action | état logique non défini | demander si l'état possède une durée et une navigation autonomes |
| Replay non autorisé | confidentialité ou droits | ne pas l'ouvrir ; poursuivre avec les événements structurés |
| Trace non liée | corrélation incomplète | parcourir la checklist de l'étape 24 |
| Seul `eu-interfaces` est connu en APM | service `console` sans relation navigateur attendue | chercher un service web depuis une ressource RUM ou noter **non vérifiable** |
| Attribut trop cardinal | identifiant presque unique | le remplacer par une catégorie bornée |

## Validation finale

- [ ] Le relevé RUM est daté et ne conserve aucune donnée sensible.
- [ ] Session, vue, action, ressource, erreur et tâche longue sont distinguées.
- [ ] La modale est modélisée par exactement trois actions stables.
- [ ] Les déclencheurs ne dépendent ni du texte ni d'un sélecteur DOM.
- [ ] Les attributs sont bornés, utiles et non sensibles.
- [ ] Les données interdites et le niveau de protection sont explicites.
- [ ] `allow` n'est jamais proposé globalement sans validation.
- [ ] `submit/open` n'est pas présenté comme un taux de succès.
- [ ] L'échantillonnage et le consentement sont pris en compte.
- [ ] Les agrégats mesurent l'ampleur et le replay contextualise un cas.
- [ ] Le chemin vers la ressource, la trace et les logs est complet.
- [ ] L'absence de trace liée n'est pas interprétée comme absence de backend.
- [ ] `eu-interfaces` n'est pas présenté comme un backend RUM corrélé.
- [ ] Versions frontend et backend ne sont pas confondues.
- [ ] Trois widgets RUM sont spécifiés pour le module 8 sans créer de dashboard.
- [ ] Le constat réel est séparé de la conception fictive PeopleShop.
- [ ] Aucune ressource Datadog n'a été créée ou modifiée.

## Références officielles

- [RUM Browser Monitoring](https://docs.datadoghq.com/real_user_monitoring/application_monitoring/browser/)
- [RUM Browser Data Collected](https://docs.datadoghq.com/real_user_monitoring/application_monitoring/browser/data_collected/)
- [Tracking User Actions](https://docs.datadoghq.com/real_user_monitoring/application_monitoring/browser/tracking_user_actions/)
- [Monitoring Resource Performance](https://docs.datadoghq.com/real_user_monitoring/browser/monitoring_resource_performance/)
- [Connect RUM and Traces](https://docs.datadoghq.com/real_user_monitoring/correlate_with_other_telemetry/apm/?tab=browserrum)
- [Session Replay Browser Privacy Options](https://docs.datadoghq.com/session_replay/browser/privacy_options/)
