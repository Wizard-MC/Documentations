# CDC STUB V1 — Magie (Coven SMP)

> **Non livré** dans le code actuel. Design + skeleton API pour roadmap future.

---

## 1. Objectif (V1)

Système de **12 écoles** de magie avec sorts majoritairement **utilitaires** (construction, commerce, récolte) pour coller au SMP Semi-RPG. Le combat magique reste secondaire et non-P2W.

---

## 2. Design V1

| Élément | Proposition |
| :--- | :--- |
| Écoles | 12 (ex. Feu, Glace, Ombre, Lumière, Nature, Arcane, Tempête, Terre, Sang, Esprit, Temps, Vide) |
| Progression | Grimoire joueur ; XP via usage / quêtes — **pas** boutique |
| Sorts utilitaires | Levitation blocs build, accélération récolte, détection minerai limitée CD, charme commerce PNJ, réparation outil |
| Sorts combat | Peu nombreux, CD longs, pas de oneshot |
| Lien Coven | Bonus Autel type / flag Nexus `library` |

---

## 3. Skeleton API

```
fr.wizardmc.magic/
├── api/MagicAPI.java
├── data/SpellSchool.java, PlayerGrimoire.java
└── managers/MagicManager.java
```

```java
boolean cast(Player p, String spellId);
boolean hasSchool(Player p, SpellSchool school);
int getSchoolLevel(Player p, SpellSchool school);
```

Persistance : `magicStorageType` JSON|MySQL — table `wizard_player_magic`.

---

## 4. Hors scope V1 stub

- Implémentation sorts  
- Packets FX complets  
- Équilibrage PvP final  

Prochaine étape : CDC complet quand priorisé en ROADMAP code.
