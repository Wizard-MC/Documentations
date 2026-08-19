# Geôle de Givre — `frost_prison`

> Fige la cible dans une gangue de glace — immobilisation, aucun dégât.

*Fiche générée par `MagicDocGenerator` depuis le catalogue et le profil VFX.*
*Les paramètres d'effet — dégâts, durées, rayons — ne sont pas recopiés ici :*
*ils font foi dans `WizardCore/src/main/resources/spells.yml`, entrée `frost_prison`.*

## Identité

| | |
|---|---|
| École | **Givre** (`FROST`) |
| Incantation | *Glacies carcer* |
| Mode | Incantation |
| Couleur | `#7EC8FF` |
| Animation de baguette | `wand_channel` |
| Famille d'impact | `ice` |

## Gameplay

| | |
|---|---|
| Coût en Essence | 26 |
| Niveau d'école requis | 3 |
| Tier de baguette | 2 |
| Temps d'incantation | 40 ticks (2 s) |
| Appris d'office | non — via le Grimoire |

## Intention

La pièce maîtresse du Givre. Deux secondes d'incantation visibles de loin, puis la cible est figée quatre secondes sans subir le moindre dégât. C'est un sort de mise en place — il crée une fenêtre pour ses alliés — et il se contre en sortant de la ligne de visée pendant l'incantation. La gangue de glace enveloppe réellement la victime à l'écran, ce qui rend l'état lisible pour tout le monde.

## Étapes VFX

Le sort se joue en étapes, chacune déclenchée par une phase annoncée par le
serveur. Voir [la conception des attaques](../conception/vfx_attaques.md).

| Étape | Phase serveur | Modèle | Animation | Durée | Ancre | Orientation | Taille |
|---|---|---|---|---|---|---|---|
| Cercle d'incantation | `CAST_START` / `CAST_STATE` | `incantation_circle.bbmodel` | `animation` | 1.5 s (bouclé) | WAND | FACE_CAST | 2 bloc(s) |
| Concentration | `CAST_START` | `frost_volley.bbmodel` | `ice_spike` | 2.2 s | CASTER_FEET | ENTITY_YAW | 3.5 bloc(s) |
| Impact | `IMPACT` | `impact_generic.bbmodel` | `animation` | 0.45 s | TARGET | TOWARD_TARGET | 1.8 bloc(s) |
| Effet sur la cible | `IMPACT` + entité ciblée | `frost_prison_cage.bbmodel` | `crystal_the_enemy` | 0.15 s (bouclé) | TARGET_ENTITY | WORLD | 1.9 bloc(s) |

Les étapes absentes de ce tableau ne sont pas jouées pour ce sort.

### Rendu procédural

En complément du modèle, le runtime inscrit une couronne de runes et une lueur
centrale. Le tirage des glyphes et leur ordre d'apparition viennent de la graine
transmise par le serveur : tous les clients voient la même chose.

| | |
|---|---|
| Runes | 6 (SEQUENTIAL) |
| Rayon du cercle | 0.95 bloc(s) |
| Orientation | WAND_FACING |
| Distance de rendu | 72 blocs |

## Audio

| Moment | Son | Repli si l'asset manque |
|---|---|---|
| Début d'incantation | `wizardmc.magic.cast_start` | `random.orb` |
| Montée en puissance | `wizardmc.magic.charge_loop` | `portal.portal` |
| Voix d'incantation | `wizardmc.magic.voice.frost_1..3` | *(aucun)* |
| Libération | `wizardmc.magic.release` | `random.bow` |
| Impact | `wizardmc.magic.impact_ice` | `random.glass` |
| Interruption | `wizardmc.magic.interrupt` | `random.break` |

## Essai en jeu

```text
/magicvfx test frost_prison cast_start
/magicvfx test frost_prison impact
```

Ces commandes rejouent la présentation localement, sans lancer le sort ni
dépenser d'Essence.
