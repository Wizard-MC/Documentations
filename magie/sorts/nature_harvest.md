# Moisson Sylvaine — `nature_harvest`

> Bonus de récolte sur le prochain coup.

*Fiche générée par `MagicDocGenerator` depuis le catalogue et le profil VFX.*
*Les paramètres d'effet — dégâts, durées, rayons — ne sont pas recopiés ici :*
*ils font foi dans `WizardCore/src/main/resources/spells.yml`, entrée `nature_harvest`.*

## Identité

| | |
|---|---|
| École | **Roc** (`EARTH`) |
| Tradition | Sylvanie (`NATURE`) — école d'origine avant regroupement |
| Incantation | *Silva metit* |
| Mode | Instant |
| Couleur | `#6BCB77` |
| Animation de baguette | `wand_thrust` |
| Famille d'impact | `leaves` |

## Gameplay

| | |
|---|---|
| Coût en Essence | 10 |
| Niveau d'école requis | 1 |
| Tier de baguette | 1 |
| Appris d'office | non — via le Grimoire |

## Intention

Bonus ponctuel sur la prochaine récolte. Instantané parce qu'il s'insère dans un geste de jeu déjà rythmé.

## Étapes VFX

Le sort se joue en étapes, chacune déclenchée par une phase annoncée par le
serveur. Voir [la conception des attaques](../conception/vfx_attaques.md).

| Étape | Phase serveur | Modèle | Animation | Durée | Ancre | Orientation | Taille |
|---|---|---|---|---|---|---|---|
| Cercle d'incantation | `CAST_START` / `CAST_STATE` | `incantation_circle.bbmodel` | `animation` | 1.5 s (étirée sur l'incantation) | WAND | FACE_CAST | 2 bloc(s) |
| Impact | `IMPACT` | `earth_spike.bbmodel` | `emerge` | 1.5 s | TARGET | WORLD | 2.2 bloc(s) |
| Aura | `CAST_START` | `earth_ring.bbmodel` | `loop` | 3.2 s (bouclé) | CASTER_FEET | ENTITY_YAW | 2.8 bloc(s) |

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
| Voix d'incantation | `wizardmc.magic.voice.earth_1..3` | voix de l'école — la tradition n'a pas de prises |
| Libération | `wizardmc.magic.release` | `random.bow` |
| Impact | `wizardmc.magic.impact_leaves` | `dig.grass` |
| Interruption | `wizardmc.magic.interrupt` | `random.break` |

## Essai en jeu

```text
/magicvfx test nature_harvest cast_start
/magicvfx test nature_harvest impact
```

Ces commandes rejouent la présentation localement, sans lancer le sort ni
dépenser d'Essence.
