# Plages d'identifiants

Les identifiants d'entités, d'objets et de paquets déjà attribués. **À consulter avant
d'ajouter quoi que ce soit.**

**Statut : livré.** Cette table est dérivée du code.

---

## 1. Pourquoi ce document existe

> Une collision d'identifiant **ne produit pas d'erreur**. Elle produit un paquet lu de
> travers, une entité qui apparaît sous la forme d'une autre, ou un objet qui devient un
> autre objet.

C'est la classe de bug la plus coûteuse du projet, parce qu'elle ne se voit pas dans un
journal : elle se voit en jeu, longtemps après, et la cause est à l'autre bout du code.

**Toute nouvelle plage se réserve ici, avant d'écrire la moindre ligne.**

---

## 2. Les paquets

Le client et le serveur se parlent par des paquets à identifiant réservé, en plus du
protocole du jeu de base. Chaque paquet est enregistré **dans les deux sens**.

| Identifiant | Paquet | Système |
|---:|---|---|
| 95 | `ScoreboardPacket` | Tableau de score |
| 96 | `BreakerPacket` | Casseurs |
| 97 | `CratePacket` | Caisses |
| 98 | `RelationPacket` | Relations |
| 99 | `TradePacket` | Échanges |
| 100 | `ProfilePacket` | Profils |
| 101 | `AuthPacket` | Authentification |
| 102 | `SelectionPacket` | Sélections |
| 103 | `CooldownPacket` | Recharges |
| 104 | `MailboxPacket` | Réserves |
| 105 | `AuctionPacket` | Hôtel des ventes |
| 106 | `ShopPacket` | Boutiques |
| 107 | `SpawnerPacket` | Générateurs |
| 108 | `TitlePacket` | Titres à l'écran |
| 109 | `MinimapPacket` | Minicarte |
| 110 | `NexusPacket` | Nexus |
| 111 | `AltarPacket` | Autels |
| 112 | `ForgeronPacket` | Forgeron |
| 113 | `ManaPacket` | Mana Brut |
| 114 | `NotificationPacket` | Notifications |
| 115 | `PowerPacket` | Pouvoirs |
| 116 | `CataclysmPacket` | Cataclysme |
| 117 | `CovenRosterPacket` | Membres du Coven |
| 118 | `ClaimBorderPacket` | Frontières de claim |
| 119 | `WarBannerPacket` | Bannière de guerre |
| 120 | `DiploUpdatePacket` | Diplomatie |
| 121 | `CovenHudPacket` | HUD de Coven |
| 122 | `EraTimerPacket` | Minuteur d'Ère |
| 123 | `EraResetPacket` | Clôture d'Ère |
| **124** | `HandshakePacket` | **Poignée de main — sans elle, le joueur est expulsé** |
| 125 | `BoutiquePacket` | Boutique |
| 126 | `WikiPacket` | Wiki en jeu |
| 127 | `CovenGuiPacket` | Interface de Coven |
| 128 | `MagicPacket` | Magie |
| 129 | `PetPacket` | Compagnons |
| 130 | `IntroPacket` | Cinématique d'arrivée |
| 131 | `QuestPacket` | Journal de quêtes |
| 132 | `MountPacket` | Montures |
| **133** | `HubPacket` | **WizardHub** — interface de navigation du lobby, *spécifié, non livré* |
| **134** | `NameplatePacket` | **Plaques de nom** — titre, nom, coven et rôle au-dessus de la tête, *spécifié, non commencé* |

### Le prochain disponible

**135.**

Les identifiants 133 et 134 sont **réservés** — à WizardHub par
[`cdc_wizardhub.md`](../90-specifications/cdc_wizardhub.md) §9.1, et aux plaques de nom par
[`cdc_nameplates.md`](../90-specifications/cdc_nameplates.md) §5. Aucun des deux n'est encore
enregistré dans le client : la réservation vaut pour qu'un autre système ne le prenne pas.

**La file d'attente ne consomme aucun identifiant de paquet** : elle vit sur le proxy et
ne parle jamais au client. C'est le lobby qui affiche son état, sur 133.

### La règle de propriété

Le paquet appartient au **greffon qui l'émet** ; le fork serveur ne fournit que
l'enregistrement. C'est ce qui permet d'ajouter un système sans modifier le fork.

---

## 3. Les entités

### La carte complète

| Plage | Usage | Combien |
|---|---|---:|
| **121–125** | Créatures hostiles : les cinq premières gelées | 5 |
| **126–149** | Bêtes paisibles | 24 |
| **150–153** | Créatures hostiles : les dragons de donjon | 4 |
| **207** | Projectile de sort | 1 |
| **208–214** | Compagnons | 7 |
| **215** | Projectile de créature | 1 |
| **216–251** | Créatures hostiles | 36 |
| **252–255** | Créatures hostiles : les dernières gelées | 4 |

### Les compagnons, en détail

| Identifiant | Compagnon |
|---:|---|
| 208 | Ignis, le Dragonnet de Braise |
| 209 | Nox, le Dragonnet d'Ébène |
| 210 | Aether |
| 211 | Aelindra *(esprit)* |
| 212 | Terra |
| 213 | Lunaris |
| 214 | Lumière |

### Ce qui reste libre

| Plage libre | Taille |
|---|---:|
| 154–206 | 53 |

**Il reste 53 identifiants d'entités.** C'est confortable, mais pas illimité : la plage
utilisable s'arrête à 255.

### Le découpage des gelées est un héritage

Les neuf gelées sont réparties sur deux plages — 121 à 125 et 252 à 255 — parce qu'elles
ont été ajoutées en deux fois. **Ce n'est pas une convention à reproduire** : une nouvelle
famille doit occuper une plage contiguë.

---

## 4. Les objets

| Plage | Usage |
|---|---|
| **680–684** | Les baguettes |
| **699–717** | Les réactifs de butin |

Le détail est dans [Catalogue des objets](catalogue-objets.md).

### La contrainte

Le jeu de base accepte des identifiants d'objets jusqu'à 32 767, mais les plages basses
sont occupées par le jeu et par les greffons tiers. **Vérifier qu'un identifiant est libre
avant de l'employer**, y compris contre les greffons installés.

---

## 5. Les blocs

| Identifiant | Usage |
|---:|---|
| 223 | Le bloc Nexus, par défaut |

Le matériau du Nexus est réglable par configuration — voir
[config.json](../05-operer/configuration/wizardcore-config-json.md).

---

## 6. Réserver un identifiant

### Pour un paquet

1. Prendre le suivant disponible : **135**.
2. L'enregistrer **dans les deux sens** dans le registre du client.
3. Le déclarer depuis le greffon qui l'émet, pas depuis le fork.
4. **Ajouter la ligne dans ce document.**

### Pour une entité

1. Prendre dans la plage libre **154–206**, en contigu si c'est une famille.
2. Laisser le générateur l'attribuer — c'est son travail, et il évite les collisions.
3. Vérifier que le générateur a bien écrit la classe serveur, la classe client, l'œuf et
   le nom traduit.
4. **Ajouter la ligne dans ce document.**

### Pour un objet

1. Vérifier qu'il est libre contre le jeu **et contre les greffons installés**.
2. L'ajouter au catalogue concerné.
3. **Ajouter la ligne dans ce document.**

---

## 7. Les collisions déjà vécues

Ce qui a été vérifié au moins une fois, et qui vaut la peine d'être su.

| Vérification | Résultat |
|---|---|
| Collisions d'identifiants d'entités | aucune |
| Collisions d'identifiants d'objets | aucune |
| Alignement du protocole client/serveur | cohérent sur toute la plage 95–132 |

Ces vérifications ont été faites en cherchant la cause d'un système qui ne répondait plus.
Elles n'ont rien trouvé — et c'est précisément ce qu'on veut pouvoir dire : **une plage
vérifiée et documentée permet d'éliminer une hypothèse en quelques minutes** plutôt qu'en
une journée.

---

## À lire ensuite

- [Architecture logicielle](../01-projet/architecture-logicielle.md) — qui possède quoi
- [Créer une créature](../06-creer-du-contenu/creer-une-creature.md) — le générateur d'entités
- [Catalogue des créatures](catalogue-creatures.md) — les 49 espèces
- [Catalogue des objets](catalogue-objets.md) — les baguettes et les réactifs
