# Rafale — `storm_gust`

> Pousse les cibles devant vous (soft).

*Fiche générée par `MagicDocGenerator` depuis le catalogue et le profil VFX.*
*Les paramètres d'effet — dégâts, durées, rayons — ne sont pas recopiés ici :*
*ils font foi dans `WizardCore/src/main/resources/spells.yml`, entrée `storm_gust`.*

## Identité

| | |
|---|---|
| École | **Tempête** (`STORM`) |
| Incantation | *Tempestas flatus* |
| Mode | Instant |
| Couleur | `#B8A0FF` |
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

Repousse ce qui se trouve devant. Outil de désengagement, pas de dégâts.

## Étapes VFX

Le sort se joue en étapes, chacune déclenchée par une phase annoncée par le
serveur. Voir [la conception des attaques](../conception/vfx_attaques.md).

| Étape | Phase serveur | Modèle | Animation | Durée | Ancre | Orientation | Taille |
|---|---|---|---|---|---|---|---|
| Cercle d'incantation | `CAST_START` / `CAST_STATE` | `incantation_circle.bbmodel` | `animation` | 1.5 s (étirée sur l'incantation) | WAND | FACE_CAST | 2 bloc(s) |
| Impact | `IMPACT` | `storm_impact.bbmodel` | `animation` | 0.75 s | TARGET | WORLD | 3.4 bloc(s) |
| Aura | `AURA` | `storm_orb.bbmodel` | `animation` | 0.5 s (bouclé) | CASTER_FEET | ENTITY_YAW | 1.5 bloc(s) |

Les étapes absentes de ce tableau ne sont pas jouées pour ce sort.

### Rendu procédural

En complément du modèle, le runtime inscrit une couronne de runes et une lueur
centrale. Le tirage des glyphes et leur ordre d'apparition viennent de la graine
transmise par le serveur : tous les clients voient la même chose.

| | |
|---|---|
| Runes | 4 (SIMULTANEOUS) |
| Rayon du cercle | 0.62 bloc(s) |
| Orientation | WAND_FACING |
| Distance de rendu | 72 blocs |

## Audio

| Moment | Son | Repli si l'asset manque |
|---|---|---|
| Début d'incantation | `wizardmc.magic.cast_start` | `random.orb` |
| Voix d'incantation | `wizardmc.magic.voice.storm_1..3` | *(aucun)* |
| Libération | `wizardmc.magic.release` | `random.bow` |
| Impact | `wizardmc.magic.impact_spark` | `random.orb` |
| Interruption | `wizardmc.magic.interrupt` | `random.break` |

## Essai en jeu

```text
/magicvfx test storm_gust cast_start
/magicvfx test storm_gust impact
```

Ces commandes rejouent la présentation localement, sans lancer le sort ni
dépenser d'Essence.
