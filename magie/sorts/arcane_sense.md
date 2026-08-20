# Sens Arcanique — `arcane_sense`

> Révèle brièvement les minerais proches.

*Fiche générée par `MagicDocGenerator` depuis le catalogue et le profil VFX.*
*Les paramètres d'effet — dégâts, durées, rayons — ne sont pas recopiés ici :*
*ils font foi dans `WizardCore/src/main/resources/spells.yml`, entrée `arcane_sense`.*

## Identité

| | |
|---|---|
| École | **Arcane** (`ARCANE`) |
| Incantation | *Arcanum cerno* |
| Mode | Instant |
| Couleur | `#D4A5FF` |
| Animation de baguette | `wand_raise` |
| Famille d'impact | `spark` |

## Gameplay

| | |
|---|---|
| Coût en Essence | 18 |
| Niveau d'école requis | 0 |
| Tier de baguette | 1 |
| Appris d'office | oui |

## Intention

Révèle brièvement les minerais proches. Long cooldown pour rester un complément d'exploration et non un remplacement du minage.

## Étapes VFX

Le sort se joue en étapes, chacune déclenchée par une phase annoncée par le
serveur. Voir [la conception des attaques](../conception/vfx_attaques.md).

| Étape | Phase serveur | Modèle | Animation | Durée | Ancre | Orientation | Taille |
|---|---|---|---|---|---|---|---|
| Cercle d'incantation | `CAST_START` / `CAST_STATE` | `incantation_circle.bbmodel` | `animation` | 1.5 s (étirée sur l'incantation) | WAND | FACE_CAST | 2 bloc(s) |
| Impact | `IMPACT` | `impact_bolt.bbmodel` | `animation` | 0.9 s | TARGET | TOWARD_TARGET | 2.4 bloc(s) |

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
| Voix d'incantation | `wizardmc.magic.voice.arcane_1..3` | *(aucun)* |
| Libération | `wizardmc.magic.release` | `random.bow` |
| Impact | `wizardmc.magic.impact_spark` | `random.orb` |
| Interruption | `wizardmc.magic.interrupt` | `random.break` |

## Essai en jeu

```text
/magicvfx test arcane_sense cast_start
/magicvfx test arcane_sense impact
```

Ces commandes rejouent la présentation localement, sans lancer le sort ni
dépenser d'Essence.
