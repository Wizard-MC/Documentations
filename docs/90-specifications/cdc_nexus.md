# CDC TECHNIQUE — Nexus (V4 Coven SMP)

> Adaptation SMP du CDC Nexus V3. Code livré conservé ; vocabulaire `faction` → `coven_id` ; soft-depend **Covens**.

---

## 1. Objectif

Le **Nexus** est le **cœur de ville** du Coven : totem physique dans un claim, progression Niv 1–10, accumulation d’**Éclats de Résonance**.

Il débloque en **double piste** :

1. **Pouvoirs actifs** (déjà implémentés) — Bouclier, Portail, Malédiction, Cataclysme  
2. **Bâtiments / flags ville** — murailles, tour de guet, forge, bibliothèque… (nouveauté SMP)

Socle pour Autels, Mana, Pouvoirs, classement d’Ère.

---

## 2. Règles métier

| ID | Règle |
| :--- | :--- |
| **N-01** | Un Coven = **un seul Nexus**, posé par Leader (permission `nexus.place`). |
| **N-02** | Retrait : Leader + confirmation ; impossible pendant guerre si config `nexus.lockDuringWar`. |
| **N-03** | Niveau 1 → 10. Formule : `ShardsRequired = floor(50 * pow(level, 1.55))` (config). |
| **N-04** | `NexusAPI.addShards(covenId, amount, source)` — sources élargies SMP. |
| **N-05** | Level-up auto ; excédent d’Éclats conservé. |
| **N-06** | Flags pouvoirs Niv 3/5/8/10 (booléens persistés). |
| **N-07** | Flags **bâtiments** Niv 1–10 (voir §2.1) — déblocage progressif. |
| **N-08** | Notifications membres online (gain Éclats / level-up) + packet client. |
| **N-09** | **Destruction Nexus ennemi** : uniquement en **guerre déclarée** ; action longue (ex. 60s canalisation) ; CD global `nexus.destroyCooldownHours` (ex. 48h) pour le Coven attaquant ; effet : reset niveau/shards du défenseur **ou** malus configurable (pas wipe claims). |
| **N-10** | Identifiant propriétaire : `coven_id` (String) via `CovenAPI`. |

### 2.1. Déblocages ville (flags)

| Niveau | Flag bâtiment | Effet design |
| :--- | :--- | :--- |
| 1 | `town_foundation` | Tag ville + hologramme Nexus |
| 2 | `walls_basic` | Accès recipe/kits murailles (ou permission build zone mur) |
| 3 | *(pouvoir)* Bouclier + `watchtower` | Tour : alerte intrusion claim (packet) |
| 4 | `market_stall` | Emplacements commerce Coven |
| 5 | *(pouvoir)* Portail + `forge` | Bonus craft / accès forge Coven |
| 6 | `granary` | Bonus génération Mana passive +5 % |
| 7 | `library` | Soft-mod XP enchant / quêtes lore |
| 8 | *(pouvoir)* Malédiction + `barracks` | Buffs garde (futures) |
| 9 | `arcane_relay` | Réduction CD Portail −10 % |
| 10 | *(pouvoir)* Cataclysme + `citadel` | Titre Forteresse + bonus Panthéon |

Les bâtiments sont des **flags** + hooks ; le build physique reste joueur-driven (pas de schematic forcée V1).

### 2.2. Sources d’Éclats (SMP)

| Source | Notes |
| :--- | :--- |
| Autels | Conservé (15, ×3 en fin d’Ère) |
| Contrats / quêtes | Conservé + élargi |
| Commerce | Vente au Forgeron / marchés Coven (petits montants) |
| Boss / événements Ère | Conservé |
| **Pas** d’achat boutique | Zéro P2W |

---

## 3. Stockage

Persistance **JSON | MySQL** (`nexusStorageType`).

```sql
CREATE TABLE IF NOT EXISTS wizard_nexus (
    coven_id VARCHAR(36) PRIMARY KEY,  -- ex-faction_id (même colonne OK en migration)
    current_level INT NOT NULL DEFAULT 1,
    shards INT NOT NULL DEFAULT 0,
    total_shards_earned INT NOT NULL DEFAULT 0,

    power_shield TINYINT(1) NOT NULL DEFAULT 0,
    power_portal TINYINT(1) NOT NULL DEFAULT 0,
    power_curse TINYINT(1) NOT NULL DEFAULT 0,
    power_cataclysm TINYINT(1) NOT NULL DEFAULT 0,

    -- Flags ville (JSON ou colonnes)
    town_flags TEXT NOT NULL DEFAULT '{}',

    world VARCHAR(64) NULL,
    pos_x INT NULL, pos_y INT NULL, pos_z INT NULL,

    power_shield_cooldown BIGINT NOT NULL DEFAULT 0,
    power_portal_cooldown BIGINT NOT NULL DEFAULT 0,
    power_curse_cooldown BIGINT NOT NULL DEFAULT 0,
    power_cataclysm_cooldown BIGINT NOT NULL DEFAULT 0,
    active_power VARCHAR(32) NULL,
    active_power_end BIGINT NOT NULL DEFAULT 0,

    last_level_up TIMESTAMP NULL,
    INDEX idx_total_shards (total_shards_earned)
);
```

Migration : `ALTER TABLE ... CHANGE faction_id coven_id` **ou** alias en code (`getOwnerId()`).

---

## 4. API & packages

```
fr.wizardmc.nexus/
├── api/NexusAPI.java
├── data/NexusData.java, NexusDAO.java (+ Json/MySQL)
├── listeners/NexusListener.java
└── managers/NexusManager.java
```

```java
public interface NexusAPI {
    boolean addShards(String covenId, int amount, Player source);
    int getLevel(String covenId);
    boolean hasPower(String covenId, NexusPower power);
    boolean hasTownFlag(String covenId, String flag);
    void resetNexus(String covenId); // Ères
    boolean tryDestroyEnemyNexus(String attackerCovenId, String defenderCovenId, Player actor);
}
```

Soft-depend : `CovenAPI` pour ownership, permissions `nexus.*`, guerre.

---

## 5. Packets client (existants / à étendre)

| ID | Usage |
| :--- | :--- |
| Nexus HUD | level, shards, next threshold |
| Level-up FX | conservé |
| Town flag unlock | packet 110 `TOWN_FLAG_UNLOCK` + toast (S3-T3) |
| Destroy progress | packet 110 `DESTROY_PROGRESS` / `CLEAR` / `SUCCESS` (S3-T2) |

Hooks soft S3-T3 : `granary` (+5 % Mana), `arcane_relay` (−10 % CD Portail), `watchtower` (alerte intrusion claim).

Config N-09 : `nexusDestroyChannelSeconds` (60), `nexusDestroyRadius` (3), `nexusDestroyCooldownHours` (48), `nexusDestroyMode` (`RESET`|`MALUS`), `nexusLockDuringWar`.

---

## 6. QA

- Place / remove Nexus (Leader)
- Level-up + flags pouvoirs + flags ville
- addShards depuis Autel / commerce mock
- Destroy Nexus uniquement en guerre + CD
- Persist JSON & MySQL après restart
- `coven_id` aligné Covens / migration Factions
