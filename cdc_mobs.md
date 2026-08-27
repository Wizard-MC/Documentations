# CDC — Mobs custom hostiles

> **Livré.** WizardSpigot (entité 215 + cerveau), WizardCore (catalogue, apparition,
> butin, nuées, marchandage), MCP (rendu client). 47 modèles importés depuis ASSETS.

---

## 1. Objectif

Peupler les mondes de créatures qui **ne se ressemblent pas** : qui pensent
différemment les unes des autres, qui ressentent, qui se rassemblent derrière
un chef, dont le niveau suit celui du joueur et l'hostilité du lieu, et qui
laissent quelque chose en tombant.

La barre est celle du jeu lui-même : les mobs custom doivent apparaître
**vraiment au hasard** sur les mondes, comme les zombies et les squelettes,
pas à des points posés à l'avance.

---

## 2. Ce qui existait avant

Le mode hostile d'un `WizardNpc` tenait en douze lignes :

```java
private void tickHostile() {
    EntityHuman target = this.world.findNearbyVulnerablePlayer(this, 16.0D);
    if (target == null) { this.tickWander(); return; }
    // ... oriente le yaw, pousse motX/motZ vers la cible
}
```

Marcher en ligne droite vers le joueur le plus proche. **Dans les murs
compris**, et sans jamais frapper : `EntityWizardNpc` dérive
d'`EntityLiving`, qui n'a ni navigation ni sélecteur de buts. C'était un
choix assumé pour un PNJ posé et immobile ; ce n'en était plus un pour une
créature qui doit contourner un obstacle et choisir son coup.

Les seuls vrais `PathfinderGoal` du projet appartenaient aux compagnons —
Ignis, Nox, Aether, Terra, Lumière, Lunaris. Aucun mob hostile n'avait de
cerveau.

Trois entités compagnon — Terra (212), Lumière (213), Lunaris (214) —
avaient leur classe mais n'étaient enregistrées ni dans `EntityTypes` côté
serveur ni dans `EntityList` côté client : elles ne pouvaient pas apparaître.

---

## 3. Architecture

Trois dépôts, une ligne de partage nette.

| Où | Quoi | Pourquoi là |
| :--- | :--- | :--- |
| **WizardSpigot / API** | Morale, tempéraments, groupes, niveaux, attaques | Arithmétique pure, sans monde : vérifiable sans serveur, et bon marché à faire tourner cent fois par seconde |
| **WizardSpigot / Server** | `EntityWizardMob` (215), `PathfinderGoalMobBrain`, registre de groupes | L'IA tourne côté serveur, jamais côté plugin : cent mobs qui réfléchissent depuis un plugin coûteraient un aller-retour d'API par décision |
| **WizardCore** | Catalogue, apparition, butin, niveaux, nuées, marchandage | Le **contenu**. Il change souvent, il vit dans un fichier, il ne doit pas demander de recompiler le serveur |
| **MCP** | `EntityWizardMob` client, `RenderWizardMob`, 47 modèles | Le rendu |

### 3.1 Pourquoi une nouvelle entité plutôt qu'un mode de plus sur le NPC

`EntityWizardMob` dérive d'`EntityMonster`. C'est ce qui lui donne la
navigation, le sélecteur de buts, les attaques et la mort avec butin — tout
ce qui manquait au patron `EntityLiving` du NPC.

Il publie en revanche **le même contrat de DataWatcher** que le NPC :

| Emplacement | Contenu | Partagé avec le NPC |
| ---: | :--- | :---: |
| 19 | modèle 3D | oui |
| 22 | clip d'action en cours | oui |
| 23 | ticks écoulés du clip | oui |
| 24 | boîte de collision (centièmes de bloc) | oui |
| 25 | niveau (1–100) | non |
| 26 | rôle + humeur, empaquetés dans un octet | non |

Le client sait déjà lire les quatre premiers et animer un bbmodel à partir
d'eux. Rien ne justifiait un second protocole pour le même travail : le
pilote d'animation est désormais partagé entre les deux rendus.

---

## 4. Conformité

### 4.1 Chaque mob réfléchit différemment

`MobTemperament` : huit façons de penser, chacune avec sa distance de
confort, sa proie de prédilection, et son besoin des autres.

| Tempérament | Courage | Distance | Vise | Recule ? | Besoin des siens ? |
| :--- | ---: | :--- | :--- | :---: | :---: |
| `BRUTE` | 0,85 | contact | le plus proche | non | non |
| `SKIRMISHER` | 0,55 | contact | le plus faible | oui | non |
| `AMBUSHER` | 0,60 | contact | le plus proche | non | non |
| `RANGED` | 0,25 | 6–14 | le plus faible | oui | non |
| `CASTER` | 0,30 | 8–16 | le plus fort | oui | **oui** |
| `PACK_HUNTER` | 0,45 | contact | l'isolé | non | **oui** |
| `SENTINEL` | 0,75 | contact | le plus proche | non | non |
| `STALKER` | 0,50 | contact | l'isolé | oui | non |

La décision est une suite de comparaisons, pas un arbre : cette méthode
tourne pour chaque mob chargé, plusieurs fois par seconde, et tout ce qui y
coûte se paie autant de fois.

### 4.2 Les mobs ressentent

`MobMorale` tient **trois nombres** entre zéro et un — la peur, la rage, un
courage de base propre à l'espèce — et tout le reste en découle : l'humeur,
la rancune, l'aptitude à mener.

Les tenir séparés plutôt que de coder directement des humeurs évite le
défaut classique : un mob qui bascule d'un état à l'autre à chaque coup reçu,
et dont le comportement clignote.

```
peur   ← coups reçus, alliés tombés à proximité      (atténuée par le courage d'espèce)
rage   ← coups reçus, alliés tombés à proximité      (non atténuée)
courage effectif = courage d'espèce + renfort du groupe + rage×0,20 − blessures
```

| Humeur | Quand |
| :--- | :--- |
| `AFRAID` | la peur passe le courage, **ou** le prochain coup l'achève |
| `VENGEFUL` | une rancune désigne quelqu'un (une minute) |
| `EMBOLDENED` | engagé, avec du monde autour |
| `ANGRY` | engagé, seul |
| `WARY` | une menace vue, pas d'engagement |
| `CALM` | rien en vue |

**Le second critère de `AFRAID` n'est pas un raffinement.** La peur et le
courage saturent tous deux à un : un mob entouré des siens, dont le courage
de groupe plafonnait, ne fuyait jamais — même à un point de vie. La règle
« le prochain coup m'achève » est la seule qui le fasse rompre, et sa marge
dépend du tempérament : une brute tient jusqu'au coup fatal, un tireur rompt
bien avant.

### 4.3 Un mob peut mener un groupe

`MobPack` porte l'appartenance, la succession et le seuil de nuée.

- Un mob rejoint le groupe d'un voisin de son espèce à portée de cohésion, ou
  en ouvre un. **Aucun regroupement global n'est calculé** : la proximité
  exigée pour entrer suffit à garder les groupes cohérents.
- Un membre qui n'a plus aucun congénère autour de lui quitte son groupe —
  sans quoi une bande s'étirait sur toute la carte, et une nuée pouvait être
  déclarée sans rassemblement.
- Les suiveurs **héritent de la cible du chef** : c'est cela, la cohésion.
- Le renfort du groupe relève le courage, plafonné (0,20 pour le nombre,
  +0,12 pour la présence d'un chef).

### 4.4 Les courageux deviennent chefs

Un mob se propose à la tête de son groupe quand son **courage effectif**
dépasse 0,70 — celui du moment, groupe et blessures compris.

Ce n'est donc pas un privilège d'espèce : une brute prend naturellement la
tête, mais un mob ordinaire porté par une grande bande peut y arriver aussi.
Un chef que la peur gagne rend son commandement plutôt que d'entraîner sa
bande dans sa fuite.

Un poste occupé ne change de main que pour un courage **nettement**
supérieur (marge de 0,08). Sans cette marge, deux mobs de courage voisin
échangeaient la tête à chaque évaluation, et le groupe entier changeait de
cible plusieurs fois par seconde.

Tuer le chef laisse une brèche : la succession n'est pas automatique.

### 4.5 Une grande bande déclenche un événement

Chaque espèce porte son seuil : **50** pour les araignées et les créatures
rares, **100** pour la horde gobeline. Au franchissement, `MobSwarmEvent`
est émis **une seule fois** — un groupe oscille autour de son seuil, et un
événement relancé à chaque passage donnerait autant de récompenses qu'il y a
d'allers-retours.

`MobSwarmListener` annonce la nuée au serveur et récompense les joueurs dans
un rayon de 64 blocs, depuis la section de butin `SWARM`. La récompense va
aux **présents** et non aux tueurs : une nuée n'est pas un boss, elle n'a pas
de vainqueur ; ce qui se paie est d'avoir tenu la zone pendant qu'elle
enflait.

### 4.6 Les mobs réfléchissent à leur attaque

`MobAttackSet.choose()` ne retient pas la première attaque prête et à portée,
mais la **meilleure ici**. Chaque attaque porte une nature, et la nature dit
quelles situations la rendent bonne :

| Nature | Meilleure quand | Mauvaise quand |
| :--- | :--- | :--- |
| `AREA` | plusieurs cibles autour | un joueur seul |
| `SUMMON` | le mob est seul | il est déjà entouré |
| `BUFF` | il a des alliés | il n'en a pas |
| `DEFENSIVE` | il a peur | tout va bien |
| `RANGED` | au-delà de six blocs | au contact |
| `LUNGE` | à trois blocs ou plus | collé à la cible |
| `MELEE` | au contact | — |

Les corrections restent petites devant les priorités d'espèce : une créature
garde la main sur l'ordre de ses coups, la situation ne fait que départager
et corriger les contresens. Un départage déterministe dépendant du tick évite
qu'un modèle offrant trois clips d'attaque n'en montre qu'un.

### 4.7 Les niveaux suivent le joueur, l'école et la région

```
ancrage = 0,40 × niveau du joueur + 0,60 × sa meilleure maîtrise d'école
niveau  = ancrage + hostilité de la région + décalage d'espèce + variation
```

La maîtrise d'école pèse plus que le niveau général : un joueur qui a poussé
une école loin est mieux armé qu'un joueur du même niveau qui n'en a monté
aucune.

La variation est **déterministe** (SplitMix sur une graine de position et de
temps) : un mob rechargé depuis le disque retrouve son niveau.

| Région | Décalage |
| :--- | ---: |
| Sanctuaire | −8 |
| Plaine des Murmures | −4 |
| Désert de Cristal | +4 |
| Forêt d'Ébène | +8 |
| Wilderness | +6 |
| Montagnes du Crépuscule | +14 |

La vie monte de 8 % par niveau, les dégâts de 5 % : **linéaire et non
géométrique**, parce qu'une croissance géométrique rend les hauts niveaux
impossibles à équilibrer — le moindre écart s'y traduit par un rapport de
force absurde.

### 4.8 Certains marchandent

Des créatures paisibles échangent comme un villageois : on clique, on paie si
l'on peut, on reçoit. Les offres sont attachées au **modèle** et non à une
espèce hostile — on ne marchande pas avec ce qui vous attaque.

Le décompte parcourt tout l'inventaire plutôt que d'utiliser `contains`, qui
ne sait compter que dans une seule pile : un joueur qui a douze pièces en
trois piles doit pouvoir acheter à dix.

> Le dépôt d'assets ne contient pour l'instant **aucun modèle de créature
> paisible**. Le seul marchand livré est le Forgeron, déjà posé dans le monde.
> Ajouter une espèce marchande ne demandera qu'un modèle et une entrée dans
> `traders`.

---

## 5. Apparition naturelle

Le principe est celui du jeu : à chaque seconde, on tire des emplacements dans
une **couronne** de 24 à 72 blocs autour de chaque joueur, on écarte ceux qui
ne conviennent pas, on y pose une espèce que le lieu autorise.

Deux précautions décident si le serveur tient :

- **Aucun chunk n'est jamais chargé pour l'occasion.** Un emplacement dans un
  chunk absent est abandonné. Faire naître des mobs en chargeant le terrain
  autour de chaque joueur revient à générer le monde en continu, et c'est la
  façon la plus sûre de mettre un serveur à genoux sans qu'aucune erreur ne
  l'annonce.
- **Le rayon est tiré sur la racine du hasard.** Tirer uniformément concentre
  les apparitions près du bord intérieur, où la couronne est étroite, et
  laisse le lointain presque vide.

La population est plafonnée à **12 mobs par joueur présent**, avec un plafond
absolu de **180 par monde** : un plafond fixe vide un monde peuplé et noie un
monde désert.

### 5.1 Ce qu'une espèce déclare

| Champ | Rôle |
| :--- | :--- |
| `biomes` | liste vide = peu importe |
| `sky` | `REQUIRED` surface, `FORBIDDEN` sous terre, `ANY` |
| `minY` / `maxY` | profondeur |
| `minLight` / `maxLight` | lumière de bloc |
| `water` | dans l'eau plutôt que sur le sol |
| `regions` | régions WorldGuard |
| `weight` | fréquence relative ; **0 = jamais d'elle-même** |
| `packMin` / `packMax` | taille de la bande posée d'un coup |
| `minSchoolLevel` | maîtrise exigée du joueur le plus proche |

`minSchoolLevel` est la porte des espèces rares : elles existent dès le
premier jour, mais un débutant ne les rencontre pas. Une créature d'élite
devant un joueur de niveau un ne lui laisse aucune chance et ne lui apprend
rien.

---

## 6. Le bestiaire

36 espèces, dont 33 apparaissent d'elles-mêmes. Les trois coffres fermés sont
du décor, à poser à la main comme fausse piste.

### 6.1 La bande gobeline — surface, nuit, plaines et forêts

C'est sur elle que se lisent les groupes : la brute prend la tête, les lames
suivent, l'archer et le mage restent derrière. Nuée à **100**.

| Espèce | Réflexion | Vie | Attaques |
| :--- | :--- | ---: | :--- |
| `goblin_melee` | `SKIRMISHER` | 22 | `atk1` `atk2` `atk3` (bond) |
| `goblin_brute` | `BRUTE` | 48 | `atk1` `atk2` `atk2_SC` (zone) |
| `goblin_ranger` | `RANGED` | 18 | `atk1` (tir 18 blocs) |
| `goblin_mage` | `CASTER` | 20 | `atk1` (trait) `atk2` (zone) |
| `goblin_whip` | `SKIRMISHER` | 24 | `atk1` (allonge 5,5 blocs) |

### 6.2 Araignées — cavernes, nuée à 50

| Espèce | Réflexion | Où | Attaques |
| :--- | :--- | :--- | :--- |
| `shadow_spider` | `STALKER` | y ≤ 55, sous terre | `attack` `attack2` `illusion` (repli) |
| `toxin_spider` | `PACK_HUNTER` | y ≤ 45 | `attack` `toxin_attack` `web_attack` |
| `flying_spider` | `PACK_HUNTER` | forêts denses, y 30–70 | `attack` `attack_fly` `attack_wings` |

### 6.3 Gardien et mimiques

| Espèce | Réflexion | Particularité |
| :--- | :--- | :--- |
| `moss_golem` | `SENTINEL` | laisse de 20 blocs : on ne l'attire pas loin de ce qu'il garde |
| `common_mimic` | `AMBUSHER` | son clip de repos est celui du coffre fermé |
| `rare_mimic` | `AMBUSHER` | y ≤ 32, maîtrise 10 |
| `legendary_mimic` | `AMBUSHER` | élite, y ≤ 24, maîtrise 25 |

### 6.4 Les quatre lots rares

Chacun tient à un lieu, et chacun a son élite.

| Lot | Lieu | Espèces | Élite |
| :--- | :--- | :--- | :--- |
| **Prairie de Grumble** | plaines, prairies fleuries | `lurking_lily` `malevolent_moss` `whispering_wisteria` `ashen_azalea` | `the_soulrot` (maîtrise 30) |
| **Bois vivant** | forêts, taïgas | `blighted_bark` `vile_vine` `broodring_blossom` `hollow_howl` | `the_hemlock` (maîtrise 35) |
| **Veine perdue** | **sous terre uniquement** | `foul_flower` `oblivion_orb_purple` `oblivion_orb_yellow` `sorrowful_sylph` `wicked_wolf` | `the_nyx` (maîtrise 40) |
| **Marais moisi** | marécages, mangroves | `brook_bug` `grossy_gator` `mangrove_glare` `wretched_weaver` | `the_cryptic` (maîtrise 45) |

Les espèces de la Veine perdue tiennent leur intérêt de la profondeur : les
croiser en plein jour casserait la promesse. Leur règle interdit le ciel.

---

## 7. Butin

Chaque ligne d'une table est tirée **indépendamment** : une table n'est pas
une roue où l'on prend un seul lot, mais une liste de choses qui peuvent
tomber ensemble. C'est ce qui permet d'écrire « toujours un peu de ceci,
parfois cela » sans multiplier les entrées.

Le niveau agit sur les **chances** et non sur les quantités, avec un plafond :
un mob de haut niveau laisse plus souvent ce qu'il laisse, jamais des piles
absurdes. Un facteur multiplicatif sur les quantités rendrait la moindre
créature de niveau élevé plus rentable qu'une expédition.

Le butin vanilla est vidé d'abord — sans cela une créature custom laissait la
chair et les os du monstre dont elle emprunte la machinerie.

---

## 8. Rendu client

Le bbmodel désigné par le `modelId` est le seul chemin. Un identifiant qui ne
correspond à rien retombe sur le rendu vanilla, **qui se voit** : substituer
silencieusement un autre modèle donnerait une créature de la mauvaise espèce
sans qu'aucun message ne le dise.

Un chef porte une teinte plus chaude, un mob qui rompt une teinte froide, la
peur passant devant le commandement. Ce n'est pas décoratif : c'est ce qui
permet de repérer la tête d'une bande avant de s'engager.

### 8.1 Le calage des animations

Une action annoncée par le serveur l'emporte sur tout, et son temps de lecture
est **celui que le serveur compte**. C'est ce calage qui fait correspondre le
geste et le moment où les dégâts tombent, donc qui rend une attaque
esquivable. Une animation lancée librement dérive de quelques ticks à chaque
répétition, et le télégraphe ne veut plus rien dire.

À défaut d'action, la locomotion est déduite du **mouvement réellement
observé** et non d'un état réseau, avec une hystérésis : un mob qui glisse
d'un tick à l'autre ne doit pas alterner marche et attente à chaque paquet.

Beaucoup de créatures n'ont pas de clip de marche — celles qui planent, qui
bondissent, qui sont enracinées. Elles jouent alors leur clip de repos pendant
le déplacement : sans ce repli elles se figeaient dans leur pose de montage,
bras écartés, en glissant sur le sol.

---

## 9. Ce que les contrôles vérifient

| Contrôle | Ce qu'il empêche |
| :--- | :--- |
| `MobMoraleTest` (25) | un mob qui ne rompt jamais, une rancune qui s'efface, un groupe qui rend invincible |
| `MobTemperamentTest` (16) | deux tempéraments qui décideraient pareil |
| `MobPackTest` (18) | une succession instable, une nuée payée deux fois |
| `MobAttackSetTest` (16) | un souffle de zone sur un joueur isolé, un renfort appelé au milieu de sa bande |
| `MobLevelsTest` (16) | une zone infranchissable, une carte sans relief |
| `MobPacksTest` (14) | deux index de groupe qui divergent |
| `MobDataWatcherPackingTest` (9) | une boîte de collision fausse, un rôle illisible |
| `ShippedMobsYmlTest` (23) | une portée inversée, une attaque sans dégâts, un élite sans porte d'entrée |
| **`MobModelClipsTest` (5)** | **un clip mal orthographié : le mob frappe sans geste** |
| `MobSpawnRulesTest` (20) | une région vide, une espèce partout |
| `MobLootAndSpawnMathTest` (24) | un butin qui ne tombe jamais, des mobs nés sous le nez du joueur |
| `MobTradersTest` (11) | un échange qui avale le paiement |

`MobModelClipsTest` est le seul qui relie les deux moitiés du système : il
passe chaque nom de clip du catalogue au bbmodel qui le porte. Un nom mal
orthographié ne lève rien — le serveur demande l'animation, le client ne la
trouve pas, le mob frappe sans geste. Les dégâts tombent quand même, mais le
joueur ne voit plus le coup venir : le combat devient injuste sans qu'aucune
erreur n'apparaisse nulle part.

Le piège est réel : le modèle du Cryptique porte un clip nommé `stome`, faute
d'orthographe comprise. Un catalogue écrivant `stomp` — l'orthographe
correcte — serait précisément faux.

---

## 10. Commandes

| Commande | Rôle |
| :--- | :--- |
| `/mobs spawn <espèce> [niveau] [nombre]` | faire apparaître à la demande |
| `/mobs list` | le catalogue, tempérament et poids |
| `/mobs info` | ce que pense le mob le plus proche : humeur, peur, rage, courage, groupe, rancune, clip |
| `/mobs purge` | retirer tous les mobs custom |
| `/mobs reload` | relire `mobs.yml` — s'applique aux créatures à naître |

`/mobs spawn` n'est pas un confort : le Nyx apparaît sous vingt-six blocs de
profondeur, dans le noir, pour un joueur de maîtrise quarante, avec un poids
d'un dixième. Autant dire jamais, quand on cherche à vérifier ses animations.

---

## 11. Reste à faire

- **Aucun modèle de créature paisible** dans le dépôt d'assets : le
  marchandage fonctionne, mais seul le Forgeron le propose.
- **Les VFX d'attaque ne sont pas encore joués.** Les modèles sont importés
  (`vfx_darkmagic_explode`, `vfx_oblivion_*`, `vfx_puff/smash/stomp`,
  `bl_toxin_spider_projectile`, `bl_toxin_spider_web`) mais rien ne les
  déclenche : il faut un canal du serveur vers le client disant « joue tel
  effet à tel endroit », comme celui des sorts.
- **`oblivion_orb_yellow` n'a pas de clip de mort** dans le modèle livré :
  elle disparaît sans geste. Le contrôle la nomme pour qu'un modèle corrigé le
  fasse tomber.
- **Cinq espèces se déplacent sur leur clip de repos**, faute d'animation de
  marche : `brook_bug`, `lurking_lily`, `malevolent_moss`, `sorrowful_sylph`,
  `the_soulrot`.
- **Les attaques `SUMMON` ne convoquent rien** encore : le clip est joué, le
  renfort n'arrive pas.
- Rien n'a été **essayé sur un serveur** : le banc de test n'existe plus dans
  cet environnement.
