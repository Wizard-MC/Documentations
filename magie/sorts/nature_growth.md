# Pousse Sylvaine — `nature_growth`

> Accélère la croissance des cultures proches.

*Fiche générée par `MagicDocGenerator` depuis le catalogue et le profil VFX.*
*Les paramètres d'effet — dégâts, durées, rayons — ne sont pas recopiés ici :*
*ils font foi dans `WizardCore/src/main/resources/spells.yml`, entrée `nature_growth`.*

## Identité

| | |
|---|---|
| École | **Sylvanie** (`NATURE`) |
| Incantation | *Silva crescit* |
| Mode | Incantation |
| Couleur | `#6BCB77` |
| Animation de baguette | `wand_thrust` |
| Famille d'impact | `leaves` |

## Gameplay

| | |
|---|---|
| Coût en Essence | 14 |
| Niveau d'école requis | 0 |
| Tier de baguette | 1 |
| Temps d'incantation | 30 ticks (1.5 s) |
| Appris d'office | oui |

## Intention

Sort de colon, pas de combattant : il accélère les cultures autour de soi. Le temps d'incantation évite d'en faire un clic répété sur un champ entier.

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
| Runes | 6 (SEQUENTIAL) |
| Rayon du cercle | 0.95 bloc(s) |
| Orientation | WAND_FACING |
| Distance de rendu | 72 blocs |

## Audio

| Moment | Son | Repli si l'asset manque |
|---|---|---|
| Début d'incantation | `wizardmc.magic.cast_start` | `random.orb` |
| Montée en puissance | `wizardmc.magic.charge_loop` | `portal.portal` |
| Voix d'incantation | `wizardmc.magic.voice.nature_1..3` | *(aucun)* |
| Libération | `wizardmc.magic.release` | `random.bow` |
| Impact | `wizardmc.magic.impact_leaves` | `dig.grass` |
| Interruption | `wizardmc.magic.interrupt` | `random.break` |

## Essai en jeu

```text
/magicvfx test nature_growth cast_start
/magicvfx test nature_growth impact
```

Ces commandes rejouent la présentation localement, sans lancer le sort ni
dépenser d'Essence.
