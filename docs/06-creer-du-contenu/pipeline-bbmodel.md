# Pipeline Blockbench

Les conventions que le moteur de rendu attend d'un fichier `.bbmodel`, et celles qui
cassent tout quand on ne les respecte pas. À lire **avant** d'ouvrir Blockbench.

**Statut : livré.** Chaque règle de ce document correspond à un comportement du moteur,
et la plupart ont été écrites après un défaut qui avait atteint le jeu.

---

## 1. Ce que le moteur attend

Un modèle de créature WizardMC est un fichier `.bbmodel` unique, qui contient la
géométrie, les textures embarquées et les animations.

| Élément | Exigence |
|---|---|
| **Géométrie** | Des cubes. Les *meshes* et les *locators* ne sont pas supportés et sont ignorés avec un avertissement. |
| **Textures** | Embarquées dans le fichier, en base64 |
| **Échelle** | 16 pixels Blockbench = 1 bloc |
| **Orientation** | Le modèle regarde vers **−Z**, comme les modèles du jeu de base |
| **Animations** | Dans le même fichier, avec des noms que le moteur reconnaît |
| **Boîte de collision** | Un cube nommé `hitbox` |

### Ce qui n'est pas supporté

| Non supporté | Conséquence |
|---|---|
| `box_uv` | Le chargement **échoue** avec une erreur explicite |
| Les *meshes* | Ignorés, avec un avertissement |
| Les *locators* | Ignorés |
| L'interpolation `bezier` | Retombe sur linéaire, avec un avertissement |

L'interpolation « smooth » de Blockbench est supportée : elle est lue comme une spline.

---

## 2. Le cube `hitbox` — la règle la plus importante

### Ce que c'est

Un cube que le modeleur dessine autour de sa créature pour caler ses proportions. Il
sert de gabarit pendant le travail, puis il est masqué.

### Ce que le moteur en fait

| | |
|---|---|
| **Il ne le dessine jamais** | Qu'il soit masqué ou non |
| **Il en retient les dimensions** | C'est la source de vérité de la boîte de collision |

Les deux moitiés de cette règle ont chacune coûté un défaut.

### Défaut n° 1 — la caisse visible

Sur treize modèles livrés, le cube est resté coché dans Blockbench, et **le joueur
voyait une caisse autour du gobelin**.

Le moteur l'écarte maintenant par son nom. La reconnaissance est **ancrée**, pas
contenue :

| Nom | Reconnu comme cube de référence ? |
|---|---|
| `hitbox` | oui |
| `hitbox2`, `hitbox 3`, `hitbox_4` | oui — le suffixe n'est qu'un numéro |
| `hitbox_bras` | **non** — c'est de la géométrie |

La distinction compte : faire disparaître un membre nommé `hitbox_bras` serait pire que
la caisse qu'on cherche à retirer.

### Défaut n° 2 — la mesure perdue

Le cube était écarté du rendu mais **jeté entièrement**. La seule mesure de référence du
fichier disparaissait, et la boîte du mob était ressaisie à la main dans le catalogue :
deux valeurs pour une même chose, qui finissaient par ne plus correspondre.

Le moteur conserve maintenant l'union de tous les cubes de référence du fichier, masqués
ou non, et l'expose sur le modèle chargé.

### Comment le dessiner

| Règle | Pourquoi |
|---|---|
| **Le masquer** avant d'exporter | Par précaution : le moteur l'écarte, mais un modèle propre est un modèle masqué |
| **Le nommer exactement `hitbox`** | Ou `hitbox2` s'il en faut plusieurs |
| **Plusieurs cubes sont permis** | Leur union fait la boîte |
| **L'aligner sur le corps, pas sur les ailes déployées** | Voir ci-dessous |

### Le piège du gabarit carré

> **Un cube aussi large que haut n'est presque jamais une boîte de collision.**

C'est le gabarit de proportions que Blockbench propose, et l'appliquer rendrait un
gobelin aussi large que grand.

| Modèle | Cube | Verdict |
|---|---|---|
| Les cinq gobelins | 1,75 × 1,75 | **gabarit** — ignoré |
| Le yak de monture | 2,19 × 2,19 | **gabarit** — ignoré |
| Le husky de monture | 1,38 × 1,88 | une vraie boîte — appliquée |
| L'axolotl de monture | 1,25 × 2,14 | appliquée |
| Le griffon de monture | 2,50 × 3,12 | appliquée |

### Le piège de l'envergure

Le cube du modeleur englobe souvent **la queue et les ailes au repos**. Pour un dragon,
ça donne une boîte de six blocs de large.

Une boîte de six blocs n'est pas absurde — l'Ender Dragon du jeu de base fait seize —
mais elle rend la créature encombrante dans un couloir.

> **Si une créature s'avère trop encombrante, c'est le cube qu'il faut resserrer autour
> du torse dans le bbmodel** — pas une constante à retoucher dans le code. Le cube est
> la source de vérité.

---

## 3. Les noms de clips

### La règle

> **Le moteur cherche des noms précis.** Un clip au bon geste mais au mauvais nom ne sera
> jamais joué.

### Ce que le moteur cherche, par canal

| Canal | Noms acceptés, dans l'ordre de recherche |
|---|---|
| **Repos** | `Idle`, `idle`, `attente`, `waiting`, `vol_stationnaire` |
| **Marche** | `marche`, `walk`, `walking`, `Walk`, `Marche` |
| **Course** | `sprint`, `run`, `course` |
| **Douleur** | `hurt`, `hurt_1`, `damage`, `hit`, `hurt1`, `hit_shell` |
| **Mort** | `death`, `dying`, `mort`, `die` |
| **Mort noyée** | `water_death`, `death_water` |
| **Mort au sol** | `land_death`, `death_land` |
| **Nage** | `walk_swim`, `swim`, `nage` |
| **Flottaison** | `idle_swim`, `float`, `flottaison` |
| **Vol stationnaire** | `idle_fly`, `vol_stationnaire`, `hover` |
| **Vol** | `walk_fly`, `glide`, `fly`, `vol` |
| **Passe rasante** | `fly_low`, `dive`, `swoop`, `rase` |

La casse est ignorée. La liste complète et à jour est dans
[Catalogue des clips d'animation](../07-reference/catalogue-clips-animation.md).

### Les clips d'attaque

Ils ne suivent **aucune convention** : c'est `mobs.yml` qui nomme le clip de chaque
attaque, et le nom doit correspondre exactement à celui du bbmodel.

> Un clip mal orthographié dans `mobs.yml` donne une **attaque sans geste** : les dégâts
> tombent, le joueur ne voit rien venir, et l'esquive devient impossible à apprendre.

C'est l'erreur la plus coûteuse du projet, et un contrôle automatisé vérifie chaque nom
contre les fichiers livrés.

### Le minimum

| Clip | Obligatoire ? |
|---|---|
| Repos | **oui** |
| Déplacement | non, mais une créature qui se déplace sans clip joue son repos |
| Au moins une attaque | oui, pour une créature hostile |
| Mort | non, mais 105 modèles en ont un |

### Les leçons de cette liste

**Trois cas réels** qui expliquent pourquoi elle est si longue :

| Cas | Ce qui s'est passé |
|---|---|
| Les quatre ours de monture | Leur modeleur a nommé tous les clips au participe présent : `waiting`, `dying`. Le moteur les accepte ; c'est un contrôle qui en avait une copie périmée et les déclarait cassés. |
| Le crocodile | Il porte `land_death` et `water_death`, dont **aucun** ne s'appelle `death`. Sa mort n'était jamais jouée. |
| 105 modèles | Ils portaient un clip de mort que rien ne déclenchait |

---

## 4. Les faces sans texture

### Le défaut

Soixante et un modèles s'affichaient en **cube magenta**. La cause n'était pas le cube
de référence : c'était des faces dont la texture vaut `null`.

| Ce qu'on croyait | Ce que c'était |
|---|---|
| Un cube mal nommé | Des faces individuelles sans texture |

Seuls six des soixante et un modèles étaient pris par le nom du cube. La bonne règle est
**par face** : une face qui ne nomme aucune texture ne se dessine pas.

### Ce que ça veut dire pour un modeleur

| Règle | Pourquoi |
|---|---|
| **Texturer toutes les faces visibles** | Une face sans texture est simplement invisible |
| Une face invisible n'est pas une erreur | C'est le comportement voulu, mais ce n'est probablement pas ce que vous vouliez |

Quarante-neuf modèles livrés en portent encore. Elles sont correctement écartées, et le
modèle reste utilisable — mais si une partie d'une créature manque à l'écran, c'est la
première chose à vérifier.

---

## 5. Le fondu entre clips

Le moteur **fond** les clips l'un dans l'autre sur un huitième de seconde. Ce n'est pas
un réglage du modèle, mais il change la façon de l'animer.

### Ce que ça apporte

Sans fondu, changer de clip réécrivait la pose d'un seul coup : le membre qui était levé
se retrouvait baissé à l'image suivante. **C'est ce saut, à chaque passage
repos/marche/attaque, qui donnait l'impression que les créatures saccadaient** — pas les
clips eux-mêmes, qui sont continus.

### Ce que le modeleur doit en savoir

| Conséquence | Ce qu'elle implique |
|---|---|
| Les poses de début et de fin d'un clip comptent moins | Le moteur comble l'écart |
| Un clip d'attaque ne doit pas commencer par un temps mort | Le fondu ajoute déjà un huitième de seconde |
| Un bone animé par un seul des deux clips revient au repos | Il n'y reste pas collé |

Le fondu dure un huitième de seconde, et c'est volontairement court : au-delà, un coup
porté paraît mou parce que le geste démarre en retard.

---

## 6. La cadence des clips de déplacement

Le moteur joue un clip de déplacement **à la vitesse réelle de la créature**, entre
0,65 et 1,60 fois sa cadence d'auteur.

### Ce que le modeleur doit en savoir

> **Animez le clip de marche à une allure moyenne.** Le moteur l'accélérera et le
> ralentira autour de cette allure.

La vitesse de référence est d'environ 0,16 bloc par tick. Un clip animé comme une course
sera joué au ralenti quand la créature marche, et paraîtra traîner.

### Pourquoi cette règle existe

Avant, tout clip de locomotion tournait à vitesse fixe quelle que soit l'allure : les
pattes battaient le même rythme à l'arrêt qu'en pleine course, et le décalage entre le
pas et le sol donnait un **patinage** caractéristique.

---

## 7. Les créatures qui bondissent

### Le clip de bond **est** le clip de marche

Pour une créature en démarche `HOP` — les gelées — le clip `walk` est le saut complet :
détente, vol, écrasement à la réception.

### Les deux règles

| Règle | Pourquoi |
|---|---|
| **Le clip doit faire un saut entier**, et un seul | Le moteur le relance à chaque détente |
| **Sa durée doit égaler la période de bond** déclarée dans `mobs.yml` | Le clip joue alors exactement une fois par saut |

| Modèle | Durée du clip `walk` | `hopTicks` |
|---|---:|---:|
| Six gelées | 1,75 s | 35 |
| Gelée ailée | 1,50 s | 30 |
| Gelée tricéphale | 1,46 s | 29 |

Un contrôle automatisé confronte chaque période à la durée du clip lue dans le bbmodel.
**Si vous retouchez la durée du clip, la période doit suivre** — sinon la gelée retombe
au milieu de son écrasement.

### Le cas du Roi des gelées

Il marche, parce que son modèle n'a **aucun** clip de déplacement : son `dash` est
déclaré comme une attaque. Le faire bondir donnerait un cube qui saute en restant figé.

---

## 8. Le pivot et l'échelle

### Le chargeur ne recentre rien

> Le chargeur bbmodel **ne recentre pas** la géométrie sur l'origine du bone racine.

Les origines de bones ne servent que de pivots de rotation. C'est au rendu de ramener le
centre horizontal du modèle sur l'origine de l'entité et de poser sa base au sol.

Conséquence : un modèle dont la géométrie est décalée dans l'espace Blockbench sera
décalé en jeu, sauf si le pivot déclaré côté client compense exactement.

### Ce que ça veut dire

| Règle | Pourquoi |
|---|---|
| **Poser le modèle au sol**, base à Y = 0 | Sinon il flotte ou s'enfonce |
| **Le centrer horizontalement** | Sinon il faut un pivot corrigé à la main |

Une erreur d'un demi-bloc sur le centre en profondeur a déjà décalé un compagnon vers
l'arrière, et la correction a demandé de mesurer les cubes visibles du fichier un par un.

---

## 9. La liste avant d'exporter

| # | À vérifier |
|---|---|
| 1 | **Le cube `hitbox` existe**, nommé exactement, et il est masqué |
| 2 | Il entoure la créature au repos, pas les ailes déployées |
| 3 | Il n'est pas un carré parfait, sauf si c'est vraiment la boîte voulue |
| 4 | **Toutes les faces visibles sont texturées** |
| 5 | Les textures sont **embarquées** dans le fichier |
| 6 | Il existe un clip de repos, nommé parmi ceux que le moteur cherche |
| 7 | Le clip de déplacement est animé à allure moyenne |
| 8 | Si la créature bondit, le clip de marche **est** le saut, et un seul |
| 9 | Il existe un clip de mort, nommé `death` ou équivalent |
| 10 | Chaque clip d'attaque a un nom qu'on pourra recopier dans `mobs.yml` |
| 11 | Le modèle regarde vers −Z |
| 12 | La base est à Y = 0, le modèle est centré horizontalement |
| 13 | Aucun *mesh*, aucun *locator* |
| 14 | Pas de `box_uv` |

---

## 10. Après l'export

1. **Déposer le fichier** dans l'arborescence des modèles du client, sous le chemin qui
   servira de `model` dans le catalogue.
2. **Relever les mesures** : le cube `hitbox`, la longueur visible, la durée des clips.
3. **Écrire l'entrée du catalogue** avec ces mesures, pas avec des valeurs estimées.
4. **Lancer le générateur d'entités**, si c'est une créature.
5. **Lancer les contrôles.** Ils confronteront vos valeurs au fichier.
6. **Regarder en jeu.** Aucun contrôle ne remplace le fait de voir bouger.

Voir [Créer une créature](creer-une-creature.md) pour la chaîne complète.

---

## 11. Les erreurs les plus fréquentes

| Erreur | Symptôme en jeu |
|---|---|
| Cube `hitbox` laissé visible | Une caisse autour de la créature |
| Pas de cube `hitbox` | Il faut saisir la boîte à la main, et elle divergera |
| Cube carré appliqué tel quel | Une créature aussi large que haute |
| Faces non texturées | Des morceaux manquants, ou un cube magenta sur un client ancien |
| Clip de repos mal nommé | La créature reste figée dans sa pose de montage |
| Clip d'attaque mal nommé dans `mobs.yml` | **Une attaque sans geste** : inesquivable |
| Clip de marche animé comme une course | Un déplacement qui traîne |
| Clip de bond qui contient deux sauts | Une gelée désynchronisée |
| `hopTicks` qui ne suit pas la durée du clip | La gelée retombe au milieu de son écrasement |
| Modèle non posé au sol | Il flotte ou s'enfonce |
| Modèle tourné vers +Z | Il avance à reculons |

---

## À lire ensuite

- [Créer une créature](creer-une-creature.md) — la chaîne complète
- [Catalogue des clips d'animation](../07-reference/catalogue-clips-animation.md) — tous les noms reconnus
- [WizardMobs](../05-operer/configuration/wizardmobs.md) — les champs du catalogue
- [Conventions et nommage](conventions-et-nommage.md) — les chemins et les identifiants
