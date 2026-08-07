# CDC TECHNIQUE — Boutique & Monétisation (V4 Coven SMP)

> **Zéro P2W** inchangé. Vocabulaire Ère / Coven ; pas d’achat d’Éclats, Mana, Nexus, sorts combat, land, war wins.

---

## 1. Objectif

Financer le serveur, récompenser la fidélité, valoriser le temps de jeu — **sans** avantage compétitif.

---

## 2. Règles métier

| ID | Règle |
| :--- | :--- |
| **B-01** | Catégories : Cosmétiques / Confort (`/home`, workbench, enderchest) / **Pass d’Ère**. |
| **B-02** | Monnaies : **Gemmes** (réel) + **Poussière d’Étoile** (gratuit in-game). |
| **B-03** | Items non-tradeables (sauf gift cosmétique dédié). |
| **B-04** | Livraison immédiate post-paiement. |
| **B-05** | Pass d’Ère (ex-Pass Saisonnier) : 1× / Ère, piste cosmétiques 1–50. |
| **B-06** | `/gift` cosmétiques. |
| **B-07** | Tebex / boutique web → Gemmes &lt; 30 s. |
| **B-08** | **Interdit** : shards, mana, power unlocks, claim slots, war tokens, XP sorts combat. |

---

## 3. Stockage (rappel)

Tables `wizard_player_accounts`, inventaire cosmétiques JSON, pass `era_number` — voir archive V3 pour schéma détaillé. Adapter `season` → `era` dans les colonnes nouvelles.

---

## 4. Packages

```
fr.wizardmc.shop/
├── api/ShopAPI.java
├── data/*
├── managers/ShopManager.java, PassManager.java
└── listeners/TebexListener.java
```

---

## 5. QA

- Aucun SKU P2W dans catalogue  
- Pass lié à l’Ère courante  
- Gift + sync web  
