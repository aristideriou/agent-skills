---
name: build-custom-cmp
description: >-
  Créer et intégrer une CMP maison légère dans un site existant, avec recueil
  interactif des vendors et purposes, options Google Consent Mode, textes validés,
  cookie de consentement et événements GTM. Utiliser pour construire une bannière
  cookies sans CMP tierce ou adapter une CMP interne à la stack du site. Ne pas
  utiliser pour configurer une CMP commerciale ou implémenter le framework IAB TCF.
---

# Créer une CMP maison pour GTM

Construire une CMP native du site en suivant les étapes et spécifications
ci-dessous. Utiliser ce fichier comme contrat complet : parcours utilisateur,
textes, stockage du consentement, événements GTM et recette de tests.

## Périmètre et conduite du travail

- Implémenter les écrans, les préférences, leur stockage et les pushes data layer.
- Laisser à GTM le mapping Consent Mode, les appels aux API de consentement,
  le blocage et le déclenchement des tags, leur déduplication et la désactivation
  des intégrations de tracking. Ne pas ajouter de calls `gtag` dans la CMP.
- Fournir le contrat d'intégration et les scénarios de recette GTM. Ne pas modifier
  ni publier un conteneur GTM sauf demande explicite distincte.
- Réutiliser les composants et dépendances du projet. Ne pas ajouter de SDK CMP,
  TCF, géolocalisation, dashboard, consent wall ou journal centralisé de preuves.
- Respecter les instructions du dépôt et le travail existant. Ne pas supprimer
  une CMP existante ni ses intégrations tant que sa migration n'est pas définie.
- Poser les questions à l'utilisateur qui exécute ce skill, dans sa langue.
  Regrouper les questions liées, sans refaire demander une réponse déjà explicite.
  Continuer l'inspection du projet pendant l'attente, mais attendre les réponses
  nécessaires avant d'implémenter les décisions correspondantes.
- Ne pas rouvrir un débat de conformité à chaque étape. Ne pas présenter la seule
  bannière comme une certification de conformité de l'ensemble du tracking.

## 1. Adapter le design à la stack du site

Inspecter le framework, le routage, le rendu serveur, les styles, les composants,
les langues, le footer, les modales existantes et le chargement de GTM. Identifier
les commandes de build, lint et tests. Demander le dépôt ou les sources si absents.

Utiliser les conventions du site : React/Vue/Svelte si présents, HTML/CSS/JS natifs
sinon ; composants du design system, typographie, couleurs, espacements et boutons.
Éviter de changer de framework ou d'ajouter un framework de styles pour la CMP.

Prévoir une modale bloquante, laissant le contenu visible derrière elle. Permettre
de continuer après acceptation ou refus explicite. En l'absence de décision valide,
réafficher la CMP à la visite suivante. Rendre « Continuer sans accepter » lisible,
dans la bannière et utilisable en un clic. Prévoir clavier, focus et lecteurs
d'écran. Adapter le rendu au mobile sans modifier les choix disponibles.

## 2. Demander les vendors et purposes

Demander explicitement :

> Quels partenaires ou outils doivent figurer dans la CMP, et dans quelles
> familles souhaites-tu les regrouper ? Pour chacun, indique son usage sur le site.

Présenter la liste dans un tableau : `vendor_id`, nom affiché, `purpose_id`, nom de
la famille, description de l'usage, lien de confidentialité lorsqu'il est fourni.
Inclure analytics et publicité. Ne pas inventer de partenaire actif ou de lien.
Proposer des descriptions puis les faire valider avec les textes à l'étape 4.
Ne pas bloquer sur un lien facultatif absent.

Créer des identifiants stables et uniques ; réserver les clés de métadonnées et
les clés `como_*`. Associer chaque vendor à une seule famille selon le modèle
simplifié décrit ici. Demander une décision uniquement si ses usages rendent
cette association ambiguë.

Afficher en premier une famille « Strictement nécessaires », distincte et non
désactivable. Pour les familles facultatives, démarrer sans autorisation. Permettre
un toggle par vendor et un toggle de famille ; afficher un état mixte si seuls
certains membres sont autorisés. Restaurer les choix enregistrés à la réouverture.

## 3. Demander les options Google et Consent Mode

Poser explicitement ces deux questions, sauf réponse déjà donnée :

> Utilises-tu des tags Google sur ce site, par exemple GA4 ou Google Ads ?
> Souhaites-tu afficher « Google Consent Mode » comme une famille distincte
> dans l'écran de personnalisation ?

Si Google est utilisé, préciser les produits concernés lorsqu'ils ne sont pas
connus et les rattacher à la liste des vendors. Ajouter au premier écran la mention
Google et le lien `https://business.safety.google/privacy/`.

Si la famille distincte est souhaitée, afficher quatre options avec nom technique
et explication en langage courant :

| Option | Explication proposée |
| --- | --- |
| `analytics_storage` | Stockage de traceurs pour mesurer l'utilisation du site. |
| `ad_storage` | Stockage de traceurs liés à la publicité. |
| `ad_user_data` | Transmission à Google de données pour des usages publicitaires. |
| `ad_personalization` | Utilisation des données pour personnaliser la publicité. |

Stocker leurs choix sous les clés `como_analytics_storage`, `como_ad_storage`,
`como_ad_user_data` et `como_ad_personalization`. Les autoriser ou refuser toutes
avec les boutons globaux ; conserver les choix individuels à la validation.

Si cette famille n'est pas affichée, stocker les choix des vendors Google comme
ceux des autres vendors et laisser à GTM la correspondance avec Consent Mode.
Ne pas inventer un mapping ni accorder des droits Google invisibles à l'utilisateur.
Ne pas créer de famille Google sans tag Google ; clarifier une demande contradictoire.

## 4. Proposer les textes et demander les modifications

Remplacer les variables des textes standard ci-dessous par les réponses
précédentes : site, nombre réel de vendors facultatifs uniques, finalités,
descriptions, options Google,
lien de confidentialité du site et langue. Demander un lien manquant nécessaire.

Présenter les textes complets des deux écrans et les libellés des boutons :

- Premier écran : « Autoriser tous les cookies », « Continuer sans accepter »,
  « Personnaliser mes choix ». Accepter en bas à droite, personnaliser en bas à
  gauche et refuser en haut à droite, à l'intérieur ; adapter au mobile.
- Second écran : « Valider mes préférences » comme seul bouton de validation.
- Footer : « Modifier mes choix de cookies ».

Demander explicitement :

> Voici les textes proposés à partir de tes réponses. Souhaites-tu les conserver
> ou modifier certains passages ou libellés ?

Attendre sa réponse avant de finaliser ces textes. Ne pas redemander si la version
concernée a déjà été approuvée. Les toggles modifieront un brouillon : ni cookie
ni consentement effectif ne changeront avant « Valider mes préférences ».
Repartir de l'état enregistré à chaque réouverture.

Adapter la langue, les finalités et les partenaires aux réponses de l'utilisateur.
Ne pas conserver les variables entre accolades dans les textes livrés.

### Premier écran

**Titre :** Vos choix de cookies

**Texte :** « {site} et {nombre_vendors} partenaires utilisent des cookies ou
technologies similaires pour {finalites}. Vous pouvez tout accepter, continuer
sans accepter les traceurs facultatifs ou personnaliser vos choix. Vous pourrez
modifier ou retirer votre consentement à tout moment depuis “Modifier mes choix
de cookies”, en bas de page. »

Calculer le nombre depuis les vendors facultatifs uniques configurés ; ne pas
compter les familles ni les quatre options Google comme quatre partenaires.
Adapter le singulier et omettre les partenaires si leur nombre est nul.

Si Google est utilisé, ajouter : « Nous utilisons également des outils Google
pour {usages_google}. En savoir plus sur [l'utilisation des données par
Google](https://business.safety.google/privacy/). »

Afficher le lien vers la politique de confidentialité du site et rendre la liste
des partenaires accessible via « Personnaliser mes choix ».

**Boutons :** Autoriser tous les cookies / Continuer sans accepter /
Personnaliser mes choix.

### Second écran

**Titre :** Personnaliser mes choix

**Introduction :** « Choisissez les usages et partenaires que vous souhaitez
autoriser. Les traceurs strictement nécessaires restent actifs. Vos modifications
seront enregistrées lorsque vous cliquerez sur “Valider mes préférences”. »

**Nécessaires :** « Ces traceurs permettent les fonctions indispensables du site,
comme {fonctions_necessaires_reelles} et la mémorisation de vos choix. Ils ne sont
pas désactivables depuis cet écran. » Ne pas inventer panier ou connexion si absents.

**Famille :** {nom_purpose} — {description_de_la_finalite}.

**Vendor :** {nom_vendor} — {usage_reel_sur_le_site}. Afficher un lien de
confidentialité lorsqu'il est fourni. Utiliser une description dépliable.

**Famille Google, si sélectionnée :** « Ces options décrivent les usages Google
que vous autorisez. Vous pouvez les régler séparément. » Utiliser les quatre
options de l'étape 3, avec un toggle de famille et un état mixte.

**Bouton :** Valider mes préférences.

## 5. Demander la portée du cookie

Demander explicitement :

> Souhaites-tu partager le choix sur le domaine racine et ses sous-domaines
> (par exemple `example.com`, `www.example.com`, `shop.example.com`), ou le limiter
> au seul hôte du site ? Quel domaine ou sous-domaine faut-il utiliser ?

Comprendre « top level domain » comme domaine enregistrable/racine, jamais comme
`.com`, `.fr` ou un autre suffixe public. Utiliser la réponse comme configuration
explicite, sans déduire le domaine par découpage des deux derniers segments.

- Partage : `Domain=example.com` selon le domaine validé.
- Hôte uniquement : omettre `Domain` ; ne pas remplacer cette omission par
  `Domain=www.example.com`, qui étendrait la portée à ses propres sous-domaines.
- Utiliser `Path=/`, `SameSite=Lax` et `Secure` sur HTTPS, sans `HttpOnly` puisque
  GTM et la CMP doivent pouvoir lire la valeur en JavaScript.
- Traiter localhost/HTTP en développement sans étendre cette exception à HTTPS
  en production.

Utiliser `mm_consent` sauf convention ou nom existant pertinent. Poser un état
initial `denied` sans le confondre avec un refus exprimé. Rendre le cookie lisible
sans SDK. Appliquer le schéma et les transitions suivants.

### Encodage et état du consentement

Utiliser une convention stable : JSON sérialisé en UTF-8, puis encodé en Base64
standard. Décoder Base64 en UTF-8 puis appeler `JSON.parse` pour lire.
Ne pas présenter Base64 comme hash, chiffrement ou compression. Éviter `btoa`
directement sur Unicode ; réutiliser un codec UTF-8 natif adapté aux navigateurs.
Garder le cookie petit et contrôler sa taille réelle pour respecter les limites
des navigateurs. Lire et écrire la même convention.

### Exemple d'objet décodé

```json
{
  "schema_version": 1,
  "config_version": 1,
  "has_choice": true,
  "last_consent": 1791720000000,
  "expires_at": 1807444800000,
  "meta": "granted",
  "tiktok": "denied",
  "como_analytics_storage": "granted",
  "como_ad_storage": "denied",
  "como_ad_user_data": "denied",
  "como_ad_personalization": "denied"
}
```

Les dates sont illustratives : calculer l'échéance depuis la durée choisie.
Conserver les `vendor_id` au premier niveau. Inclure toutes les clés configurées,
avec `granted` ou `denied`, y compris les refus. Inclure les
clés `como_*` seulement si les options distinctes sont affichées. Garder les
nécessaires hors des préférences facultatives.

Définir `schema_version` pour le format de stockage et `config_version` pour la
configuration des vendors et finalités. Incrémenter la version de configuration
si une nouvelle finalité ou un nouveau vendor est introduit, ou si leurs usages
changent. Comparer cette version à la configuration courante pour détecter un
ancien choix qui ne couvre pas les nouveaux usages.

Définir `last_consent` comme date de la dernière **décision**, acceptation, refus
ou choix partiel, en millisecondes Unix. Avant décision : `has_choice: false`,
`last_consent: null`, `expires_at: null`, toutes les préférences facultatives
`denied`. La présence du cookie initial ne doit jamais masquer la bannière à
la visite suivante ni transformer le silence en refus enregistré.

Sur décision : construire l'état complet, `has_choice: true`, calculer les dates,
écrire le cookie avec une expiration correspondante, puis publier les pushes.
Appliquer la durée également au refus. Ne pas réécrire un choix valide à chaque
visite. Ne pas prendre une durée fixe en secondes pour un équivalent exact de six
mois calendaires ; gérer la fin de mois sans débordement.

Traiter un cookie absent, expiré, corrompu ou de version non prise en charge comme
un état sans choix. Une valeur inconnue ou une clé manquante n'accorde aucun droit.
Lors d'un ajout de finalité ou vendor, ne pas autoriser la nouvelle clé au titre
d'un ancien « tout accepter » ; invalider le choix pour demander les nouveaux choix.
Ne pas dépendre de GTM pour valider les métadonnées de stockage.

Si l'écriture échoue, maintenir une UI utilisable et signaler l'absence de
persistance. Ne pas émettre un consentChange annonçant une décision enregistrée
que GTM ne peut pas relire. Si aucun état valide n'est disponible au prochain
chargement, repartir sans autorisation. Documenter cette limitation dans la recette.

## 6. Demander la durée du cookie

Demander explicitement :

> Par défaut, les choix sont conservés six mois, sans prolongation automatique
> pendant la navigation. Souhaites-tu une autre durée ?

Utiliser six mois calendaires si confirmé ; pour une autre durée, demander une
valeur positive et son unité. Appliquer la même durée aux acceptations, refus et
choix partiels. Démarrer cette durée à la décision explicite ; ne pas renouveler
le cookie à chaque lecture, chargement ou changement de route.

À expiration, réafficher la CMP avec les préférences facultatives désactivées.
Une nouvelle décision explicite crée une nouvelle échéance. À état initial sans
choix, ne pas considérer une expiration technique comme un refus conservé.

## 7. Prévoir les tests et la non-régression

Réutiliser la suite du projet. Automatiser en priorité les transitions, le stockage
et l'ordre cookie/push ; tester dans le navigateur la persistance réelle, les
attributs, les deux écrans et le footer.

Vérifier desktop/mobile, routes et, si applicable, SSR/hydratation et SPA.
Comparer build, tests existants et parcours essentiels avant et après modification.
Ne pas ajouter de tests portant seulement sur les classes CSS ou les détails privés.

Tester le contrat CMP avec un data layer observé ou simulé, sans dépendre d'un
accès GTM. Séparer les résultats CMP de la recette GTM réelle : ne pas annoncer
des tags bloqués, dédupliqués ou désactivés sans vérification du conteneur.
Fournir ces vérifications au responsable GTM sans en faire une condition
supplémentaire à la création de la CMP.

### Scénarios de recette

Tester localement ou en recette, sans commande ni achat réels. Adapter la
couverture au site et aux outils disponibles ; une suite navigateur légère suffit
si aucun framework de tests n'existe. Distinguer tests exécutés et prescrits.

| Scénario | Résultat attendu |
| --- | --- |
| Première visite sans cookie | Modale visible ; état initial denied ; has_choice false ; aucun consentChange. |
| Nouvelle visite après silence | Modale visible malgré le cookie initial. |
| Tout accepter | Préférences facultatives et options Google affichées granted ; choix daté ; un consentChange First Consent. |
| Continuer sans accepter | Préférences facultatives denied ; choix daté ; fermeture ; un consentChange First Consent. |
| Revenir après refus | Modale fermée ; refus restauré ; aucun nouveau consentChange. |
| Modifier un toggle | Aucune modification du cookie ni consentChange avant validation. |
| Accepter une famille puis refuser un membre | Famille mixte ; seuls les membres autorisés granted après validation. |
| Valider un choix partiel | État complet enregistré, y compris les denied ; un consentChange. |
| Ouvrir depuis le footer | Préférences restaurées ; focus correct ; éventuel display Footer uniquement. |
| Réenregistrer sans modification | Un consentChange Ulterior consent ; aucun tracking exécuté directement par la CMP. |
| Retirer une acceptation | Refus enregistré ; un consentChange Ulterior consent ; cookie CMP conservé. |
| Lire le cookie dans le callback du push | Nouvelles valeurs déjà présentes. |
| Charger sans GTM | CMP utilisable ; pushes mis en file ; tableau dataLayer existant conservé. |
| Cookie corrompu, expiré ou incompatible | Pas d'autorisation implicite ; nouvelle demande. |
| Vendor ou finalité ajouté | Pas de granted hérité pour la nouvelle clé ; nouvelle demande. |
| Naviguer avec choix valide | Cookie et échéance non renouvelés ; état conservé. |
| Fin de mois / durée personnalisée | Échéance selon durée annoncée, sans débordement de mois. |
| Valeur Unicode | Aller-retour codec sans erreur ni perte. |
| Cookie sur HTTPS | Path, Secure, SameSite et portée configurés ; absence de HttpOnly. |
| Partage de sous-domaines | Même choix lu sur les hôtes accessibles du domaine configuré. |
| Limitation à un hôte | Cookie host-only, sans Domain ; pas de partage avec un autre hôte. |
| SPA, remontage, hydratation | Pas de crash window/document côté serveur, d'instance ou de listeners doublés. |
| Events optionnels désactivés | Seuls les consentChange obligatoires sont poussés. |
| Events optionnels activés | Libellés exacts ; un event par interaction effective. |
| File ecommerce existante | File préservée ; aucun payload ecommerce modifié ou purgé. |
| Cookie refusé par navigateur | UI utilisable ; aucune promesse de persistance ou de lecture GTM. |
| Clavier, lecteur d'écran, mobile | Contrôles nommés, focus contenu dans la modale, boutons accessibles. |
| Parcours essentiels | Navigation, formulaires, panier et connexion utilisables après acceptation ou refus. |
| Build, lint, tests existants | Pas de nouvelle erreur imputable à l'intégration. |

## 8. Implémenter le contrat data layer

Conserver exactement `event: 'cmpInteraction'`, l'objet `cmpInteraction`, ses
actions et les valeurs ci-dessous. Initialiser `window.dataLayer` seulement s'il
n'existe pas et conserver toute sa file ; ne pas remplacer le tableau existant.

Émettre une fois `consentChange` après chaque décision explicite : tout accepter,
continuer sans accepter, valider les préférences. Écrire le cookie avant ce push.
Inclure les validations ultérieures même si le choix est inchangé ; laisser GTM
décider des déclenchements et de leur déduplication. Ne pas émettre `consentChange`
pour la pose initiale, l'ouverture, un toggle ou un rechargement de page.

Garder le cookie comme source des états pour GTM. Ne pas ajouter de listes de
vendors/purposes au payload minimal, de `cmpReady`, de trigger group, de page vue,
de replay ecommerce ni de données personnelles. Remettre la liste des clés de
consentement à l'intégrateur GTM.

Prévoir `display` et `button click` comme options désactivées par défaut, activées
si demandées. Pour un bouton enregistrant un choix, écrire le cookie avant tous
les pushes liés au clic. Ne pas envoyer ces interactions à un outil analytics
depuis la CMP. Ne pas modifier `ecommerce` dans les pushes.

Conserver exactement ce contrat minimal.
Le cookie est la source des préférences détaillées ; ne pas dupliquer les états
dans le payload sans demande explicite de modification du contrat.

### Décision explicite — obligatoire

```js
window.dataLayer = window.dataLayer || [];
window.dataLayer.push({
  event: 'cmpInteraction',
  cmpInteraction: {
    action: 'consentChange',
    changeType: 'First Consent'
  }
});
```

Utiliser exactement `First Consent` pour la première décision suivant l'absence
de choix valide, y compris un refus ; `Ulterior consent` pour une décision sur
un choix déjà enregistré et valide. Calculer cette distinction depuis l'état
**avant** la décision, pas depuis la seule présence du cookie initial.
Respecter exactement la casse de ces valeurs. Ne pas remplacer `consentChange`
par `accept`.

Émettre une fois par action de validation, même à valeurs identiques. Ne pas
émettre à chaque toggle. Empêcher une deuxième validation du même formulaire
fermé en cas de double clic accidentel.

### Affichage — optionnel

```js
window.dataLayer.push({
  event: 'cmpInteraction',
  cmpInteraction: {
    action: 'display',
    displayType: 'Website load'
  }
});
```

Utiliser `Website load` pour l'ouverture automatique et `Footer` pour la
réouverture depuis le footer. Émettre à chaque ouverture effective, pas à chaque
rerender ni au passage entre les écrans. Ne pas émettre au chargement si la CMP
reste fermée parce qu'un choix valide existe.

### Clic sur un bouton — optionnel

```js
window.dataLayer.push({
  event: 'cmpInteraction',
  cmpInteraction: {
    action: 'button click',
    button: 'Continuer sans accepter'
  }
});
```

Utiliser le libellé affiché dans `button`. Pour les boutons qui enregistrent,
écrire le cookie avant tous les pushes du clic, puis émettre l'éventuel
`button click` et l'unique `consentChange`. Pour la personnalisation, émettre
seulement le clic si activé ; ne pas enregistrer de choix. Ne pas transformer
les toggles en `button click` par défaut.

## Implémenter et livrer

Une fois les décisions requises recueillies et les textes approuvés, implémenter
configuration, codec, écrans, footer et tests. Utiliser une configuration de
vendors/purposes modifiable sans changer le moteur. Éviter les dépendances réseau
nécessaires au fonctionnement de la bannière. Préserver URL, paramètres et referrer.

Exposer une fonction pour ouvrir les préférences et un lecteur synchrone du
consentement décodé, selon les conventions du projet. Nettoyer les listeners au
démontage et éviter les événements doublés par les rerenders.

Terminer par : fichiers modifiés, décisions retenues, nom/format/portée/durée du
cookie, clés de consentement, contrat des pushes, commandes de validation et
résultats, puis vérifications restant à effectuer dans GTM. Ne pas déclarer une
recette complète si un environnement ou navigateur n'a pas pu être testé.

## Transmettre le contrat à GTM

Fournir nom, portée, codec, métadonnées et clés vendor/Google du cookie, ainsi que
les trois pushes et leurs valeurs. Préciser que `consentChange` signifie décision
enregistrée, pas forcément accord. La présence du cookie ou un affichage ne vaut
pas consentement.

Remettre ces vérifications au responsable GTM :

- Initialiser et restaurer Consent Mode avant les tags concernés ; mapper les
  clés vers les API natives GTM dans le template de consentement.
- Lire les choix valides sur les événements de page et métier ; traiter une clé
  inconnue ou absente comme non autorisée.
- Sur consentChange, gérer l'ordre Consent Mode/tags, les vendors nouvellement
  autorisés, les refus et les validations inchangées.
- Vérifier les doublons page_view/purchase et préciser le traitement des events
  précoces ; ne pas ajouter de trigger group par défaut.
- Vérifier le retrait dans les SDK chargés, tags hors GTM, contenus embarqués et,
  si applicable, tracking server-side.
- Si les interactions CMP sont mesurées, vérifier que leur envoi respecte les choix.

Indiquer « à vérifier côté GTM » pour les points non exécutés dans un conteneur.
Ne pas présenter un push correct comme preuve du comportement des tags.
