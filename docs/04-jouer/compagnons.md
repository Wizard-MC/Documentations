# Compagnons

Les familiers : quatre espèces, un seul actif à la fois, et des sorts qui leur sont
propres. Le système qui change la sensation de jouer seul.

**Statut : livré partiellement.** Les quatre espèces existent, s'affichent, évoluent
et suivent. Seul le Dragonnet de Braise porte une barre de sorts complète ; les trois
autres attendent la leur.

---

## 1. Ce qu'un compagnon est

Une créature personnelle qui vous suit, agit de son côté, et monte en puissance avec
vous.

| | |
|---|---|
| **Combien** | Un seul actif à la fois |
| **Appel** | **K** par défaut |
| **Ce qu'il fait** | Combat, défend, ramasse, et porte des sorts |
| **Ce qu'il n'est pas** | Une monture. Les deux systèmes sont séparés. |

### Pourquoi ça compte plus que les chiffres ne le disent

C'est le jalon que les joueurs remarquent le plus. Un compagnon change la **sensation
de jouer seul** — et c'est à ce moment-là qu'un joueur sans Coven cesse de se sentir
en marge.

---

## 2. Les quatre espèces

| Compagnon | Ce qu'il est | Statut |
|---|---|---|
| **Ignis**, le Dragonnet de Braise | Niche sur les crêtes, nocturne, rare | Complet : cinq sorts |
| **Nox**, le Dragonnet d'Ébène | Son pendant d'ombre | Affichage et évolution ; pas de sorts |
| **Aelindra** *(esprit)* | Un esprit d'âmes | Affichage et évolution ; pas de sorts |
| **L'Esprit Cristallin** | Né de l'Aether, cristal et lumière | Affichage et évolution ; pas de sorts |

Le compagnon **Aelindra** n'a aucun rapport avec l'Éveilleuse des Âmes de la
cinématique d'arrivée. Le nom est partagé, la figure non — l'Éveilleuse ne revient
jamais.

---

## 3. Les sorts de compagnon

Seul Ignis en porte pour l'instant. Cinq emplacements, ouverts par paliers.

| Sort | Palier | Coût | Recharge |
|---|---:|---:|---|
| **Souffle de Feu** | 1 | 20 | 8 s |
| **Écailles de Braise** | 3 | 25 | 15 s |
| **Régénération Ardente** | 5 | 30 | 12 s |
| **Détection de Minerai** | 7 | 15 | 30 s |
| **Cataclysme** | 10 | 50 | 60 s |

### Comment les lire

Les cinq sorts couvrent quatre usages différents, et c'est le modèle à suivre pour
les trois autres compagnons :

| Usage | Le sort |
|---|---|
| Attaquer | Souffle de Feu |
| Se protéger | Écailles de Braise |
| Se soigner | Régénération Ardente |
| Rendre service hors combat | Détection de Minerai |
| Le coup d'éclat | Cataclysme |

La **Détection de Minerai** est la plus intéressante du lot : c'est le seul sort de
compagnon qui ne sert pas au combat. Un compagnon qui n'aurait que des sorts de combat
ne serait qu'une arme de plus.

### La barre de sorts de compagnon

Elle s'affiche à l'écran quand le compagnon est actif, avec les recharges. Elle se
règle dans les options du client.

---

## 4. L'évolution

Un compagnon progresse par **stades**. Chaque stade peut changer :

| Ce qui change | |
|---|---|
| Son niveau d'évolution | Ce qui ouvre les emplacements de sorts |
| Son échelle | Il grandit |
| Son modèle | Certains stades ont une apparence propre |

Le passage de stade est annoncé. Si le stade n'a pas de nom déclaré, le message dit
simplement le seuil atteint.

### Ce qui fait monter un compagnon

| Source | Comment |
|---|---|
| **Collecte** | Il ramasse pendant que vous êtes à côté |
| **Combat** | Vous tuez une créature près de lui |
| **Défense** | Il vous défend |
| **Événements** | Ce qu'un événement accorde |
| **Friandises** | Vous lui en donnez une |

Les quatre premières sont passives : elles arrivent en jouant. La cinquième est le
seul levier direct.

### Les friandises

Quatre friandises, à fabriquer avec les réactifs laissés par les créatures. Clic droit,
compagnon invoqué.

| Friandise | XP | Ce qu'il faut |
|---|---:|---|
| **Friandise du Familier** | 40 | 2 crocs de gobelin, sucre, blé — donne 2 friandises |
| **Bouchée Venimeuse** | 80 | 1 glande à venin, 2 sucres |
| **Braise Confite** | 150 | 2 poudres de blaze, 1 crème de magma, 2 pépites d'or |
| **Festin du Dragon** | 400 | 4 lingots d'or, 2 émeraudes, 2 poudres de blaze |

**Un plafond de 500 XP par jour et par joueur.** Au-delà, la friandise rend ce qu'il
reste sous le plafond plutôt que d'être refusée — le message dit alors combien elle a
vraiment donné. C'est un coup de pouce, pas un raccourci : sans plafond, un stock de
friandises ferait sauter quarante niveaux en une soirée, et les paliers qui ouvrent les
emplacements de sorts ne voudraient plus rien dire.

Le plafond se règle dans `config.yml`, à `progression.treats.dailyXpCap`. À zéro, il
n'y a plus de plafond.

---

## 5. Les accessoires

Sept accessoires, un par effet, répartis sur cinq emplacements. Ils tombent des
créatures — jamais de la boutique, puisqu'ils donnent des bonus.

| Accessoire | Emplacement | Effet | Où le trouver |
|---|---|---|---|
| **Amulette du Sorcier** | Cou | +10 % régénération de mana | Gobelin mage, Gelée arcanique, Sylphe éplorée |
| **Cristal de Vitesse** | Corps | +10 % vitesse | Loup maudit, Araignée d'ombre, Nyx |
| **Cape de l'Ombre** | Dos | +10 % esquive (PvE) | Cryptique, Nyx, Araignée d'ombre |
| **Couronne de Feu** | Tête | +10 % dégâts de feu (PvE) | Dragon Aile-de-braise, Gelée de lave |
| **Selle de Braise** | Corps | +15 % vitesse monture, +10 % stamina de vol | Drake Terravore, Seigneur-Tonnerre céleste |
| **Bottes de Braise** | Jambes | +10 % rayon de collecte | Golem de mousse, Écorce flétrie, Mousse malveillante |
| **Plume de Vent** | Dos | +15 % stamina de vol | Vouivre Chuchevent, Araignée volante |

**Une mise à mort ne rend jamais deux accessoires.** Quand une créature figure dans
deux tables, les chances se partagent un seul tirage — sans quoi le pourcentage affiché
pour chacun ne voudrait pas dire ce qu'il dit.

Les bonus sont doux et hors PvP : c'est le principe
[aucun avantage payant](../01-projet/vision-et-positionnement.md#21-aucun-avantage-payant)
appliqué aussi à ce qui se gagne.

---

## 6. Les dragonnets sauvages

Les deux dragonnets existent aussi **à l'état sauvage**, et ce sont alors des
adversaires.

| | Compagnon | Sauvage |
|---|---|---|
| Boîte de collision | 0,8 à 0,9 bloc | **6 blocs de large** |
| Taille du modèle | Réduite | Un dragon adolescent : six blocs de long |
| Comportement | Vous suit | Vous attaque |

Un dragonnet sauvage apprivoisé **rétrécit**. C'est une contrainte de jeu assumée :
un compagnon de six blocs collé au joueur traverserait le décor et ne passerait aucune
porte.

Relâcher un compagnon lui rend sa taille sauvage.

### Où les trouver

Le Dragonnet de Braise niche sur les crêtes du **Désert de Cristal** et des
**Montagnes du Crépuscule**, de nuit, à ciel ouvert, et il est rare. Le Dragonnet
d'Ébène est du côté de la **Forêt d'Ébène**.

---

## 7. Ce que la boutique peut et ne peut pas

| Autorisé | Interdit |
|---|---|
| Des apparences de compagnon | Toute statistique de combat |
| Des cosmétiques | Tout sort |
| — | Tout stade d'évolution |

Un compagnon acheté ne se bat pas mieux qu'un compagnon gagné. C'est le principe
[aucun avantage payant](../01-projet/vision-et-positionnement.md#21-aucun-avantage-payant),
et il n'a pas d'exception.

---

## 8. Ce qui n'existe pas encore

Dit franchement, pour que personne ne le cherche.

| Absent | Conséquence |
|---|---|
| Les sorts de Nox, Aelindra et l'Esprit Cristallin | Trois compagnons sur quatre sont décoratifs en combat |
| Un inventaire de compagnon | Il ne porte rien pour vous |
| Des ordres détaillés | On l'appelle et on le renvoie ; le reste est à son initiative |
| Des rôles de compagnon alignés sur le Coven | Prévu dans la spécification historique, non livré |

Le cadre de conception initial est dans
[cdc_compagnons](../90-specifications/cdc_compagnons.md). Il prévoyait des
compagnons de rôle — construction, commerce, guerre — qui n'ont pas été retenus en
faveur des quatre espèces actuelles.

---

## 9. Les pièges

| Piège | Ce qui se passe | Quoi faire |
|---|---|---|
| Attendre des sorts d'un autre compagnon qu'Ignis | Il n'en a pas | Prendre Ignis pour le combat |
| Confondre compagnon et monture | Deux systèmes, deux touches | **K** pour le compagnon, **M** pour la monture |
| S'approcher d'un dragonnet sauvage comme d'un compagnon | Six blocs de large, et hostile | Le traiter comme un boss |
| Croire que le compagnon Aelindra est l'Éveilleuse | Ce sont deux choses différentes | L'Éveilleuse ne revient jamais |
| Acheter une apparence en espérant un gain | Les cosmétiques ne donnent rien | C'est le principe |
| Enchaîner les friandises | Au-delà de 500 XP dans la journée, elles ne rendent presque plus rien | En garder pour demain |
| Manger la friandise soi-même | Impossible : le clic droit est intercepté | — |
| Attendre un accessoire de la boutique | Ils donnent des bonus, donc ils ne s'achètent pas | Tuer la créature qui le laisse |

---

## À lire ensuite

- [Panthéon et figures](../02-univers/pantheon-et-figures.md#6-les-compagnons) — ce qu'ils sont dans l'univers
- [Quêtes et montures](quetes-et-montures.md) — le système voisin, à ne pas confondre
- [Interface et client](interface-client.md) — régler la barre de sorts
- [cdc_compagnons](../90-specifications/cdc_compagnons.md) — le cadre historique
