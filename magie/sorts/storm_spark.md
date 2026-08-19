# Étincelle de Foudre — `storm_spark`

> Projectile de foudre douce (plafonné).

*Fiche générée par `MagicDocGenerator` depuis le catalogue et le profil VFX.*
*Les paramètres d'effet — dégâts, durées, rayons — ne sont pas recopiés ici :*
*ils font foi dans `WizardCore/src/main/resources/spells.yml`, entrée `storm_spark`.*

## Identité

| | |
|---|---|
| École | **Tempête** (`STORM`) |
| Incantation | *Tempestas scintilla* |
| Mode | Projectile |
| Couleur | `#B8A0FF` |
| Animation de baguette | `wand_thrust` |
| Famille d'impact | `spark` |

## Gameplay

| | |
|---|---|
| Coût en Essence | 14 |
| Niveau d'école requis | 1 |
| Tier de baguette | 2 |
| Appris d'office | non — via le Grimoire |

## Intention

Projectile de foudre : la version Tempête de l'éclair d'Arcane, plus vif et plus bruyant.

## Étapes VFX

Le sort se joue en étapes, chacune déclenchée par une phase annoncée par le
serveur. Voir [la conception des attaques](../conception/vfx_attaques.md).

| Étape | Phase serveur | Modèle | Animation | Durée | Ancre | Orientation | Taille |
|---|---|---|---|---|---|---|---|
| Cercle d'incantation | `CAST_START` / `CAST_STATE` | `incantation_circle.bbmodel` | `animation` | 1.5 s (bouclé) | WAND | FACE_CAST | 2 bloc(s) |
| Projectile | `PROJECTILE` | `storm_orb.bbmodel` | `animation` | 0.5 s (bouclé) | ORIGIN | TOWARD_TARGET | 0.8 bloc(s) |
| Impact | `IMPACT` | `storm_impact.bbmodel` | `animation` | 0.75 s | TARGET | WORLD | 3.4 bloc(s) |
| Effet sur la cible | `IMPACT` + entité ciblée | `storm_strike.bbmodel` | `animation` | 0.75 s | TARGET_ENTITY | WORLD | 2.6 bloc(s) |

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
| Voix d'incantation | `wizardmc.magic.voice.storm_1..3` | *(aucun)* |
| Départ du projectile | `wizardmc.magic.projectile_launch` | `random.bow` |
| Impact | `wizardmc.magic.impact_spark` | `random.orb` |
| Interruption | `wizardmc.magic.interrupt` | `random.break` |

## Essai en jeu

```text
/magicvfx test storm_spark cast_start
/magicvfx test storm_spark impact
```

Ces commandes rejouent la présentation localement, sans lancer le sort ni
dépenser d'Essence.
