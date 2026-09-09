# Le Coven

Fonder un Coven, le faire vivre, tenir du territoire, négocier, et faire la guerre.
L'unité de jeu de WizardMC n'est pas le joueur seul — presque toute la progression
durable appartient au groupe.

**Statut : livré partiellement.** Le socle social tourne derrière un adaptateur ; le
greffon `WizardCovens` natif est spécifié et en cours. Voir
[cdc_covens](../90-specifications/cdc_covens.md).

---

## 1. Pourquoi un Coven

Un Coven n'est pas une armée. C'est ce qui reste quand des gens s'entendent pour
tenir un morceau de terre stable au milieu de terre qui ne l'est pas — voir
[le lore](../02-univers/lore.md#52-ce-qui-a-remplacé-les-villages).

D'où la forme qu'ils prennent : des villes, des murs, une forge, un marché. Pas des
camps.

### Ce qu'un Coven donne

| | |
|---|---|
| **Un Nexus** | Le cœur. Son niveau décide du territoire, des bâtiments, des pouvoirs. |
| **Du territoire** | Des claims protégés hors guerre |
| **Des pouvoirs** | Quatre capacités collectives à longue recharge |
| **Une banque** | Un trésor commun |
| **De la diplomatie** | Des alliés, des ennemis, des trêves |
| **Des Autels tenus** | Les bonus de type, et les Éclats |

### Ce qu'on peut faire sans

Tout ce qui est personnel : les quarante et un sorts, les neuf écoles, toute la
trame et les annexes, les dix montures, un compagnon, presque tout le bestiaire,
l'économie.

**Un joueur seul n'est pas bridé sur sa progression, il est bridé sur la
domination.** Ce qui est cohérent : dominer seul n'a pas de sens.

---

## 2. Fonder ou rejoindre

### Rejoindre

C'est presque toujours le bon choix au début. Un Coven établi a un Nexus, des murs,
et quelqu'un qui sait où sont les Autels.

On se fait inviter, puis on accepte. Un joueur n'appartient qu'à **un seul** Coven à
la fois.

### Fonder

Coûte de la monnaie de serveur. Il faut un nom unique et un tag de deux à cinq
caractères.

À faire aussitôt après :

1. **Poser le Nexus.** Sans lui, pas de territoire. Seul le chef peut le poser.
2. **Claimer autour.** Le terrain sur lequel on bâtit.
3. **Recruter.** Un Coven de une personne plafonne très vite.

### Ce qu'il faut savoir avant de fonder

| | |
|---|---|
| Un seul Nexus par Coven | Il n'y a pas de seconde base |
| Le chef ne peut pas partir | Il doit d'abord transmettre, ou dissoudre |
| Dissoudre libère les claims | Et la banque est perdue ou redistribuée selon la configuration |
| Plafond de membres | Cinquante, réglable |

---

## 3. Les rôles

Sept rôles, hiérarchisés. Ce ne sont pas des classes de jeu : ce sont des jeux de
droits.

| Rôle | Ce qu'il peut |
|---|---|
| **Leader** — le Maire | Tout. Poser et retirer le Nexus, dissoudre, déclarer la guerre. |
| **Officier** | Inviter, claimer, déclarer la guerre, retirer de la banque |
| **Mage** | Activer les pouvoirs du Nexus |
| **Garde** | Accès aux coffres de défense, reçoit les alertes d'intrusion |
| **Bâtisseur** | Construire, retirer des claims de façon limitée |
| **Marchand** | Déposer à la banque, voir les taxes, mener les convois de Mana |
| **Fermier** | Construire et ouvrir les conteneurs dans les zones de culture |

### Comment les distribuer

Le piège classique est de donner Officier à tout le monde. Un Officier peut déclarer
la guerre et vider la banque.

Ce qui marche :

- **Mage** à ceux qui jouent les pouvoirs. C'est le rôle qui donne le plus
  d'influence sur une guerre sans donner accès à la banque.
- **Garde** à ceux qui sont souvent connectés : ils reçoivent les alertes
  d'intrusion, et une alerte sans personne pour la lire ne sert à rien.
- **Marchand** à ceux qui mènent les convois. Le rôle existe pour ça.
- **Officier** à deux ou trois personnes, pas plus.

---

## 4. Le territoire

### Claimer

Un claim se prend sur le terrain où on se trouve. La quantité totale qu'un Coven
peut tenir dépend de ses membres, de son activité, et du niveau de son Nexus.

### La puissance du Coven

La capacité à tenir du territoire suit une formule qui compte les membres connectés
et l'activité. **Ce n'est pas le modèle punitif classique** où chaque mort fait
perdre du terrain : mourir peut coûter un petit malus temporaire, jamais une ville.

La raison est dans [la vision](../01-projet/vision-et-positionnement.md) : un
bâtisseur qui meurt trois fois ne doit pas voir sa ville devenir pillable.

### Ce qu'un claim protège

| Hors guerre | En guerre déclarée |
|---|---|
| Personne d'autre ne casse, ne pose, n'ouvre un coffre | Casse et pose autorisées selon la configuration |
| Le PvP y est désactivé par défaut | Le PvP y est **forcé** pour les belligérants |

Les alliés peuvent recevoir des droits selon la relation.

### Les claims n'ont pas besoin d'être contigus

Par défaut, un Coven peut avoir des parcelles séparées. C'est un choix SMP : il
permet d'avoir une ville principale et un avant-poste près d'un Autel, plutôt
qu'une seule masse qu'il faut étendre de proche en proche.

### L'`overclaim` est désactivé

On ne prend pas le terrain d'un Coven affaibli en le revendiquant. Il faut une
guerre.

---

## 5. La diplomatie

Quatre états, déclarés.

| État | Ce qu'il change |
|---|---|
| **Neutre** | Par défaut. Claims protégés des deux côtés. |
| **Allié** | Droits étendus dans les claims, selon la configuration |
| **Ennemi** | Une déclaration d'hostilité, sans ouvrir les claims |
| **Guerre déclarée** | Le seul état qui ouvre les claims au raid et force le PvP |

La distinction **ennemi / guerre déclarée** est importante : on peut être en froid
sans que les villes brûlent. C'est ce qui rend la diplomatie jouable — il existe un
cran entre la paix et l'assaut.

---

## 6. La guerre

### Déclarer

Coûte de l'argent à la banque du Coven, et il existe une recharge globale. Les deux
camps reçoivent un titre à l'écran et une bannière de guerre.

Le thème **Chaos** d'une Ère réduit ce coût d'un dixième. C'est le seul modificateur
de thème qui touche la guerre, et il reste léger.

### Ce qui change

| | |
|---|---|
| Les claims des belligérants s'ouvrent | Casse et pose selon la configuration |
| Le PvP est forcé dans ces claims | Plus de refuge |
| Les pouvoirs de Coven deviennent décisifs | C'est leur raison d'être |
| Le Nexus ennemi devient attaquable | Sous conditions strictes |

### Détruire un Nexus ennemi

La seule action de la guerre qui a un effet durable, et elle est volontairement
difficile.

| | |
|---|---|
| Uniquement en **guerre déclarée** | — |
| Une canalisation longue | Une minute par défaut, à rester près du Nexus |
| Une recharge globale pour l'attaquant | Quarante-huit heures par défaut |
| L'effet | Le Nexus du défenseur retombe au premier niveau, ou perd quelques niveaux selon la configuration |
| **Ce que ça ne fait pas** | Les claims ne sont pas effacés. La ville reste. |

La canalisation existe pour que la destruction soit **défendable** : une minute
donne au défenseur le temps d'arriver. Une destruction instantanée serait
indéfendable, donc arbitraire.

La recharge de quarante-huit heures empêche le harcèlement d'un même Coven jour
après jour.

### Sortir d'une guerre

Trois façons : la reddition, une paix mutuelle, ou l'expiration. Il existe une durée
minimale avant qu'une paix puisse être conclue — autrement, déclarer et conclure la
paix dans la minute permettrait d'ouvrir une ville le temps d'un raid.

---

## 7. La banque et les taxes

| | |
|---|---|
| **La banque** | Un solde commun. Toute opération est journalisée. |
| **Qui dépose** | Tout le monde, y compris le Marchand |
| **Qui retire** | Officier et plus |
| **Les taxes** | Optionnelles, à l'initiative du Coven |

La journalisation n'est pas une formalité : c'est ce qui permet de savoir qui a vidé
la banque, et c'est la première chose que le staff regarde en cas de litige interne.
Le staff **n'arbitre pas** les litiges internes à un Coven — voir
[Modération](../05-operer/moderation.md).

---

## 8. Le home de Coven

Un point de retour commun, posé par le chef ou un Officier, avec une recharge.

La recharge existe pour qu'il ne serve pas de fuite en combat.

---

## 9. Mener un Coven — ce qui marche

### Les quatre premières heures

1. **Poser le Nexus et claimer.** Avant tout le reste.
2. **Faire faire les contrats à tout le monde.** Trois par joueur et par jour : c'est
   le revenu le plus sûr du Coven, et il ne demande aucune organisation.
3. **Monter le Nexus au niveau 3.** Il ouvre le Bouclier et la tour de guet. La tour
   prévient des intrusions, et c'est ce qui permet de dormir.
4. **Choisir une région.** Avec un Autel, si possible.

### Les erreurs les plus coûteuses

| Erreur | Conséquence |
|---|---|
| Donner Officier à tout le monde | N'importe qui déclare la guerre ou vide la banque |
| Déclarer la guerre sans murs ni pouvoirs rechargés | On se fait raider sans pouvoir répondre |
| Négliger les contrats | On laisse le revenu le plus sûr sur la table |
| S'étaler sur trop de claims | La capacité se consomme, et on ne défend rien |
| Stocker tout dans un seul coffre | En guerre, c'est une seule cible |
| Oublier que les Autels ont une recharge | On part à sept sur un Autel qui ne donnera rien |

### Répartir les profils

Un Coven a besoin des quatre : quelqu'un qui se bat, quelqu'un qui construit,
quelqu'un qui commerce, quelqu'un qui joue la magie. Un Coven de cinq combattants
n'a pas de murs ; un Coven de cinq bâtisseurs n'a pas d'Éclats.

### Le rôle le plus ingrat

**Chef de Coven.** Il porte la charge la moins amusante — les journaux, les rôles,
les décisions de dépense, la diplomatie — et n'a aucune progression propre. C'est un
déséquilibre connu et écrit, pas un oubli : voir
[Équilibrage](../03-gdd/equilibrage.md#3-les-déséquilibres-connus).

En attendant, les deux seules contreparties sont le titre de vainqueur d'Ère et
l'autorité. Un Coven qui le sait peut au moins alléger la charge : déléguer les
journaux à un Officier, et ne pas laisser une seule personne décider de tout.

---

## 10. Ce qui retombe à la fin d'une Ère

| Retombe | Reste |
|---|---|
| Le niveau du Nexus et ses Éclats | Les claims |
| Les déblocages de ville | Les constructions |
| Les recharges des pouvoirs | Les membres |
| Les Autels tenus | La banque, selon la configuration |

La ville reste la ville. C'est le seul contenu du serveur qui survit à tout, et c'est
pour ça que construire vaut la peine.

---

## À lire ensuite

- [Nexus et ville](nexus-et-ville.md) — monter le cœur du Coven
- [Autels et Mana Brut](autels-et-mana.md) — ce qu'on va chercher dehors
- [cdc_covens](../90-specifications/cdc_covens.md) — la spécification complète
- [Chronologie des Ères](../02-univers/chronologie-des-eres.md) — ce qui arrive tous les 45 jours
