---
title: 3. Créer le module
---
# Créer le module

## Pourquoi commencer par le module ?

Le fichier module est le "chef d'orchestre". Il déclare les métadonnées et coordonne tous les composants. Il permet
d'enregistrer les commandes, les interactions et les écouteurs. Sans ce fichier, le bot ne saura pas que votre
module existe.

Il est important que ce fichier soit nommé en respectant la convention `nom-module.module.ts` pour que le système
de modules puisse le reconnaître automatiquement. Si ce n'est pas le cas, le module ne sera pas chargé.

## Créer le fichier principal

```typescript title="src/modules/role-reactions/role-reactions.module.ts"
import {GatewayIntentBits} from "discord.js";
import {defineModule} from "../../lib/module.js";

export default defineModule({
    // Métadonnées du module
    id: "role-reactions",
    name: "Rôles par Réactions",
    description: "Permet aux utilisateurs d'obtenir des rôles en cliquant sur des réactions",
    version: "1.0.0",
    author: "Votre Nom",

    // Permissions Discord nécessaires
    intents: [
        GatewayIntentBits.Guilds,                   // Accès aux serveurs
        GatewayIntentBits.GuildMembers,             // Gestion des membres
        GatewayIntentBits.GuildMessageReactions,    // Réactions aux messages
    ],

    // Quand le module se charge au démarrage du bot
    onLoad(client, registry) {
        // On enregistrera les composants ici
        
        console.log("Module Rôles par Réactions chargé avec succès");
    },

    // Quand un admin installe le module sur son serveur
    onInstall(client, guild, registry) {
        console.log(`Module Rôles par Réactions installé sur ${guild.name}`);
        // Ici on pourrait créer des rôles par défaut, etc.
    },

    // Quand un admin désinstalle le module
    onUninstall(client, guild, registry) {
        console.log(`Module Rôles par Réactions désinstallé de ${guild.name}`);
        // Ici on nettoierait les données stockées
    },
});
```

## Points clés expliqués

**Intents** : Ce sont les "permissions" que votre bot demande à Discord pour recevoir certains événements.

- `Guilds` : Pour accéder aux informations du serveur
- `GuildMembers` : Pour modifier les rôles des membres
- `GuildMessageReactions` : Pour détecter les clics sur les réactions

**Hooks de cycle de vie** :

- `onLoad` : Se déclenche au démarrage, même si le module n'est installé nulle part
- `onInstall` : Se déclenche quand un admin active le module sur son serveur
- `onUninstall` : Se déclenche quand un admin désactive le module

