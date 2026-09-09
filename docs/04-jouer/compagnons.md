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

---

## 5. Les dragonnets sauvages

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

## 6. Ce que la boutique peut et ne peut pas

| Autorisé | Interdit |
|---|---|
| Des apparences de compagnon | Toute statistique de combat |
| Des cosmétiques | Tout sort |
| — | Tout stade d'évolution |

Un compagnon acheté ne se bat pas mieux qu'un compagnon gagné. C'est le principe
[aucun avantage payant](../01-projet/vision-et-positionnement.md#21-aucun-avantage-payant),
et il n'a pas d'exception.

---

## 7. Ce qui n'existe pas encore

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

## 8. Les pièges

| Piège | Ce qui se passe | Quoi faire |
|---|---|---|
| Attendre des sorts d'un autre compagnon qu'Ignis | Il n'en a pas | Prendre Ignis pour le combat |
| Confondre compagnon et monture | Deux systèmes, deux touches | **K** pour le compagnon, **M** pour la monture |
| S'approcher d'un dragonnet sauvage comme d'un compagnon | Six blocs de large, et hostile | Le traiter comme un boss |
| Croire que le compagnon Aelindra est l'Éveilleuse | Ce sont deux choses différentes | L'Éveilleuse ne revient jamais |
| Acheter une apparence en espérant un gain | Les cosmétiques ne donnent rien | C'est le principe |

---

## À lire ensuite

- [Panthéon et figures](../02-univers/pantheon-et-figures.md#6-les-compagnons) — ce qu'ils sont dans l'univers
- [Quêtes et montures](quetes-et-montures.md) — le système voisin, à ne pas confondre
- [Interface et client](interface-client.md) — régler la barre de sorts
- [cdc_compagnons](../90-specifications/cdc_compagnons.md) — le cadre historique
