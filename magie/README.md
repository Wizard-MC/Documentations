# Système de Magie — documentation

Documentation complète du système de magie WizardMC : règles, écoles, sorts,
et conception des effets visuels.

*Les fiches d'écoles et de sorts sont générées par `MagicDocGenerator`
(dépôt MCP, `tests/fr/wizardmc/magic/vfx/test/`) à partir du catalogue et des
profils VFX réellement exécutés. Les régénérer après toute modification.*

## Documents de référence

| Document | Rôle |
|---|---|
| [`../cdc_magie.md`](../cdc_magie.md) | Cahier des charges du système |
| [`conception/vfx_attaques.md`](conception/vfx_attaques.md) | Conception des VFX d'attaque : étapes, ancres, orientations |
| [`conception/bbmodel_attaque.md`](conception/bbmodel_attaque.md) | Concevoir le bbmodel d'une attaque, de Blockbench au jeu |

## Écoles

| École | Sorts | Fiche |
|---|---|---|
| **Aurore** | 2 | [`light`](ecoles/light.md) |
| **Sylvanie** | 2 | [`nature`](ecoles/nature.md) |
| **Roc** | 2 | [`earth`](ecoles/earth.md) |
| **Arcane** | 2 | [`arcane`](ecoles/arcane.md) |
| **Givre** | 3 | [`frost`](ecoles/frost.md) |
| **Braises** | 2 | [`fire`](ecoles/fire.md) |
| **Tempête** | 3 | [`storm`](ecoles/storm.md) |
| **Ombre** | 3 | [`shadow`](ecoles/shadow.md) |
| **Esprit** | 3 | [`spirit`](ecoles/spirit.md) |
| **Sang** | 3 | [`blood`](ecoles/blood.md) |
| **Chronos** | 3 | [`time`](ecoles/time.md) |
| **Vide** | 3 | [`void`](ecoles/void.md) |

## Sorts

| Sort | École | Mode | Fiche |
|---|---|---|---|
| Lueur d'Aurore | Aurore | Instant | [`light_glimmer`](sorts/light_glimmer.md) |
| Suture d'Aurore | Aurore | Incantation | [`light_mend`](sorts/light_mend.md) |
| Pousse Sylvaine | Sylvanie | Incantation | [`nature_growth`](sorts/nature_growth.md) |
| Moisson Sylvaine | Sylvanie | Instant | [`nature_harvest`](sorts/nature_harvest.md) |
| Soulèvement de Roc | Roc | Canal | [`earth_lift`](sorts/earth_lift.md) |
| Ancrage de Roc | Roc | Instant | [`earth_brace`](sorts/earth_brace.md) |
| Sens Arcanique | Arcane | Instant | [`arcane_sense`](sorts/arcane_sense.md) |
| Éclair d'Arcane | Arcane | Projectile | [`arcane_bolt`](sorts/arcane_bolt.md) |
| Préserve de Givre | Givre | Instant | [`frost_preserve`](sorts/frost_preserve.md) |
| Éclat de Givre | Givre | Projectile | [`frost_shard`](sorts/frost_shard.md) |
| Geôle de Givre | Givre | Incantation | [`frost_prison`](sorts/frost_prison.md) |
| Torche de Braise | Braises | Projectile | [`ember_torch`](sorts/ember_torch.md) |
| Embrasement | Braises | Incantation | [`ember_flare`](sorts/ember_flare.md) |
| Brise de Tempête | Tempête | Instant | [`storm_breeze`](sorts/storm_breeze.md) |
| Rafale | Tempête | Instant | [`storm_gust`](sorts/storm_gust.md) |
| Étincelle de Foudre | Tempête | Projectile | [`storm_spark`](sorts/storm_spark.md) |
| Voile d'Ombre | Ombre | Instant | [`shadow_veil`](sorts/shadow_veil.md) |
| Pas d'Ombre | Ombre | Instant | [`shadow_step`](sorts/shadow_step.md) |
| Hex d'Ombre | Ombre | Projectile | [`shadow_hex`](sorts/shadow_hex.md) |
| Apaisement | Esprit | Instant | [`spirit_soothe`](sorts/spirit_soothe.md) |
| Purge d'Esprit | Esprit | Incantation | [`spirit_cleanse`](sorts/spirit_cleanse.md) |
| Garde Spirituelle | Esprit | Instant | [`spirit_ward`](sorts/spirit_ward.md) |
| Rite de Sang | Sang | Instant | [`blood_rite`](sorts/blood_rite.md) |
| Poussée Sanguine | Sang | Instant | [`blood_surge`](sorts/blood_surge.md) |
| Lance de Sang | Sang | Projectile | [`blood_lance`](sorts/blood_lance.md) |
| Empressement | Chronos | Instant | [`time_haste`](sorts/time_haste.md) |
| Ralentissement | Chronos | Projectile | [`time_slow`](sorts/time_slow.md) |
| Distorsion | Chronos | Incantation | [`time_warp`](sorts/time_warp.md) |
| Garde du Vide | Vide | Instant | [`void_ward`](sorts/void_ward.md) |
| Mute du Vide | Vide | Projectile | [`void_mute`](sorts/void_mute.md) |
| Faille | Vide | Projectile | [`void_rift`](sorts/void_rift.md) |
