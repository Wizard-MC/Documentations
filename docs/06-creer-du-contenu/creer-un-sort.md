# Créer un sort

Ajouter un sort au catalogue, sans écrire de code. Compte une heure, plus le temps de
calibrer.

**Statut : livré.** Quarante et un sorts existent ; le quarante-deuxième s'écrit en
configuration tant qu'il compose des effets déjà disponibles.

---

## 1. Avant de commencer

### Ce qui s'ajoute sans développeur

Un sort qui **compose des effets déjà disponibles**. Les vingt-trois effets existants
couvrent les dégâts, les soins, les entraves, les potions, les poussées, les utilitaires,
et le sacrifice de vie.

### Ce qui demande un développeur

Un sort qui fait quelque chose qu'aucun effet ne sait faire. Ajouter un effet, c'est
ajouter une implémentation et l'enregistrer — **aucun sort n'a de code dédié dans le
moteur de lancement**, ce qui veut dire qu'un nouvel effet profite ensuite à tous.

### Les questions auxquelles il faut répondre

| Question | Pourquoi |
|---|---|
| **Quelle école ?** | Elle décide de la palette, des sons, et de l'identité du sort |
| **Qu'est-ce qu'il fait que les autres ne font pas ?** | Un quarante-deuxième sort qui répète le dix-septième dilue le catalogue |
| **Où s'apprend-il ?** | Au grimoire, ou seulement par un Tome |
| **Est-il hostile ?** | Un sort hostile a des contraintes supplémentaires |

---

## 2. Les champs

| Champ | Ce qu'on y met | Obligatoire |
|---|---|---|
| `school` | L'école, parmi les neuf | **oui** |
| `name` | Le nom affiché, avec son code de couleur | oui |
| `description` | Ce que le joueur lit | oui |
| `incantation` | La formule affichée pendant l'incantation | oui |
| `mode` | `INSTANT`, `CAST_TIME` ou `CHANNEL` | oui |
| `manaCost` | Le coût en Essence | oui |
| `castTicks` | L'incantation propre au sort, si elle diffère du défaut | non |
| `cooldownTicks` | La recharge | oui |
| `gcdTicks` | La pause commune, si elle diffère | non |
| `requiredSchoolLevel` | Le niveau d'école exigé | oui |
| `requiredWandTier` | Le palier de baguette exigé | oui |
| `range` | La portée, en blocs | oui |
| `hostile` | S'il vise un adversaire | oui |
| `interruptible` | S'il peut être coupé | oui |
| `autoLearn` | S'il est connu d'office | non |
| `tradition` | L'école d'origine, si elle a été absorbée. **Purement narratif.** | non |
| `fx` | `color`, `trail`, `impact`, `anim` | oui |
| `effects` | Ce que le sort fait réellement | **oui** |

---

## 3. Les vingt-trois effets disponibles

| Effet | Ce qu'il fait | Usages livrés |
|---|---|---:|
| `terrain_impact` | Laisse une trace dans le décor | 22 |
| `damage_target` | Dégâts à la cible, plafonnés | 17 |
| `potion_self` | Effet de potion sur soi | 14 |
| `self_message` | Un retour texte au lanceur | 8 |
| `potion_target` | Effet de potion sur la cible | 3 |
| `self_hurt` | Sacrifice de points de vie | 2 |
| `potion_area` | Effet de potion sur les alliés proches | 2 |
| `cone_damage` | Dégâts dans un cône devant le lanceur | 1 |
| `slow_target` | Ralentit | 1 |
| `silence_target` | Empêche la cible de lancer des sorts | 1 |
| `frost_prison` | Immobilisation totale, avec une cage visible | 1 |
| `heal_target`, `heal_self` | Soin | 1 chacun |
| `cleanse_debuffs` | Retire les effets négatifs | 1 |
| `fall_resist` | Absorbe une chute courte | 1 |
| `gust_push` | Pousse les entités devant le lanceur | 1 |
| `crop_boost`, `harvest_boost` | Croissance, rendement | 1 chacun |
| `lift_block` | Aide au levage d'un bloc | 1 |
| `preserve_zone` | Zone de conservation | 1 |
| `ore_sense` | Révèle brièvement les minerais proches | 1 |
| `light_aura` | Une lueur qui suit le lanceur | 1 |
| `essence_grant` | Gain d'Essence | 1 |

### Ce que cette table apprend

`terrain_impact` est l'effet le plus utilisé du jeu, et il ne fait **aucun dégât** : il
laisse une trace dans le décor. C'est ce qui rend les sorts lisibles après coup — on voit
où ça a frappé.

`self_message` est le quatrième : huit sorts se contentent de dire au lanceur que
quelque chose s'est passé. Un sort dont l'effet est invisible a besoin de le dire.

### Composer plutôt qu'inventer

La plupart des sorts combinent deux à quatre effets. Le premier sort du catalogue, une
simple lueur, en a trois : un effet de potion sur soi, le même sur les alliés proches, et
un message.

> **Avant de demander un nouvel effet, vérifier qu'une composition ne suffit pas.**

---

## 4. Calibrer un sort

### Les trois modes

| Mode | Quand l'utiliser |
|---|---|
| `INSTANT` | L'effet part à la fin de l'incantation de base |
| `CAST_TIME` | Le sort déclare sa propre durée d'incantation, plus longue |
| `CHANNEL` | L'effet se répète pendant qu'on canalise |

**Tout sort s'incante**, y compris `INSTANT` : l'incantation de base est de cinq
secondes, réduite par le niveau d'école et le rang. C'est ce qui rend un sort esquivable,
et il n'y a pas d'exception.

### Le coût en Essence

| Référence livrée | Coût |
|---|---:|
| Un sort utilitaire | 8 à 11 |
| Un sort de dégâts ordinaire | 9 à 14 |
| Un soin | 18 |

La réserve de base est de cent, et elle se régénère de quatre points toutes les dix
secondes hors combat. Un sort à trente coûte donc plus d'une minute d'attente.

### La recharge

| Référence livrée | Recharge |
|---|---:|
| Un sort de dégâts | 90 à 100 ticks |
| Un soin | 180 ticks |
| Un utilitaire durable | 200 à 300 ticks |

> **Un sort hostile doit avoir une recharge d'au moins cinquante ticks.** C'est ce qui
> empêche le harcèlement à répétition.

### Le niveau d'école et le palier de baguette

Ce sont les deux verrous de progression.

| Verrou | À quoi il sert |
|---|---|
| `requiredSchoolLevel` | Ordonne les sorts d'une même école |
| `requiredWandTier` | Réserve les sorts forts aux baguettes avancées |

Un sort de départ demande niveau 0 et palier 1. Un sort avancé demande un niveau d'école
réel et un palier 3.

### La portée

Une portée longue sur un sort hostile impose de vérifier la ligne de vue — sans quoi on
frappe à travers les murs, ce qui ne se voit pas venir et ne s'esquive pas.

---

## 5. Les effets visuels

| Champ | Ce qu'on y met |
|---|---|
| `color` | La couleur, au format hexadécimal |
| `trail` | La traînée |
| `impact` | L'effet à l'arrivée |
| `anim` | L'animation du lanceur |

### Respecter l'identité de l'école

Chaque école a une palette et une grammaire visuelle, et c'est ce qui permet de
reconnaître un sort sans lire son nom.

| École | Palette | Formes |
|---|---|---|
| Arcane | violet | géométrie pure |
| Braises | orangé | particules qui montent |
| Givre | bleu pâle | formes anguleuses, sons de verre |
| Tempête | violine | éclats, étincelles |
| Roc | terre | **particules qui retombent** au lieu de monter |
| Aurore | doré | formes verticales, sons cristallins |
| Esprit | pâle | — |
| Ombre | noir | **en alpha plutôt qu'additif** : absorbe la lumière au lieu d'en émettre |
| Vide | presque noir | spirales |

Les deux lignes en gras sont les plus distinctives du lot, et elles montrent ce qu'on
attend : une école ne se distingue pas par sa couleur seulement, mais par la **direction**
et le **mode de rendu** de ses particules.

La fiche détaillée de chaque école est dans
[`docs/90-specifications/magie/ecoles/`](../90-specifications/magie/ecoles/).

---

## 6. Comment le sort s'apprend

| Voie | Comment la déclarer |
|---|---|
| **Connu d'office** | `autoLearn: true` |
| **Au grimoire** | Rien à déclarer : c'est le défaut, contre de l'expérience d'école |
| **Par un Tome uniquement** | Voir la spécification : dix-neuf sorts sont dans ce cas |

Un sort de Tome est une récompense de boss. Lui donner un accès au grimoire annulerait
l'intérêt d'aller chercher le boss.

---

## 7. Ce qu'un sort ne peut jamais faire

| Jamais | Pourquoi |
|---|---|
| S'acheter | Le principe n'a pas d'exception |
| Être réservé à une école d'origine | Le choix de voie ne doit fermer aucune porte |
| Partir sans incantation | C'est ce qui le rend esquivable |
| Dépenser l'Essence à la résolution | Elle est dépensée **au départ** : un sort interrompu rend la moitié |
| Ignorer la pause commune | Sans elle, deux sorts prêts vident un adversaire avant qu'il bouge |
| Fonctionner dans une région interdite | Vérifié **au lancement et à l'impact** |

### La règle des régions interdites

L'interdiction est vérifiée deux fois, et c'est important : **sans la vérification à
l'impact, il suffirait de se placer juste en dehors d'une arène pour en arroser
l'intérieur.**

Un sort nouveau n'a rien à déclarer pour ça — c'est le moteur qui s'en charge — mais il
faut le savoir quand on calibre une portée.

---

## 8. Les rangs et les sceaux

Un sort monte jusqu'au rang V. Chaque rang réduit le coût de 6 % et l'incantation de 5 %.

Un sort peut déclarer les **sceaux** qu'il accepte. Leur raison d'être : un sort qui ne
progresserait que sur un seul axe finirait par n'avoir qu'une seule bonne façon d'être
monté. Les sceaux donnent des choix.

Le prix d'un rang comme d'un sceau est de l'**expérience d'école**, et rien d'autre.
Jamais de monnaie, jamais de Gemmes.

---

## 9. Essayer

| À vérifier | Comment |
|---|---|
| Il s'apprend | `/magic learn` |
| Il se place sur la barre | `/magic bind` |
| L'incantation se voit | Le lancer et se regarder |
| Le coût est débité au départ | Le lancer et se faire interrompre : la moitié revient |
| L'interruption ne déclenche pas la recharge | Relancer aussitôt après une interruption |
| La recharge est tenable | L'enchaîner |
| La portée correspond | Viser de plus en plus loin |
| Les effets visuels respectent l'école | Comparer à un sort voisin |
| Il est refusé dans une région interdite | L'essayer au bord d'une arène, **depuis l'extérieur** |

Les commandes d'administration `/magic give` et `/magic reload` exigent la permission
`wizardmc.magic.admin`.

---

## 10. La liste complète

| # | Étape |
|---|---|
| 1 | Choisir l'école, et vérifier que le sort n'en répète pas un autre |
| 2 | Choisir les effets **parmi ceux qui existent** |
| 3 | Écrire l'entrée, avec la raison des valeurs |
| 4 | Calibrer coût, recharge, portée par comparaison aux sorts voisins |
| 5 | Respecter la palette et la grammaire visuelle de l'école |
| 6 | Décider comment il s'apprend |
| 7 | Recharger et essayer, y compris l'interruption et les régions interdites |
| 8 | Documenter dans le [catalogue des sorts](../07-reference/catalogue-sorts.md) |
| 9 | Une branche, un commit, une *pull request* |

---

## À lire ensuite

- [Catalogue des sorts](../07-reference/catalogue-sorts.md) — les 41 sorts livrés
- [cdc_magie](../90-specifications/cdc_magie.md) — la spécification complète
- [Les fiches d'école](../90-specifications/magie/ecoles/) — l'identité visuelle de chacune
- [La magie](../04-jouer/magie.md) — le point de vue du joueur
