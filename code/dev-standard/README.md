# Dev Standard & Context Orchestrator

Plugin AntiGravity pour l'orchestration disciplinée du développement assisté par IA. Il unifie l'**Interpretable Context Methodology (ICM)** de Jake Van Clief et la suite de skills d'ingénierie de **Matt Pocock** (`mattpocock/skills`).

---

## Fonctionnalités Principales

* **Routage de Contexte Multi-Niveaux (ICM) :** L'agent ne charge que les documents strictement nécessaires à la phase en cours (cadrage, implémentation, debug, rétrospective), évitant la saturation de la fenêtre de contexte et la perte d'attention (*Lost in the Middle*).
* **Protocole Contract-in-Ticket :** Chaque tâche dispose d'un contrat explicite (`## Inputs`, `## Expected Outputs`, `## Verification Criteria`). L'agent a l'interdiction de toucher au code sans avoir lu les entrées déclarées.
* **Invariants Logiciels Stricts :** Développement piloté par les tests (TDD), modifications chirurgicales minimales, programmation défensive sans exception silencieuse, preuve d'exécution réelle.
* **Gouvernance des Fichiers d'Agents :**
  * `.agents/tmp/` : Scratchpad temporaire horodaté et exclu de Git.
  * `.agents/docs/` : Documents libres indexés dans `INDEX.md` pour un chargement ciblé.
  * `docs/adr/` : Architecture Decision Records alignés sur `/grill-with-docs`.
  * `.agents/COMMANDS.md` & `.agents/LEARNINGS.md` : Vérité terrain des commandes et mémoire des pièges passés.
* **Porte de Revue Humaine & Commits Atomiques :** Arrêt systématique après chaque implémentation réussie, génération d'une fiche de test manuel pas-à-pas, rédaction d'un message de commit conforme (anglais, mode impératif, test "If applied...", max 50 chars, What & Why) et staging chirurgical post-validation explicite (`human-review-and-commit`).
* **Mise à Jour Déterministe & Idempotente :** `/update-dev-standard` synchronise chirurgicalement le moteur du plugin depuis le monorepo GitHub central (`Nath-Hane/ai_plugins/code`) sans jamais écraser l'état ou les documents propres au projet.
* **Skill d'Initialisation Automatisé :** `/setup-project-context` initialise l'arborescence, installe de manière sécurisée les skills de Matt Pocock et configure les templates prêts à l'emploi.

---

## Installation & Utilisation

### Initialiser un Projet
Dans n'importe quel dépôt, lancez la commande :
```text
/setup-project-context
```
Le skill vérifie votre environnement, crée l'arborescence `.agents/`, met à jour votre `.gitignore`, installe les skills de Matt Pocock dans `.agents/skills/` et déploie les modèles de cadrage.

### Mettre à Jour le Plugin dans un Projet
Pour récupérer la dernière version du moteur depuis GitHub (`Nath-Hane/ai_plugins/code`) :
```text
/update-dev-standard
```

### Cycle de Travail Typique (Spine)
1. **Cadrage :** `/grill-with-docs` (remplit `CONTEXT.md` et crée les ADRs dans `docs/adr/`).
2. **Spécification :** `/to-spec` (génère la spec dans `.scratch/<feature>/spec.md`).
3. **Tickets :** `/to-tickets` (génère les tickets ICM dans `.scratch/<feature>/issues/`).
4. **Implémentation :** `/implement` (lit les `## Inputs`, applique le TDD et valide via `.agents/COMMANDS.md`).
5. **Revue Manuelle & Commit :** `/human-review-and-commit` (fiche de test manuel pas-à-pas, proposition de commit strict, validation humaine explicite et staging chirurgical).
6. **Revue de Code Approfondie :** `/code-review` (audit du diff).
7. **Rétrospective :** `/retro` (enrichit `.agents/LEARNINGS.md` et `docs/adr/`).
