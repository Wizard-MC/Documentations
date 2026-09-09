# Commandes

Les 129 commandes de WizardMC, leur permission et leurs alias. Référence de
consultation.

**Statut : livré.** Cette table est dérivée du code.

---

## Comment la lire

| Colonne | Ce qu'elle dit |
|---|---|
| **Commande** | Ce qu'on tape, sans la barre oblique |
| **Alias** | Les autres écritures acceptées |
| **Permission** | Le nœud exigé. **Vide = accessible à tout joueur.** |
| **Console** | Si la commande peut être lancée depuis la console du serveur |

Les commandes à points — `magic.learn` — se tapent avec un espace : `/magic learn`.

Trente-quatre commandes n'ont **aucune permission** : ce sont les commandes de jeu,
accessibles à tous. Toutes les autres exigent un nœud, listé dans
[Permissions](permissions.md).


## Magie

| Commande | Alias | Permission | Console |
|---|---|---|---|
| `/magic` | `/magic help` | — | oui |
| `/magic bind` | — | — | non |
| `/magic essence` | — | `wizardmc.magic.admin` | oui |
| `/magic faconnier` | — | `wizardmc.magic.admin` | non |
| `/magic give` | — | `wizardmc.magic.admin` | oui |
| `/magic info` | — | `wizardmc.magic.admin` | oui |
| `/magic learn` | — | — | non |
| `/magic potion` | — | `wizardmc.magic.admin` | oui |
| `/magic presetslot` | — | `wizardmc.magic.admin` | oui |
| `/magic qa` | — | `wizardmc.magic.admin` | oui |
| `/magic reload` | — | `wizardmc.magic.admin` | oui |
| `/magic scroll` | — | `wizardmc.magic.admin` | oui |
| `/magic starter` | `/magic debut` | `wizardmc.magic.admin` | oui |
| `/magic teach` | — | `wizardmc.magic.admin` | oui |
| `/magic tome` | `/magic livre` | `wizardmc.magic.admin` | oui |
| `/magic unlock` | `/magic school` | `wizardmc.magic.admin` | oui |
| `/magic voie` | `/magic way` | `wizardmc.magic.admin` | oui |

## Nexus et Coven

| Commande | Alias | Permission | Console |
|---|---|---|---|
| `/covenremap` | — | `wizardmc.nexus.admin` | oui |
| `/nexus` | `/nexus help` | — | oui |
| `/nexus addshards` | `/nexus add` | `wizardmc.nexus.admin` | oui |
| `/nexus give` | — | `wizardmc.nexus.admin` | non |
| `/nexus info` | — | — | oui |
| `/nexus qa` | — | `wizardmc.nexus.admin` | oui |
| `/nexus set` | — | `wizardmc.nexus.admin` | oui |
| `/power` | `/power help`, `/powers`, `/powers help`, `/pouvoir`, `/pouvoirs`, `/pouvoir help`, `/pouvoirs help` | — | oui |
| `/power qa` | `/powers qa`, `/pouvoir qa`, `/pouvoirs qa` | `wizardmc.powers.admin` | oui |

## Autels

| Commande | Alias | Permission | Console |
|---|---|---|---|
| `/altar` | `/altar help`, `/autel`, `/autel help` | — | oui |
| `/altar qa` | `/autel qa` | `wizardmc.altars.admin` | oui |
| `/altar reset` | `/autel reset` | `wizardmc.altars.admin` | oui |
| `/altar setcooldown` | `/altar cooldown`, `/autel setcooldown` | `wizardmc.altars.admin` | oui |

## Mana Brut et Forgeron

| Commande | Alias | Permission | Console |
|---|---|---|---|
| `/forgeron` | `/forgeron help` | — | oui |
| `/forgeron info` | — | `wizardmc.forgeron.admin` | oui |
| `/forgeron move` | `/forgeron tp` | `wizardmc.forgeron.admin` | oui |
| `/forgeron spawn` | `/forgeron respawn`, `/forgeron restart` | `wizardmc.forgeron.admin` | oui |
| `/mana` | `/mana info`, `/mana stock` | — | non |
| `/mana generate` | `/mana gen` | `wizardmc.mana.admin` | oui |
| `/mana help` | — | — | oui |
| `/mana withdraw` | `/mana take` | — | non |

## Ères et Pass

| Commande | Alias | Permission | Console |
|---|---|---|---|
| `/boss set` | — | `wizardmc.admin.occurrences` | non |
| `/era` | `/ere`, `/ère`, `/season`, `/era info`, `/ere info` | — | oui |
| `/era qa` | `/ere qa`, `/ère qa`, `/season qa` | `wizardmc.eras.admin` | oui |
| `/pass` | `/erapass`, `/passe`, `/battlepass` | — | non |

## Contrats

| Commande | Alias | Permission | Console |
|---|---|---|---|
| `/contrat` | `/contrat list`, `/contract`, `/contract list` | — | non |
| `/contrat complete` | `/contract complete` | `wizardmc.contracts.admin` | oui |
| `/contrat help` | `/contract help` | — | oui |

## Boutique et cosmétiques

| Commande | Alias | Permission | Console |
|---|---|---|---|
| `/boutique` | `/gemmes`, `/cosmetics` | — | non |
| `/gift` | `/offrir` | — | non |
| `/shop` | — | — | non |
| `/shop add` | — | `core.shops` | non |
| `/shop create` | — | `core.shops` | non |
| `/shop edit` | — | `core.shops` | non |
| `/shop edit market` | — | `core.shops` | non |
| `/shop help` | — | `core.shops` | oui |
| `/shop remove` | — | `core.shops` | non |
| `/wizardshop` | `/wizardshop credit`, `/wizardshop grant`, `/wizardshop dust` | `wizardmc.shop.admin` | oui |

## Économie et échanges

| Commande | Alias | Permission | Console |
|---|---|---|---|
| `/bottlexp` | `/bouteillexp` | — | non |
| `/box` | `/mailbox`, `/mail`, `/reserve`, `/reserves` | — | non |
| `/box help` | `/mailbox help`, `/mail help`, `/reserve help`, `/reserves help` | — | non |
| `/box send` | `/mailbox send`, `/mail send`, `/reserve send`, `/reserves send`, `/don`, `/donation`, `/dons` | — | non |
| `/exchange` | `/trade` | — | non |
| `/exchange accept` | `/trade accept` | — | non |
| `/exchange deny` | `/trade deny` | — | non |
| `/hdv` | `/hoteldesventes`, `/auction` | — | non |
| `/hdv help` | `/hoteldesventes help`, `/auction help` | `core.auctions` | oui |
| `/hdv log` | `/hoteldesventes log`, `/auction log` | `core.auctions` | oui |
| `/hdv reset` | `/hoteldesventes reset`, `/auction reset` | `core.auctions` | oui |
| `/hdv sell` | `/hdv create`, `/auction sell`, `/auction create` | — | non |

## Confort

| Commande | Alias | Permission | Console |
|---|---|---|---|
| `/enderchest` | `/ec`, `/echest` | — | non |
| `/spawners` | `/sp`, `/spawnerlist`, `/myspawners` | — | non |
| `/spawners give` | `/sp give`, `/spawnerlist give`, `/myspawners give` | `core.spawners.give` | oui |
| `/spawners help` | `/sp help`, `/spawnerlist help`, `/myspawners help` | `core.spawners` | oui |
| `/spawners remove` | `/sp remove`, `/spawnerlist remove`, `/myspawners remove` | `core.spawners.remove` | oui |
| `/spawners send` | `/sp send`, `/spawnerlist send`, `/myspawners send` | `core.spawners.send` | non |
| `/totem help` | — | `core.totem` | non |
| `/totem set` | `/totem create` | `core.totem` | non |
| `/totem warp` | `/totem tp` | — | non |
| `/workbench` | `/wb`, `/craft` | — | non |

## Événements et occurrences

| Commande | Alias | Permission | Console |
|---|---|---|---|
| `/arena create` | — | `core.admin` | non |
| `/arena delete` | `/arena remove` | `core.admin` | non |
| `/blood create` | `/blood set` | `core.blood` | non |
| `/blood edit` | `/blood remove` | `core.blood` | non |
| `/blood help` | — | `core.blood` | non |
| `/boost config` | — | `core.boost` | oui |
| `/boost enable` | — | `core.boost` | oui |
| `/boost help` | — | `core.boost` | oui |
| `/boost mode` | — | `core.boost` | oui |
| `/crate add` | — | `core.crates` | non |
| `/crate create` | — | `core.crates` | non |
| `/crate delete` | — | `core.crates` | non |
| `/crate edit` | — | `core.crates` | non |
| `/crate give` | — | `core.crates` | oui |
| `/crate help` | — | `core.crates` | non |
| `/crate setlocation` | `/crate setloc` | `core.crates` | non |
| `/event help` | — | `core.occurrences` | non |
| `/event now` | — | `core.occurrences` | non |
| `/event start` | — | `core.occurrences` | non |
| `/event stop` | — | `core.occurrences` | non |
| `/fallenchest add common` | — | `core.fallenchest` | non |
| `/fallenchest add legendary` | — | `core.fallenchest` | non |
| `/fallenchest edit common` | — | `core.fallenchest` | non |
| `/fallenchest edit legendary` | — | `core.fallenchest` | non |
| `/fallenchest edit point` | — | `core.fallenchest` | non |
| `/fallenchest generate` | — | `core.fallenchest` | non |
| `/fallenchest point` | — | `core.fallenchest` | non |
| `/fallenchest set common` | — | `core.fallenchest` | non |
| `/fallenchest set legendary` | — | `core.fallenchest` | non |
| `/koth create` | — | `core.koths` | non |
| `/koth help` | — | `core.koths` | non |
| `/koth list` | — | `core.koths` | non |
| `/lucky add` | — | `core.luckys` | non |
| `/lucky remove` | — | `core.luckys` | non |
| `/schedule` | — | `core.schedules` | non |
| `/schedule add` | — | `core.schedules` | non |
| `/schedule help` | — | `core.schedules` | non |
| `/treatment add` | — | `core.occurrence.treatments` | non |
| `/treatment edit` | — | `core.occurrence.treatments` | non |
| `/treatment help` | — | `core.occurrence.treatments` | non |
| `/treatment set` | — | `core.occurrence.treatments` | non |

## Chat et divers

| Commande | Alias | Permission | Console |
|---|---|---|---|
| `/chat clear` | — | `core.chat.*` | non |
| `/chat toggle` | `/chat on`, `/chat off` | `core.chat.*` | non |
| `/combattag` | `/ct`, `/combat` | — | non |
| `/loot edit` | `/loots event` | `core.loots` | non |
| `/loot help` | `/loots help` | `core.loots` | non |
| `/loot list` | `/loots list` | `core.loots` | non |
| `/notif test` | — | `core.notifications` | non |
| `/save` | — | `core.save` | non |
| `/title test` | — | `core.titles` | non |
| `/wiki` | `/help`, `/aide` | — | non |
| `/wizardcore` | `/wizardcore help`, `/whelp`, `/wchelp`, `/wizard help` | — | oui |

---

## À lire ensuite

- [Permissions](permissions.md) — les nœuds, et des rôles prêts à l'emploi
- [Modération](../05-operer/moderation.md) — quelles commandes pour quel incident
- [Game master](../05-operer/game-master.md) — les commandes d'animation
- [Premiers pas](../04-jouer/premiers-pas.md) — les commandes de la première heure

