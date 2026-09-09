# Game master

Animer le serveur : occurrences, apparitions, nuées, boss, Colosse. La différence entre
un serveur qui tourne et un serveur où il se passe quelque chose.

**Statut : livré.**

---

## 1. Le rôle

Un game master ne surveille pas, il **provoque**. Son travail est de créer des raisons
de sortir.

| Ce qu'il fait | Ce qu'il ne fait pas |
|---|---|
| Lancer des événements | Intervenir dans une guerre |
| Faire apparaître des créatures | Donner un objet à un joueur |
| Animer la fin d'Ère | Arbitrer un différend |
| Raconter ce qui arrive | Prendre parti |

### Le problème qu'il résout

Au bout de trois semaines d'Ère, les rapports de force sont connus et plus personne ne
prend de risque. C'est le principal danger de la phase d'installation — voir
[Boucles de jeu](../03-gdd/boucles-de-jeu.md#52-linstallation--jours-8-à-42).

Les occurrences sont la réponse : un événement donne une raison d'aller ailleurs, et
d'y croiser quelqu'un.

---

## 2. Les occurrences

Le module qui porte tous les événements planifiés, y compris le Colosse.

| Commande | Ce qu'elle fait | Permission |
|---|---|---|
| `/event help` | L'aide | `core.occurrences` |
| `/event start` | Lance une occurrence | `core.occurrences` |
| `/event now` | Lance immédiatement | `core.occurrences` |
| `/event stop` | Arrête l'occurrence en cours | `core.occurrences` |
| `/schedule`, `/schedule add` | La planification | `core.schedules` |
| `/treatment add`, `/treatment edit`, `/treatment set` | Les traitements d'occurrence | `core.occurrence.treatments` |

Une occurrence apporte, sans rien réécrire : la planification, les annonces
d'approche, un repère sur la carte, et un tableau de score. C'est pour ça que le
Colosse en est une et non un système à part.

---

## 3. Le Colosse de l'Ère

Le boss mondial. **Il ne paraît qu'en phase de Grand Cataclysme** — lancé hors de
cette phase, il est refusé.

| | Valeur |
|---|---|
| Durée | 2 heures |
| Vie | 20 000 par défaut |
| Vainqueur | **Celui qui porte le dernier coup** |
| Nom | Suit le thème de l'Ère |

| Commande | Ce qu'elle fait | Permission |
|---|---|---|
| `/boss set <vie> [dégâts] [modèle]` | Règle le Colosse | `wizardmc.admin.occurrences` |

### Les trois règles

1. **Deux heures, puis il se retire.** Sans vainqueur ni butin. Le laisser traîner
   ferait d'un événement une décoration ; le faire mourir seul récompenserait l'attente.
2. **Le dernier coup gagne.** C'est le seul critère tenable sans compter les dégâts de
   chacun sur deux heures — un tableau de contributions vivrait en mémoire,
   disparaîtrait au redémarrage, et se disputerait au premier écart d'un point.
3. **Son nom suit le thème.** Colosse des Ténèbres, d'Aurore, des Fractures. C'est la
   seule chose qui rattache visiblement le combat à l'Ère qu'il clôt.

### Comment l'animer

| Avant | Pendant | Après |
|---|---|---|
| Annoncer une heure à l'avance | Laisser faire. Ne pas intervenir. | Annoncer le vainqueur |
| Vérifier qu'on est bien en Cataclysme | Surveiller la charge serveur | Écrire l'entrée du journal d'Ère |
| Choisir un lieu accessible | — | Ne pas le relancer le même jour |

---

## 4. Faire apparaître des créatures

| Commande | Ce qu'elle fait | Permission |
|---|---|---|
| `/mobs spawn <espèce>` | Fait apparaître une espèce | `core.mobs.list` |
| `/mobs list` | La liste des espèces | `core.mobs.list` |
| `/mobs info` | Le détail d'une espèce | `core.mobs.list` |
| `/mobs purge` | Retire les créatures custom | `core.mobs.list` |
| `/mobs reload` | Recharge le catalogue | `core.mobs.list` |

### Les neuf espèces qui n'apparaissent jamais seules

Leur poids est nul : c'est **ici** qu'on les fait venir.

| Espèce | Ce que c'est |
|---|---|
| Drake Terravore | Boss de donjon, reste au sol |
| Vouivre Chuchevent | Boss volant, le plus rapide |
| Dragon Aile-de-braise | Boss volant, le plus résistant |
| Seigneur-Tonnerre céleste | Boss volant, le plus dangereux |
| Coffre, Coffre rare, Coffre légendaire | Les leurres des mimiques |

> **Un boss croisé au détour d'une plaine n'en serait plus un.** C'est pour ça qu'ils
> ne naissent pas d'eux-mêmes, et c'est pour ça qu'un game master doit les placer avec
> intention.

### Les règles à respecter en faisant apparaître un boss

1. **Annoncer.** Un boss qui tombe sur un joueur sans prévenir n'est pas un événement,
   c'est une punition.
2. **Un lieu accessible.** Un dragon dans un canyon fermé n'est combattu par personne.
3. **Pas dans un claim sans accord.** C'est intervenir dans le jeu d'un Coven.
4. **Pas au spawn.** C'est une safezone ; une créature hostile y est une incohérence.
5. **Un à la fois.** Deux dragons volants en même temps dépassent ce que la charge
   serveur et les joueurs peuvent tenir.
6. **Purger après.** `/mobs purge` évite qu'un boss oublié traîne une semaine.

### Les dragons volants, en pratique

| À savoir | Conséquence |
|---|---|
| Ils se posent pour frapper | Un joueur au sol peut les combattre |
| Ils font des passes rasantes | Prévoir de l'espace dégagé |
| Leur butin tombe au point d'impact | Pas besoin de les tuer au sol |
| Le Drake Terravore ne vole pas | Son modèle n'a aucun clip de vol |

---

## 5. Les nuées

Un événement d'apparition massive d'une espèce, avec une récompense à la clé.

C'est le seul moment où une créature **ordinaire** devient un objectif collectif : une
nuée de gobelins n'est pas plus dangereuse qu'un gobelin, elle est plus nombreuse, et la
récompense va à ceux qui se sont organisés.

### Bien choisir l'espèce

| Bon choix | Mauvais choix |
|---|---|
| Une espèce fréquente que tout le monde connaît | Un boss |
| Une espèce de groupe : gobelins, gelées, insectes | Une espèce solitaire |
| Une espèce dont le butin sert | Une espèce sans intérêt |

Le gobelin ordinaire est le meilleur candidat du catalogue : c'est l'espèce la plus
fréquente du jeu, donc celle que tout le monde sait combattre, donc celle où le nombre
seul fait l'événement.

---

## 6. Les autres événements

| Système | Commandes | Permission |
|---|---|---|
| **KotH** — tenir un point | `/koth create`, `/koth list` | `core.koths` |
| **Coffres tombés** | `/fallenchest generate`, `/fallenchest point`, `/fallenchest add` | `core.fallenchest` |
| **Caisses** | `/crate create`, `/crate add`, `/crate give`, `/crate setlocation` | `core.crates` |
| **Blocs chanceux** | `/lucky add`, `/lucky remove` | `core.luckys` |
| **Boosts** | `/boost enable`, `/boost mode`, `/boost config` | `core.boost` |
| **Arènes** | `/arena create`, `/arena delete` | `core.admin` |
| **Totems** | `/totem set`, `/totem warp` | `core.totem` |
| **Sang** | `/blood create`, `/blood edit` | `core.blood` |

### Les coffres tombés

Des coffres qui apparaissent à des positions déclarées, en deux raretés. Le nombre
maximal de chaque rareté se règle.

C'est l'événement le moins coûteux à animer : il se génère, et les joueurs s'en
occupent. Bon pour relancer une soirée creuse.

### Les boosts

Attention : un boost est le genre d'outil qui peut casser l'équilibre sans qu'on le
remarque. Avant d'en activer un, se demander **ce qu'il rend dérisoire**.

Un boost de butin pendant une soirée rend le butin des autres soirées décevant. La
règle des modificateurs de thème s'applique aussi ici : **léger, ou pas du tout**.

---

## 7. Animer une fin d'Ère

Les trois derniers jours sont le moment où un game master compte le plus.

| Jour | Ce qu'il fait |
|---|---|
| **J-7** | Annoncer que le Cataclysme approche |
| **J43** | Annoncer l'ouverture du Cataclysme. Autels triplés, recharges divisées. |
| **J43 à J45** | Un Colosse. Des nuées. Des coffres tombés. Le serveur doit être dehors. |
| **J45** | Annoncer le vainqueur |

### Ce qu'il ne fait pas pendant le Cataclysme

| Jamais | Pourquoi |
|---|---|
| Intervenir dans une guerre décisive | C'est l'enjeu de toute l'Ère |
| Lancer un boss dans une zone contestée | Ça décide une guerre à la place des joueurs |
| Activer un boost qui favorise un camp | — |
| Lancer deux Colosses | Un seul clôt l'Ère |

---

## 8. Écrire une annonce

Le registre est celui du [lore](../02-univers/lore.md#le-ton) : sobre, factuel, sans
emphase.

| À faire | À éviter |
|---|---|
| Dire ce qui se passe dans le monde | Dire ce qui change dans les règles |
| Deux ou trois phrases | Un paragraphe |
| Laisser le joueur décider si ça l'intéresse | Lui dire que c'est l'événement du siècle |

> « Quelque chose s'est levé dans les Montagnes du Crépuscule. Ça ne restera pas deux
> heures. »

Plutôt que : « ÉVÉNEMENT EXCEPTIONNEL ! Le Colosse de l'Ère apparaît avec 20 000 PV et
un butin légendaire ! »

La seconde version dit tout, donc ne laisse rien à découvrir — et elle annonce une
mécanique au lieu de raconter.

---

## 9. La charge serveur

Les modèles animés coûtent cher. Quelques repères :

| Situation | Effet |
|---|---|
| Une nuée de cinquante créatures | Sensible chez les joueurs proches |
| Un dragon volant avec passes rasantes | Coûteux en calcul de trajectoire |
| Deux boss en même temps | À éviter |
| Beaucoup de joueurs au même endroit | C'est le cas normal d'un événement : le prévoir |

Surveiller pendant l'événement, et `/mobs purge` si ça dérape.

---

## 10. La liste avant événement

1. **Annoncer**, au moins quelques minutes à l'avance.
2. **Vérifier la phase d'Ère** si c'est un Colosse.
3. **Choisir un lieu** accessible, hors claim, hors safezone.
4. **Vérifier la charge** avant de commencer.
5. **Ne pas intervenir** pendant.
6. **Purger** après.
7. **Noter** ce qui s'est passé, pour le journal d'Ère.

---

## À lire ensuite

- [Modération](moderation.md) — quand c'est un incident et non un événement
- [Exploitation quotidienne](exploitation.md) — la clôture d'Ère
- [Catalogue des créatures](../07-reference/catalogue-creatures.md) — les 49 espèces
- [Commandes](../07-reference/commandes.md) — les 129 commandes
