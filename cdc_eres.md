# CDC TECHNIQUE — Ères (V4 Coven SMP)

> Ex-« Cycle de vie / Saisons ». Durée **45 jours**, reset partiel conservé, thèmes narratifs + soft-modifiers.

---

## 1. Objectif

1. Éviter l’hégémonie permanente.  
2. FOMO de fin d’Ère.  
3. Récompenser la constance (Panthéon, titres, cosmétiques).  
4. Offrir une **narration** (Ténèbres / Lumière / Chaos) sans casser l’équilibre.

---

## 2. Règles métier

| ID | Règle |
| :--- | :--- |
| **E-01** | Une Ère = **45 jours** ; dates dans `era.yml` (ex-`season.yml`). |
| **E-02** | Phase Normale J1–42 ; **Grand Cataclysme** J43–45. |
| **E-03** | Reset fin d’Ère : Nexus level 1 / shards 0 / pouvoirs flags 0 / CD pouvoirs 0 ; Autels owners NULL + CD clear ; archive `total_shards_earned`. |
| **E-04** | **Conservé** : claims, builds, inventaires, membres Coven, grades boutique, banque Coven (config). |
| **E-05** | Vainqueur = plus haut niveau Nexus (tie-break `total_shards_earned`) → titre `[Vainqueur E#]`, cosmétique, **Panthéon** spawn. |
| **E-06** | Fin d’Ère : Autels ×3 Éclats ; **le Colosse** 2 h (§2.2) ; CD pouvoirs **/2**. |
| **E-07** | Chaque Ère a un **thème** (`DARKNESS` / `LIGHT` / `CHAOS`) → soft-mods (voir §2.1). |
| **E-08** | Vocabulaire joueur : « Ère » partout (HUD, messages) ; tables SQL peuvent garder `season_*` en alias. |

### 2.1. Soft-modifiers thématiques

| Thème | Soft-mod (exemples, config) |
| :--- | :--- |
| **Ténèbres** | Autels Ombre +1 éclat ; nuits plus dangereuses (mobs) |
| **Lumière** | Regen claim légère ; Autels Lumière bonus home |
| **Chaos** | Forgeron move 45 min ; war declare cost −10 % |

Mods **légers** : jamais +loot P2W, jamais bypass protection hors guerre.

---

### 2.2 Le Colosse de l'Ère

Le boss mondial que promet **E-06**. Il paraît en phase de Cataclysme, et
seulement là : hors de cette phase, son lancement est refusé — un boss de fin
d'Ère qui se présenterait au premier jour n'aurait plus rien à clore.

C'est une **occurrence** ([`cdc_covens.md`](cdc_covens.md) et le module
`occurrences`), pas un système à part. Ce choix lui donne sans rien réécrire la
planification, les annonces d'approche, le waypoint sur la minimap et le tableau
de score — et le branche sur l'événement de victoire, si bien que sa mort ouvre
exactement les mêmes portes que n'importe quelle autre : hauts faits, butin de
la section `BOSS`, et tirage de Tome ([`cdc_magie.md`](cdc_magie.md) §5.6).

| Attribut | Valeur | Réglé par |
|---|---|---|
| Durée | 2 h | fixe, comme E-06 l'annonce |
| Vie | 20 000 | `/boss set <vie> [dégâts] [modèle]` |
| Vainqueur | le dernier coup | — |

Son **nom suit le thème de l'Ère** : Colosse des Ténèbres, Colosse d'Aurore,
Colosse des Fractures. C'est la seule chose qui rattache visiblement le combat à
l'Ère qu'il clôt.

Le vainqueur est celui qui porte le dernier coup. C'est le seul critère tenable
sans compter les dégâts de chacun sur deux heures — un tableau de contributions
vivrait en mémoire, disparaîtrait au redémarrage, et se disputerait au premier
écart d'un point.

**Deux heures passées, il se retire** sans vainqueur ni butin. Le laisser traîner
ferait d'un événement une décoration ; le faire mourir seul récompenserait
l'attente.

---

## 3. Stockage

```sql
CREATE TABLE IF NOT EXISTS wizard_eras (
    id INT PRIMARY KEY DEFAULT 1,
    era_number INT NOT NULL DEFAULT 1,
    theme VARCHAR(16) NOT NULL DEFAULT 'CHAOS',
    start_timestamp BIGINT NOT NULL,
    end_timestamp BIGINT NOT NULL,
    phase VARCHAR(20) NOT NULL DEFAULT 'NORMAL' -- NORMAL | CATACLYSM
);

CREATE TABLE IF NOT EXISTS wizard_nexus_archive_eras (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    era_number INT NOT NULL,
    coven_id VARCHAR(36) NOT NULL,
    coven_name VARCHAR(64) NOT NULL,
    final_level INT NOT NULL,
    total_shards_earned INT NOT NULL,
    archived_at BIGINT NOT NULL
);
```

`erasStorageType: JSON|MYSQL`.

---

## 4. Packages & API

```
fr.wizardmc.eras/   # ou fr.wizardmc.seasons/ avec alias
├── api/EraAPI.java
├── data/*
├── managers/EraManager.java
└── listeners/*
```

```java
int getEraNumber();
EraTheme getTheme();
EraPhase getPhase();
void forceAdvancePhase(); // admin
void runEraReset();       // fin
```

Hooks : `NexusAPI.resetNexus`, `AltarAPI.clearOwners`, `PowerAPI` clear CDs.

---

## 5. Client

- Compteur J-restants / phase
- Bannière thème Ère
- Alertes boss + Grand Cataclysme
- Packets : **`122`** timer Ère, **`123`** reset (ROADMAP ; ne pas réutiliser 117–121 Covens / 115–116 Pouvoirs).  
  Note CDC historique `0x45`/`0x46` obsolète — plage custom **95+**.

---

## 6. QA

- Durée / phases
- Reset partiel correct (claims intactes)
- Archive + Panthéon
- Soft-mods thème on/off
- Messages « Ère » cohérents
