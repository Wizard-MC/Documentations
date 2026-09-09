# Éclair d'Arcane — `arcane_bolt`

> Projectile de dégâts soft (plafonné).

*Fiche générée par `MagicDocGenerator` depuis le catalogue et le profil VFX.*
*Les paramètres d'effet — dégâts, durées, rayons — ne sont pas recopiés ici :*
*ils font foi dans `WizardCore/src/main/resources/spells.yml`, entrée `arcane_bolt`.*

## Identité

| | |
|---|---|
| École | **Arcane** (`ARCANE`) |
| Incantation | *Arcanum ictus* |
| Mode | Projectile |
| Couleur | `#D4A5FF` |
| Animation de baguette | `wand_thrust` |
| Famille d'impact | `spark` |

## Gameplay

| | |
|---|---|
| Coût en Essence | 14 |
| Niveau d'école requis | 1 |
| Tier de baguette | 1 |
| Appris d'office | non — via le Grimoire |

## Intention

Le projectile d'entrée de l'Arcane : le sort auquel on compare tous les autres. Dégâts plafonnés, trajectoire lisible, aucun effet annexe.

## Étapes VFX

Le sort se joue en étapes, chacune déclenchée par une phase annoncée par le
serveur. Voir [la conception des attaques](../conception/vfx_attaques.md).

| Étape | Phase serveur | Modèle | Animation | Durée | Ancre | Orientation | Taille |
|---|---|---|---|---|---|---|---|
| Cercle d'incantation | `CAST_START` / `CAST_STATE` | `incantation_circle.bbmodel` | `animation` | 1.5 s (étirée sur l'incantation) | WAND | FACE_CAST | 2 bloc(s) |
| Projectile | `PROJECTILE` | `storm_orb.bbmodel` | `animation` | 0.5 s (bouclé) | ORIGIN | TOWARD_TARGET | 0.75 bloc(s) |
| Impact | `IMPACT` | `impact_bolt.bbmodel` | `animation` | 0.9 s | TARGET | TOWARD_TARGET | 2.4 bloc(s) |

Les étapes absentes de ce tableau ne sont pas jouées pour ce sort.

### Rendu procédural

En complément du modèle, le runtime inscrit une couronne de runes et une lueur
centrale. Le tirage des glyphes et leur ordre d'apparition viennent de la graine
transmise par le serveur : tous les clients voient la même chose.

| | |
|---|---|
| Runes | 4 (RANDOM) |
| Rayon du cercle | 0.55 bloc(s) |
| Orientation | WAND_FACING |
| Distance de rendu | 96 blocs |

## Audio

| Moment | Son | Repli si l'asset manque |
|---|---|---|
| Début d'incantation | `wizardmc.magic.cast_start` | `random.orb` |
| Voix d'incantation | `wizardmc.magic.voice.arcane_1..3` | *(aucun)* |
| Départ du projectile | `wizardmc.magic.projectile_launch` | `random.bow` |
| Impact | `wizardmc.magic.impact_spark` | `random.orb` |
| Interruption | `wizardmc.magic.interrupt` | `random.break` |

## Essai en jeu

```text
/magicvfx test arcane_bolt cast_start
/magicvfx test arcane_bolt impact
```

Ces commandes rejouent la présentation localement, sans lancer le sort ni
dépenser d'Essence.
