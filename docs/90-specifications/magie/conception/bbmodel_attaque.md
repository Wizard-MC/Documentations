# Concevoir le bbmodel d'une attaque

De Blockbench au jeu : ce que le client sait lire, ce qu'il refuse, et
comment vérifier un modèle avant de le câbler sur un sort.

Voir aussi : [`vfx_attaques.md`](vfx_attaques.md) (déclarer les étapes d'un
sort), [`../README.md`](../README.md) (index de la documentation magie).

---

## 1. Ce que le client lit

Le chargeur du client (`BBModelParser` / `BBModelLoader`, dépôt MCP) est un
lecteur `.bbmodel` écrit pour ce projet. Il ne dépend ni de ModelEngine ni
d'un plugin serveur : le fichier est chargé côté client, transformé en
maillage et animé localement.

Il lit :

| Donnée | Détail |
|---|---|
| Géométrie | éléments de type `cube` uniquement |
| UV | **par face** (`faces.<face>.uv`), rotation d'UV comprise |
| Textures | PNG **embarqué en base64** dans le fichier |
| Hiérarchie | `outliner` — groupes imbriqués, pivots, rotations de repos |
| Animations | canaux `position`, `rotation`, `scale` par bone |
| Interpolation | `linear`, `step`, `catmullrom` / `smooth` |
| Boucle | `once`, `loop`, `hold` |

Il **ignore silencieusement** (avec un avertissement dans le journal) :

- les éléments `mesh` et `locator` — le maillage libre de Blockbench n'est pas
  supporté, il faut des cubes ;
- les animateurs d'effet (sons, particules, instructions Molang posées sur la
  timeline) — le son est joué par le runtime, pas par le modèle ;
- les expressions Molang dans les valeurs de keyframe — seule une constante
  numérique est lue, une expression vaut `0`.

Il **refuse** le fichier, sans le charger :

| Cause | Message |
|---|---|
| `meta.box_uv` à `true` | *box_uv non supporté (utiliser Per-face UV dans Blockbench)* |
| Aucun cube visible | *BBModel sans géométrie* |
| Plus de 4096 cubes | *Trop d'elements* |
| Plus de 32 textures | *Trop de textures* |
| Plus de 20 000 keyframes | *Trop de keyframes* |
| Hiérarchie de plus de 64 niveaux | *Hiérarchie de bones trop profonde* |
| Texture de plus de 4096 px | *Texture trop grande* |

Un modèle refusé ne fait pas planter le jeu : le sort se joue sans son étape
modèle, le reste (runes, particules, son) est inchangé.

---

## 2. Réglages Blockbench

**Format du projet.** *Free Model* ou *Generic Model*. Le format *Java Block /
Item* impose le box UV, que le chargeur refuse.

**UV.** Passer le projet en *Per-face UV* (menu `File ▸ Project ▸ UV Mode`).
C'est le seul point de configuration qui peut faire rejeter un fichier
autrement correct.

**Unités.** 16 unités Blockbench = 1 bloc. Un sceau de 5 blocs de large mesure
80 unités dans l'éditeur.

**Textures.** Elles doivent être *embarquées*, pas référencées par chemin. Dans
Blockbench : clic droit sur la texture ▸ *Save as* n'est pas ce qu'il faut ;
il faut que le `.bbmodel` contienne la texture en base64, ce qui est le cas par
défaut quand la texture a été importée dans le projet plutôt que liée à un
fichier externe. Une texture non embarquée donne un modèle au damier violet et
noir — visible, mais faux.

**Origine du projet.** Le runtime mesure lui-même le modèle et le repose sur sa
base (voir §4) ; il n'y a donc pas de contrainte forte sur la position absolue.
Modéliser malgré tout autour de `X=0, Z=0` évite les surprises sur les modèles
asymétriques.

---

## 3. Géométrie d'un VFX

Un VFX d'attaque n'est pas un modèle d'objet. Les règles ne sont pas les mêmes.

**Des plans, pas des volumes.** L'essentiel d'un effet est fait de cubes
d'épaisseur nulle texturés en alpha additif : un plan pour une onde, quatre
plans croisés pour une explosion. Un cube plein coûte six faces pour un rendu
souvent moins convaincant.

**Orientation du plan.** Elle détermine l'orientation à déclarer côté sort :

| Modélisé dans | Lecture | Orientation à déclarer |
|---|---|---|
| plan XZ (à plat) | sceau vu de dessus | `WORLD` ou `ENTITY_YAW` |
| plan XY (debout) | sceau dressé face au joueur | `FACE_CAST` |

Un sceau modélisé à plat puis déclaré `FACE_CAST` apparaît sur la tranche : le
joueur voit une ligne. C'est l'erreur la plus fréquente et elle ne produit
aucun message d'erreur.

**Budget.** Viser quelques dizaines de cubes. Les limites du chargeur (4096
cubes) sont des garde-fous anti-fichier hostile, pas une cible. Un effet lourd
se paie à chaque instance simultanée, et le budget du runtime est de 96 effets
en vol.

**Ce qui ne sert pas.** Cubes repères, hitbox, plans de construction : les
marquer `export: false` **ou** invisibles (œil fermé). Le chargeur saute les
deux. Attention au piège du §6.

---

## 4. Taille : ne pas la fixer dans le modèle

Le sort ne déclare **jamais** une échelle. Il déclare une taille voulue en
blocs, et le runtime :

1. mesure l'encombrement horizontal réel du maillage — ou sa hauteur, si le
   modèle est un plan strictement vertical ;
2. en déduit l'échelle qui atteint la taille demandée ;
3. recentre le modèle horizontalement sur son ancre ;
4. le pose par sa base.

Conséquences pour le modeleur :

- **la taille absolue dans l'éditeur n'a pas d'importance** — seules comptent
  les proportions ;
- **la base du modèle est son point de contact.** Un sceau qui doit être posé
  au sol se modélise avec son plan à `Y = 0`. Un halo destiné à flotter à
  hauteur de poitrine se modélise lui aussi base à zéro : c'est l'ancre du sort
  (`WAND`, `CASTER_EYES`) qui le porte en hauteur, pas la géométrie ;
- **pas de marge vide.** Un cube transparent oublié loin du modèle élargit
  l'encombrement mesuré et rétrécit tout le reste.

---

## 5. Animations

**Une animation par état, nommée pour ce qu'elle fait.** Le nom est ce que le
sort déclare : `spawn`, `loop`, `despawn`, `ice_spike`, `crystal_the_enemy`.
Il est recopié tel quel dans le catalogue, et le test d'intégrité vérifie qu'il
existe bien dans le fichier.

**La durée est contractuelle.** La longueur de l'animation (`length`, en
secondes) est recopiée dans le catalogue du sort et confrontée au fichier par
`VfxModelCatalogTest`, à un tick près. Rallonger une animation sans mettre à
jour la déclaration fait échouer le test — c'est voulu : la durée pilote
l'enchaînement des étapes.

**Modes de boucle.**

| Mode Blockbench | Comportement | Usage |
|---|---|---|
| `once` | joue puis disparaît | impact, libération, projectile |
| `loop` | reboucle | cercle d'incantation, entrave, aura |
| `hold` | joue et fige sur la dernière pose | trace persistante |

Une étape déclarée en boucle n'est jouée que si le serveur annonce une durée
d'au moins 20 ticks (voir [`vfx_attaques.md`](vfx_attaques.md) §2). Une
animation `loop` de 0,15 s est parfaitement valable : elle est rejouée jusqu'à
la fin de la durée annoncée.

**Canaux.** `position`, `rotation`, `scale`. Rien d'autre n'est lu. Un effet
qui doit apparaître et disparaître le fait par l'échelle ou par un déplacement
hors champ, pas par une opacité animée — le canal n'existe pas.

**Interpolation.** `linear` par défaut, `step` pour un saut franc (utile pour
un flipbook : une pose par frame), `catmullrom` pour un mouvement souple. Tout
autre mode retombe sur `linear` avec un avertissement.

**Pas de Molang.** Une valeur comme `math.sin(query.anim_time * 20)` est lue
comme `0`. Les mouvements procéduraux se font par keyframes.

**Bones.** Une animation ne peut bouger qu'un groupe de l'`outliner`, jamais un
cube isolé. Tout ce qui doit bouger indépendamment est son propre groupe. Un
modèle sans aucun groupe nommé et sans animation est chargé en modèle statique
— plus rapide, mais figé.

---

## 6. Le piège des drapeaux inversés

Une partie du lot `ASSETS` vient d'exports ModelEngine où la convention est
inversée : la géométrie **visible** porte `export: false` et un cube repère de
1×1 porte `export: true`. Chargé tel quel, le modèle se charge « avec succès »
et n'affiche rien — ou affiche un cube blanc minuscule.

`tools/import_vfx_models.py` détecte la signature (la surface exclue dépasse
largement la surface exportée), rétablit les drapeaux sur les cubes **et** sur
les groupes de l'`outliner`, et le journalise dans sa colonne `flip`.

Pour un modèle fabriqué à la main, la règle reste simple : **exportable et
visible = dessiné**. Tout le reste est ignoré.

---

## 7. Boucle de validation

### a. Auditer le fichier

```bash
java ... fr.wizardmc.magic.vfx.test.VfxAssetAudit /chemin/vers/ASSETS
```

L'outil charge chaque `.bbmodel` avec le chargeur du jeu et affiche, fichier
par fichier : cubes retenus, bones, vertices, animations avec durée et mode de
boucle. Deux lectures décisives :

- `vertices=0` → le modèle se chargerait sans rien afficher (drapeaux inversés,
  ou géométrie entièrement en `mesh`) ;
- une animation absente de la liste → son nom est mal orthographié dans la
  déclaration du sort.

### b. Importer

Ajouter une ligne au manifeste de `MCP/tools/import_vfx_models.py` :

```python
("frost_prison_cage.bbmodel",
 "attacks_animated/mage/cryo_prison/cryo_prison_cage.bbmodel",
 ["crystal_the_enemy"], "Cage de glace maintenue autour de la cible"),
```

puis lancer le script. Il copie le fichier, ne garde que les animations
déclarées, corrige les drapeaux si besoin, et **échoue bruyamment** si une
animation nommée n'existe pas dans la source. Un `--dry-run` montre le résultat
sans rien écrire.

Garder un import scripté plutôt qu'une copie manuelle a une raison précise :
un ré-import doit produire exactement le même fichier, sans qu'il faille se
souvenir des retouches faites six mois plus tôt.

### c. Déclarer et tester

La déclaration du clip dans `SpellVfxModels` puis la vérification par
`VfxModelCatalogTest` et `/magicvfx test` sont décrites dans
[`vfx_attaques.md`](vfx_attaques.md) §5.

---

## 8. Aide-mémoire

Avant de proposer un modèle :

- [ ] format *Free* ou *Generic*, UV **par face** ;
- [ ] uniquement des éléments `cube` — aucun `mesh` ;
- [ ] texture embarquée en base64, au plus 4096 px ;
- [ ] géométrie utile `export: true` et visible ; repères exclus ;
- [ ] base du modèle à `Y = 0`, centré horizontalement, sans cube fantôme ;
- [ ] orientation du plan cohérente avec l'ancrage prévu ;
- [ ] une animation par état, nommée explicitement, durée relevée ;
- [ ] mode de boucle cohérent (ponctuel `once`, entretenu `loop`) ;
- [ ] aucune valeur Molang dans les keyframes ;
- [ ] `VfxAssetAudit` affiche des vertices et les bonnes animations.
