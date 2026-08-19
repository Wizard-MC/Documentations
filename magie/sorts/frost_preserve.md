# Préserve de Givre — `frost_preserve`

> Zone de conservation (fonte / feu).

*Fiche générée par `MagicDocGenerator` depuis le catalogue et le profil VFX.*
*Les paramètres d'effet — dégâts, durées, rayons — ne sont pas recopiés ici :*
*ils font foi dans `WizardCore/src/main/resources/spells.yml`, entrée `frost_preserve`.*

## Identité

| | |
|---|---|
| École | **Givre** (`FROST`) |
| Incantation | *Glacies serva* |
| Mode | Instant |
| Couleur | `#7EC8FF` |
| Animation de baguette | `wand_thrust` |
| Famille d'impact | `ice` |

## Gameplay

| | |
|---|---|
| Coût en Essence | 12 |
| Niveau d'école requis | 0 |
| Tier de baguette | 1 |
| Appris d'office | oui |

## Intention

Zone qui étouffe le feu et la fonte. Sort de logistique et de défense de base, jamais offensif.

## Étapes VFX

Le sort se joue en étapes, chacune déclenchée par une phase annoncée par le
serveur. Voir [la conception des attaques](../conception/vfx_attaques.md).

| Étape | Phase serveur | Modèle | Animation | Durée | Ancre | Orientation | Taille |
|---|---|---|---|---|---|---|---|
| Cercle d'incantation | `CAST_START` / `CAST_STATE` | `incantation_circle.bbmodel` | `animation` | 1.5 s (bouclé) | WAND | FACE_CAST | 2 bloc(s) |
| Impact | `IMPACT` | `impact_generic.bbmodel` | `animation` | 0.45 s | TARGET | TOWARD_TARGET | 1.8 bloc(s) |
| Effet sur la cible | `IMPACT` + entité ciblée | `frost_prison_cage.bbmodel` | `crystal_the_enemy` | 0.15 s (bouclé) | TARGET_ENTITY | WORLD | 1.9 bloc(s) |

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
| Voix d'incantation | `wizardmc.magic.voice.frost_1..3` | *(aucun)* |
| Libération | `wizardmc.magic.release` | `random.bow` |
| Impact | `wizardmc.magic.impact_ice` | `random.glass` |
| Interruption | `wizardmc.magic.interrupt` | `random.break` |

## Essai en jeu

```text
/magicvfx test frost_preserve cast_start
/magicvfx test frost_preserve impact
```

Ces commandes rejouent la présentation localement, sans lancer le sort ni
dépenser d'Essence.
