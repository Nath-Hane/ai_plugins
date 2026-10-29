# Commandes Canoniques du Projet

Ce document est la vérité terrain des commandes exécutables pour l'agent IA. Il élimine toute incertitude quant aux scripts à lancer pour tester, vérifier les types, formater et construire le projet.

---

## 1. Tests Automatisés
* **Exécuter tous les tests :** `<COMMANDE_COMPLETE_TESTS>` (ex: `npm test`, `pytest`, `cargo test`)
* **Exécuter un fichier de test ciblé :** `<COMMANDE_TEST_CIBLE>` (ex: `npm test -- path/to/file.test.ts`, `pytest tests/test_file.py`)
* **Exécuter avec couverture :** `<COMMANDE_TEST_COVERAGE>`

## 2. Vérification des Types & Lint
* **Vérification statique des types :** `<COMMANDE_TYPECHECK>` (ex: `npx tsc --noEmit`, `mypy .`)
* **Linter :** `<COMMANDE_LINT>` (ex: `npm run lint`, `flake8`)
* **Formatage :** `<COMMANDE_FORMAT>` (ex: `npx prettier --write .`, `black .`)

## 3. Build & Développement
* **Build de production :** `<COMMANDE_BUILD>`
* **Lancement local / Dev server :** `<COMMANDE_DEV>`

## 4. Base de Données & Migrations (Si applicable)
* **Appliquer les migrations :** `<COMMANDE_MIGRATE>`
* **Générer une migration :** `<COMMANDE_MAKE_MIGRATION>`
