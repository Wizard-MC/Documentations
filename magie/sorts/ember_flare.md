# Embrasement — `ember_flare`

> Cône de braise — dégâts soft PvE/PvP plafonnés.

*Fiche générée par `MagicDocGenerator` depuis le catalogue et le profil VFX.*
*Les paramètres d'effet — dégâts, durées, rayons — ne sont pas recopiés ici :*
*ils font foi dans `WizardCore/src/main/resources/spells.yml`, entrée `ember_flare`.*

## Identité

| | |
|---|---|
| École | **Braises** (`FIRE`) |
| Incantation | *Ignis flamma* |
| Mode | Incantation |
| Couleur | `#FF6A3D` |
| Animation de baguette | `wand_channel` |
| Famille d'impact | `flame` |

## Gameplay

| | |
|---|---|
| Coût en Essence | 22 |
| Niveau d'école requis | 2 |
| Tier de baguette | 2 |
| Temps d'incantation | 24 ticks (1.2 s) |
| Appris d'office | non — via le Grimoire |

## Intention

Cône de braise en incantation : le sort d'aire de la Braise. La concentration devant la baguette prévient les cibles de ce qui arrive.

## Étapes VFX

Le sort se joue en étapes, chacune déclenchée par une phase annoncée par le
serveur. Voir [la conception des attaques](../conception/vfx_attaques.md).

| Étape | Phase serveur | Modèle | Animation | Durée | Ancre | Orientation | Taille |
|---|---|---|---|---|---|---|---|
| Cercle d'incantation | `CAST_START` / `CAST_STATE` | `incantation_circle.bbmodel` | `animation` | 1.5 s (bouclé) | WAND | FACE_CAST | 2 bloc(s) |
| Concentration | `CAST_START` | `fire_charge.bbmodel` | `animation` | 2.8 s | WAND | FACE_CAST | 2.4 bloc(s) |
| Impact | `IMPACT` | `impact_burst.bbmodel` | `animation` | 0.75 s | TARGET | WORLD | 3 bloc(s) |
| Aura | `CAST_START` | `fire_ground_crack.bbmodel` | `spawn` | 3 s | TARGET | WORLD | 4 bloc(s) |

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
| Voix d'incantation | `wizardmc.magic.voice.fire_1..3` | *(aucun)* |
| Libération | `wizardmc.magic.release` | `random.bow` |
| Impact | `wizardmc.magic.impact_flame` | `fire.ignite` |
| Interruption | `wizardmc.magic.interrupt` | `random.break` |

## Essai en jeu

```text
/magicvfx test ember_flare cast_start
/magicvfx test ember_flare impact
```

Ces commandes rejouent la présentation localement, sans lancer le sort ni
dépenser d'Essence.
