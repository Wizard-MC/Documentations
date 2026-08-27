# CDC — Le Colosse de l'Ère (modèle 3D)

Cahier des charges du modèle animé du boss mondial, prêt à transmettre à
Kodari. Trois variantes de thème, un jeu d'animations, et les effets qui
accompagnent ses frappes.

Voir aussi : [`../cdc_eres.md`](../cdc_eres.md) §2.2 (le boss dans le système
d'Ères), [`../magie/conception/bbmodel_attaque.md`](../magie/conception/bbmodel_attaque.md)
(ce que le chargeur `.bbmodel` du client lit et refuse),
[`../magie/conception/vfx_attaques.md`](../magie/conception/vfx_attaques.md)
(vocabulaire des étapes d'un effet).

---

## 📐 Description générale

Le Colosse est la **dernière chose que voit une Ère**. Il paraît en phase de
Cataclysme, dans les trois derniers jours d'un cycle de quarante-cinq, et le
serveur entier a deux heures pour l'abattre. Il ne reparaît jamais dans la même
Ère.

Ce n'est donc pas un mob de plus : c'est un décor de fin du monde qui marche.
Il doit se lire **de loin**, se reconnaître **en une seconde**, et donner
l'impression qu'il était déjà là avant qu'on arrive.

**Ce qu'il est.** Un géant de pierre et de roche fracturée, tenu par une force
qui n'est pas la sienne. Les Terres Fracturées ont été brisées par la guerre
des Arcanes ; le Colosse en est un morceau qui s'est levé. Entre ses plaques de
pierre, on voit la lumière de la faille — c'est ce qui l'anime, et c'est ce
qu'on frappe.

**Ce qu'il n'est pas.** Ni un golem de fer, ni une armure vide, ni un
squelette. Pas de visage humain, pas de bouche, pas d'expression. Une masse
minérale et deux points de lumière à la place des yeux.

**Silhouette.** Trapue et large plutôt que haute et fine. Épaules très larges,
bras longs qui descendent sous le bassin, jambes courtes et massives, pas de
cou. La tête est un bloc enfoncé entre les épaules. Le centre de la poitrine
est **creux** : une cage de pierre ouverte où flotte le cœur de faille, seul
élément lumineux visible au repos.

**Trois variantes.** Le nom du Colosse suit le thème de l'Ère — Colosse des
Ténèbres, Colosse d'Aurore, Colosse des Fractures. **La géométrie est
strictement la même dans les trois.** Seule la texture change. C'est le point
le plus important du lot : trois maillages différents coûteraient trois fois le
travail d'animation pour un résultat que le joueur ne verrait qu'une fois par
Ère.

---

## 🏗️ Formes

### Le corps commun

| Élément | Détail |
|---|---|
| **Hauteur** | 5 blocs (80 unités Blockbench) du sol au sommet des épaules |
| **Largeur** | 3,5 blocs (56 unités) d'épaule à épaule |
| **Profondeur** | 2 blocs (32 unités) au torse |
| **Forme** | trapèze inversé : très large en haut, resserré aux hanches |
| **Base** | plante des pieds à `Y = 0`, modèle centré sur `X = 0, Z = 0` |
| **Budget** | 120 à 200 cubes. La limite du chargeur est à 4096, ce n'est pas une cible |

**Découpe en groupes.** Chaque groupe de l'`outliner` est un os : le chargeur
n'anime **que** des groupes, jamais un cube isolé. Il en faut donc un par
partie mobile, et pas davantage.

```
colosse
├── bassin
│   ├── jambe_gauche ── genou_gauche ── pied_gauche
│   ├── jambe_droite ── genou_droit  ── pied_droit
│   └── torse
│       ├── coeur           (le cœur de faille, dans la cage thoracique)
│       ├── tete
│       ├── epaule_gauche ── bras_gauche ── avant_bras_gauche ── poing_gauche
│       └── epaule_droite ── bras_droit  ── avant_bras_droit  ── poing_droit
└── debris                  (3 à 5 éclats de roche en orbite lente autour du torse)
```

Dix-neuf groupes. Les pivots comptent autant que la géométrie : un pivot de
coude placé au milieu de l'avant-bras produit une articulation qui se
disloque à l'animation, et ça ne se voit qu'une fois le modèle en jeu.

**Le cœur de faille.** Un octaèdre — six à huit cubes fins croisés — logé au
centre du torse, dans une cage de pierre ouverte sur l'avant. Il ne touche
aucun autre groupe : il flotte. C'est le seul élément qui bouge au repos, et
c'est la cible visuelle du combat.

**Les débris en orbite.** Trois à cinq éclats de roche irréguliers, entre 0,3
et 0,6 bloc, tournant lentement autour du torse à environ 2,5 blocs du centre.
Ils donnent l'échelle et signalent de loin que la chose est animée. Un seul
groupe `debris` qui tourne suffit — inutile de les animer séparément.

**Textures.** 128 × 128 px par variante, style pixel cohérent avec le reste du
lot (pas de dégradé lisse, pas de bruit photographique). Chaque face porte son
UV propre — le mode *box UV* de Blockbench fait **refuser** le fichier par le
chargeur.

### Contraintes techniques

Elles ne sont pas négociables : le chargeur du client les vérifie et refuse ou
ignore silencieusement ce qui sort du cadre.

| Contrainte | Détail |
|---|---|
| Format de projet | *Free Model* ou *Generic Model* — **jamais** *Java Block/Item* |
| UV | **Per-face UV** obligatoire. `meta.box_uv: true` → fichier refusé |
| Géométrie | **cubes uniquement**. Les `mesh` et `locator` sont ignorés sans erreur |
| Textures | **embarquées en base64** dans le `.bbmodel`, jamais liées par chemin |
| Taille de texture | ≤ 4096 px (on vise 128) |
| Pose de repos | **T-pose** bras à l'horizontale, jambes droites, toutes rotations à zéro |
| Unités | 16 unités Blockbench = 1 bloc |
| Canaux d'animation | `position`, `rotation`, `scale` — **et rien d'autre** |
| Opacité | **n'existe pas**. Une disparition se fait à l'échelle ou hors champ |
| Molang | non lu. `math.sin(...)` vaut `0` — tout mouvement se fait par keyframes |
| Drapeaux | tout ce qui doit s'afficher est **visible et `export: true`** |
| Marge vide | aucun cube transparent hors du corps : il élargit l'encombrement mesuré et rétrécit tout le reste |

---

## 🎬 Animations

Le nom de l'animation est **contractuel** : il est recopié tel quel dans le
code du client, et la durée déclarée est confrontée au fichier. Rallonger un
clip sans le dire casse l'enchaînement.

La nomenclature suit celle déjà livrée pour les compagnons (`Idle`, `marche`,
`attaque_*`, `defense_*`) : le résolveur d'animations du client la connaît
déjà.

| Nom | Durée | Ticks | Boucle | Rôle |
|---|---|---|---|---|
| `apparition` | 3,00 s | 60 | `once` | il sort du sol |
| `Idle` | 4,00 s | 80 | `loop` | respiration de pierre, cœur qui pulse |
| `marche` | 1,20 s | 24 | `loop` | pas lourd, deux appuis |
| `attaque_ecrasement` | 2,00 s | 40 | `once` | les deux poings au sol |
| `attaque_balayage` | 1,60 s | 32 | `once` | revers du bras droit |
| `attaque_faille` | 2,40 s | 48 | `once` | ouvre le torse, projette la lumière |
| `defense_carapace` | 2,40 s | 48 | `once` | se referme sur son cœur |
| `blesse` | 0,50 s | 10 | `once` | recul bref, superposable |
| `mort` | 4,00 s | 80 | `hold` | il se défait |

Toutes les durées sont des multiples de 0,05 s : un tick serveur. Une durée qui
tombe entre deux ticks se joue tronquée.

### `apparition` — 3,00 s

Le Colosse commence **entièrement sous le sol**, groupe racine à `Y = -80`
unités. Il monte en trois temps, pas en un mouvement continu : c'est ce qui
donne le poids.

| Temps | Pose |
|---|---|
| 0,00 s | racine à `Y = -80`, invisible sous le sol |
| 0,60 s | les deux poings crèvent la surface, le reste encore enfoui |
| 1,40 s | épaules et tête sortent, dos courbé, bras tendus vers l'avant |
| 2,20 s | il se déplie — la colonne se redresse, le cœur s'allume |
| 3,00 s | pose de garde, poings serrés, légèrement penché en avant |

Le cœur de faille est **éteint jusqu'à 2,20 s** (échelle 0), puis grossit à sa
taille pleine en 0,4 s. C'est le seul moment où l'on peut faire apparaître
quelque chose : sans canal d'opacité, l'échelle est le seul levier.

### `Idle` — 4,00 s, bouclée

Il ne fait presque rien, et c'est voulu : un boss qui s'agite au repos perd sa
masse.

- le torse monte de 1,5 unité et redescend, une fois sur les quatre secondes ;
- les épaules suivent avec 0,3 s de retard — c'est ce décalage qui fait la
  respiration, pas l'amplitude ;
- le cœur de faille pulse **deux fois** par cycle, échelle 1,0 → 1,15 → 1,0,
  en `catmullrom` ;
- le groupe `debris` fait un tour complet sur le cycle, rotation `Y` linéaire
  de 0° à 360° ;
- la tête tourne de ±4° en `catmullrom`, décalée d'une demi-seconde du torse.

Aucun mouvement de jambe. Aucun déplacement de la racine.

### `marche` — 1,20 s, bouclée

Deux appuis par cycle, 0,6 s chacun. Le poids compte plus que la vitesse.

- amplitude de hanche ±18°, genou jusqu'à 35° en phase de retour ;
- à chaque poser de pied, la racine descend de 2 unités en `step` puis remonte
  en `linear` sur 0,15 s — c'est l'impact ;
- les bras se balancent en opposition, ±12° seulement : des bras de cette masse
  ne se balancent pas ;
- le cœur garde sa pulsation de `Idle`, sans se caler sur les pas.

### `attaque_ecrasement` — 2,00 s

La frappe principale, celle qu'on voit venir.

| Temps | Pose |
|---|---|
| 0,00 s | garde |
| 0,70 s | les deux bras levés au-dessus de la tête, torse cambré en arrière — **c'est la fenêtre d'esquive**, elle doit être lisible |
| 0,95 s | maintien de la pose haute (`step`) : un temps mort qui annonce le coup |
| 1,15 s | poings au sol, genoux fléchis, torse plié |
| 1,35 s | rebond : la racine remonte de 3 unités |
| 2,00 s | retour à la garde |

Le contact est à **1,15 s** (tick 23). C'est l'instant où le VFX d'onde part et
où le serveur applique les dégâts.

### `attaque_balayage` — 1,60 s

Le revers, pour ce qui reste au corps à corps.

- 0,00 → 0,50 s : armé, bras droit ramené derrière l'épaule gauche, torse en
  torsion de 35° ;
- 0,50 → 0,80 s : le bras balaie sur 180° devant lui, le torse suit et
  dépasse de 25° de l'autre côté ;
- 0,80 → 1,60 s : retour lent à la garde, le bras traîne.

Contact à **0,65 s** (tick 13).

### `attaque_faille` — 2,40 s

L'attaque à distance : il ouvre sa poitrine et libère la lumière de la faille.

| Temps | Pose |
|---|---|
| 0,00 → 0,80 s | il se penche en avant, bras écartés, cage thoracique qui s'ouvre — les cubes de la cage pivotent vers l'extérieur de 40° |
| 0,80 → 1,20 s | le cœur grossit à l'échelle 2,0, la tête bascule en arrière |
| 1,20 s | **libération** : le cœur revient à 1,0 en deux ticks (`step`) |
| 1,20 → 2,40 s | la cage se referme, il se redresse |

Libération à **1,20 s** (tick 24).

### `defense_carapace` — 2,40 s

Il protège son cœur — la seule pose où le point faible disparaît.

- 0,00 → 0,40 s : les bras se croisent devant le torse, la tête rentre ;
- 0,40 → 2,00 s : maintien, avec un tremblement de ±1,5° sur le torse à 6 Hz —
  ce n'est pas du bruit, c'est ce qui montre l'effort ;
- 2,00 → 2,40 s : il rouvre.

Le cœur passe à l'échelle 0,7 pendant le maintien : moins visible, donc moins
attirant. La lecture doit être immédiate — **frapper maintenant ne sert à
rien**.

### `blesse` — 0,50 s

Recul du torse de 6° et de 2 unités vers l'arrière, retour en `catmullrom`.
Rien de plus : cette animation se joue par-dessus les autres, elle ne doit pas
casser une pose d'attaque en cours.

### `mort` — 4,00 s, `hold`

| Temps | Pose |
|---|---|
| 0,00 → 1,00 s | il tombe à genoux, mains au sol |
| 1,00 → 2,00 s | le cœur s'emballe : trois pulsations rapides, échelle jusqu'à 1,6 |
| 2,00 s | le cœur passe à l'échelle 0 en un tick — il s'éteint |
| 2,00 → 3,20 s | sans lui, la structure se défait : chaque groupe s'écarte de 2 à 6 unités de son parent et tombe |
| 3,20 → 4,00 s | l'ensemble s'affaisse sous le sol, racine à `Y = -30` |

Le clip **tient sa dernière pose** (`hold`) : c'est le runtime qui retire
l'entité, pas l'animation.

---

## ✨ VFX des attaques

Les effets ne font pas partie du `.bbmodel` du Colosse : ils sont des modèles
séparés, déclarés en étapes et joués par le runtime VFX du client (voir
[`vfx_attaques.md`](../magie/conception/vfx_attaques.md) §2). Kodari les livre
comme des `.bbmodel` autonomes, aux mêmes contraintes techniques.

| Attaque | Étape | Modèle | Durée | Orientation |
|---|---|---|---|---|
| `attaque_ecrasement` | attaque | `colosse_onde_sol` — anneau de roche soulevée, 6 blocs, plan XZ | 0,80 s `once` | `WORLD` |
| `attaque_ecrasement` | impact | `colosse_eclat` — gerbe de 4 plans croisés | 0,40 s `once` | `WORLD` |
| `attaque_balayage` | attaque | `colosse_arc` — arc de poussière de 180°, plan XZ | 0,50 s `once` | `ENTITY_YAW` |
| `attaque_faille` | concentration | `colosse_coeur_charge` — sphère de failles qui se resserre | 1,20 s `stretch` | `ENTITY_YAW` |
| `attaque_faille` | projectile | `colosse_trait` — éclat de lumière allongé | 0,60 s `loop` | `ENTITY_YAW` |
| `attaque_faille` | impact | `colosse_faille` — fissure lumineuse au sol, plan XZ | 1,00 s `once` | `WORLD` |
| `defense_carapace` | aura | `colosse_carapace` — coque de pierre translucide | 1,00 s `loop` | `ENTITY_YAW` |

**Des plans, pas des volumes.** Un effet est fait de cubes d'épaisseur nulle
texturés en alpha additif. Un anneau de six blocs, c'est un plan et une
texture — pas un tore modélisé.

**Le piège de l'orientation.** Un effet modélisé à plat (plan XZ) et déclaré
face au joueur apparaît sur la tranche : le joueur voit une ligne, et rien ne
le signale. Les effets au sol se modélisent **à plat, base à `Y = 0`**.

---

## 🖌️ Palette

Une géométrie, trois textures. Les couleurs sont reprises des écoles
correspondantes du système de magie, pour que le Colosse d'une Ère parle la
même langue visuelle que les sorts de cette Ère.

### Commun aux trois variantes

| Rôle | Hex | Note |
|---|---|---|
| Coutures de faille | `#D4A5FF` | l'arcane, cause de la fracture du monde — le fil qui relie les trois variantes |
| Ombre portée / creux | `#14121A` | fond de toutes les cavités |
| Poussière / éclats | `#8A8079` | débris en orbite, base neutre |

### Colosse des Ténèbres (thème `DARKNESS`)

| Rôle | Hex |
|---|---|
| Roche, face éclairée | `#3A3644` |
| Roche, face à l'ombre | `#1E1B24` |
| Plaques d'armure | `#2D2A32` |
| Veines internes | `#6B5B95` |
| Cœur de faille | `#9B7FD4` |
| Yeux | `#C9A9FF` |

### Colosse d'Aurore (thème `LIGHT`)

| Rôle | Hex |
|---|---|
| Roche, face éclairée | `#EFE8D6` |
| Roche, face à l'ombre | `#B9AC8C` |
| Plaques d'armure | `#D9CBA3` |
| Veines internes | `#FFE66D` |
| Cœur de faille | `#FFF3B0` |
| Yeux | `#FFFFFF` |

### Colosse des Fractures (thème `CHAOS`)

| Rôle | Hex |
|---|---|
| Roche, face éclairée | `#6B4F45` |
| Roche, face à l'ombre | `#332623` |
| Plaques d'armure | `#4A3B38` |
| Veines internes | `#C23B22` |
| Cœur de faille | `#FF6A3D` |
| Yeux | `#FFB347` |

---

## 🎯 Prompt Kodari

À copier tel quel.

```markdown
Crée un modèle 3D animé au format .bbmodel (Blockbench) pour un boss mondial
de Minecraft 1.7.10 : LE COLOSSE DE L'ÈRE.

## Ce que c'est
Un géant de pierre et de roche fracturée, tenu par une force qui n'est pas la
sienne. Entre ses plaques de pierre on voit la lumière d'une faille : c'est
elle qui l'anime. Ce n'est ni un golem de fer, ni une armure vide, ni un
squelette. Pas de visage humain, pas de bouche : une masse minérale et deux
points de lumière à la place des yeux.

## Silhouette
Trapue et large, pas haute et fine. Épaules très larges, bras longs qui
descendent sous le bassin, jambes courtes et massives, pas de cou, tête
enfoncée entre les épaules. Le centre de la poitrine est CREUX : une cage de
pierre ouverte où flotte un cœur de faille lumineux, qui ne touche rien.
Trois à cinq éclats de roche tournent lentement autour du torse.

## Dimensions
- Hauteur : 80 unités Blockbench (5 blocs), du sol au sommet des épaules
- Largeur : 56 unités (3,5 blocs) d'épaule à épaule
- Profondeur : 32 unités (2 blocs) au torse
- Base : plante des pieds à Y = 0, modèle centré sur X = 0 et Z = 0
- Budget : 120 à 200 cubes

## Hiérarchie des groupes (obligatoire — seuls les groupes s'animent)
colosse
├── bassin
│   ├── jambe_gauche > genou_gauche > pied_gauche
│   ├── jambe_droite > genou_droit > pied_droit
│   └── torse
│       ├── coeur
│       ├── tete
│       ├── epaule_gauche > bras_gauche > avant_bras_gauche > poing_gauche
│       └── epaule_droite > bras_droit > avant_bras_droit > poing_droit
└── debris
Placer les pivots exactement aux articulations (épaule, coude, hanche, genou).

## Contraintes techniques (le chargeur refuse le fichier sinon)
- Format de projet : Free Model ou Generic Model. JAMAIS Java Block/Item.
- UV : Per-face UV obligatoire. box_uv = true fait refuser le fichier.
- Géométrie : cubes uniquement. Les mesh et locators sont ignorés.
- Textures : embarquées en base64 dans le .bbmodel, 128x128 px, style pixel,
  pas de dégradé lisse ni de bruit photographique.
- Pose de repos : T-pose, bras à l'horizontale, toutes rotations à zéro.
- Canaux d'animation : position, rotation, scale uniquement. L'opacité
  n'existe pas : une disparition se fait par l'échelle.
- Aucun Molang : tout mouvement se fait par keyframes.
- Tous les cubes visibles et export = true. Aucun cube transparent hors du
  corps.

## Animations (noms et durées contractuels)
| Nom | Durée | Boucle |
|---|---|---|
| apparition | 3,00 s | once |
| Idle | 4,00 s | loop |
| marche | 1,20 s | loop |
| attaque_ecrasement | 2,00 s | once |
| attaque_balayage | 1,60 s | once |
| attaque_faille | 2,40 s | once |
| defense_carapace | 2,40 s | once |
| blesse | 0,50 s | once |
| mort | 4,00 s | hold |

- apparition : il sort du sol en trois temps. Racine à Y = -80 au départ. À
  0,60 s les poings crèvent la surface ; à 1,40 s épaules et tête sortent, dos
  courbé ; à 2,20 s il se déplie et le cœur s'allume (échelle 0 → 1 en 0,4 s) ;
  à 3,00 s pose de garde.
- Idle : presque immobile. Le torse monte de 1,5 unité et redescend une fois
  par cycle ; les épaules suivent avec 0,3 s de retard ; le cœur pulse deux
  fois (échelle 1,0 → 1,15 → 1,0, catmullrom) ; le groupe debris fait un tour
  complet (rotation Y 0° → 360°, linéaire) ; la tête tourne de ±4°. Aucun
  mouvement de jambe.
- marche : deux appuis de 0,6 s. Hanche ±18°, genou jusqu'à 35°. À chaque
  poser de pied la racine descend de 2 unités en step puis remonte en 0,15 s.
  Bras en opposition, ±12° seulement.
- attaque_ecrasement : garde ; à 0,70 s les deux bras levés au-dessus de la
  tête, torse cambré en arrière ; maintien de cette pose jusqu'à 0,95 s
  (interpolation step) ; à 1,15 s les poings frappent le sol, genoux fléchis ;
  à 1,35 s rebond de 3 unités vers le haut ; retour à la garde à 2,00 s.
- attaque_balayage : armé jusqu'à 0,50 s (bras droit derrière l'épaule gauche,
  torse en torsion de 35°) ; balayage de 180° entre 0,50 et 0,80 s, le torse
  dépasse de 25° de l'autre côté ; retour lent à la garde.
- attaque_faille : il se penche en avant et la cage thoracique s'ouvre (les
  cubes de la cage pivotent de 40° vers l'extérieur) jusqu'à 0,80 s ; le cœur
  grossit à l'échelle 2,0 et la tête bascule en arrière jusqu'à 1,20 s ; à
  1,20 s le cœur revient à 1,0 en deux ticks (step) ; la cage se referme.
- defense_carapace : bras croisés devant le torse et tête rentrée en 0,40 s ;
  maintien jusqu'à 2,00 s avec un tremblement de ±1,5° sur le torse à 6 Hz ;
  réouverture. Le cœur passe à l'échelle 0,7 pendant le maintien.
- blesse : recul du torse de 6° et de 2 unités vers l'arrière, retour en
  catmullrom. Rien d'autre : ce clip se superpose aux autres.
- mort : à genoux en 1,00 s ; le cœur s'emballe (trois pulsations, échelle
  jusqu'à 1,6) jusqu'à 2,00 s ; à 2,00 s le cœur passe à l'échelle 0 en un
  tick ; entre 2,00 et 3,20 s chaque groupe s'écarte de 2 à 6 unités de son
  parent et tombe ; l'ensemble s'affaisse sous le sol (racine Y = -30).

## Trois variantes
Livrer TROIS fichiers .bbmodel à la GÉOMÉTRIE ET AUX ANIMATIONS IDENTIQUES,
qui ne diffèrent que par la texture embarquée.

Communs aux trois : coutures de faille #D4A5FF, creux et ombres portées
#14121A, poussière et débris #8A8079.

1. colosse_tenebres — roche éclairée #3A3644, roche à l'ombre #1E1B24,
   plaques #2D2A32, veines #6B5B95, cœur #9B7FD4, yeux #C9A9FF
2. colosse_aurore — roche éclairée #EFE8D6, roche à l'ombre #B9AC8C,
   plaques #D9CBA3, veines #FFE66D, cœur #FFF3B0, yeux #FFFFFF
3. colosse_fractures — roche éclairée #6B4F45, roche à l'ombre #332623,
   plaques #4A3B38, veines #C23B22, cœur #FF6A3D, yeux #FFB347

## Effets d'attaque (fichiers .bbmodel séparés)
Mêmes contraintes techniques. Faits de plans (cubes d'épaisseur nulle) en
alpha additif, pas de volumes. Ceux qui se posent au sol se modélisent à plat
dans le plan XZ, base à Y = 0.

| Fichier | Description | Taille | Durée | Boucle |
|---|---|---|---|---|
| colosse_onde_sol | anneau de roche soulevée, au sol | 6 blocs | 0,80 s | once |
| colosse_eclat | gerbe de 4 plans croisés | 3 blocs | 0,40 s | once |
| colosse_arc | arc de poussière de 180°, au sol | 5 blocs | 0,50 s | once |
| colosse_coeur_charge | sphère de failles qui se resserre | 2 blocs | 1,20 s | once |
| colosse_trait | éclat de lumière allongé | 1,5 bloc | 0,60 s | loop |
| colosse_faille | fissure lumineuse au sol | 4 blocs | 1,00 s | once |
| colosse_carapace | coque de pierre translucide | 4 blocs | 1,00 s | loop |

## Livrable
Dix fichiers .bbmodel : trois variantes du Colosse et sept effets. Textures
embarquées. Aucun fichier externe.
```

---

## 📁 Nomenclature des fichiers

### Dépôt `ASSETS`

```
eres/colosse/
├── colosse_tenebres.bbmodel
├── colosse_aurore.bbmodel
├── colosse_fractures.bbmodel
├── vfx/
│   ├── colosse_onde_sol.bbmodel
│   ├── colosse_eclat.bbmodel
│   ├── colosse_arc.bbmodel
│   ├── colosse_coeur_charge.bbmodel
│   ├── colosse_trait.bbmodel
│   ├── colosse_faille.bbmodel
│   └── colosse_carapace.bbmodel
├── textures/           (les PNG hors fichier, pour retouche)
└── README.md           (source, licence, date de livraison)
```

### Dépôt `MCP` (destination client)

| Fichier | Destination |
|---|---|
| Colosse | `src/minecraft/assets/minecraft/textures/wizardmc/models/entity/colosse/<nom>.bbmodel` |
| Effets | `src/minecraft/assets/minecraft/textures/wizardmc/models/magic/<nom>.bbmodel` |

Le dossier `entity/<espèce>/<fichier>.bbmodel` est celui que le résolveur de
chemins des compagnons parcourt déjà ; le Colosse n'a pas besoin d'un chemin à
lui.

### Identifiants de modèle

`/boss set <vie> <dégâts> <modèle>` stocke une chaîne, qui devient le `modelId`
du NPC. Les trois valeurs attendues sont `colosse_tenebres`, `colosse_aurore`
et `colosse_fractures`.

---

## ✅ Checklist de validation

À passer sur chaque fichier avant intégration. Les identifiants sont ceux qu'on
cite en revue.

| ID | Vérification | Comment |
|---|---|---|
| **V-T01** | `meta.box_uv` est à `false` | ouvrir le `.bbmodel`, chercher `box_uv` |
| **V-T02** | Aucun élément de type `mesh` ou `locator` | chercher `"type": "mesh"` |
| **V-T03** | Toutes les textures sont embarquées en base64 | chercher `"source": "data:image/png;base64,` |
| **V-T04** | Tous les cubes visibles portent `export: true` | le piège des drapeaux inversés des exports ModelEngine |
| **V-T05** | Les 19 groupes de la hiérarchie existent, aux noms exacts | comparer à l'arbre du §Formes |
| **V-T06** | Les pivots tombent sur les articulations | plier chaque membre à 90° dans Blockbench |
| **V-T07** | Les 9 animations existent, aux noms exacts | la casse compte : `Idle`, pas `idle` |
| **V-T08** | Chaque durée est celle du tableau, à un tick près | `length` dans le fichier |
| **V-T09** | Aucune valeur de keyframe n'est une expression Molang | chercher `math.` et `query.` |
| **V-T10** | Les trois variantes ont une géométrie et des animations identiques | comparer les fichiers hors bloc `textures` |
| **V-T11** | Pose de repos en T-pose, toutes rotations à zéro | onglet *Edit*, aucune rotation non nulle |
| **V-T12** | Aucun cube hors du corps (pas de marge vide) | l'encombrement mesuré rétrécirait tout le modèle |
| **V-T13** | Les effets au sol sont modélisés dans le plan XZ | un effet à plat déclaré face au joueur apparaît sur la tranche |
| **V-T14** | Le modèle se charge sans avertissement dans le journal client | poser le fichier et regarder les logs |
| **V-T15** | Les 9 animations se jouent bout à bout sans pose cassée | Blockbench, onglet *Animate* |

---

## Ce qu'il reste à câbler côté code

Le modèle ne suffit pas : trois choses manquent pour qu'il se voie tel qu'il est
décrit ici. Elles sont notées pour ne pas les redécouvrir à la livraison.

**Le rendu NPC ne lit que de l'OBJ.** `RenderWizardNpc` (dépôt MCP) associe un
`modelId` à un chemin `.obj` statique, et **retombe sur le modèle du Forgeron**
pour tout identifiant inconnu. Un Colosse posé aujourd'hui apparaît donc comme
un forgeron de taille humaine. Il faut y brancher le chargeur `.bbmodel`, celui
qu'utilisent déjà l'Ignis et les compagnons.

**Le NPC n'a pas de canal d'état d'action.** Le `DataWatcher` de
`EntityWizardNpc` porte le modèle, le nom, l'IA et le genre — rien qui dise
« il frappe ». Les compagnons ont ce canal (`CompanionState`, dix valeurs) ;
sans l'équivalent ici, le client ne peut jouer que `Idle` et `marche`, déduites
du mouvement observé. Les six autres animations resteraient inertes.

**La boîte de collision fait 0,6 × 1,8 bloc.** C'est la taille d'un joueur,
posée dans le constructeur d'`EntityWizardNpc`. Un modèle de cinq blocs de haut
et trois et demi de large déborderait largement : on frapperait dans le vide
partout sauf au centre, et le Colosse encaisserait des coups qu'on ne voit pas
porter. Il faut une taille de boîte réglable par NPC avant de livrer un modèle
de cette échelle.

**Le nom du modèle suit le thème.** L'occurrence stocke aujourd'hui **un**
`modelId`, réglé à la main par `/boss set`. Avec trois variantes, c'est le thème
de l'Ère qui doit choisir — comme il choisit déjà le nom du Colosse.
