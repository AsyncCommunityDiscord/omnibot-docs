---
title: 4. Créer une commande
---

# Créer la commande `/setup-roles`

## Objectif de cette étape

Créer une commande que seuls les administrateurs peuvent utiliser pour configurer le système de rôles par réactions.

## Étape 1 : Structure de base et imports

Créez d'abord le dossier `commands/` dans votre module, puis créez le fichier :

```typescript title="src/modules/role-reactions/commands/setup-roles.command.ts"
import {SlashCommandBuilder} from "discord.js";
import {declareCommand} from "../../../lib/command.js";

// highlight-start
export default declareCommand({
    // Notre commande sera définie ici
});
// highlight-end
```

### Comprendre `declareCommand`

La fonction `declareCommand` est un utilitaire du framework qui :

- **Enregistre** automatiquement votre commande dans le système
- **Valide** la structure de votre commande
- **Fournit** le typage TypeScript approprié
- **Gère** l'intégration avec Discord.js

Elle attend un objet avec deux propriétés principales :

- `data` : La configuration Discord de la commande
- `execute` : La fonction qui s'exécute quand la commande est utilisée

Il est également possible de fournir une autocomplétion pour les options avec la méthode `complete`, mais nous n'en
aurons pas besoin ici.

## Étape 2 : Configuration de base de la commande

Ajoutons la configuration Discord de base :

```typescript title="src/modules/role-reactions/commands/setup-roles.command.ts"
import {SlashCommandBuilder} from "discord.js";
import {declareCommand} from "../../../lib/command.js";

export default declareCommand({
    // highlight-start
    data: new SlashCommandBuilder()
        .setName("setup-roles")
        .setDescription("Configure un message avec des rôles à attribuer par réaction"),
    // highlight-end

    async execute(interaction) {
        // La logique de la commande sera ici
        await interaction.reply("Commande en cours de développement !");
    },
});
```

L'objet `SlashCommandBuilder` permet de définir les métadonnées de la commande. Ceci permet, à l'activation du module
sur un serveur, d'enregistrer automatiquement la commande auprès de l'API Discord.

## Étape 3 : Ajouter des paramètres et permissions

Enrichissons la commande avec un paramètre canal et des restrictions :

```typescript title="src/modules/role-reactions/commands/setup-roles.command.ts"
import {
    SlashCommandBuilder,
    PermissionFlagsBits
} from "discord.js";
import {declareCommand} from "../../../lib/command.js";

export default declareCommand({
    data: new SlashCommandBuilder()
        .setName("setup-roles")
        .setDescription("Configure un message avec des rôles à attribuer par réaction")
        // highlight-start
        .addChannelOption(option =>
            option.setName("canal")
                .setDescription("Canal où envoyer le message de rôles")
                .setRequired(true)
        )
        .setDefaultMemberPermissions(PermissionFlagsBits.ManageRoles),
    // highlight-end

    async execute(interaction) {
        // highlight-start
        const channel = interaction.options.getChannel("canal", true);
        // highlight-end
        await interaction.reply(`Configuration pour le canal ${channel} !`);
    },
});
```

:::note
Ici, nous avons changé la définition de la commande. Si vous modifiez une commande existante, vous devrez soit :

- Changer la version du module afin que le système détecte une modification et réenregistre la commande
- Activer et désactiver le module sur le serveur pour forcer la mise à jour
  :::

## Étape 4 : Validation et gestion d'erreurs

Ajoutons des vérifications de base :

```typescript title="src/modules/role-reactions/commands/setup-roles.command.ts"
import {
    SlashCommandBuilder,
    PermissionFlagsBits,
    MessageFlags
} from "discord.js";
import {declareCommand} from "../../../lib/command.js";

export default declareCommand({
    data: new SlashCommandBuilder()
        .setName("setup-roles")
        .setDescription("Configure un message avec des rôles à attribuer par réaction")
        .addChannelOption(option =>
            option.setName("canal")
                .setDescription("Canal où envoyer le message de rôles")
                .setRequired(true)
        )
        .setDefaultMemberPermissions(PermissionFlagsBits.ManageRoles),

    async execute(interaction) {
        // Récupérer le canal sélectionné
        const channel = interaction.options.getChannel("canal", true);

        // Vérifier que c'est un canal textuel
        if (!channel.isTextBased()) {
            await interaction.reply({
                content: "❌ Le canal sélectionné doit être un canal textuel.",
                flags: MessageFlags.Ephemeral,
            });
            return;
        }

        await interaction.reply({
            content: `✅ Canal ${channel} validé !`,
            flags: MessageFlags.Ephemeral,
        });
    },
});
```

### Messages éphémères

- **`MessageFlags.Ephemeral`** : Le message n'est visible que par l'utilisateur qui a lancé la commande
- Utile pour les messages d'erreur ou de configuration

## Étape 5 : Créer un menu de sélection des rôles

Maintenant, créons une interface pour sélectionner les rôles :

```typescript title="src/modules/role-reactions/commands/setup-roles.command.ts"
import {
    SlashCommandBuilder,
    PermissionFlagsBits,
    StringSelectMenuBuilder,
    ActionRowBuilder,
    MessageFlags
} from "discord.js";
import {declareCommand} from "../../../lib/command.js";

export default declareCommand({
    data: new SlashCommandBuilder()
        .setName("setup-roles")
        .setDescription("Configure un message avec des rôles à attribuer par réaction")
        .addChannelOption(option =>
            option.setName("canal")
                .setDescription("Canal où envoyer le message de rôles")
                .setRequired(true)
        )
        .setDefaultMemberPermissions(PermissionFlagsBits.ManageRoles),

    async execute(interaction) {
        const channel = interaction.options.getChannel("canal", true);

        if (!channel.isTextBased()) {
            await interaction.reply({
                content: "❌ Le canal sélectionné doit être un canal textuel.",
                flags: MessageFlags.Ephemeral,
            });
            return;
        }

        // Récupérer les rôles disponibles du serveur
        const availableRoles = interaction.guild!.roles.cache
            .filter(role => !role.managed && role.name !== "@everyone")
            .first(25); // Discord limite à 25 options

        if (availableRoles.length === 0) {
            await interaction.reply({
                content: "❌ Aucun rôle disponible trouvé sur ce serveur.",
                flags: MessageFlags.Ephemeral,
            });
            return;
        }

        // highlight-start
        // Créer le menu de sélection
        const roleSelect = new StringSelectMenuBuilder()
            .setCustomId(`role-select:${channel.id}:${interaction.user.id}`)
            .setPlaceholder("Sélectionnez les rôles à inclure...")
            .setMinValues(1)
            .setMaxValues(Math.min(availableRoles.length, 10))
            .addOptions(
                availableRoles.map(role => ({
                    label: role.name,
                    value: role.id,
                    description: `${role.members.size} membres`,
                    emoji: "🎭",
                }))
            );
        // highlight-end

        const selectRow = new ActionRowBuilder<StringSelectMenuBuilder>()
            .addComponents(roleSelect);

        await interaction.reply({
            content: `🎭 **Configuration des rôles pour ${channel}**\n\nSélectionnez les rôles que les utilisateurs pourront obtenir :`,
            components: [selectRow],
            flags: MessageFlags.Ephemeral,
        });
    },
});
```

### Comprendre les interactions Discord

#### Custom ID avec données

```typescript
.setCustomId(`role-select:${channel.id}:${interaction.user.id}`)
```

Les Custom ID doivent suivre le format `nom:arg1:arg2:arg3` permet de pouvoir passer des informations entre la commande
et le gestionnaire d'interaction. Le gestionnaire d'interaction pourra extraire ces informations pour savoir quel canal utiliser et qui a initié la
commande.

#### Composants d'interface

- **`StringSelectMenuBuilder`** : Crée un menu déroulant
- **`ActionRowBuilder`** : Conteneur obligatoire pour les composants
- **`components`** : Propriété pour attacher les composants au message

### Cycle de vie des interactions

1. **Commande exécutée** → Affiche le menu de sélection
2. **Utilisateur sélectionne** → Déclenche une nouvelle interaction
3. **Handler d'interaction** → Traite la sélection (prochaine étape)

## Points clés à retenir

- **`declareCommand`** permet l'enregistrement des commandes
- **Les paramètres** se récupèrent via `interaction.options`
- **Les permissions** se définissent au niveau de la commande
- **Les Custom ID** transportent des données entre interactions
- **Les composants** créent des interfaces interactives
