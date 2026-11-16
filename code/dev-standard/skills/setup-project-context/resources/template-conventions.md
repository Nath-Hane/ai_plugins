# Conventions d'Ingénierie, Stack & Patterns Autorisés

Ce document définit la stack technique de référence, les règles architecturales, les choix de conception invariables et les standards de code spécifiques à ce projet.

---

## 1. Stack Technique & Outillage
* **Langage(s) & Runtime :** `<ex. TypeScript 5.x / Node.js 22 LTS, Python 3.12>`
* **Framework(s) & Bibliothèques clés :** `<ex. Next.js 15, FastAPI, Prisma>`
* **Stockage / Base de données :** `<ex. PostgreSQL 16, Redis>`
* **Gestionnaire de paquets & Build :** `<ex. pnpm, uv, cargo>`
* **Tests & Qualité :** `<ex. Vitest + Playwright, Ruff + Mypy>`

### Skills & Références associés à la stack
Lister ici les skills (`.agents/skills/` ou globaux) et documents (`.agents/docs/`) que l'agent doit activer ou consulter selon le type de tâche sur cette stack :
| Périmètre / Type de tâche | Skill ou Référence à activer | Notes |
| :--- | :--- | :--- |
| `<ex. Frontend / UI>` | `<ex. skill-react-patterns ou .agents/docs/ui.md>` | `<optionnel>` |
| `<ex. Base de données / Migrations>` | `<ex. skill-prisma-migrations>` | `<optionnel>` |

---

## 2. Principes d'Architecture
* **Paradigme prédominant :** (ex. Clean Architecture, Modular Monolith, Functional Core / Imperative Shell)
* **Organisation des couches :**
  * `Domain` : Logique métier pure, sans dépendances externes.
  * `Application` : Cas d'usage, orchestration et interfaces d'adaptateurs.
  * `Infrastructure` : Implémentations concrètes (BDD, API tierces, drivers).

## 3. Standards de Code & Bonnes Pratiques
* **Nommage :** (ex. camelCase pour les fonctions, PascalCase pour les types/classes, SCREAMING_SNAKE pour les constantes).
* **Gestion des erreurs :** Toujours lever des exceptions personnalisées typées. Interdiction des captures silencieuses.
* **Fonctions :** Fonctions pures privilégiées, courtes, respectant le principe de responsabilité unique (SRP).

## 4. Stratégie de Test
* **Granularité :** Tests unitaires pour toute la logique métier (`Domain`), tests d'intégration pour les adaptateurs d'infrastructure.
* **Pratiques :** Mocks limités aux frontières strictes du système (I/O, réseau).
