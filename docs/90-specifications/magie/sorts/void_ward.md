# Garde du Vide — `void_ward`

> Résistance légère face aux coups.

*Fiche générée par `MagicDocGenerator` depuis le catalogue et le profil VFX.*
*Les paramètres d'effet — dégâts, durées, rayons — ne sont pas recopiés ici :*
*ils font foi dans `WizardCore/src/main/resources/spells.yml`, entrée `void_ward`.*

## Identité

| | |
|---|---|
| École | **Vide** (`VOID`) |
| Incantation | *Vacuum tutela* |
| Mode | Instant |
| Couleur | `#2D2A32` |
| Animation de baguette | `wand_raise` |
| Famille d'impact | `glow` |

## Gameplay

| | |
|---|---|
| Coût en Essence | 12 |
| Niveau d'école requis | 0 |
| Tier de baguette | 1 |
| Appris d'office | oui |

## Intention

Résistance face aux coups. La défense du Vide est passive et brève.

## Étapes VFX

Le sort se joue en étapes, chacune déclenchée par une phase annoncée par le
serveur. Voir [la conception des attaques](../conception/vfx_attaques.md).

| Étape | Phase serveur | Modèle | Animation | Durée | Ancre | Orientation | Taille |
|---|---|---|---|---|---|---|---|
| Cercle d'incantation | `CAST_START` / `CAST_STATE` | `incantation_circle.bbmodel` | `animation` | 1.5 s (étirée sur l'incantation) | WAND | FACE_CAST | 2 bloc(s) |
| Effet sur la cible | `IMPACT` + entité ciblée | `shadow_chains.bbmodel` | `slam` | 3.5 s | TARGET_ENTITY | WORLD | 3 bloc(s) |
| Aura | `AURA` | `circle_ground_void.bbmodel` | `idle` | 3 s (bouclé) | CASTER_FEET | ENTITY_YAW | 2.6 bloc(s) |

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
| Voix d'incantation | `wizardmc.magic.voice.void_1..3` | *(aucun)* |
| Libération | `wizardmc.magic.release` | `random.bow` |
| Impact | `wizardmc.magic.impact_glow` | `random.levelup` |
| Interruption | `wizardmc.magic.interrupt` | `random.break` |

## Essai en jeu

```text
/magicvfx test void_ward cast_start
/magicvfx test void_ward impact
```

Ces commandes rejouent la présentation localement, sans lancer le sort ni
dépenser d'Essence.
