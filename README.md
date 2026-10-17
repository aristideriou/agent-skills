# Might & Metrics — Agent Skills

Des skills pour aider les agents de développement à réaliser des tâches de web analytics et de gestion du consentement.

Les instructions sont publiées au format ouvert [Agent Skills](https://agentskills.io/), pour une utilisation notamment avec Claude Code et Codex.

## Skills disponibles

| Skill | Usage |
| --- | --- |
| [build-custom-cmp](skills/build-custom-cmp/SKILL.md) | Créer une CMP maison adaptée à la stack du site, avec cookie de consentement et contrat data layer pour GTM. |

## Installation dans un projet

Prérequis : Node.js/npm et un agent de développement compatible.

Depuis la racine du projet cible :

```bash
npx skills add aristideriou/agent-skills --skill build-custom-cmp --copy
```

L’installateur propose les agents à cibler. Pour choisir directement :

```bash
# Claude Code
npx skills add aristideriou/agent-skills --skill build-custom-cmp --agent claude-code --copy

# Codex
npx skills add aristideriou/agent-skills --skill build-custom-cmp --agent codex --copy
```

Les points d’entrée sont `.claude/skills/` pour Claude Code et `.agents/skills/` pour Codex. Commitez les fichiers installés pour partager le skill avec les autres contributeurs du projet.

Pour une installation personnelle disponible dans tous vos projets, ajoutez `--global` à la commande d’installation.

## Utilisation

### Claude Code

Dans la conversation Claude Code du projet :

```text
/build-custom-cmp
```

### Codex

Dans la conversation Codex du projet :

```text
Utilise $build-custom-cmp pour créer une CMP maison adaptée à ce site.
```

## Ce que fait build-custom-cmp

Le skill inspecte le projet, puis accompagne l’utilisateur en huit étapes :

1. Adapter l’interface aux composants, styles et conventions du site.
2. Recueillir les partenaires et les finalités à afficher.
3. Demander si des tags Google sont utilisés et si Google Consent Mode doit avoir sa propre famille.
4. Proposer les textes des écrans et les faire valider.
5. Choisir la portée du cookie : hôte seul ou domaine partagé avec ses sous-domaines.
6. Confirmer la durée de conservation, de six mois par défaut.
7. Prévoir les tests et la non-régression.
8. Implémenter les événements data layer et leur séquencement.

Le fichier `SKILL.md` contient directement les spécifications des écrans, du cookie, des transitions et des événements GTM. Il peut être utilisé sans ressource éditoriale externe.

Le skill implémente l’interface CMP, les préférences et leur stockage. Le mapping Consent Mode, le blocage et le déclenchement des tags ainsi que leur déduplication restent à configurer et à vérifier dans GTM. La recette CMP et les vérifications attendues côté GTM sont décrites dans le skill.

## Installation manuelle

Copiez le dossier `skills/build-custom-cmp/` complet dans :

- `.claude/skills/build-custom-cmp/` pour Claude Code ;
- `.agents/skills/build-custom-cmp/` pour Codex.

Conservez les sous-dossiers `agents/` et `assets/` avec leurs chemins relatifs.

## Vérification et mises à jour

Lister les skills proposés par ce dépôt :

```bash
npx skills add aristideriou/agent-skills --list
```

Lister les skills installés dans le projet :

```bash
npx skills list
```

Récupérer les mises à jour des skills installés :

```bash
npx skills update
```

Après une mise à jour, vérifiez les changements des instructions avant de les utiliser dans votre projet.

## Contributions

Les retours et propositions d’amélioration sont les bienvenus via les [issues](https://github.com/aristideriou/agent-skills/issues) et les pull requests.

Pour proposer un nouveau skill, ajoutez un dossier `skills/<nom-du-skill>/` avec un `SKILL.md` contenant au minimum les champs YAML `name` et `description`, puis mettez à jour la liste ci-dessus. Gardez les instructions autonomes et les exemples sans données propres à un client.

## Auteur et licence

Créé par [Aristide Riou](https://github.com/aristideriou), [Might & Metrics](https://www.mightandmetrics.io/).

Distribué sous licence [MIT](LICENSE).
