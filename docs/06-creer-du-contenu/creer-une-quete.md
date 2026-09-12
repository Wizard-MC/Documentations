# Créer une quête

Écrire une quête, ses étapes, ses objectifs et ses récompenses. C'est le contenu le plus
facile à ajouter et le plus facile à casser.

**Statut : livré.** Sept quêtes existent. Aucun code n'est nécessaire pour en ajouter.

---

## 1. Avant d'écrire

### Les questions

| Question | Pourquoi |
|---|---|
| **Trame ou annexe ?** | Une quête de trame s'insère dans une chaîne ordonnée ; une annexe est indépendante |
| **Qui la donne ?** | Le Forgeron donne tout aujourd'hui. Un nouveau donneur demande un personnage. |
| **Qu'est-ce qu'elle apprend ?** | Les trois quêtes de trame enseignent : parler, lire une troupe, explorer |
| **Combien de temps ?** | Une quête doit tenir dans une session. Voir [Boucles de jeu](../03-gdd/boucles-de-jeu.md) |
| **Débloque-t-elle une monture ?** | C'est la seule source de montures du jeu |

### La règle d'or

> **Une quête se découpe en étapes, et les étapes s'enchaînent. Les objectifs d'une même
> étape, non.**

C'est ce découpage qui permet d'écrire « va parler au forgeron, **puis** reviens » sans
montrer la fin de l'histoire dès le début.

---

## 2. L'anatomie d'une quête

### Les champs de la quête

| Champ | Ce qu'on y met | Obligatoire |
|---|---|---|
| `name` | Le titre, avec son code de couleur | oui |
| `description` | Le texte d'accroche — deux phrases maximum | oui |
| `kind` | `MAIN` ou `SIDE` | oui |
| `chapter` | Le rang dans la trame | **si `MAIN`** |
| `giver` | Qui la propose | oui |
| `turnIn` | Auprès de qui on la rend. Défaut : le donneur. | non |
| `minMastery` | La maîtrise minimale pour qu'elle soit proposée | non |
| `stages` | Les étapes | oui |
| `reward` | La récompense | oui |

### Une étape

| Champ | Ce qu'on y met |
|---|---|
| `summary` | Ce que le journal affiche pour cette étape |
| `objectives` | Les objectifs, qui se remplissent dans n'importe quel ordre |

### Un objectif

| Champ | Ce qu'on y met | Obligatoire |
|---|---|---|
| `kind` | `TALK`, `KILL`, `COLLECT`, `REACH` ou `DELIVER` | oui |
| `target` | Ce qui est visé | selon le type |
| `amount` | La quantité | oui |
| `description` | Ce que le journal affiche | oui |
| `marker` | Le point du monde, ou le personnage à suivre | **exigé pour `REACH`** |

---

## 3. Les cinq types d'objectif

| Type | `target` désigne | À savoir |
|---|---|---|
| `TALK` | L'identifiant d'un personnage | Le plus doux pour commencer une quête |
| `KILL` | Une espèce custom, ou un type de créature ordinaire | Vérifier le nom contre le catalogue |
| `COLLECT` | Une matière | Le joueur doit l'avoir, pas la donner |
| `REACH` | rien | **Un `marker` est exigé** |
| `DELIVER` | Une matière | **Les objets quittent l'inventaire à la remise** |

### Le piège de `KILL`

Une créature custom porte **deux noms** : celui de son espèce, et son type vanilla.

Un objectif `KILL` dont la cible est **vide** compte tous les morts. Ce n'est presque
jamais ce qu'on veut, et ça a déjà produit un bug : les victimes comptaient **deux fois**,
une par nom remonté.

> **Toujours nommer la cible**, sauf si on veut vraiment compter tout.

Sans WizardMobs, les objectifs qui visent une espèce custom ne peuvent pas se valider.

### Le piège de `DELIVER`

Les objets **quittent l'inventaire**. Le joueur doit les avoir sur lui au moment de
parler au destinataire, sinon il ne peut pas rendre.

Le destinataire est le `turnIn` de la quête, qui vaut le donneur par défaut.

---

## 4. Les marqueurs

| Champ | Ce qu'on y met |
|---|---|
| `world` | Le monde |
| `x`, `y`, `z` | La position |
| `follow` | Un personnage mobile, **au lieu** de `world`/`x`/`y`/`z` |
| `radius` | Le rayon de validation |
| `label` | Le nom affiché sur le chemin |

### Pourquoi ils comptent

> Le client transforme un marqueur en **chemin à l'écran**. Le texte décrit, **le point
> conduit**.

Sans marqueur, une quête qui dit « rendez-vous aux Terres Fracturées » laisse chercher au
hasard. C'est la différence entre une quête et une devinette.

### Choisir un rayon

| Rayon | Effet |
|---|---|
| 2 blocs | Il faut trouver exactement le bon endroit. Rarement l'intention. |
| **16 blocs** | Une marge raisonnable, utilisée par les quêtes livrées |
| 64 blocs | Tellement large qu'on valide sans avoir vu le lieu |

### Le `label`

Il s'affiche sur le chemin. « Le belvédère » vaut mieux que « Objectif 1 ».

### Ne jamais fixer un personnage qui se déplace

Le **Forgeron** est itinérant : WizardCore lui donne dix campements et le fait tourner.
Trois quêtes l'ont pointé à `world 0, 64, 0` — un point qui n'est aucun des dix. La flèche
conduisait à un terrain vide pendant qu'il était ailleurs, et la quête semblait cassée.

Pour un personnage mobile, écrire `follow:` au lieu des coordonnées :

```yaml
marker:
  follow: forgeron
  radius: 6.0
  label: "Le Forgeron"
```

Le serveur lui donne sa position au moment d'envoyer le journal. Deux refus à connaître :
un `follow` que le serveur ne sait pas situer **fait écarter la quête au chargement**, et
`follow` mêlé à `world`/`x`/`y`/`z` aussi — la position du moment gagne toujours, des
coordonnées écrites ne serviraient jamais.

> **Il n'y a pas de solution de repli.** Si la position n'est pas joignable — WizardCore
> absent, par exemple — aucune flèche n'est envoyée. C'est préférable à une flèche qui
> conduit ailleurs.

---

## 5. Le donneur

Désigné par son **nom affiché**, ou par son **modèle** s'il n'en a pas.

Les couleurs et les espaces ne comptent pas : `&5Vieux Forgeron` répond à
`vieux_forgeron`. C'est volontairement tolérant — un donneur qui ne répond pas parce
qu'on a mis une majuscule est un défaut très coûteux à diagnostiquer.

---

## 6. La récompense

| Champ | Ce qu'on y met |
|---|---|
| `experience` | L'expérience du joueur |
| `mastery` | La maîtrise gagnée |
| `unlocks` | Les clés de déblocage, par exemple `mount.husky` |
| `items` | La liste, chaque entrée avec `item` et `amount` |

### Calibrer sur les quêtes livrées

| Quête | Maîtrise min. | Expérience | Maîtrise |
|---|---:|---:|---:|
| *Le premier souffle* (trame 1) | — | 40 | 5 |
| *Ceux qui mènent* (trame 2) | — | 90 | 10 |
| *La sente encombrée* (annexe) | — | 70 | 5 |
| *Le belvédère* (annexe) | 10 | 60 | 5 |
| *Braises vives* (annexe) | 15 | 150 | 10 |
| *Écailles et cendres* (annexe) | 20 | 140 | 15 |
| *La veille du Nyx* (annexe) | 30 | 260 | 15 |
| *Le pacte de Chuchevent* (annexe) | 55 | 420 | 25 |

L'échelle est lisible : la récompense suit le seuil, et le seuil suit ce que les quêtes
ouvertes avant lui rapportent.

### La maîtrise est un seuil, pas une monnaie

Une quête peut exiger un `minMastery`. C'est ce qui empêche un nouveau joueur de prendre
*Écailles et cendres* avant de pouvoir tuer une Gelée de lave — sans quoi il passerait son
temps à mourir dans les profondeurs sans comprendre pourquoi.

**Calibrer le `minMastery` sur ce que les quêtes précédentes rapportent.** Un seuil trop
haut rend la quête invisible : elle reste au catalogue et ne se propose jamais.

La maîtrise d'un joueur est la **somme des récompenses `mastery` des quêtes qu'il a
réclamées** — rien d'autre, et rien de stocké à part. Les treize quêtes livrées en
distribuent 135 en tout. Un contrôle automatique rejoue la progression sur `quests.yml` et
échoue sur toute quête qu'aucun chemin n'ouvre ; le greffon avertit aussi en console au
démarrage.

### Les déblocages de montures

`unlocks` est la **seule source de montures du jeu**. Les dix montures ont chacune leur
quête.

La clé vaut `mount.<identifiant de la monture>` — **pas** le nom de l'espèce. Le Drakelet
d'or a l'identifiant `drake_gold`, donc la clé `mount.drake_gold` ; écrire `mount.drake`
n'ouvre rien, et c'est arrivé. WizardMobs avertit maintenant en console d'une monture dont
aucune quête n'accorde la clé.

Voir [Créer une monture](creer-une-monture.md).

---

## 7. Le piège qui a déjà coûté une quête

> **Une seule matière mal orthographiée dans une récompense fait écarter la quête entière
> au chargement.**

C'est arrivé : une faute de frappe sur un totem a fait disparaître un maillon complet de
la trame. La quête n'apparaissait simplement plus, sans message évident.

| À vérifier systématiquement | |
|---|---|
| Chaque nom de matière des récompenses | — |
| Chaque cible de `KILL` et `COLLECT` | Contre le catalogue de créatures et la liste des matières |
| Le `turnIn`, s'il est déclaré | Le personnage doit exister |

Les contrôles automatisés détectent ce cas. **Les lancer après toute modification.**

---

## 8. Écrire le texte

Le registre est celui du [lore](../02-univers/lore.md#le-ton) : sobre, factuel, un peu
sec.

| À faire | À éviter |
|---|---|
| Dire ce qu'il y a à faire | Dire au joueur ce qu'il doit ressentir |
| Deux phrases d'accroche | Un paragraphe |
| Laisser le joueur en tirer ce qu'il veut | La grandiloquence |
| De l'humour sec quand l'occasion se présente | L'emphase |

### Les exemples livrés

> « Le Sanctuaire vous a recueilli. Il attend quelque chose en retour. »

> « Une bande sans chef se disperse. Trouvez celui qui la tient. »

> « Le Forgeron dort mal. Ce n'est pas votre problème, mais il paie. »

> « Les gelées s'installent partout. Personne ne les regrette. »

C'est le registre visé. La dernière est la meilleure : elle dit l'objectif, donne une
raison, et se moque gentiment de lui.

### Ce qu'un texte de quête ne fait jamais

| Jamais | Pourquoi |
|---|---|
| Désigner le joueur comme élu | Les Terres ne doivent rien à personne |
| Répondre à une question ouverte du lore | Voir [les puissances muettes](../02-univers/pantheon-et-figures.md#7-les-puissances-muettes) |
| Promettre une mécanique qui n'existe pas | — |
| Faire revenir Aelindra | Elle ne revient jamais |

---

## 9. Construire une progression

### Une bonne première étape est douce

La première quête de la trame commence par **parler**, pas par combattre. À dix minutes,
un joueur a besoin de quelqu'un avant d'avoir besoin d'un adversaire.

### Une bonne quête de trame enseigne quelque chose

| Quête | Ce qu'elle enseigne |
|---|---|
| *Le premier souffle* | Qu'il y a quelqu'un à qui parler, et qu'on se bat |
| *Ceux qui mènent* | Qu'une troupe a une forme, et un chef |
| *Ce qui dort dans les fanges* | Qu'il faut aller voir ailleurs |

### Les annexes se prennent dans n'importe quel ordre

Donc chacune doit se tenir seule. Une annexe qui suppose qu'on a fait une autre annexe
est une quête de trame mal rangée.

---

## 10. Essayer

| À vérifier | Comment |
|---|---|
| Elle est proposée | Parler au donneur |
| Le journal affiche l'étape courante | Touche **L** |
| Le chemin s'affiche | `/quest track` |
| Chaque objectif se valide | Les faire un par un |
| L'ordre des étapes est respecté | Ne pas pouvoir sauter |
| La récompense arrive | `/quest claim` |
| Le déblocage fonctionne | `/monture list` |
| Elle n'apparaît plus après | — |

Recharger avec `/quest reload`, permission `wizardmc.quest.admin`.

---

## 11. Ce qui ne se fait jamais

| Jamais | Pourquoi |
|---|---|
| **Renommer l'identifiant** d'une quête déjà en jeu | La progression des joueurs devient orpheline. Créer une nouvelle entrée. |
| Deux quêtes de trame au même `chapter` | Deux maillons proposés en même temps |
| Un `REACH` sans `marker` | Rien à atteindre |
| Un point fixe posé sur un personnage itinérant | La flèche conduit à un endroit vide |
| Un `minMastery` supérieur à ce que les quêtes ouvertes avant lui rapportent | La quête n'apparaît jamais |
| Un objectif `KILL` sans cible, sauf intention | Il compte tous les morts |

---

## 12. La liste complète

| # | Étape |
|---|---|
| 1 | Décider trame ou annexe, et ce que la quête enseigne |
| 2 | Découper en étapes — les étapes s'enchaînent, les objectifs non |
| 3 | Écrire les objectifs, avec un `marker` dès qu'il faut se déplacer — `follow:` si la cible se déplace |
| 4 | **Vérifier chaque nom de matière et chaque cible** |
| 5 | Calibrer la récompense sur les quêtes voisines |
| 6 | Écrire le texte dans le registre du lore |
| 7 | Recharger, essayer chaque objectif |
| 8 | Lancer les contrôles |
| 9 | Documenter dans le [catalogue des quêtes](../07-reference/catalogue-quetes.md) |
| 10 | Une branche, un commit, une *pull request* |

---

## À lire ensuite

- [WizardQuest](../05-operer/configuration/wizardquest.md) — chaque champ en détail
- [Catalogue des quêtes](../07-reference/catalogue-quetes.md) — les treize quêtes livrées
- [Créer une monture](creer-une-monture.md) — pour un nouveau déblocage
- [Quêtes et montures](../04-jouer/quetes-et-montures.md) — le point de vue du joueur
