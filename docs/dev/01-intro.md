# Introduction

Bienvenue dans la documentation développeur d'OmniBot ! 

Cette documentation s'adresse aux développeurs qui souhaitent **étendre les fonctionnalités** d'OmniBot en créant des modules personnalisés pour ce bot Discord.

## Que trouverez-vous ici ?

- 📖 **Guides détaillés** pour créer vos propres modules
- ⚡ **Création de commandes Discord** personnalisées
- 🎯 **Gestion des événements** et interactions
- 💾 **Intégration avec la base de données**

## Architecture d'OmniBot

### Principe modulaire

OmniBot utilise une **architecture modulaire** qui vous permet d'ajouter facilement de nouvelles fonctionnalités sans modifier le code principal du bot. 

Chaque module est **indépendant** et peut contenir :
- Ses propres commandes Discord
- Ses gestionnaires d'événements
- Ses interactions (boutons, menus déroulants, etc.)
- Ses modèles de données
- Ses services métier

### Composants principaux

Le bot s'articule autour de deux éléments clés :

1. **Module Core** : Fournit les fonctionnalités de base
   - Gestion des commandes
   - Système d'événements
   - Gestion des interactions Discord

2. **Bibliothèque partagée** : Outils et utilitaires communs à tous les modules

## Structure du projet

Voici comment le code source est organisé :

```
src/
├── modules/                    # 📁 Tous vos modules personnalisés
│   └── module-1/               # 📦 Un module exemple
│   └── module-2/               # 📦 Un autre module
│   └── module-3/               # 📦 Encore un autre module
├── lib/                        # 📚 Bibliothèques utiles partagées
└── core/                       # 🛠️ Système central d'OmniBot
```

## Prochaines étapes

Maintenant que vous avez une vue d'ensemble, nous allons voir dans la section suivante comment **configurer votre environnement de développement** et créer votre premier module.

:::tip
Chaque module que vous créez est totalement isolé des autres, ce qui garantit la stabilité du bot même en cas d'erreur dans votre code.
:::