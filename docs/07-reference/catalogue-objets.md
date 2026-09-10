# Catalogue des objets

Les baguettes, les fioles, les matières et les cosmétiques.

**Statut : livré.** Cette table est dérivée des catalogues.

---

## 1. Les baguettes

Treize baguettes, quatre paliers. Une baguette fait trois choses : elle permet de lancer,
son palier ouvre des sorts, son affinité réduit coût et recharge.

| Identifiant | Nom | Palier | Affinité | Coût | Recharge |
|---|---|---:|---|---:|---:|
| `fracture_oak` | Baguette de Chêne Fracturé | 1 | — | ×1.0 | ×1.0 |
| `ember_ash` | Baguette de Cendre Vive | 2 | Braises | ×0.9 | ×0.9 |
| `frost_pine` | Baguette de Pin Givré | 2 | Givre | ×0.9 | ×0.9 |
| `grove_willow` | Baguette de Saule Sylvain | 2 | Roc | ×0.9 | ×0.9 |
| `stonebinder` | Baguette de Liant-Pierre | 2 | Roc | ×0.9 | ×0.9 |
| `arcane_crystal` | Baguette de Cristal d'Arcane | 3 | Arcane | ×0.85 | ×0.85 |
| `aurora_birch` | Baguette de Bouleau d'Aurore | 3 | Aurore | ×0.85 | ×0.85 |
| `bloodiron` | Baguette de Fer-Sang | 3 | Ombre | ×0.85 | ×0.85 |
| `chronoglass` | Baguette de Verre-Chronos | 3 | Vide | ×0.85 | ×0.85 |
| `echo_alder` | Baguette d'Aulne d'Écho | 3 | Esprit | ×0.85 | ×0.85 |
| `shadowthorn` | Baguette d'Épine d'Ombre | 3 | Ombre | ×0.85 | ×0.85 |
| `stormglass` | Baguette de Verre-Tempête | 3 | Tempête | ×0.85 | ×0.85 |
| `fracture_prime` | Baguette Fractale Prime | 4 | — | ×0.8 | ×0.8 |

### Ce qu'il faut lire dans cette table

**Deux baguettes peuvent partager une affinité.** Le Saule Sylvain et le Liant-Pierre ont
la même affinité Roc, le même palier et les mêmes facteurs : ce sont des **variantes à
collectionner**, pas des paliers de puissance.

**Deux baguettes portent le nom d'une tradition absorbée.** Le Fer-Sang (tradition
Sanguine, affinité Ombre) et le Verre-Chronos (tradition Chronienne, affinité Vide). C'est
de la mémoire ; aucune règle de jeu ne connaît les traditions.

**La baguette de départ n'a aucune affinité.** Le Chêne Fracturé est volontairement neutre :
un débutant ne doit pas être poussé vers une école avant d'avoir essayé les autres.

### La fabrication

Chaque palier se fabrique **à partir du précédent**. Le prix d'une baguette de palier 4
contient donc tous les paliers inférieurs, et on ne peut pas échanger un palier contre un
autre.

Le réactif commun est l'Essence Arcane sous forme d'objet.

---

## 2. Les fioles de régénération

Quatre recettes, une par palier, chacune à partir de la précédente.

| Palier | Ce qu'elle demande |
|---|---|
| **Standard** | 4 Essence Arcane + 1 fiole en verre |
| **Supérieure** | 4 poudres de glowstone + 4 Essence Arcane + 1 Standard |
| **Épique** | 4 Essence Arcane + 2 poudres de blaze + 2 diamants + 1 Supérieure |
| **Légendaire** | 1 étoile du Nether + 2 larmes de Ghast + 3 Essence Arcane + 1 Épique |

### Pourquoi elles existent

La régénération d'Essence est lente — quatre points toutes les dix secondes hors combat,
**un seul en combat**. Les fioles sont la seule façon de revenir dans un échange.

C'est aussi la raison pour laquelle la régénération a été baissée deux fois : trop rapide,
elle rendait les fioles inutiles, et personne n'allait dépenser une étoile du Nether pour
gagner une minute d'attente.

---

## 3. Les Tomes

Un Tome enseigne un sort qui ne s'apprend pas autrement. **Dix-neuf sorts** sont dans ce
cas.

| | |
|---|---|
| **Où ils tombent** | Les boss et les occurrences, uniquement |
| **Ce qu'ils valent** | Les objets les plus chers du jeu à l'hôtel des ventes |
| **Pourquoi** | Ils sont la seule chose qu'on ne peut pas obtenir en jouant seul patiemment |

---

## 4. Les matières de butin

Chaque espèce a sa table. Les matières notables :

| Matière | D'où elle vient | À quoi elle sert |
|---|---|---|
| Croc de gobelin | Les gobelins | Forge |
| Noyau en fusion | La Gelée de lave | Forge, et la quête annexe *Écailles et cendres* |
| Membrane de bourrasque | La Vouivre Chuchevent | Forge |
| Clé de trésor | Les boss | Ouvrir un trésor |
| Totem de bande | La quête *Ceux qui mènent* | Objet de quête |

### Ce que le noyau en fusion enseigne

C'est le seul butin du jeu qui est **l'objet d'une quête**. Une matière qui a un usage
nommé donne une raison d'aller chercher une espèce précise — c'est ce qui distingue un
butin d'un ramassage.

---

## 5. Les objets de compagnon

Deux familles, ni l'une ni l'autre achetable : elles agissent sur les statistiques d'un
familier, ce que la boutique n'a pas le droit de vendre.

### Les accessoires

Sept, sur cinq emplacements. Ils **tombent des créatures**, par famille cohérente — la
Couronne de Feu de ce qui brûle, la Plume de Vent de ce qui vole. Le détail des espèces
est dans [Compagnons §5](../04-jouer/compagnons.md#5-les-accessoires).

### Les friandises

Quatre, à fabriquer avec les matières de butin ci-dessus. Elles donnent de l'XP au
compagnon actif, sous un plafond de 500 XP par joueur et par jour.

| Friandise | XP | Coût |
|---|---:|---|
| Friandise du Familier | 40 | 2 crocs de gobelin, sucre, blé *(rend 2 friandises)* |
| Bouchée Venimeuse | 80 | 1 glande à venin, 2 sucres |
| Braise Confite | 150 | 2 poudres de blaze, 1 crème de magma, 2 pépites d'or |
| Festin du Dragon | 400 | 4 lingots d'or, 2 émeraudes, 2 poudres de blaze |

### Ni l'un ni l'autre n'a d'identifiant propre

Accessoires et friandises empruntent des matériaux du jeu de base — une pépite d'or, une
plume, un cookie — et se distinguent par un marqueur écrit dans leur description. Un
identifiant neuf aurait demandé une texture côté client et une nouvelle version du client
sur le nuage, pour des objets que personne ne regarde plus de deux secondes.

C'est aussi ce qui rend leur ajout **sans effet sur les plages d'identifiants** : voir
[Plages d'identifiants](plages-d-identifiants.md#4-les-objets).

---

## 6. Les cosmétiques

Vingt entrées, six catégories. Aucune ne donne d'avantage.

| Catégorie | Combien | Ce que c'est |
|---|---:|---|
| Apparences de baguette | 7 | L'aspect, sans toucher aux statistiques |
| Auras | 4 | Un effet permanent autour du personnage |
| Titres | 3 | Affichés avec le nom |
| Apparences de compagnon | 3 | L'aspect du familier |
| Effets de mort | 2 | Ce qui se passe quand on meurt |
| Traces | 1 | Ce qu'on laisse derrière soi |

Ils s'achètent en **Gemmes** ou en **Poussière d'Étoile** — l'une avec de l'argent, l'autre
avec du temps, et aucune des deux ne donne de puissance.

---

## 7. Les utilitaires

| Utilitaire | Gemmes | Poussière | Plafond | Recharge |
|---|---:|---:|---:|---:|
| Slot de `/home` | 50 | — | 5 | — |
| `/workbench` permanent | 100 | — | — | 300 s |
| `/enderchest` permanent | 100 | — | — | 300 s |
| Emplacement de preset de sorts | 60 | 4000 | 3 | — |

### Le cas limite

L'**emplacement de preset** est celui qui mérite l'explication : il n'accorde ni sort, ni
niveau, ni statistique. Le joueur pouvait déjà composer cette barre à la main.

> **Enregistrer ce qu'on peut déjà faire** est du confort. **Pouvoir faire plus** serait un
> avantage.

Un « slot de claim » payant serait refusé : il donne du territoire, donc de la puissance.

---

## 8. Le Mana Brut

Un objet, pas une monnaie abstraite.

| | |
|---|---|
| Pile | jusqu'à 64 |
| **Interdit dans** | les coffres, les fours, les entonnoirs |
| À la mort | la moitié tombe au sol, **la moitié est détruite** |
| Le porteur | est signalé par une pastille visible |

L'interdiction de stockage est la règle centrale : **soit il est au stock virtuel, soit il
est sur quelqu'un.** Il n'y a pas de troisième état, donc pas de moyen d'éviter le risque.

---

## À lire ensuite

- [La magie](../04-jouer/magie.md) — les baguettes et les fioles en jeu
- [Boutique et Pass d'Ère](../04-jouer/boutique-et-pass.md) — ce qui s'achète
- [Autels et Mana Brut](../04-jouer/autels-et-mana.md) — le Mana Brut
- [Catalogue des sorts](catalogue-sorts.md) — les 41 sorts

