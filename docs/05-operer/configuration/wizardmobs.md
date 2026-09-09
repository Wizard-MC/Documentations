# Configuration de WizardMobs

Les trois fichiers du catalogue des créatures, champ par champ. Référence pour tout
ajout ou réglage de créature, de bête ou de monture.

**Statut : livré.** 49 créatures hostiles, 24 bêtes, 10 montures.

---

## 1. Les trois fichiers

| Fichier | Ce qu'il porte |
|---|---|
| `mobs.yml` | Les 49 créatures hostiles, leur IA, leurs attaques, leur butin, leur apparition |
| `beasts.yml` | Les 24 bêtes paisibles et leurs apparences |
| `mounts.yml` | Les 10 montures |

### Le principe qui les gouverne

> Le serveur **ne lit pas** ces fichiers. Il reçoit des valeurs déjà arbitrées.

Ce sont des fichiers de greffon : WizardMobs les lit, en fait des options
d'apparition, et les passe au serveur. Cela veut dire qu'une valeur absurde ne fait
pas planter le serveur — elle produit une créature absurde.

---

## 2. `mobs.yml` — les créatures hostiles

### 2.1. La section `regions`

Ce que chaque région ajoute au niveau des créatures qui y naissent.

| Champ | Ce qu'il règle | Valeur livrée |
|---|---|---|
| `default` | Le décalage par défaut | 0 |
| `hostility.SANCTUAIRE` | Le spawn | −8 |
| `hostility.PLAINE_DES_MURMURES` | La zone de départ | −4 |
| `hostility.DESERT_DE_CRISTAL` | — | +4 |
| `hostility.FORET_D_EBENE` | — | +8 |
| `hostility.MONTAGNES_DU_CREPUSCULE` | — | +14 |
| `hostility.WILDERNESS` | Tout le reste | +6 |

> **C'est ici que se règle la difficulté du serveur**, et nulle part ailleurs.

Aucune espèce n'est réservée à une région : ce qui change, c'est le niveau. Un joueur
apprend à lire la carte comme une échelle de danger.

Le choix inverse — réserver les grosses espèces aux zones dures — aurait donné un
catalogue par région, donc un contenu vu une fois puis abandonné. Là, on revoit les
mêmes créatures toute l'Ère, et ce sont elles qui montent.

Sans WorldGuard, ce découpage est ignoré.

### 2.2. Une espèce

| Champ | Ce qu'il règle | Obligatoire |
|---|---|---|
| `name` | Le nom affiché, avec ses codes de couleur | oui |
| `model` | Le chemin du bbmodel, sans extension | **oui** |
| `temperament` | Comment elle se bat — voir ci-dessous | oui |
| `health` | Points de vie de base | oui |
| `damage` | Dégâts de base | oui |
| `speed` | Vitesse de déplacement | oui |
| `perception` | Distance à laquelle elle remarque une cible | oui |
| `cohesion` | Distance à laquelle elle reste avec son groupe | non |
| `swarm` | Seuil de nuée | non |
| `width`, `height` | La boîte de collision, en blocs | oui |
| `levelOffset` | Décalage de niveau propre à l'espèce | non |
| `levelSpread` | Dispersion aléatoire du niveau | non |
| `experience` | Expérience lâchée | oui |
| `elite` | Nom affiché en permanence, niveau rehaussé | non |
| `leash` | Distance maximale depuis son point d'apparition | non |
| `gait` | `WALK` ou `HOP` | non |
| `hopTicks` | Ticks entre deux bonds, si `HOP` | non |
| `attacks` | Ses attaques | oui |
| `loot` | Sa table de butin | non |
| `spawn` | Ses conditions d'apparition | non |
| `flight` | Son vol, si elle vole | non |

### 2.3. Les tempéraments

| Tempérament | Comportement |
|---|---|
| `BRUTE` | Avance droit, encaisse, frappe fort |
| `SKIRMISHER` | Harcèle, recule, revient |
| `RANGED` | Tient ses distances et tire |
| `CASTER` | Lance, se replie quand on l'approche |
| `AMBUSHER` | Attend, surgit |
| `STALKER` | Suit à distance avant d'engager |
| `PACK_HUNTER` | Ne vaut qu'en groupe |
| `SENTINEL` | Garde un point, ne poursuit pas loin |
| `DEFENSIVE` | Riposte, n'initie pas |

### 2.4. La boîte de collision

`width` et `height`, en blocs. La boîte est carrée en plan : `width` est à la fois la
largeur et la profondeur.

> **La bonne source est le cube `hitbox` du bbmodel**, quand le modeleur en a dessiné
> un qui n'est pas un simple gabarit carré.

Un cube aussi large que haut n'est pas une boîte de collision : c'est le gabarit de
proportions que propose Blockbench, et l'appliquer rendrait un gobelin aussi large que
grand. Voir [Pipeline Blockbench](../../06-creer-du-contenu/pipeline-bbmodel.md).

### 2.5. La démarche

| Champ | Valeurs | Défaut |
|---|---|---|
| `gait` | `WALK` ou `HOP` | `WALK` |
| `hopTicks` | 5 à 120 | 20 |

Une créature en `HOP` ne pousse que pendant la détente : l'élan horizontal est donné
en une fois, puis elle retombe. **Entre deux bonds elle n'avance pas** — et c'est ce
silence qui fait la démarche, autant que le saut.

> **`hopTicks` doit valoir la durée du clip de bond de l'espèce, en ticks.** Le clip
> joue alors exactement une fois par saut, et l'écrasement à la réception tombe quand
> la créature touche le sol.

Les huit gelées bondissantes sont réglées à 35, 30 ou 29 ticks selon la durée de leur
clip `walk`. Un contrôle automatisé vérifie que ces valeurs correspondent toujours aux
fichiers bbmodel : une valeur saisie à la main dérive dès que le modeleur retouche le
clip, et rien ne le dit avant de voir la gelée retomber au milieu de son écrasement.

Le Roi des gelées garde `WALK` : son modèle n'a aucun clip de déplacement — son
`dash` est déclaré comme une attaque — et le client se rabat donc sur son repos. Le
faire bondir donnerait un cube qui saute en restant figé.

### 2.6. Une attaque

| Champ | Ce qu'il règle | Obligatoire |
|---|---|---|
| `clip` | Le nom du clip du bbmodel | **oui** |
| `kind` | Le type — voir ci-dessous | oui |
| `windup` | Ticks de montée, avant les dégâts | oui |
| `duration` | Ticks de durée totale | oui |
| `cooldown` | Ticks de recharge | oui |
| `maxRange` | Portée maximale, **de surface à surface** | oui |
| `minRange` | Portée minimale | non |
| `damage` | Dégâts | oui |
| `priority` | Poids dans le choix de l'attaque | non |
| `stance` | `AIR` pour une attaque qui ne se fait qu'en vol | non |
| `projectile` | Le projectile, pour une attaque à distance | non |
| `summon` | Ce qui est invoqué | non |
| `struck`, `struckTicks` | Ce qui est joué sur la cible touchée | non |
| `impact`, `impactTicks` | L'effet à l'impact | non |

### Les huit types d'attaque

| Type | Combien en usage | Ce que c'est |
|---|---:|---|
| `MELEE` | 49 | Au contact |
| `AREA` | 27 | En aire autour de la créature |
| `LUNGE` | 17 | Une charge sur une distance |
| `DEFENSIVE` | 9 | Une posture qui réduit les dégâts reçus |
| `RANGED` | 8 | Un projectile |
| `SUMMON` | 3 | Fait venir du renfort |
| `BUFF` | 3 | Renforce les alliés |
| `ARROW` | 1 | Une flèche ordinaire |

### La règle du `clip`

> **Le nom du clip n'est pas choisi ici — c'est celui du bbmodel.**

Un clip mal orthographié donne une **attaque sans geste** : les dégâts tombent, le
joueur ne voit rien venir, et l'esquive devient impossible à apprendre.

C'est l'erreur la plus coûteuse qu'on puisse faire dans ce fichier, et c'est pour ça
qu'un contrôle automatisé vérifie chaque nom de clip contre les fichiers livrés.

### La règle du `maxRange`

> **La portée se mesure de surface à surface**, pas de centre à centre.

Cette distinction a coûté une conversion des 116 valeurs du catalogue. Mesurée de
centre à centre, la demi-largeur d'une grosse créature comptait dans la distance : un
dragon de cinq blocs de large atteignait un joueur qu'il ne touchait pas.

Passer à la mesure de surface **sans convertir** aurait rendu les grosses créatures
plus généreuses encore, leur demi-largeur passant de « comptée dedans » à « bonus en
plus ». Les 116 valeurs ont donc été converties pour que chaque attaque perde
exactement le même bloc et qu'aucune n'y gagne.

**Un `maxRange` ajouté aujourd'hui se raisonne en distance entre les deux boîtes.**

### 2.7. Le butin

| Champ | Ce qu'il règle |
|---|---|
| `perLevelBonus` | Ce que chaque niveau ajoute à la chance de chute |
| `drops` | La liste, chaque entrée portant `item`, `min`, `max` et `chance` |

Les `chance` sont des probabilités entre 0 et 1.

### 2.8. L'apparition

| Champ | Ce qu'il règle |
|---|---|
| `weight` | Le poids dans le tirage. **`0` ou pas de section : l'espèce n'apparaît jamais d'elle-même.** |
| `biomes` | La liste des biomes acceptés |
| `sky` | `REQUIRED` pour exiger le ciel ouvert |
| `minLight`, `maxLight` | Les niveaux de lumière acceptés |
| `minY`, `maxY` | L'altitude |
| `packMin`, `packMax` | La taille du groupe |
| `minSchoolLevel` | Le niveau d'école minimal d'un joueur proche |

### `weight: 0` — l'usage le plus important

Neuf entrées ont un poids nul : les quatre dragons de donjon et les trois coffres
leurres.

> **Un boss croisé au détour d'une plaine n'en serait plus un.** Pour un boss, le
> poids nul est presque toujours le bon choix : seul un donjon, une occurrence ou une
> commande le fait venir.

### `minSchoolLevel`

C'est le garde-fou de progression. Les boss naturels exigent de 30 à 45 ; les espèces
de départ exigent 0.

Il ne décide pas du niveau de la créature, mais de la **présence d'un joueur capable**
à proximité.

### 2.9. Le vol

| Champ | Ce qu'il règle |
|---|---|
| `hoverHeight` | L'altitude de vol stationnaire |
| `speed` | La vitesse en vol |
| `takeoffRange` | La distance à laquelle elle décolle |
| `landRange` | La distance à laquelle elle se pose |
| `ceiling` | Le plafond absolu |
| `lowPass.height`, `.overshoot`, `.every`, `.duration` | La passe rasante |
| `death.falling`, `.impact`, `.impactTicks` | La mort en vol, en trois temps |

### Les trois règles du vol

1. **Elle se pose pour frapper.** `landRange` n'est pas optionnel : un dragon qui
   resterait en l'air serait invincible pour un joueur au sol.
2. **Elle a besoin de clips de vol.** Le Drake Terravore reste au sol précisément
   parce que son modèle n'en a aucun — le faire décoller l'aurait fait glisser dans les
   airs en marchant.
3. **Le butin tombe au point d'impact.** Sinon, tuer un dragon à trente blocs de
   hauteur donnerait un butin inatteignable.

---

## 3. `beasts.yml` — les bêtes paisibles

| Champ | Ce qu'il règle |
|---|---|
| `name` | Le nom affiché |
| `disposition` | `CALM`, `SKITTISH` ou `DEFENSIVE` |
| `health`, `speed` | — |
| `width`, `height` | La boîte de collision |
| `experience` | — |
| `petClip` | Le clip joué quand on la caresse |
| `sounds` | Le jeu de sons |
| `models` | Les apparences possibles, tirées au sort |
| `rideable` | Si elle peut porter un cavalier |

### Une bête sauvage tire son apparence au sort

C'est la différence essentielle avec une monture. Un ours sauvage peut être brun ou
blanc ; une monture a une apparence **fixe**.

### Les boîtes des bêtes montables

Trois d'entre elles ont été alignées sur le cube `hitbox` de leur bbmodel : le husky
(1,4 × 1,9), l'axolotl (1,3 × 2,1) et le griffon (2,5 × 3,1).

Les autres gardent leur valeur parce que leur cube est un **gabarit carré** — 1,75 ×
1,75 pour les gobelins, 2,19 × 2,19 pour le yak — et qu'un gabarit n'est pas une boîte
de collision.

---

## 4. `mounts.yml` — les montures

| Champ | Ce qu'il règle | Obligatoire |
|---|---|---|
| `name` | Le nom affiché | oui |
| `species` | L'espèce de `beasts.yml` | **oui** |
| `model` | L'apparence **fixe**, parmi celles de l'espèce | non |
| `rarity` | `COMMUNE`, `RARE`, `EPIQUE` ou `LEGENDAIRE` | oui |
| `speed` | Entre 0,20 et 0,85 | oui |
| `flying` | Si elle vole | non |
| `unlock` | La clé qu'une quête accorde. Défaut : `mount.<id>` | non |
| `description` | Le texte | non |

### La rareté ne change rien à ce que la monture fait

Elle dit ce que la monture a **coûté à obtenir**, et décide de sa couleur.

La vitesse et le vol sont écrits monture par monture. Lier la vitesse à la rareté
aurait retiré le seul moyen de faire un yak de trait ou un crabe pesant.

### Les bornes de vitesse

Entre 0,20 et 0,85, et ce n'est pas arbitraire :

| Sous 0,20 | Au-dessus de 0,85 |
|---|---|
| On dépasse sa monture à pied | On dépasse le terrain que le serveur charge devant soi : on traverse des morceaux de monde vides, et l'anti-triche renvoie en arrière |

### Le modèle est fixe

> Un joueur qui a mérité l'ours blanc n'accepterait pas d'en recevoir un gris un jour
> sur quatre.

C'est pourquoi l'Ours brun et l'Ours des glaces sont **deux montures** de la même
espèce, et non une monture à deux apparences.

### Le déblocage

`unlock` vaut `mount.<id>` par défaut, ce qui évite de l'écrire deux fois et de se
tromper une fois sur deux.

**Sans WizardQuest, tout est ouvert.** C'est un comportement de repli, pas une
intention : un serveur sans quêtes ne doit pas priver ses joueurs de montures.

---

## 5. Avant de changer quoi que ce soit

1. **Les boîtes de collision viennent du bbmodel**, pas de l'intuition. Voir
   [Pipeline Blockbench](../../06-creer-du-contenu/pipeline-bbmodel.md).
2. **Les noms de clips viennent du bbmodel.** Un contrôle les vérifie ; ne pas le
   contourner.
3. **`maxRange` se raisonne de surface à surface.**
4. **`hopTicks` doit valoir la durée du clip.** Un contrôle le vérifie aussi.
5. **Un boss se déclare à `weight: 0`.**
6. **Écrire la raison** d'une valeur, dans le fichier.

La procédure complète d'ajout est dans
[Créer une créature](../../06-creer-du-contenu/creer-une-creature.md).

---

## À lire ensuite

- [Créer une créature](../../06-creer-du-contenu/creer-une-creature.md) — la chaîne complète
- [Catalogue des créatures](../../07-reference/catalogue-creatures.md) — les valeurs livrées
- [cdc_mobs](../../90-specifications/cdc_mobs.md) — la spécification
- [Pipeline Blockbench](../../06-creer-du-contenu/pipeline-bbmodel.md) — les conventions du modèle
