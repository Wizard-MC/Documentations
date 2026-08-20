# Torche de Braise — `ember_torch`

> Une lanterne de braise flotte à vos côtés et éclaire vos pas.

*Fiche générée par `MagicDocGenerator` depuis le catalogue et le profil VFX.*
*Les paramètres d'effet — dégâts, durées, rayons — ne sont pas recopiés ici :*
*ils font foi dans `WizardCore/src/main/resources/spells.yml`, entrée `ember_torch`.*

## Identité

| | |
|---|---|
| École | **Braises** (`FIRE`) |
| Incantation | *Ignis fax* |
| Mode | Instantané |
| Couleur | `#FFB347` |
| Animation de baguette | `wand_raise` |
| Famille d'impact | `flame` |

## Gameplay

| | |
|---|---|
| Coût en Essence | 10 |
| Niveau d'école requis | 0 |
| Tier de baguette | 1 |
| Appris d'office | oui |

## Intention

Une lanterne de braise flotte à hauteur d'épaule et suit le mage. Elle éclaire trois à quatre blocs autour de lui — assez pour marcher, pas assez pour transformer la nuit en jour. Elle posait autrefois un bloc de lumière, ce qui obligeait à s'arrêter et laissait des torches partout ; portée, elle devient le sort qu'on garde allumé en explorant. Un rang la fait durer plus longtemps et porter un peu plus loin.

## Étapes VFX

Le sort se joue en étapes, chacune déclenchée par une phase annoncée par le
serveur. Voir [la conception des attaques](../conception/vfx_attaques.md).

| Étape | Phase serveur | Modèle | Animation | Durée | Ancre | Orientation | Taille |
|---|---|---|---|---|---|---|---|
| Cercle d'incantation | `CAST_START` / `CAST_STATE` | `incantation_circle.bbmodel` | `animation` | 1.5 s (étirée sur l'incantation) | WAND | FACE_CAST | 2 bloc(s) |
| Aura | `AURA` | `carried_lantern.bbmodel` | `idle` | 4 s (bouclé) | CASTER_FEET | ENTITY_YAW | 0.7 bloc(s) |

Les étapes absentes de ce tableau ne sont pas jouées pour ce sort.

### Rendu procédural

En complément du modèle, le runtime inscrit une couronne de runes et une lueur
centrale. Le tirage des glyphes et leur ordre d'apparition viennent de la graine
transmise par le serveur : tous les clients voient la même chose.

| | |
|---|---|
| Runes | 3 (SIMULTANEOUS) |
| Rayon du cercle | 0.62 bloc(s) |
| Orientation | WAND_FACING |
| Distance de rendu | 72 blocs |

## Audio

| Moment | Son | Repli si l'asset manque |
|---|---|---|
| Début d'incantation | `wizardmc.magic.cast_start` | `random.orb` |
| Voix d'incantation | `wizardmc.magic.voice.fire_1..3` | *(aucun)* |
| Libération | `wizardmc.magic.release` | `random.bow` |
| Impact | `wizardmc.magic.impact_flame` | `fire.ignite` |
| Interruption | `wizardmc.magic.interrupt` | `random.break` |

## Essai en jeu

```text
/magicvfx test ember_torch cast_start
/magicvfx test ember_torch impact
```

Ces commandes rejouent la présentation localement, sans lancer le sort ni
dépenser d'Essence.
