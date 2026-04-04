# Spécification Fonctionnelle — Gestionnaire de Tournois de Football

> **Version** : 0.3  
> **Date** : 04 avril 2026  
> **Auteur** : Utilisateur (usage personnel)  
> **Statut** : Prêt pour maquettage et développement

---

## 1. Contexte & Objectifs

### 1.1 Présentation

L'application est un **outil de gestion de tournois de football** à usage personnel. Elle permet de configurer un tournoi, d'enregistrer les équipes participantes, de générer automatiquement les rencontres, de saisir les résultats et de suivre l'avancement jusqu'au classement final.

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
| Gestion des équipes | Gestion des joueurs individuels |
| Génération automatique des matchs | Gestion des arbitres |
| Saisie des résultats | Notifications / partage public |
| Classements de poules | Multi-tournois simultanés |
| Brackets phase finale complets | Authentification |
| Classement final places 1 à 16 | Application mobile native |
| Export JSON des données | Planning horaire des matchs |

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
|------|-----------|--------------------|
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

- L'organisateur saisit directement le score final (incluant le résultat des tirs au but si nécessaire, géré hors application).
- **Contrainte de validation** : un score nul est bloqué — un vainqueur doit impérativement être désigné pour valider le match.

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

- Les **24 matchs de Phase 1** sont générés automatiquement à la validation du tirage.
- Les **24 matchs de Phase 2** sont générés manuellement par l'organisateur une fois tous les matchs de Phase 1 saisis.

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
| Tirs au but en poule | Champ supplémentaire si critères 1-5 à parfaite égalité |
| Recalcul | Toute modification recalcule classements et qualifications |
| Accès Phase 2 | Matchs accessibles uniquement après génération du bracket |

### 4.6 Suivi et Classements

**Phase 1 :**
- 4 tableaux de classement : Points, J / V / N / D, BP / BC / Diff
- Statut de chaque match (à jouer / joué)
- Qualifiés mis en évidence dès que la poule est complète

**Phase 2 :**
- Bracket visuel du Tableau Principal avec progression en temps réel
- Bracket visuel du Tableau Consolant avec progression en temps réel

**Classement final :**
- Classement général de la 1re à la 16e place à l'issue de tous les matchs

### 4.7 Export des Données (v1)

- Export de l'ensemble des données du tournoi au format **JSON**
- Import d'un fichier JSON pour recharger un tournoi précédemment exporté
- Le fichier exporté contient : configuration, équipes, poules, matchs, résultats, classements

---

## 5. Navigation & États du Tournoi

### 5.1 Structure de l'Application

```
┌─────────────────────────────────┐
│  Dashboard (accueil)            │  ← Vue d'ensemble, statut, progression
├─────────────────────────────────┤
│  Configuration                  │  ← Nom, date du tournoi
├─────────────────────────────────┤
│  Équipes                        │  ← Liste, ajout, modification, suppression
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

**Tournoi** : nom, date, statut, horodatage création/modification

**Équipe** : identifiant unique, nom, poule affectée

**Poule** : identifiant (A/B/C/D), liste des équipes

**Match** : phase (1 ou 2), poule/tour, équipe 1, équipe 2, score 1, score 2, score tirs au but 1, score tirs au but 2, statut (à jouer / joué)

**Classement de poule** *(calculé)* : points, matchs joués, victoires, nuls, défaites, buts pour, buts contre, différence de buts

### 6.2 Format d'export JSON

```json
{
  "version": "1.0",
  "exportedAt": "2026-04-04T18:00:00Z",
  "tournament": { "name": "", "date": "", "status": "" },
  "teams": [],
  "groups": [],
  "matches": []
}
```

---

## 7. Contraintes Techniques

| Contrainte | Détail |
|------------|--------|
| Type | Site web statique — mono-fichier HTML / CSS / JS |
| Stockage | En mémoire (session) + export/import JSON |
| Persistance | Export JSON manuel pour sauvegarder et recharger |
| Compatibilité | Navigateur desktop moderne (Chrome, Firefox, Edge) |
| Responsive | Desktop prioritaire — adaptation mobile en meilleur effort |
| Dépendances | Aucun serveur — 100% client-side |

---

## 8. Exigences d'Accessibilité et de Conformité (RGAA)

L'application est conçue selon une approche **"accessibility by design"** et vise un haut niveau de conformité au **RGAA 4.x** (Référentiel Général d'Amélioration de l'Accessibilité), référentiel officiel français organisé en 13 thématiques et 106 critères.

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
| Lien d'évitement | Lien "Aller au contenu principal" comme premier élément focusable |
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
| Annonces dynamiques | Changements importants annoncés via `aria-live` (ex : "Résultat enregistré", "Phase 2 générée") |
| Groupes de champs | `<fieldset>` / `<legend>` pour les groupes de contrôles liés |

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
- [ ] Vérification responsive (zoom 200%, 400%)
- [ ] Validation HTML (Nu Html Checker)

---

## 9. Points Ouverts Résiduels

| # | Question | Impact | Priorité |
|---|----------|--------|----------|
| 1 | Design visuel exact : palette de couleurs, typographie — à valider sur maquette | UI | Avant développement |
| 2 | Comportement à la perte de données (rechargement sans export) : avertissement ? | UX | À définir en dev |

---

**Note** : Tous les points fonctionnels (règles de départage, matchs de classement, grilles d'appariement, export JSON v1) sont validés et intégrés dans la spécification. Les points résiduels ci-dessus concernent uniquement l'implémentation technique et l'UX.

*Version 0.3 — Spécification complète. Prêt pour le développement écran par écran.*


---

*Version 0.3 — Spécification complète et validée. Prêt pour le développement écran par écran.*
