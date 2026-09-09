# Géographie des Terres Fracturées

Les régions du monde, leur ambiance, ce qu'on y trouve et ce qu'on y risque.
Référence pour les bâtisseurs, les rédacteurs de quêtes, et tout joueur qui veut
savoir où aller.

**Statut : livré partiellement.** Les six régions existent comme découpage de
règles (décalage d'hostilité, placement des Autels, points du Forgeron). Il n'y a
pas de génération de carte sur mesure : la géographie est bâtie à la main par
dessus un monde Minecraft ordinaire.

---

## 1. La règle qui gouverne tout

**Aucune créature n'est réservée à une région.** Les quarante-deux espèces qui
apparaissent d'elles-mêmes peuvent naître n'importe où.

Ce qui change d'une région à l'autre, c'est le **niveau** de ce qu'on y rencontre.
Chaque région porte un décalage d'hostilité, qui relève ou abaisse le niveau des
créatures qui y naissent.

| Région | Décalage | Ce que ça veut dire |
|---|---:|---|
| **Sanctuaire** (spawn) | −8 | On y survit même sans rien comprendre |
| **Plaine des Murmures** | −4 | La zone de départ. On y apprend sans être puni. |
| **Désert de Cristal** | +4 | Déjà sérieux |
| **Forêt d'Ébène** | +8 | On n'y va pas seul |
| **Montagnes du Crépuscule** | +14 | Contenu de fin d'Ère |
| **Wilderness** (le reste) | +6 | Par défaut, hostile |

Ce choix est structurant et mérite d'être compris : **la difficulté est
géographique, pas thématique**. Un gobelin de la Plaine et un gobelin des
Montagnes sont la même espèce, avec vingt-deux niveaux d'écart. Un joueur apprend à
lire la carte comme une échelle de danger, et il peut décider d'aller trop loin.

L'alternative — réserver les grosses espèces aux zones dures — aurait donné un
monde où chaque région a son catalogue, donc un contenu vu une fois puis
abandonné. Là, on revoit les mêmes créatures toute l'Ère, et ce sont elles qui
montent avec le joueur.

Le niveau final dépend aussi de la puissance du joueur le plus proche. Voir
[cdc_mobs](../90-specifications/cdc_mobs.md) §4.7.

---

## 2. Les six régions

### 2.1. Le Sanctuaire

| | |
|---|---|
| **Rôle** | Spawn. Safezone : pas de PvP, pas de casse. |
| **Hostilité** | −8 |
| **Ce qu'on y trouve** | Le Panthéon, les personnages de lore, l'apprentissage du Coven |
| **Ce qu'on n'y trouve pas** | De ressource. On ne s'y installe pas. |

Le point d'arrivée de tout le monde, et le seul endroit du jeu où rien ne peut
arriver. Il n'est **pas** le Sanctuaire des Origines — celui-là est hors du monde
et on n'y retourne pas. Le nom est volontairement proche : le spawn est ce qui
reste du Sanctuaire dans les Terres.

C'est aussi là que se trouve le Panthéon, donc le seul lieu qui garde la mémoire
des Ères passées.

### 2.2. La Plaine des Murmures

| | |
|---|---|
| **Rôle** | Zone de départ. C'est là que les premiers Covens s'installent. |
| **Hostilité** | −4 |
| **Autels** | L'*Autel des Murmures* (Nature) et l'*Autel Central* (Nature) |
| **Ambiance** | Ouverte, praticable, vide. On voit loin, donc on voit venir. |

Le nom vient de ce que la nappe y affleure assez pour qu'on l'entende sans la
chercher — des murmures, rien de plus.

En termes de jeu, c'est la zone franchissable du principe de conception : un
joueur qui débute doit pouvoir traverser, se tromper, et rentrer. Le décalage
négatif est là pour ça, et il ne doit pas être relevé sans changer autre chose.

### 2.3. Le Désert de Cristal

| | |
|---|---|
| **Rôle** | Routes du Forgeron. Première zone qui demande de l'équipement. |
| **Hostilité** | +4 |
| **Autels** | L'*Autel de Cristal* (Glace), l'*Autel des Braises* (Feu) |
| **Ambiance** | Étendues sèches, cristallisations. La saignée a durci ici. |

Les cristaux ne sont pas décoratifs : c'est du Mana Brut qui a eu le temps de se
figer. D'où les points du Forgeron dans la région, et d'où les convois.

Le Dragonnet de Braise y niche — de nuit, sur les crêtes, et rarement.

### 2.4. La Forêt d'Ébène

| | |
|---|---|
| **Rôle** | Embuscades. La région où les convois se perdent. |
| **Hostilité** | +8 |
| **Autels** | L'*Autel d'Ébène* (Ombre), l'*Autel des Brumes* (Ombre) |
| **Ambiance** | Couvert dense, visibilité courte. On ne voit pas venir. |

La seule région dont la géographie sert directement une mécanique : une route de
convoi qui traverse la Forêt d'Ébène est une route où l'escorte compte. Les deux
Autels d'Ombre y sont placés pour que les tenir demande d'y rester.

C'est aussi le domaine du Dragonnet d'Ébène sauvage.

### 2.5. Les Montagnes du Crépuscule

| | |
|---|---|
| **Rôle** | Claims de fin d'Ère. Contenu pour Covens établis. |
| **Hostilité** | +14 |
| **Autels** | L'*Autel du Crépuscule* (Lumière) |
| **Ambiance** | Altitude, cols, versants manquants. La Fracture se voit à l'œil nu. |

Le décalage de quatorze est le plus fort du jeu : un gobelin ordinaire y devient
un adversaire sérieux. C'est délibéré — il faut un endroit où l'équipement acquis
redevient insuffisant.

Les versants manquants sont la marque géologique de la Fracture, et le seul endroit
où le lore est littéralement visible dans le terrain.

### 2.6. Le Wilderness

| | |
|---|---|
| **Rôle** | Tout le reste. PvP libre, ressources libres, rien de garanti. |
| **Hostilité** | +6 |
| **Autels** | aucun |

Ce n'est pas une région au sens narratif : c'est le défaut. Le décalage de six dit
l'essentiel — **par défaut, les Terres sont hostiles**, et les zones praticables
sont les exceptions qu'on a aménagées.

---

## 3. Les points d'intérêt

### 3.1. Les sept Autels

Ils ne se déplacent pas. Leur position et leur type sont déclarés en
configuration ; les coordonnées exactes sont dans
[la configuration de WizardCore](../05-operer/configuration/wizardcore-fichiers-yml.md).

| Autel | Type | Région |
|---|---|---|
| Autel des Murmures | Nature | Plaine des Murmures |
| Autel Central | Nature | Plaine des Murmures |
| Autel de Cristal | Glace | Désert de Cristal |
| Autel des Braises | Feu | Désert de Cristal |
| Autel d'Ébène | Ombre | Forêt d'Ébène |
| Autel des Brumes | Ombre | Forêt d'Ébène |
| Autel du Crépuscule | Lumière | Montagnes du Crépuscule |

La répartition n'est pas uniforme, et c'est le point : **deux Autels d'Ombre sont
dans la région la plus dangereuse**. Un Coven qui veut le bonus d'Ombre doit
accepter d'y vivre.

Un type d'Autel donne un bonus en plus des Éclats, et les bonus d'un même type ne
s'empilent que jusqu'à un plafond — sans quoi tenir quatre Autels de Feu
transformerait une avance en domination.

### 3.2. Les points du Forgeron

Une dizaine de positions déclarées, chacune nommée d'après sa région. Le Forgeron
en occupe une à la fois et change régulièrement.

Le placement obéit à une règle simple : **aucun point ne doit être à la fois
proche du spawn et sûr**. Un Forgeron trop commode supprime le risque du convoi,
donc la valeur du Mana Brut, donc tout l'intérêt de la ressource.

### 3.3. Le Panthéon

Au spawn. Cinq entrées par défaut, une par Ère récente. Voir
[Panthéon et figures](pantheon-et-figures.md#4-le-panthéon).

### 3.4. Les donjons

Les quatre dragons — Drake Terravore, Vouivre Chuchevent, Dragon Aile-de-braise,
Seigneur-Tonnerre céleste — **n'apparaissent jamais d'eux-mêmes**. Leur poids
d'apparition est nul : seul un donjon, ou une commande d'administration, les fait
venir.

C'est la raison pour laquelle il n'y a pas de « région des dragons ». Un boss
croisé au détour d'une plaine n'en serait plus un.

---

## 4. Construire une région

Pour un bâtisseur ou un *worldbuilder* qui ajoute une zone.

### Ce qu'il faut décider, dans l'ordre

1. **Le décalage d'hostilité.** C'est la seule chose qui rende une région
   différente mécaniquement. Entre −8 et +14 ; au-delà, on sort de l'échelle
   établie et les repères du joueur ne marchent plus.
2. **S'il y a un Autel, et de quel type.** Un Autel change une région en objectif.
   Sept existent ; en ajouter un dilue les six autres.
3. **S'il y a un point de Forgeron.** Voir la règle du paragraphe 3.2.
4. **La lisibilité du danger.** Un joueur doit pouvoir sentir qu'il change de
   région avant d'en payer le prix. Couvert, relief, couleur, son : le décalage
   d'hostilité doit se *voir*.
5. **La route d'entrée et de sortie.** Une région sans sortie praticable est une
   région où on meurt sans avoir choisi d'y aller.

### Ce qu'il ne faut pas faire

- **Réserver des espèces à la région.** Voir le paragraphe 1 : ça casse le modèle.
- **Faire une région sans raison d'y aller.** Une zone qui n'a ni Autel, ni
  ressource, ni Forgeron, ni quête est une zone vide avec un nom.
- **Cacher le danger.** Une région +14 qui ressemble à une région −4 n'est pas
  difficile, elle est injuste.
- **Mettre une safezone ailleurs qu'au spawn.** Une seule existe, et c'est ce qui
  donne du poids à tout le reste.

---

## 5. Ce qui n'existe pas encore

Dit franchement, pour que personne ne le cherche.

| Absent | Conséquence |
|---|---|
| Génération de carte sur mesure | Les régions sont des conventions posées sur un monde ordinaire |
| Frontières de région visibles en jeu | Le joueur devine la région au terrain et aux créatures |
| Marqueurs de carte automatiques | Les points d'intérêt se transmettent de bouche à oreille ou par quête |
| Quêtes liées à une région | Les marqueurs de quête pointent des coordonnées, pas des zones |

Le cadre de conception est dans
[cdc_spawn_map](../90-specifications/cdc_spawn_map.md). Il reste à détailler avec
le *worldbuilder* avant tout code.

---

## À lire ensuite

- [Bestiaire](bestiaire.md) — ce qui vit dans ces régions
- [Autels et Mana Brut](../04-jouer/autels-et-mana.md) — ce qu'on va y chercher
- [Le lore](lore.md) — pourquoi le terrain est cassé
