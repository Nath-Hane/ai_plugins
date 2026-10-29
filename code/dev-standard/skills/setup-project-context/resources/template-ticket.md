# Ticket : <ID> - <Titre Explicite de la Tâche>

* **Branche / Feature :** `<feature-slug>`
* **Date :** `YYYY-MM-DD`
* **Statut :** `TODO` *(TODO | IN_PROGRESS | DONE)*

---

## 1. Description & Objectif
<Explication concise de ce que la tâche doit accomplir et pourquoi.>

---

## 2. Contrat ICM : Inputs
*Fichiers obligatoires à lire par l'agent avant toute écriture de code :*
- [ ] **Spécification liée :** `.scratch/<feature-slug>/spec.md`
- [ ] **ADR associée :** `docs/adr/<NNNN>-<decision>.md` (si applicable)
- [ ] **Commandes du projet :** `.agents/COMMANDS.md`
- [ ] **Code / Interfaces existantes :** `src/<chemin>/<fichier>.<ext>`
- [ ] **Tests existants :** `tests/<chemin>/<test_fichier>.<ext>`

---

## 3. Contrat ICM : Expected Outputs
*Fichiers impactés par cette tâche :*
* `src/<chemin>/<fichier_modifie>.<ext>`
* `tests/<chemin>/<nouveau_test>.<ext>`

---

## 4. Contrat ICM : Verification Criteria
*Commandes concrètes dont l'exécution doit impérativement réussir :*
```bash
# Exemple : test unitaire ciblé
<COMMANDE_TEST_CIBLE>

# Exemple : contrôle statique de types
<COMMANDE_TYPECHECK>
```

---

## 5. Journal d'Exécution & Preuves
*(Rempli par l'agent lors de la réalisation du ticket)*
* **Test Red initial :** `<Trace ou confirmation de l'échec du test avant implémentation>`
* **Test Green final :** `<Trace de succès>`
* **Fichiers temporaires purgés :** `<Liste des fichiers temporaires supprimés dans .agents/tmp/>`
