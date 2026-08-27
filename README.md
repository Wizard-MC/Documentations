# WizardMC — Docs « Coven SMP » (V4)

> **Source de vérité design** pour le positionnement **SMP Semi-RPG**.  

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
| [eres/](eres/bbmodel_colosse.md) | Cahier des charges du modèle 3D du Colosse de l'Ère |
| [cdc_exp_client.md](cdc_exp_client.md) | Client MCP / HUD / FX |
| [cdc_boutique.md](cdc_boutique.md) | Monétisation zéro P2W |
| [cdc_magie.md](cdc_magie.md) | **Magie** — 9 écoles, 41 sorts, runtime VFX |
| [magie/](magie/README.md) | Fiches détaillées : une par école, une par sort, conception des VFX |
| [cdc_intro.md](cdc_intro.md) | **Cinématique d'arrivée** — L'Invocation d'Aelindra |
| [cdc_compagnons.md](cdc_compagnons.md) | Stub Compagnons SMP |
| [cdc_spawn_map.md](cdc_spawn_map.md) | Stub Spawn & map |
| [cdc_wizardcloud.md](cdc_wizardcloud.md) | **WizardCloud** — distribution et mise à jour du client MCP |


## Principes techniques transverses

- Namespace Java : `fr.wizardmc.*`
- Identifiant social : `coven_id` (String) — remplace progressivement `faction_id`
- Persistance modules : **JSON ou MySQL** configurable (`*StorageType`)
- Transition : `CovenAPI` → adaptateur MassiveCraft puis plugin `WizardCovens` natif
- Code déjà livré (Nexus, Autels, Mana, Pouvoirs) : **conservé**, branché sur Covens via adaptateur
