# CDC TECHNIQUE — Autels Sacrés (V4 Coven SMP)

> Capture de points de carte pour Éclats + **bonus de type** au Coven. Timers V3 conservés.

---

## 1. Objectif

Les Autels sont des **POI dramatiques** : capture 45s, recharge globale, message serveur. En SMP ils nourrissent aussi l’identité du Coven (types élémentaires) et la diplomatie (contestation = casus belli soft).

---

## 2. Règles métier

| ID | Règle |
| :--- | :--- |
| **A-01** | Nombre d’Autels configurable (défaut **7**), positions `altars.yml`. |
| **A-02** | Capture : rayon 3 blocs, **immobile** X/Z, **45s**. |
| **A-03** | Interruption si move / mort / logout / TP / sortie rayon → progression **0 %**. |
| **A-04** | Contestation : ≥2 joueurs de Covens différents sur l’Autel → **pause** pour tous. |
| **A-05** | Succès : owner = `coven_id` ; **+15 Éclats** via `NexusAPI` ; broadcast ; recharge **1h**. |
| **A-06** | Pendant recharge : FX neutres client. |
| **A-07** | Owner persisté ; en SMP : **bonus de type** tant que propriétaire (voir §2.1). |
| **A-08** | Progression 0–100 % → packet client (jauge). |
| **A-09** | Capture d’un Autel ennemi = événement diplo (log + option notif Officers) — **ne déclare pas** la guerre automatiquement. |

### 2.1. Types d’Autel

Config par Autel dans `altars.yml` :

| Type | Bonus Coven (soft, stack limité) |
| :--- | :--- |
| **Feu** | +5 % dégâts épée (hors claim allié) **ou** forge XP |
| **Glace** | +10 % durée Bouclier si actif (soft) |
| **Ombre** | −5 % CD Portail |
| **Lumière** | +1 home temporaire / regen claim légère |
| **Nature** | +10 % génération Mana passive |

Un Coven cumule les bonus des Autels **qu’il possède** ; cap global configurable pour éviter snowball.

---

## 3. Stockage

```sql
CREATE TABLE IF NOT EXISTS wizard_altars (
    altar_id INT PRIMARY KEY,
    world_name VARCHAR(64) NOT NULL,
    pos_x INT NOT NULL, pos_y INT NOT NULL, pos_z INT NOT NULL,
    altar_type VARCHAR(16) NOT NULL DEFAULT 'NATURE',
    current_owner VARCHAR(36) NULL, -- coven_id
    cooldown_end TIMESTAMP NULL,
    last_capture_time TIMESTAMP NULL
);
```

```yaml
altars:
  1:
    world: "world"
    x: 100
    y: 64
    z: 200
    type: FEU
```

Persistance JSON|MySQL (`altarsStorageType`).

---

## 4. Packages & API

```
fr.wizardmc.altars/
├── api/AltarAPI.java
├── data/AltarData.java, AltarDAO.java
├── listeners/AltarMoveListener.java, AltarInteractListener.java
├── managers/AltarManager.java
└── tasks/AltarCaptureTask.java
```

```java
Optional<String> getOwner(int altarId); // coven_id
List<AltarType> getOwnedTypes(String covenId);
boolean isOnCooldown(int altarId);
```

Intégration : `CovenAPI.getCovenByPlayer` ; plus de soft-depend Factions direct.

---

## 5. Client

- Jauge capture (existante)
- Couleur type Autel (teinte particules / jauge)
- Pastille « Autels tenus » dans HUD Coven (`OWNED_SUMMARY` packet 111)
- Caps : `altarBonusMaxPerType` (2), `altarBonusGlobalMax` (5)

---

## 6. QA

- Capture solo 45s / contestation pause
- CD 1h / shards +15
- Bonus type appliqué / retiré au change owner
- Restart persist owner + type
- Fin d’Ère : owners NULL (lien [cdc_eres.md](cdc_eres.md))
