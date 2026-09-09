# Vision et positionnement

Ce que WizardMC est, ce qu'il n'est pas, et à qui il s'adresse. À lire avant
toute décision de conception : c'est ici que se tranchent les arbitrages dont les
autres documents ne font qu'appliquer la conséquence.

---

## 1. La phrase

> **WizardMC — Les Terres Fracturées** est un serveur Minecraft 1.7.10 Semi-RPG
> où les joueurs forment des **Covens**, bâtissent des villes, apprennent la
> magie et se disputent un monde qui change tous les quarante-cinq jours.

Trois mots portent tout le reste.

**Semi-RPG.** Il y a des niveaux, des écoles de magie, des quêtes et un bestiaire
— mais le monde reste celui de Minecraft, et on y construit. Un joueur qui veut
seulement bâtir doit pouvoir passer une Ère entière à bâtir sans se sentir en
retard. Un joueur qui veut seulement se battre doit trouver à qui parler pour
obtenir un mur autour de sa base.

**Coven.** L'unité de jeu n'est pas le joueur seul, c'est le groupe. Presque toute
la progression durable — le Nexus, les Autels tenus, la banque, les pouvoirs — est
une propriété du Coven et pas d'un individu. On peut jouer seul ; on joue moins
bien.

**Quarante-cinq jours.** Rien n'est acquis pour toujours. À chaque Ère, les
compteurs de domination retombent et l'histoire avance d'un cran. Ce qu'on a
construit reste ; ce qu'on a gagné se rejoue.

---

## 2. Les cinq principes

Ils sont classés. Quand deux principes s'opposent, le plus haut gagne.

### 2.1. Aucun avantage payant

Pas « peu d'avantage payant ». Aucun. Rien dans la boutique ne donne d'Éclat, de
Mana Brut, de niveau de Nexus, de sort de combat, de statistique, ni de
raccourci vers une condition de victoire.

Ce que la boutique vend : des cosmétiques, du confort qui ne change pas l'issue
d'un affrontement, et un Pass d'Ère dont les récompenses sont cosmétiques.

Le test à appliquer devant n'importe quelle idée de boutique : *un joueur qui
n'achète rien perd-il quelque chose face à un joueur qui achète tout ?* Si la
réponse est oui, l'idée est refusée, même si elle rapporte.

Voir [Boutique et Pass d'Ère](../04-jouer/boutique-et-pass.md) et
[cdc_boutique](../90-specifications/cdc_boutique.md).

### 2.2. On peut briller sans être bon en PvP

Le PvP existe, il est parfois décisif, et il n'est jamais la seule voie. Un Coven
peut dominer une Ère par l'économie, par la diplomatie, ou en tenant des Autels
que personne ne vient contester parce qu'on a su négocier.

Conséquence pratique : chaque source de puissance doit avoir au moins une voie
d'accès non combattante. Les Éclats viennent des contrats *et* des Autels. Le Mana
Brut se gagne en convoi *ou* s'achète à un autre Coven. Les montures se débloquent
par les quêtes et jamais par le classement.

### 2.3. La progression est double

Une courbe personnelle — les écoles de magie, les sorts, les baguettes, les
compagnons — et une courbe collective — le Nexus, la ville, les pouvoirs, le
territoire. Elles se rejoignent sans se remplacer : un mage de haut niveau dans
un Coven sans Nexus reste vulnérable, un Coven de niveau 10 sans mages ne perce
aucune défense.

Voir [Progression et jalons](../03-gdd/progression-et-jalons.md).

### 2.4. Le monde est lisible

Un joueur doit pouvoir comprendre ce qui lui arrive. Une créature qui frappe doit
montrer son geste avant que les dégâts tombent. Un Autel en cours de capture doit
s'annoncer à tout le serveur. Un pouvoir de Coven doit avoir un effet visible
depuis l'extérieur.

Cette exigence a un coût technique assumé : les gestes des créatures sont
chorégraphiés et synchronisés sur le serveur, précisément pour qu'une attaque
reste esquivable. Un dégât sans télégraphe est un défaut, pas une difficulté.

### 2.5. Le client est obligatoire, et il le mérite

WizardMC exige un client MCP modifié. C'est une barrière à l'entrée, et elle ne
se justifie que si elle rend le jeu meilleur : modèles 3D animés pour les
créatures et les compagnons, interface de magie, journal de quêtes, frontières de
claim affichées, effets de sorts.

Conséquence : aucune mécanique de jeu ne doit dépendre du client pour être
*équitable*. Le client montre, le serveur décide. Un client modifié ne doit
pouvoir ni se donner une école, ni connaître la position d'un ennemi caché, ni
sauter une étape.

---

## 3. Ce que WizardMC n'est pas

Dire ce qu'on refuse vaut mieux qu'une liste d'intentions.

| Ce n'est pas… | Pourquoi c'est important de le dire |
|---|---|
| **Un serveur Faction** | Les claims ne sont pas des bunkers : ce sont des villes qu'on visite. Le PvP libre est le cas particulier, pas la règle. |
| **Un RPG à quêtes linéaires** | La trame existe et tient en quelques heures. Le contenu durable, c'est le monde et les autres joueurs. |
| **Un serveur pay-to-win déguisé** | Voir le principe 2.1. Aucune exception n'a jamais été accordée et il n'en sera pas accordé. |
| **Un serveur hardcore** | Mourir coûte, mourir ne ruine pas. Un joueur qui perd une guerre garde sa ville et son stuff. |
| **Un projet Harry Potter** | L'univers est original. Aucun nom, aucune texture, aucun son issu d'une œuvre protégée ne sera livré, même en référence. |
| **Une copie de serveur existant** | Les emprunts mécaniques sont assumés ; l'assemblage et la fiction sont à nous. |

---

## 4. Le public

### 4.1. Le joueur que l'on vise

Un joueur Minecraft francophone, plutôt expérimenté, qui a déjà joué sur du
Faction ou du SMP et s'en est lassé. Il accepte d'installer un client pour avoir
mieux. Il joue en groupe, ou il veut en trouver un. Il a quelques heures par
semaine, pas quelques heures par jour.

Conséquence sur la conception : **une session d'une heure doit produire quelque
chose**. Un contrat rempli, un niveau de Nexus, une monture débloquée, un convoi
ramené. Un système qui n'a de sens qu'après dix heures d'affilée est un système
mal calibré.

### 4.2. Ceux qu'on n'exclut pas

- **Le joueur solo.** Il peut tout voir, tout apprendre, tout visiter. Il
  progressera moins vite sur les axes collectifs, et c'est le seul prix.
- **Le bâtisseur pur.** Les déblocages de ville lui donnent une raison de suivre
  le Nexus, et sa ville compte pour son Coven.
- **Le joueur qui arrive au milieu d'une Ère.** Il ne doit jamais être hors
  course : les compteurs de domination retombent bientôt, et les quêtes, la
  magie et le bestiaire ne dépendent pas du calendrier.

### 4.3. Ceux pour qui ce n'est pas fait

Le joueur qui veut du PvP immédiat et permanent sans contexte. Le joueur qui
refuse tout client modifié. Le joueur qui cherche à acheter sa place.

---

## 5. Les contraintes structurantes

Elles ne sont pas négociables, et elles expliquent une grande partie des choix
techniques qu'on trouvera ailleurs.

| Contrainte | Ce qu'elle impose |
|---|---|
| **Minecraft 1.7.10** | Pas d'API moderne, pas de composants de texte riches, 32 767 identifiants d'objets, entités à identifiant numérique. Tout le contenu custom passe par des paquets maison. |
| **Java 8** | Pas de `var`, pas de *records*, pas de *streams* dans le code chaud. Les bibliothèques récentes sont hors de portée. |
| **Client MCP décompilé** | Le client est un fork source, pas un mod : chaque mise à jour se distribue comme un client complet. D'où [WizardCloud](../90-specifications/cdc_wizardcloud.md). |
| **Serveur unique (pour l'instant)** | Les persistances acceptent MySQL pour préparer le multi-serveur, mais rien n'en dépend aujourd'hui. |
| **Équipe réduite** | Un système livré à moitié coûte plus cher qu'un système non commencé. D'où les statuts explicites : voir [Comment lire cette documentation](../00-lire-cette-doc.md). |

---

## 6. Comment trancher un arbitrage

Quand une décision de conception n'est pas évidente, les questions se posent dans
cet ordre, et on s'arrête à la première qui donne une réponse.

1. **Est-ce que ça crée un avantage payant ?** Si oui : refusé.
2. **Est-ce que ça rend une seule façon de jouer obligatoire ?** Si oui : il faut
   une seconde voie, sinon refusé.
3. **Est-ce que le joueur comprendra ce qui lui arrive ?** Si non : il manque un
   télégraphe, un message ou un affichage.
4. **Est-ce que ça tient dans une session d'une heure ?** Si non : découper.
5. **Est-ce qu'un client modifié peut en tirer un avantage ?** Si oui : la
   décision doit remonter au serveur.
6. **Est-ce qu'on saura l'expliquer en trois phrases dans cette documentation ?**
   Si non, c'est probablement trop compliqué pour le jeu aussi.

---

## À lire ensuite

- [Le lore](../02-univers/lore.md) — la fiction que tout cela habille
- [GDD global](../03-gdd/gdd.md) — l'application de ces principes, système par système
- [Architecture logicielle](architecture-logicielle.md) — comment c'est construit
- [Glossaire](glossaire.md) — le vocabulaire exact
