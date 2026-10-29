# Règles d'Ingénierie & Gouvernance du Contexte (Dev Standard)

Ces règles s'appliquent de manière impérative à toute session de développement. Elles unifient l'Interpretable Context Methodology (ICM) et la discipline logicielle des skills d'ingénierie.

---

## 1. Matrice de Consultation Obligatoire (Context Routing)

Pour préserver la fenêtre de contexte et éviter toute régression ou décision redondante, l'agent doit impérativement charger le contexte ciblé selon la phase active avant d'agir :

1. **Phase de Cadrage, Architecture ou Réfutation :**
   * Consulter l'index des décisions d'architecture : `docs/adr/INDEX.md` (ou lister `docs/adr/`).
   * Si une décision passée concerne le périmètre de la tâche, lire l'ADR correspondant avant de proposer une modification structurelle.
   * Consulter `docs/agents/domain.md` (ou le fichier `CONTEXT.md` racine) pour s'aligner sur le langage omniprésent (*ubiquitous language*) et bannir tout synonyme imprécis.

2. **Phase d'Implémentation de Tâche / Ticket :**
   * Identifier et charger le ticket actif (situé dans `.scratch/**/issues/` ou le gestionnaire configuré dans `docs/agents/issue-tracker.md`).
   * **Contract Reader Protocol :** Charger obligatoirement en contexte **tous** les fichiers explicitement déclarés dans la section `## Inputs` du ticket.
   * Consulter `.agents/COMMANDS.md` pour connaître les commandes exactes de test, de lint et de build du projet.
   * Vérifier s'il existe un fichier `CONTEXT.md` local dans le répertoire du code concerné (ex. `src/<module>/CONTEXT.md`) et internaliser ses contraintes.

3. **Phase de Diagnostic / Erreur Inattendue :**
   * Consulter `.agents/LEARNINGS.md` pour vérifier si le piège, le bug d'environnement ou le comportement contre-intuitif a déjà été documenté et résolu par le passé.

---

## 2. Protocole "Contract-in-Ticket"

Tout ticket de travail doit adopter une structure contractuelle inspirée d'ICM :
* `## Inputs` : Chemins précis de tous les fichiers à lire (spécifications, interfaces, ADRs pertinentes, tests existants).
* `## Expected Outputs` : Fichiers précis à créer, modifier ou supprimer.
* `## Verification Criteria` : Commandes concrètes et automatisées de validation (tests ciblés, vérification de types, lint).

**Règle d'or :** L'agent a l'interdiction formelle de modifier la moindre ligne de code source tant qu'il n'a pas lu l'intégralité des fichiers mentionnés dans `## Inputs`.

---

## 3. Invariants de Développement & TDD (Philosophie Pocock)

* **Développement Piloté par les Tests (TDD) :**
  1. Identifier ou écrire le test unitaire automatisé qui échoue (phase *Red*).
  2. Écrire l'implémentation minimale nécessaire pour faire passer le test (phase *Green*).
  3. Nettoyer et refactoriser sans altérer le comportement vérifié (phase *Refactor*).
* **Modifications Chirurgicales (Diff Minimal) :**
  * Ne modifier que le code strictement nécessaire à la résolution du ticket actif.
  * Interdiction absolue de reformater, renommer ou refactoriser du code hors périmètre.
  * Préserver scrupuleusement les commentaires, docstrings et signatures non concernés.
* **Programmation Défensive :**
  * Interdiction formelle des blocs de capture silencieux (`catch {}`, `except: pass`).
  * Les erreurs doivent être typées, propagées ou journalisées avec contexte utile.
* **Zéro Code Mort :**
  * Aucun code commenté pour "archive" n'est toléré dans le code source.
  * Aucune fonction, variable ou importation inutilisée ne doit être introduite.
* **Preuve d'Exécution :**
  * Un ticket n'est considéré achevé qu'après exécution réelle et attestation du succès de la commande déclarée dans `## Verification Criteria`.

---

## 4. Protocole de Clôture, Revue Manuelle & Commit (Review Gate)

Dès que les tests automatisés d'un ticket ou d'une modification de code sont passants :
1. **Arrêt Obligatoire (*Review Gate*) :** Ne jamais déclarer la tâche terminée et **ne jamais exécuter de `git commit`** sans validation explicite de l'utilisateur.
2. **Restitution à l'Utilisateur :**
   * Présenter un diff synthétique (`git diff --stat`).
   * Fournir la fiche de test manuel pas-à-pas (pré-requis, actions précises, résultat attendu).
   * Proposer un message de commit formaté selon les guidelines strictes (anglais, mode impératif, test "If applied...", max 50 caractères, séparation par ligne vide, corps axé exclusivement sur WHAT & WHY, atomique).
   * Demander la validation humaine explicite.
3. **Exécution du Commit Post-Validation :**
   * Une fois la validation explicite reçue, appliquer un **staging chirurgical** (`git add <fichier1> <fichier2>` nommant uniquement les fichiers du ticket ; interdiction absolue de `git add .` ou `git add -A`).
   * Créer le commit et afficher son identifiant court.
   * Pour le déroulé exhaustif, activer le skill `human-review-and-commit`.

---

## 5. Gouvernance des Fichiers Créés par l'IA

1. **Fichiers Scratch / Temporaires :**
   * Tout script de reproduction, fichier de mock temporaire, trace ou dump intermédiaire doit être écrit dans `.agents/tmp/`.
   * Format de nommage obligatoire : `YYYYMMDD_HHMM_<nom_descriptif>.<ext>`.
   * Le dossier `.agents/tmp/` ne doit jamais être commité dans Git (vérifier sa présence dans `.gitignore`).
2. **Documents Libres & Recherches :**
   * Tout document de synthèse, audit ou compte-rendu de recherche sans dossier dédié doit être enregistré dans `.agents/docs/`.
   * **Règle d'or d'indexation :** Chaque création dans `.agents/docs/` exige la mise à jour immédiate de `.agents/docs/INDEX.md` (Tableau : Date | Fichier | Résumé en 1 ligne | Tags).
   * Avant toute recherche documentaire, l'agent doit consulter cet index pour charger uniquement le document pertinent.
3. **Purge en Fin de Tâche :**
   * À la conclusion d'un ticket, l'agent liste les fichiers temporaires résiduels dans `.agents/tmp/` et propose formellement leur suppression à l'utilisateur.

---

## 6. Boucle Rétrospective & Principe Edit-Source

1. **Apprentissage Continu (Post-Incident / Retro) :**
   * Lorsqu'une correction est apportée suite à un piège vicieux ou un bug de configuration, l'agent consigne immédiatement une entrée concise dans `.agents/LEARNINGS.md`.
2. **Amélioration des Règles Source :**
   * Si une erreur récurrente survient en raison d'une consigne sous-spécifiée, l'agent ne se contente pas de corriger le code : il propose d'amender `.agents/CONVENTIONS.md` ou d'initier un ADR dans `docs/adr/`.
