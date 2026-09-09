# Configuration de WizardCore — les fichiers YAML

Les neuf fichiers YAML de WizardCore, champ par champ. Référence d'administration et
de création de contenu.

**Statut : livré.**

---

## 1. Vue d'ensemble

| Fichier | Ce qu'il porte | Qui le touche |
|---|---|---|
| `altars.yml` | Position et type des Autels | administrateur |
| `contracts.yml` | Les contrats quotidiens | game designer |
| `era.yml` | Calendrier et thème de l'Ère | administrateur |
| `era_pass.yml` | Le Pass d'Ère | game designer |
| `forgeron_locations.yml` | Les points du Forgeron | administrateur, *worldbuilder* |
| `magic.yml` | Les règles de la magie | game designer |
| `spells.yml` | Les 41 sorts | game designer |
| `wands.yml` | Les 13 baguettes | game designer |
| `shop.yml` | Cosmétiques et utilitaires | game designer |

---

## 2. `altars.yml` — les Autels

Une entrée par Autel, numérotée. Sept sont livrés.

| Champ | Ce qu'il règle |
|---|---|
| `world` | Le monde |
| `x`, `y`, `z` | La position du bloc |
| `name` | Le nom affiché dans les annonces |
| `type` | `FEU`, `GLACE`, `OMBRE`, `LUMIERE` ou `NATURE` |

Le **type** décide du bonus accordé au Coven propriétaire. Voir
[Autels et Mana Brut](../../04-jouer/autels-et-mana.md#les-cinq-types).

### Ce que le fichier ne porte pas

Le propriétaire courant et la recharge. Ils sont persistés à part, en JSON ou MySQL
selon `altarsStorageType`. **Changer ce fichier ne remet pas les propriétaires à
zéro.**

### Ajouter ou déplacer un Autel

| À savoir | |
|---|---|
| Le nombre est configurable | Sept est un défaut, pas une limite |
| Ajouter un Autel **dilue les autres** | Sept bonus à se partager deviennent huit |
| La répartition par région est volontairement inégale | Les deux Autels d'Ombre sont dans la région la plus dangereuse |
| Déplacer un Autel en cours d'Ère | À éviter : un Coven qui a bâti autour perd son investissement |

---

## 3. `contracts.yml` — les contrats

| Champ racine | Ce qu'il règle |
|---|---|
| `contracts-per-player-per-day` | Combien de contrats par joueur et par jour — 3 par défaut |

Puis une entrée par contrat :

| Champ | Ce qu'il règle |
|---|---|
| `type` | `KILL_ENEMIES`, `CAPTURE_ALTAR` ou `SURVIVE_HOSTILE` |
| `name` | Le titre affiché |
| `description` | Le texte |
| `target` | La quantité à atteindre — nombre de victimes, de captures, ou **secondes** de survie |
| `shards` | Les Éclats versés au Nexus du Coven |

### Les trois types

| Type | Comment il se remplit |
|---|---|
| `KILL_ENEMIES` | Éliminer des joueurs adverses, hors alliés et hors même Coven |
| `CAPTURE_ALTAR` | Mener une capture jusqu'au bout |
| `SURVIVE_HOSTILE` | Rester en wilderness ou en claim ennemi. **Remis à zéro à la mort.** |

### Ce qu'il faut comprendre avant d'y toucher

Les contrats sont le **plancher de revenu** d'un Coven : ce qu'il gagne en jouant
normalement, sans risque. Les Autels sont le levier.

Le contrat `SURVIVE_HOSTILE` est la voie de repli des joueurs qui ne font pas de PvP.
**S'il disparaît, le principe « on peut briller sans être bon en PvP » devient un
slogan.** Un serveur dont les trois contrats demandent du combat a changé de
positionnement sans le dire.

La remise à zéro à la mort est ce qui empêche `SURVIVE_HOSTILE` d'être du temps
d'attente déguisé.

---

## 4. `era.yml` — l'Ère courante

| Champ | Ce qu'il règle | Défaut |
|---|---|---|
| `durationDays` | Durée totale d'une Ère | 45 |
| `cataclysmLastDays` | Derniers jours en Grand Cataclysme | 3 |
| `theme` | `DARKNESS`, `LIGHT` ou `CHAOS` | CHAOS |
| `startTimestamp` | Début forcé, en millisecondes. `0` = maintenant au premier démarrage | 0 |
| `displayName` | Libellé | « Ère I » |
| `autoResetOnExpiry` | Clôture automatique à l'échéance | vrai |
| `pantheon.world`, `.x`, `.y`, `.z` | Où se pose le Panthéon | — |
| `pantheon.maxEntries` | Combien de vainqueurs affichés | 5 |

### Ce que le fichier ne porte pas

Le numéro d'Ère, les horodatages réels et la phase courante. Ils sont persistés selon
`erasStorageType`.

Les effets du Cataclysme et les modificateurs de thème sont dans `config.json` — voir
[config.json](wizardcore-config-json.md#5-les-modificateurs-de-thème-dère).

### Changer de thème

À faire **entre deux Ères**, jamais pendant. Deux règles :

- **Ne pas enchaîner deux fois le même thème.** Les modificateurs deviennent le décor.
- **Chaos est le plus déstabilisant** : il raccourcit les routes du Forgeron et rend la
  guerre moins chère. À ne pas mettre juste après une Ère agitée.

### `autoResetOnExpiry`

Vrai par défaut. La clôture se fait toute seule à l'échéance.

> **Une sauvegarde avant reset n'est pas optionnelle.** Un reset touche les compteurs
> de tous les Covens d'un coup, et il n'existe pas de retour arrière partiel.

Si le calendrier n'est pas sûr, mettre cette clé à faux et clôturer à la main est plus
prudent. Voir [Exploitation quotidienne](../exploitation.md).

---

## 5. `era_pass.yml` — le Pass d'Ère

| Champ | Ce qu'il règle | Défaut |
|---|---|---|
| `price_gems` | Le prix de la voie payante | 300 |
| `max_level` | Nombre de paliers | 50 |
| `exp_per_level` | Expérience par palier, si un palier n'en déclare pas | 100 |
| `xp.contract` | Par contrat rempli | 10 |
| `xp.altar` | Par capture d'Autel | 5 |
| `xp.pvp_kill` | Par victoire en combat joueur | 2 |
| `xp.playtime_hour` | Par heure de jeu | 1 |
| `levels.<n>.free` | Récompense gratuite du palier |  |
| `levels.<n>.premium` | Récompense payante du palier |  |

Une récompense est soit un identifiant de cosmétique, soit `dust:N` ou `gems:N`.

### Les deux règles à tenir

**Le temps de jeu est la source la plus faible.** Un Pass qui se remplirait en restant
connecté récompenserait l'absence. Le contrat est la plus forte, et c'est aussi
l'activité la plus accessible à un joueur seul.

**La voie gratuite donne quelque chose à chaque palier déclaré.** Un Pass dont la voie
gratuite est vide n'est pas un Pass, c'est une vitrine.

### Ce qu'on ne met jamais dans un palier

Un Éclat, du Mana Brut, un sort, un niveau, une statistique. Le Pass est
**entièrement cosmétique**, plus de la Poussière d'Étoile.

---

## 6. `forgeron_locations.yml` — le Forgeron

Une entrée par point, numérotée à partir de zéro.

| Champ | Ce qu'il règle |
|---|---|
| `world` | Le monde |
| `x`, `y`, `z` | La position |
| `name` | Le nom de la région, pour les annonces |

La position courante est persistée selon `forgeronStorageType`.

### La règle de placement

> **Aucun point ne doit être à la fois proche du spawn et sûr.**

Un Forgeron commode supprime le risque du convoi, donc la valeur du Mana Brut, donc la
ressource — et avec elle tout le profil du marchand.

### Combien de points

Une dizaine. Moins, et les routes deviennent prévisibles au point qu'on peut camper
chaque point. Plus, et le Forgeron devient introuvable.

---

## 7. `magic.yml` — les règles de la magie

### `essence`

| Champ | Ce qu'il règle | Défaut |
|---|---|---|
| `baseMax` | Réserve de départ | 100 |
| `regenOutOfCombatPer10s` | Régénération hors combat, par dix secondes | 4 |
| `regenInCombatPer10s` | Régénération en combat | 1 |
| `outOfCombatDelaySeconds` | Délai avant reprise de la régénération hors combat | 5 |
| `libraryMaxBonus` | Bonus de réserve de la bibliothèque | 10 |
| `librarySchoolXpBonusPercent` | Bonus d'expérience d'école de la bibliothèque | 10 |
| `schoolLevelBonusPerLevel` | Réserve ajoutée par niveau d'école moyen | 2 |
| `schoolLevelBonusCap` | Plafond de ce bonus | 40 |

> **`regenOutOfCombatPer10s` est la clé la plus dangereuse du fichier.**
>
> Le rythme a été de 40, puis de 10, avant de descendre à 4. Les deux premiers étaient
> trop rapides, et la raison n'est pas le confort : à un point par seconde, une réserve
> vide se refaisait en cent secondes sans rien faire, et **les fioles de régénération
> ne servaient plus à rien**. Personne n'allait dépenser une étoile du Nether pour
> gagner une minute d'attente.
>
> La remonter supprime un système entier. Ce n'est pas une opinion, c'est arrivé deux
> fois.

### `cast`

| Champ | Ce qu'il règle | Défaut |
|---|---|---|
| `globalGcdTicks` | La pause commune après n'importe quel sort | 10 ticks, soit 0,5 s |
| `interruptRefundPercent` | Ce qui est rendu à l'interruption | 0,5 |
| `interruptMoveBlocks` | Déplacement toléré pendant une incantation | 0,5 bloc |
| `channelPulseTicks` | Intervalle d'effets pendant une canalisation | 10 ticks |
| `incantationTicks` | Durée de base d'une incantation | 100 ticks, soit 5 s |

L'incantation est réduite de 4 % par niveau d'école et de 5 % par rang au-delà du
premier, sans descendre sous 20 ticks. Un sort qui déclare une incantation plus longue
garde la sienne.

| Pourquoi ces valeurs | |
|---|---|
| La pause commune | Sans elle, deux sorts prêts vident un adversaire avant qu'il bouge |
| Le remboursement de moitié | Sans lui, une seule interruption ruine un lanceur |
| L'incantation obligatoire | C'est ce qui rend un sort esquivable |

### `regions.blocked`

La liste des régions WorldGuard où la magie est interdite. Insensible à la casse.

| Comportement | |
|---|---|
| Liste vide | Magie autorisée partout ; WorldGuard n'est même pas consulté |
| Régions déclarées, WorldGuard absent | Un avertissement **une seule fois** au démarrage, et la magie reste autorisée |

> L'interdiction est vérifiée **au lancement et à l'impact**. Sans la seconde
> vérification, il suffirait de se placer juste en dehors d'une arène pour en arroser
> l'intérieur.

Une porte cassée ne doit pas rendre la magie injouable sur tout le serveur : c'est
pour ça que l'absence de WorldGuard n'interdit rien.

---

## 8. `spells.yml` — les sorts

Quarante et un sorts. Le champ par champ et les quarante et une valeurs sont dans
[Créer un sort](../../06-creer-du-contenu/creer-un-sort.md) et
[Catalogue des sorts](../../07-reference/catalogue-sorts.md).

### Les champs d'un sort

| Champ | Ce qu'il règle |
|---|---|
| `school` | L'école, parmi les neuf |
| `name`, `description` | Ce que le joueur lit |
| `incantation` | La formule affichée |
| `mode` | `INSTANT`, `CAST_TIME` ou `CHANNEL` |
| `manaCost` | Le coût en Essence |
| `castTicks` | L'incantation propre au sort, si elle diffère du défaut |
| `cooldownTicks` | La recharge |
| `gcdTicks` | La pause commune, si elle diffère |
| `requiredSchoolLevel` | Le niveau d'école exigé |
| `requiredWandTier` | Le palier de baguette exigé |
| `range` | La portée, en blocs |
| `hostile` | S'il vise un adversaire |
| `interruptible` | S'il peut être coupé |
| `autoLearn` | S'il est connu d'office |
| `tradition` | L'école d'origine, quand elle a été absorbée. **Purement narratif.** |
| `fx` | Couleur, traînée, impact, animation |
| `effects` | Ce que le sort fait réellement |

### La règle de base

Un sort déclare **dix-neuf** variantes qui ne s'obtiennent que par un Tome. Les autres
s'apprennent au grimoire contre de l'expérience d'école.

Un sort hostile doit avoir une recharge d'au moins cinquante ticks : c'est ce qui
empêche le harcèlement à répétition.

---

## 9. `wands.yml` — les baguettes

| Champ racine | Ce qu'il règle |
|---|---|
| `defaultId` | La baguette de départ |

Puis une entrée par baguette :

| Champ | Ce qu'il règle |
|---|---|
| `displayName` | Le nom affiché |
| `tier` | Le palier, de 1 à 4 |
| `affinity` | L'école favorisée, ou vide |
| `essenceCostFactor` | Multiplicateur du coût en Essence |
| `cooldownFactor` | Multiplicateur de la recharge |
| `material` | La matière de l'objet |
| `description` | Le texte |

### Deux baguettes peuvent partager une affinité

Ce sont alors des **variantes à collectionner**, pas des paliers de puissance. La
Baguette de Saule Sylvain et la Baguette de Liant-Pierre ont la même affinité Roc, le
même palier et les mêmes facteurs.

Deux baguettes portent le nom d'une **tradition absorbée** — Fer-Sang pour la
Sanguine, Verre-Chronos pour la Chronienne. C'est de la mémoire : aucune règle de jeu
ne connaît les traditions.

---

## 10. `shop.yml` — la boutique

Deux sections : `cosmetics` et `utilities`.

### Un cosmétique

| Champ | Ce qu'il règle |
|---|---|
| `type` | `WAND_SKIN`, `AURA`, `TITLE`, `PET_SKIN`, `DEATH_EFFECT`, `TRAIL` |
| `name`, `description` | Ce que le joueur lit |
| `price_gems`, `price_dust` | Les deux prix possibles |

### Un utilitaire

| Champ | Ce qu'il règle |
|---|---|
| `type` | `EXTRA_HOME`, `WORKBENCH`, `ENDERCHEST`, `MAGIC_PRESET_SLOT` |
| `price_gems`, `price_dust` | Les prix |
| `max_per_player` | Le plafond par joueur |

### La seule règle qui compte

> **Rien dans ce fichier ne doit donner d'avantage en jeu.**

Le test : *un joueur qui n'achète rien perd-il quelque chose face à un joueur qui
achète tout ?* Si oui, l'entrée est à retirer.

Le cas limite est `MAGIC_PRESET_SLOT`. Il n'accorde ni sort, ni niveau, ni
statistique : le joueur pouvait déjà composer cette barre à la main. **Enregistrer ce
qu'on peut déjà faire** est du confort ; **pouvoir faire plus** serait un avantage.

Un « slot de claim » payant serait refusé : il donne du territoire.

### Les plafonds

`max_per_player` n'est pas décoratif. Sans lui, l'accumulation illimitée d'un confort
finit par constituer un avantage. Voir
[cdc_boutique](../../90-specifications/cdc_boutique.md).

---

## À lire ensuite

- [config.json](wizardcore-config-json.md) — les 74 clés
- [WizardMobs](wizardmobs.md) — créatures, bêtes, montures
- [WizardQuest](wizardquest.md) — quêtes et journaux
- [Créer un sort](../../06-creer-du-contenu/creer-un-sort.md) — la procédure complète
