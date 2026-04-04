# Spécification Fonctionnelle — Gestionnaire de Tournois de Football

> **Version** : 0.2  
> **Date** : 04 avril 2026  
> **Auteur** : Utilisateur (usage personnel)  
> **Statut** : Points ouverts résolus — Prêt pour développement

---

## 1. Contexte & Objectifs

### 1.1 Présentation

L'application est un **outil de gestion de tournois de football** à usage personnel. Elle permet de configurer un tournoi, d'enregistrer les équipes participantes, de générer automatiquement les rencontres, de saisir les résultats et de suivre l'avancement jusqu'au classement final.

### 1.2 Utilisateurs cibles

- **Unique utilisateur** : l'organisateur du tournoi (pas de gestion multi-utilisateurs, pas d'authentification dans un premier temps)

### 1.3 Périmètre v1

| Inclus | Exclu (hors périmètre v1) |
|--------|--------------------------|
| Gestion des équipes | Gestion des joueurs individuels |
| Génération automatique des matchs | Gestion des arbitres |
| Saisie des résultats | Notifications / partage public |
| Classements de poules | Multi-tournois simultanés |
| Brackets phase finale | Authentification |
| Classement final places 1 à 16 | Application mobile native |
| | Planning horaire des matchs |
| | Export / Import des données (v2) |

---

## 2. Structure du Tournoi

### 2.1 Vue d'ensemble

Le tournoi se déroule en **2 phases distinctes** pour **16 équipes** :

```
16 équipes
    │
    ▼
┌─────────────────────────────────┐
│  PHASE 1 — Phase de poules      │
│  4 poules de 4 équipes          │
│  24 matchs au total             │
└────────────────┬────────────────┘
                 │
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
| Nombre d'équipes | 16 |
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

> **Règle de gestion — Tirs au but (critère 6)** : lorsque deux équipes sont à parfaite égalité sur les critères 1 à 5, l'application affiche un champ de saisie dédié pour le résultat de la séance de tirs au but (ex. : 4 − 3). Ce champ n'apparaît que lorsque nécessaire. Le vainqueur est classé devant, sans modification des statistiques de buts.

> **Règle de gestion — Égalité à 3 équipes** : si trois équipes ou plus sont à égalité de points, les critères 2 à 6 s'appliquent en considérant uniquement les matchs entre ces équipes (sous-classement) avant de revenir aux statistiques générales.

#### 2.2.3 Qualification pour la Phase 2

| Classement en poule | Destination |
|--------------------|-------------|
| 1er | Tableau Principal (places 1-8) |
| 2e | Tableau Principal (places 1-8) |
| 3e | Tableau Consolant (places 9-16) |
| 4e | Tableau Consolant (places 9-16) |

---

### 2.3 Phase 2 — Brackets de Classement

#### 2.3.1 Principe général

Chaque bracket classe **toutes ses équipes de la 1re à la 8e place locale** (soit places 1-8 et 9-16 au global). Les perdants de chaque tour continuent à jouer entre eux pour déterminer leur classement exact — aucune équipe n'est éliminée sans être classée.

#### 2.3.2 Structure d'un bracket (8 équipes → 12 matchs)

```
QUARTS DE FINALE         DEMI-FINALES             FINALES DE CLASSEMENT
                                               
Eq1 ─┐                                         
     ├─ V12 ──┐                                
Eq2 ─┘        │                               
              ├── V12vsV34 ──► Match 1re/2e place
Eq3 ─┐        │                               
     ├─ V34 ──┘                               
Eq4 ─┘                                        
                                               
Eq5 ─┐                                        
     ├─ V56 ──┐                               
Eq6 ─┘        │                               
              ├── V56vsV78 ──► Match 3e/4e place
Eq7 ─┐        │                               
     ├─ V78 ──┘                               
Eq8 ─┘                                        
                                               
Perdants QF → 4 matchs → 4 demi-finales perdants → Match 5e/6e + Match 7e/8e
```

**Détail des 12 matchs par bracket :**

| Tour | Matchs | Détermine |
|------|--------|-----------|
| Quarts de finale | 4 matchs | Qualifiés pour le top 4 et le bas de tableau |
| Demi-finales vainqueurs | 2 matchs | Finalistes 1re/2e et 3e/4e |
| Demi-finales perdants QF | 2 matchs | Finalistes 5e/6e et 7e/8e |
| Finale 1re/2e place | 1 match | 🥇 1er et 🥈 2e |
| Finale 3e/4e place | 1 match | 🥉 3e et 4e |
| Finale 5e/6e place | 1 match | 5e et 6e |
| Finale 7e/8e place | 1 match | 7e et 8e |

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

- En phase d'élimination directe, un match nul à l'issue du temps réglementaire est possible.
- L'application **n'impose pas de prolongation** — l'organisateur saisit directement le score final incluant le cas échéant le résultat après tirs au but.
- **Contrainte de validation** : le score doit impérativement désigner un vainqueur (scores différents). Un score nul est bloqué à la validation.

---

## 3. Récapitulatif des Matchs

| Phase | Matchs | Détail |
|-------|--------|--------|
| Phase 1 — Poules | 24 | 6 matchs × 4 poules |
| Phase 2 — Tableau Principal | 12 | 4 QF + 2 DF vainqueurs + 2 DF perdants + 4 finales de classement |
| Phase 2 — Tableau Consolant | 12 | 4 QF + 2 DF vainqueurs + 2 DF perdants + 4 finales de classement |
| **TOTAL** | **48** | |

---

## 4. Fonctionnalités de l'Application

### 4.1 Configuration du Tournoi

L'utilisateur peut :
- Définir le **nom du tournoi**
- Définir la **date** du tournoi
- Consulter un récapitulatif de la configuration avant démarrage

> 💡 **Évolution future** : rendre le nombre d'équipes et de poules paramétrable.

### 4.2 Gestion des Équipes

L'utilisateur peut :
- **Ajouter** une équipe (nom obligatoire)
- **Modifier** le nom d'une équipe
- **Supprimer** une équipe (uniquement si le tournoi n'a pas encore démarré)
- Visualiser la liste des 16 équipes avec leur poule d'affectation

#### Règles de gestion — Équipes

| Règle | Détail |
|-------|--------|
| Nombre requis | Exactement 16 équipes pour démarrer |
| Unicité | Deux équipes ne peuvent pas avoir le même nom |
| Suppression | Interdite après le démarrage du tournoi |

### 4.3 Tirage au Sort / Affectation des Poules

L'utilisateur peut :
- Lancer un **tirage au sort automatique** (répartition aléatoire en 4 poules de 4)
- Ou affecter **manuellement** chaque équipe à une poule
- **Relancer** le tirage tant que le tournoi n'a pas démarré

#### Règles de gestion — Tirage

| Règle | Détail |
|-------|--------|
| Équilibre | Chaque poule doit contenir exactement 4 équipes |
| Unicité | Une équipe ne peut appartenir qu'à une seule poule |
| Verrouillage | Le tirage est figé dès la saisie du premier résultat de Phase 1 |

### 4.4 Génération des Rencontres

- Les **24 matchs de Phase 1** sont générés automatiquement à la validation du tirage
- Les **24 matchs de Phase 2** sont générés après clôture manuelle de la Phase 1 (tous les matchs saisis)

### 4.5 Saisie des Résultats

L'utilisateur peut :
- Saisir le **score** d'un match (buts équipe 1 / buts équipe 2)
- **Modifier** un résultat déjà saisi (recalcul automatique)
- **Effacer** un résultat (le match repasse à "à jouer")

#### Règles de gestion — Résultats

| Règle | Détail |
|-------|--------|
| Scores négatifs | Interdits |
| Score nul en Phase 2 | Bloqué — un vainqueur doit être désigné |
| Tirs au but en poule | Champ supplémentaire affiché si critères 1-5 à égalité |
| Recalcul | Toute modification recalcule classements et qualifications |
| Accès Phase 2 | Matchs de Phase 2 accessibles uniquement après génération du bracket |

### 4.6 Suivi et Classements

**Phase 1 :**
- 4 tableaux de classement de poules : Points, J / V / N / D, BP / BC / Diff
- Statut de chaque match (à jouer / joué)
- Qualifiés mis en évidence dès que la poule est complète

**Phase 2 :**
- Bracket visuel du Tableau Principal avec avancement en temps réel
- Bracket visuel du Tableau Consolant avec avancement en temps réel

**Classement final :**
- Classement général de la 1re à la 16e place à l'issue de tous les matchs

---

## 5. Navigation & États du Tournoi

### 5.1 Structure de l'Application

```
┌─────────────────────────────────┐
│  Dashboard (accueil)            │  ← Vue d'ensemble, statut, progression
├─────────────────────────────────┤
│  Configuration                  │  ← Nom, date
├─────────────────────────────────┤
│  Équipes                        │  ← Liste, ajout, modification
├─────────────────────────────────┤
│  Poules & Tirage                │  ← Affectation des équipes aux poules
├─────────────────────────────────┤
│  Phase 1 — Matchs & Classements │  ← Matchs par poule, saisie, tableaux
├─────────────────────────────────┤
│  Phase 2 — Brackets             │  ← Tableau principal + consolant
├─────────────────────────────────┤
│  Classement Final               │  ← Places 1 à 16
└─────────────────────────────────┘
```

### 5.2 Cycle de Vie du Tournoi

```
NOUVEAU
  └─► CONFIGURATION (nom + date saisis)
        └─► ÉQUIPES_SAISIES (16 équipes enregistrées)
              └─► POULES_DÉFINIES (tirage validé)
                    └─► PHASE1_EN_COURS (≥ 1 résultat saisi)
                          └─► PHASE1_TERMINÉE (24/24 matchs saisis)
                                └─► PHASE2_EN_COURS (bracket généré, ≥ 1 résultat)
                                      └─► TERMINÉ (48/48 matchs saisis)
```

Chaque état détermine les actions disponibles et verrouille les sections précédentes.

---

## 6. Données Gérées

### 6.1 Entités principales

**Tournoi** : nom, date, statut

**Équipe** : nom, poule affectée

**Poule** : identifiant (A/B/C/D), liste des équipes

**Match** : phase, poule/tour, équipe 1, équipe 2, score 1, score 2, score TAB 1, score TAB 2 (tirs au but si besoin), statut

**Classement de poule** *(calculé)* : points, J/V/N/D, buts pour, buts contre, différence de buts

---

## 7. Contraintes Techniques

| Contrainte | Détail |
|------------|--------|
| Type | Site web statique — mono-fichier HTML/CSS/JS |
| Stockage | En mémoire (session) — pas de base de données |
| Persistance | Données perdues au rechargement (export JSON prévu en v2) |
| Compatibilité | Navigateur desktop moderne (Chrome, Firefox, Edge) |
| Responsive | Desktop prioritaire |
| Dépendances | Aucun serveur — 100% client-side |

---

## 8. Points Ouverts Résiduels

| # | Question | Impact |
|---|----------|--------|
| 1 | Critère de départage à 3 équipes ou plus : confirmer l'application du sous-classement (confrontations directes entre les équipes concernées uniquement) | Logique de classement |
| 2 | Export / import JSON des données : v1 ou v2 ? | Architecture |

---

*Version 0.2 — Tous les points majeurs sont résolus. Prêt pour la phase de développement.*
