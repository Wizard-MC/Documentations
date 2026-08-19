# Torche de Braise — `ember_torch`

> Projectile plaçant une lumière / petit feu contrôlé.

*Fiche générée par `MagicDocGenerator` depuis le catalogue et le profil VFX.*
*Les paramètres d'effet — dégâts, durées, rayons — ne sont pas recopiés ici :*
*ils font foi dans `WizardCore/src/main/resources/spells.yml`, entrée `ember_torch`.*

## Identité

| | |
|---|---|
| École | **Braises** (`FIRE`) |
| Incantation | *Ignis fax* |
| Mode | Projectile |
| Couleur | `#FF6A3D` |
| Animation de baguette | `wand_thrust` |
| Famille d'impact | `flame` |

## Gameplay

| | |
|---|---|
| Coût en Essence | 10 |
| Niveau d'école requis | 0 |
| Tier de baguette | 1 |
| Appris d'office | oui |

## Intention

Projectile utilitaire qui pose une lumière. Le premier sort de Braises, pensé pour l'exploration avant le combat.

## Étapes VFX

Le sort se joue en étapes, chacune déclenchée par une phase annoncée par le
serveur. Voir [la conception des attaques](../conception/vfx_attaques.md).

| Étape | Phase serveur | Modèle | Animation | Durée | Ancre | Orientation | Taille |
|---|---|---|---|---|---|---|---|
| Cercle d'incantation | `CAST_START` / `CAST_STATE` | `incantation_circle.bbmodel` | `animation` | 1.5 s (bouclé) | WAND | FACE_CAST | 2 bloc(s) |
| Projectile | `PROJECTILE` | `projectile_fireball.bbmodel` | `animation` | 0.25 s (bouclé) | ORIGIN | TOWARD_TARGET | 0.9 bloc(s) |
| Impact | `IMPACT` | `impact_burst.bbmodel` | `animation` | 0.75 s | TARGET | WORLD | 3 bloc(s) |
| Aura | `CAST_START` | `fire_ground_crack.bbmodel` | `spawn` | 3 s | TARGET | WORLD | 4 bloc(s) |

Les étapes absentes de ce tableau ne sont pas jouées pour ce sort.

### Rendu procédural

En complément du modèle, le runtime inscrit une couronne de runes et une lueur
centrale. Le tirage des glyphes et leur ordre d'apparition viennent de la graine
transmise par le serveur : tous les clients voient la même chose.

| | |
|---|---|
| Runes | 3 (RANDOM) |
| Rayon du cercle | 0.55 bloc(s) |
| Orientation | WAND_FACING |
| Distance de rendu | 96 blocs |

## Audio

| Moment | Son | Repli si l'asset manque |
|---|---|---|
| Début d'incantation | `wizardmc.magic.cast_start` | `random.orb` |
| Voix d'incantation | `wizardmc.magic.voice.fire_1..3` | *(aucun)* |
| Départ du projectile | `wizardmc.magic.projectile_launch` | `random.bow` |
| Impact | `wizardmc.magic.impact_flame` | `fire.ignite` |
| Interruption | `wizardmc.magic.interrupt` | `random.break` |

## Essai en jeu

```text
/magicvfx test ember_torch cast_start
/magicvfx test ember_torch impact
```

Ces commandes rejouent la présentation localement, sans lancer le sort ni
dépenser d'Essence.
