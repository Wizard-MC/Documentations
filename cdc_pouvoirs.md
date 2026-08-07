# CDC TECHNIQUE — Pouvoirs du Coven (V4 Coven SMP)

> Ex-`cdc_evolution_du_nexus.md`. **Specs timers / effets / packets déjà livrées restent**. Framing SMP : défense / mobilité / sabotage / ultime de guerre.

---

## 1. Objectif

Quatre compétences actives de **Coven** (pas P2W, pas skill individuel remplacé). Utiles surtout en **guerre** et défense de ville, tout en restant activables hors guerre pour utilité (Portail, Bouclier préventif selon config).

---

## 2. Règles métier

| ID | Règle |
| :--- | :--- |
| **P-01** | Activation : clic droit Nexus + permission `nexus.power.use` (Leader / Officer / Mage). |
| **P-02** | Déblocage Niv 3 / 5 / 8 / 10 + cooldown persistant. |
| **P-03** | Broadcast + FX client. |
| **P-04** | CD persistants (restart-safe). |
| **P-05** | Un seul pouvoir actif à la fois. |
| **P-06** | Cataclysme : cible un Coven **ennemi en guerre** (GUI) — pas un simple ENEMY sans War (SMP). |
| **P-07** | Bouclier : protège claims du Coven (cohérent protection Covens). |

### Détail (inchangé gameplay)

| Pouvoir | Niv | Effet | Durée | CD |
| :--- | ---: | :--- | :--- | :--- |
| **Bouclier d’Arcanes** | 3 | Claims indestructibles ; pas de place ennemi ; barrière entités | 3 min | 6 h |
| **Passage des Ombres** | 5 | Portail 5 charges, TP membres ; refus claim ennemi | 2 min | 4 h |
| **Malédiction** | 8 | 40 % drop item précieux sur kill ennemi **dans claim** | 10 min | 8 h |
| **Cataclysme** | 10 | Debuffs 30 s sur claim cible + FX tempête | 30 s | 12 h |

Framing SMP :

- Bouclier → **défense de ville**
- Portail → **logistique / commerce / renfort**
- Malédiction → **dissuasion raid**
- Cataclysme → **ultime de guerre**

---

## 3. Stockage

Colonnes cooldown / `active_power` sur `wizard_nexus` (voir [cdc_nexus.md](cdc_nexus.md)).  
`powersStorageType` JSON|MySQL si DAO séparé (`PowerDAO`) — pattern actuel WizardCore.

---

## 4. Packages & API

```
fr.wizardmc.powers/
├── api/PowerAPI.java, PowerType.java
├── data/PowerCooldownData.java, PowerDAO.java
├── listeners/*, gui/PowersGUI.java, CataclysmTargetGUI.java
├── managers/PowerManager.java
└── tasks/*
```

```java
boolean isPowerAvailable(String covenId, PowerType power);
boolean activate(String covenId, PowerType power, Player activator, Object... targets);
long getCooldownRemaining(String covenId, PowerType power);
void cancelActivePower(String covenId);
```

Packets : `115` Bouclier/FX, `116` Cataclysme.

---

## 5. Intégration Covens

- Owner / membres / claims via `CovenAPI`
- CataclysmTargetGUI liste Covens `areAtWar` / `getWarTargetIds` (guerre déclarée, pas ENEMY seul)
- Gate : `CovenAPI.canRaid` / `WarGate.canRaid` (S3-T1)
- Permission rôles Mage

---

## 6. QA

- Activation permissions
- CD persist
- Bouclier break/place
- Portail 5 charges / refus ennemi
- Malédiction 40 %
- Cataclysme seulement en guerre
- Un seul actif
- `/power qa` si conservé
