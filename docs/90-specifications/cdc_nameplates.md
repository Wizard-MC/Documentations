# CDC TECHNIQUE — Plaques de nom

> La plaque au-dessus de la tête d'un joueur : son **titre**, son **nom**, son **coven** et
> son **rôle**. Greffon `WizardTags` côté serveur, rendu dans le mod MCP côté client.

**État : spécifié, non commencé.** Aucune ligne n'existe — ni le greffon, ni le rendu. La
plaque affichée aujourd'hui est celle du jeu, intacte : `RenderPlayer.func_96449_a` dessine
le score du tableau de score puis le nom, et rien d'autre.

Documents liés :

- [`cdc_covens.md`](cdc_covens.md) — d'où viennent le tag de coven et le rôle
- [`cdc_wizardquest.md`](cdc_wizardquest.md) — d'où viennent les tags gagnés
- [`cdc_exp_client.md`](cdc_exp_client.md) — le HUD, qui n'est pas la plaque
- [`plages-d-identifiants.md`](../07-reference/plages-d-identifiants.md) — le paquet **134**

---

## 1. Objet et intention

Une plaque de nom MMORPG ne dit pas qui vous êtes : elle dit **ce que vous avez fait**. Un
joueur croisé dans un couloir de Throne & Liberty ou sur une place d'Eorzea se lit en une
seconde — sa guilde, son titre, son rang — et cette lecture décide d'une approche, d'un
salut, d'une fuite.

La plaque du jeu, elle, ne porte qu'un pseudo. Sur un serveur où l'on tient des terres en
Coven, où l'on gravit une maîtrise et où l'on termine une trame de huit chapitres, c'est
gâcher la seule surface que tout le monde regarde déjà.

Trois règles de conception commandent le reste :

| Règle | Pourquoi |
|---|---|
| **La plaque ne ment jamais** | tout ce qu'elle affiche vient du serveur. Un client ne peut ni inventer un titre, ni s'attribuer un rang |
| **Trois sources, trois pannes indépendantes** | le rang, le coven et le tag viennent de trois systèmes différents. L'un tombe, les deux autres s'affichent |
| **Illisible vaut moins que rien** | trente joueurs au Sanctuaire, c'est trente plaques de trois lignes. La densité est une contrainte de conception, pas un réglage de confort |

**Ce que la plaque n'est pas.** Ce n'est pas le HUD de Coven (`C-07` de
[`cdc_exp_client.md`](cdc_exp_client.md)), qui parle de *votre* coven dans un panneau. Ce
n'est pas la liste des joueurs (tabulation). Ce n'est pas un système de progression : elle
**affiche** des états que d'autres systèmes produisent, et n'en produit aucun — sauf les
tags, qui n'ont pas d'autre raison d'exister.

---

## 2. Les trois informations, et leur provenance

| Sur la plaque | D'où ça vient | Par quel chemin |
|---|---|---|
| **Rang** | LuckPerms | groupe principal et méta (`prefix`, `suffix`, poids) |
| **Coven et rôle** | WizardCovens | le coven du joueur, son `role_id`, le `tag` 2–5 caractères |
| **Tag** | `WizardTags` lui-même | le catalogue, et ce que le joueur a gagné |
| **Nom affiché** | le serveur | `getDisplayName()`, couleurs retirées de ce qui n'est pas voulu |

Les trois sont **indépendants**. C'est le point le plus important du document, et il
commande l'architecture : un joueur sans coven, sans tag et sans groupe LuckPerms doit
quand même avoir une plaque — avec son nom seul, comme aujourd'hui.

### 2.1. Le rang vient de LuckPerms, et d'un seul endroit

LuckPerms est la source de vérité du rang. Rien d'autre ne doit en décider.

> ⚠️ **Un rang existe déjà ailleurs, et c'est le piège de ce chantier.** Le bridge Discord
> résout un rang de son côté — `PlayerTagResolver` interroge le groupe principal Vault, puis
> une liste ordonnée de règles `rank_permissions`, puis l'équipe du tableau de score. Si la
> plaque lit autre chose, **deux rangs divergeront** : le même joueur sera « Archimage » dans
> le chat Discord et « Apprenti » au-dessus de sa tête, et personne ne saura lequel est faux.
>
> La plaque doit donc lire **le même rang que le bridge**, ou le bridge doit être rebranché
> sur la même source. Le §11 en fait une décision à prendre avant d'écrire une ligne.

LuckPerms est atteint **par réflexion**, comme WizardCore l'est depuis WizardQuest et
WizardQuest depuis WizardMobs. Ce n'est pas de la coquetterie : c'est la convention de la
stack, et elle a une raison — un greffon qui compile contre un autre ne démarre plus sans
lui, et une plaque n'est pas une raison suffisante pour empêcher un serveur de s'allumer.

L'ordre de résolution :

1. l'API LuckPerms, si elle répond ;
2. sinon Vault — LuckPerms en fournit une implémentation, et c'est déjà ce que le bridge
   utilise ;
3. sinon aucun rang : la plaque n'affiche pas de ligne de rang, et la console le dit **une
   fois** au démarrage.

> **À vérifier avant d'écrire** : quelle version de LuckPerms tourne réellement sur un
> serveur 1.7.10, et si son API y est exposée. Aucun dépôt de la stack ne mentionne
> LuckPerms aujourd'hui — la recherche ne remonte rien. Le repli Vault existe pour que la
> réponse ne bloque pas le chantier, mais elle doit être connue.

### 2.2. Le coven et le rôle viennent de WizardCovens

`cdc_covens.md` porte déjà tout ce qu'il faut : un `tag` de 2 à 5 caractères unique
(`uk_tag`), un `role_id` par membre, une table `wizard_coven_roles`, et un paquet
`CovenRoster` qui descend `members[{uuid, name, role, online}]`.

**Mais le roster ne suffit pas.** Il décrit *votre* coven. La plaque doit s'afficher
au-dessus de **n'importe qui** — un allié, un inconnu, un ennemi en pleine guerre — et pour
ceux-là le client ne reçoit rien. C'est la raison d'être du paquet 134 : il porte le coven
de tous ceux qu'on peut voir, et pas seulement des siens.

### 2.3. Les tags sont des décorations, et rien d'autre

**Un tag n'a aucun rapport avec un coven ni avec un rang.** C'est une récompense
d'affichage : on la gagne en finissant une quête, en franchissant un seuil, en survivant à
une Ère, et on la porte parce qu'elle se voit.

C'est le seul contenu que ce système produit lui-même, et c'est pourquoi le greffon
s'appelle `WizardTags` et non `WizardNameplates` : la plaque est la vitrine, les tags sont la
marchandise.

---

## 3. Anatomie de la plaque

```
                ⟨ Briseur d'Augures ⟩          ← tag, petit, couleur du tag
                      Yuketsu                  ← nom, grand, couleur du rang
                 [WZD] Archimage                ← coven et rôle, petit, gris
                 ▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁
```

| Ligne | Contenu | Taille | Couleur | Quand elle disparaît |
|---|---|---|---|---|
| 1 | le tag porté, entre chevrons | 0,75× | celle du tag | aucun tag porté |
| 2 | le nom affiché | 1,0× | celle du rang | jamais |
| 3 | `[TAG]` du coven, puis le rôle | 0,75× | gris, tag à la couleur du coven | pas de coven |

**Pourquoi cet ordre.** Le nom est au centre et le plus grand parce que c'est ce qu'on
cherche : l'œil y tombe sans le viser. Le tag est au-dessus, comme un titre honorifique —
c'est la place que Final Fantasy XIV lui donne, et elle se lit comme une mention plutôt
qu'une identité. Le coven est en dessous, collé au rôle, parce que les deux disent la même
chose : à qui ce joueur appartient.

L'alternative — coven en haut, à la manière de Throne & Liberty — mettrait l'appartenance
au-dessus de la personne. C'est un choix défendable pour un jeu de guildes ; sur un SMP où
l'on joue aussi seul, la personne passe devant.

### 3.1. Ce que la plaque ne montre pas

| Pas sur la plaque | Pourquoi |
|---|---|
| la vie | elle se lit sur la cible, pas au-dessus de chaque passant |
| la maîtrise ou un niveau | un chiffre invite à comparer ; le serveur n'est pas un classement |
| l'école de magie | neuf écoles, neuf couleurs de plus : la plaque devient un tableau |
| le score du tableau de score | la plaque le remplace — voir le §6.3 |

Chacune de ces lignes a été écartée volontairement. Les ajouter plus tard demandera de
revoir la densité, pas seulement le gabarit.

---

## 4. Les tags

### 4.1. Le catalogue

Un fichier `tags.yml`, sur le modèle de `mounts.yml` et de `pets.yml` :

```yaml
tags:

  briseur_d_augures:
    label: "Briseur d'Augures"
    colour: "&5"
    rarity: EPIQUE
    description: "Pour qui a vu ce que le Nyx regardait."
    requirement:
      type: QUEST
      quest: ce_que_le_nyx_a_vu

  premier_souffle:
    label: "Premier Souffle"
    colour: "&7"
    rarity: COMMUNE
    description: "Le Sanctuaire vous a recueilli, et vous avez répondu."
    requirement:
      type: QUEST
      quest: eveil

  veilleur:
    label: "Veilleur"
    colour: "&b"
    rarity: RARE
    description: "Cent nuits passées dehors."
    requirement:
      type: STATISTIC
      statistic: nights_outside
      amount: 100
```

| Champ | Ce qu'il règle |
|---|---|
| `label` | ce qui s'affiche. Sans code couleur — la couleur est un champ à part |
| `colour` | un seul code, appliqué au label entier |
| `rarity` | `COMMUNE`, `RARE`, `EPIQUE`, `LEGENDAIRE` — la couleur du cadre dans l'interface, et l'ordre de tri |
| `description` | une ligne, dans l'interface. Dit **comment on l'obtient** quand il n'est pas gagné |
| `requirement` | la condition. Voir ci-dessous |

La couleur est séparée du libellé pour la même raison que dans les quêtes engendrées : un
libellé qui porte ses propres codes donne un jeu où chaque tag a sa palette, et une plaque
qui ressemble à une enseigne de néon.

### 4.2. Comment on gagne un tag

| `type` | Condition | Qui le sait |
|---|---|---|
| `QUEST` | une quête précise est réclamée | WizardQuest, par `hasClaimed` |
| `UNLOCK` | une clé `tag.<id>` est accordée | WizardQuest, par `hasUnlocked` |
| `MASTERY` | un seuil de maîtrise | WizardQuest |
| `STATISTIC` | un compteur de jeu atteint un seuil | `WizardTags` tient le compteur |
| `ERA` | avoir participé à une Ère donnée | WizardCore |
| `SHOP` | acheté à la boutique | WizardCore, catégorie cosmétique |
| `MANUAL` | accordé à la main | un administrateur |

**`UNLOCK` est la voie la plus courte**, et elle est déjà construite. Une quête qui écrit
`unlocks: [tag.briseur_d_augures]` dans sa récompense accorde la clé ; `WizardTags` écoute
`QuestClaimedEvent`, qui porte déjà le joueur, la quête et les clés. Aucun code neuf de part
ni d'autre.

> **La leçon de Q-01 s'applique mot pour mot.** La quête *Écailles et cendres* accordait
> `mount.drake` quand la monture attendait `mount.drake_gold` : la récompense n'ouvrait rien,
> en silence, pendant des semaines. Un tag dont la clé n'est accordée par aucune quête sera
> exactement le même défaut.
>
> **Exigence** : au démarrage, `WizardTags` interroge `WizardQuest.grantableKeys()` et écrit
> en console chaque tag de type `UNLOCK` dont la clé n'est accordée par personne. C'est la
> même garde que WizardMobs pose sur les montures, pour la même raison.

### 4.3. Porter un tag

**Un seul tag porté à la fois.** C'est ce qui lui donne sa valeur : un joueur qui les
afficherait tous n'afficherait rien. Les autres restent dans sa collection, et se changent
quand il veut.

| Commande | Ce qu'elle fait |
|---|---|
| `/tags` | ouvre la collection : gagnés, et à gagner avec leur condition |
| `/tag <id>` | porte ce tag |
| `/tag off` | n'en porte aucun |

L'interface montre aussi les tags **non gagnés**, avec leur description. Cacher ce qui reste
à faire retire au système sa fonction : un tag qu'on ne sait pas pouvoir gagner ne donne
envie de rien.

### 4.4. Ce qu'un tag ne fait jamais

- **Aucun effet de jeu.** Pas de bonus, pas d'accès, pas de permission. Un tag qui ouvrirait
  une porte deviendrait un prérequis déguisé, et la boutique en vendrait un avantage.
- **Aucun texte libre.** Le libellé vient du catalogue, jamais du joueur. Un tag saisi à la
  main est une insulte qui attend son heure.
- **Aucune perte.** Un tag gagné est gagné, y compris à la fin d'une Ère. Le reset partiel
  d'Ère ne touche pas la collection.

---

## 5. Le protocole

**Paquet 134**, descendant uniquement. Le client n'a rien à demander : il reçoit, il
dessine.

> L'identifiant 134 est le suivant disponible, et la table des plages le nomme déjà comme
> tel. 133 reste réservé à WizardHub, spécifié et non livré.

### 5.1. Un dictionnaire, puis des références

La tentation est d'envoyer, pour chaque joueur, son rang, sa couleur, son coven et son tag
en clair. Trente joueurs au Sanctuaire, c'est alors trente fois les mêmes chaînes.

Le paquet porte donc **deux dictionnaires** et **des références** :

| Action | Sens | Contenu |
|---|---|---|
| `STYLES` | S→C | les rangs, les covens et les tags connus : `id`, libellé, couleur |
| `SYNC` | S→C | la plaque de tous les joueurs visibles : `uuid`, nom, `rankId`, `covenId`, `roleLabel`, `tagId` |
| `UPDATE` | S→C | une seule plaque, quand elle change |
| `REMOVE` | S→C | une plaque qui n'a plus lieu d'être |

Les dictionnaires partent **une fois** à la connexion, et se complètent au besoin : un coven
créé pendant la session arrive par un `STYLES` d'une seule entrée.

### 5.2. Quand le serveur envoie

| Moment | Ce qui part |
|---|---|
| connexion du joueur | `STYLES` complet, puis `SYNC` des joueurs visibles |
| un joueur entre en vue | `UPDATE` de sa plaque |
| changement de rang, de coven, de rôle ou de tag porté | `UPDATE` |
| un joueur quitte | `REMOVE` |

**Une plaque inconnue ne rend personne anonyme.** Si le client doit dessiner un joueur dont
il n'a pas la plaque — une trame perdue, un joueur apparu avant le `SYNC` — il dessine la
plaque du jeu. Le nom reste lisible, et le défaut se voit sans gêner.

### 5.3. Ce qui ne remonte jamais

Le client n'émet rien sur 134. Le choix du tag passe par la commande, donc par le chemin
ordinaire des commandes, qui est déjà vérifié côté serveur.

> Laisser le client annoncer son tag serait lui laisser en inventer un. C'est la première
> chose que quelqu'un essaiera.

---

## 6. Le rendu client

### 6.1. Où ça s'accroche

Deux méthodes, et les deux comptent :

| Méthode | Rôle |
|---|---|
| `RenderPlayer.func_96449_a` | décide d'afficher une plaque pour ce joueur, à cette distance |
| `Render.func_147906_a` | dessine une ligne de texte orientée vers la caméra |

La première est remplacée : au lieu du score puis du nom, elle appelle notre plaque quand
elle en a une, et retombe sur `super` sinon. La seconde est **réutilisée** ligne par ligne —
elle sait déjà faire face à la caméra, gérer la profondeur et le fond semi-transparent, et
la réécrire serait refaire moins bien.

### 6.2. La densité

C'est la contrainte qui décide si le système est agréable ou insupportable. Trente joueurs
au Sanctuaire, à trois lignes chacun, c'est quatre-vingt-dix textes orientés par image.

| Règle | Valeur | Pourquoi |
|---|---|---|
| distance maximale | 32 blocs | au-delà, un nom n'est plus lu mais deviné |
| plaque complète | sous 16 blocs | plus loin, le nom seul |
| plaques dessinées au plus | 20, les plus proches | au-delà, c'est une foule, pas des individus |
| joueur accroupi | nom seul, 8 blocs | la discrétion du jeu reste une mécanique |
| joueur visé | plaque complète, toujours | celui qu'on regarde est celui qu'on veut lire |

Les seuils sont des valeurs de départ, pas des constantes : ils vont dans les réglages
client, comme `enableCameraShake` l'est déjà (`C-06`).

### 6.3. Le score du tableau de score

La plaque le remplace et **ne le dessine plus**. La ligne que le jeu affichait — le score de
l'objectif en position 2, au-dessus du nom — occupait exactement la place que prend
maintenant le tag.

Aucun système de la stack n'utilise cet emplacement aujourd'hui. S'il devait servir un jour,
il faudrait lui trouver une quatrième ligne, et la densité du §6.2 dit déjà que ce serait une
de trop.

### 6.4. Ce qui doit rester vrai

- **La plaque ne traverse pas les murs.** Comme celle du jeu : le test de profondeur
  s'applique, et un joueur derrière un bloc ne se signale pas.
- **Elle ne s'affiche pas en vue à la première personne sur soi-même.** Évident, et tout
  aussi évident à oublier.
- **Elle ne clignote pas.** Un changement de tag ou de coven remplace la plaque sans la faire
  disparaître le temps d'une image.

---

## 7. Persistance

| Table | Ce qu'elle garde |
|---|---|
| `wizard_tags_unlocked` | `player`, `tag`, `earned_at` — ce qu'un joueur a gagné |
| `wizard_tags_worn` | `player`, `tag` — ce qu'il porte, une ligne par joueur |
| `wizard_tags_statistic` | `player`, `statistic`, `value` — les compteurs de `STATISTIC` |

Deux modes comme pour les quêtes : `JSON` par joueur pour un serveur seul, `MYSQL` dès que
plusieurs serveurs partagent les joueurs. Un hôte ou une base absents **font retomber sur
JSON** plutôt que d'échouer.

**Ce qui n'est pas stocké** : les tags dont la condition est vérifiable ailleurs. Un tag de
type `QUEST` ou `MASTERY` se recalcule en interrogeant WizardQuest, et le stocker donnerait
deux vérités à réconcilier. Seuls `STATISTIC`, `SHOP` et `MANUAL` écrivent une ligne.

---

## 8. Commandes et permissions

| Commande | Permission | Ce qu'elle fait |
|---|---|---|
| `/tags` | — | la collection |
| `/tag <id>` | — | porter |
| `/tag off` | — | ne rien porter |
| `/tag grant <joueur> <id>` | `wizardmc.tags.admin` | accorder à la main |
| `/tag revoke <joueur> <id>` | `wizardmc.tags.admin` | retirer — pour une erreur, pas pour une punition |
| `/tag reload` | `wizardmc.tags.admin` | relire `tags.yml` |

`wizardmc.tags.admin` par défaut `op`.

---

## 9. Ce que le système perd sans ses dépendances

| Sans | Conséquence |
|---|---|
| **LuckPerms** *(et Vault)* | aucune ligne de rang ; le nom s'affiche en blanc |
| **WizardCovens** | aucune ligne de coven |
| **WizardQuest** | les tags de type `QUEST`, `UNLOCK` et `MASTERY` ne se gagnent plus. Ceux déjà gagnés restent portés |
| **le mod client** | rien. Le handshake expulse les clients non modés (`C-01`), donc la question ne se pose que sur un hub ouvert |

Tout est `softdepend`. Le greffon démarre sans aucune des trois, et une plaque réduite au nom
reste une plaque.

---

## 10. Les pièges

| Piège | Ce qui se passe | Garde-fou |
|---|---|---|
| **Deux rangs divergents** | le chat Discord dit « Archimage », la plaque dit « Apprenti » | une seule source, décidée au §11 |
| **Un tag que rien n'accorde** | il existe au catalogue, s'affiche dans la collection, et ne se gagne jamais | l'avertissement de démarrage du §4.2 |
| **Un libellé de tag trop long** | la plaque dépasse le joueur et se chevauche avec celle du voisin | longueur maximale vérifiée au chargement, et le tag est écarté |
| **Un code couleur dans un libellé** | chaque tag sa palette, et une plaque illisible | `colour` est un champ à part ; un `&` dans `label` est refusé |
| **Une plaque par image recalculée** | le rang et le coven interrogés soixante fois par seconde et par joueur | la plaque est **poussée** par le serveur, jamais tirée par le client |
| **Le roster de coven pris pour source** | les plaques des ennemis restent vides | le paquet 134 porte tout le monde |

---

## 11. Ce qui reste à trancher

Trois décisions, et aucune n'est technique au point de pouvoir être prise en écrivant le
code.

1. **Qui détient le rang ?** LuckPerms est la réponse, mais le bridge Discord résout
   aujourd'hui un rang par Vault et par des règles de permissions. Soit la plaque lit la même
   chose, soit le bridge est rebranché. Laisser les deux vivre est le seul choix qui garantit
   une incohérence.
2. **Quelle version de LuckPerms tourne en 1.7.10, et son API y est-elle exposée ?** Aucun
   dépôt ne la mentionne. Le repli Vault existe, mais il faut savoir si c'est le chemin
   principal ou le chemin de secours.
3. **Combien de tags au lancement, et sur quoi ?** Un système de tags avec trois tags est une
   promesse vide. La trame en offre huit occasions naturelles, les dix montures autant, et
   les Ères une par saison — mais c'est une décision de contenu, pas de code.

---

## 12. Ce qui reste à faire

| # | Tâche | Dépend de |
|---|---|---|
| 1 | Trancher les trois points du §11 | personne |
| 2 | Greffon `WizardTags` : catalogue, conditions, collection, persistance | §11.2 |
| 3 | Paquet 134 des deux côtés, avec son contrôle de trame octet par octet | 2 |
| 4 | Rendu client : `RenderPlayer`, densité, réglages | 3 |
| 5 | Écrire les tags de lancement, et les clés `unlocks` dans les quêtes | §11.3 |
| 6 | Rebrancher le bridge Discord sur la source retenue | §11.1 |

**Le contrôle de trame du point 3 n'est pas optionnel.** Le paquet 131 des quêtes a appris la
leçon : un protocole positionnel où un champ se déplace ne lève aucune erreur, il affiche des
compteurs à la place des étapes. Les deux côtés figent l'ordre par un contrôle qui relit la
trame octet par octet, et c'est le seul garde-fou qui existe.

---

## À lire ensuite

- [`cdc_covens.md`](cdc_covens.md) — le coven, son tag et ses rôles
- [`cdc_wizardquest.md`](cdc_wizardquest.md) — les clés `unlocks`, et la leçon de Q-01
- [`cdc_exp_client.md`](cdc_exp_client.md) — le HUD, à ne pas confondre avec la plaque
- [`permissions.md`](../07-reference/permissions.md) — les préfixes de permission existants
