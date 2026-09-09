# Conventions et nommage

Comment nommer un identifiant, un fichier, un modèle ; quelle couleur employer ; comment
écrire un texte destiné au joueur. Référence courte, à consulter en écrivant.

---

## 1. Les identifiants

### La forme

| Règle | Exemple |
|---|---|
| En minuscules | `slime_lava` |
| Mots séparés par un souligné | `goblin_ranger` |
| Sans accent, sans espace, sans tiret | `the_soulrot`, pas `le-pourrisseur-d'âmes` |
| En **anglais** | `shadow_spider`, pas `araignee_ombre` |
| Descriptif, pas numéroté | `slime_healer`, pas `slime_07` |

### Pourquoi l'anglais

Les identifiants sont des **clés**, pas des mots. Ils apparaissent dans des noms de
classes générés, des chemins de fichiers, des tables de base de données. Un accent ou une
apostrophe y cause des problèmes qui ne se voient qu'au déploiement.

Le nom **affiché** est en français, et c'est lui que le joueur lit.

### La convention famille-variante

Quand plusieurs entrées forment une famille, la famille vient en premier.

| Bon | Mauvais |
|---|---|
| `slime_common`, `slime_frost`, `slime_lava` | `common_slime`, `frost_slime` |
| `goblin_melee`, `goblin_brute`, `goblin_mage` | `melee_goblin`, `brute_goblin` |
| `oblivion_orb_purple`, `oblivion_orb_yellow` | `purple_orb`, `yellow_orb` |

Le tri alphabétique regroupe alors la famille, ce qui rend les fichiers lisibles.

### Ce qui ne se renomme jamais

> **Un identifiant déjà en jeu ne se renomme pas.**

| Identifiant | Ce qui casse si on le renomme |
|---|---|
| Une espèce | La classe générée, l'œuf d'apparition, les objectifs de quête qui la visent |
| Une quête | La progression des joueurs devient orpheline |
| Une monture | Le déblocage accordé par une quête |
| Un sort | Les grimoires des joueurs |

Pour changer un nom **affiché**, on change le champ `name`. L'identifiant, non.

---

## 2. Les noms affichés

### La forme

| Règle | Exemple |
|---|---|
| En français | « Gelée de lave » |
| Précédé d'un code de couleur | `&cGelée de lave` |
| Majuscule au premier mot seulement | « Brute gobeline », pas « Brute Gobeline » |
| Sauf un nom propre | « Le Pourrisseur d'âmes », « Le Nyx » |

### L'article pour les boss

Les boss portent un article défini : « **Le** Roi des gelées », « **Le** Cryptique »,
« **La** Ciguë », « **Le** Nyx ».

C'est un signal discret : un nom avec article est unique, donc c'est un boss.

---

## 3. Les couleurs

### La palette

| Code | Couleur | Usage |
|---|---|---|
| `&2` | vert foncé | Gobelins, créatures végétales |
| `&a` | vert clair | Gelées communes, quêtes annexes |
| `&c` | rouge | Feu, lave, danger |
| `&6` | orange | Or, boss, quêtes de trame |
| `&e` | jaune | Lumière pâle |
| `&b` | cyan | Givre, air |
| `&3` | cyan foncé | Eau |
| `&d` | rose | Arcane, floral |
| `&5` | violet | Magie, Vide, boss arcaniques |
| `&8` | gris foncé | Ombre, cendre, créatures mortes |
| `&7` | gris | Neutre, bêtes ordinaires |
| `&f` | blanc | Montures nobles |

### Les deux règles

**La couleur suit la famille.** Les cinq gobelins sont tous en `&2`, les coffres suivent
leur rareté (`&7`, `&9`, `&6`). Un joueur apprend à lire la famille à la couleur.

**La couleur suit la rareté, quand il y en a une.**

| Rareté | Code |
|---|---|
| Commune | `&7` ou `&f` |
| Rare | `&9` |
| Épique | `&5` |
| Légendaire | `&6` |

### Pour les quêtes

| Type | Code |
|---|---|
| Quête de trame | `&6` |
| Quête annexe | `&a` |

C'est immédiatement lisible dans le journal.

---

## 4. Les chemins de modèles

### La forme

Sous l'arborescence des modèles du client, en respectant la hiérarchie par famille.

| Exemple | Ce que ça dit |
|---|---|
| `mobs/gobelin/am_goblin_melee` | Une créature hostile, famille gobelin |
| `mobs/slime/slime_lava` | Famille gelée |
| `mobs/dungeon/dragons/flarewing_dragon` | Un boss de donjon |
| `mobs/rare/lost_vein/the_nyx` | Une créature rare, famille de la veine perdue |
| `mobs/passive/mounts/bearbrownec` | Une apparence de monture |
| `mobs/vfx/vfx_soul_ball` | Un effet visuel |
| `dragonnet_ebene/dragonnet_ebene` | Un compagnon, à la racine |

### Les règles

| Règle | Pourquoi |
|---|---|
| Hostiles sous `mobs/` | — |
| Paisibles sous `mobs/passive/` | — |
| Boss sous `mobs/dungeon/` | — |
| Effets visuels sous un dossier `vfx/` | Ils ne sont pas des créatures |
| Compagnons à la racine | Ils sont antérieurs à la convention |
| **Sans extension** dans le catalogue | Le champ `model` ne porte pas `.bbmodel` |

### Les noms de modèles existants sont irréguliers

`am_goblin_melee`, `bl_shadow_spider`, `nm_squirrel_brown`, `bearbrownec` : les préfixes
et suffixes viennent des banques de modèles d'origine.

> **Ne pas les renommer** — le catalogue, les classes générées et les contrôles y
> renvoient. Pour un modèle neuf, suivre la convention des identifiants : minuscules,
> soulignés, anglais.

---

## 5. Les noms de clips

Voir [Pipeline Blockbench](pipeline-bbmodel.md#3-les-noms-de-clips) pour la liste
complète de ce que le moteur reconnaît.

| Catégorie | Convention |
|---|---|
| **Locomotion, mort, douleur** | **Un nom que le moteur cherche.** Pas de liberté ici. |
| **Attaques** | Libre, mais recopié à l'identique dans le catalogue |

### Pour un modèle neuf

Préférer les noms anglais simples : `idle`, `walk`, `sprint`, `death`, `hurt`. Ils sont
tous reconnus, et ce sont ceux que la majorité des modèles livrés emploient.

Les variantes françaises et les participes présents sont acceptés parce que des modèles
livrés les emploient — ce n'est pas une raison d'en ajouter.

---

## 6. Les messages

### Les couleurs de message

| Situation | Code |
|---|---|
| Une erreur, un refus | `&c` |
| Une information | `&7` |
| Une valeur mise en avant | `&b`, `&e` ou `&f` |
| Un titre de section | `&8&m----------` autour du titre |

### Le registre

Court. Un message en jeu est lu en passant.

| À faire | À éviter |
|---|---|
| Dire ce qui s'est passé | Expliquer pourquoi |
| Une ligne | Un paragraphe |
| Nommer l'action suivante si elle existe | Laisser le joueur deviner |

---

## 7. Le vocabulaire

Voir le [glossaire](../01-projet/glossaire.md) pour la liste complète. Les substitutions
les plus souvent oubliées :

| On écrit | Jamais |
|---|---|
| **Coven** | faction, guilde |
| **Ère** | saison, cycle |
| **Éclat** | shard |
| **Poussière d'Étoile** | dust |
| **Essence Arcane** | mana (du joueur) |
| **Mana Brut** | mana (tout court) |

La dernière paire est celle qui se confond le plus, et la confusion a déjà coûté des
heures : **l'Essence Arcane est personnelle, le Mana Brut appartient au Coven.**

---

## 8. Les commits et les branches

| Élément | Convention |
|---|---|
| **Branche** | Une par sujet. Jamais d'écriture sur `main`. |
| **Commit** | [Conventional Commits](https://www.conventionalcommits.org/fr/) |
| **Portée** | `docs(magie)`, `feat(mobs)`, `fix(quest)` |
| **Message** | Impératif, en une ligne, puis un corps qui dit **pourquoi** |

### Ce qu'un bon message contient

Pas ce qui a changé — le diff le dit. **Pourquoi** ça a changé, et ce que ça corrige.

> « fix(anim) : une face sans texture ne se dessine plus en magenta »

Le titre dit le symptôme corrigé, pas la ligne modifiée.

---

## 9. Les commentaires de configuration

Un fichier de configuration livré porte des commentaires qui expliquent **pourquoi** une
valeur vaut ce qu'elle vaut.

| À commenter | À ne pas commenter |
|---|---|
| Une valeur qui a une histoire | Une valeur évidente |
| Un garde-fou, et ce qu'il empêche | La répétition du nom du champ |
| Un choix contre-intuitif | — |

### L'exemple de référence

La régénération d'Essence porte en commentaire l'histoire de ses **deux baisses** et la
raison de la dernière — sans cette note, quelqu'un la remonterait en croyant rendre
service, et supprimerait tout l'intérêt des fioles.

C'est le niveau attendu : trois lignes qui empêchent une régression.

---

## 10. Les unités

| Grandeur | Unité | Note |
|---|---|---|
| Temps court | **ticks** | 20 ticks = 1 seconde. Toujours préciser. |
| Temps long | secondes, minutes, heures | — |
| Distance | **blocs** | — |
| Pixels Blockbench | 16 px = 1 bloc | Dans les modèles uniquement |
| Probabilité | décimal entre 0 et 1 | `0.35`, pas `35 %` |
| Pourcentage de configuration | selon le champ | Certains acceptent `0.5`, d'autres `50` — lire la référence |

La dernière ligne est un piège réel : le remboursement à l'interruption accepte les deux
formes. Vérifier dans la
[référence de configuration](../05-operer/configuration/README.md) avant d'écrire.

---

## À lire ensuite

- [Glossaire](../01-projet/glossaire.md) — tout le vocabulaire
- [Pipeline Blockbench](pipeline-bbmodel.md) — les noms de clips
- [Plages d'identifiants](../07-reference/plages-d-identifiants.md) — ce qui est pris
- [Comment lire cette documentation](../00-lire-cette-doc.md) — les conventions d'écriture
