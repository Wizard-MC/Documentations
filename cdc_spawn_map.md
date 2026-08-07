# CDC STUB V1 — Spawn & Map (Coven SMP)

> **Non livré** comme feature plugin dédiée. Cadre design des zones et POI.

---

## 1. Objectif

Définir l’**espace de jeu** SMP : spawn sûr, biomes narratifs, placement Autels, routes de convoi Mana.

---

## 2. Zones proposées

| Zone | Rôle |
| :--- | :--- |
| **Spawn / Sanctuaire** | Safezone PvP off ; Panthéon ; tutoriel Coven ; PNJ lore |
| **Plaine des Murmures** | Starter claims, Autel Nature/Lumière |
| **Désert de Cristal** | Routes Forgeron, Autel Feu |
| **Forêt d’Ébène** | Ambush convois, Autel Ombre |
| **Montagnes du Crépuscule** | End-game claims, Autel Glace |
| **Wilderness libre** | PvP on, ressources |

---

## 3. Points d’intérêt

- **7 Autels** (voir [cdc_autels_sacres.md](cdc_autels_sacres.md)) — coords dans `altars.yml`  
- **10+ spots Forgeron** — `forgeron_locations.yml`  
- **Panthéon** hologrammes vainqueurs d’Ère  
- Routes naturelles entre Autels (canyons / ponts) pour drama convois  

---

## 4. Skeleton technique (futur)

```
fr.wizardmc.world/
├── api/ZoneAPI.java          # isSafezone, getZoneId
├── data/ZoneType.java
└── managers/SpawnManager.java
```

Soft-depend WorldGuard **ou** régions internes Covens pour spawn.

---

## 5. Hors scope stub

- Génération map custom  
- Quêtes zonées complètes  
- Dynmap markers auto (nice-to-have)  

À détailler avec le worldbuilder avant code.
