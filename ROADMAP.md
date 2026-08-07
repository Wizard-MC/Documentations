# ROADMAP — WizardMC Coven SMP (V4)

> **Source de vérité d’exécution** alignée sur [`gdd.md`](gdd.md) + CDCs de ce dossier.  
> Archive plan Faction V3 : [`../archive/ROADMAP_V3_FACTION.md`](../archive/ROADMAP_V3_FACTION.md)  
> Redirect racine : [`../ROADMAP.md`](../ROADMAP.md)

---

## 1. Vision produit (rappel)

SMP Semi-RPG : **Covens**, construction, commerce, diplomatie, magie. PvP semi-ouvert (wilderness libre ; claims protégés ; **guerre** ouvre le raid). Zéro P2W.

Stack : MCP `wizardmc-clean` · `WizardSpigot` · `WizardCore` · cible `WizardCovens` · namespace `fr.wizardmc.*`.

---

## 2. État actuel vs docs SMP

| Module | Doc | Code | Verdict |
| :--- | :--- | :--- | :--- |
| Nexus (niveaux, Éclats, pose) | [cdc_nexus.md](cdc_nexus.md) | WizardCore ✅ | OK — destroy + town_flags + Autels typés ✅ |
| Autels (45s, CD, shards) | [cdc_autels_sacres.md](cdc_autels_sacres.md) | ✅ | OK — types + bonus soft ✅ |
| Mana + Forgeron | [cdc_mana_brut.md](cdc_mana_brut.md) | ✅ | OK — framing convoi déjà là |
| Pouvoirs 4 | [cdc_pouvoirs.md](cdc_pouvoirs.md) | ✅ | OK — Cataclysme **guerre native** (`canRaid`) |
| Contrats → Éclats | GDD | stub ✅ | OK |
| Client FX pouvoirs / Autels / Mana / Nexus | [cdc_exp_client.md](cdc_exp_client.md) | partiel ✅ | Polish HUD Coven manquant |
| Ères 45j | [cdc_eres.md](cdc_eres.md) | `EraAPI` + `SeasonAPI` compat ✅ | **Phase S4 complète** |
| WizardCovens | [cdc_covens.md](cdc_covens.md) | absent | **À faire** (T0 = FactionAdapter) |
| Boutique | [cdc_boutique.md](cdc_boutique.md) | non | À faire |
| Magie / Compagnons / Spawn | stubs | non | Backlog design |

**Transition sociale**

| Étape | État |
| :--- | :--- |
| **T0** FactionAdapter MassiveCraft | ✅ actuel |
| **T1** `CovenAPI` façade (même IDs) | ❌ |
| **T2** Plugin `WizardCovens` + migration | ❌ |
| **T3** Soft-depend Covens, retrait Factions | ❌ |

Colonne SQL `faction_id` : **conservée** jusqu’à T2 (alias logique `coven_id` dans les docs). Messages joueur : vocabulaire **Coven** (alignement UX fait).

---

## 3. Décisions d’architecture (V4)

1. Design = ce dossier `smp/` ; code historique V3 = archive, pas à étendre en framing Faction.  
2. Canal packets : NMS WizardMC en **95+** (hors vanilla). Covens = **117–121** ; Pouvoirs = **115–116** ; Ères = **122–123**.  
   (Ancien plan hex `0x40–0x44` / `0x30` collisionnait avec `S30PacketWindowItems` etc. — corrigé.)  
3. Guerre SMP : `CovenAPI.areAtWar` / `canRaid` = **WarManager natif** uniquement (S3-T1). Relation `ENEMY` ≠ guerre. Mode MASSIVECRAFT : war gates OFF.  
4. Persistance modules : JSON \| MySQL (`*StorageType`).  
5. Soft-depends futurs : WizardCore → Covens (puis retrait hard `Factions`).

---

## 4. Phases d’exécution (à partir de maintenant)

Les phases **0–5 V3** (bootstrap, protocole, Nexus, Autels, Mana, Pouvoirs) sont **considérées livrées** pour le socle mécanique. Détail historique : archive V3.

### Phase S0 — Alignement SMP (court)

| ID | Tâche | Statut |
| :--- | :--- | :--- |
| **S0-T1** | Docs `smp/` (GDD + CDCs + ce ROADMAP) | ✅ |
| **S0-T2** | Vocabulaire joueur faction → Coven (Nexus/Mana/Pouvoirs/Contrats) | ✅ |
| **S0-T3** | Cataclysme : cibles guerre-only (`areAtWar` / ENEMY) | ✅ |
| **S0-T4** | Note archive ROADMAP V3 + redirect racine | ✅ (cette livraison) |

Branche : `feature/smp-align`  
Commit type : `docs(smp)`, `fix(powers)`, `refactor(ux)`

---

### Phase S1 — Pont `CovenAPI` (T1)

| ID | Description | CDC | Statut |
| :--- | :--- | :--- | :--- |
| **S1-T1** | Interface `fr.wizardmc.covens.api.CovenAPI` (+ events) dans plugin **WizardCovens** | covens | ✅ |
| **S1-T2** | Impl `MassiveCraftCovenAdapter` : délègue à Factions + `areAtWar`/`areAllied`/`hasPermission` approx | covens | ✅ |
| **S1-T3** | Brancher Nexus / Autels / Mana / Pouvoirs / Contrats sur `CovenAPI` (plus d’appels directs Factions hors adaptateur) | — | ✅ |
| **S1-T4** | Alias doc + javadoc : `factionId` param = `covenId` sémantique | — | ✅ |

Branche : `feature/coven-api`  
Critère : aucun `Factions.i` hors package `factions` / adapter.

> **Note Phase 5 V3** : Pouvoirs (Bouclier / Portail / Malédiction / Cataclysme) = **livrés** dans WizardCore. Rien à rattraper avant S1 ; le gate guerre Cataclysme est déjà en S0-T3 (proxy ENEMY).

---

### Phase S2 — WizardCovens natif (T2)

| ID | Description | Statut |
| :--- | :--- | :--- |
| **S2-T1** | Plugin `WizardCovens` : Coven, Role, Claim, Relation, War, Bank (voir CDC) | ✅ |
| **S2-T2** | Persistance JSON\|MySQL + commandes `/coven` | ✅ |
| **S2-T3** | Protection claims + PvP rules semi-ouvert | ✅ |
| **S2-T4** | Packets `117–121` (roster, frontiers, war banner, diplo, HUD) | ✅ |
| **S2-T5** | Migration `/covenadmin migrate factions` + remap IDs si besoin | ✅ |
| **S2-T6** | Client MCP : frontiers + HUD Coven + war banner | ✅ |

Branche : `feature/wizard-covens`  
Critère : serveur de staging tourne **sans** MassiveCraft (ou Factions en read-only post-migration).

---

### Phase S3 — Gates guerre & Nexus ville

| ID | Description | CDC | Statut |
| :--- | :--- | :--- | :--- |
| **S3-T1** | `areAtWar` natif (plus proxy ENEMY) branché Cataclysme + `canRaid` | pouvoirs / covens | ✅ |
| **S3-T2** | Destroy Nexus ennemi en guerre (canalisation + CD) | nexus N-09 | ✅ |
| **S3-T3** | Flags ville `town_flags` Niv 1–10 + notifs unlock | nexus §2.1 | ✅ |
| **S3-T4** | Types Autel + bonus soft Coven | autels | ✅ |

Branche : `feature/smp-town`  

---

### Phase S4 — Ères (ex-Saisons)

| ID | Description | CDC |
| :--- | :--- | :--- |
| **S4-T1** | `EraManager` / `era.yml` / tables `wizard_eras` (+ alias season OK) ✅ | eres |
| **S4-T2** | Phases NORMAL / CATACLYSM J-3 ; boost Autels ×3 ; CD pouvoirs /2 ✅ | eres |
| **S4-T3** | Reset partiel + archive + Panthéon ✅ | eres |
| **S4-T4** | Thèmes soft-mod (Ténèbres / Lumière / Chaos) ✅ | eres |
| **S4-T5** | Packets Ère `122` timer / `123` reset ; HUD client « Ère » ✅ | eres + client |
| **S4-T6** | Remplacer stub : `SeasonAPI` → bridge `EraAPI` (compat) ✅ | — |

Branche : `feature/eras`  
**Ne pas** réutiliser 117–121 (Covens) ni 115–116 (Pouvoirs).

---

### Phase S5 — Client polish SMP

| ID | Description |
| :--- | :--- |
| **S5-T1** | HUD unifié Coven / Nexus / Ère / danger Mana ✅ |
| **S5-T2** | Frontiers claim couleurs (own/ally/enemy/war) ✅ |
| **S5-T3** | Settings anti-motion + priorité notifs ✅ |
| **S5-T4** | Handshake launcher (si pas déjà durci) |

Branche : `feature/client-smp`  
CDC : [cdc_exp_client.md](cdc_exp_client.md)

---

### Phase S6 — Boutique zéro P2W

| ID | Description |
| :--- | :--- |
| **S6-T1** | Comptes Gemmes / Poussière + catalogue cosmétiques / confort |
| **S6-T2** | Pass d’Ère (pas « saison ») |
| **S6-T3** | Tebex sync ; checklist anti-P2W (pas shards/mana/land/war) |

Branche : `feature/shop`  
CDC : [cdc_boutique.md](cdc_boutique.md)

---

### Phase S7 — Backlog design (stubs)

Prioriser après S1–S6 selon capacité :

| Module | Doc | Notes |
| :--- | :--- | :--- |
| Magie | [cdc_magie.md](cdc_magie.md) | 12 écoles, utilitaire first |
| Compagnons | [cdc_compagnons.md](cdc_compagnons.md) | rôles build/commerce/guerre |
| Spawn & map | [cdc_spawn_map.md](cdc_spawn_map.md) | zones + POI Autels |

---

## 5. Mapping ancien plan V3 → V4

| Phase V3 | Devenir V4 |
| :--- | :--- |
| 0–1 Bootstrap / protocole | Livré — maintenance only |
| 2 Nexus | Livré + **S3** (ville / destroy) |
| 3 Autels | Livré + **S3** (types) |
| 4 Mana | Livré |
| 5 Pouvoirs | Livré + gate guerre (**S0**/**S3**) |
| 6 Saisons | **S4 Ères** (packets 122–123) |
| 7 Client polish | **S5** (+ HUD Coven) |
| 8 Boutique | **S6** (Pass d’Ère) |
| *(absent)* Covens | **S1–S2** |

---

## 6. Conventions

- Branches : `feature/<scope>` → `dev` → `main`  
- Commits : Conventional Commits ; scopes `covens`, `nexus`, `altars`, `mana`, `powers`, `eras`, `client`, `shop`, `smp`  
- Une PR par phase Sx  
- QA : checklists des CDC concernés  

---

## 7. Risques

| Risque | Mitigation |
| :--- | :--- |
| Collision packet vanilla (0x30/0x40) | IDs WizardMC en 115+ (hors S0–S64) |
| Double vérité Factions / Covens | T1 adaptateur unique ; migration dry-run |
| Snowball Autels typés | Cap bonus global config |
| Reset Ère corrompu | Transaction + backup pré-reset (reprendre pattern archive V3) |
