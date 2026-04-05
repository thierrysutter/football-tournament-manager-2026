# Football Tournament Manager 2026

Application web de gestion de tournois de football à usage personnel.

## 🎯 Description

Outil permettant de gérer intégralement un tournoi de football en 2 phases :
- **Phase 1** : 4 poules de 4 équipes (tous contre tous)
- **Phase 2** : Brackets de classement complets (places 1 à 16)

## ✨ Fonctionnalités

### Configuration et Gestion
- Configuration du tournoi (nom, date)
- Gestion des 16 équipes participantes
- Tirage au sort ou affectation manuelle des poules
- Génération automatique des 48 matchs

### Suivi des Résultats
- Saisie des résultats avec recalcul en temps réel
- Classements de poules avec critères de départage complets
- Brackets visuels Phase 2 (tableau principal + tableau consolant)
- Classement final de la 1ère à la 16ème place

### Persistance des Données
- Sauvegarde automatique dans le localStorage du navigateur
- Aucune perte de données lors du rechargement de la page

## 🚀 Accès à l'Application

**URL de l'application :** https://thierrysutter.github.io/football-tournament-manager-2026/

## 📁 Structure du Projet

```
├── docs/
│   └── specification-fonctionnelle.md  # Spécification fonctionnelle complète
└── src/
    ├── index.html                      # Page de configuration
    ├── teams.html                      # Gestion des équipes
    ├── groups.html                     # Composition des poules
    ├── matches.html                    # Saisie des résultats
    ├── standings.html                  # Classements
    ├── brackets.html                   # Brackets de la phase 2
    └── final-results.html              # Classement final
```

## 📖 Documentation

La spécification fonctionnelle détaillée est disponible dans [`docs/specification-fonctionnelle.md`](docs/specification-fonctionnelle.md).

## 🛠️ Technologie

Application web statique — HTML / CSS / JavaScript (100% client-side, aucun serveur requis).

### Stack Technique
- **Frontend** : HTML5, CSS3, JavaScript ES6+
- **Stockage** : localStorage API
- **Déploiement** : GitHub Pages

## 📝 Changelog

### Dernières Mises à Jour
- **2026-04-05** : Correction du bug de suppression d'équipe (fonction deleteTeam)
- **2026-04-04** : Ajout de la roadmap dans la documentation
- **2026-04-04** : Correction visuelle de la page matches.html

## 🔧 Installation Locale

1. Cloner le repository :
```bash
git clone https://github.com/thierrysutter/football-tournament-manager-2026.git
```

2. Ouvrir le fichier `src/index.html` dans un navigateur web

Aucune installation de dépendances n'est nécessaire.

## 👤 Auteur

**Thierry Sutter**
- GitHub: [@thierrysutter](https://github.com/thierrysutter)

## 📄 Licence

Projet à usage personnel.
