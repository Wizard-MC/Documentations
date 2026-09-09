# CDC TECHNIQUE — Mana Brut (V4 Coven SMP)

> Ressource stratégique **inter-Covens** : production sur claims, transport risqué, Forgeron itinérant. Anti-AFK conservé ; framing commerce / mercenaires ajouté.

---

## 1. Objectif

1. Éviter le farm AFK passif « à la pioche ».  
2. Créer des **convois** dramatiques (cibles mobiles).  
3. Nourrir l’économie Coven (échanges, escortes payées entre joueurs).

---

## 2. Règles métier

| ID | Règle |
| :--- | :--- |
| **M-01** | Stock virtuel par `coven_id` — `/mana` ou GUI Nexus. |
| **M-02** | Production passive : toutes les **30 min**, **1 Mana / claim** (hook `CovenAPI.getClaimCount`). Bonus flag Nexus `granary` possible (+5 %). |
| **M-03** | Withdraw vers inventaire (`/mana withdraw` / GUI) — permission `bank`-like `mana.withdraw` (Merchant+). |
| **M-04** | Item Mana : stack jusqu’à 64 ; **interdit** coffres / fours / hoppers (transport ou échange joueur / Forgeron uniquement). |
| **M-05** | Forgeron itinérant : reposition **60 min**, points `forgeron_locations.yml`. |
| **M-06** | GUI échange : armes, potions, blocs, cosmétiques légers. |
| **M-07** | Mort transporteur : **50 %** drop au sol, **50 %** détruit (pas de retour stock). |
| **M-08** | Stock virtuel persistant. |
| **M-09** | **Convois** (SMP) : pas de véhicule plugin obligatoire V1 — design = joueurs + chat Coven + pastille HUD « danger Mana ». Mercenariat = paiement joueur (hors plugin) ou banque Coven tip. |
| **M-10** | Covens alliés peuvent **escort** ; pickup Mana ennemi autorisé en wilderness / guerre. |

---

## 3. Stockage

```sql
CREATE TABLE IF NOT EXISTS wizard_mana_stock (
    coven_id VARCHAR(36) PRIMARY KEY,
    virtual_stock INT NOT NULL DEFAULT 0,
    last_generation TIMESTAMP NULL
);

CREATE TABLE IF NOT EXISTS wizard_forgeron_state (
    id INT PRIMARY KEY DEFAULT 1,
    current_world VARCHAR(64) NOT NULL,
    current_x INT NOT NULL,
    current_y INT NOT NULL,
    current_z INT NOT NULL,
    next_move_time TIMESTAMP NOT NULL
);
```

`manaStorageType: JSON|MYSQL`.

---

## 4. Packages & API

```
fr.wizardmc.mana/
├── api/ManaAPI.java
├── data/*
├── listeners/ManaItemListener.java, ForgeronListener.java
├── managers/ManaManager.java, ForgeronManager.java
└── gui/ForgeronGUI.java
```

```java
int getVirtualStock(String covenId);
boolean withdraw(String covenId, Player p, int amount);
void depositVirtual(String covenId, int amount); // admin / events
```

---

## 5. Client

- Icône HUD si Mana dans l’inventaire (danger)
- Alerte proximité Forgeron (option)
- Son / particules drop Mana au kill

---

## 6. QA

- Génération × claims après 30 min
- Withdraw permissions
- Anti-container Mana
- Mort 50/50
- Forgeron move + persist
- Alignement `coven_id` migration
