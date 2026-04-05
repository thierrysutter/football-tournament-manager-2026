# Spécification Fonctionnelle — Gestionnaire de Tournois de Football

> **Version** : 0.5  
> **Date** : 05 avril 2026  
> **Auteur** : Utilisateur (usage personnel)  
> **Statut** : Prêt pour maquettage et développement  
> **Changelog v0.5** : Précisions Phase 2 — gestion des tirs au but sur score nul, ajout du workflow de validation de la Phase 2, génération du classement final, clôture du tournoi (consultation seule).

---

## 1. Contexte & Objectifs

### 1.1 Présentation

L'application est un **outil de gestion de tournois de football** à usage personnel. Elle permet de créer et gérer **plusieurs tournois indépendants**, d'enregistrer les équipes participantes, de générer automatiquement les rencontres, de saisir les résultats et de suivre l'avancement jusqu'au classement final.

### 1.2 Utilisateurs cibles

- **Unique utilisateur** : l'organisateur du tournoi (pas de gestion multi-utilisateurs, pas d'authentification dans un premier temps)

### 1.3 Orientation UI

- Interface **sobre et professionnelle**
- Priorité à la **clarté de l'information** et à la lisibilité
- Hiérarchie visuelle simple et maîtrisée
- Densité d'information adaptée à un usage desktop
- Aucune dépendance à des effets visuels pour comprendre l'information

### 1.4 Périmètre v1

| Inclus | Exclu (hors périmètre v1) |
|--------|--------------------------|
| Gestion multi-tournois | Gestion des joueurs individuels |
| Gestion des équipes | Gestion des arbitres |
| Génération automatique des matchs | Notifications / partage public |
| Classements de poules | Authentification |
| Brackets phase finale complets | Application mobile native |
| Classement final places 1 à 16 | Planning horaire des matchs |
| Export JSON des données | |
| Validation de phase avec verrouillage | |

---

## 2. Structure du Tournoi

### 2.1 Vue d'ensemble

Le tournoi se déroule en **2 phases distinctes** pour **N équipes** (16 par défaut) :

```
N équipes (16 par défaut)
    │
    ▼
┌─────────────────────────────────┐
│  PHASE 1 — Phase de poules      │
│  4 poules de 4 équipes          │
│  24 matchs au total             │
└────────────────┬────────────────┘
                 │  ← Validation obligatoire avant Phase 2
        ┌────────┴────────┐
        ▼                 ▼
┌──────────────────┐  ┌──────────────────┐
│  TABLEAU         │  │  TABLEAU         │
│  PRINCIPAL       │  │  CONSOLANT       │
│  (8 équipes)     │  │  (8 équipes)     │
│  1ers + 2es      │  │  3es + 4es       │
│  → Places 1-8    │  │  → Places 9-16   │
│  12 matchs       │  │  12 matchs       │
└──────────────────┘  └──────────────────┘

         TOTAL : 48 matchs
```

---

### 2.2 Phase 1 — Phase de Poules

| Paramètre | Valeur |
|-----------|--------|
| Nombre d'équipes | 16 (paramétrable à la création) |
| Nombre de poules | 4 (Poule A, B, C, D) |
| Équipes par poule | 4 |
| Format | Tous contre tous (round-robin) |
| Matchs aller-retour | Non — une seule confrontation |
| Matchs par équipe | 3 |
| Total matchs Phase 1 | 24 (6 matchs × 4 poules) |

#### 2.2.1 Barème des points

| Résultat | Points |
|----------|--------|
| Victoire | 3 pts |
| Match nul | 1 pt |
| Défaite | 0 pt |

#### 2.2.2 Critères de classement dans une poule

Le classement est établi selon les critères suivants, **dans l'ordre de priorité strict** :

| Priorité | Critère | Détail |
|----------|---------|--------|
| 1 | **Points** | Total cumulé sur les 3 matchs |
| 2 | **Différence de buts générale** | Buts marqués − buts encaissés sur tous les matchs de poule |
| 3 | **Confrontation directe** | Résultat du match entre les deux équipes à égalité |
| 4 | **Buts marqués** | Total de buts inscrits sur tous les matchs de poule |
| 5 | **Buts encaissés** | Total de buts encaissés (le moins est le mieux) |
| 6 | **Tirs au but** | Saisie manuelle par l'organisateur du résultat de la séance |

> **Règle — Tirs au but (critère 6)** : lorsque deux équipes sont à parfaite égalité sur les critères 1 à 5, l'application affiche un champ de saisie dédié pour le résultat de la séance de tirs au but (ex. : 4 − 3). Ce champ n'apparaît que lorsque nécessaire. Le vainqueur est classé devant, sans modification des statistiques de buts.

> **Règle — Égalité à 3 équipes ou plus** : si trois équipes ou plus sont à égalité de points, les critères 2 à 6 s'appliquent d'abord en considérant **uniquement les matchs joués entre ces équipes** (sous-classement), avant de revenir aux statistiques générales sur l'ensemble de la poule.

#### 2.2.3 Qualification pour la Phase 2

| Classement en poule | Destination |
|--------------------|-------------|
| 1er | Tableau Principal (places 1-8) |
| 2e | Tableau Principal (places 1-8) |
| 3e | Tableau Consolant (places 9-16) |
| 4e | Tableau Consolant (places 9-16) |

---

### 2.3 Phase 2 — Brackets de Classement Complets

#### 2.3.1 Principe général

Chaque bracket classe **toutes ses équipes** du 1er au 8e rang local. Les perdants de chaque tour continuent à jouer pour déterminer leur classement exact — aucune équipe n'est éliminée sans être classée.

#### 2.3.2 Structure d'un bracket (8 équipes → 12 matchs)

```
QUARTS DE FINALE      DEMI-FINALES          FINALES DE CLASSEMENT

Eq1 ─┐               
     ├─ V12 ──┐      
Eq2 ─┘        ├──────────────────────► Finale 1re/2e place
Eq3 ─┐        │      
     ├─ V34 ──┘      
Eq4 ─┘               

Eq5 ─┐               
     ├─ V56 ──┐      
Eq6 ─┘        ├──────────────────────► Finale 3e/4e place
Eq7 ─┐        │      
     ├─ V78 ──┘      
Eq8 ─┘               

Perdants QF1 vs QF2 ──────────────────► Finale 5e/6e place
Perdants QF3 vs QF4 ──────────────────► Finale 7e/8e place
```

**Détail des 12 matchs par bracket :**

| Tour | Nb matchs | Places déterminées |
|------|-----------|-------------------|
| Quarts de finale | 4 | Qualifiés top 4 / bas de tableau |
| Demi-finales vainqueurs | 2 | Finalistes 1re/2e et 3e/4e |
| Demi-finales perdants | 2 | Finalistes 5e/6e et 7e/8e |
| Finale 1re/2e place | 1 | 🥇 1er et 🥈 2e |
| Finale 3e/4e place | 1 | 🥉 3e et 4e |
| Finale 5e/6e place | 1 | 5e et 6e |
| Finale 7e/8e place | 1 | 7e et 8e |

#### 2.3.3 Constitution des brackets — Croisement des poules

**Tableau Principal — Quarts de finale :**

| Match | Équipe 1 | Équipe 2 |
|-------|----------|----------|
| QF1 | 1er Poule A | 2e Poule B |
| QF2 | 1er Poule C | 2e Poule D |
| QF3 | 1er Poule B | 2e Poule A |
| QF4 | 1er Poule D | 2e Poule C |

**Tableau Consolant — Quarts de finale :**

| Match | Équipe 1 | Équipe 2 |
|-------|----------|----------|
| QC1 | 3e Poule A | 4e Poule B |
| QC2 | 3e Poule C | 4e Poule D |
| QC3 | 3e Poule B | 4e Poule A |
| QC4 | 3e Poule D | 4e Poule C |

> **Principe** : les poules sont croisées pour éviter que deux équipes de la même poule se retrouvent en quart de finale.

#### 2.3.4 Gestion des matchs nuls en Phase 2

En Phase 2, chaque match doit obligatoirement désigner un vainqueur. Si le score à la fin du temps réglementaire est nul, l'application propose la saisie du résultat de la séance de tirs au but :

| Situation | Comportement |
|-----------|-------------|
| Score différent (ex. : 2-1) | Match validé directement — vainqueur désigné |
| Score nul (ex. : 1-1 ou 0-0) | Champ tirs au but affiché — obligatoire avant validation |
| Tirs au but | L'utilisateur sélectionne ou saisit l'équipe gagnante (ex. : « Équipe A remporte les TAB ») |

> **Règle** : le score au tableau affiche le résultat du temps réglementaire (ex. : 1-1 TAB). Le vainqueur des tirs au but est enregistré séparément et utilisé pour la progression dans le bracket. Les statistiques de buts ne sont pas affectées par les tirs au but.
>
> **Contrainte de validation** : un match ne peut pas être validé si le score est nul et qu'aucun vainqueur des tirs au but n'a été désigné.

---

## 3. Récapitulatif des Matchs

| Phase | Matchs | Détail |
|-------|--------|--------|
| Phase 1 — Poules | 24 | 6 matchs × 4 poules |
| Phase 2 — Tableau Principal | 12 | 4 QF + 2 DF vainqueurs + 2 DF perdants + 4 finales classement |
| Phase 2 — Tableau Consolant | 12 | 4 QF + 2 DF vainqueurs + 2 DF perdants + 4 finales classement |
| **TOTAL** | **48** | |

---

## 4. Fonctionnalités de l'Application

### 4.1 Gestion Multi-Tournois

L'application permet de gérer **plusieurs tournois indépendants**. L'écran d'accueil présente la liste de tous les tournois créés avec leur statut et les principales informations.

L'utilisateur peut :
- **Créer** un nouveau tournoi
- **Ouvrir** un tournoi existant pour le consulter ou le modifier
- **Supprimer** un tournoi (avec confirmation)
- Voir d'un coup d'œil l'état d'avancement de chaque tournoi

#### Règles de gestion — Multi-tournois

| Règle | Détail |
|-------|--------|
| Indépendance | Chaque tournoi possède ses propres équipes, poules, matchs et résultats |
| Nombre | Aucune limite sur le nombre de tournois créés |
| Suppression | Une confirmation est demandée avant suppression — opération irréversible |

---

### 4.2 Création d'un Tournoi

À la création d'un tournoi, l'utilisateur doit renseigner les informations suivantes :

| Champ | Obligatoire | Valeur par défaut | Description |
|-------|-------------|-------------------|-------------|
| Nom du tournoi | ✅ Oui | — | Nom libre, doit être unique |
| Date du tournoi | ✅ Oui | — | Date au format JJ/MM/AAAA |
| Lieu du tournoi | ✅ Oui | — | Nom du lieu ou ville |
| Nombre d'équipes | ✅ Oui | **16** | Nombre entier pair ≥ 4 |

> 💡 Le nombre d'équipes est fixé à la création et ne peut plus être modifié une fois le tournoi créé. La valeur par défaut est **16 équipes**.

#### Règles de validation à la création

| Règle | Détail |
|-------|--------|
| Nom unique | Deux tournois ne peuvent pas avoir le même nom |
| Tous champs requis | La création est impossible tant qu'un champ obligatoire est vide |
| Format date | La date doit être valide (format JJ/MM/AAAA) |

---

### 4.3 Gestion des Équipes

Après la création du tournoi, l'utilisateur procède à l'inscription des équipes participantes. Cette étape est préalable à toute génération des poules.

L'utilisateur peut :
- **Ajouter** une équipe (nom obligatoire, unique au sein du tournoi)
- **Modifier** le nom d'une équipe
- **Supprimer** une équipe (selon les règles ci-dessous)

#### Règles de gestion — Équipes (tableau complet)

| Situation | Ajout | Suppression | Règle |
|-----------|-------|-------------|-------|
| Nombre d'équipes non atteint | ✅ Autorisé | ✅ Autorisée | Saisie en cours |
| Nombre d'équipes atteint, poules non générées | ❌ Bloqué | ✅ Autorisée | Liste complète mais non verrouillée |
| Poules générées, Phase 1 non démarrée | ❌ Bloqué | ⚠️ Avec réinitialisation (voir ci-dessous) | Suppression entraîne reset total des poules |
| Phase 1 en cours ou validée | ❌ Bloqué | ❌ Interdite | Tournoi en cours |

> **Règle — Suppression après génération des poules** : si l'utilisateur tente de supprimer une équipe après que les poules ont été générées, l'application affiche un message d'avertissement clair :
>
> *« La suppression de cette équipe entraînera la réinitialisation complète des poules et la suppression de tous les matchs déjà enregistrés. Cette action est irréversible. Confirmer ? »*
>
> L'utilisateur doit **confirmer explicitement** avant que l'opération soit exécutée. En cas de confirmation, toutes les poules et tous les résultats de Phase 1 sont supprimés et le tournoi repasse à l'état `ÉQUIPES_EN_COURS`.

#### Pré-requis pour passer à la génération des poules

- Le nombre d'équipes inscrites doit être **exactement égal** au nombre d'équipes défini à la création du tournoi.
- Tant que ce nombre n'est pas atteint, le bouton « Générer les poules » reste **désactivé** avec un indicateur du nombre d'équipes manquantes (ex. : *« 3 équipes manquantes »*).

---

### 4.4 Tirage au Sort / Affectation des Poules

L'utilisateur peut :
- Lancer un **tirage au sort automatique** (répartition aléatoire en 4 poules de 4)
- Ou affecter **manuellement** chaque équipe à une poule
- **Relancer** le tirage tant que la Phase 1 n'a pas démarré

#### Règles de gestion — Tirage

| Règle | Détail |
|-------|--------|
| Équilibre | Chaque poule doit contenir exactement 4 équipes |
| Unicité | Une équipe ne peut appartenir qu'à une seule poule |
| Verrouillage | Le tirage est figé dès la validation de la Phase 1 |

---

### 4.5 Génération des Rencontres

- Les **24 matchs de Phase 1** sont générés automatiquement à la validation du tirage.
- Les **24 matchs de Phase 2** sont générés automatiquement après la **validation de la Phase 1** par l'utilisateur.

---

### 4.6 Saisie des Résultats

L'utilisateur peut :
- Saisir le **score** d'un match (buts équipe 1 / buts équipe 2)
- **Modifier** un résultat déjà saisi (recalcul automatique des classements)
- **Effacer** un résultat (le match repasse à « à jouer »)

> ⚠️ La modification et l'effacement d'un résultat de Phase 1 sont **interdits après la validation de la Phase 1**.

#### Règles de gestion — Résultats

| Règle | Détail |
|-------|--------|
| Scores négatifs | Interdits |
| Score nul en Phase 2 | Champ tirs au but obligatoire — un vainqueur doit être désigné avant de valider le match |
| Tirs au but en poule | Champ supplémentaire si critères 1-5 à parfaite égalité |
| Tirs au but en Phase 2 | Champ affiché automatiquement si score nul — l'équipe gagnante est sélectionnée par l'utilisateur |
| Recalcul Phase 1 | Toute saisie ou modification recalcule immédiatement classements et qualifications |
| Accès Phase 2 | Matchs accessibles uniquement après validation de la Phase 1 |
| Verrouillage Phase 1 | Après validation, aucun score de Phase 1 ne peut être modifié ou effacé |
| Verrouillage Phase 2 | Après validation de la Phase 2, aucun score ne peut être modifié ou effacé |

---

### 4.7 Classements et Suivi

**Phase 1 :**
- Classements de poule mis à jour **en temps réel** à chaque saisie de résultat
- 4 tableaux de classement : Points, J / V / N / D, BP / BC / Diff
- Statut de chaque match (à jouer / joué)
- Qualifiés mis en évidence dès que la poule est complète

**Phase 2 :**
- Bracket visuel du Tableau Principal avec progression en temps réel
- Bracket visuel du Tableau Consolant avec progression en temps réel

**Classement final** *(généré à la clôture du tournoi)* :
- Classement général de la 1re à la 16e place, accessible dans un écran dédié après validation de la Phase 2
- Affiché sous forme de tableau ordonné avec le nom de l'équipe et sa place finale
- Disponible en consultation permanente une fois le tournoi clôturé

---

### 4.8 Validation de la Phase 1

Lorsque **tous les résultats de la Phase 1 sont saisis** (24/24 matchs), un bouton « **Valider la Phase 1** » devient disponible.

#### Comportement de la validation

| Étape | Description |
|-------|-------------|
| Déclencheur | L'utilisateur clique sur « Valider la Phase 1 » |
| Confirmation | Une modale de confirmation est affichée : *« Tous les résultats de la Phase 1 vont être verrouillés. Cette action est irréversible. Confirmer ? »* |
| Après confirmation | Le statut du tournoi passe à `PHASE1_VALIDÉE` |
| Verrouillage | Tous les scores de Phase 1 sont figés — aucune modification ni effacement possibles |
| Déblocage Phase 2 | Les matchs de Phase 2 sont générés automatiquement et deviennent accessibles |

#### Pré-requis pour la validation

- Les **24 matchs de Phase 1** doivent tous avoir un résultat saisi.
- Aucun match ne doit être en attente de départage par tirs au but (critère 6) sans résultat renseigné.

> ⚠️ La validation est **irréversible**. Il n'existe pas de mécanisme pour « dévalider » la Phase 1 sans supprimer l'intégralité du tournoi.

---

### 4.9 Validation de la Phase 2 et Clôture du Tournoi

Lorsque **tous les résultats de la Phase 2 sont saisis** (24/24 matchs, tirs au but inclus si applicable), un bouton « **Valider la Phase 2 et clôturer le tournoi** » devient disponible.

#### Comportement de la validation

| Étape | Description |
|-------|-------------|
| Déclencheur | L'utilisateur clique sur « Valider la Phase 2 et clôturer le tournoi » |
| Confirmation | Une modale de confirmation est affichée : *« Tous les résultats de la Phase 2 vont être verrouillés. Le classement final sera généré. Cette action est irréversible. Confirmer ? »* |
| Après confirmation | Le statut du tournoi passe à `TERMINÉ` |
| Verrouillage | Tous les scores de Phase 2 sont figés — aucune modification ni effacement possibles |
| Classement final | Le classement général des places 1 à 16 est généré et affiché dans un écran dédié |
| Clôture | Le tournoi passe en mode **consultation seule** — aucune saisie ni modification n'est possible |

#### Pré-requis pour la validation

- Les **24 matchs de Phase 2** doivent tous avoir un résultat saisi.
- Aucun match ne doit afficher un score nul sans vainqueur des tirs au but désigné.

#### Mode consultation (tournoi clôturé)

Un tournoi à l'état `TERMINÉ` reste **accessible et consultable** depuis la liste des tournois. L'utilisateur peut :
- Consulter tous les résultats de Phase 1 et Phase 2
- Consulter les classements de poules
- Consulter les brackets Phase 2
- Consulter le classement final (places 1 à 16)
- Exporter les données au format JSON

Toute action de saisie ou de modification est désactivée et les champs de saisie sont masqués ou en lecture seule. Un bandeau ou badge « Tournoi clôturé » est affiché de manière visible sur toutes les vues du tournoi.

---

### 4.10 Export des Données (v1)

- Export de l'ensemble des données du tournoi au format **JSON**
- Import d'un fichier JSON pour recharger un tournoi précédemment exporté
- Le fichier exporté contient : configuration, équipes, poules, matchs, résultats, classements

---

## 5. Navigation & États du Tournoi

### 5.1 Structure de l'Application

```
┌─────────────────────────────────┐
│  Liste des Tournois (accueil)   │  ← Tous les tournois, création, suppression
├─────────────────────────────────┤
│  [Tournoi sélectionné]          │
│  ┌───────────────────────────┐  │
│  │  Tableau de bord          │  │  ← Vue d'ensemble, statut, progression
│  ├───────────────────────────┤  │
│  │  Configuration            │  │  ← Nom, date, lieu, nb équipes (lecture seule après création)
│  ├───────────────────────────┤  │
│  │  Équipes                  │  │  ← Liste, ajout, modification, suppression
│  ├───────────────────────────┤  │
│  │  Poules & Tirage          │  │  ← Affectation des équipes aux poules
│  ├───────────────────────────┤  │
│  │  Phase 1 — Matchs         │  │  ← Matchs par poule, saisie, tableaux, validation
│  ├───────────────────────────┤  │
│  │  Phase 2 — Brackets       │  │  ← Tableau principal + consolant (après validation P1)
│  ├───────────────────────────┤  │
│  │  Classement Final         │  │  ← Places 1 à 16 (généré après clôture, consultation seule)
│  └───────────────────────────┘  │
└─────────────────────────────────┘
```

### 5.2 Cycle de Vie d'un Tournoi

```
NOUVEAU
  └─► CONFIGURÉ (nom + date + lieu + nb équipes saisis)
        └─► ÉQUIPES_EN_COURS (au moins 1 équipe, nombre cible non atteint)
              └─► ÉQUIPES_COMPLÈTES (nombre d'équipes cible atteint, poules non générées)
                    └─► POULES_DÉFINIES (tirage validé, matchs P1 générés)
                          └─► PHASE1_EN_COURS (≥ 1 résultat saisi)
                                └─► PHASE1_COMPLÈTE (24/24 matchs saisis, en attente validation)
                                      └─► PHASE1_VALIDÉE (validation confirmée, scores verrouillés)
                                            └─► PHASE2_EN_COURS (bracket généré, ≥ 1 résultat)
                                                  └─► PHASE2_COMPLÈTE (24/24 matchs saisis, en attente validation)
                                                        └─► TERMINÉ (validation confirmée, tournoi clôturé — consultation seule)
```

Chaque état détermine les actions disponibles et verrouille les sections précédentes.

---

## 6. Données Gérées

### 6.1 Entités principales

**Liste des tournois** : collection de tournois indépendants, identifiés chacun par un identifiant unique

**Tournoi** : nom, date, lieu, nombre d'équipes cible, statut, horodatage création/modification

**Équipe** : identifiant unique, nom, poule affectée

**Poule** : identifiant (A/B/C/D), liste des équipes

**Match** : phase (1 ou 2), poule/tour, équipe 1, équipe 2, score 1, score 2, statut (à jouer / joué), vainqueur TAB (identifiant équipe, Phase 2 uniquement si score nul)

**Classement de poule** *(calculé)* : points, matchs joués, victoires, nuls, défaites, buts pour, buts contre, différence de buts

### 6.2 Format d'export JSON

```json
{
  "version": "1.0",
  "exportedAt": "2026-04-05T18:00:00Z",
  "tournament": {
    "name": "",
    "date": "",
    "location": "",
    "teamCount": 16,
    "status": "TERMINÉ"
  },
  "teams": [],
  "groups": [],
  "matches": [
    {
      "phase": 2,
      "round": "QF1",
      "team1Id": "",
      "team2Id": "",
      "score1": 1,
      "score2": 1,
      "penaltyWinnerId": "team2Id",
      "status": "joué"
    }
  ],
  "finalRanking": []
}
```

---

## 7. Contraintes Techniques

| Contrainte | Détail |
|------------|--------|
| Type | Site web statique — mono-fichier HTML / CSS / JS |
| Stockage | localStorage (persistance entre sessions) + export/import JSON |
| Persistance | localStorage pour la session courante — export JSON pour sauvegarde externe |
| Compatibilité | Navigateur desktop moderne (Chrome, Firefox, Edge) |
| Responsive | Desktop prioritaire — adaptation mobile en meilleur effort |
| Dépendances | Aucun serveur — 100% client-side |

> **Note v0.4** : le stockage passe de « en mémoire (session) » à **localStorage** pour permettre la persistance des données multi-tournois entre les rechargements de page.

---

## 8. Exigences d'Accessibilité et de Conformité (RGAA)

L'application est conçue selon une approche **«accessibility by design»** et vise un haut niveau de conformité au **RGAA 4.x** (Référentiel Général d'Amélioration de l'Accessibilité), référentiel officiel français organisé en 13 thématiques et 106 critères.

### 8.1 Principes généraux

- Accessibilité prise en compte **dès la conception** des écrans, composants et interactions
- Interface sobre, professionnelle et lisible, sans effet visuel porteur d'information
- Information jamais transmise **uniquement par la couleur**

### 8.2 Exigences structurelles

| Exigence | Détail |
|----------|--------|
| HTML sémantique | Balises `<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, `<footer>` utilisées correctement |
| Hiérarchie des titres | Un seul `<h1>` par page, niveaux `<h2>`→`<h3>` sans saut |
| Validité HTML | Code valide, identifiants `id` uniques, balises correctement fermées |
| Langue | Attribut `lang="fr"` sur la balise `<html>` |
| Titre de page | Balise `<title>` descriptive et unique par vue |

### 8.3 Exigences de navigation

| Exigence | Détail |
|----------|--------|
| Navigation clavier | Tous les éléments interactifs accessibles via Tab / Entrée / Espace / Échap |
| Focus visible | Indicateur de focus visible sur tous les éléments interactifs |
| Lien d'évitement | Lien «Aller au contenu principal» comme premier élément focusable |
| Ordre de tabulation | Ordre logique et cohérent avec la disposition visuelle |

### 8.4 Exigences visuelles et de contraste

| Exigence | Ratio minimum |
|----------|--------------|
| Texte courant (< 18px normal ou < 14px gras) | 4,5 : 1 |
| Grand texte (≥ 18px normal ou ≥ 14px gras) | 3 : 1 |
| Composants UI et éléments graphiques informatifs | 3 : 1 |
| Texte sur fond coloré (boutons, badges) | 4,5 : 1 |

### 8.5 Exigences formulaires et interactions

| Exigence | Détail |
|----------|--------|
| Labels | Chaque `<input>` associé à un `<label>` explicite |
| Messages d'erreur | Textuels, spécifiques, proches du champ concerné |
| État des composants | États actif, désactivé, invalide, requis annoncés programmatiquement |
| Annonces dynamiques | Changements importants annoncés via `aria-live` (ex : «Résultat enregistré», «Phase 1 validée», «Phase 2 générée») |
| Groupes de champs | `<fieldset>` / `<legend>` pour les groupes de contrôles liés |
| Modales de confirmation | Focus piégé dans la modale, fermeture via Échap, annonce du message via `role="alertdialog"` |

### 8.6 Exigences médias et composants riches

| Exigence | Détail |
|----------|--------|
| Images décoratives | `alt=""` |
| Images informatives | `alt` descriptif |
| Icônes seules | `aria-label` ou texte caché `.sr-only` |
| Tableaux de données | En-têtes `<th>` avec attribut `scope`, légende `<caption>` |
| Brackets (composants custom) | Navigation clavier, rôles ARIA appropriés |

### 8.7 Recette accessibilité minimale avant livraison

- [ ] Navigation clavier complète sur tous les écrans
- [ ] Vérification des ratios de contraste (outil : Colour Contrast Analyser ou browser DevTools)
- [ ] Vérification de la hiérarchie des titres
- [ ] Vérification des labels de formulaires et messages d'erreur
- [ ] Vérification des annonces `aria-live`
- [ ] Vérification des modales de confirmation (focus trap, `role="alertdialog"`)
- [ ] Vérification responsive (zoom 200%, 400%)
- [ ] Validation HTML (Nu Html Checker)

---

## 9. Points Ouverts Résiduels

| # | Question | Impact | Priorité |
|---|----------|--------|----------|
| 1 | Design visuel exact : palette de couleurs, typographie — à valider sur maquette | UI | Avant développement |
| 2 | Comportement à la perte de données (effacement du localStorage) : avertissement à l'export ? | UX | À définir en dev |
| 3 | Nombre d'équipes paramétrable : adapter la génération des poules pour un nombre différent de 16 (ex. : 8 équipes → 2 poules) | Fonctionnel | v1 si besoin, sinon v2 |

---

**Note** : Tous les points fonctionnels (gestion multi-tournois, règles de gestion des équipes, workflow de validation Phase 1 et Phase 2, tirs au but Phase 2, clôture et consultation du tournoi, règles de départage, matchs de classement, grilles d'appariement, export JSON v1) sont validés et intégrés dans la spécification. Les points résiduels ci-dessus concernent uniquement l'implémentation technique et l'UX.

*Version 0.5 — Spécification mise à jour le 05 avril 2026. Prêt pour le développement écran par écran.*

---

## 10. Roadmap des Fonctionnalités Futures

Pour une vision détaillée des fonctionnalités planifiées pour les versions futures (v2.0, v3.0, etc.), consulter le fichier **[roadmap.md](./roadmap.md)** dans le répertoire `docs/`.

Ce document de roadmap inclut :
- Les fonctionnalités non implémentées dans v1.0
- Les priorités et critères de priorisation
- Le processus de développement et versioning
- Les notes de conception technique
