# Catalogue des créatures

Les 49 créatures hostiles, les 24 bêtes paisibles et les 10 montures, avec leurs valeurs
livrées. Référence de consultation.

**Statut : livré.** Cette table est dérivée des fichiers de catalogue.

---

## Comment la lire

| Colonne | Ce qu'elle dit |
|---|---|
| **Identifiant** | La clé, telle qu'elle s'écrit dans les commandes et les objectifs de quête |
| **Nom** | Ce que le joueur lit |
| **Tempérament** | Comment elle se bat — voir [le glossaire](../01-projet/glossaire.md#les-tempéraments) |
| **PV**, **Dégâts**, **Vitesse** | Les valeurs de base, avant le niveau |
| **Boîte** | Largeur × hauteur, en blocs |
| **Poids** | Le poids d'apparition. **`0` = n'apparaît jamais d'elle-même.** |
| **Niv. école** | Le niveau d'école minimal d'un joueur proche |

Les valeurs de PV et de dégâts sont celles d'une créature **de base** : le niveau, qui
dépend de la région et de la puissance du joueur le plus proche, les fait monter.


## Gobelins — 5 espèces

| Identifiant | Nom | Tempérament | PV | Dégâts | Vitesse | Boîte | Élite |
|---|---|---|---:|---:|---:|---|---|
| `goblin_brute` | Brute gobeline | BRUTE | 48.0 | 6.0 | 0.24 | 0.9 × 2.0 | — |
| `goblin_mage` | Chaman gobelin | CASTER | 20.0 | 4.0 | 0.26 | 0.6 × 1.8 | — |
| `goblin_melee` | Gobelin | SKIRMISHER | 22.0 | 3.5 | 0.27 | 0.6 × 1.8 | — |
| `goblin_ranger` | Archer gobelin | RANGED | 18.0 | 3.0 | 0.29 | 0.6 × 1.8 | — |
| `goblin_whip` | Gobelin au fouet | SKIRMISHER | 24.0 | 4.0 | 0.28 | 0.6 × 1.8 | — |

## Gelées — 9 espèces

| Identifiant | Nom | Tempérament | PV | Dégâts | Vitesse | Boîte | Élite |
|---|---|---|---:|---:|---:|---|---|
| `slime_common` | Gelee commune | BRUTE | 22.0 | 3.0 | 0.24 | 0.9 × 0.9 | — |
| `slime_frost` | Gelee de givre | BRUTE | 30.0 | 4.0 | 0.22 | 1.0 × 1.0 | — |
| `slime_healer` | Gelee soigneuse | SKIRMISHER | 26.0 | 2.0 | 0.27 | 0.9 × 0.9 | — |
| `slime_king` | Le Roi des gelees | BRUTE | 190.0 | 12.0 | 0.24 | 2.6 × 2.6 | oui |
| `slime_lava` | Gelee de lave | BRUTE | 38.0 | 7.0 | 0.21 | 1.0 × 1.0 | — |
| `slime_mage` | Gelee arcanique | CASTER | 28.0 | 5.0 | 0.23 | 0.9 × 1.0 | — |
| `slime_triple` | Gelee tricephale | PACK_HUNTER | 44.0 | 6.0 | 0.26 | 1.2 × 1.0 | — |
| `slime_warrior` | Gelee guerriere | SENTINEL | 52.0 | 8.0 | 0.25 | 1.1 × 1.4 | — |
| `slime_winged` | Gelee ailee | SKIRMISHER | 20.0 | 4.0 | 0.30 | 0.8 × 1.0 | — |

## Araignées — 3 espèces

| Identifiant | Nom | Tempérament | PV | Dégâts | Vitesse | Boîte | Élite |
|---|---|---|---:|---:|---:|---|---|
| `flying_spider` | Araignee ailee | PACK_HUNTER | 24.0 | 4.5 | 0.32 | 1.8 × 1.2 | — |
| `shadow_spider` | Araignee d'ombre | STALKER | 20.0 | 4.0 | 0.31 | 1.2 × 0.9 | — |
| `toxin_spider` | Araignee toxique | PACK_HUNTER | 26.0 | 4.0 | 0.28 | 1.6 × 1.3 | — |

## Mimiques et coffres — 6 espèces

| Identifiant | Nom | Tempérament | PV | Dégâts | Vitesse | Boîte | Élite |
|---|---|---|---:|---:|---:|---|---|
| `common_chest` | Coffre | SENTINEL | 20.0 | 0.0 |  | 1.0 × 0.9 | — |
| `common_mimic` | Mimique | AMBUSHER | 30.0 | 5.0 | 0.25 | 1.0 × 0.9 | — |
| `legendary_chest` | Coffre legendaire | SENTINEL | 20.0 | 0.0 |  | 1.2 × 1.2 | — |
| `legendary_mimic` | Mimique legendaire | AMBUSHER | 110.0 | 10.0 | 0.26 | 1.2 × 1.2 | oui |
| `rare_chest` | Coffre rare | SENTINEL | 20.0 | 0.0 |  | 1.1 × 0.9 | — |
| `rare_mimic` | Mimique rare | AMBUSHER | 55.0 | 7.0 | 0.25 | 1.1 × 0.9 | — |

## Golem — 1 espèces

| Identifiant | Nom | Tempérament | PV | Dégâts | Vitesse | Boîte | Élite |
|---|---|---|---:|---:|---:|---|---|
| `moss_golem` | Golem de mousse | SENTINEL | 60.0 | 6.5 | 0.20 | 1.0 × 1.6 | — |

## Bois vivants — 5 espèces

| Identifiant | Nom | Tempérament | PV | Dégâts | Vitesse | Boîte | Élite |
|---|---|---|---:|---:|---:|---|---|
| `blighted_bark` | Ecorce fletrie | SENTINEL | 45.0 | 6.0 | 0.20 | 1.2 × 2.4 | — |
| `broodring_blossom` | Fleur couveuse | BRUTE | 55.0 | 6.5 | 0.22 | 1.6 × 2.6 | — |
| `hollow_howl` | Hurlement creux | BRUTE | 80.0 | 8.0 | 0.24 | 1.8 × 3.0 | — |
| `the_hemlock` | La Cigue | CASTER | 170.0 | 10.0 | 0.21 | 1.8 × 3.2 | oui |
| `vile_vine` | Liane vile | SKIRMISHER | 28.0 | 5.0 | 0.31 | 0.9 × 1.8 | — |

## Prairies grondantes — 5 espèces

| Identifiant | Nom | Tempérament | PV | Dégâts | Vitesse | Boîte | Élite |
|---|---|---|---:|---:|---:|---|---|
| `ashen_azalea` | Azalee de cendres | BRUTE | 120.0 | 9.0 | 0.19 | 3.5 × 5.5 | oui |
| `lurking_lily` | Nenuphar tapi | AMBUSHER | 26.0 | 5.0 | 0.22 | 1.2 × 1.6 | — |
| `malevolent_moss` | Mousse malveillante | AMBUSHER | 24.0 | 4.0 | 0.18 | 1.2 × 2.0 | — |
| `the_soulrot` | Le Pourriseur d'ames | CASTER | 160.0 | 10.0 | 0.22 | 2.4 × 3.6 | oui |
| `whispering_wisteria` | Glycine murmurante | STALKER | 34.0 | 6.0 | 0.30 | 1.0 × 2.2 | — |

## Veine perdue — 6 espèces

| Identifiant | Nom | Tempérament | PV | Dégâts | Vitesse | Boîte | Élite |
|---|---|---|---:|---:|---:|---|---|
| `foul_flower` | Fleur immonde | AMBUSHER | 34.0 | 6.0 | 0.20 | 1.4 × 1.6 | — |
| `oblivion_orb_purple` | Orbe d'oubli | CASTER | 22.0 | 9.0 | 0.24 | 1.2 × 1.2 | — |
| `oblivion_orb_yellow` | Orbe d'oubli pale | CASTER | 18.0 | 7.0 | 0.26 | 1.2 × 1.2 | — |
| `sorrowful_sylph` | Sylphe eploree | CASTER | 30.0 | 6.0 | 0.27 | 1.0 × 2.0 | — |
| `the_nyx` | Le Nyx | STALKER | 200.0 | 12.0 | 0.26 | 2.0 × 3.4 | oui |
| `wicked_wolf` | Loup maudit | PACK_HUNTER | 38.0 | 7.0 | 0.33 | 1.6 × 1.4 | — |

## Marais moisi — 5 espèces

| Identifiant | Nom | Tempérament | PV | Dégâts | Vitesse | Boîte | Élite |
|---|---|---|---:|---:|---:|---|---|
| `brook_bug` | Insecte des ruisseaux | PACK_HUNTER | 16.0 | 3.5 | 0.32 | 1.0 × 0.8 | — |
| `grossy_gator` | Gator fangeux | AMBUSHER | 65.0 | 8.0 | 0.25 | 2.2 × 1.2 | — |
| `mangrove_glare` | Glaneur de la mangrove | STALKER | 36.0 | 6.0 | 0.29 | 1.2 × 1.8 | — |
| `the_cryptic` | Le Cryptique | BRUTE | 220.0 | 13.0 | 0.23 | 3.0 × 4.0 | oui |
| `wretched_weaver` | Tisseuse infame | STALKER | 90.0 | 8.5 | 0.28 | 2.0 × 2.0 | — |

## Dragons de donjon — 4 espèces

| Identifiant | Nom | Tempérament | PV | Dégâts | Vitesse | Boîte | Élite |
|---|---|---|---:|---:|---:|---|---|
| `celestial_thunderlord` | Seigneur-Tonnerre celeste | CASTER | 480.0 | 22.0 | 0.30 | 5.6 × 9.5 | oui |
| `flarewing_dragon` | Dragon Aile-de-braise | BRUTE | 360.0 | 18.0 | 0.29 | 3.3 × 5.1 | oui |
| `skywhisper_wyvern` | Vouivre Chuchevent | SKIRMISHER | 280.0 | 15.0 | 0.32 | 2.4 × 4.1 | oui |
| `terravore_drake` | Drake Terravore | BRUTE | 300.0 | 16.0 | 0.28 | 5 × 6.3 | oui |

---

## Les créatures qui bondissent

Huit espèces avancent par bonds. `hopTicks` **doit valoir la durée du clip de bond**
du modèle : le clip joue alors exactement une fois par saut.

| Identifiant | Nom | Période de bond | Durée du clip |
|---|---|---:|---:|
| `slime_common` | Gelee commune | 35 ticks | 1.75 s |
| `slime_frost` | Gelee de givre | 35 ticks | 1.75 s |
| `slime_healer` | Gelee soigneuse | 35 ticks | 1.75 s |
| `slime_lava` | Gelee de lave | 35 ticks | 1.75 s |
| `slime_mage` | Gelee arcanique | 35 ticks | 1.75 s |
| `slime_triple` | Gelee tricephale | 29 ticks | 1.45 s |
| `slime_warrior` | Gelee guerriere | 35 ticks | 1.75 s |
| `slime_winged` | Gelee ailee | 30 ticks | 1.50 s |

Le Roi des gelées garde la marche : son modèle n'a aucun clip de déplacement.

---

## Les bêtes paisibles — 24 espèces

| Identifiant | Nom | Disposition | PV | Vitesse | Boîte | Clip de caresse |
|---|---|---|---:|---:|---|---|
| `alpha_boar` | Sanglier alpha | DEFENSIVE | 34.0 | 0.29 | 1.1 × 1.1 | rear |
| `axolotl` | Axolotl | CALM | 14.0 | 0.24 | 1.3 × 2.1 | petting |
| `bear` | Ours | CALM | 55.0 | 0.27 | 1.4 × 1.5 | petting |
| `boar` | Sanglier | CALM | 14.0 | 0.26 | 0.9 × 0.9 | rear |
| `budgie` | Perruche | SKITTISH | 3.0 | 0.32 | 0.4 × 0.5 | preen |
| `buffalo` | Bison | CALM | 40.0 | 0.24 | 1.4 × 1.6 | lick |
| `butterfly` | Papillon | SKITTISH | 2.0 | 0.26 | 0.4 × 0.4 | — |
| `catfish` | Poisson-chat | CALM | 6.0 | 0.20 | 0.6 × 0.4 | — |
| `crab` | Crabe | CALM | 20.0 | 0.22 | 1.1 × 0.8 | petting |
| `crocodile` | Crocodile | DEFENSIVE | 45.0 | 0.22 | 1.6 × 0.8 | — |
| `crow` | Corbeau | SKITTISH | 12.0 | 0.31 | 0.7 × 1.0 | petting |
| `drake` | Drakelet | CALM | 60.0 | 0.30 | 1.3 × 1.6 | petting |
| `foxy` | Renard | SKITTISH | 16.0 | 0.33 | 0.8 × 0.8 | petting |
| `frostwhisker` | Loutre des neiges | CALM | 22.0 | 0.28 | 0.9 × 0.9 | petting |
| `griffon` | Griffon | CALM | 70.0 | 0.32 | 2.5 × 3.1 | petting |
| `husky` | Husky | CALM | 24.0 | 0.32 | 1.4 × 1.9 | petting |
| `knight_flail` | Garde au fleau | DEFENSIVE | 60.0 | 0.27 | 0.7 × 1.9 | idle_variant |
| `knight_halberd` | Garde a la hallebarde | DEFENSIVE | 62.0 | 0.26 | 0.7 × 1.9 | idle_variant |
| `knight_pike` | Garde a la pique | DEFENSIVE | 58.0 | 0.27 | 0.7 × 1.9 | idle_variant |
| `knight_warhammer` | Garde au marteau | DEFENSIVE | 66.0 | 0.25 | 0.7 × 1.9 | idle_variant |
| `squirrel` | Ecureuil | SKITTISH | 4.0 | 0.34 | 0.5 × 0.5 | sniff |
| `timber_wolf` | Loup des bois | DEFENSIVE | 24.0 | 0.31 | 0.8 × 0.9 | — |
| `tortoise` | Tortue | CALM | 18.0 | 0.12 | 0.9 × 0.6 | hide |
| `yak` | Yak | CALM | 44.0 | 0.22 | 1.3 × 1.5 | petting |

---

## Les montures — 10

> **La rareté ne dit pas ce que la monture fait.** Elle dit ce qu'elle a coûté à
> obtenir, et décide de sa couleur. La vitesse est écrite monture par monture.

| Identifiant | Nom | Espèce | Rareté | Vitesse | Vol |
|---|---|---|---|---:|---|
| `husky` | Husky | `husky` | Commune | 0.28 | — |
| `yak` | Yak des cimes | `yak` | Commune | 0.26 | — |
| `foxy_red` | Renard des braises | `foxy` | Rare | 0.42 | — |
| `bear_brown` | Ours brun | `bear` | Rare | 0.34 | — |
| `crab` | Crabe de jade | `crab` | Rare | 0.30 | — |
| `crow` | Corbeau des augures | `crow` | Epique | 0.50 | oui |
| `frostwhisker` | Murmure-de-givre | `frostwhisker` | Epique | 0.46 | — |
| `bear_white` | Ours des glaces | `bear` | Epique | 0.34 | — |
| `griffon` | Griffon | `griffon` | Legendaire | 0.62 | oui |
| `drake_gold` | Drakelet d'or | `drake` | Legendaire | 0.58 | oui |

---

## Les décalages d'hostilité par région

Aucune espèce n'est réservée à une région. Ce qui change, c'est le **niveau**.

| Région | Décalage |
|---|---:|
| Sanctuaire (spawn) | −8 |
| Plaine des Murmures | −4 |
| Désert de Cristal | +4 |
| Forêt d'Ébène | +8 |
| Montagnes du Crépuscule | +14 |
| Wilderness | +6 |

---

## À lire ensuite

- [Bestiaire](../02-univers/bestiaire.md) — les familles racontées
- [WizardMobs](../05-operer/configuration/wizardmobs.md) — chaque champ du catalogue
- [Créer une créature](../06-creer-du-contenu/creer-une-creature.md) — la procédure
- [Combat et créatures](../04-jouer/combat-et-creatures.md) — le point de vue du joueur

