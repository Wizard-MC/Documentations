# CDC TECHNIQUE — Ères (V4 Coven SMP)

> Ex-« Cycle de vie / Saisons ». Durée **45 jours**, reset partiel conservé, thèmes narratifs + soft-modifiers.

---

## 1. Objectif

1. Éviter l’hégémonie permanente.  
2. FOMO de fin d’Ère.  
3. Récompenser la constance (Panthéon, titres, cosmétiques).  
4. Offrir une **narration** (Ténèbres / Lumière / Chaos) sans casser l’équilibre.

---

## 2. Règles métier

| ID | Règle |
| :--- | :--- |
| **E-01** | Une Ère = **45 jours** ; dates dans `era.yml` (ex-`season.yml`). |
| **E-02** | Phase Normale J1–42 ; **Grand Cataclysme** J43–45. |
| **E-03** | Reset fin d’Ère : Nexus level 1 / shards 0 / pouvoirs flags 0 / CD pouvoirs 0 ; Autels owners NULL + CD clear ; archive `total_shards_earned`. |
| **E-04** | **Conservé** : claims, builds, inventaires, membres Coven, grades boutique, banque Coven (config). |
| **E-05** | Vainqueur = plus haut niveau Nexus (tie-break `total_shards_earned`) → titre `[Vainqueur E#]`, cosmétique, **Panthéon** spawn. |
| **E-06** | Fin d’Ère : Autels ×3 Éclats ; boss mondial 2 h ; CD pouvoirs **/2**. |
| **E-07** | Chaque Ère a un **thème** (`DARKNESS` / `LIGHT` / `CHAOS`) → soft-mods (voir §2.1). |
| **E-08** | Vocabulaire joueur : « Ère » partout (HUD, messages) ; tables SQL peuvent garder `season_*` en alias. |

### 2.1. Soft-modifiers thématiques

| Thème | Soft-mod (exemples, config) |
| :--- | :--- |
| **Ténèbres** | Autels Ombre +1 éclat ; nuits plus dangereuses (mobs) |
| **Lumière** | Regen claim légère ; Autels Lumière bonus home |
| **Chaos** | Forgeron move 45 min ; war declare cost −10 % |

Mods **légers** : jamais +loot P2W, jamais bypass protection hors guerre.

---

## 3. Stockage

```sql
CREATE TABLE IF NOT EXISTS wizard_eras (
    id INT PRIMARY KEY DEFAULT 1,
    era_number INT NOT NULL DEFAULT 1,
    theme VARCHAR(16) NOT NULL DEFAULT 'CHAOS',
    start_timestamp BIGINT NOT NULL,
    end_timestamp BIGINT NOT NULL,
    phase VARCHAR(20) NOT NULL DEFAULT 'NORMAL' -- NORMAL | CATACLYSM
);

CREATE TABLE IF NOT EXISTS wizard_nexus_archive_eras (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    era_number INT NOT NULL,
    coven_id VARCHAR(36) NOT NULL,
    coven_name VARCHAR(64) NOT NULL,
    final_level INT NOT NULL,
    total_shards_earned INT NOT NULL,
    archived_at BIGINT NOT NULL
);
```

`erasStorageType: JSON|MYSQL`.

---

## 4. Packages & API

```
fr.wizardmc.eras/   # ou fr.wizardmc.seasons/ avec alias
├── api/EraAPI.java
├── data/*
├── managers/EraManager.java
└── listeners/*
```

```java
int getEraNumber();
EraTheme getTheme();
EraPhase getPhase();
void forceAdvancePhase(); // admin
void runEraReset();       // fin
```

Hooks : `NexusAPI.resetNexus`, `AltarAPI.clearOwners`, `PowerAPI` clear CDs.

---

## 5. Client

- Compteur J-restants / phase
- Bannière thème Ère
- Alertes boss + Grand Cataclysme
- Packets : **`122`** timer Ère, **`123`** reset (ROADMAP ; ne pas réutiliser 117–121 Covens / 115–116 Pouvoirs).  
  Note CDC historique `0x45`/`0x46` obsolète — plage custom **95+**.

---

## 6. QA

- Durée / phases
- Reset partiel correct (claims intactes)
- Archive + Panthéon
- Soft-mods thème on/off
- Messages « Ère » cohérents
