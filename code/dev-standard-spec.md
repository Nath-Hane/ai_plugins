# Spécification Détaillée : Plugin `dev-standard`
*(Version 2.0 - Alignement Natif ICM × Matt Pocock)*

Ce document formalise les spécifications complètes du plugin AntiGravity **`dev-standard`**. Il unifie la rigueur procédurale des skills de Matt Pocock et la philosophie de contexte à multi-niveaux de l'Interpretable Context Methodology (ICM).

---

## 1. Principes Fondateurs & Alignement

1. **Agnosticisme Total :** Aucune dépendance à un langage ou framework particulier. Les règles imposent des invariants d'ingénierie logicielle universels.
2. **Interopérabilité sans friction avec Matt Pocock :**
   * Respect strict des emplacements natifs exploités par les skills de Matt Pocock (`docs/adr/`, `docs/agents/`, `.scratch/`) pour bannir tout doublon ou hallucination d'aiguillage.
   * Format **Contract-in-Ticket** enrichi : les tickets générés (par `/to-tickets` ou manuellement) conservent leur emplacement d'origine mais intègrent la structure d'entrées/sorties ICM (`## Inputs`, `## Expected Outputs`, `## Verification Criteria`).
3. **Contexte à Multi-Niveaux (Philosophie ICM) :**
   * Macro-contexte : au niveau du dépôt (`CONTEXT.md`, `docs/adr/`, `.agents/`).
   * Micro-contexte local : fichiers `CONTEXT.md` colocalisés dans les sous-dossiers de code source à haute complexité métier pour expliciter les contrats locaux.
4. **Gouvernance Étanche des Fichiers d'Agents :**
   * `.agents/tmp/` pour le scratchpad d'exécution (ignoré par Git).
   * `.agents/docs/` piloté par `.agents/docs/INDEX.md` pour archiver et indexer les documents libres (recherches, audits, synthèses) sans saturer la fenêtre de contexte.
5. **Déploiement Initial :** Installation locale dans le workspace pour validation en conditions réelles avant promotion en configuration globale (`~/.gemini/config/plugins/`).

---

## 2. Topologie Standard d'un Dépôt Projet

Un projet administré avec `engineering-spine` et les skills de Matt Pocock s'organise ainsi :

```text
mon-projet/
├── CONTEXT.md                       # Contexte macro & domaine partagé (mis à jour par grill-with-docs)
├── .gitignore                       # Ignore obligatoirement .agents/tmp/ et .scratch/
│
├── docs/
│   ├── adr/                         # Architecture Decision Records natifs Pocock
│   │   ├── INDEX.md                 # Sommaire léger des décisions (Date | Titre | Statut | Résumé)
│   │   └── NNNN-<slug>.md           # Décisions structurelles rédigées lors des cadrages
│   └── agents/                      # Configuration issue du setup Matt Pocock
│       ├── domain.md                # Modèle de domaine & langage omniprésent
│       ├── issue-tracker.md         # Configuration du tracker (GitHub, Linear ou Local)
│       └── triage-labels.md         # Vocabulaire de triage
│
├── .scratch/                        # Emplacement NATIF Pocock pour specs et tickets locaux
│   └── <feature-slug>/
│       ├── spec.md                  # Spécification issue de /to-spec
│       └── issues/                  # Tickets générés par /to-tickets
│           └── <NN>-<slug>.md       # Tickets enrichis du contrat ICM (Inputs/Outputs/Verification)
│
├── .agents/                         # Hub de mémoire et gouvernance d'ingénierie
│   ├── COMMANDS.md                  # Vérité terrain : commandes de test, lint, build et migrations
│   ├── LEARNINGS.md                 # Mémoire des erreurs, pièges passés et enseignements (retro)
│   ├── CONVENTIONS.md               # Invariants de style, patterns et règles transversales du projet
│   ├── docs/                        # Documents libres générés par l'IA (recherches, analyses)
│   │   └── INDEX.md                 # Table d'indexation obligatoire pour découverte sans surcharge
│   └── tmp/                         # Fichiers scratch temporaires horodatés (purgés en fin de ticket)
│
├── src/                             # Code source
│   ├── common/
│   └── complex-domain/              # Sous-domaine complexe nécessitant un contrat local
│       ├── CONTEXT.md               # Contrat & invariants locaux du module (Philosophie ICM)
│       └── ...
└── tests/                           # Tests automatisés
```

---

## 3. Architecture du Plugin AntiGravity (`dev-standard`)

Le plugin réside dans le workspace de test sous `dev-standard/` :

```text
dev-standard/
├── plugin.json
├── README.md
├── rules/
│   └── AGENTS.md                    # Règles passives : routage de contexte, invariants, review gates & gouvernance
└── skills/
    ├── setup-project-context/
    │   ├── SKILL.md                 # Procédure de scaffolding et d'audit de conformité
    │   └── resources/
    │       ├── template-commands.md # Modèle pour .agents/COMMANDS.md
    │       ├── template-learnings.md# Modèle pour .agents/LEARNINGS.md
    │       ├── template-conventions.md # Modèle pour .agents/CONVENTIONS.md
    │       ├── template-index-docs.md  # Modèle pour .agents/docs/INDEX.md
    │       ├── template-index-adr.md   # Modèle pour docs/adr/INDEX.md
    │       └── template-ticket.md   # Modèle de ticket ICM (Inputs/Outputs/Verification)
    ├── human-review-and-commit/
    │   └── SKILL.md                 # Protocole de test manuel pas-à-pas et commit atomique post-validation
    └── update-dev-standard/
        └── SKILL.md                 # Mise à jour déterministe et idempotente depuis Nath-Hane/ai_plugins/code
```

---

## 4. Spécification Détaillée des Composants

### A. Manifeste `plugin.json`
* **Nom :** `dev-standard`
* **DisplayName :** `Dev Standard & Context Orchestrator`
* **Description :** `Orchestrateur universel unifiant la méthodologie ICM et les skills d'ingénierie : routage de contexte strict, gouvernance des fichiers IA, gestion des ADRs et invariants de code TDD.`
* **Version :** `1.0.0`
* **Repository :** `https://github.com/Nath-Hane/ai_plugins` (sous-dossier `code`)
* **Prompts suggérés :**
  1. `"Initialiser l'arborescence de contexte (.agents, docs/adr, commandes)"`
  2. `"Préparer la fiche de test manuel et le commit après implémentation"`
  3. `"Auditer les décisions dans docs/adr et consigner les apprentissages"`

---

### B. Règles Passives : `rules/AGENTS.md`
Le fichier de règles passives est condensé pour respecter scrupuleusement le budget de tokens. Il contient 5 sections :

#### 1. Matrice de Consultation Obligatoire (*Context Routing Table*)
L'agent doit impérativement respecter cette grille de lecture avant toute action :
* **Phase Cadrage / Architecture :**
  * Consulter l'index `docs/adr/INDEX.md` (ou lister `docs/adr/`). Si un ADR touche au sujet, en charger le contenu avant de formuler une proposition.
  * Consulter `docs/agents/domain.md` et le `CONTEXT.md` racine pour respecter scrupuleusement le langage omniprésent.
* **Phase Implémentation :**
  * Charger le ticket actif (dans `.scratch/**/issues/` ou le tracker configuré).
  * Exécuter le **Contract Reader Protocol** : lire obligatoirement tous les fichiers déclarés dans la section `## Inputs` du ticket.
  * Consulter `.agents/COMMANDS.md` pour identifier les commandes exactes de test et de validation.
  * Vérifier s'il existe un `CONTEXT.md` local dans le répertoire du code concerné.
* **Phase Résolution d'Anomalie / Debug :**
  * Consulter `.agents/LEARNINGS.md` pour vérifier si le piège a déjà été documenté.

#### 2. Format de Ticket Contractuel (*Contract-in-Ticket*)
Tout ticket d'implémentation doit comporter les sections :
* `## Inputs` : liste explicite des fichiers (specs, interfaces, ADRs, tests existants) à lire.
* `## Expected Outputs` : liste des fichiers cibles à créer/modifier.
* `## Verification Criteria` : commandes concrètes pour attester du succès.
* *Règle bloquante :* Interdiction absolue d'éditer le code source tant que les `## Inputs` n'ont pas été lus.

#### 3. Invariants de Code & TDD (Philosophie Pocock)
* **TDD Strict :** Identifier ou rédiger le test unitaire échouant avant de modifier l'implémentation (Red -> Green -> Refactor).
* **Modifications Chirurgicales :** Pas de réécriture stylistique hors du périmètre strict du ticket. Diff minimal et préservation absolue des commentaires et conventions existants.
* **Programmation Défensive :** Zéro exception silencieuse (`catch {}` ou `except: pass` interdits). Gestion explicite et typée des cas d'erreur.
* **Zéro Code Mort :** Pas de code commenté ou de modules orphelins.

#### 4. Protocole de Clôture, Revue Manuelle & Commit (Review Gate)
* Dès que les tests automatisés du ticket sont validés :
  * Arrêt obligatoire avant toute clôture : **interdiction formelle de commiter sans validation humaine explicite**.
  * Fournir la fiche de test manuel détaillée (pré-requis, actions pas-à-pas, résultat attendu).
  * Formuler la proposition de commit (anglais, mode impératif, test "If applied...", max 50 caractères, séparation par ligne vide, What & Why exclusivement).
  * Post-validation humaine : exécution d'un **staging chirurgical** (`git add <fichiers>` ciblés, pas de `git add .`) et commit atomique.

#### 5. Gouvernance des Fichiers Créés par l'IA
* **Fichiers temporaires :** Tout fichier de test temporaire, dump ou trace doit être placé dans `.agents/tmp/` sous la forme `YYYYMMDD_HHMM_<slug>.<ext>`.
* **Documents libres :** Tout document généré hors spec formelle (ex. audit, recherche web) doit être déposé dans `.agents/docs/` et immédiatement consigné dans `.agents/docs/INDEX.md` (Date, Fichier, Sujet en 1 ligne, Tags).
* **Nettoyage :** En fin de ticket, l'agent liste les fichiers créés dans `.agents/tmp/` et propose à l'utilisateur de les purger.

#### 6. Boucle Rétrospective & Principe *Edit-Source*
* Si une erreur est corrigée ou qu'un comportement anormal est découvert, l'agent doit demander la mise à jour de `.agents/LEARNINGS.md`.
* Si un invariant structurel a été transgressé par manque de clarté, l'agent propose d'amender `.agents/CONVENTIONS.md` ou d'initier un ADR dans `docs/adr/`.

---

### C. Skill : `setup-project-context`
* **Déclencheur :** `/setup-project-context` ou demande d'initialisation de projet.
* **Actions exécutées :**
  1. Crée l'arborescence requise (`.agents/tmp/`, `.agents/docs/`, `docs/adr/`).
  2. Vérifie et ajoute `.agents/tmp/` et `.scratch/` dans le fichier `.gitignore`.
  3. **Installation Sécurisée des Skills de Matt Pocock :**
     * *Vérification préalable :* Contrôle si les compétences (`grill-with-docs`, `to-spec`, etc.) existent déjà dans `.agents/skills/` ou en global. Si oui, passe l'étape (idempotence).
     * *Méthode Principale :* Tente l'exécution officielle `npx skills@latest add mattpocock/skills`.
     * *Fallback Résilient Sécurisé (Sans `.git`) :* En cas d'indisponibilité de Node/npx, télécharge l'archive ZIP/Tarball depuis l'API GitHub (`https://github.com/mattpocock/skills/archive/refs/heads/main.zip`), extrait les `SKILL.md` en aplatissant la structure thématique directement dans `.agents/skills/<skill-name>/SKILL.md` (format requis par AntiGravity), préserve les compétences locales existantes et supprime l'archive temporaire sans jamais introduire de sous-dossier `.git`.
  4. Déploie les templates initiaux :
     * `.agents/COMMANDS.md` (pré-rempli avec détection automatique du gestionnaire de paquets / compilateur).
     * `.agents/LEARNINGS.md` (initialisé vierge avec en-têtes explicatifs).
     * `.agents/CONVENTIONS.md` (structure de base des conventions du projet).
     * `.agents/docs/INDEX.md` (tableau d'index vierge).
     * `docs/adr/INDEX.md` (tableau d'index des décisions vierge).
  5. Valide la conformité de l'environnement avec un diagnostic final.

---

### D. Skill : `human-review-and-commit`
* **Déclencheur :** Fin d'implémentation de ticket ou demande de clôture/commit.
* **Actions exécutées :**
  1. Inspecte le diff réel (`git diff --stat`, `git status --porcelain`).
  2. Rédige la fiche de test manuel avec actions utilisateur concrètes et résultat attendu.
  3. Rédige la proposition de commit selon les 3 règles strictes (sujet impératif en anglais de 50 chars max, ligne vide, corps avec What & Why, commit atomique).
  4. Suspend l'exécution et attend la validation explicite de l'utilisateur.
  5. Après validation, effectue le staging chirurgical (`git add` ciblé) et crée le commit Git.

---

### E. Skill : `update-dev-standard`
* **Déclencheur :** `/update-dev-standard` ou demande de mise à jour du plugin.
* **Source distante :** `https://github.com/Nath-Hane/ai_plugins` (sous-dossier `code`).
* **Actions exécutées :**
  1. Contrôle préalable de version via `https://raw.githubusercontent.com/Nath-Hane/ai_plugins/main/code/plugin.json` (court-circuit idempotent si déjà à jour).
  2. Téléchargement de l'archive ZIP du monorepo dans `.agents/tmp/`.
  3. Extraction ciblée du sous-dossier `ai_plugins-main/code/`.
  4. Synchronisation chirurgicale du moteur uniquement (`rules/AGENTS.md`, `skills/`, `plugin.json`, `README.md`) vers le répertoire d'installation local.
  5. Sanctification garantie : zéro modification des données d'instance du projet (`COMMANDS.md`, `LEARNINGS.md`, `CONVENTIONS.md`, `docs/adr/`, `.agents/docs/`).
  6. Purge complète des fichiers temporaires dans `.agents/tmp/` et affichage du rapport de mise à jour.

---

## 5. Matrice des Risques & Mitigations

| Risque | Impact | Solution intégrée |
| :--- | :--- | :--- |
| **Conflit de chemins avec Matt Pocock** | Doublons de tickets ou d'ADRs. | Adoption des dossiers natifs de Pocock (`docs/adr/`, `.scratch/`, `docs/agents/`). |
| **Amnésie contextuelle de l'agent** | L'agent ignore les ADRs et viole l'architecture. | Matrice d'obligation explicite dans `rules/AGENTS.md` + passage par l'index `docs/adr/INDEX.md`. |
| **Pollution du dépôt Git** | Fichiers scratch temporaires commités par erreur. | Règle d'isolation dans `.agents/tmp/` + contrôle automatique du `.gitignore`. |
| **Érosion documentaire** | Fichiers libres enfouis et oubliés. | Règle d'or : tout ajout dans `.agents/docs/` exige la mise à jour de `INDEX.md`. |
