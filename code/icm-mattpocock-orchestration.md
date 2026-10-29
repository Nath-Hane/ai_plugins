# Unification ICM (Interpretable Context Methodology) & Skills de Matt Pocock

Ce document synthétise les concepts, architectures et règles de gouvernance permettant de fusionner l'**Interpretable Context Methodology (ICM)** de Jake Van Clief et la philosophie des **Skills de Matt Pocock** dans un cadre de développement assisté par IA rigoureux et déterministe.

---

## 1. L'Interpretable Context Methodology (ICM - Jake Van Clief)

L'ICM repose sur un constat fondamental : pour des workflows séquentiels sous supervision humaine, les frameworks multi-agents complexes (LangChain, AutoGen, CrewAI) introduisent une surcharge d'abstraction inutile. L'ICM **remplace le code d'orchestration par la structure du système de fichiers**.

### A. La Hiérarchie de Contexte à 5 Couches

```text
[Layer 0] Identité Racine (GEMINI.md / AGENTS.md)    "Où suis-je ?"        ~800 tokens
   │
   ▼
[Layer 1] Routage du Workspace (CONTEXT.md)           "Où dois-je aller ?"  ~300 tokens
   │
   ▼
[Layer 2] Contrat d'Étape (stages/NN/CONTEXT.md)      "Que dois-je faire ?" 200–500 tokens
   │
   ├───────────────┬───────────────┐
   ▼                               ▼
[Layer 3] Références / L'Usine    [Layer 4] Artefacts / Le Produit
  (references/, _config/)           (output/)
  "Quelles règles s'appliquent ?"   "Sur quoi est-ce que je travaille ?"
  500–2 000 tokens (Invariable)     Taille variable (Par exécution)
```

1. **Layer 0 (Identité & Environnement) :** Fichier global décrivant la nature du dépôt et les directives transversales.
2. **Layer 1 (Aiguillage général) :** Table de routage reliant l'intention utilisateur aux étapes correspondantes.
3. **Layer 2 (Contrat d'étape) :** Contrat triadique impératif :
   * `## Inputs` : fichiers précis des couches 3 et 4 à ingérer.
   * `## Process` : consignes d'exécution spécifiques à l'étape.
   * `## Outputs` : destination exacte et format des livrables.
4. **Layer 3 (Le Matériel de Référence - "L'Usine") :** Directives de style, conventions d'architecture, chartes, templates. Reste stable à travers les exécutions. Le LLM doit l'intérioriser comme des **contraintes**.
5. **Layer 4 (Les Artefacts de Travail - "Le Produit") :** Données d'entrée, résultats d'étapes précédentes, code produit. Évolue à chaque exécution. Le LLM doit le traiter comme de la **matière à transformer**.

### B. Principes Directeurs de l'ICM
* **One stage, one job :** Une étape qui extrait de la donnée ne la filtre pas. Une étape qui filtre ne formate pas le livrable final (analogie avec les passes d'un compilateur multi-passes).
* **Texte brut comme interface universelle :** Markdown et JSON exclusifs. Pas de formats binaires opaques ou de bases de données internes. Tout humain avec un éditeur peut inspecter l'état du système.
* **Chargement de contexte étagé (*Layered Context Loading*) :** Chaque étape ne charge que 2 000 à 8 000 tokens ciblés, éliminant la dégradation d'attention (*Lost in the Middle*) des invites monolithiques de 40k+ tokens.
* **Chaque sortie est une surface d'édition (*Review Gates*) :** L'humain peut ouvrir, relire, modifier et corriger l'artefact produit dans `output/` avant d'autoriser l'étape suivante.
* **Le principe d'intégrité de la source (*Edit-Source Principle*) :** Modifier directement le livrable généré revient à patcher un binaire compilé. Quand une erreur récurrente apparaît, la correction pérenne consiste à rectifier la source (la règle Layer 3 ou le contrat Layer 2).

---

## 2. La Philosophie des Skills de Matt Pocock (`mattpocock/skills`)

Matt Pocock propose un cadre méthodique pour les ingénieurs logiciels opérant avec des agents IA, refusant le « vibe coding » au profit de processus prévisibles et testables.

### L'Épine Dorsale "Idea-to-Ship" (Spine)
1. **Interrogatoire & Cadrage (`/grill-me`, `/grill-with-docs`) :**
   * L'agent pose des questions ciblées pour tester les hypothèses, identifier les cas limites et verrouiller l'architecture avant toute implémentation.
2. **Spécification formelle (`/to-spec`) :**
   * Transformation du dialogue de cadrage en une spécification écrite, exhaustive et falsifiable.
3. **Fragmentation atomique (`/to-tickets`) :**
   * Découpage de la spécification en tickets de travail indépendants, dotés de critères d'acceptation clairs.
4. **Implémentation pilotée par les tests (`/implement`, `/implement-spec`) :**
   * Développement strict en TDD (Red-Green-Refactor) sur un ticket à la fois.
5. **Revue de code & Preuve de conformité (`/code-review`, `/pr`) :**
   * Audit critique des diffs, validation de non-régression et conformité aux standards du dépôt.
6. **Boucle de rétrospective (`/retro`) :**
   * Débriefing post-tâche pour identifier les erreurs de l'IA et convertir ces enseignements en règles pérennes dans `AGENTS.md`.

---

## 3. Matrice d'Unification : ICM × Matt Pocock

L'unification consiste à matérialiser la démarche procédurale de Matt Pocock au sein de la structure spatiale de fichiers de l'ICM.

| Étape de travail | Compétence Pocock | Répertoire ICM | Contrat d'Étape (Layer 2) |
| :--- | :--- | :--- | :--- |
| **1. Cadrage & Découverte** | `/grill-me`, `/grill-with-docs` | `stages/01_discovery/` | **Inputs :** Besoins utilisateur, architecture existante<br>**Outputs :** `output/brief.md` |
| **2. Spécification** | `/to-spec` | `stages/02_specification/` | **Inputs :** `01_discovery/output/brief.md`, Layer 3 (règles métier)<br>**Outputs :** `output/spec.md` |
| **3. Découpage** | `/to-tickets` | `stages/03_tickets/` | **Inputs :** `02_specification/output/spec.md`<br>**Outputs :** `output/tickets/*.md` |
| **4. Implémentation** | `/implement` (TDD) | `stages/04_implementation/` | **Inputs :** Ticket actif, code source, Layer 3 (coding standards)<br>**Outputs :** Code source modifié + tests passants |
| **5. Audit & Validation** | `/code-review` | `stages/05_review/` | **Inputs :** Diff Git, spécification source<br>**Outputs :** `output/review-report.md` |
| **6. Amélioration Continue**| `/retro` | *Système global* | **Action :** Mise à jour de Layer 3 (`references/`) et Layer 0 (`AGENTS.md`) |

---

## 4. Gouvernance des Fichiers et Gestion du Contexte

Pour éviter la pourriture du contexte (*Context Rot*) et la prolifération de fichiers orphelins :

### A. Ségrégation Étanche des Types de Fichiers
* **Artefacts de production (Définitifs) :** Strictly confinés dans les dossiers `output/` d'une étape donnée ou dans le code source officiel du dépôt.
* **Fichiers de scratchpad temporaires :** Interdiction stricte de créer des fichiers de logs, scripts de test temporaires ou notes volantes à la racine du workspace. Ils doivent être placés dans l'espace réservé AntiGravity : `<appDataDir>/brain/<id>/scratch/`.
* **Références stables (Layer 3) :** Placés dans `references/` ou `_config/`. Ils sont en lecture seule pour l'agent lors des étapes 01 à 05 et ne sont mis à jour que lors de l'étape de rétrospective.

### B. Le "Contract Reader Protocol" (Protocole de Lecture Obligatoire)
Avant toute exécution de tâche ou d'écriture de code, l'agent doit impérativement respecter la séquence :
1. **Localiser le Contrat d'Étape :** Identifier le dossier `stages/NN_*/CONTEXT.md` correspondant à la phase en cours.
2. **Lire la section `## Inputs` :** Charger en contexte **uniquement** les fichiers mentionnés dans la table des inputs. Ne jamais tenter de lire l'intégralité du projet d'un coup.
3. **Consulter la section `## Process` :** Suivre les étapes méthodologiques prescrites.
4. **Écrire l'artefact dans `## Outputs` :** Déposer le livrable au chemin spécifié, sans créer de fichiers annexes dispersés.
5. **Déclencher la Porte de Revue (*Review Gate*) :** Notifier l'utilisateur de l'emplacement du livrable pour validation ou amendement direct avant de passer à l'étape suivante.

### C. La Boucle "Edit-Source"
Si l'utilisateur doit corriger manuellement un livrable dans `output/` (ex. une spec mal formulée ou une convention de code bafouée) :
* L'agent doit analyser la nature de la correction.
* Si l'erreur provient d'un manque de clarté dans les règles de l'usine, l'agent propose formellement d'amender le document Layer 3 correspondant dans `references/` ou la règle dans `AGENTS.md`.
