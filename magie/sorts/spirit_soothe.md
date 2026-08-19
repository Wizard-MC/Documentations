# Apaisement — `spirit_soothe`

> Léger soin personnel.

*Fiche générée par `MagicDocGenerator` depuis le catalogue et le profil VFX.*
*Les paramètres d'effet — dégâts, durées, rayons — ne sont pas recopiés ici :*
*ils font foi dans `WizardCore/src/main/resources/spells.yml`, entrée `spirit_soothe`.*

## Identité

| | |
|---|---|
| École | **Esprit** (`SPIRIT`) |
| Incantation | *Spiritus sano* |
| Mode | Instant |
| Couleur | `#A0E7E5` |
| Animation de baguette | `wand_raise` |
| Famille d'impact | `heal` |

## Gameplay

| | |
|---|---|
| Coût en Essence | 12 |
| Niveau d'école requis | 0 |
| Tier de baguette | 1 |
| Appris d'office | oui |

## Intention

Petit soin personnel, instantané. Le filet de sécurité de l'Esprit.

## Étapes VFX

Le sort se joue en étapes, chacune déclenchée par une phase annoncée par le
serveur. Voir [la conception des attaques](../conception/vfx_attaques.md).

| Étape | Phase serveur | Modèle | Animation | Durée | Ancre | Orientation | Taille |
|---|---|---|---|---|---|---|---|
| Cercle d'incantation | `CAST_START` / `CAST_STATE` | `incantation_circle.bbmodel` | `animation` | 1.5 s (bouclé) | WAND | FACE_CAST | 2 bloc(s) |
| Impact | `IMPACT` | `light_tear.bbmodel` | `skill` | 2.5 s | TARGET | WORLD | 1.8 bloc(s) |
| Aura | `CAST_START` | `shadow_wisps.bbmodel` | `orbit_1` | 1.25 s (bouclé) | CASTER_FEET | ENTITY_YAW | 2 bloc(s) |

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
| Voix d'incantation | `wizardmc.magic.voice.spirit_1..3` | *(aucun)* |
| Libération | `wizardmc.magic.release` | `random.bow` |
| Impact | `wizardmc.magic.impact_heal` | `random.levelup` |
| Interruption | `wizardmc.magic.interrupt` | `random.break` |

## Essai en jeu

```text
/magicvfx test spirit_soothe cast_start
/magicvfx test spirit_soothe impact
```

Ces commandes rejouent la présentation localement, sans lancer le sort ni
dépenser d'Essence.
