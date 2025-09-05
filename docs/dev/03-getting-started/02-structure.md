---
title: 2. Structure du module
---

# La structure du module

### Pourquoi cette étape est importante

Avant d'écrire du code, il faut planifier l'architecture. Un module bien structuré est plus facile à maintenir et à
déboguer. Dans ce projet, nous suivons des conventions de nommage et d'organisation spécifiques qui garantissent la
cohérence et la lisibilité du code.

### Structure que nous allons créer

```
src/modules/role-reactions/
├── role-reactions.module.ts        # ← Fichier de module (cerveau)
├── commands/
│   └── setup-roles.command.ts      # ← Commande /setup-roles
├── interactions/
│   ├── role-select.menu.ts         # ← Menu de sélection
│   └── confirm-setup.button.ts     # ← Bouton de confirmation
└── listeners/
    └── reaction-add.listener.ts    # ← Gestion des réactions
```

### Conventions de nommage utilisées

#### 1. **Noms de fichiers**

- **Format** : `nom-fonctionnalité.type.ts`
- **Exemples** :
    - `setup-roles.command.ts` → Commande slash
    - `role-select.menu.ts` → Menu de sélection
    - `confirm-setup.button.ts` → Bouton d'interaction
    - `reaction-add.listener.ts` → Écouteur d'événements

#### 2. **Noms de dossiers**

- **`commands/`** : Contient toutes les commandes slash du module
- **`interactions/`** : Contient tous les composants d'interface (menus, boutons, modaux)
- **`listeners/`** : Contient tous les écouteurs d'événements Discord

#### 3. **Module principal**

- **`role-reactions.module.ts`** : Le fichier principal qui orchestre tout le module
- **Format** : `nom-module.module.ts`

### Avantages de cette structure

#### **🎯 Clarté immédiate**

En regardant un nom de fichier, on comprend instantanément :

- **Quoi** : `setup-roles` → Configuration des rôles
- **Type** : `.command` → C'est une commande slash

#### **📁 Organisation logique**

Chaque type de fonctionnalité a son dossier dédié :

- Besoin d'une nouvelle commande ? → `commands/`
- Besoin d'un nouveau bouton ? → `interactions/`
- Besoin d'écouter un événement ? → `listeners/`

#### **🔧 Maintenance facilitée**

- Plus facile de trouver et modifier du code spécifique
- Réduction des conflits lors du travail en équipe
- Structure prévisible pour tous les développeurs

### Exemple concret

Si vous voulez ajouter une commande `/list-roles`, vous savez immédiatement :

- **Où** : Dans le dossier `commands/`
- **Nom** : `list-roles.command.ts`
- **Contenu** : Une déclaration qui gère cette commande
