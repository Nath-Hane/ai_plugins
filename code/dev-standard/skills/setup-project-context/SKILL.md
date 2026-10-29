---
name: setup-project-context
description: >-
  Initialise ou audite l'arborescence d'ingénierie d'un projet (.agents/, docs/adr/, COMMANDS.md, LEARNINGS.md),
  sécurise le .gitignore et installe les skills de Matt Pocock de manière déterministe avec fallback sécurisé.
---

# Setup Project Context (Dev Standard)

Ce skill permet d'initialiser ou d'auditer l'infrastructure documentaire et méthodologique d'un projet de développement pour l'agent IA.

---

## Procédure Séquentielle

### Étape 1 : Création de l'Arborescence Spatiale
Vérifier l'existence et créer si nécessaire les répertoires suivants :
* `.agents/tmp/` (scratchpad temporaire des agents)
* `.agents/docs/` (recherches et documents libres)
* `.agents/skills/` (répertoire des compétences locales)
* `docs/adr/` (décisions d'architecture)
* `docs/agents/` (configuration Matt Pocock)

---

### Étape 2 : Sécurisation du `.gitignore`
Vérifier le fichier `.gitignore` à la racine du projet.
Si les entrées suivantes sont absentes, les ajouter à la fin du fichier :
```gitignore
# AI Agent scratchpads & ephemeral files
.agents/tmp/
.scratch/
```

---

### Étape 3 : Installation Sécurisée des Skills de Matt Pocock

1. **Contrôle d'Idempotence :**
   Vérifier si le dossier `.agents/skills/grill-with-docs/` ou `.agents/skills/to-spec/` existe déjà dans le projet ou dans la configuration globale de l'agent.
   * Si les compétences sont déjà présentes : **Ne pas écraser**. Passer directement à l'Étape 4.

2. **Méthode Principale (`npx skills`) :**
   Tenter d'exécuter la commande officielle :
   ```bash
   npx skills@latest add mattpocock/skills
   ```
   Si la commande réussit, passer à l'Étape 4.

3. **Fallback Sécurisé (Sans `.git`) :**
   Si Node/npx échoue ou n'est pas installé, exécuter ce script sécurisé (PowerShell ou Bash selon l'OS) qui télécharge l'archive GitHub sans créer de sous-dossier `.git` :
   * Télécharger l'archive ZIP : `https://github.com/mattpocock/skills/archive/refs/heads/main.zip` dans `.agents/tmp/skills-archive.zip`.
   * Décompresser l'archive dans un dossier temporaire dans `.agents/tmp/`.
   * Parcourir les dossiers extraits (`skills/engineering/*` et `skills/productivity/*`) pour déplacer chaque dossier contenant un `SKILL.md` directement sous `.agents/skills/<skill-name>/` (mise à plat requise par AntiGravity).
   * Supprimer l'archive et le dossier temporaire dans `.agents/tmp/`.

---

### Étape 4 : Déploiement des Modèles de Cadrage

Déployer les fichiers de référence à partir des templates situés dans `./resources/` (uniquement si les fichiers cibles n'existent pas déjà) :
1. `.agents/COMMANDS.md` : Initialiser à partir de `[template-commands.md](./resources/template-commands.md)`.
   * **Détection automatique de la stack :**
     * Si `package.json` existe : suggérer `npm test` ou `pnpm test`, `npm run lint`.
     * Si `pyproject.toml` ou `requirements.txt` existe : suggérer `pytest`, `mypy .`.
     * Si `Cargo.toml` existe : suggérer `cargo test`, `cargo clippy`.
2. `.agents/LEARNINGS.md` : Initialiser à partir de `[template-learnings.md](./resources/template-learnings.md)`.
3. `.agents/CONVENTIONS.md` : Initialiser à partir de `[template-conventions.md](./resources/template-conventions.md)`.
4. `.agents/docs/INDEX.md` : Initialiser à partir de `[template-index-docs.md](./resources/template-index-docs.md)`.
5. `docs/adr/INDEX.md` : Initialiser à partir de `[template-index-adr.md](./resources/template-index-adr.md)`.
6. `.agents/template-ticket.md` : Copier `[template-ticket.md](./resources/template-ticket.md)` comme référence locale pour la création de tickets ICM.

---

### Étape 5 : Rapport de Synthèse & Prochaines Actions

Afficher un compte-rendu clair à l'utilisateur :
* Liste des dossiers et fichiers créés ou vérifiés.
* Statut de l'installation des skills de Matt Pocock.
* Rappel des commandes prêtes à l'emploi :
  * `/grill-with-docs` pour cadrer une nouvelle fonctionnalité et générer des ADRs.
  * `/setup-matt-pocock-skills` pour choisir son tracker (`docs/agents/issue-tracker.md`).
  * `/to-spec` et `/to-tickets` pour planifier le travail au format ICM.
