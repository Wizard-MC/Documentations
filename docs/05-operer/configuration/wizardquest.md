# Configuration de WizardQuest

Les deux fichiers du système de quêtes, champ par champ.

**Statut : livré.** Trois quêtes de trame, dix annexes, et les dix montures accordées.

---

## 1. Les deux fichiers

| Fichier | Ce qu'il porte |
|---|---|
| `config.yml` | Où vont les journaux de quête |
| `quests.yml` | Les quêtes, leurs étapes, leurs objectifs, leurs récompenses |

---

## 2. `config.yml`

Une seule section, `storage`.

| Champ | Ce qu'il règle | Défaut |
|---|---|---|
| `type` | `JSON` ou `MYSQL` | `JSON` |
| `host` | L'hôte de la base | `localhost` |
| `port` | Le port | 3306 |
| `database` | Le nom de la base | `wizardmc` |
| `username` | L'utilisateur | `root` |
| `password` | Le mot de passe — **un secret** | vide |

### Quand choisir `MYSQL`

| Support | Quand | Où ça va |
|---|---|---|
| `JSON` | Un seul serveur | Un fichier par joueur, dans `plugins/WizardQuest/journaux` |
| `MYSQL` | Plusieurs serveurs partageant les mêmes joueurs | Deux tables, `wizard_quest_progress` et `wizard_quest_tracked` |

`JSON` tient pour un serveur seul. `MYSQL` devient nécessaire dès que plusieurs
serveurs partagent les mêmes joueurs : sans base commune, un joueur qui change de
serveur y retrouve un journal vide.

### Le repli

> Un hôte ou une base absents font **retomber sur JSON** plutôt qu'échouer.

Perdre l'avancement de tout le monde pour une ligne oubliée serait cher payé. C'est le
même principe que partout ailleurs : une dépendance manquante coûte une capacité, pas
le démarrage.

---

## 3. `quests.yml`

### 3.1. Une quête

| Champ | Ce qu'il règle | Obligatoire |
|---|---|---|
| `name` | Le titre, avec ses codes de couleur | oui |
| `description` | Le texte d'accroche | oui |
| `kind` | `MAIN` ou `SIDE` | oui |
| `chapter` | Le rang dans la trame. **Obligatoire pour `MAIN`.** | selon |
| `giver` | Qui la propose | oui |
| `turnIn` | Auprès de qui on la rend. Défaut : le donneur. | non |
| `minMastery` | Maîtrise minimale pour qu'elle soit proposée | non |
| `stages` | Les étapes | oui |
| `reward` | La récompense | oui |

### Les deux natures

| Nature | Comportement |
|---|---|
| `MAIN` | Une chaîne ordonnée par `chapter`. **Un seul maillon est proposé à la fois.** |
| `SIDE` | Se prend et se laisse dans n'importe quel ordre |

### 3.2. Les étapes et les objectifs

Trois niveaux, et la distinction est le cœur du système :

| Niveau | Règle |
|---|---|
| La quête | Un donneur, une récompense |
| **L'étape** | Les étapes s'enchaînent **dans l'ordre écrit** |
| **L'objectif** | Les objectifs d'une même étape se remplissent **dans n'importe quel ordre** |

C'est ce découpage qui permet d'écrire « va parler au forgeron, puis reviens » sans
montrer la fin de l'histoire dès le début.

Une étape porte :

| Champ | Ce qu'il règle |
|---|---|
| `summary` | Ce que le journal affiche pour cette étape |
| `objectives` | Les objectifs |

### 3.3. Un objectif

| Champ | Ce qu'il règle | Obligatoire |
|---|---|---|
| `kind` | `KILL`, `COLLECT`, `REACH`, `TALK` ou `DELIVER` | oui |
| `target` | Ce qui est visé — voir ci-dessous | selon |
| `amount` | La quantité | oui |
| `description` | Ce que le journal affiche | oui |
| `marker` | Le point du monde | selon |

### Les cinq types

| Type | Ce que `target` désigne |
|---|---|
| `KILL` | Une espèce custom, ou un type de créature ordinaire |
| `COLLECT` | Une matière |
| `REACH` | Rien — **un `marker` est exigé** |
| `TALK` | L'identifiant d'un personnage |
| `DELIVER` | Une matière. Le destinataire est le `turnIn` de la quête. **Les objets quittent l'inventaire à la remise.** |

### 3.4. Le marqueur

| Champ | Ce qu'il règle |
|---|---|
| `world` | Le monde |
| `x`, `y`, `z` | La position |
| `follow` | Le personnage mobile à suivre — **au lieu** de `world`/`x`/`y`/`z` |
| `radius` | Le rayon de validation |
| `label` | Le nom affiché sur le chemin |

> Le client transforme un marqueur en **chemin à l'écran**.

Sans lui, une quête qui dit « rendez-vous aux Terres Fracturées » laisse chercher au
hasard : **le texte décrit, le point conduit.**

Le `radius` compte : seize blocs sur une crête laisse de la marge ; deux blocs
demandent de trouver exactement le bon endroit, ce qui est rarement l'intention.

#### `follow` — un point qui se déplace

Un personnage itinérant ne se fixe pas. Le **Forgeron** change de campement : un point
figé posé sur lui conduit à un terrain vide dès la première rotation.

```yaml
marker:
  follow: forgeron
  radius: 6.0
  label: "Le Forgeron"
```

| Règle | Conséquence si elle est enfreinte |
|---|---|
| Un seul nom est reconnu aujourd'hui : `forgeron` | Tout autre nom **fait écarter la quête au chargement**, avec son nom en console |
| `follow` remplace `world`/`x`/`y`/`z` ; il ne s'y ajoute pas | Les deux ensemble **font écarter la quête** — la position du moment gagne toujours, des coordonnées écrites ne serviraient jamais |
| La position est résolue à l'envoi du journal | Si WizardCore est absent ou ne répond pas, **aucune flèche n'est envoyée**. C'est voulu : mieux vaut pas de flèche qu'une flèche qui mène ailleurs |

La casse et les espaces ne comptent pas : `"  Forgeron "` vaut `forgeron`.

### 3.5. Le donneur

Désigné par son **nom affiché**, ou par son **modèle** s'il n'en a pas. Les couleurs et
les espaces ne comptent pas : `&5Vieux Forgeron` répond à `vieux_forgeron`.

C'est volontairement tolérant. Un donneur qui ne répond pas parce qu'on a mis une
majuscule ou un code de couleur est un défaut de configuration très coûteux à
diagnostiquer.

### 3.6. La récompense

| Champ | Ce qu'il règle |
|---|---|
| `experience` | L'expérience du joueur |
| `mastery` | La maîtrise gagnée |
| `unlocks` | Les clés de déblocage — `mount.<id>` pour une monture |
| `items` | La liste, chaque entrée portant `item` et `amount` |

### La maîtrise

C'est un **seuil**, pas une monnaie. Une quête peut exiger un `minMastery` pour être
proposée.

C'est ce qui empêche un nouveau joueur de prendre *Écailles et cendres* avant de
pouvoir tuer une Gelée de lave — sans quoi il passerait son temps à mourir dans les
profondeurs sans comprendre pourquoi.

**D'où elle vient** : de la somme des récompenses `mastery` des quêtes que le joueur a
**réclamées**. Une quête terminée mais non rendue ne compte pas encore. Rien n'est stocké
à part : le total se refait à la lecture du journal, et un `quests.yml` rechargé le refait
avec lui.

> Conséquence pratique : **retirer une quête du fichier retire sa maîtrise** à tous ceux
> qui l'avaient réclamée, et peut refermer une quête qu'ils avaient ouverte. Baisser une
> récompense `mastery` a le même effet.

Au démarrage, le greffon **avertit en console** d'une quête dont le `minMastery` dépasse
tout ce que le catalogue entier sait accorder. Une telle quête n'est pas difficile : elle
ne s'ouvrira jamais.

### Les déblocages

`unlocks` est la **seule source de montures du jeu**. Dix quêtes en accordent une :

| Quête | Monture |
|---|---|
| *Ceux qui mènent* (trame 2) | Husky |
| *Ce qui dort dans les fanges* (trame 3) | Crabe de jade |
| *La sente encombrée* | Yak des cimes |
| *Le belvédère* | Corbeau des augures |
| *Braises vives* | Renard des braises |
| *Ce que le bois garde* | Ours brun |
| *Écailles et cendres* | Drakelet d'or |
| *La veille du Nyx* | Murmure-de-givre |
| *Ce qui dort sous la glace* | Ours des glaces |
| *Le pacte de Chuchevent* | Griffon |

La clé d'une monture vaut `mount.<identifiant de la monture>`, **pas** le nom de son
espèce : le Drakelet d'or a l'identifiant `drake_gold`, et sa clé est donc
`mount.drake_gold`. C'est exactement la faute qui l'a tenu fermé — la quête accordait
`mount.drake`, qui n'ouvrait rien.

**Les dix montures ont chacune leur quête.** Si une clé accordée ne correspond à aucune
monture, ou si une monture attend une clé que personne n'accorde, **WizardMobs l'écrit en
console au démarrage** — la monture resterait sinon verrouillée sans un mot. Sans
WizardQuest, toutes sont ouvertes : c'est un comportement de repli, pas une intention.

---

## 4. Les pièges de configuration

| Piège | Ce qui se passe | Comment l'éviter |
|---|---|---|
| Un `target` mal orthographié | L'objectif ne se valide jamais | Vérifier contre le catalogue de créatures ou la liste des matières |
| Une récompense avec une matière inexistante | **La quête entière est écartée au chargement** | Vérifier chaque nom de matière |
| `REACH` sans `marker` | Rien à atteindre | Un marqueur est exigé |
| Un point fixe posé sur un personnage itinérant | La flèche conduit à un endroit vide | Écrire `follow:` — voir §3.4 |
| Un `follow` mal orthographié | **La quête entière est écartée au chargement** | Un seul nom existe : `forgeron` |
| Un `radius` trop petit | Le joueur ne trouve pas le point exact | Seize blocs est une valeur raisonnable |
| Un `chapter` en double dans la trame | Deux maillons proposés en même temps | Un chapitre par quête `MAIN` |
| Un `minMastery` trop haut | La quête n'apparaît jamais | Le comparer à ce que les quêtes précédentes rapportent |
| Un `DELIVER` dont le `turnIn` n'existe pas | Impossible à rendre | Vérifier le personnage |

### Le piège de la matière inexistante

Celui-là mérite un avertissement : **une seule matière mal orthographiée dans une
récompense fait écarter toute la quête au chargement.** C'est arrivé — une faute de
frappe sur un totem a fait disparaître un maillon entier de la trame, et la quête
n'apparaissait simplement plus.

Les contrôles automatisés du projet détectent ce cas. Après toute modification de
`quests.yml`, les lancer.

---

## 5. Les objectifs qui visent une créature custom

`KILL` accepte une espèce custom ou un type de créature ordinaire. Attention à une
subtilité :

> Une créature custom porte **deux noms** — celui de son espèce, et son type vanilla.

Un objectif dont la cible est vide comptait autrefois **deux fois** par victime, parce
que les deux noms étaient remontés séparément. C'est corrigé, mais la leçon tient : un
objectif `KILL` sans cible compte tous les morts, et c'est rarement ce qu'on veut.

**Sans WizardMobs, les objectifs qui visent une espèce custom ne peuvent pas se
valider.**

---

## 6. Avant de changer quoi que ce soit

1. **Sauvegarder les journaux.** Un changement de `quests.yml` ne les efface pas, mais
   un identifiant de quête renommé rend la progression orpheline.
2. **Ne jamais renommer un identifiant de quête** déjà en jeu. Créer une nouvelle
   entrée et laisser l'ancienne.
3. **Vérifier chaque nom de matière et chaque cible.**
4. **Lancer les contrôles.**
5. **Recharger** avec `/quest reload`, permission `wizardmc.quest.admin`.

La procédure complète d'écriture est dans
[Créer une quête](../../06-creer-du-contenu/creer-une-quete.md).

---

## À lire ensuite

- [Créer une quête](../../06-creer-du-contenu/creer-une-quete.md) — la procédure
- [Catalogue des quêtes](../../07-reference/catalogue-quetes.md) — les treize quêtes livrées
- [Quêtes et montures](../../04-jouer/quetes-et-montures.md) — le point de vue du joueur
- [Créer une monture](../../06-creer-du-contenu/creer-une-monture.md) — pour un nouveau déblocage
