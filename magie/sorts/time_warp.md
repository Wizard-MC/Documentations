# Distorsion — `time_warp`

> Incantation : haste renforcée courte.

*Fiche générée par `MagicDocGenerator` depuis le catalogue et le profil VFX.*
*Les paramètres d'effet — dégâts, durées, rayons — ne sont pas recopiés ici :*
*ils font foi dans `WizardCore/src/main/resources/spells.yml`, entrée `time_warp`.*

## Identité

| | |
|---|---|
| École | **Chronos** (`TIME`) |
| Incantation | *Chronos flexus* |
| Mode | Incantation |
| Couleur | `#E8D5B7` |
| Animation de baguette | `wand_channel` |
| Famille d'impact | `glow` |

## Gameplay

| | |
|---|---|
| Coût en Essence | 18 |
| Niveau d'école requis | 2 |
| Tier de baguette | 2 |
| Temps d'incantation | 25 ticks (1.25 s) |
| Appris d'office | non — via le Grimoire |

## Intention

Incantation qui renforce brièvement la hâte. Récompense le fait de rester immobile un instant.

## Étapes VFX

Le sort se joue en étapes, chacune déclenchée par une phase annoncée par le
serveur. Voir [la conception des attaques](../conception/vfx_attaques.md).

| Étape | Phase serveur | Modèle | Animation | Durée | Ancre | Orientation | Taille |
|---|---|---|---|---|---|---|---|
| Cercle d'incantation | `CAST_START` / `CAST_STATE` | `incantation_circle.bbmodel` | `animation` | 1.5 s (bouclé) | WAND | FACE_CAST | 2 bloc(s) |
| Impact | `IMPACT` | `impact_generic.bbmodel` | `animation` | 0.45 s | TARGET | TOWARD_TARGET | 1.6 bloc(s) |

Les étapes absentes de ce tableau ne sont pas jouées pour ce sort.

### Rendu procédural

En complément du modèle, le runtime inscrit une couronne de runes et une lueur
centrale. Le tirage des glyphes et leur ordre d'apparition viennent de la graine
transmise par le serveur : tous les clients voient la même chose.

| | |
|---|---|
| Runes | 12 (SEQUENTIAL) |
| Rayon du cercle | 0.95 bloc(s) |
| Orientation | WAND_FACING |
| Distance de rendu | 72 blocs |

## Audio

| Moment | Son | Repli si l'asset manque |
|---|---|---|
| Début d'incantation | `wizardmc.magic.cast_start` | `random.orb` |
| Montée en puissance | `wizardmc.magic.charge_loop` | `portal.portal` |
| Voix d'incantation | `wizardmc.magic.voice.time_1..3` | *(aucun)* |
| Libération | `wizardmc.magic.release` | `random.bow` |
| Impact | `wizardmc.magic.impact_glow` | `random.levelup` |
| Interruption | `wizardmc.magic.interrupt` | `random.break` |

## Essai en jeu

```text
/magicvfx test time_warp cast_start
/magicvfx test time_warp impact
```

Ces commandes rejouent la présentation localement, sans lancer le sort ni
dépenser d'Essence.
