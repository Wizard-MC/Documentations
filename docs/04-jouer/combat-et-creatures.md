# Combat et créatures

Affronter ce qui vit dans les Terres, et en tirer quelque chose. Quarante-neuf
espèces hostiles, regroupées en dix familles, dont chacune enseigne une chose
différente.

**Statut : livré.**

---

## 1. Les trois règles du combat

### 1.1. Une attaque s'annonce toujours

Toute attaque a trois temps :

| Temps | Ce qui se passe |
|---|---|
| **Montée** | Le geste commence. C'est la fenêtre d'esquive. |
| **Durée** | Les dégâts tombent. |
| **Recharge** | La créature ne peut pas recommencer. |

La montée est le **télégraphe**, et c'est une exigence de conception : un dégât sans
geste visible est un défaut, pas une difficulté. Les clips sont synchronisés sur le
compteur du serveur précisément pour que le geste et les dégâts coïncident.

> Une créature qui vous touche sans avoir rien montré est un bug. **Signalez-le.**

### 1.2. La portée se mesure de surface à surface

Ce qui décide si une attaque touche, c'est la distance entre les **boîtes de
collision**, pas entre les centres.

Ça change la lecture d'un combat contre les grosses créatures. La limite est le bord
de la bête, pas son milieu : on peut être « loin » de son centre et parfaitement à
portée.

### 1.3. Le niveau dépend de l'endroit et de vous

Le niveau d'une créature se calcule à sa naissance, à partir de trois choses :

| | |
|---|---|
| Un décalage propre à l'espèce | Un boss naît plus haut qu'un gobelin |
| Le décalage d'hostilité de la région | De −8 au spawn à +14 aux Montagnes |
| La puissance du joueur le plus proche | Le monde monte avec vous |

**La même espèce n'est pas le même adversaire selon l'endroit et le moment.** Un
gobelin de la Plaine des Murmures et un gobelin des Montagnes du Crépuscule sont
séparés par vingt-deux niveaux.

C'est pourquoi la carte se lit comme une échelle de danger — voir
[Géographie](../02-univers/geographie.md#1-la-règle-qui-gouverne-tout).

---

## 2. Lire un tempérament

Le tempérament dit comment une créature se bat. Le connaître vaut mieux que connaître
ses points de vie.

| Tempérament | Comportement | Comment y répondre |
|---|---|---|
| **BRUTE** | Avance droit, encaisse, frappe fort | Ne pas échanger coup pour coup. Reculer en frappant. |
| **SKIRMISHER** | Harcèle, recule, revient | Ne pas poursuivre : elle revient d'elle-même |
| **RANGED** | Tient ses distances et tire | La fermer en premier, ou se mettre à couvert |
| **CASTER** | Lance, se replie quand on approche | L'approcher, et ne pas la laisser respirer |
| **AMBUSHER** | Attend, surgit | Se méfier de ce qui ne bouge pas |
| **STALKER** | Suit à distance avant d'engager | Vérifier derrière soi |
| **PACK_HUNTER** | Ne vaut qu'en groupe | Casser le groupe, pas les individus |
| **SENTINEL** | Garde un point, ne poursuit pas loin | **La contourner** |
| **DEFENSIVE** | Riposte, n'initie pas | La laisser tranquille |

La ligne SENTINEL est la plus utile : c'est le seul tempérament qu'on peut
simplement éviter. Tout ce qui poursuit apprend à courir ; un sentinelle apprend à
choisir.

---

## 3. Les dix familles, et ce qu'elles enseignent

| Famille | Espèces | Ce qu'elle enseigne |
|---|---:|---|
| **Gobelins** | 5 | Qu'une troupe a une forme, et qu'on la défait par le bon bout |
| **Gelées** | 9 | Le rythme : elles avancent par bonds, donc on peut anticiper |
| **Araignées** | 3 | Qu'on peut être suivi |
| **Mimiques et coffres** | 6 | Que ce qui brille n'est pas un cadeau |
| **Golem** | 1 | Qu'on peut contourner |
| **Bois vivants** | 5 | Qu'un décor peut être un adversaire |
| **Prairies grondantes** | 5 | Que ce qui ne bouge pas est souvent le pire |
| **Veine perdue** | 6 | Que tout ce qui est fragile n'est pas inoffensif |
| **Marais moisi** | 5 | L'eau, les essaims, et les pièges |
| **Dragons de donjon** | 4 | Rien : à ce stade on sait déjà |

Détail dans le [bestiaire](../02-univers/bestiaire.md).

### Les gobelins, la leçon de base

| Qui | Où il se tient | Ordre de priorité |
|---|---|---|
| Chaman | Le plus loin | **1** — il soigne |
| Archer | Derrière | **2** — il tire gratuitement |
| Gobelins et fouets | Au contact | 3 |
| Brute | Devant | 4 — elle encaisse, elle attend |

On ne prend pas une troupe par le milieu. La brute est faite pour retenir l'attention
pendant que l'arrière travaille.

### Les gelées, la leçon du rythme

Elles **bondissent**. Entre deux bonds, elles ne bougent pas. On peut donc compter,
et frapper dans l'intervalle.

Deux exceptions à connaître :

- La **Gelée soigneuse** soigne les autres gelées. À abattre en premier, comme le
  chaman.
- Le **Roi des gelées** marche au lieu de bondir. C'est le seul boss du jeu qui
  apparaît naturellement, et il est très rare.

### Les mimiques, la leçon de la méfiance

Un coffre isolé est soit un coffre, soit une mimique. Trois niveaux :

| Apparence | Ce que ça peut être |
|---|---|
| Coffre ordinaire | Mimique ordinaire |
| Coffre rare | Mimique rare |
| Coffre légendaire | Mimique légendaire |

**Plus le coffre est beau, plus la mimique est dure.** La récompense et le risque
sont le même objet.

### Les orbes, la leçon de l'économie

L'Orbe d'oubli pâle a dix-huit points de vie et fait sept de dégâts. C'est le rapport
le plus défavorable du bestiaire : fragile, mais capable de faire mal avant de
tomber.

---

## 4. Les boss

### Les boss naturels

Six espèces portent un boss de famille : Le Pourrisseur d'âmes, La Ciguë, Le Nyx, Le
Cryptique, Le Roi des gelées, et l'Azalée de cendres. Ils apparaissent naturellement,
très rarement, et exigent un niveau d'école élevé pour se montrer.

### Les dragons de donjon

Quatre espèces qui **n'apparaissent jamais d'elles-mêmes** : seul un donjon ou une
commande les fait venir.

| Dragon | Vol | Ce qui le caractérise |
|---|---|---|
| Drake Terravore | non | Reste au sol |
| Vouivre Chuchevent | oui | Le plus rapide |
| Dragon Aile-de-braise | oui | Le plus résistant |
| Seigneur-Tonnerre céleste | oui | Le plus dangereux |

### Combattre un dragon volant

Trois choses à savoir, et elles sont toutes les trois des décisions de conception :

1. **Il se pose pour frapper.** Un dragon qui resterait en l'air serait invincible
   pour un joueur au sol. Il descend, et c'est là qu'on le travaille.
2. **Il fait des passes rasantes.** Il fond sur vous sans se poser. Deux des trois
   n'ont pas de geste dédié pour ça et fondent quand même.
3. **Son butin tombe au point d'impact.** Si on l'abat à trente blocs de hauteur, le
   butin ne reste pas en l'air : il suit le corps jusqu'au sol.

### Le Colosse de l'Ère

Le boss mondial, et il ne paraît que pendant les **trois derniers jours** d'une Ère.
Deux heures, vingt mille points de vie, et le vainqueur est **celui qui porte le
dernier coup**.

S'il se retire au bout de deux heures, personne n'a rien gagné : ni vainqueur, ni
butin.

Voir [Panthéon et figures](../02-univers/pantheon-et-figures.md#5-le-colosse-de-lère).

---

## 5. Les nuées

Un événement d'apparition massive d'une espèce, avec une récompense à la clé.

C'est le seul moment où une créature ordinaire devient un objectif collectif : une
nuée de gobelins n'est pas plus dangereuse qu'un gobelin, elle est plus nombreuse, et
la récompense va à ceux qui se sont organisés.

Les nuées sont lancées par le staff. Voir
[Game master](../05-operer/game-master.md).

---

## 6. Les bêtes paisibles

Vingt-quatre espèces qui ne cherchent pas le combat.

| Disposition | Comportement | Attention |
|---|---|---|
| **CALM** | Se laisse approcher | — |
| **SKITTISH** | Fuit avant qu'on l'atteigne | Inutile de la poursuivre |
| **DEFENSIVE** | Riposte si on insiste | Le sanglier alpha et le crocodile |

Certaines sont **montables** : ours, crabe, drakelet, renard, loutre des neiges,
corbeau, husky, axolotl, griffon, yak. Voir
[Quêtes et montures](quetes-et-montures.md).

Les quatre **gardes** — fléau, hallebarde, pique, marteau — sont rangés ici parce
qu'ils se comportent comme des bêtes défensives : ils riposent, ils n'initient pas.

---

## 7. Ce qu'on en tire

Chaque espèce a une table de butin, avec un bonus par niveau : une créature de haut
niveau donne plus.

| Ce qui tombe | D'où |
|---|---|
| Matières ordinaires | Toutes les espèces |
| Matières de forge | Les élites et les boss |
| Clés de trésor | Les boss |
| **Tomes** | Les boss et les occurrences uniquement |

Les **Tomes** sont la récompense la plus précieuse : dix-neuf sorts ne s'apprennent
que par eux, et ils ne tombent nulle part ailleurs. C'est ce qui donne une raison
d'aller chercher un boss plutôt que de farmer du commun.

Certains butins ont un usage précis : la **Gelée de lave** laisse un noyau que les
forges convoitent, et c'est l'objet d'une quête annexe.

---

## 8. Les élites

Une créature élite porte son nom affiché en permanence et naît à un niveau rehaussé.
Les boss en sont.

C'est la seule façon de savoir, avant d'engager, qu'on a en face quelque chose de
plus dur que d'habitude.

---

## 9. Se battre avec de la magie

| Principe | Conséquence en combat |
|---|---|
| Tout sort s'incante | On est visible et vulnérable pendant la préparation |
| Bouger annule l'incantation | Se poser avant de lancer |
| L'Essence ne revient presque pas en combat | Garder deux sorts de marge |
| Une pause commune suit chaque sort | On n'enchaîne pas deux sorts instantanément |
| Un sort interrompu rend la moitié de son Essence | Être coupé n'est pas la ruine |

L'école change la façon de se battre : le **Givre** entrave pendant que le groupe
frappe, le **Vide** coupe la magie d'un chaman, l'**Ombre** permet de jouer seul.

Détail dans [La magie](magie.md).

---

## 10. Les pièges

| Piège | Ce qui se passe | Quoi faire |
|---|---|---|
| Attaquer une troupe par le milieu | L'archer et le chaman travaillent tranquilles | L'arrière d'abord |
| Poursuivre un SKIRMISHER | Il recule et revient : on se fait promener | Le laisser venir |
| Échanger coup pour coup avec une BRUTE | Elle est faite pour ça | Reculer en frappant |
| Ignorer la Gelée soigneuse | Le groupe ne descend plus | Elle d'abord |
| Ouvrir un coffre doré sans se préparer | La mimique légendaire est un vrai combat | Se préparer, ou passer |
| Monter aux Montagnes trop tôt | +14 niveaux d'hostilité | Rester vers la Plaine au début |
| Abattre un dragon très haut en croyant perdre le butin | Il tombe au point d'impact | Suivre le corps |
| Compter sur les points de vie pour juger le danger | L'orbe pâle a 18 PV et fait 7 de dégâts | Lire le tempérament |

---

## À lire ensuite

- [Bestiaire](../02-univers/bestiaire.md) — les familles racontées
- [Catalogue des créatures](../07-reference/catalogue-creatures.md) — les valeurs exactes
- [La magie](magie.md) — se battre avec des sorts
- [cdc_mobs](../90-specifications/cdc_mobs.md) — la spécification
