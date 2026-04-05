# Roadmap — Football Tournament Manager 2026

> **Document de planification des évolutions**  
> Dernière mise à jour : 05 avril 2026

---

## 📊 Vue d'Ensemble

Ce document liste les fonctionnalités planifiées pour les futures versions de l'application Football Tournament Manager 2026.

### Statut Actuel
- **Version déployée :** v1.0
- **Date de release :** 05 avril 2026
- **Statut :** Production - Opérationnel

---

## ✅ Version 1.0 — Fonctionnalités Implémentées

### Phase 1 — Phase de Poules
- [x] Configuration du tournoi (nom, date)
- [x] Gestion des 16 équipes
- [x] Répartition en 4 poules de 4 équipes (manuelle ou aléatoire)
- [x] Génération automatique des 24 matchs de poules
- [x] Saisie des résultats des matchs
- [x] Calcul automatique des classements avec critères de départage

### Phase 2 — Brackets de Classement
- [x] Génération des brackets (Principal 1-8 et Consolant 9-16)
- [x] Saisie des résultats des phases finales
- [x] Classement final de la 1ère à la 16ème place

### Technique
- [x] Application web statique (HTML/CSS/JavaScript)
- [x] Stockage des données dans localStorage
- [x] Design moderne avec police Inter
- [x] Navigation entre les étapes du tournoi
- [x] Déploiement sur GitHub Pages

---

## 🚀 Version 2.0 — Prochaine Version Majeure

**Date cible :** T3 2026

### 🎯 Priorité HAUTE

#### 1. Export/Import JSON
**Statut :** Non implémenté  
**Effort estimé :** 2-3 jours  
**Description :**
- Bouton "Exporter" pour télécharger les données du tournoi au format JSON
- Bouton "Importer" pour recharger un tournoi à partir d'un fichier JSON
- Validation du fichier JSON à l'import (format, intégrité)
- Gestion des erreurs (fichier corrompu, format invalide)

**Bénéfices :**
- Sauvegarde persistante hors navigateur
- Possibilité de partager un tournoi
- Backup manuel des données
- Migration entre navigateurs/machines

**Spécification technique :**
- Format JSON structuré (version, métadonnées, équipes, poules, matchs)
- Nom de fichier : `tournoi-{nom}-{date}.json`
- Compression optionnelle pour réduire la taille

---

#### 2. Gestion des Tirs au But (Phase 1)
**Statut :** Non implémenté  
**Effort estimé :** 3-4 jours  
**Description :**
- Détection automatique des égalités parfaites (critères 1-5 identiques)
- Affichage d'un champ de saisie supplémentaire pour tirs au but
- Format : "4 - 3" (nombre de tirs marqués par équipe)
- Le vainqueur aux tirs au but est classé devant
- Les statistiques de buts ne sont PAS modifiées

**Règle actuelle (v1.0) :**
En cas d'égalité parfaite, l'ordre alphabétique est utilisé comme dernier recours.

**Bénéfices :**
- Résolution équitable des égalités
- Conformité avec les règles du football
- Transparence du classement

---

### 🎯 Priorité MOYENNE

#### 3. Validation des Scores Nuls en Phase 2
**Statut :** À vérifier/Renforcer  
**Effort estimé :** 1 jour  
**Description :**
- Bloquer la saisie d'un score nul (ex: 0-0, 1-1) en Phase 2
- Message d'erreur explicite : "Un vainqueur doit être désigné"
- Obligation de saisir un résultat avec vainqueur

**Note :** La spécification prévoit cette contrainte. À vérifier dans le code actuel.

---

#### 4. Gestion Multi-Tournois
**Statut :** Planifié v2.0  
**Effort estimé :** 5-7 jours  
**Description :**
- Liste des tournois sauvegardés
- Possibilité de créer plusieurs tournois
- Sélection du tournoi actif
- Archivage des tournois terminés
- Suppression de tournois

**Impact technique :**
- Modification du modèle de données localStorage
- Ajout d'un ID unique par tournoi
- Page de sélection/gestion des tournois

---

#### 5. Statistiques Avancées
**Statut :** Planifié v2.0  
**Effort estimé :** 4-5 jours  
**Description :**
- Meilleur buteur du tournoi
- Meilleure défense (moins de buts encaissés)
- Plus grande victoire
- Matchs les plus prolifiques
- Graphiques de performance par équipe

---

### 🎯 Priorité BASSE

#### 6. Export PDF/Impression
**Statut :** Planifié v2.1  
**Effort estimé :** 3-4 jours  
**Description :**
- Export des classements en PDF
- Feuilles de matchs imprimables
- Brackets visuels pour affichage
- Version imprimable du classement final

---

#### 7. Améliorations UX/Accessibilité
**Statut :** Amélioration continue  
**Effort estimé :** Ongoing  
**Description :**
- Lien d'évitement "Aller au contenu principal"
- Annonces dynamiques avec `aria-live`
- Vérification complète des contrastes WCAG AA
- Tests au lecteur d'écran
- Navigation clavier optimisée

---

#### 8. Planning Horaire des Matchs
**Statut :** Planifié v2.1  
**Effort estimé :** 5-6 jours  
**Description :**
- Attribution d'horaires aux matchs
- Gestion des terrains/créneaux
- Vue calendrier
- Export du planning

---

#### 9. Gestion des Arbitres
**Statut :** Planifié v2.2  
**Effort estimé :** 3-4 jours  
**Description :**
- Liste des arbitres
- Affectation aux matchs
- Disponibilité/Conflits

---

## 🔮 Version 3.0 — Vision Long Terme

**Date cible :** 2027

### Fonctionnalités Avancées
- [ ] Authentification et comptes utilisateurs
- [ ] Mode multi-utilisateur (plusieurs organisateurs)
- [ ] Notifications en temps réel
- [ ] Application mobile native (React Native / Flutter)
- [ ] Mode hors ligne (PWA)
- [ ] Partage public de tournois (URL dédiée)
- [ ] API REST pour intégrations tierces
- [ ] Gestion des joueurs individuels (compositions d'équipes)
- [ ] Système de commentaires/annotations
- [ ] Historique complet des modifications

---

## 📋 Critères de Priorisation

Les fonctionnalités sont priorisées selon :

1. **Impact utilisateur** : Valeur apportée aux organisateurs
2. **Complexité technique** : Effort de développement
3. **Dépendances** : Prérequis techniques
4. **Feedback utilisateurs** : Demandes terrain

---

## 🛠️ Processus de Développement

### Cycle de Release
1. **Planification** : Sélection des fonctionnalités
2. **Développement** : Implémentation + tests
3. **Recette** : Validation fonctionnelle
4. **Déploiement** : Mise en production
5. **Suivi** : Monitoring + corrections

### Versioning
- **Major (X.0.0)** : Nouvelles fonctionnalités majeures
- **Minor (x.X.0)** : Fonctionnalités mineures, améliorations
- **Patch (x.x.X)** : Corrections de bugs, optimisations

---

## 📝 Notes de Conception

### Décisions Techniques v1.0

**Pourquoi localStorage plutôt que sessionStorage ?**
- ✅ Persistance des données après fermeture du navigateur
- ✅ Meilleure expérience utilisateur
- ❌ Données limitées à un navigateur/machine
- ➡️ Solution : Export JSON (v2.0)

**Pourquoi pas de backend ?**
- ✅ Application 100% client-side
- ✅ Aucun serveur requis
- ✅ Déploiement simple (GitHub Pages)
- ✅ Pas de coûts d'hébergement
- ❌ Pas de synchronisation multi-devices
- ➡️ Solution : Authentification + Backend (v3.0)

---

## 📞 Contribuer

Pour suggérer une nouvelle fonctionnalité ou signaler un problème :
1. Ouvrir une **Issue** sur le repository GitHub
2. Décrire le besoin, le contexte et les bénéfices attendus
3. L'équipe projet évaluera et priorisera la demande

---

**Dernière révision :** 05 avril 2026  
**Auteur :** Thierry Sutter  
**Repository :** [github.com/thierrysutter/football-tournament-manager-2026](https://github.com/thierrysutter/football-tournament-manager-2026)
