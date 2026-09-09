# CDC STUB V1 — Compagnons (Coven SMP)

> **Non livré**. Compagnons = aides de rôle SMP (construction / commerce / guerre), pas pets P2W.

---

## 1. Objectif (V1)

Permettre un compagnon (familier / esprit) par joueur avec **rôles** alignés Coven :

| Rôle | Utilité |
| :--- | :--- |
| **Construction** | Porte-blocs limitée, spotlight build |
| **Commerce** | Marqueur Forgeron / rappel stock Mana Coven |
| **Guerre** | Alerte intrusion claim / ping Officers |

---

## 2. Règles design

- 1 compagnon actif max  
- Cosmétiques skins = boutique OK ; **stats combat** non achetable  
- Soft-cap bonus (jamais auto-mine AFK)  
- Lien optionnel grade Coven (Guard → compagnon Guerre)

---

## 3. Skeleton API

```
fr.wizardmc.companions/
├── api/CompanionAPI.java
├── data/CompanionType.java, CompanionData.java
└── managers/CompanionManager.java
```

```java
void summon(Player p, CompanionType type);
void dismiss(Player p);
Optional<CompanionType> getActive(Player p);
```

Packets client : spawn FX local (pas d’AI lourde serveur V1 — ArmorStand / mob léger).

---

## 4. Hors scope stub

- Pathfinding complexe  
- Inventaire compagnon  
- Combattants autonomes  

CDC complet à rédiger avant implémentation.
