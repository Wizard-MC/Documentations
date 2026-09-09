# Catalogue des sorts

Les 41 sorts de WizardMC, par école, avec leurs valeurs livrées.

**Statut : livré.** Cette table est dérivée du catalogue.

---

## Comment la lire

| Colonne | Ce qu'elle dit |
|---|---|
| **Identifiant** | La clé |
| **Nom** | Ce que le joueur lit |
| **Mode** | `INSTANT` (21), `PROJECTILE` (13), `CAST_TIME` (6) ou `CHANNEL` (1) |
| **Essence** | Le coût |
| **Recharge** | En ticks. 20 ticks = 1 seconde. |
| **Niv.** | Le niveau d'école exigé |
| **Palier** | Le palier de baguette exigé |
| **Portée** | En blocs |
| **Hostile** | S'il vise un adversaire |
| **Tradition** | L'école d'origine, quand elle a été absorbée. **Purement narratif.** |

**Tout sort s'incante**, y compris ceux en mode `INSTANT` : l'incantation de base est de
cinq secondes, réduite de 4 % par niveau d'école et de 5 % par rang, sans descendre sous
une seconde.


## Arcane — 4 sorts

| Identifiant | Nom | Mode | Essence | Recharge | Niv. | Palier | Portée | Hostile | Tradition |
|---|---|---|---:|---:|---:|---:|---:|---|---|
| `arcane_sense` | Sens Arcanique | INSTANT | 18 | 600 | 0 | 1 | 12 | — | — |
| `arcane_spark` | Etincelle d'Arcane | PROJECTILE | 11 | 85 | 0 | 1 | 20 | oui | — |
| `arcane_bolt` | Éclair d'Arcane | PROJECTILE | 14 | 55 | 1 | 1 | 24 | oui | — |
| `arcane_ward` | Egide d'Arcane | INSTANT | 16 | 240 | 1 | 1 | 0 | — | — |

## Braises — 3 sorts

| Identifiant | Nom | Mode | Essence | Recharge | Niv. | Palier | Portée | Hostile | Tradition |
|---|---|---|---:|---:|---:|---:|---:|---|---|
| `ember_fireball` | Boule de Feu | PROJECTILE | 10 | 90 | 0 | 1 | 20 | oui | — |
| `ember_torch` | Torche de Braise | INSTANT | 10 | 120 | 0 | 1 | 0 | — | — |
| `ember_flare` | Embrasement | CAST_TIME | 22 | 140 | 2 | 2 | 8 | oui | — |

## Givre — 4 sorts

| Identifiant | Nom | Mode | Essence | Recharge | Niv. | Palier | Portée | Hostile | Tradition |
|---|---|---|---:|---:|---:|---:|---:|---|---|
| `frost_needle` | Aiguille de Givre | PROJECTILE | 10 | 90 | 0 | 1 | 20 | oui | — |
| `frost_preserve` | Préserve de Givre | INSTANT | 12 | 240 | 0 | 1 | 6 | — | — |
| `frost_shard` | Éclat de Givre | PROJECTILE | 15 | 65 | 1 | 1 | 22 | oui | — |
| `frost_prison` | Geôle de Givre | CAST_TIME | 26 | 600 | 3 | 2 | 12 | oui | — |

## Tempête — 4 sorts

| Identifiant | Nom | Mode | Essence | Recharge | Niv. | Palier | Portée | Hostile | Tradition |
|---|---|---|---:|---:|---:|---:|---:|---|---|
| `storm_breeze` | Brise de Tempête | INSTANT | 10 | 160 | 0 | 1 | 1 | — | — |
| `storm_jolt` | Secousse de Tempete | INSTANT | 12 | 80 | 0 | 1 | 12 | oui | — |
| `storm_gust` | Rafale | INSTANT | 14 | 100 | 1 | 1 | 6 | oui | — |
| `storm_spark` | Étincelle de Foudre | PROJECTILE | 14 | 55 | 1 | 2 | 22 | oui | — |

## Roc — 5 sorts

| Identifiant | Nom | Mode | Essence | Recharge | Niv. | Palier | Portée | Hostile | Tradition |
|---|---|---|---:|---:|---:|---:|---:|---|---|
| `earth_brace` | Ancrage de Roc | INSTANT | 10 | 200 | 0 | 1 | 1 | — | — |
| `earth_shard` | Eclat de Roc | INSTANT | 9 | 95 | 0 | 1 | 14 | oui | — |
| `nature_growth` | Pousse Sylvaine | CAST_TIME | 14 | 200 | 0 | 1 | 4 | — | Sylvanie |
| `earth_lift` | Soulèvement de Roc | CHANNEL | 16 | 120 | 1 | 1 | 5 | — | — |
| `nature_harvest` | Moisson Sylvaine | INSTANT | 10 | 300 | 1 | 1 | 3 | — | Sylvanie |

## Aurore — 3 sorts

| Identifiant | Nom | Mode | Essence | Recharge | Niv. | Palier | Portée | Hostile | Tradition |
|---|---|---|---:|---:|---:|---:|---:|---|---|
| `light_glimmer` | Lueur d'Aurore | INSTANT | 8 | 100 | 0 | 1 | 8 | — | — |
| `light_ray` | Rayon d'Aurore | INSTANT | 11 | 95 | 0 | 1 | 24 | oui | — |
| `light_mend` | Suture d'Aurore | CAST_TIME | 18 | 180 | 2 | 1 | 4 | — | — |

## Esprit — 4 sorts

| Identifiant | Nom | Mode | Essence | Recharge | Niv. | Palier | Portée | Hostile | Tradition |
|---|---|---|---:|---:|---:|---:|---:|---|---|
| `spirit_lash` | Morsure d'Esprit | INSTANT | 11 | 85 | 0 | 1 | 10 | oui | — |
| `spirit_soothe` | Apaisement | INSTANT | 12 | 160 | 0 | 1 | 1 | — | — |
| `spirit_cleanse` | Purge d'Esprit | CAST_TIME | 18 | 220 | 1 | 1 | 1 | — | — |
| `spirit_ward` | Garde Spirituelle | INSTANT | 16 | 240 | 2 | 1 | 6 | — | — |

## Ombre — 7 sorts

| Identifiant | Nom | Mode | Essence | Recharge | Niv. | Palier | Portée | Hostile | Tradition |
|---|---|---|---:|---:|---:|---:|---:|---|---|
| `blood_rite` | Rite de Sang | INSTANT | 6 | 180 | 0 | 1 | 1 | — | Sang |
| `shadow_barb` | Dard d'Ombre | PROJECTILE | 10 | 90 | 0 | 1 | 20 | oui | — |
| `shadow_veil` | Voile d'Ombre | INSTANT | 16 | 280 | 0 | 1 | 1 | — | — |
| `shadow_step` | Pas d'Ombre | INSTANT | 12 | 200 | 1 | 2 | 1 | — | — |
| `blood_lance` | Lance de Sang | PROJECTILE | 16 | 80 | 2 | 2 | 20 | oui | Sang |
| `blood_surge` | Poussée Sanguine | INSTANT | 8 | 200 | 2 | 2 | 1 | — | Sang |
| `shadow_hex` | Hex d'Ombre | PROJECTILE | 16 | 70 | 2 | 2 | 20 | oui | — |

## Vide — 7 sorts

| Identifiant | Nom | Mode | Essence | Recharge | Niv. | Palier | Portée | Hostile | Tradition |
|---|---|---|---:|---:|---:|---:|---:|---|---|
| `time_haste` | Empressement | INSTANT | 12 | 160 | 0 | 1 | 1 | — | Chronos |
| `void_shard` | Eclat du Vide | PROJECTILE | 12 | 95 | 0 | 1 | 18 | oui | — |
| `void_ward` | Garde du Vide | INSTANT | 12 | 200 | 0 | 1 | 1 | — | — |
| `time_slow` | Ralentissement | PROJECTILE | 14 | 75 | 1 | 2 | 22 | oui | Chronos |
| `void_rift` | Faille | PROJECTILE | 16 | 80 | 1 | 2 | 22 | oui | — |
| `time_warp` | Distorsion | CAST_TIME | 18 | 240 | 2 | 2 | 1 | — | Chronos |
| `void_mute` | Mute du Vide | PROJECTILE | 18 | 90 | 2 | 2 | 20 | oui | — |

---

## Les sorts connus d'office

21 sorts sont appris dès le départ. Ce sont ceux qui apprennent le système.

| Identifiant | Nom | École |
|---|---|---|
| `arcane_sense` | Sens Arcanique | Arcane |
| `arcane_spark` | Etincelle d'Arcane | Arcane |
| `blood_rite` | Rite de Sang | Ombre |
| `earth_brace` | Ancrage de Roc | Roc |
| `earth_shard` | Eclat de Roc | Roc |
| `ember_fireball` | Boule de Feu | Braises |
| `ember_torch` | Torche de Braise | Braises |
| `frost_needle` | Aiguille de Givre | Givre |
| `frost_preserve` | Préserve de Givre | Givre |
| `light_glimmer` | Lueur d'Aurore | Aurore |
| `light_ray` | Rayon d'Aurore | Aurore |
| `nature_growth` | Pousse Sylvaine | Roc |
| `shadow_barb` | Dard d'Ombre | Ombre |
| `shadow_veil` | Voile d'Ombre | Ombre |
| `spirit_lash` | Morsure d'Esprit | Esprit |
| `spirit_soothe` | Apaisement | Esprit |
| `storm_breeze` | Brise de Tempête | Tempête |
| `storm_jolt` | Secousse de Tempete | Tempête |
| `time_haste` | Empressement | Vide |
| `void_shard` | Eclat du Vide | Vide |
| `void_ward` | Garde du Vide | Vide |

---

## Les traditions absorbées

Trois traditions ne sont plus des écoles. Leurs sorts gardent leur nom d'origine et
progressent dans l'école qui les a absorbées.

| Tradition | Absorbée par | Ce qui les rapproche |
|---|---|---|
| **Sylvanie** | Roc | Le même rapport à la terre |
| **Sang** | Ombre | Le sacrifice est une facette de la dissimulation |
| **Chronos** | Vide | Déformer le temps et déformer l'espace sont le même geste |

**Aucune règle de jeu ne connaît les traditions.** C'est de la mémoire.

---

## Les règles communes

| Règle | Valeur |
|---|---|
| Incantation de base | 100 ticks, soit 5 s |
| Réduction par niveau d'école | −4 % |
| Réduction par rang | −5 % |
| Plancher d'incantation | 20 ticks, soit 1 s |
| Pause commune après un sort | 10 ticks, soit 0,5 s |
| Remboursement à l'interruption | 50 % de l'Essence |
| Déplacement toléré pendant l'incantation | 0,5 bloc |
| Recharge minimale d'un sort hostile | 50 ticks |
| Réduction de coût par rang | −6 % (−24 % au rang V) |

---

## À lire ensuite

- [La magie](../04-jouer/magie.md) — le point de vue du joueur
- [Créer un sort](../06-creer-du-contenu/creer-un-sort.md) — la procédure
- [cdc_magie](../90-specifications/cdc_magie.md) — la spécification complète
- [Les fiches de sorts](../90-specifications/magie/sorts/) — une fiche par sort

