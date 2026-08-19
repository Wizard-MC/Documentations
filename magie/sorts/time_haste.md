# Empressement — `time_haste`

> Accélère brièvement vos mouvements.

*Fiche générée par `MagicDocGenerator` depuis le catalogue et le profil VFX.*
*Les paramètres d'effet — dégâts, durées, rayons — ne sont pas recopiés ici :*
*ils font foi dans `WizardCore/src/main/resources/spells.yml`, entrée `time_haste`.*

## Identité

| | |
|---|---|
| École | **Vide** (`VOID`) |
| Tradition | Chronos (`TIME`) — école d'origine avant regroupement |
| Incantation | *Chronos celer* |
| Mode | Instant |
| Couleur | `#E8D5B7` |
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

Accélération personnelle. La forme la plus simple de la manipulation du temps.

## Étapes VFX

Le sort se joue en étapes, chacune déclenchée par une phase annoncée par le
serveur. Voir [la conception des attaques](../conception/vfx_attaques.md).

| Étape | Phase serveur | Modèle | Animation | Durée | Ancre | Orientation | Taille |
|---|---|---|---|---|---|---|---|
| Cercle d'incantation | `CAST_START` / `CAST_STATE` | `incantation_circle.bbmodel` | `animation` | 1.5 s (étirée sur l'incantation) | WAND | FACE_CAST | 2 bloc(s) |
| Impact | `IMPACT` | `impact_critical.bbmodel` | `animation` | 0.45 s | TARGET | TOWARD_TARGET | 2 bloc(s) |
| Effet sur la cible | `IMPACT` + entité ciblée | `shadow_chains.bbmodel` | `slam` | 3.5 s | TARGET_ENTITY | WORLD | 3 bloc(s) |

Les étapes absentes de ce tableau ne sont pas jouées pour ce sort.

### Rendu procédural

En complément du modèle, le runtime inscrit une couronne de runes et une lueur
centrale. Le tirage des glyphes et leur ordre d'apparition viennent de la graine
transmise par le serveur : tous les clients voient la même chose.

| | |
|---|---|
| Runes | 6 (SIMULTANEOUS) |
| Rayon du cercle | 0.62 bloc(s) |
| Orientation | WAND_FACING |
| Distance de rendu | 72 blocs |

## Audio

| Moment | Son | Repli si l'asset manque |
|---|---|---|
| Début d'incantation | `wizardmc.magic.cast_start` | `random.orb` |
| Voix d'incantation | `wizardmc.magic.voice.void_1..3` | voix de l'école — la tradition n'a pas de prises |
| Libération | `wizardmc.magic.release` | `random.bow` |
| Impact | `wizardmc.magic.impact_glow` | `random.levelup` |
| Interruption | `wizardmc.magic.interrupt` | `random.break` |

## Essai en jeu

```text
/magicvfx test time_haste cast_start
/magicvfx test time_haste impact
```

Ces commandes rejouent la présentation localement, sans lancer le sort ni
dépenser d'Essence.
