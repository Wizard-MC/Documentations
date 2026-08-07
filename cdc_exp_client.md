# CDC TECHNIQUE — Expérience Client (V4 Coven SMP)

> Mod MCP 1.7.10 obligatoire. HUD / FX étendus au vocabulaire **Coven / ville / guerre / convois**.

---

## 1. Objectif

1. Réception centralisée des packets (Nexus, Autels, Mana, Pouvoirs, Ères, **Covens**).  
2. HUD cohérent non-intrusif.  
3. FX immersifs (pouvoirs, frontiers claim, Autels).  
4. Handshake launcher obligatoire.

---

## 2. Règles métier

| ID | Règle |
| :--- | :--- |
| **C-01** | Handshake 5 s → kick si mauvais client. |
| **C-02** | HUD via `ScaledResolution`. |
| **C-03** | FX 100 % client (pas d’entités serveur pour frontiers). |
| **C-04** | Sons assets mod (mix vanilla). |
| **C-05** | Priorité notifs : Guerre/Cataclysme > Capture > Level-up > Mana > HUD statique (file triée + bordures). |
| **C-06** | Settings : désactiver shake (`enableCameraShake`) / frontiers denses. |
| **C-07** | **HUD Coven** : tag, land used/max, war flag, Autels tenus. |
| **C-07b** | **HUD SMP unifié** (`ElementSmpHud`) : colonne Coven → Nexus → Ère → Mana ; pastilles legacy si setting off. |
| **C-08** | **Frontiers claim** packet `118` / CDC `0x41` (couleurs own/ally/enemy/war) ; arêtes entre couleurs + ruban sol. |
| **C-09** | **Alerte convoi** : pastille si Mana dans l’inventaire. |
| **C-10** | **War banner** packet `0x42`. |

---

## 3. Architecture MCP

```
fr.wizardmc.client/
├── WizardMCMod.java
├── core/ClientPacketHandler.java, HandshakeHandler.java
├── gui/
│   ├── GuiWizardOverlay.java
│   ├── GuiAltarCapture.java
│   ├── GuiCovenHud.java
│   └── GuiNotification.java
├── powers/          # FX Bouclier / Portail / Malédiction / Cataclysme
├── covens/          # frontiers, war banner
├── audio/
└── settings/
```

Canal packets : existants + `0x40`–`0x44` (voir [cdc_covens.md](cdc_covens.md)).

---

## 4. Priorité d’affichage (SMP)

1. Guerre déclarée / Cataclysme  
2. Capture Autel  
3. Level-up Nexus / bâtiment ville  
4. Danger Mana  
5. HUD Coven / Ère  

---

## 5. QA

- Kick sans launcher  
- HUD Coven + frontiers  
- FX pouvoirs  
- Pastille Mana  
- Settings persistants  
