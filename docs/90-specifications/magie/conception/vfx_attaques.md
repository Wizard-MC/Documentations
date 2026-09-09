# Conception des VFX d'attaque

Comment un sort se présente à l'écran, de la phase serveur au modèle animé.
Ce document est la référence pour ajouter ou retoucher l'aspect d'un sort.

Voir aussi : [`bbmodel_attaque.md`](bbmodel_attaque.md) (fabriquer le modèle),
[`../README.md`](../README.md) (index de la documentation magie).

---

## 1. Principe

Le serveur ne pousse **jamais** de particule ni de modèle. Il annonce des
événements — « ce lanceur commence une incantation de deux secondes », « ce
projectile part avec cet identifiant d'entité », « cet impact a touché cette
entité pendant quatre secondes » — et le client décide de la présentation.

Conséquence pratique : **on ne conçoit pas un VFX en écrivant du code de
rendu**. On déclare des *étapes*, et le runtime les joue.

```
Serveur                        Client
────────                       ──────
CastService                    MagicVFXRuntime
  phase + graine + durée  ──▶    ├── étape « cercle »      (modèle + runes)
  + entité concernée             ├── étape « concentration »
                                 ├── étape « projectile »
                                 ├── étape « impact »
                                 └── étape « effet sur la cible »
```

---

## 2. Les étapes d'un sort

| Étape | Déclenchée par | Rôle | Exemple |
|---|---|---|---|
| **Cercle d'incantation** | `CAST_START`, entretenue par `CAST_STATE` | Annonce le sort, occupe le temps de cast | sceau devant la baguette |
| **Concentration** | `CAST_START` (modes à incantation) | Montre la puissance qui s'accumule **chez le lanceur** | boule de braise qui grossit au bout de la baguette |
| **Projectile** | `PROJECTILE` | Le corps qui vole | boule de feu, orbe de foudre |
| **Libération** | `RELEASE` | Le trait entre le lanceur et sa visée | rayon d'un sort instantané |
| **Attaque** | `IMPACT` | Le corps de l'effet, **au point touché** | gerbe de pics de givre, vague de roche |
| **Impact** | `IMPACT` | L'éclat bref, par-dessus l'attaque | explosion, flash |
| **Effet sur la cible** | `IMPACT` + entité ciblée | Ce qui reste sur la victime | gangue de glace, chaînes |
| **Aura** | `AURA` | Ce qui reste sur le lanceur | lanterne portée, anneau de pierre, voile d'ombre |

Une étape absente n'est pas jouée : un sort n'a pas besoin des huit.

### Concentration ou attaque : la distinction qui compte

C'est la confusion qui a coûté le plus cher. La gerbe de pics de givre, la
vague de roche et la colonne de lumière avaient été rangées dans l'étape de
**concentration**, ancrée aux pieds du lanceur. L'attaque se jouait donc en
entier sur le mage, et la cible ne recevait qu'un éclat de moins de deux blocs.
Rien ne le signalait : les deux ancrages sont légitimes, pris séparément.

La règle est maintenant explicite, et vérifiée par
`VfxModelCatalogTest.testStagesAreAnchoredWhereTheyBelong` :

- **concentration** et **aura** sont ancrées sur le lanceur — `CASTER_FEET`,
  `CASTER_EYES`, `WAND` ;
- **attaque** et **impact** sont ancrés sur le point touché — `TARGET`,
  `TARGET_ENTITY`.

Si un modèle montre ce que le sort *fait*, il va dans l'attaque. S'il montre ce
que le lanceur *prépare*, il va dans la concentration.

### Bouclée, étirée, ou jouée une fois

Trois façons de dérouler une animation, et une seule est juste pour chaque
étape :

| Mode | Ce que ça fait | Pour quoi |
|---|---|---|
| **une fois** (défaut) | l'animation joue sa longueur puis tient sa dernière pose | impacts, attaques |
| `loop(true)` | l'animation repart au début tant que l'étape dure | sceaux au sol, auras, projectiles en vol |
| `stretch(true)` | l'animation est ramenée à la durée annoncée par le serveur | cercle d'incantation |

Le cercle d'incantation était bouclé. Sur une incantation de cinq secondes, son
animation d'une seconde et demie se redessinait trois fois et demie : le joueur
voyait un motif tourner, pas un sort se préparer, et rien n'indiquait le moment
où il allait partir. Étiré, il se trace une seule fois, du premier trait au
sceau achevé, quelle que soit la durée du cast.

Les deux modes s'excluent : `stretch(true)` annule `loop(true)`. Sans durée
annoncée par le serveur, l'étirement n'a pas de cible et le clip retombe sur sa
longueur naturelle.

### Une étape déclarée doit être jouée — et annoncée

Un emplacement rempli dans le catalogue mais qu'aucune phase ne dépêche ne
produit rien, et ne lève aucune erreur. L'aura a vécu ainsi plusieurs versions :
Voile d'Ombre, anneau de Roc et feux-follets d'Esprit étaient chargés au
démarrage et n'apparaissaient jamais.
`VfxModelCatalogTest.testEveryStageIsPlayedByTheRuntime` relit la source du
runtime et échoue si un emplacement cesse d'y être dépêché.

Être dépêché ne suffit pourtant pas. Une aura bouclée n'est jouée que si le
serveur **annonce sa durée**, sans quoi le runtime la refuse — et il a raison,
un effet bouclé sans fin connue tourne pour toujours. La libération d'un sort ne
porte aucune durée ; c'est la phase `AURA` qui la porte. Une école peut donc
déclarer une aura parfaite et ne rien voir tant que la mécanique ne l'annonce
pas.

### La lueur portée

La lanterne de la Torche de Braise ajoute une chose qu'aucune autre étape ne
fait : elle **éclaire**. La lumière n'est pas un modèle, elle est composée dans
`getLightBrightnessForSkyBlocks`, le point par lequel passe toute la luminosité
du monde — terrain comme entités.

Deux détails valent d'être connus avant d'en ajouter une deuxième :

- la lumière du **terrain** est cuite dans les listes d'affichage d'un chunk. Il
  faut redemander le rendu de la zone quand le porteur change de bloc, sinon la
  lueur n'apparaît qu'au prochain rafraîchissement, c'est-à-dire presque jamais.
  Les entités, elles, lisent la luminosité à chaque image ;
- seul le **joueur local** est éclairé. La lanterne d'un autre joueur reste
  visible comme modèle, mais n'éclaire pas notre écran : la lumière est un
  confort de jeu, pas une information partagée.

### Compagne, pas décalage

La lanterne était soudée au lanceur : décalage fixe, lacet du joueur. Elle
pivotait autour du mage à chaque coup de souris et se téléportait avec lui à
chaque pas, ce qui la faisait lire comme un élément d'interface collé à l'écran
plutôt que comme une chose à côté de soi.

Un clip marqué `companion(true)` délègue sa position à `CompanionMotion`, qui
lui donne les trois traits d'un Allay — et aucun n'est décoratif :

- **le retard** — elle va vers où vous êtes, pas où vous êtes. Elle traîne au
  départ, vous rattrape et dépasse un peu à l'arrêt. C'est cette seule règle qui
  donne le poids ;
- **la dérive** — sa place autour de vous tourne lentement, elle n'occupe jamais
  deux fois le même point ;
- **le flottement** — elle monte et redescend, sans rapport avec ce que vous
  faites.

Sur un clip compagnon, `offset(avant, haut)` cesse de désigner un décalage
figé : le premier terme devient le **rayon** autour du lanceur, le second sa
**hauteur de repos**. Au-delà de douze blocs d'écart la lanterne est reposée
plutôt que de traverser le décor pour rattraper une téléportation.

La phase de dérive vient de la **graine du sort** : deux lanternes ne tournent
pas en miroir, mais tous ceux qui regardent la même lanterne la voient au même
endroit.

### Éclore plutôt qu'apparaître

`summon(ticks)` fait sortir un modèle d'un nuage de particules au lieu de le
rendre visible d'un coup. Pendant la fenêtre déclarée, des braises convergent
vers sa place et le modèle enfle depuis rien, avec un léger dépassement avant de
se poser à sa taille. Ce ressort — pas une rampe — est ce qui fait lire une
éclosion.

### Un sort sur soi ne frappe rien

Un sort qui se résout sur son lanceur n'a **ni attaque ni impact**. Leur donner
l'un ou l'autre fait raconter à l'animation une détonation qui n'a pas lieu :
c'est ce qui faisait exploser une boule de feu devant le mage quand il allumait
sa Torche. Douze sorts sont concernés, reconnus par leur portée nulle ou d'un
bloc.

Retirer l'impact laisse cependant un trou. Sans aura, ces sorts n'auraient plus
rien à montrer après le cercle, et le joueur ne saurait pas si le sort est parti.
Une **aura de repli** couvre donc toute école qui n'en déclare pas ; celles qui
en déclarent une gardent la leur.

### Étape entretenue ou ponctuelle

Une étape « sur la cible » **entretenue** (`loop`) n'est jouée que si le
serveur annonce une durée d'au moins 20 ticks. C'est la règle qui empêche un
simple éclat de givre d'emprisonner sa victime : seule une mécanique qui
déclare explicitement sa durée obtient une entrave visible.

Une étape ponctuelle se joue dès qu'une entité est touchée.

---

## 3. Ancrage et orientation

Un même modèle rendu au mauvais endroit ruine l'effet. Deux réglages le
déterminent.

### Ancres

| Ancre | Où | Pour quoi |
|---|---|---|
| `CASTER_FEET` | Pieds du lanceur | sceaux au sol, ondes, auras |
| `CASTER_EYES` | Yeux du lanceur | effets de tête, halos |
| `WAND` | Devant la baguette | cercle dressé, concentration |
| `ORIGIN` | Point de départ de l'événement | projectiles |
| `TARGET` | Point d'arrivée | impacts au sol |
| `TARGET_ENTITY` | Sur la victime, qu'elle bouge ou non | entraves, prisons |

### Orientations

| Orientation | Effet |
|---|---|
| `WORLD` | Aucun pivot — pour un modèle déjà posé à plat |
| `FACE_CAST` | Pivote en lacet vers le sens du lancer |
| `TOWARD_TARGET` | Pointe vers la destination de l'événement |
| `ENTITY_YAW` | Suit le lacet de l'entité portante |
| `UPRIGHT_FACE_CAST` | Bascule le plan à la verticale puis vers la visée |

Un sceau conçu à plat (plan XZ) posé au sol prend `WORLD` ou `ENTITY_YAW`.
Un sceau conçu debout (plan XY) devant la baguette prend `FACE_CAST`.

---

## 4. Taille : ne jamais écrire une échelle

Les VFX du lot d'attaques ont été composés dans des scènes indépendantes : un
sceau y mesure cinq blocs, une explosion dix. Écrire une échelle à la main
oblige à mesurer chaque fichier — et à tout reprendre au moindre ré-import.

On déclare donc une **taille voulue en blocs**. Le runtime mesure
l'encombrement réel du maillage et en déduit l'échelle, puis recentre le
modèle sur son ancre en le posant par sa base.

Repères utiles :

| Élément | Taille |
|---|---|
| Cercle devant la baguette | 2 blocs |
| Sceau au sol (canalisation) | 3 à 3,5 blocs |
| Projectile | 0,7 à 1,1 bloc |
| Impact ponctuel | 1,5 à 2 blocs |
| Explosion | 3 à 3,5 blocs |
| Entrave sur un joueur | 1,9 bloc (un joueur fait 1,8) |

---

## 5. Ajouter le VFX d'un sort

1. **Choisir le modèle.** Auditer les candidats avec l'outil du dépôt MCP :

   ```bash
   java ... fr.wizardmc.magic.vfx.test.VfxAssetAudit /chemin/vers/ASSETS
   ```

   Il liste, fichier par fichier, les cubes retenus, les bones, les vertices et
   les animations avec leur durée et leur mode de boucle. Un modèle affiché
   avec `vertices=0` se chargerait en jeu sans rien afficher.

2. **Importer.** Ajouter une ligne au manifeste de
   `MCP/tools/import_vfx_models.py`, puis lancer le script. Il garde une
   animation utile par fichier et corrige les modèles dont la géométrie
   visible est marquée hors export.

3. **Déclarer le clip** dans `SpellVfxModels` (dépôt MCP) : modèle, animation,
   durée relevée, ancre, orientation, taille, teinte.

4. **Vérifier.** `VfxModelCatalogTest` confronte chaque clip au fichier :
   présence de l'asset, présence de l'animation, durée annoncée à un tick près.

5. **Regarder.** En jeu :

   ```text
   /magicvfx test <sort> cast_start
   /magicvfx test <sort> impact
   /magicvfx debug
   ```

   La sous-commande `test` rejoue la présentation localement, sans lancer le
   sort ni dépenser d'Essence.

6. **Régénérer la documentation** avec `MagicDocGenerator` : la fiche du sort
   reprend automatiquement les nouvelles étapes.

---

## 6. Ce qui reste procédural

Le modèle ne fait pas tout. Se superposent au sceau :

- **l'inscription des runes** — glyphes tirés de la graine du cast, écrits un à
  un, l'écriture s'étirant pour se terminer aux trois quarts du sort quelle que
  soit sa durée ;
- **la lueur centrale**, qui pulse ;
- **les particules** de charge, d'ambiance, de trail et d'impact, dérivées de
  l'identité de l'école ;
- **le ruban** derrière les projectiles.

Quand un modèle de cercle est déclaré, les anneaux texturés procéduraux ne sont
pas émis : deux sceaux superposés sur un même lancement seraient illisibles.

---

## 7. Règles à ne pas enfreindre

1. **Le serveur reste autoritaire.** Un VFX ne décide jamais d'un dégât, d'une
   portée ou d'un état. Il montre ce qui a déjà été tranché.
2. **Pas de packet par particule.** Une phase, une graine, une durée — le
   client génère le reste localement.
3. **Le déterminisme passe par la graine.** Jamais de `Random` local pour ce
   que plusieurs joueurs doivent voir pareil.
4. **Tout effet doit dégrader proprement.** Modèle absent, son absent, école
   inconnue : on retombe sur un repli, jamais sur un écran vide ou un crash.
5. **Le budget est un plafond dur.** 96 effets simultanés, 3000 particules. En
   combat massif la densité baisse ; aucun effet déjà commencé ne disparaît.
6. **Rien d'illisible.** Un état de jeu — entrave, invulnérabilité, soin — doit
   se lire depuis l'extérieur, pas seulement chez celui qui le subit.
7. **Un modèle se rend en mélange alpha.** Sans état de mélange, les zones
   transparentes d'une texture se rendent opaques : le cercle d'incantation, qui
   est un disque peint sur un plan carré, apparaissait comme un carré plein qui
   tournait. Le mélange **additif** est réservé aux particules et aux traits
   lumineux — un modèle porte des zones sombres qui doivent le rester.
8. **Deux plans coplanaires scintillent.** L'import
   (`tools/import_vfx_models.py`) les sépare d'un demi-centimètre. C'est fait à
   l'import et non à la main pour qu'un ré-import ne réintroduise pas le défaut.
9. **Un sort a toujours une étape finale.** Impact s'il frappe, aura s'il se
   résout sur son lanceur. Jamais rien.
