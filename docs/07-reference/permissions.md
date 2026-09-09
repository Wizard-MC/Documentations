# Permissions

Tous les nœuds de permission de WizardMC, ce qu'ils ouvrent, et des rôles prêts à
l'emploi.

**Statut : livré.** Cette table est dérivée du code.

---

## 1. Les deux familles de nœuds

| Préfixe | Origine | Portée |
|---|---|---|
| `wizardmc.*` | Les systèmes propres à WizardMC | Magie, Nexus, Autels, Mana, Ères, Pouvoirs, Contrats, Quêtes |
| `core.*` | Le socle historique | Événements, coffres, enchères, boutiques, chat, butin |

La coexistence des deux préfixes est un héritage. Elle n'a pas de signification : un
nœud `core.*` n'est ni plus ni moins puissant qu'un nœud `wizardmc.*`.

---

## 2. Les nœuds, et ce qu'ils ouvrent

| Nœud | Commandes ouvertes |
|---|---|
| `core.admin` | `/arena create`, `/arena delete` |
| `core.auctions` | `/hdv help`, `/hdv log`, `/hdv reset` |
| `core.blood` | `/blood create`, `/blood edit`, `/blood help` |
| `core.boost` | `/boost config`, `/boost enable`, `/boost help`, `/boost mode` |
| `core.chat.*` | `/chat clear`, `/chat toggle` |
| `core.crates` | `/crate add`, `/crate create`, `/crate delete`, `/crate edit`, `/crate give`, `/crate help`, `/crate setlocation` |
| `core.fallenchest` | `/fallenchest add common`, `/fallenchest add legendary`, `/fallenchest edit common`, `/fallenchest edit legendary`, `/fallenchest edit point`, `/fallenchest generate`, `/fallenchest point`, `/fallenchest set common`, `/fallenchest set legendary` |
| `core.koths` | `/koth create`, `/koth help`, `/koth list` |
| `core.loots` | `/loot edit`, `/loot help`, `/loot list` |
| `core.luckys` | `/lucky add`, `/lucky remove` |
| `core.notifications` | `/notif test` |
| `core.occurrence.treatments` | `/treatment add`, `/treatment edit`, `/treatment help`, `/treatment set` |
| `core.occurrences` | `/event help`, `/event now`, `/event start`, `/event stop` |
| `core.save` | `/save` |
| `core.schedules` | `/schedule`, `/schedule add`, `/schedule help` |
| `core.shops` | `/shop add`, `/shop create`, `/shop edit`, `/shop edit market`, `/shop help`, `/shop remove` |
| `core.spawners` | `/spawners help` |
| `core.spawners.give` | `/spawners give` |
| `core.spawners.remove` | `/spawners remove` |
| `core.spawners.send` | `/spawners send` |
| `core.titles` | `/title test` |
| `core.totem` | `/totem help`, `/totem set` |
| `wizardmc.admin.occurrences` | `/boss set` |
| `wizardmc.altars.admin` | `/altar qa`, `/altar reset`, `/altar setcooldown` |
| `wizardmc.contracts.admin` | `/contrat complete` |
| `wizardmc.eras.admin` | `/era qa` |
| `wizardmc.forgeron.admin` | `/forgeron info`, `/forgeron move`, `/forgeron spawn` |
| `wizardmc.magic.admin` | `/magic essence`, `/magic faconnier`, `/magic give`, `/magic info`, `/magic potion`, `/magic presetslot`, `/magic qa`, `/magic reload`, `/magic scroll`, `/magic starter`, `/magic teach`, `/magic tome`, `/magic unlock`, `/magic voie` |
| `wizardmc.mana.admin` | `/mana generate` |
| `wizardmc.nexus.admin` | `/covenremap`, `/nexus addshards`, `/nexus give`, `/nexus qa`, `/nexus set` |
| `wizardmc.powers.admin` | `/power qa` |
| `wizardmc.shop.admin` | `/wizardshop` |

---

## 3. Ce qui est ouvert à tout le monde

Ces 34 commandes n'exigent **aucune permission**. Ce sont les commandes de jeu.

| Commande |
|---|
| `/altar` |
| `/bottlexp` |
| `/boutique` |
| `/box` |
| `/box help` |
| `/box send` |
| `/combattag` |
| `/contrat` |
| `/contrat help` |
| `/enderchest` |
| `/era` |
| `/exchange` |
| `/exchange accept` |
| `/exchange deny` |
| `/forgeron` |
| `/gift` |
| `/hdv` |
| `/hdv sell` |
| `/magic` |
| `/magic bind` |
| `/magic learn` |
| `/mana` |
| `/mana help` |
| `/mana withdraw` |
| `/nexus` |
| `/nexus info` |
| `/pass` |
| `/power` |
| `/shop` |
| `/spawners` |
| `/totem warp` |
| `/wiki` |
| `/wizardcore` |
| `/workbench` |

---

## 4. Des rôles prêts à l'emploi

Quatre rôles qui couvrent les besoins courants. À adapter, mais l'ordre de puissance
compte : chaque rôle contient le précédent.

### Joueur

**Aucun nœud.** Les commandes de jeu suffisent, et c'est délibéré : un joueur n'a besoin
d'aucune permission pour tout faire dans le jeu.

### Support

Voir sans modifier. Pour répondre aux questions sans pouvoir casser quoi que ce soit.

| Nœud | Pourquoi |
|---|---|
| `core.chat.*` | Nettoyer le chat après un débordement |
| `core.mobs.list` | Voir les espèces, et faire apparaître pour reproduire un bug |

> **Aucun nœud de modification.** C'est le point : un membre du support qui ne peut rien
> changer ne peut rien casser, et n'est jamais soupçonné de favoritisme.

### Game master

Animer le serveur. Voir [Game master](../05-operer/game-master.md).

| Nœud | Ce qu'il ouvre |
|---|---|
| `core.occurrences` | Lancer et arrêter les événements |
| `core.occurrence.treatments` | Les traitements d'occurrence |
| `core.schedules` | La planification |
| `core.koths` | Les KotH |
| `core.fallenchest` | Les coffres tombés |
| `core.crates` | Les caisses |
| `core.luckys` | Les blocs chanceux |
| `core.boost` | Les boosts |
| `core.mobs.list` | Faire apparaître des créatures |
| `wizardmc.admin.occurrences` | Régler le Colosse |

> **Pas de nœud qui modifie la progression d'un joueur ou d'un Coven.** Un game master
> anime ; il n'arbitre pas.

### Administrateur

Tout. Les nœuds de correction sont ici, et **toute correction se note**.

| Nœud | Ce qu'il ouvre |
|---|---|
| `wizardmc.nexus.admin` | Modifier un Nexus |
| `wizardmc.altars.admin` | Réinitialiser un Autel |
| `wizardmc.mana.admin` | Créer du Mana Brut |
| `wizardmc.magic.admin` | Donner un sort, une école, recharger |
| `wizardmc.eras.admin` | L'état de l'Ère |
| `wizardmc.powers.admin` | Les pouvoirs |
| `wizardmc.forgeron.admin` | Déplacer le Forgeron |
| `wizardmc.contracts.admin` | Valider un contrat |
| `wizardmc.shop.admin` | La boutique |
| `wizardmc.quest.admin` | Recharger les quêtes |
| `core.admin`, `core.save` | Le socle |
| Tous les `core.*` restants | — |

---

## 5. Les nœuds à ne jamais donner à la légère

| Nœud | Ce qu'il permet | Le risque |
|---|---|---|
| `wizardmc.nexus.admin` | Fixer le niveau et les Éclats d'un Coven | Décider d'une Ère |
| `wizardmc.mana.admin` | Créer du Mana Brut | Détruire l'économie du convoi |
| `wizardmc.magic.admin` | Donner sorts et écoles | Contourner toute la progression personnelle |
| `wizardmc.altars.admin` | Réinitialiser un Autel | Annuler une capture disputée |
| `core.boost` | Activer un boost | Casser l'équilibre sans que ça se voie |
| `core.crates`, `core.fallenchest` | Distribuer du butin | — |

Chacun de ces nœuds permet de **donner un avantage**, ce que le principe central du
serveur interdit. Ils existent pour réparer, pas pour récompenser.

---

## 6. Les règles d'attribution

1. **Le moins possible.** Un rôle qui a plus de nœuds qu'il n'en utilise finira par s'en
   servir.
2. **Le support ne modifie rien.** Voir ci-dessus.
3. **Un game master n'a aucun nœud de progression.** Il anime, il n'arbitre pas.
4. **Toute correction se note** : qui, quoi, quand, pourquoi. Sans trace, une correction
   est indiscernable d'un abus, et c'est le staff qui y perd.
5. **Un membre du staff qui joue en Coven** ne traite aucun incident impliquant son
   Coven. Voir [Modération](../05-operer/moderation.md#le-piège-du-staff-qui-joue).

---

## À lire ensuite

- [Commandes](commandes.md) — les 129 commandes
- [Modération](../05-operer/moderation.md) — la procédure et le barème
- [Game master](../05-operer/game-master.md) — animer sans arbitrer

