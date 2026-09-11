# CDC TECHNIQUE — WizardQuest

> Le greffon de **quêtes** : trame, annexes, journal, suivi de chemin et récompenses.
> Greffon Spigot autonome, `softdepend` WizardCore et WizardMobs.

**État : livré.** Ce document décrit l'existant. Les écarts connus entre ce qui est
écrit ici et ce qui tourne sont listés au §11, et repris dans
[`etat-du-serveur.md`](../01-projet/etat-du-serveur.md). **Quatre d'entre eux sont
corrigés** — Q-01, Q-02, Q-03 et Q-06 ; deux restent ouverts, et ils tiennent à la
géographie, pas au code.

Documents liés :

- [`quetes-et-montures.md`](../04-jouer/quetes-et-montures.md) — le manuel joueur
- [`catalogue-quetes.md`](../07-reference/catalogue-quetes.md) — les treize quêtes livrées
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
 │                            ou (follow, rayon, libellé) pour un point mobile
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

### 2.3. Les points mobiles

Un marqueur nomme d'ordinaire un monde et trois coordonnées. Certains personnages ne
tiennent pas en place : le **Forgeron Itinérant** change de campement, et un point figé
posé sur lui conduit à un terrain vide dès la première rotation — c'est le Q-03.

Un marqueur peut donc écrire `follow:` **au lieu** de `world`/`x`/`y`/`z` :

```yaml
marker:
  follow: forgeron
  radius: 6.0
  label: "Le Forgeron"
```

| Règle | Pourquoi |
|---|---|
| La liste des noms suivables est **fermée** (`QuestFollow`) | Un `follow:` mal orthographié donnerait un point que rien ne sait placer, donc une flèche absente et muette |
| `follow:` **et** `world:` ensemble sont **refusés** | La position du moment gagne toujours : accepter les deux laisserait croire que les coordonnées servent de secours |
| La position est résolue **à l'envoi du journal**, pas au chargement | Le catalogue ne connaît que le fichier ; savoir où se tient le Forgeron demande d'interroger WizardCore |
| Position injoignable ⇒ **aucune flèche envoyée** | Mieux vaut pas de flèche qu'une flèche qui conduit ailleurs |

Aujourd'hui un seul nom est suivable : `forgeron`. La résolution passe par la réflexion
(`ForgeronTracker`), comme `MountUnlocks` côté WizardMobs — WizardCore est un
`softdepend`, et le greffon doit démarrer sans lui.

---

## 3. Les statuts d'une quête

```
        refreshAvailability()        accept()          tous objectifs        claim()
LOCKED ──────────────────────► AVAILABLE ──────► ACTIVE ──────────────► COMPLETE ──────► CLAIMED
   ▲                                                │                                       │
   └────────────────────────────────────────────────┘ abandon()                              │
                                                                          repeatable ────────┘
```

| Statut | Ce qu'il veut dire |
|---|---|
| `LOCKED` | Prérequis ou maîtrise insuffisants |
| `AVAILABLE` | Proposée ; pour la trame, **un seul maillon à la fois** |
| `ACTIVE` | Acceptée, en cours |
| `COMPLETE` | Tous les objectifs remplis, récompense non versée |
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

**Elle se compte dans le journal du joueur** : `QuestMasteryLedger` somme la maîtrise
des quêtes au statut `CLAIMED`. Rien n'est stocké à part — le total se refait à la
lecture, et un `quests.yml` rechargé le refait avec lui.

> Ce n'était pas le cas. La source valait une implémentation neutre qui rendait zéro, et
> la méthode censée la remplacer n'était appelée par personne : **aucun seuil ne se
> franchissait**. C'était le Q-06, le plus grave des six — la trame s'arrêtait au
> chapitre 2 et une seule monture sur dix restait accessible. Les seuils livrés collent
> exactement à cette lecture : *Le premier souffle* (5) puis *Ceux qui mènent* (10) font
> les 15 que *Ce qui dort dans les fanges* demande.

`setMastery()` reste ouvert pour qu'un greffon de progression fournisse un jour la
maîtrise autrement. Il n'est appelé par personne aujourd'hui, et le défaut n'est plus
« zéro ».

**Au démarrage, le greffon avertit** d'une quête dont le seuil dépasse tout ce que le
catalogue entier sait accorder : une telle quête n'est pas difficile, elle est morte.

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

> ⚠️ **Le contrat reste fragile par construction.** Les clés sont des chaînes libres,
> rapprochées à l'exécution : une clé accordée qui ne correspond à rien n'échoue pas,
> elle **n'ouvre rien**. Le §11 en donne deux cas réels, Q-01 et Q-02.

**Le silence, lui, est levé.** La façade expose désormais :

```java
WizardQuest.grantableKeys()   // toutes les clés qu'une quête du catalogue accorde
```

WizardMobs l'interroge un tour après son chargement — WizardQuest est un `softdepend`,
il démarre avant, mais lit son catalogue pendant son propre démarrage — et **avertit en
console** de chaque monture dont la clé n'est accordée par aucune quête. Un contrôle du
fichier livré fait la même vérification quand les deux dépôts sont côte à côte.

La méthode est cherchée à part, par réflexion tolérante : la chercher avec les autres
ferait échouer toute la résolution sur un WizardQuest plus ancien, et **ouvrirait toutes
les montures d'un coup** — exactement le contraire du but.

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
| **WizardCore** | Les marqueurs `follow: forgeron` ne se placent plus : les trois quêtes concernées n'affichent pas de flèche. La maîtrise, elle, continue de se compter — elle vient du journal, pas de WizardCore |
| **WizardMobs** | Les objectifs `KILL` visant une espèce custom ne se valident plus |

Les deux sont des `softdepend` : le greffon démarre sans eux.

---

## 10. Le contenu livré

Treize quêtes — **trois de trame, dix annexes**. La trame reste courte : elle sert
d'abord à prouver que la chaîne fonctionne de bout en bout. Les annexes, elles, portent
désormais les dix montures.

| Quête | Nature | Ch. | Maîtrise | Maîtrise gagnée | Débloque |
|---|---|---:|---:|---:|---|
| Le premier souffle | MAIN | 1 | — | 5 | — |
| Ceux qui mènent | MAIN | 2 | — | 10 | `mount.husky` |
| Ce qui dort dans les fanges | MAIN | 3 | 15 | 20 | `mount.crab` |
| Vermine des caves | SIDE *(répétable)* | — | — | — | — |
| Un lit de plumes | SIDE | — | — | — | — |
| La sente encombrée | SIDE | — | — | 5 | `mount.yak` |
| Le belvédère | SIDE | — | 10 | 5 | `mount.crow` |
| Écailles et cendres | SIDE | — | 20 | 15 | `mount.drake_gold` |
| Braises vives | SIDE | — | 15 | 10 | `mount.foxy_red` |
| Ce que le bois garde | SIDE | — | 15 | 10 | `mount.bear_brown` |
| La veille du Nyx | SIDE | — | 30 | 15 | `mount.frostwhisker` |
| Ce qui dort sous la glace | SIDE | — | 30 | 15 | `mount.bear_white` |
| Le pacte de Chuchevent | SIDE | — | 55 | 25 | `mount.griffon` |

Les treize sont données par le **Forgeron** — voir le §12, c'est un problème à part.

**Les seuils sont franchissables dans l'ordre.** 135 de maîtrise sont distribuables en
tout, et aucun seuil ne dépasse ce que les quêtes ouvertes avant lui rapportent :

```
0 → souffle +5 → sente +5 → ceux qui mènent +10 = 20
  → belvédère (10 ✓) +5 → fanges (15 ✓) +20 = 45
  → braises · bois (15 ✓) +20 → écailles (20 ✓) +15 = 80
  → Nyx · glace (30 ✓) +30 = 110 → Chuchevent (55 ✓) +25 = 135
```

Un contrôle rejoue cette progression sur le fichier livré, prérequis compris, et échoue
sur toute quête qu'aucun chemin n'ouvre.

**Aucune des six quêtes neuves ne pose d'objectif `REACH`.** Le lore ne donne de
coordonnées canoniques pour aucun lieu ; en inventer aurait refait le Q-03 sous une autre
forme. Elles chassent, récoltent, et rapportent au Forgeron — dont le point, lui, suit
le personnage.

---

## 11. Écarts connus entre ce document et le code

Six écarts ont été vérifiés contre le code. **Quatre sont corrigés**, deux restent
ouverts — et les deux qui restent tiennent à la géographie, pas au code.

| # | Écart | État |
|---|---|---|
| Q-01 | « Écailles et cendres » n'ouvrait aucune monture | ✅ corrigé |
| Q-02 | Six montures sur dix ne s'ouvraient jamais | ✅ corrigé |
| Q-03 | Le marqueur du Forgeron pointait un lieu où il n'est pas | ✅ corrigé |
| Q-04 | Les lieux du Forgeron ne sont pas ceux du lore | ⏳ ouvert |
| Q-05 | Deux lieux de quête n'existent pas dans l'univers | ⏳ ouvert |
| Q-06 | La maîtrise ne se comptait pas | ✅ corrigé |

### Q-01 — « Écailles et cendres » n'ouvrait aucune monture ✅

La quête accordait `mount.drake`. La monture s'appelle `drake_gold`, et `mounts.yml` ne
déclare aucun `unlock:` explicite : sa clé effective est `mount.drake_gold`.

**La clé accordée ne correspondait à rien.** Le Drake doré restait fermé après la quête
censée l'ouvrir, sans message ni journal.

*Corrigé : la clé est alignée dans `quests.yml`. Et surtout, le silence est levé — voir
le §6 : WizardMobs avertit en console d'une monture dont personne n'accorde la clé, et un
contrôle du fichier livré le vérifie. La même faute ne pourrait plus passer.*

### Q-02 — Six montures sur dix ne s'ouvraient jamais ✅

Quatre clés seulement étaient accordées par une quête : `husky`, `crab`, `crow`, et le
`drake` fautif du Q-01. Restaient sans aucune voie d'accès :

`yak` · `foxy_red` · `bear_brown` · `frostwhisker` · `bear_white` · `griffon`

Avec WizardQuest actif, **elles étaient verrouillées définitivement**, quand le manuel
joueur affirme « les dix montures qu'elles débloquent. C'est la seule voie ».

*Corrigé : six quêtes neuves les accordent — voir le §10. Le manuel dit maintenant vrai.
Les espèces visées, les matières ramassées et les récompenses ont toutes été vérifiées
présentes dans `mobs.yml`, `beasts.yml` et `Material`.*

### Q-03 — Le marqueur du Forgeron pointait un lieu où il n'est pas ✅

Trois quêtes — *Le premier souffle*, *Ceux qui mènent*, *Un lit de plumes* — posaient le
marqueur « Le Forgeron » à `world 0, 64, 0`.

Or le Forgeron de WizardCore est **itinérant** : `forgeron_locations.yml` lui donne dix
points d'apparition, et `0, 64, 0` n'en est aucun. La flèche du client conduisait donc à
un endroit vide pendant que le personnage était ailleurs.

*Corrigé : les trois marqueurs écrivent `follow: forgeron`, et le serveur leur donne la
position du moment à l'envoi du journal — voir le §2.3. Un contrôle du fichier livré
échoue désormais sur tout marqueur fixe posé sur le Forgeron.*

### Q-04 — Les lieux du Forgeron ne sont pas ceux du lore ⏳

Neuf de ses dix emplacements nomment des régions absentes de
[`geographie.md`](../02-univers/geographie.md) : *Forêt d'Émeraude*, *Plaines du Nexus*,
*Gorges de Brume*, *Ruines Obsidiennes*, *Centre du Monde*, *Marais Sombre*, *Collines
de Lune*, *Littoral Oublié*, *Plateau Volcanique*. Seul *Désert de Cristal* existe.

*Correction : trancher — soit la géographie gagne ces lieux, soit les emplacements
prennent les noms des six régions. C'est une décision d'univers, pas une correction de
code : elle n'est pas prise ici.*

### Q-05 — Deux lieux de quête n'existent pas dans l'univers ⏳

`mushy_mare` (« les marais moisis ») apparaît dans le
[bestiaire](../02-univers/bestiaire.md) §2.9 comme biome d'espèces, mais n'est pas une
région. `belvedere` n'apparaît nulle part.

*Correction : les inscrire dans la géographie, ou les rattacher à une région existante.
Même remarque qu'au Q-04 — et c'est aussi pourquoi les six quêtes neuves ne posent aucun
objectif `REACH`.*

### Q-06 — La maîtrise ne se comptait pas ✅

Le plus grave des six, et celui qui ne se voyait pas. La source de maîtrise valait une
implémentation neutre qui rend zéro, et `setMastery()` **n'était appelé par personne** —
vérifié sur WizardCore, WizardMobs, WizardPets, WizardQuest et WizardSpigot.

Conséquence : tout `minMastery` restait infranchissable. **La trame s'arrêtait au
chapitre 2**, *Le belvédère*, *Ce qui dort dans les fanges* et *Écailles et cendres* ne
s'ouvraient jamais, et **une seule monture sur dix** (`mount.husky`) était réellement
obtenable. Rien ne le disait : les quêtes restaient simplement `LOCKED`.

*Corrigé : `QuestMasteryLedger` somme la maîtrise des quêtes `CLAIMED` — voir le §5. Le
greffon avertit en plus au démarrage d'un seuil que le catalogue entier ne permet pas
d'atteindre.*

### Ce qui est cohérent

Vérifié : **toutes les cibles `KILL` / `COLLECT` / `DELIVER` des treize quêtes
existent** — espèces de `mobs.yml`, réactifs de butin réellement lâchés par les espèces
visées, matières vanilla. Le paquet 131 est bien celui que réserve la table des
identifiants.

---

## 12. Ce qui reste à faire

| # | Tâche | Pourquoi |
|---|---|---|
| 1 | Trancher Q-04 et Q-05 | Le lore et le monde jouable doivent nommer les mêmes lieux ; tant que c'est ouvert, aucune quête ne peut poser un `REACH` honnête |
| 2 | Étendre la trame au-delà du chapitre 3 | Elle s'arrête sans conclusion — et c'est maintenant visible, puisqu'on peut l'atteindre |
| 3 | Ouvrir d'autres donneurs que le Forgeron | Les treize quêtes viennent du même personnage, qui n'a pourtant rien à voir avec un griffon |
| 4 | Valider les clés `unlocks` des **autres** systèmes au chargement | Les montures sont couvertes ; le prochain système debloquable repartira de zéro |
| 5 | Des objectifs `REACH` une fois la géographie tranchée | Six quêtes sur treize se résument à chasser et récolter |

**Ce qui est fait et n'a plus besoin d'être suivi** : Q-01, Q-02, Q-03, Q-06, et
l'avertissement console sur les clés non accordées.
