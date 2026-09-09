# Créer une créature

La chaîne complète, du modèle 3D au catalogue. Compte une demi-journée la première fois,
une heure ensuite.

**Statut : livré.** La chaîne est outillée : un générateur crée les cent cinquante
fichiers qu'une espèce demande dans les trois dépôts.

---

## 1. Avant de commencer

### Les questions auxquelles il faut répondre

| Question | Pourquoi elle compte |
|---|---|
| **Quel degré d'altération ?** | Rien, imprégnation, déformation. Voir le [bestiaire](../02-univers/bestiaire.md#1-ce-que-la-saignée-a-fait-aux-bêtes). Si on ne sait pas dire ce que la saignée lui a fait, elle n'a pas sa place. |
| **Quelle famille ?** | Une espèce seule de son genre a besoin d'une raison de l'être, comme le Golem de mousse |
| **Qu'est-ce qu'elle apprend au joueur ?** | Chaque famille existante enseigne quelque chose. Une famille qui n'enseigne rien répète. |
| **Apparaît-elle d'elle-même ?** | Un poids nul en fait un contenu de donjon ou d'événement. Pour un boss, c'est presque toujours le bon choix. |
| **Hostile ou paisible ?** | Deux fichiers différents, deux générateurs différents |

### Ce qu'il faut avoir lu

- [Pipeline Blockbench](pipeline-bbmodel.md) — **obligatoire**
- [WizardMobs](../05-operer/configuration/wizardmobs.md) — les champs du catalogue
- [Plages d'identifiants](../07-reference/plages-d-identifiants.md) — ce qui est pris

---

## 2. Étape 1 — Le modèle

Voir [Pipeline Blockbench](pipeline-bbmodel.md) en entier. Le minimum :

| À produire | Exigence |
|---|---|
| Un fichier `.bbmodel` | Textures embarquées, cubes uniquement |
| Un cube `hitbox` | Masqué, autour du corps au repos |
| Un clip de repos | Nommé parmi ceux que le moteur cherche |
| Un clip de déplacement | Animé à allure moyenne |
| Un clip par attaque | Les noms iront dans le catalogue |
| Un clip de mort | `death` ou équivalent |

### Déposer le fichier

Dans l'arborescence des modèles du client, sous un chemin qui respecte les
[conventions de nommage](conventions-et-nommage.md). Ce chemin deviendra le champ
`model` du catalogue.

### Relever les mesures

Avant de quitter Blockbench, noter :

| Mesure | Pour quoi |
|---|---|
| Les dimensions du cube `hitbox`, en blocs | La boîte de collision |
| La longueur visible du modèle | Pour une créature à redimensionner |
| La durée de chaque clip | Indispensable pour une créature qui bondit |
| Le nom exact de chaque clip | À recopier dans le catalogue |

> **Ne pas estimer ces valeurs.** Un contrôle automatisé les confrontera au fichier, et
> une valeur saisie à la main dérive dès que le modeleur retouche le modèle.

---

## 3. Étape 2 — L'entrée du catalogue

Dans `mobs.yml` pour une créature hostile, `beasts.yml` pour une bête paisible.

### Le minimum pour une créature hostile

| Champ | Ce qu'on y met |
|---|---|
| `name` | Le nom affiché, avec un code de couleur cohérent avec sa famille |
| `model` | Le chemin du bbmodel, sans extension |
| `temperament` | Comment elle se bat |
| `health`, `damage`, `speed`, `perception` | Ses caractéristiques |
| `width`, `height` | **Les dimensions relevées sur le cube `hitbox`** |
| `experience` | Ce qu'elle lâche |
| `attacks` | Au moins une |

### Choisir le tempérament

C'est la décision la plus importante du lot : le tempérament décide de **comment** elle
se bat, et donc de ce que le joueur apprend en la combattant.

| Si la créature doit… | Tempérament |
|---|---|
| Avancer droit et encaisser | `BRUTE` |
| Harceler et revenir | `SKIRMISHER` |
| Tirer de loin | `RANGED` |
| Lancer et se replier | `CASTER` |
| Surprendre | `AMBUSHER` |
| Suivre avant d'engager | `STALKER` |
| Ne valoir qu'en nombre | `PACK_HUNTER` |
| Garder un point sans poursuivre | `SENTINEL` |
| Ne riposter que si on insiste | `DEFENSIVE` |

`SENTINEL` est celui qu'on oublie, et c'est dommage : c'est le seul tempérament qu'un
joueur peut **contourner**. Tout ce qui poursuit apprend à courir ; un sentinelle apprend
à choisir.

### La boîte de collision

Les dimensions viennent du cube `hitbox`, **sauf si c'est un gabarit carré**.

| Cas | Quoi faire |
|---|---|
| Le cube n'est pas carré | Recopier ses dimensions |
| Le cube est un carré parfait | C'est un gabarit : saisir une boîte plausible à la main |

Voir [le piège du gabarit carré](pipeline-bbmodel.md#le-piège-du-gabarit-carré).

### Les attaques

Chaque attaque demande :

| Champ | À savoir |
|---|---|
| `clip` | **Le nom exact du clip du bbmodel.** Un nom faux donne une attaque sans geste. |
| `kind` | `MELEE`, `AREA`, `LUNGE`, `DEFENSIVE`, `RANGED`, `SUMMON`, `BUFF`, `ARROW` |
| `windup` | Ticks avant que les dégâts tombent. **C'est le télégraphe.** |
| `duration` | Ticks de durée totale |
| `cooldown` | Ticks de recharge |
| `maxRange` | **De surface à surface**, pas de centre à centre |
| `damage` | — |

### Calibrer un `windup`

C'est la fenêtre d'esquive, et c'est ce qui rend la créature lisible.

| Référence livrée | `windup` |
|---|---|
| Un coup rapide de gobelin | 10 ticks |
| Un coup lourd de brute | 18 ticks |
| Une attaque de zone de dragon | 32 ticks |

> **Un `windup` très court sur une attaque puissante est un défaut de conception**, pas
> une difficulté : le joueur n'a pas le temps de réagir, donc il ne peut pas apprendre.

### Calibrer un `maxRange`

Raisonner sur la distance **entre les deux boîtes**, pas entre les centres.

| Référence livrée | `maxRange` |
|---|---|
| Un corps à corps de gobelin | 2,4 à 2,9 |
| Une charge | 6,4 |
| Une attaque de zone de boss | 5,5 à 8,7 |

### Le butin

| Champ | Ce qu'on y met |
|---|---|
| `perLevelBonus` | Ce que chaque niveau ajoute à la chance |
| `drops` | La liste, chaque entrée avec `item`, `min`, `max`, `chance` |

Donner un butin qui **sert à quelque chose** : la Gelée de lave lâche un noyau qui est
l'objet d'une quête annexe, et c'est ce qui fait qu'on va la chercher.

### L'apparition

| Champ | À savoir |
|---|---|
| `weight` | **`0` pour un boss.** Un boss croisé au détour d'une plaine n'en serait plus un. |
| `biomes`, `sky`, `minLight`, `maxLight`, `minY`, `maxY` | Les conditions |
| `packMin`, `packMax` | La taille du groupe |
| `minSchoolLevel` | Le niveau d'école minimal d'un joueur proche |

**Ne pas déclarer de régions.** Aucune espèce n'est réservée à une région : ce qui change
d'une région à l'autre, c'est le niveau. Voir
[Géographie](../02-univers/geographie.md#1-la-règle-qui-gouverne-tout).

### Écrire la raison

Pour toute valeur qui n'est pas évidente, un commentaire. Les fichiers livrés le font :

> « Le coup lourd : il ne vaut son temps de recharge que contre plusieurs. »

Trois mots qui disent pourquoi la recharge est longue, et qui empêcheront quelqu'un de la
raccourcir dans six mois.

---

## 4. Étape 3 — Le générateur

Une espèce demande environ **cent cinquante fichiers** dans trois dépôts : un
identifiant d'entité, une classe serveur, une classe client, un œuf d'apparition, un nom
traduit.

Les écrire à la main serait long et surtout **désynchronisé dès la première espèce
ajoutée**. Un générateur s'en charge.

| Ce que le générateur fait | |
|---|---|
| Attribue l'identifiant d'entité | Le suivant disponible |
| Crée la classe serveur et la classe client | Le cerveau reste partagé ; les sous-classes ne portent que ce qui distingue |
| Crée l'œuf d'apparition | **Ses couleurs sont tirées de la texture du modèle**, ce qui donne un œuf reconnaissable sans que personne ait à l'assortir |
| Crée le nom traduit | — |

Il se lance depuis le dépôt WizardMobs, et il a un mode de vérification qui dit ce qui
manquerait sans rien écrire.

> **Ajouter une créature se résume à une entrée dans le catalogue et une exécution du
> générateur.** Si vous écrivez une classe à la main, vous faites du travail que le
> générateur refera différemment.

---

## 5. Étape 4 — Les contrôles

Lancer les contrôles des trois dépôts. Ce qu'ils vérifient :

| Contrôle | Ce qu'il attrape |
|---|---|
| Les noms de clips | Un `clip` qui n'existe pas dans le bbmodel |
| Les boîtes de collision | Une boîte qui ne correspond pas au cube `hitbox` |
| Les périodes de bond | Un `hopTicks` désaligné de la durée du clip |
| Le catalogue | Une espèce déclarée mais non chargée |
| Les modèles | Un modèle introuvable, un clip de repos manquant |

Chacun a été ajouté après un défaut réel qui avait atteint le jeu. **Un contrôle qui
échoue dit quelque chose de vrai.**

---

## 6. Étape 5 — L'essai en jeu

Aucun contrôle ne remplace le fait de regarder.

| À vérifier | Comment |
|---|---|
| Elle a un modèle 3D | La faire apparaître |
| Aucun cube magenta, aucune partie manquante | La regarder sous tous les angles |
| Son repos ne la laisse pas figée dans sa pose de montage | L'observer immobile |
| Son déplacement ne patine pas | La suivre |
| **Chaque attaque montre un geste avant les dégâts** | Se laisser frapper |
| Sa portée correspond à sa boîte | S'en approcher lentement |
| Elle ne flotte ni ne s'enfonce | La regarder de côté |
| Elle ne se coince pas | La faire passer dans un couloir |
| Sa mort joue un clip | La tuer |
| Son butin tombe | — |

La commande est `/mobs spawn <espèce>`, permission `core.mobs.list`.

### Le point à ne pas manquer

> **Se laisser frapper par chaque attaque**, une par une, et vérifier qu'un geste
> commence avant que les dégâts tombent.

C'est la seule vérification qui ne peut pas être automatisée, et c'est celle qui protège
la règle la plus importante du combat.

---

## 7. Étape 6 — Documenter

| Où | Quoi |
|---|---|
| [Catalogue des créatures](../07-reference/catalogue-creatures.md) | Une ligne avec les valeurs |
| [Bestiaire](../02-univers/bestiaire.md) | Un mot dans sa famille, si c'en est une nouvelle |
| [Plages d'identifiants](../07-reference/plages-d-identifiants.md) | L'identifiant d'entité attribué |

---

## 8. Le cas particulier d'une bête paisible

Même chaîne, avec trois différences :

| Différence | |
|---|---|
| Le fichier | `beasts.yml` |
| Le générateur | Celui des bêtes |
| La disposition remplace le tempérament | `CALM`, `SKITTISH` ou `DEFENSIVE` |

Une bête peut déclarer **plusieurs apparences**, tirées au sort. Elle peut aussi être
montable — auquel cas elle peut servir de base à une monture, qui aura une apparence
**fixe**. Voir [Créer une monture](creer-une-monture.md).

---

## 9. Le cas particulier d'un boss

| Règle | Pourquoi |
|---|---|
| `weight: 0` | Un boss croisé par hasard n'en est plus un |
| `elite: true` | Son nom s'affiche en permanence : le joueur sait ce qu'il affronte |
| `minSchoolLevel` élevé | De 30 à 45 pour les boss naturels |
| Un `levelOffset` franc | Il naît plus haut que son environnement |
| Plusieurs attaques, avec des `windup` longs | Un boss doit être lisible, pas rapide |

### Si le boss vole

Trois règles qui ne se devinent pas :

1. **Déclarer un `landRange`.** Il se posera pour frapper. Un dragon qui resterait en
   l'air serait invincible pour un joueur au sol.
2. **Le modèle doit porter des clips de vol.** Le Drake Terravore reste au sol
   précisément parce que son modèle n'en a aucun — le faire décoller l'aurait fait
   glisser dans les airs en marchant.
3. **Déclarer la mort en trois temps** : la chute, l'impact, et la durée de l'impact. Le
   butin tombera au point d'impact, sans quoi tuer le dragon en altitude donnerait un
   butin inatteignable.

---

## 10. La liste complète

| # | Étape |
|---|---|
| 1 | Répondre aux cinq questions du paragraphe 1 |
| 2 | Produire le modèle, selon le [pipeline](pipeline-bbmodel.md) |
| 3 | Relever les mesures : cube `hitbox`, longueur, durées de clips, noms de clips |
| 4 | Écrire l'entrée du catalogue, avec la raison des valeurs non évidentes |
| 5 | Lancer le générateur d'entités |
| 6 | Lancer les contrôles des trois dépôts |
| 7 | Essayer en jeu, **en se laissant frapper par chaque attaque** |
| 8 | Documenter dans les catalogues de référence |
| 9 | Une branche, un commit par idée, une *pull request* |

---

## À lire ensuite

- [Pipeline Blockbench](pipeline-bbmodel.md) — les conventions du modèle
- [WizardMobs](../05-operer/configuration/wizardmobs.md) — chaque champ en détail
- [Catalogue des créatures](../07-reference/catalogue-creatures.md) — les 49 espèces livrées
- [cdc_mobs](../90-specifications/cdc_mobs.md) — la spécification
