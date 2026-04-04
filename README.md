# Football Tournament Manager 2026

Application web de gestion de tournois de football à usage personnel.

## Description

Outil permettant de gérer intégralement un tournoi de football en 2 phases :
- **Phase 1** : 4 poules de 4 équipes (tous contre tous)
- **Phase 2** : Brackets de classement complets (places 1 à 16)

## Fonctionnalités

- Configuration du tournoi (nom, date)
- Gestion des 16 équipes participantes
- Tirage au sort ou affectation manuelle des poules
- Génération automatique des 48 matchs
- Saisie des résultats avec recalcul en temps réel
- Classements de poules avec critères de départage complets
- Brackets visuels Phase 2 (tableau principal + tableau consolant)
- Classement final de la 1ère à la 16ème place

## Structure du projet

```
├── docs/
│   └── specification-fonctionnelle.md   # Spécification fonctionnelle complète
└── src/
    └── (code de l'application)
```

## Documentation

La spécification fonctionnelle détaillée est disponible dans [`docs/specification-fonctionnelle.md`](docs/specification-fonctionnelle.md).

## Technologie

Application web statique — HTML / CSS / JavaScript (100% client-side, aucun serveur requis).
