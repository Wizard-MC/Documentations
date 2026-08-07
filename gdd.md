# WizardMC V4 — GDD « Coven SMP »

## 1. Identité

| Champ | Valeur |
| :--- | :--- |
| **Nom** | WizardMC – *Les Terres Fracturées* |
| **Mode** | SMP Semi-RPG avec **Covens** (guildes / nations) |
| **Thème** | Magie, sorcellerie, gestion de territoire, diplomatie |
| **Client** | MCP 1.7.10 obligatoire (`wizardmc-clean`) |
| **Monétisation** | Zéro P2W (cosmétiques / confort / Pass) |

### Vision

> *WizardMC est un serveur Semi-RPG où les joueurs forment des Covens, développent leurs territoires, et influencent l’histoire des Terres Fracturées. Guerres, alliances, magie, construction : chaque saison est une nouvelle ère.*

### Principes fondamentaux

1. **Accessibilité** — On peut briller sans être un pro du PvP.
2. **Progression** — Puissance personnelle **et** territoire du Coven évoluent.
3. **Stratégie** — Diplomatie, économie et construction valent le combat.
4. **Immersion** — Lore vivant, Ères narratives, FX client.
5. **Équilibre** — Aucun avantage payant ; skill + organisation.

---

## 2. Ce qui reste / change / s’ajoute

### Conservé (mécaniques livrées ou spécifiées)

- Nexus (niveaux, Éclats, pouvoirs actifs)
- Autels (capture 45s, CD, drama global)
- Mana Brut + Forgeron itinérant (convois à risque)
- Pouvoirs du Coven (Bouclier, Portail, Malédiction, Cataclysme)
- Cycles 45 jours + reset partiel + Panthéon
- Client MCP (HUD, FX, sons)
- Boutique zéro P2W

### Adapté (framing SMP)

| Avant (Faction) | Après (Coven SMP) |
| :--- | :--- |
| Claims = bunkers PvP | Claims = **villes / villages / forteresses** |
| PvP = objectif unique | PvP = **outil** parmi d’autres |
| Autels = shards only | Autels = **shards + bonus de type** |
| Mana = anti-AFK PvP | Mana = **ressource stratégique + commerce** |
| Saisons = reset ranking | **Ères** narratives + soft-mods |
| Factions MassiveCraft | Plugin **Covens** (`WizardCovens`) |

### Ajouté

- Rôles Coven (Maire/Leader, Officier, Builder, Merchant, Farmer, Mage, Guard…)
- Diplomatie structurée (allié / ennemi / trêve / guerre déclarée)
- Banque Coven, taxes optionnelles, logs
- Déblocages **ville** liés au Nexus (murailles, tour, forge, enchant…)
- Magie utilitaire & compagnons (stubs V1)
- Claims protégés hors guerre ; raids ouverts en guerre

---

## 3. Univers & map

- **Contexte** : monde fracturé par la guerre des Arcanes. Les sorciers se regroupent en **Covens**.
- **Map** : zones nommées (Plaine des Murmures, Désert de Cristal, Forêt d’Ébène, Montagnes du Crépuscule…) — influence butins, contrats, types d’Autels.
- Détail spawn / POI : [cdc_spawn_map.md](cdc_spawn_map.md)

---

## 4. Loop de jeu

### Quotidien (joueur)

1. Se connecter → HUD Nexus / Coven / danger Mana
2. Contrats / quêtes (Éclats, Poussière d’Étoile)
3. Build / farm / rôle (forge, commerce, défense)
4. Sortie Autel ou convoi Mana (avec escorte éventuelle)
5. Diplomatie (messages, traités) ou guerre si ouverte
6. Améliorer le Nexus → bâtiments + pouvoirs

### Hebdomadaire (Coven)

- Tenir / contester Autels
- Négocier alliances
- Préparer convois massifs
- Monter le Nexus vers la Forteresse (Niv 10)

### Par Ère (45 jours)

- Phase normale → fin d’ère (boost Autels, boss, CD pouvoirs /2)
- Reset Nexus / cooldowns / Autels owners
- Conservation : claims, builds, stuff, membres
- Panthéon + titres pour le Coven vainqueur

---

## 5. Piliers mécaniques (liens CDC)

| Pilier | CDC |
| :--- | :--- |
| Covens (social, claims, diplo, banque) | [cdc_covens.md](cdc_covens.md) |
| Nexus / ville | [cdc_nexus.md](cdc_nexus.md) |
| Autels | [cdc_autels_sacres.md](cdc_autels_sacres.md) |
| Mana Brut | [cdc_mana_brut.md](cdc_mana_brut.md) |
| Pouvoirs | [cdc_pouvoirs.md](cdc_pouvoirs.md) |
| Ères | [cdc_eres.md](cdc_eres.md) |
| Client | [cdc_exp_client.md](cdc_exp_client.md) |
| Boutique | [cdc_boutique.md](cdc_boutique.md) |
| Magie | [cdc_magie.md](cdc_magie.md) |
| Compagnons | [cdc_compagnons.md](cdc_compagnons.md) |

---

## 6. Règles PvP (semi-ouvert)

| Zone | Règle |
| :--- | :--- |
| Wilderness | PvP libre |
| Claim hors guerre | Protégé (pas de break/place ennemis ; PvP claim off par défaut) |
| Claim en **guerre** | Raid / PvP autorisés ; pouvoirs Coven pleinement utiles |
| Safezones (spawn) | PvP off |

Les pouvoirs (Bouclier, Cataclysme…) restent des **swing states** de guerre / défense, pas le seul contenu du serveur.

---

## 7. Monétisation (rappel)

Rien n’achète : Éclats, Mana, niveaux Nexus, sorts combat, win conditions.  
Boutique = cosmétiques, confort, Pass (défis cosmétiques). Voir [cdc_boutique.md](cdc_boutique.md).

---

## 8. Stack technique (référence)

- Serveur : WizardSpigot 1.7.10 + WizardCore + **WizardCovens** (cible)
- Client : wizardmc-clean (MCP)
- Persistance : JSON ou MySQL par module
- Identifiant social : `coven_id` (String)

Le code Faction actuel continue de fonctionner derrière `FactionAdapter` jusqu’au swap Covens.
