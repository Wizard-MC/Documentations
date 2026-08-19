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
| **B-09** | **Autorisé (Confort)** : emplacements de preset de barre de sorts — 3 au maximum, voir §2.1. |

### 2.1 Emplacements de preset — pourquoi ce n'est pas un « claim slot »

Le grimoire permet d'enregistrer une barre de sorts et de la rappeler d'un
clic. Huit emplacements existent : deux offerts, trois ouverts par le niveau
d'école moyen, **trois vendus en Confort**.

B-08 interdit les *claim slots*, et la ressemblance de vocabulaire mérite une
explication plutôt qu'une exception muette.

| | Claim slot | Emplacement de preset |
|---|---|---|
| Ce qu'il donne | du **territoire** — une ressource disputée, en quantité finie, prise à d'autres | une **sauvegarde** de ce que le joueur possède déjà |
| Effet sur un adversaire | direct : plus de terrain, plus d'économie, plus de guerre | aucun |
| Sans l'achat | impossible d'avoir plus de terrain | la même barre se refait à la main en quelques secondes |

Un emplacement de preset n'accorde ni sort, ni niveau, ni statistique, ni
Essence, ni réduction de cooldown. Il fait gagner du **temps de clic** — la
définition même de la catégorie Confort, au même titre que `/home`.

Deux garanties techniques, décrites dans
[`cdc_magie.md`](cdc_magie.md) §5.6, rendent la promesse vérifiable :

1. le chargement d'un preset **revalide chaque sort** contre ce que le joueur a
   réellement appris et son niveau d'école — un preset ne peut donc jamais
   équiper un sort auquel son propriétaire n'a pas droit ;
2. les emplacements se créditent **uniquement par commande console**
   (`/magic presetslot`) : le client ne peut pas s'en accorder.

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
- Emplacement de preset acheté : ne débloque aucun sort ni niveau (test automatisé côté WizardCore)  
- Pass lié à l’Ère courante  
- Gift + sync web  
