# CDC TECHNIQUE — Plugin **WizardCovens** (V1.0 SMP)

> Remplace **MassiveCraft Factions UUID** comme couche sociale / territoriale.  
> Packages : `fr.wizardmc.covens.*`  
> Soft-depend pour WizardCore (Nexus, Autels, Mana, Pouvoirs, Ères).

---

## 1. Objectif / non-objectifs

### Objectif

Fournir un plugin **complet** de guildes (**Covens**) pour WizardMC SMP :

- Création / gestion de membres / rôles granulaires
- Claims de chunks (protection build + règles PvP)
- Diplomatie (allié, ennemi, trêve, guerre)
- Banque Coven + taxes optionnelles + logs
- API publique consommée par le reste de WizardCore
- Packets MCP (liste membres, frontiers, bannière de guerre, icone (image) du Covens/Guildes (doit pouvoir déssinner le logo en pixelart ou fournir liens vers l'image pour la coller dans le truc de dessin))
- GUI/Interface cohérent avec nos custom interface (interface userfriendly & complète et joliement présenté)
- Stockage data JSON / MySQL configurable (l'un ou l'autre !)
- Queue/Tasks possibilité de configurer et activé un queue/worker redis/rabbitmq ect
- Migration depuis Factions MassiveCraft

### Non-objectifs (V1)

- Remplacer Essentials / economy globale (Vault optionnel)
- Système de quêtes / contrats (hors scope Covens)
- Magie individuelle / compagnons (CDC séparés)
- Modifier le code existant Nexus/Autels dans *cette* mission docs
- Feature-parity 1:1 avec toutes les commandes MassiveCraft obscures

---

## 2. Glossaire

| Terme | Définition |
| :--- | :--- |
| **Coven** | Guilde / nation joueur. Identifiant stable `coven_id` (UUID string). |
| **Claim** | Chunk `(world, chunkX, chunkZ)` appartenant à un Coven. |
| **Role** | Ensemble de permissions nommées (Leader, Officer, Builder…). |
| **Relation** | État diplomatique entre deux Covens. |
| **War** | Guerre déclarée : ouvre le raid/PvP sur les claims des deux camps. |
| **Bank** | Solde virtuel du Coven (monnaie serveur Vault ou interne). |

---

## 3. Entités

```
Coven
 ├── id, name, tag, description, home, createdAt, disbanded
 ├── power (score SMP-friendly), maxLand
 ├── bankBalance
 ├── nexusWorld/x/y/z (optionnel miroir)
 └── members[] → Member(playerUuid, roleId, joinedAt)
Role
 ├── id, covenId (null = template global), name, priority
 └── permissions[] (strings)
Claim
 ├── world, chunkX, chunkZ, covenId, claimedAt, claimedBy
Relation
 ├── covenA, covenB, type (NEUTRAL|ALLY|ENEMY|TRUCE), since
War
 ├── id, attackerId, defenderId, startedAt, endedAt, status
 └── optional: casusBelli, costPaid
Treaty (optionnel V1.1)
 └── type, parties, expiresAt, termsJson
BankTransaction
 └── id, covenId, actorUuid, delta, reason, timestamp
```

---

## 4. Règles métier

| ID | Règle |
| :--- | :--- |
| **CV-01** | Création : `/coven create <name> <tag>` — coût configurable (ex. 5000$), tag 2–5 chars, name unique (case-insensitive). |
| **CV-02** | Un joueur n’appartient qu’à **un** Coven à la fois. |
| **CV-03** | Invite : `/coven invite <player>` — cible accepte via `/coven join <tag|name>` ou GUI. |
| **CV-04** | Kick : Officer+ selon permission `member.kick` ; Leader ne peut pas être kick (sauf succession). |
| **CV-05** | Leave : `/coven leave` — Leader doit d’abord `transfer` ou `disband`. |
| **CV-06** | Disband : Leader uniquement + confirmation ; claims libérés ; banque perdue ou redistribuée (config). |
| **CV-07** | Claim : `/coven claim` sur le chunk courant — coût power/land ; maxLand = f(membres, power, config). |
| **CV-08** | Unclaim : permission `land.unclaim` ; unclaim forcé si overclaim (option SMP : **overclaim OFF** par défaut). |
| **CV-09** | **Power SMP-friendly** : power = `base + membersOnlineWeight + activityScore` (pas le modèle Factions punitif « mort = -power » brutal). Configurable ; mort peut retirer un petit malus temporaire. |
| **CV-10** | Protection claim hors guerre : ennemis / neutres ne peuvent pas break/place/open containers (sauf permissions relation ALLY). |
| **CV-11** | En **guerre** : break/place autorisés selon config guerre ; PvP forcé ON dans les claims des belligérants. |
| **CV-12** | Home Coven : `/coven home` — set par Leader/Officer ; CD anti-abuse. |
| **CV-13** | Soft-cap membres V1 : 50 (config). |
| **CV-14** | Claims contiguous optionnels : `requireConnectedClaims` (défaut **false** pour SMP exploration). |

### Limites land (défaut proposés)

```yaml
land:
  baseClaims: 10
  claimsPerMember: 2
  maxClaimsAbsolute: 80
  claimCostMoney: 0
  overclaimEnabled: false
```

---

## 5. Rôles & permissions

### Rôles par défaut (templates)

| Rôle | Priority | Intention SMP |
| :--- | ---: | :--- |
| **Leader** | 100 | Maire / fondateur — tous droits |
| **Officer** | 80 | Officiers — invite, claim, guerre, banque withdraw |
| **Mage** | 60 | Activation pouvoirs Nexus (lien WizardCore) |
| **Guard** | 55 | Accès coffres défense, alertes |
| **Builder** | 50 | Build / unclaim limité / structures |
| **Merchant** | 50 | Banque deposit + taxes view + convois Mana |
| **Farmer** | 40 | Build farm zones, containers farm |
| **Member** | 10 | Droits de base |
| **Recruit** | 0 | Lecture seule + build restreint |

### Permissions granulaires (exemples)

```
coven.disband
coven.rename
coven.sethome
member.invite
member.kick
member.setrole
land.claim
land.unclaim
land.build
land.container
land.interact
bank.deposit
bank.withdraw
bank.tax.set
diplo.ally
diplo.enemy
diplo.truce
diplo.war.declare
diplo.war.surrender
nexus.power.use          # Bouclier / Portail / …
nexus.upgrade
chat.coven
chat.ally
```

Leader = `*` implicite. Les permissions sont stockées par rôle ; overrides joueur possibles en V1.1.

---

## 6. Diplomatie

### États

| Relation | Effet |
| :--- | :--- |
| **NEUTRAL** | Défaut. Pas d’accès claims. |
| **ALLY** | Build/containers selon config ally ; pas de PvP friendly-fire (option). |
| **TRUCE** | Comme NEUTRAL + pas de déclaration de guerre pendant la durée. |
| **ENEMY** | Marqueur UI ; **ne suffit pas** à ouvrir les claims — il faut une **War**. |
| **WAR** | État actif `War` : raid claims + PvP claim ON. |

### Déclaration de guerre

| ID | Règle |
| :--- | :--- |
| **W-01** | `/coven war declare <coven>` — permission `diplo.war.declare`, coût banque + cooldown global. |
| **W-02** | Les deux camps reçoivent title + packet bannière ; broadcast optionnel. |
| **W-03** | Durée min avant paix forcée : `war.minDurationMinutes` (ex. 60). |
| **W-04** | Fin : `surrender`, `peace` mutuel, ou timeout `war.maxDurationHours`. |
| **W-05** | Destruction / raid du **Nexus** ennemi : règles dans [cdc_nexus.md](cdc_nexus.md) (CD long, guerre requise). |

### Coûts défaut

```yaml
diplomacy:
  warDeclareCost: 2500
  warDeclareCooldownHours: 12
  allyRequestExpireMinutes: 30
  truceDefaultHours: 48
```

---

## 7. Économie Coven

| ID | Règle |
| :--- | :--- |
| **E-01** | Banque : solde `bank_balance` ; deposit/withdraw via cmd/GUI. |
| **E-02** | Toute opération → `BankTransaction` loguée. |
| **E-03** | Taxes optionnelles : % sur ventes PNJ / contrats (hook futur) ou prélèvement périodique membres (opt-in). |
| **E-04** | Soft-depend Vault ; si absent, monnaie interne `coven_credits`. |
| **E-05** | Mana Brut reste dans WizardCore (`wizard_mana_stock`) — Covens expose seulement `getClaimCount(covenId)` pour la génération. |

---

## 8. Intégration — `CovenAPI`

Remplace progressivement `FactionAdapter`.

```java
package fr.wizardmc.covens.api;

import java.util.List;
import java.util.Optional;
import java.util.UUID;

public interface CovenAPI {
    Optional<Coven> getCoven(String covenId);
    Optional<Coven> getCovenByPlayer(UUID player);
    Optional<Coven> getCovenAt(String world, int chunkX, int chunkZ);

    boolean areAllied(String a, String b);
    boolean areAtWar(String a, String b); // WarManager natif — ≠ ENEMY diplo
    boolean canRaid(String attacker, String defender); // S3 = areAtWar
    RelationType getRelation(String a, String b);

    boolean hasPermission(UUID player, String permission);
    boolean canBuild(UUID player, String world, int x, int y, int z);

    int getClaimCount(String covenId);
    List<Claim> getClaims(String covenId);

    // Transition helper
    String getCovenIdCompatible(UUID player); // same semantic as old faction id string
}
```

### Phase de transition

1. **T0 (actuel)** : WizardCore → `FactionAdapter` MassiveCraft  
2. **T1** : `CovenAPI` implémentée par **adaptateur** MassiveCraft (même IDs)  
3. **T2** : `WizardCovens` natif + migration SQL/JSON  
4. **T3** : retrait soft-depend MassiveCraft  

Les modules Nexus/Autels/Mana/Pouvoirs consomment **uniquement** `CovenAPI` dès T1.

### Events Bukkit custom

```
CovenCreateEvent / CovenDisbandEvent
CovenJoinEvent / CovenLeaveEvent / CovenKickEvent
CovenClaimEvent / CovenUnclaimEvent
CovenRelationChangeEvent
CovenWarStartEvent / CovenWarEndEvent
CovenBankTransactionEvent
```

---

## 9. Stockage (JSON | MySQL)

Pattern WizardCore : `covensStorageType: JSON|MYSQL` dans `config.yml`.

### MySQL

```sql
CREATE TABLE wizard_covens (
  coven_id VARCHAR(36) PRIMARY KEY,
  name VARCHAR(32) NOT NULL,
  tag VARCHAR(5) NOT NULL,
  description VARCHAR(255) DEFAULT '',
  home_world VARCHAR(64) NULL,
  home_x DOUBLE NULL, home_y DOUBLE NULL, home_z DOUBLE NULL,
  home_yaw FLOAT NULL, home_pitch FLOAT NULL,
  power DOUBLE NOT NULL DEFAULT 0,
  max_land INT NOT NULL DEFAULT 10,
  bank_balance DOUBLE NOT NULL DEFAULT 0,
  created_at BIGINT NOT NULL,
  disbanded TINYINT(1) NOT NULL DEFAULT 0,
  UNIQUE KEY uk_name (name),
  UNIQUE KEY uk_tag (tag)
);

CREATE TABLE wizard_coven_members (
  player_uuid VARCHAR(36) PRIMARY KEY,
  coven_id VARCHAR(36) NOT NULL,
  role_id VARCHAR(32) NOT NULL,
  joined_at BIGINT NOT NULL,
  INDEX idx_coven (coven_id)
);

CREATE TABLE wizard_coven_roles (
  role_id VARCHAR(32) NOT NULL,
  coven_id VARCHAR(36) NULL, -- NULL = template global
  name VARCHAR(32) NOT NULL,
  priority INT NOT NULL,
  permissions TEXT NOT NULL, -- JSON array
  PRIMARY KEY (role_id, coven_id)
);

CREATE TABLE wizard_coven_claims (
  world VARCHAR(64) NOT NULL,
  chunk_x INT NOT NULL,
  chunk_z INT NOT NULL,
  coven_id VARCHAR(36) NOT NULL,
  claimed_at BIGINT NOT NULL,
  claimed_by VARCHAR(36) NULL,
  PRIMARY KEY (world, chunk_x, chunk_z),
  INDEX idx_coven (coven_id)
);

CREATE TABLE wizard_coven_relations (
  coven_a VARCHAR(36) NOT NULL,
  coven_b VARCHAR(36) NOT NULL,
  relation VARCHAR(16) NOT NULL,
  since BIGINT NOT NULL,
  PRIMARY KEY (coven_a, coven_b)
);

CREATE TABLE wizard_coven_wars (
  war_id VARCHAR(36) PRIMARY KEY,
  attacker_id VARCHAR(36) NOT NULL,
  defender_id VARCHAR(36) NOT NULL,
  status VARCHAR(16) NOT NULL, -- ACTIVE|ENDED
  started_at BIGINT NOT NULL,
  ended_at BIGINT NULL,
  cost_paid DOUBLE NOT NULL DEFAULT 0
);

CREATE TABLE wizard_coven_bank_log (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  coven_id VARCHAR(36) NOT NULL,
  actor_uuid VARCHAR(36) NULL,
  delta DOUBLE NOT NULL,
  reason VARCHAR(64) NOT NULL,
  created_at BIGINT NOT NULL,
  INDEX idx_coven_time (coven_id, created_at)
);
```

### JSON

Répertoire `plugins/WizardCovens/data/` :

- `covens/<id>.json`
- `claims/<world>_<cx>_<cz>.json` ou index spatial unique
- `relations.json`, `wars.json`, `bank/<id>.jsonl`

DAO unique : `CovenDAO` + impl `JsonCovenDAO` / `MySQLCovenDAO`.

---

## 10. Packets MCP (IDs à réserver)

Canal : `WizardMC|Covens` (ou extension du canal Wizard existant).

| Packet | Dir | Contenu |
| :--- | :--- | :--- |
| `0x40` CovenRoster | S→C | covenId, name, tag, members[{uuid,name,role,online}] |
| `0x41` ClaimBorder | S→C | chunks[] + color (own/ally/enemy/war) pour frontiers client |
| `0x42` WarBanner | S→C | warId, attackerTag, defenderTag, startedAt, active |
| `0x43` DiploUpdate | S→C | relations snapshot légère |
| `0x44` CovenHud | S→C | power, land used/max, bank (si permission), war flag |

Client : overlay frontiers + pastille guerre dans le HUD (voir [cdc_exp_client.md](cdc_exp_client.md)).

---

## 11. Structure packages & commandes

```
fr.wizardmc.covens/
├── WizardCovensPlugin.java
├── api/
│   ├── CovenAPI.java
│   ├── Coven.java, Member.java, Role.java, Claim.java
│   ├── RelationType.java, War.java
│   └── event/*.java
├── command/
│   ├── CovenCommand.java          # /coven …
│   └── CovenAdminCommand.java     # /covenadmin …
├── data/
│   ├── model/*
│   ├── CovenDAO.java
│   ├── JsonCovenDAO.java
│   └── MySQLCovenDAO.java
├── listeners/
│   ├── ClaimProtectionListener.java
│   ├── PvpRuleListener.java
│   └── PlayerJoinSyncListener.java
├── managers/
│   ├── CovenManager.java
│   ├── ClaimManager.java
│   ├── DiplomacyManager.java
│   ├── WarManager.java
│   ├── BankManager.java
│   └── PowerScoreManager.java
├── migration/
│   └── FactionsMigrationTool.java
└── network/
    └── CovenPacketService.java
```

### Commandes joueur (`/coven` alias `/c`)

```
create, disband, invite, join, leave, kick, roster
claim, unclaim, map, home, sethome
ally, enemy, truce, war, peace, surrender
bank, deposit, withdraw, tax
role, setrole, perms
chat (c / a)
info, list, top
```

### Admin

```
/covenadmin bypass
/covenadmin setpower | setland | forcejoin | forcedisband
/covenadmin war end <id>
/covenadmin migrate factions
```

---

## 12. Migration depuis MassiveCraft Factions

### Entrées

- Factions UUID (id, name, tag)
- Memberships + ranks approximés (Leader/Officer/Member)
- Board claims (world, chunk → faction)

### Mapping

| Factions | Covens |
| :--- | :--- |
| faction id | `coven_id` (conserver l’UUID si possible) |
| Leader / Officer | Leader / Officer |
| Member | Member |
| Ally / Enemy | Relation ALLY / ENEMY |
| War (si présent) | War ACTIVE ou ENEMY seul |
| Power Factions | recalcul SMP au premier boot |

### Procédure

1. Snapshot BDD / flatfile Factions  
2. `/covenadmin migrate factions --dry-run` → rapport  
3. Migration réelle + mode **read-only** Factions  
4. Switch WizardCore soft-depend vers Covens  
5. Validation QA claims + diplo + Nexus ownership  

Les tables `wizard_nexus.faction_id`, `wizard_mana_stock.faction_id`, etc. restent compatibles si l’ID string est préservé ; sinon script de remap.

---

## 13. Sécurité / anti-abuse

- Rate-limit invites, claims, war declares
- Confirmation textuelle pour disband / surrender
- Audit log admin des war / bank withdraw
- Bypass admin traçable
- Protection exploit claim/unclaim spam (cooldown chunk)
- Validation tag/name (anti-spam unicode, longueur)

---

## 14. Config (`config.yml` — extrait)

```yaml
storage:
  covensStorageType: JSON # JSON | MYSQL
  mysql: { ... }

limits:
  maxMembers: 50
  baseClaims: 10
  claimsPerMember: 2
  maxClaimsAbsolute: 80
  overclaimEnabled: false
  requireConnectedClaims: false

pvp:
  wildernessPvp: true
  claimPvpDefault: false
  claimPvpDuringWar: true
  allyFriendlyFire: false

diplomacy: { ... }
land: { ... }
bank:
  useVault: true
  currencyName: "credits"

migration:
  factionsPluginName: "Factions"
```

---

## 15. Checklist QA

| # | Cas |
| :--- | :--- |
| 1 | Create / invite / join / leave / kick / disband |
| 2 | Claim protection hors guerre (break refusé) |
| 3 | War declare → break autorisé + packet WarBanner |
| 4 | Ally build rules |
| 5 | Bank deposit/withdraw + log |
| 6 | Permissions rôle Mage → `nexus.power.use` |
| 7 | Restart JSON et MySQL : claims/relations OK |
| 8 | `CovenAPI` consommée depuis stub Nexus |
| 9 | Migration dry-run + réel sur copie |
| 10 | Frontiers client `0x41` cohérentes |

---

## 16. Critères d’acceptation V1

- [ ] Soft-depend MassiveCraft retiré côté WizardCore (après T2)
- [ ] Aucune feature P2W dans Covens
- [ ] Guerre = seul moyen de raid claims (semi-ouvert)
- [ ] API documentée + events pour Nexus/Autels/Mana/Pouvoirs
- [ ] Persistance JSON **et** MySQL testées
