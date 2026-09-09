# Boucles de jeu

Ce qu'un joueur fait concrètement, à chaque échelle de temps et selon ce qu'il aime
faire. Ce document sert à vérifier qu'un ajout de contenu a une place dans la vie
réelle d'un joueur — et à repérer les profils qu'on a oubliés.

---

## 1. La contrainte qui commande tout

> **Une session d'une heure doit produire quelque chose.**

Le joueur visé a quelques heures par semaine, pas quelques heures par jour. Un
système qui n'a de sens qu'après dix heures d'affilée est mal calibré, et il ne
sera pas utilisé.

Le test à appliquer devant n'importe quel ajout : *que rapporte une heure ?* Si la
réponse est « rien de visible », l'ajout est à découper.

| Durée de session | Ce qui doit être atteignable |
|---|---|
| **15 minutes** | Un contrat, quelques niveaux de sort, une quête annexe courte |
| **1 heure** | Les trois contrats du jour, une capture d'Autel, une étape de trame, un niveau de Nexus au début |
| **2 à 3 heures** | Un convoi complet avec escorte, un donjon, une guerre |
| **Une soirée de groupe** | Un Colosse, une nuée, une guerre décisive |

---

## 2. La boucle quotidienne

### 2.1. L'ossature commune

Ce que fait à peu près tout le monde en se connectant, dans l'ordre où ça se
présente naturellement.

1. **Lire l'état du monde.** Le HUD donne l'Ère et sa phase, le niveau du Nexus, les
   Autels tenus, et si quelqu'un transporte du Mana Brut. Trente secondes.
2. **Prendre ses contrats.** Trois par jour et par joueur. Ce sont les Éclats les
   plus sûrs : pas de déplacement risqué, pas de concurrence.
3. **Faire ce qu'on est venu faire.** C'est là que les profils se séparent — voir
   le paragraphe 3.
4. **Rentrer.** Déposer le Mana Brut, verser les Éclats, ranger.

Les deux premières étapes sont délibérément courtes. Un joueur qui passe vingt
minutes dans des menus avant de jouer ne revient pas.

### 2.2. Les contrats, et pourquoi ils sont trois

Trois contrats par joueur et par jour, avec trois natures différentes :

| Contrat | Objectif | Éclats | Pour qui |
|---|---:|---:|---|
| Tuer des ennemis | 5 joueurs adverses | 10 | Le combattant |
| Capturer un Autel | 1 capture complète | 15 | Le groupe |
| Survivre en zone hostile | 10 minutes en wilderness ou claim ennemi | 8 | Tout le monde |

Le troisième est le plus important du lot. Il est la voie de repli : un joueur seul,
sans groupe, qui ne veut pas de PvP, peut toujours le remplir. C'est l'application
du principe *on peut briller sans être bon en PvP* — si les trois contrats
demandaient du PvP, le principe serait un slogan.

Le contrat de survie se remet à zéro à la mort. C'est ce qui l'empêche d'être du
temps d'attente déguisé.

---

## 3. Les boucles par profil

Cinq profils. Ils ne sont pas des classes : un joueur passe de l'un à l'autre selon
sa semaine. Ce qui compte, c'est qu'aucun des cinq ne soit un citoyen de seconde
zone.

### 3.1. Le combattant

**Ce qu'il fait en une heure.** Contrats de kill et de survie, sortie d'Autel,
chasse aux espèces élites, embuscade sur un convoi adverse.

**Ce qui le fait progresser.** Ses sorts, sa baguette, son niveau d'école. Les
Éclats qu'il rapporte montent le Nexus de son Coven.

**Ce qu'il ne peut pas faire seul.** Tenir un Autel dans la durée, monter un Nexus,
gagner une guerre.

**Le risque de conception à surveiller.** Qu'il devienne la seule voie rentable. Si
le PvP rapporte nettement plus que tout le reste, le principe 2.2 de la
[vision](../01-projet/vision-et-positionnement.md) tombe.

### 3.2. Le bâtisseur

**Ce qu'il fait en une heure.** Étendre la ville, monter les bâtiments que le Nexus
a débloqués, fortifier avant une guerre.

**Ce qui le fait progresser.** Les déblocages de ville, qui suivent le niveau du
Nexus. Il a donc besoin que d'autres rapportent des Éclats — et eux ont besoin de
ses murs.

**Le piège.** Un bâtisseur n'a structurellement aucune source d'Éclats propre. Le
contrat de survie et les quêtes sont ses deux voies, et il faut qu'elles restent
praticables sans combat.

**Ce qu'il apporte au Coven.** Une ville défendable, et le seul contenu du serveur
qui survit au reset d'Ère.

### 3.3. Le marchand

**Ce qu'il fait en une heure.** Suivre le Forgeron, acheter du Mana Brut à ceux qui
n'osent pas le transporter, organiser l'hôtel des ventes, vendre des matières de
forge issues du butin.

**Ce qui le fait progresser.** La banque de son Coven, son stock, ses relations.

**Pourquoi ce profil existe.** Parce que le Mana Brut se **porte** et se perd à la
mort. Dès qu'une ressource a un risque de transport, quelqu'un trouve plus rentable
de l'acheter que de l'aller chercher. Le marchand n'a pas été conçu : il est une
conséquence, et on l'a laissé exister.

**Sa fragilité.** Il dépend entièrement du Forgeron. Si le Forgeron devient trop
commode, le marchand disparaît.

### 3.4. Le mage

**Ce qu'il fait en une heure.** Monter ses écoles, chercher des Tomes, affiner sa
barre de sorts, apprendre les sorts des autres écoles.

**Ce qui le fait progresser.** Son Essence Arcane, ses niveaux d'école, ses
baguettes, ses sceaux.

**La particularité.** C'est le seul profil dont toute la progression **survit au
reset d'Ère**. Les sorts appris restent. C'est voulu : un joueur qui a passé une Ère
à apprendre ne doit pas recommencer.

**Le risque.** Que la magie devienne un passage obligé pour tout le monde, ce qui
ferait de chaque joueur un mage. Les sorts coûtent de l'Essence et s'incantent
précisément pour qu'ils ne remplacent pas l'épée.

### 3.5. Le chef de Coven

**Ce qu'il fait en une heure.** Lire les journaux, répartir les rôles, décider des
dépenses du Nexus, négocier, choisir quand déclarer la guerre.

**Ce qui le fait progresser.** Rien, personnellement. Et c'est un problème de
conception connu.

**Le problème.** Le chef de Coven porte la charge la moins amusante et n'a aucune
récompense propre. Les seules contreparties actuelles sont le titre de vainqueur
d'Ère et l'autorité. C'est mince.

Tout ajout de contenu qui donne une raison de jouer ce rôle est prioritaire.

---

## 4. La boucle hebdomadaire

À l'échelle de la semaine, les décisions deviennent celles du Coven et non plus
celles du joueur.

| Ce qu'on fait | Pourquoi cette échelle |
|---|---|
| **Tenir ou contester des Autels** | La recharge après capture se compte en heures ; une campagne d'Autels se pense sur plusieurs jours |
| **Monter le Nexus d'un palier** | Le coût en Éclats croît avec le niveau : passé les premiers, un palier est un objectif de semaine |
| **Organiser un gros convoi** | Un convoi massif demande une escorte, donc des gens disponibles en même temps |
| **Négocier** | Une alliance ou une trêve se discute, et ne sert que si elle tient quelques jours |
| **Préparer ou mener une guerre** | Déclarer coûte ; il faut des murs, des pouvoirs rechargés, et du monde |

### Le rythme des pouvoirs de Coven

Les quatre pouvoirs — Bouclier, Portail, Malédiction, Cataclysme — ont des
recharges longues, comptées en heures ou en jours. C'est ce qui les rend des
**décisions** et non des outils : on ne lance pas un Bouclier parce qu'on l'a, on le
lance parce que c'est le bon moment.

Pendant le Grand Cataclysme, les recharges sont divisées par deux. Les trois
derniers jours d'une Ère sont donc les seuls où les pouvoirs s'enchaînent, et c'est
ce qui rend cette phase si différente.

---

## 5. La boucle par Ère

Quarante-cinq jours, trois temps.

### 5.1. L'ouverture — jours 1 à 7

Tout le monde repart du premier niveau de Nexus et d'Autels sans propriétaire. Les
claims, les constructions et le stuff sont restés.

C'est la seule fenêtre où un nouveau Coven peut s'établir sur un pied d'égalité.
Conséquence : **les premiers jours d'une Ère sont le meilleur moment pour inviter
des joueurs**, et c'est là que la communication doit se concentrer.

### 5.2. L'installation — jours 8 à 42

Le cœur du jeu. Les Covens montent leur Nexus, se disputent les Autels, font leurs
routes de convoi, et la diplomatie se fixe.

Le risque de cette phase est la **stagnation** : au bout de trois semaines, les
rapports de force sont connus et plus personne ne prend de risque. Deux choses
existent contre ça — les occurrences planifiées (boss, nuées, coffres tombés) et
les quêtes, qui donnent une raison d'aller ailleurs.

C'est aussi la phase où un Coven dominant peut décourager les autres. Les plafonds
de bonus d'Autel sont là pour ça : tenir cinq Autels du même type ne donne pas cinq
fois le bonus.

### 5.3. Le Grand Cataclysme — jours 43 à 45

Autels triplés, recharges divisées, Colosse. Trois jours où tout se rejoue.

C'est la fenêtre de rattrapage, et elle est courte exprès — voir
[Chronologie des Ères](../02-univers/chronologie-des-eres.md#le-grand-cataclysme).

### 5.4. La clôture

Le vainqueur entre au Panthéon, les compteurs retombent, et l'Ère suivante ouvre.

---

## 6. La boucle du nouveau joueur

La plus importante, et celle qu'on juge le plus durement. Détail complet dans
[Premiers pas](../04-jouer/premiers-pas.md).

| Temps | Ce qui se passe | Ce qu'il doit avoir compris |
|---|---|---|
| 0 à 2 min | Cinématique d'arrivée, choix de l'école | Où il est, et qu'il a une voie |
| 2 à 10 min | Spawn, premiers pas, le Forgeron | Qu'il y a quelqu'un à qui parler |
| 10 à 30 min | La première quête de trame, ses premiers sorts | Comment on lance un sort, et qu'il progresse |
| 30 à 60 min | Un premier groupe de gobelins, un contrat | Qu'une troupe a une forme, et qu'il y a une récompense quotidienne |
| 1 à 3 h | Un Coven, ou la décision d'en fonder un | Que le jeu se joue à plusieurs |

**Le point de rupture est à dix minutes.** Si à dix minutes le joueur n'a rien à
faire et personne à qui parler, il part. C'est pour ça que le Forgeron donne la
première quête, et pour ça que la trame commence par « présentez-vous » et non par
un combat.

---

## 7. Vérifier qu'un ajout a sa place

Les questions à se poser avant d'ajouter du contenu. Si aucune réponse n'est
satisfaisante, l'ajout occupe du temps de développement sans occuper un joueur.

1. **Dans quelle boucle ça tombe ?** Quotidienne, hebdomadaire, par Ère, ou
   première heure. Un contenu qui ne tombe dans aucune est un contenu qu'on voit
   une fois.
2. **Pour quel profil ?** Si c'est pour le combattant, est-ce qu'il en a besoin ?
   Si c'est pour le chef de Coven, c'est prioritaire.
3. **Qu'est-ce que ça rapporte en une heure ?**
4. **Est-ce que ça demande un groupe ?** Si oui, combien de personnes, et à quelle
   heure ? Un contenu à huit joueurs simultanés n'existera pas.
5. **Est-ce que ça survit au reset d'Ère ?** Les deux réponses sont bonnes, mais il
   faut l'avoir décidé : ça détermine si c'est une domination ou un parcours.
6. **Qu'est-ce que ça remplace ?** Un nouveau contenu plus rentable que l'existant
   ne s'ajoute pas, il substitue.

---

## À lire ensuite

- [Progression et jalons](progression-et-jalons.md) — les courbes derrière ces boucles
- [Économie](economie.md) — ce qui circule
- [Premiers pas](../04-jouer/premiers-pas.md) — la première heure, en détail
- [GDD global](gdd.md) — l'intention générale
