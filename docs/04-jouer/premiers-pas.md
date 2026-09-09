# Premiers pas

De la première connexion à la fin de la première heure. Ce document décrit ce qui
arrive à un nouveau joueur, dans l'ordre, et ce qu'il doit avoir compris à chaque
étape.

À lire par les joueurs, mais aussi par le staff : **c'est sur cette heure-là que le
serveur se juge**, et savoir où un joueur décroche est la donnée la plus utile du
support.

---

## 1. Avant de se connecter

### Il faut le client WizardMC

Le client Minecraft ordinaire ne suffit pas. Sans lui, le serveur expulse au bout de
cinq secondes — et ce n'est pas une sanction, c'est une poignée de main qui échoue.

Le client apporte ce que le jeu de base ne sait pas faire : les créatures en modèles
3D animés, l'interface de magie, le journal de quêtes, les frontières de territoire
visibles, les effets de sorts.

Si le client refuse de se connecter, voir
[Interface et client](interface-client.md#7-quand-ça-cloche).

---

## 2. L'arrivée — les deux premières minutes

### Ce qui se passe

On ne se réveille pas dans un monde : on y est **invoqué**. Une colonne de lumière,
un anneau de particules, puis une voix.

| Moment | Ce qu'on voit |
|---|---|
| Le faisceau | Une colonne de lumière bleue. On *paraît*, on n'atterrit pas. |
| Le portail | Un anneau de particules dorées et violettes |
| « Où suis-je ? » | Sa propre réplique, sans voix |
| Aelindra | Elle se pose devant. Cinq répliques, une animation par réplique. |
| Le choix | Une grille de neuf écoles |
| Le retour | Téléportation au spawn |

Environ quatre-vingt-dix secondes, dont quatre-vingt-une de dialogue.

### Le seul choix qui compte

Aelindra pose une question : **laquelle des neuf écoles est la tienne ?**

| École | Ce qu'elle fait |
|---|---|
| **Arcane** | L'étalon. Polyvalente, sans spécialité. |
| **Braises** | Dégâts directs, en aire, trace au sol |
| **Givre** | Contrôle : ralentir, bloquer, entraver |
| **Tempête** | Vitesse : projectiles rapides, déplacements, poussées |
| **Roc** | Protection, construction, cultures |
| **Aurore** | Soins, lumière, révélation |
| **Esprit** | Soutien de groupe |
| **Ombre** | Dissimulation, affaiblissement, sacrifice |
| **Vide** | Annulation : couper la magie de l'adversaire |

> **Ce choix ne ferme rien.** Il donne un avantage de progression dans cette école.
> Tous les sorts des neuf écoles restent apprenables. Un joueur qui découvre au bout
> de vingt heures qu'il préfère le Givre n'a rien à recommencer.

Sans choix dans le délai imparti, une école par défaut est attribuée et la
cinématique se clôt. Elle ne se rejoue jamais.

### Si ça se passe mal

Une déconnexion en cours de route **ne coûte rien** : rien n'est écrit avant le
choix, et l'invocation se rejoue à la reconnexion.

---

## 3. Le spawn — minutes 2 à 10

C'est la **safezone** : pas de PvP, pas de casse, rien ne peut arriver.

### Les trois choses à faire

1. **Trouver le Forgeron.** C'est lui qui donne la première quête. Il est le seul
   personnage à qui on peut parler, et c'est volontaire : un joueur qui débarque n'a
   personne, et il faut quelqu'un.
2. **Regarder le Panthéon.** Les noms des Covens vainqueurs des Ères passées. Ça dit
   en un coup d'œil qu'il y a une histoire avant soi.
3. **Ouvrir l'aide.** La commande `/wiki` — aussi `/help` ou `/aide` — donne le wiki
   en jeu.

### Le point de rupture

**À dix minutes.** Si à dix minutes un joueur n'a rien à faire et personne à qui
parler, il part. C'est pourquoi le Forgeron donne la première quête, et pourquoi la
trame commence par « présentez-vous » plutôt que par un combat.

---

## 4. La première quête — minutes 10 à 30

### *Le premier souffle*

La première quête de la trame. Trois étapes, et la première est de parler au
Forgeron — parce qu'on apprend à parler avant d'apprendre à se battre.

Le journal s'ouvre avec **L** par défaut. Il affiche les quêtes en cours, l'étape
courante, et ce qui reste à faire.

### Le faisceau de quête

Quand une quête demande d'aller quelque part, le client trace un **chemin à
l'écran** vers le point. C'est ce qui évite qu'un texte comme « rendez-vous aux
Terres Fracturées » laisse chercher au hasard : le texte décrit, le point conduit.

### Ses premiers sorts

| Commande | Ce qu'elle fait |
|---|---|
| `/magic` | L'aide de la magie |
| `/magic learn <sort>` | Apprendre un sort accessible |
| `/magic bind` | Placer un sort sur sa barre |

L'état — Essence, école, niveaux — se lit au **HUD** et dans le **grimoire**, pas en
ligne de commande : `/magic info` est réservée à l'administration.

Quelques sorts s'apprennent d'office dès le départ. Le premier geste utile est d'en
placer un sur la barre et de le lancer sur rien, pour voir.

### Ce qu'il doit avoir compris à trente minutes

- Qu'un sort **s'incante** : le cercle se trace, et pendant ce temps on est visible.
- Que lancer coûte de l'**Essence Arcane**, et que l'Essence revient lentement —
  plus lentement encore en combat.
- Qu'il y a une **recharge** après chaque sort, et une courte pause commune à tous.

Détail complet dans [La magie](magie.md).

---

## 5. Le premier combat — minutes 30 à 60

### Les gobelins

La famille la plus fréquente, et celle qui sert de leçon. Une bande a une **forme** :

| Qui | Où il se tient |
|---|---|
| La brute | Devant |
| Les gobelins et les fouets | Au contact |
| L'archer | Derrière |
| Le chaman | Encore derrière, et il soigne |

La leçon : **on ne prend pas une troupe par le milieu**. L'archer et le chaman
d'abord, ou la brute fixée pendant que quelqu'un contourne.

### Lire une attaque

Toute attaque a trois temps : le geste commence, les dégâts tombent, puis la
créature se recharge. **La première phase est le signal** — c'est là qu'on esquive.

Une attaque qui toucherait sans geste visible est un bug : il faut le signaler.

### Le premier contrat

`/contrat` liste les trois contrats du jour. Ce sont les Éclats les plus sûrs du jeu.

| Contrat | Ce qu'il demande |
|---|---|
| Tuer des ennemis | 5 joueurs adverses |
| Capturer un Autel | une capture complète |
| Survivre en zone hostile | 10 minutes dehors, remis à zéro à la mort |

Le troisième est celui d'un joueur seul : il n'exige ni groupe, ni PvP.

### Mourir

Ça arrive, et ça ne ruine pas. On perd ce qu'on portait, pas sa progression : les
sorts appris, les niveaux d'école et les quêtes restent.

Une seule chose se perd vraiment à la mort : le **Mana Brut** qu'on transportait.
C'est pour ça qu'on ne le transporte pas sans raison.

---

## 6. La première heure passée

### Trouver un Coven

C'est l'étape qui change le jeu. Presque toute la progression durable — le Nexus,
les Autels, les pouvoirs, le territoire — appartient au **Coven** et pas à un joueur.

On peut jouer seul : tous les sorts, toutes les écoles, toute la trame, les dix
montures et presque tout le bestiaire sont accessibles en solitaire. Ce qui est
inaccessible seul est exactement ce qui sert à dominer, et dominer seul n'a pas de
sens.

Voir [Le Coven](coven.md).

### Les quatre directions possibles

| Si on aime… | On va vers… | Et on lit… |
|---|---|---|
| Se battre | les Autels, les élites, les convois adverses | [Combat et créatures](combat-et-creatures.md) |
| Construire | la ville et les déblocages du Nexus | [Nexus et ville](nexus-et-ville.md) |
| Commercer | le Forgeron, l'hôtel des ventes | [Autels et Mana Brut](autels-et-mana.md) |
| La magie | les écoles, les Tomes, les baguettes | [La magie](magie.md) |

Aucune n'est meilleure. Un Coven a besoin des quatre.

---

## 7. Les erreurs de débutant

| Erreur | Ce qui se passe | Ce qu'il faut faire |
|---|---|---|
| Transporter du Mana Brut sans escorte | Le porteur est **signalé** à tout le monde. On sera attendu. | Y aller à plusieurs, ou vendre son Mana à un marchand |
| Aller aux Montagnes du Crépuscule trop tôt | Les créatures y sont quatorze niveaux au-dessus | Rester vers la Plaine des Murmures au début |
| Vider son Essence avant un combat | Elle revient très lentement en combat | Garder de quoi lancer deux sorts |
| Ignorer les contrats | On laisse les Éclats les plus sûrs du jeu | `/contrat` à chaque connexion |
| Attaquer une troupe par le milieu | L'archer tire pendant qu'on s'occupe de la brute | L'arrière d'abord |
| Ouvrir un coffre isolé sans méfiance | C'est peut-être une mimique | Les coffres dorés sont les pires |
| Rester seul trop longtemps | On plafonne sur tout l'axe collectif | Rejoindre un Coven |

---

## 8. Les commandes de la première heure

| Commande | Ce qu'elle fait |
|---|---|
| `/wiki` — ou `/help`, `/aide` | Le wiki en jeu |
| `/magic` | L'aide de la magie |
| `/quest` — ou `/journal` | Le journal de quêtes |
| `/contrat` | Les contrats du jour |
| `/era` | L'Ère en cours, sa phase, le temps restant |
| `/nexus` | L'aide du Nexus |
| `/mana` | Son stock de Mana Brut et celui de son Coven |
| `/boutique` | Les cosmétiques |
| `/box` | Ses réserves |

La liste complète est dans [Commandes](../07-reference/commandes.md).

---

## 9. Les raccourcis

| Touche | Ce qu'elle ouvre |
|---|---|
| **R** | La roue des sorts |
| **L** | Le journal de quêtes |
| **K** | Appeler son compagnon |
| **M** | Appeler sa monture |

Tous se changent dans les options du client, comme n'importe quelle touche.

---

## À lire ensuite

- [La magie](magie.md) — le système qu'on vient de commencer
- [Le Coven](coven.md) — l'étape suivante
- [Combat et créatures](combat-et-creatures.md) — affronter le bestiaire
- [Interface et client](interface-client.md) — régler son affichage
