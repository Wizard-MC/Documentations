# Quêtes et montures

La trame, les annexes, et les montures qu'elles débloquent. C'est la seule voie du jeu
entièrement accessible à un joueur seul, et la seule source de montures.

**Statut : livré partiellement.** Trois quêtes de trame, quatre annexes, dix montures
déclarées — mais **trois seulement s'obtiennent réellement**. Le contenu de quêtes est
volontairement court pour l'instant. Les écarts sont détaillés au §7 et suivis en
[E-01 et E-02](../01-projet/etat-du-serveur.md#3-les-écarts-entre-le-code-et-les-documents).

---

## 1. Les deux natures de quête

| Nature | Comment elle se prend |
|---|---|
| **Principale** (trame) | Une chaîne ordonnée par chapitre. **Un seul maillon est proposé à la fois.** |
| **Annexe** | Se prend et se laisse dans n'importe quel ordre |

La trame est courte — trois chapitres — et c'est assumé : le contenu durable de
WizardMC, c'est le monde et les autres joueurs. La trame sert à apprendre et à
rencontrer le Forgeron.

---

## 2. Comment une quête est faite

Trois niveaux, et la distinction compte :

| Niveau | Règle |
|---|---|
| **La quête** | Un donneur, une récompense, un ou plusieurs étapes |
| **L'étape** | Les étapes s'enchaînent **dans l'ordre écrit** |
| **L'objectif** | Les objectifs d'une même étape se remplissent **dans n'importe quel ordre** |

C'est ce découpage qui permet d'écrire « va parler au forgeron, puis reviens » sans
montrer la fin de l'histoire dès le début.

### Les cinq types d'objectif

| Type | Ce qu'il demande |
|---|---|
| **TALK** | Parler à un personnage |
| **KILL** | Abattre une espèce custom, ou un type de créature ordinaire |
| **COLLECT** | Ramasser une matière |
| **REACH** | Se rendre quelque part |
| **DELIVER** | Remettre des objets. **Ils quittent l'inventaire à la remise.** |

### Les marqueurs

Un objectif peut porter un point du monde, que le client transforme en **chemin à
l'écran**.

Sans marqueur, une quête qui dit « rendez-vous aux Terres Fracturées » laisse
chercher au hasard. Le texte décrit, le point conduit. Les objectifs de type REACH en
exigent un.

### Le donneur et la remise

Le **donneur** propose la quête. La **remise** est celui auprès de qui on la rend, et
c'est le donneur par défaut.

Un donneur est désigné par son nom affiché, ou par son modèle s'il n'en a pas. Les
couleurs et les espaces ne comptent pas.

---

## 3. La trame

Trois chapitres, tous donnés par le **Forgeron**.

### Chapitre 1 — *Le premier souffle*

> « Le Sanctuaire vous a recueilli. Il attend quelque chose en retour. »

Se présenter au Forgeron, puis abattre cinq gobelins.

**Récompense** : 40 d'expérience, 5 de maîtrise, trois lingots de fer et huit pains.

La première étape est de **parler**, pas de combattre. C'est délibéré : à dix
minutes, un joueur a besoin de quelqu'un avant d'avoir besoin d'un adversaire.

### Chapitre 2 — *Ceux qui mènent*

> « Une bande sans chef se disperse. Trouvez celui qui la tient. »

Trois étapes. La leçon de la bande gobeline, appliquée.

**Récompense** : 90 d'expérience, 10 de maîtrise, un totem de bande — et **le
Husky**, la première monture.

### Chapitre 3 — *Ce qui dort dans les fanges*

> « Quelque chose remue sous les marais. Allez voir. »

Trois étapes, dont un déplacement et une collecte.

**Récompense** : expérience, maîtrise, deux diamants — et **le Crabe de jade**.

---

## 4. Les annexes

Quatre, toutes données par le Forgeron. Elles ne se suivent pas.

| Quête | Ce qu'elle demande | Récompense notable |
|---|---|---|
| ***Vermine des caves*** | Abattre des gelées | Boules de gelée |
| ***Un lit de plumes*** | Ramasser, puis **remettre** au Forgeron | Émeraudes |
| ***Le belvédère*** | Atteindre une crête | **Le Corbeau des augures** |
| ***Écailles et cendres*** | Réunir quatre noyaux en fusion | **Le Drakelet d'or** |

*Un lit de plumes* est la seule quête qui utilise DELIVER : les objets quittent
l'inventaire. Il faut les avoir sur soi au moment de rendre.

*Écailles et cendres* demande une maîtrise minimale : les noyaux en fusion tombent de
la Gelée de lave, qu'il faut pouvoir tuer.

> « Le Forgeron dort mal. Ce n'est pas votre problème, mais il paie. »
> — *Un lit de plumes*

C'est le registre : sobre, factuel, et un peu sec.

---

## 5. Ce qu'une quête rapporte

| Récompense | À quoi ça sert |
|---|---|
| **Expérience** | La progression du joueur |
| **Maîtrise** | Ce qui ouvre les quêtes suivantes et certaines annexes |
| **Objets** | Matières, parfois un objet unique |
| **Déblocages** | Les montures, et rien d'autre |

La **maîtrise** est un seuil, pas une monnaie : une quête peut exiger un minimum pour
être proposée. C'est ce qui empêche un nouveau joueur de prendre *Écailles et cendres*
avant de pouvoir tuer une Gelée de lave.

---

## 6. Le journal

**L** par défaut. Il affiche :

| | |
|---|---|
| Les quêtes en cours | Avec l'étape courante et ce qui reste |
| Les quêtes suivies | Celles dont le chemin s'affiche à l'écran |
| Les quêtes terminées | L'historique |
| Les quêtes disponibles | Ce qu'on peut prendre |

### Le suivi et le faisceau

Suivre une quête fait apparaître un **chemin à l'écran** vers son objectif courant.
C'est le mécanisme qui rend les marqueurs utiles, et on peut le désactiver dans les
réglages du client.

### Les commandes

| Commande | Ce qu'elle fait |
|---|---|
| `/quest` — ou `/quetes`, `/journal` | Ouvre le journal |
| `/quest list` | La liste |
| `/quest info <quête>` | Le détail |
| `/quest accept <quête>` | Accepter |
| `/quest track <quête>` | Suivre — affiche le chemin |
| `/quest abandon <quête>` | Abandonner |
| `/quest claim` | Réclamer une récompense |

`/quest reload` exige la permission `wizardmc.quest.admin`.

---

## 7. Les montures

### Les dix montures

| Monture | Rareté | Vitesse | Vol | Comment on l'obtient |
|---|---|---:|---|---|
| **Husky** | Commune | 0,28 | non | Trame, chapitre 2 |
| **Yak des cimes** | Commune | 0,26 | non | — |
| **Crabe de jade** | Rare | 0,30 | non | Trame, chapitre 3 |
| **Renard des braises** | Rare | 0,42 | non | — |
| **Ours brun** | Rare | 0,34 | non | — |
| **Murmure-de-givre** | Épique | 0,46 | non | — |
| **Ours des glaces** | Épique | 0,34 | non | — |
| **Corbeau des augures** | Épique | 0,50 | **oui** | *Le belvédère* |
| **Drakelet d'or** | Légendaire | 0,58 | **oui** | *Écailles et cendres* ⚠️ |
| **Griffon** | Légendaire | 0,62 | **oui** | — |

> « Il marche de travers. On s'y fait. » — le Crabe de jade
>
> « Ni tout à fait aigle, ni tout à fait lion, et fier des deux. » — le Griffon

### Six d'entre elles ne s'obtiennent pas

Les six lignes marquées « — » n'ont **aucune quête qui les accorde**. Tant que
WizardQuest est actif, elles restent fermées : il n'existe pas d'autre voie.

Et le **Drakelet d'or** ne s'ouvre pas non plus. *Écailles et cendres* accorde la clé
`mount.drake`, alors que la monture attend `mount.drake_gold` : la quête aboutit, la
monture reste verrouillée, et rien ne le signale.

Trois montures sur dix sont donc réellement accessibles : le Husky, le Crabe de jade et
le Corbeau des augures. C'est un défaut connu, pas une intention de conception — voir
[`cdc_wizardquest`](../90-specifications/cdc_wizardquest.md#11-écarts-connus-entre-ce-document-et-le-code).

### Ce que la rareté veut dire

**Rien sur ce que la monture fait.** La rareté dit ce qu'elle a coûté à obtenir, et
décide de sa couleur d'affichage.

La vitesse et le vol sont écrits monture par monture. Lier la vitesse à la rareté
aurait retiré le seul moyen de faire un yak de trait — lent et commun — ou un crabe
pesant.

### La vitesse est bornée

Entre 0,20 et 0,85. En dessous, on dépasse sa monture à pied. Au-dessus, on dépasse le
terrain que le serveur charge devant soi : on traverse des morceaux de monde vides, et
l'anti-triche renvoie en arrière.

### Une monture a une apparence fixe

Une bête sauvage tire son apparence au sort. Une monture, non : **un joueur qui a
mérité l'ours blanc n'accepterait pas d'en recevoir un gris un jour sur quatre**.

C'est pourquoi l'Ours brun et l'Ours des glaces sont deux montures distinctes de la
même espèce, et non une monture à deux apparences.

### Les montures ne s'achètent pas

Elles viennent des quêtes, jamais du classement, jamais de la boutique. C'est
l'application directe du principe : un joueur seul peut obtenir les dix.

Sans WizardQuest, toutes les montures sont ouvertes — c'est le comportement de repli,
pas une intention.

### Les appeler

**M** par défaut ouvre la roue des montures. Ou :

| Commande | Ce qu'elle fait |
|---|---|
| `/monture` — ou `/mount`, `/montures` | La liste |
| `/monture <nom>` | Appeler une monture |
| `/monture dismiss` | La renvoyer |
| `/monture list` | Les montures débloquées |

### Les montures volantes

Trois volent : le Corbeau, le Drakelet et le Griffon. Ce sont aussi les trois plus
rapides du jeu.

---

## 8. Les pièges

| Piège | Ce qui se passe | Quoi faire |
|---|---|---|
| Prendre une annexe trop tôt | Certaines exigent une maîtrise minimale | Avancer la trame d'abord |
| Rendre une quête DELIVER sans les objets | Les objets doivent être sur soi | Vérifier l'inventaire avant de parler |
| Chercher un objectif sans suivre la quête | Pas de chemin à l'écran | `/quest track` |
| Abandonner une quête de trame | Le chapitre se reprend, mais on perd la progression de l'étape | Inutile de l'abandonner pour en prendre une autre : les annexes sont indépendantes |
| Attendre une monture de la boutique | Aucune ne s'y vend | Les quêtes |
| Croire qu'une monture légendaire est meilleure | La rareté ne dit que le coût d'obtention | Lire la vitesse |

---

## À lire ensuite

- [Catalogue des quêtes](../07-reference/catalogue-quetes.md) — les valeurs exactes
- [Créer une quête](../06-creer-du-contenu/creer-une-quete.md) — en écrire une
- [Créer une monture](../06-creer-du-contenu/creer-une-monture.md) — en ajouter une
- [Combat et créatures](combat-et-creatures.md) — pour les objectifs KILL
