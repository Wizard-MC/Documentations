# CDC TECHNIQUE — WizardQuest

> Le greffon de **quêtes** : trame, annexes, journal, suivi de chemin et récompenses.
> Greffon Spigot autonome, `softdepend` WizardCore et WizardMobs.

**État : livré.** Ce document décrit l'existant. Les écarts connus entre ce qui est
écrit ici et ce qui tourne sont listés au §11, et repris dans
[`etat-du-serveur.md`](../01-projet/etat-du-serveur.md).

Documents liés :

- [`quetes-et-montures.md`](../04-jouer/quetes-et-montures.md) — le manuel joueur
- [`catalogue-quetes.md`](../07-reference/catalogue-quetes.md) — les sept quêtes livrées
- [`wizardquest.md`](../05-operer/configuration/wizardquest.md) — la configuration
- [`creer-une-quete.md`](../06-creer-du-contenu/creer-une-quete.md) — ajouter une quête
- [`cdc_mobs.md`](cdc_mobs.md) — les espèces que les objectifs `KILL` visent

---

## 1. Objet et intention

Une quête existe pour **donner une direction**. Un SMP ouvert ne dit rien de ce qu'il
faut faire ; la trame le dit, sans l'imposer.

Trois règles de conception commandent le reste :

| Règle | Pourquoi |
|---|---|
| **Un seul maillon de trame à la fois** | Montrer les dix chapitres d'un coup transforme une histoire en liste de courses |
| **Les annexes se prennent dans n'importe quel ordre** | Un joueur bloqué sur la trame doit avoir autre chose à faire |
| **Chaque objectif de déplacement porte un point** | « Rends-toi aux marais » sans marqueur, c'est chercher au hasard |

**Ce que WizardQuest n'est pas.** Ce n'est pas un moteur de dialogue : un donneur ne
propose pas d'arbre de choix. Ce n'est pas un système de réputation. Ce n'est pas le
système de progression du joueur — la **maîtrise** est lue, jamais écrite par ce
greffon.

---

## 2. Le modèle

```
Quête
 ├─ kind : MAIN | SIDE
 ├─ chapter, requires[]        (MAIN uniquement : l'ordre de la trame)
 ├─ minMastery                 (seuil d'accès)
 ├─ giver, turnIn              (personnages)
 ├─ repeatable                 (SIDE uniquement)
 ├─ Étape ×N                   ordonnées, une seule active
 │   └─ Objectif ×N            dans l'ordre qu'on veut
 │       ├─ kind : KILL | COLLECT | REACH | TALK | DELIVER
 │       ├─ target, amount
 │       └─ marker             (monde, x, y, z, rayon, libellé)
 └─ Récompense
     ├─ experience             XP vanilla
     ├─ mastery                maîtrise
     ├─ items[]
     └─ unlocks[]              clés ouvertes à d'autres greffons
```

**Étapes contre objectifs.** Les objectifs d'une même étape se remplissent dans
n'importe quel ordre ; les étapes s'enchaînent. C'est ce qui permet d'écrire « va
parler au Forgeron, **puis** reviens » sans montrer la fin dès le début.

### 2.1. Les cinq types d'objectif

| Type | Cible | Validé par |
|---|---|---|
| `KILL` | une espèce custom ou un type vanilla | `QuestKillListener` |
| `COLLECT` | une matière | `QuestCollectListener` |
| `REACH` | un lieu — **exige un `marker`** | `QuestReachTask`, par sondage |
| `TALK` | l'identifiant d'un personnage | `QuestNpcListener` |
| `DELIVER` | une matière, remise au `turnIn` | `QuestDelivery` — **les objets quittent l'inventaire** |

`DELIVER` est le seul type qui **retire** quelque chose. C'est ce qui le distingue de
`COLLECT`, qui se contente de constater.

### 2.2. Les personnages

Un donneur est désigné par le **nom affiché** du PNJ, ou par son modèle s'il n'en a
pas. Les couleurs et les espaces ne comptent pas : `&5Vieux Forgeron` répond à
`vieux_forgeron`. Cette tolérance est délibérée — un nom de PNJ change souvent, et une
quête ne doit pas casser parce qu'on a ajouté une couleur.

Le `turnIn` vaut le `giver` par défaut.

---

## 3. Les statuts d'une quête

```
        refreshAvailability()        accept()          tous objectifs        claim()
LOCKED ──────────────────────► AVAILABLE ──────► ACTIVE ──────────────► COMPLETED ─────► CLAIMED
   ▲                                                │                                       │
   └────────────────────────────────────────────────┘ abandon()                              │
                                                                          repeatable ────────┘
```

| Statut | Ce qu'il veut dire |
|---|---|
| `LOCKED` | Prérequis ou maîtrise insuffisants |
| `AVAILABLE` | Proposée ; pour la trame, **un seul maillon à la fois** |
| `ACTIVE` | Acceptée, en cours |
| `COMPLETED` | Tous les objectifs remplis, récompense non versée |
| `CLAIMED` | Récompense versée |

Une quête `SIDE` marquée `repeatable` retourne à `AVAILABLE` après réclamation.

---

## 4. Le protocole client

**Paquet 131**, bidirectionnel — voir
[`plages-d-identifiants.md`](../07-reference/plages-d-identifiants.md).

| Action | Sens | Rôle |
|---|---:|---|
| `SYNC` | 0 | serveur → client : le journal entier |
| `UPDATE` | 1 | serveur → client : une quête |
| `REMOVE` | 2 | serveur → client : retirer une quête |
| `TRACK` | 3 | serveur → client : la quête suivie |
| `OPEN` | 10 | client → serveur : ouvrir le journal |
| `ACCEPT` | 11 | client → serveur |
| `SET_TRACK` | 12 | client → serveur |
| `ABANDON` | 13 | client → serveur |
| `CLAIM` | 14 | client → serveur |

Les actions 0–3 descendent, les actions 10–14 remontent. Le client n'invente rien :
il demande, le serveur tranche et renvoie l'état.

**Le journal entier est renvoyé après une réclamation**, et non la seule quête rendue.
Réclamer ouvre souvent le maillon suivant de la trame, qui n'apparaîtrait pas autrement.

---

## 5. La maîtrise

La maîtrise est un **seuil**, pas une monnaie. Elle ouvre des quêtes, elle ne s'échange
pas. `minMastery` la compare ; la récompense `mastery` l'augmente.

**WizardQuest ne la stocke pas.** Elle est lue à chaque évaluation d'accessibilité. Sans
la source, le greffon fonctionne, mais aucune quête à `minMastery` ne s'ouvre jamais.

---

## 6. Les déblocages

`unlocks` accorde des **clés de texte** — `mount.husky` — que d'autres greffons
interrogent :

```java
WizardQuest.hasUnlocked(playerUuid, "mount.husky")
```

Un `QuestClaimedEvent` est également émis, portant le joueur, la quête et les clés.

**WizardMobs est le seul consommateur actuel** (`MountUnlocks`). Sa convention : la clé
d'une monture vaut `mount.<id>` sauf `unlock:` explicite dans `mounts.yml`.

> ⚠️ **Le contrat est fragile par construction.** Les clés sont des chaînes libres,
> rapprochées à l'exécution et jamais validées au chargement : une clé accordée qui ne
> correspond à rien n'échoue pas, elle **n'ouvre rien, en silence**. Le §11 en donne un
> cas réel.

**Sans WizardQuest installé, tout est ouvert.** `MountUnlocks` le dit explicitement :
verrouiller sur l'absence du greffon donnerait un serveur où plus rien ne s'invoque,
sans qu'aucune ligne ne l'explique.

---

## 7. Persistance

| Mode | Ce que c'est | Quand |
|---|---|---|
| `JSON` | un fichier par joueur, `plugins/WizardQuest/journaux` | serveur seul |
| `MYSQL` | `wizard_quest_progress` et `wizard_quest_tracked` | plusieurs serveurs partagent les joueurs |

Un hôte ou une base absents **font retomber sur JSON** plutôt que d'échouer : perdre
l'avancement de tout le monde pour une ligne oubliée serait cher payé.

Le journal est chargé à la connexion, écrit quand il change, et sorti de la mémoire à
la déconnexion.

---

## 8. Commandes et permissions

`/quest [list|info|accept|track|abandon|claim|reload] [quête]` — alias `/quetes`,
`/journal`.

`wizardmc.quest.admin` (défaut `op`) — relire le catalogue.

---

## 9. Ce que le greffon perd sans ses dépendances

| Sans | Conséquence |
|---|---|
| **WizardCore** | La maîtrise vaut zéro : toute quête à `minMastery` reste fermée |
| **WizardMobs** | Les objectifs `KILL` visant une espèce custom ne se valident plus |

Les deux sont des `softdepend` : le greffon démarre sans eux.

---

## 10. Le contenu livré

Sept quêtes — **trois de trame, quatre annexes**. Volontairement court : la trame sert
d'abord à prouver que la chaîne fonctionne de bout en bout.

| Quête | Nature | Ch. | Maîtrise | Débloque |
|---|---|---:|---:|---|
| Le premier souffle | MAIN | 1 | — | — |
| Ceux qui mènent | MAIN | 2 | — | `mount.husky` |
| Ce qui dort dans les fanges | MAIN | 3 | 15 | `mount.crab` |
| Vermine des caves | SIDE *(répétable)* | — | — | — |
| Un lit de plumes | SIDE | — | — | — |
| Le belvédère | SIDE | — | 10 | `mount.crow` |
| Écailles et cendres | SIDE | — | 20 | `mount.drake` ⚠️ |

Les sept sont données par le **Forgeron**.

---

## 11. Écarts connus entre ce document et le code

Ils sont réels, vérifiés, et ouverts. Ils ne sont pas des détails de rédaction.

### Q-01 — « Écailles et cendres » n'ouvre aucune monture

La quête accorde `mount.drake`. La monture s'appelle `drake_gold`, et `mounts.yml` ne
déclare aucun `unlock:` explicite : sa clé effective est donc `mount.drake_gold`.

**La clé accordée ne correspond à rien.** Le Drake doré reste fermé après la quête qui
est censée l'ouvrir, sans message ni journal.

*Correction : aligner la clé — dans `quests.yml` ou par un `unlock:` dans `mounts.yml`.*

### Q-02 — Six montures sur dix ne s'ouvrent jamais

Quatre clés seulement sont accordées par une quête : `husky`, `crab`, `crow`, et le
`drake` fautif du Q-01. Restent sans aucune voie d'accès :

`yak` · `foxy_red` · `bear_brown` · `frostwhisker` · `bear_white` · `griffon`

Avec WizardQuest actif, **elles sont verrouillées définitivement**. Le manuel joueur
affirme pourtant « les dix montures qu'elles débloquent. C'est la seule voie ».

*Correction : écrire les quêtes manquantes, ou retirer la porte pour ces montures.*

### Q-03 — Le marqueur du Forgeron pointe un lieu où il n'est pas

Trois quêtes — *Le premier souffle*, *Ceux qui mènent*, *Un lit de plumes* — posent le
marqueur « Le Forgeron » à `world 0, 64, 0`.

Or le Forgeron de WizardCore est **itinérant** : `forgeron_locations.yml` lui donne dix
points d'apparition, et `0, 64, 0` n'en est aucun. La flèche du client conduit donc à un
endroit vide pendant que le personnage est ailleurs.

*Correction : résoudre le marqueur à l'exécution depuis la position courante du
Forgeron, au lieu de le figer dans le catalogue.*

### Q-04 — Les lieux du Forgeron ne sont pas ceux du lore

Neuf de ses dix emplacements nomment des régions absentes de
[`geographie.md`](../02-univers/geographie.md) : *Forêt d'Émeraude*, *Plaines du Nexus*,
*Gorges de Brume*, *Ruines Obsidiennes*, *Centre du Monde*, *Marais Sombre*, *Collines
de Lune*, *Littoral Oublié*, *Plateau Volcanique*. Seul *Désert de Cristal* existe.

*Correction : trancher — soit la géographie gagne ces lieux, soit les emplacements
prennent les noms des six régions.*

### Q-05 — Deux lieux de quête n'existent pas dans l'univers

`mushy_mare` (« les marais moisis ») apparaît dans le
[bestiaire](../02-univers/bestiaire.md) §2.9 comme biome d'espèces, mais n'est pas une
région. `belvedere` n'apparaît nulle part.

*Correction : les inscrire dans la géographie, ou les rattacher à une région existante.*

### Ce qui est cohérent

Vérifié : **les neuf cibles `KILL` / `COLLECT` / `DELIVER` des sept quêtes existent
toutes** — quatre espèces custom, trois réactifs de butin, une matière vanilla. Le
paquet 131 est bien celui que réserve la table des identifiants.

---

## 12. Ce qui reste à faire

| # | Tâche | Pourquoi |
|---|---|---|
| 1 | Corriger Q-01 | Une quête ment sur sa récompense |
| 2 | Corriger Q-03 | Trois quêtes envoient le joueur au mauvais endroit |
| 3 | Trancher Q-02 | Six montures inaccessibles, ou un manuel qui ment |
| 4 | Valider les clés `unlocks` au chargement | Q-01 n'aurait pas pu passer inaperçu |
| 5 | Aligner Q-04 et Q-05 sur la géographie | Le lore et le monde jouable doivent nommer les mêmes lieux |
| 6 | Étendre la trame au-delà du chapitre 3 | Elle s'arrête sans conclusion |
| 7 | Ouvrir d'autres donneurs que le Forgeron | Les sept quêtes viennent du même personnage |
