# Conventions d'Ingénierie & Patterns Autorisés

Ce document définit les règles architecturales, les choix de conception invariables et les standards de code spécifiques à ce projet.

---

## 1. Principes d'Architecture
* **Paradigme prédominant :** (ex. Clean Architecture, Modular Monolith, Functional Core / Imperative Shell)
* **Organisation des couches :**
  * `Domain` : Logique métier pure, sans dépendances externes.
  * `Application` : Cas d'usage, orchestration et interfaces d'adaptateurs.
  * `Infrastructure` : Implémentations concrètes (BDD, API tierces, drivers).

## 2. Standards de Code & Bonnes Pratiques
* **Nommage :** (ex. camelCase pour les fonctions, PascalCase pour les types/classes, SCREAMING_SNAKE pour les constantes).
* **Gestion des erreurs :** Toujours lever des exceptions personnalisées typées. Interdiction des captures silencieuses.
* **Fonctions :** Fonctions pures privilégiées, courtes, respectant le principe de responsabilité unique (SRP).

## 3. Stratégie de Test
* **Granularité :** Tests unitaires pour toute la logique métier (`Domain`), tests d'intégration pour les adaptateurs d'infrastructure.
* **Pratiques :** Mocks limités aux frontières strictes du système (I/O, réseau).
