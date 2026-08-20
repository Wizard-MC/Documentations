# Voile d'Ombre — `shadow_veil`

> Invisibilité courte — furtivité soft.

*Fiche générée par `MagicDocGenerator` depuis le catalogue et le profil VFX.*
*Les paramètres d'effet — dégâts, durées, rayons — ne sont pas recopiés ici :*
*ils font foi dans `WizardCore/src/main/resources/spells.yml`, entrée `shadow_veil`.*

## Identité

| | |
|---|---|
| École | **Ombre** (`SHADOW`) |
| Incantation | *Umbra velum* |
| Mode | Instant |
| Couleur | `#6B5B95` |
| Animation de baguette | `wand_raise` |
| Famille d'impact | `smoke` |

## Gameplay

| | |
|---|---|
| Coût en Essence | 16 |
| Niveau d'école requis | 0 |
| Tier de baguette | 1 |
| Appris d'office | oui |

## Intention

Invisibilité brève. Le voile d'ombre qui enveloppe le lanceur est visible au moment du lancement : disparaître ne doit jamais être silencieux.

## Étapes VFX

Le sort se joue en étapes, chacune déclenchée par une phase annoncée par le
serveur. Voir [la conception des attaques](../conception/vfx_attaques.md).

| Étape | Phase serveur | Modèle | Animation | Durée | Ancre | Orientation | Taille |
|---|---|---|---|---|---|---|---|
| Cercle d'incantation | `CAST_START` / `CAST_STATE` | `incantation_circle.bbmodel` | `animation` | 1.5 s (étirée sur l'incantation) | WAND | FACE_CAST | 2 bloc(s) |
| Effet sur la cible | `IMPACT` + entité ciblée | `shadow_chains.bbmodel` | `slam` | 3.5 s | TARGET_ENTITY | WORLD | 3 bloc(s) |
| Aura | `AURA` | `shadow_veil.bbmodel` | `spawn` | 6.85 s | CASTER_FEET | ENTITY_YAW | 3 bloc(s) |

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
| Voix d'incantation | `wizardmc.magic.voice.shadow_1..3` | *(aucun)* |
| Libération | `wizardmc.magic.release` | `random.bow` |
| Impact | `wizardmc.magic.impact_smoke` | `random.fizz` |
| Interruption | `wizardmc.magic.interrupt` | `random.break` |

## Essai en jeu

```text
/magicvfx test shadow_veil cast_start
/magicvfx test shadow_veil impact
```

Ces commandes rejouent la présentation localement, sans lancer le sort ni
dépenser d'Essence.
