# WizardMC — Docs « Coven SMP » (V4)

> **Source de vérité design** pour le positionnement **SMP Semi-RPG**.  
> Les documents Faction V3 restent dans le dossier parent (`../`) comme **archive**.

## Vision

WizardMC – *Les Terres Fracturées* : les joueurs forment des **Covens**, développent des territoires, commerçent, diplomatisent et influencent l’histoire. Le PvP existe, mais construction, économie et narration comptent autant.

## Index

| Document | Rôle |
| :--- | :--- |
| [gdd.md](gdd.md) | GDD global Coven SMP |
| [ROADMAP.md](ROADMAP.md) | **Plan d’exécution V4** (phases S0–S7) |
| [cdc_covens.md](cdc_covens.md) | **Plugin Covens** (remplace MassiveCraft Factions) |
| [cdc_nexus.md](cdc_nexus.md) | Nexus = cœur de ville + progression |
| [cdc_autels_sacres.md](cdc_autels_sacres.md) | Autels typés + drama diplomatique |
| [cdc_mana_brut.md](cdc_mana_brut.md) | Mana Brut = ressource stratégique / convois |
| [cdc_pouvoirs.md](cdc_pouvoirs.md) | Pouvoirs actifs du Coven |
| [cdc_eres.md](cdc_eres.md) | Ères (ex-saisons) 45 jours |
| [cdc_exp_client.md](cdc_exp_client.md) | Client MCP / HUD / FX |
| [cdc_boutique.md](cdc_boutique.md) | Monétisation zéro P2W |
| [cdc_magie.md](cdc_magie.md) | Stub Magie (12 écoles + utilitaires) |
| [cdc_compagnons.md](cdc_compagnons.md) | Stub Compagnons SMP |
| [cdc_spawn_map.md](cdc_spawn_map.md) | Stub Spawn & map |

## Archive Faction V3

Documents historiques (ne plus étendre) :

- [`../gdd.md`](../gdd.md)
- [`../cdc_nexus.md`](../cdc_nexus.md), [`../cdc_autels_sacres.md`](../cdc_autels_sacres.md), [`../cdc_mana_brut.md`](../cdc_mana_brut.md)
- [`../cdc_evolution_du_nexus.md`](../cdc_evolution_du_nexus.md), [`../cdc_cycle_de_vie.md`](../cdc_cycle_de_vie.md)
- [`../cdc_exp_client.md`](../cdc_exp_client.md), [`../cdc_boutique_monetisation.md`](../cdc_boutique_monetisation.md)
- [`../archive/ROADMAP_V3_FACTION.md`](../archive/ROADMAP_V3_FACTION.md) — anciennes phases 0–8
- [`../ROADMAP.md`](../ROADMAP.md) — redirect vers ce dossier

## Principes techniques transverses

- Namespace Java : `fr.wizardmc.*`
- Identifiant social : `coven_id` (String) — remplace progressivement `faction_id`
- Persistance modules : **JSON ou MySQL** configurable (`*StorageType`)
- Transition : `CovenAPI` → adaptateur MassiveCraft puis plugin `WizardCovens` natif
- Code déjà livré (Nexus, Autels, Mana, Pouvoirs) : **conservé**, branché sur Covens via adaptateur
