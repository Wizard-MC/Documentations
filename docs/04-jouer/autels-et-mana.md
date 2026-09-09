# Autels et Mana Brut

Les deux ressources qu'on va chercher dehors. Les Autels se prennent en restant
debout sous le feu ; le Mana Brut se porte, et se perd.

**Statut : livré.**

---

## 1. Les Autels Sacrés

### Ce qu'ils sont

Sept points fixes de la carte. On les capture, on les tient, et tant qu'on les tient
ils donnent un bonus au Coven.

Dans la fiction, ce sont les seules choses matérielles que l'Âge Lié ait laissées.
Personne ne sait qui les a taillés, ni s'ils étaient sept à l'origine.

### Capturer

| | Valeur |
|---|---|
| Rayon à tenir | 3 blocs |
| Durée | 45 secondes |
| Position | **immobile** en X et Z |
| Récompense | 15 Éclats, **45** pendant le Grand Cataclysme |
| Recharge après capture | 1 heure |

La progression s'affiche en jauge, et la capture est **annoncée à tout le serveur**.
Il n'y a pas de capture discrète : dès que vous commencez, tout le monde le sait.

### Ce qui annule une capture

Bouger, mourir, se déconnecter, se téléporter, sortir du rayon. La progression
retombe à **zéro**, pas à la moitié.

Quarante-cinq secondes immobile au même endroit, avec tout le serveur prévenu : c'est
le système le plus exposé du jeu, et c'est fait pour. Une capture qui ne se défend pas
ne vaut rien.

### La contestation

Si deux joueurs de Covens différents sont sur l'Autel, la progression se **met en
pause pour tout le monde**. Personne ne capture tant que le terrain n'est pas à un
seul camp.

Conséquence : un seul joueur adverse suffit à bloquer une capture. Tenir un Autel
contre plus nombreux est impossible, mais l'empêcher est à la portée de n'importe qui.

### Les cinq types

Le type décide du bonus accordé tant qu'on est propriétaire.

| Type | Bonus | Qui en veut |
|---|---|---|
| **Feu** | +5 % de dégâts à l'épée hors claim allié, ou expérience de forge | Les combattants |
| **Glace** | +10 % sur la durée du Bouclier | Un Coven sur la défensive |
| **Ombre** | −5 % sur la recharge du Portail | Un Coven mobile |
| **Lumière** | Un point de retour temporaire, légère régénération dans les claims | Un Coven dispersé |
| **Nature** | +10 % de génération passive de Mana Brut | Les marchands |

### Les plafonds, et pourquoi ils existent

Un Coven cumule les bonus des Autels qu'il possède — **jusqu'à un plafond par type,
et un plafond global**.

Sans eux, un Coven dominant qui prend les deux Autels d'Ombre et les deux de Nature
transformerait une avance en domination définitive. Les plafonds sont ce qui garde la
fin d'Ère jouable.

### Où ils sont

| Autel | Type | Région |
|---|---|---|
| Autel des Murmures | Nature | Plaine des Murmures |
| Autel Central | Nature | Plaine des Murmures |
| Autel de Cristal | Glace | Désert de Cristal |
| Autel des Braises | Feu | Désert de Cristal |
| Autel d'Ébène | Ombre | Forêt d'Ébène |
| Autel des Brumes | Ombre | Forêt d'Ébène |
| Autel du Crépuscule | Lumière | Montagnes du Crépuscule |

La répartition est volontairement inégale : **les deux Autels d'Ombre sont dans la
région la plus dangereuse**. Un Coven qui veut le bonus d'Ombre doit accepter d'y
vivre.

### Capturer un Autel ennemi n'est pas une déclaration de guerre

C'est un **événement diplomatique** : il est journalisé, et les Officiers adverses
peuvent être prévenus. Mais il ne déclenche pas la guerre automatiquement.

C'est ce qui permet de tâter un voisin sans s'engager — et c'est aussi ce qui rend les
Autels une source de tension permanente plutôt qu'un déclencheur mécanique.

### Prendre un Autel, en pratique

1. **Vérifier la recharge.** `/altar` donne l'état. Partir à sept sur un Autel en
   recharge ne donnera rien.
2. **Y aller à plusieurs.** Un joueur tient le rayon, les autres tiennent le terrain.
   Le porteur de la capture est la cible.
3. **S'attendre à être vu.** L'annonce part dès le début.
4. **Se méfier de la contestation.** Un seul adversaire dans le rayon suffit à geler
   la progression.

---

## 2. Le Mana Brut

### Ce qu'il est

La seule ressource du jeu qu'on peut se faire voler. Dans la fiction, c'est la
saignée du monde à l'état solide.

Il existe sous deux formes :

| Forme | Où | Risque |
|---|---|---|
| **Stock virtuel** | Au Coven, à l'abri | aucun |
| **Objet** | Dans un inventaire | se perd à la mort |

### Comment il arrive

| Source | Rendement |
|---|---|
| Génération passive | 1 Mana par claim, toutes les 30 minutes |
| Bonus du grenier (Nexus niveau 6) | +5 % |
| Bonus d'un Autel de Nature | +10 % |

La génération suit le **nombre de claims**. Un Coven qui s'étale produit plus — c'est
l'une des rares récompenses directes du territoire.

### Le retirer

`/mana withdraw <n>` sort du Mana du stock virtuel vers l'inventaire. Le rôle
**Marchand** et au-dessus peuvent le faire.

À partir de cet instant, il est en danger.

### Les contraintes de l'objet

| | |
|---|---|
| Se stocke jusqu'à 64 par pile | — |
| **Interdit dans les coffres, fours et entonnoirs** | Il ne se met pas de côté |
| Transport ou échange direct uniquement | Entre joueurs, ou au Forgeron |

L'interdiction de stockage est la règle la plus importante du système : **on ne peut
pas accumuler du Mana Brut dans un coffre**. Soit il est au stock virtuel, soit il est
sur quelqu'un. Il n'y a pas de troisième état, donc pas de moyen d'éviter le risque.

### Mourir avec du Mana sur soi

La moitié tombe au sol, **la moitié est détruite**. Rien ne retourne au stock
virtuel.

La destruction n'est pas une punition gratuite : elle fait que le Mana Brut a un vrai
coût d'échec. Si tout tombait au sol, mourir ne serait qu'un transfert au tueur, et le
stock du monde ne bougerait jamais.

### La pastille de danger

Un joueur qui porte du Mana Brut porte une **pastille visible**. Ce n'est pas une
option : c'est la mécanique.

Le risque du convoi ne vient pas d'une règle de PvP. Il vient de ce que le porteur
est visible, que le Forgeron bouge, et que tout le monde doit y aller. Les routes se
croisent.

---

## 3. Le Forgeron Itinérant

### Ce qu'il fait

Il achète le Mana Brut, et c'est le seul débouché fiable. En échange : armes,
potions, blocs, cosmétiques légers.

### Il bouge

Toutes les soixante minutes par défaut, entre une dizaine de points déclarés. Le
thème **Chaos** d'une Ère raccourcit ce délai — les routes deviennent imprévisibles.

### La règle de placement

**Aucun point n'est à la fois proche du spawn et sûr.** Un Forgeron commode
supprimerait le risque, donc la valeur du Mana Brut, donc la ressource — et avec elle
tout le profil du marchand.

### Le trouver

`/forgeron` donne son emplacement actuel.

---

## 4. Les convois

### Ce que c'est

Un déplacement de Mana Brut. Il n'y a pas de véhicule, pas de système dédié : un
convoi, c'est des joueurs qui marchent ensemble.

C'est volontaire. Le système le plus intéressant du jeu est celui qui a le moins de
code : la tension naît de trois faits simples, et les joueurs font le reste.

### Mener un convoi

1. **Retirer le Mana au dernier moment.** Chaque minute avec la pastille est une
   minute d'exposition.
2. **Connaître la route.** `/forgeron` avant de partir, pas pendant.
3. **Répartir la charge.** Plusieurs porteurs avec peu chacun valent mieux qu'un seul
   chargé : perdre un porteur ne coûte qu'une fraction.
4. **Une escorte.** Les alliés peuvent escorter. Un convoi seul dans la Forêt
   d'Ébène est un cadeau.
5. **Éviter la Forêt d'Ébène.** La visibilité y est courte — c'est la région des
   embuscades, et c'est écrit dans sa conception.

### Intercepter un convoi

Le Mana d'un ennemi peut être ramassé en wilderness, et pendant une guerre. Ce qui
marche :

- **Attendre sur la route du Forgeron.** On sait où ils vont.
- **Surveiller la pastille.** Elle dit qui porte.
- **Viser la Forêt d'Ébène.** La région est faite pour ça.

### Le mercenariat

Escorter un convoi contre paiement est une pratique de joueurs, pas une mécanique.
Le paiement passe par un échange direct ou un pourboire de la banque du Coven.

C'est le genre d'usage qu'on laisse vivre sans l'outiller : une fois codifié, il
cesserait d'être une négociation.

---

## 5. Acheter plutôt que transporter

C'est une stratégie valable, et l'une des deux voies d'accès au Mana Brut prévues
par la conception.

Un joueur qui ne veut pas du risque peut acheter le Mana d'un autre, ou payer
quelqu'un pour aller le vendre. C'est ce qui fait exister le profil du **marchand** —
et ce profil n'a pas été conçu, il est une conséquence du fait que la ressource se
porte.

---

## 6. Les commandes

| Commande | Ce qu'elle fait |
|---|---|
| `/mana` — ou `/mana info`, `/mana stock` | Son stock, celui du Coven, et l'interface |
| `/mana withdraw <n>` — ou `/mana take` | Sortir du Mana vers l'inventaire |
| `/mana help` | L'aide |
| `/altar` — ou `/autel` | L'état des Autels |
| `/forgeron` | Où il est |

Les commandes d'administration — `/mana generate`, `/altar reset`,
`/altar setcooldown`, `/forgeron move`, `/forgeron spawn` — exigent respectivement
`wizardmc.mana.admin`, `wizardmc.altars.admin` et `wizardmc.forgeron.admin`.

---

## 7. Les pièges

| Piège | Ce qui se passe | Quoi faire |
|---|---|---|
| Retirer du Mana puis aller faire autre chose | La pastille reste visible tout ce temps | Retirer au dernier moment |
| Tout charger sur un porteur | Une mort coûte tout le convoi | Répartir |
| Partir à l'Autel sans vérifier la recharge | Une heure d'attente pour rien | `/altar` d'abord |
| Capturer seul | L'annonce part, et on est immobile 45 secondes | Y aller à plusieurs |
| Croire qu'on peut stocker du Mana | Coffres, fours et entonnoirs le refusent | Stock virtuel, ou sur soi |
| Oublier que la moitié du Mana est détruite à la mort | Le tueur n'en récupère que la moitié, et vous rien | C'est le coût du risque |
| Prendre un Autel ennemi en croyant déclarer la guerre | Ce n'est pas automatique | C'est un signal, pas un acte |

---

## À lire ensuite

- [Nexus et ville](nexus-et-ville.md) — ce que les Éclats achètent
- [Géographie](../02-univers/geographie.md) — où sont les Autels et les routes
- [cdc_autels_sacres](../90-specifications/cdc_autels_sacres.md) — la spécification
- [cdc_mana_brut](../90-specifications/cdc_mana_brut.md) — la spécification
