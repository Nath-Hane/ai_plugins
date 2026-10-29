# Guide d'Architecture & Bonnes Pratiques des Plugins AntiGravity

Ce document constitue la référence technique pour la conception, l'intégration et l'exploitation des plugins au sein de l'environnement Google AntiGravity (AGY).

---

## 1. Fondations Conceptuelles

Un **plugin** AntiGravity est un bundle autonome et versionnable regroupant un ensemble cohérent de personnalisations :
* **Règles (`rules`)** : Directives passives et invariantes appliquées continuellement à l'agent.
* **Compétences (`skills`)** : Procédures modulaires exécutées à la demande via le mécanisme de divulgation progressive (*Progressive Disclosure*).
* **Sous-agents (`agents`)** : Rôles et personas délégués opérant dans des contextes isolés.
* **Crochets de cycle de vie (`hooks`)** : Scripts et commandes déclenchés lors d'événements de l'agent.
* **Serveurs d'outils (`mcp`)** : Connecteurs Model Context Protocol (MCP) isolés et activés uniquement avec le plugin.

L'activation ou la désactivation d'un plugin active ou coupe l'ensemble de son bundle en une seule opération atomique.

---

## 2. Anatomie d'un Plugin AntiGravity

L'arborescence standard d'un plugin dans le système se structure ainsi :

```text
<dossier-du-plugin>/
├── plugin.json         # Manifeste obligatoire (JSONC)
├── README.md           # Documentation utilisateur (non injectée dans le contexte du modèle)
├── mcp_config.json     # Optionnel : serveurs MCP spécifiques au plugin
├── hooks.json          # Optionnel : hooks de cycle de vie
├── assets/
│   └── logo.svg        # Optionnel : icône carrée >=128x128
├── rules/
│   └── AGENTS.md       # Règles obligatoires/passives du plugin (sans frontmatter)
├── skills/
│   └── <skill_name>/
│       ├── SKILL.md    # Procédure avec frontmatter YAML (name, description)
│       ├── scripts/    # Scripts exécutables par l'agent
│       ├── examples/   # Exemples de référence
│       └── references/ # Documentation volumineuse lue à la demande
└── agents/
    └── <agent_name>.md # Spécification de sous-agents
```

### Le Manifeste : `plugin.json`
Le fichier `plugin.json` supporte la syntaxe JSONC (commentaires et virgules de fin tolérés).

```jsonc
{
  "name": "mon-plugin",
  "displayName": "Mon Plugin Pro",
  "description": "Description concise (1-2 phrases) utilisée pour la recherche et l'indexation.",
  "version": "1.0.0",
  "logo": "assets/logo.svg",
  "suggestedPrompts": [
    "Action de démarrage 1",
    "Action de démarrage 2",
    "Action de démarrage 3"
  ],
  "disabled": false
}
```

> **Attention technique :** Les champs inconnus tels que `author` ou `homepage` sont silencieusement ignorés par le chargeur AntiGravity. Le tableau `suggestedPrompts` ne doit pas contenir plus de 3 éléments (les éléments au-delà du 3e sont ignorés par l'interface).

---

## 3. Mécanismes Internes & Contraintes Système

### A. L'Identifiant Clé : `dir` vs `name`
* Le champ `"name"` de `plugin.json` est le libellé visuel et la clé globale d'unicité.
* **Le répertoire d'installation (`dir`) est l'identifiant effectif de mutation.** Toutes les opérations programmatiques (activation, désactivation, suppression via le Language Server) s'effectuent sur le nom du dossier disque (`dir`), jamais sur le champ `"name"`.
* Deux plugins portant le même nom entrent en collision : le premier découvert prend la priorité, le second est **silencieusement ignoré**.

### B. Gestion des Budgets de Contexte & Règle des 24 Ko
Pour préserver la fenêtre de contexte de l'agent :
1. **Plafond par fichier de règles (`24 Ko`) :** Tout fichier de règle (`AGENTS.md`) est strictement plafonné à 24 000 octets. Au-delà, il est tronqué sur la dernière limite de ligne valide.
2. **Budget cumulé des règles (`20 000 tokens`) :** L'ensemble des règles actives partage un budget dédié de 20 000 tokens (`defaultRulesBudget`). Si le total dépasse cette limite, les règles excédentaires sont dégradées en pointeurs de chemin de fichiers, obligeant l'agent à effectuer une lecture d'outil explicite pour en prendre connaissance.
3. **Divulgation Progressive (*Progressive Disclosure*) des Skills :** Les compétences ne sont jamais injectées en totalité au démarrage. Seuls leurs métadonnées (nom + description YAML) consomment une poignée de tokens (~50 tokens par skill). Le corps de `SKILL.md` et les dossiers `references/` ne sont chargés que sur décision contextuelle du modèle ou demande de l'utilisateur.

### C. Cycle de Découverte & Ordre de Priorité
AntiGravity charge les composants selon la hiérarchie de précédence suivante (du plus prioritaire au moins prioritaire) :
1. **Workspace Project** : Découverte ascendante depuis le répertoire courant (CWD) jusqu'à la racine du dépôt (ex. `.agents/`).
2. **Configurations déclarées du Workspace** : Fichiers `plugins.json` ou `skills.json` locaux au projet.
3. **Configuration Globale** : Dossier machine local `~/.gemini/config/plugins/`.
4. **Plugins Intégrés (Built-in)** : Composants livrés avec la plateforme (`builtin/plugins/`).
5. **Configurations déclarées globales**.

> **Redémarrage :** La création physique d'un nouveau dossier de plugin nécessite un redémarrage de la session du serveur de langage (Language Server) pour être indexée. En revanche, l'activation/désactivation d'un plugin existant s'applique à chaud.

---

## 4. Crochets de Cycle de Vie (`hooks.json`)

Les hooks permettent d'exécuter des scripts locaux déterministes sans dépendre de la complaisance probabiliste du LLM.

* Emplacement : `<plugin_dir>/hooks.json` impérativement à la racine du plugin.
* Événements supportés :
  * `PreInvocation` : Exécuté avant que l'agent ne traite un tour de discussion.
  * `PreToolUse` : Exécuté avant qu'un outil ne soit appelé (possibilité de bloquer un appel risqué).
  * `PostToolUse` : Exécuté immédiatement après qu'un outil ait modifié un fichier ou exécuté une commande (idéal pour formater le code ou exécuter un linter).

Exemple type :
```json
{
  "code-quality-gate": {
    "PostToolUse": [
      {
        "matcher": "edit_file",
        "hooks": [
          {
            "type": "command",
            "command": "./scripts/format-check.sh",
            "timeout": 15
          }
        ]
      }
    ]
  }
}
```

---

## 5. Bonnes Pratiques de Conception d'un Plugin

1. **Responsabilité Unique & Cohésion Forte :** Un plugin doit répondre à un rôle précis (ex. ingénierie de code, documentation, intégration cloud). Éviter les bundles "fourre-tout" qui forcent l'utilisateur à un basculement tout-ou-rien.
2. **Isolation des règles vs procédures :**
   * Placer dans `rules/AGENTS.md` **uniquement les invariants** : lois architecturales non négociables, interdiction de patterns dangereux, formats de réponse obligatoires.
   * Déporter dans `skills/<name>/SKILL.md` **toutes les démarches séquentielles** : pas-à-pas de refactoring, configuration d'un environnement, audits de sécurité.
3. **Espace de nommage des serveurs MCP :** Les noms des outils MCP sont partagés dans un espace global. Dans `mcp_config.json`, préfixer systématiquement les serveurs par le nom du plugin (ex. `coding_tools` plutôt que `tools`).
4. **Documentation utilisateur étanche :** Rédiger un `README.md` complet pour l'humain à la racine du plugin, car ce fichier n'est pas ingéré par le LLM et n'alourdit pas la fenêtre de contexte.
