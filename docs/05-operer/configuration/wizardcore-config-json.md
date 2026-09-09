# Configuration de WizardCore — `config.json`

Les soixante-quatorze clés du fichier principal de WizardCore, ce qu'elles règlent, et
les valeurs par défaut. Référence d'administration.

**Statut : livré.**

---

## 1. Où le fichier vit, et comment il est lu

| | |
|---|---|
| **Emplacement** | `plugins/WizardCore/config.json` |
| **Format** | JSON. Une clé absente prend sa valeur par défaut. |
| **Rechargement** | Au démarrage du serveur. Quelques modules ont une commande de rechargement. |

### La règle des clés absentes

Une clé absente n'est pas une erreur : elle prend sa valeur par défaut. Ce qui veut
dire que **le fichier peut rester court** — on n'y écrit que ce qu'on change.

Corollaire : un fichier qui contient les soixante-quatorze clés est plus difficile à
relire qu'un fichier qui en contient cinq.

### La règle des `*StorageType`

Chaque module choisit indépendamment `JSON` ou `MYSQL`. Un module dont la clé est
absente **suit celle du Nexus** — sauf le Forgeron, qui suit celle du Mana.

Les identifiants MySQL sont partagés par tous les modules.

> **Une base injoignable doit faire retomber sur JSON, pas empêcher le démarrage.**
> Perdre l'avancement de tout le monde pour une ligne oubliée serait cher payé.

---

## 2. Les secrets

`mysqlPassword` et `azLinkToken` sont des secrets. Ils n'ont **jamais** leur place
dans un dépôt, une capture d'écran, un rapport d'incident ou un message de support.

Cette documentation dit qu'une clé existe ; elle ne dira jamais sa valeur.

---

## 3. Les clés

Chaque ligne donne la clé, son type, et ce qu'elle règle. Quand la valeur par défaut
est connue, elle est dans la description.


### Infrastructure et butin général

| Clé | Type | Ce qu'elle règle |
|---|---|---|
| `apiUrl` | String | — |
| `azLinkToken` | String | — |
| `minimumLoots` | int | — |
| `maximumLoots` | int | — |

### Persistance

| Clé | Type | Ce qu'elle règle |
|---|---|---|
| `nexusStorageType` | NexusStorageType | Persistance Nexus : JSON (fichier local) ou MYSQL (GlobalAPI Database). |
| `mysqlHost` | String | — |
| `mysqlPort` | int | — |
| `mysqlDatabase` | String | — |
| `mysqlUsername` | String | — |
| `mysqlPassword` | String | — |
| `contractsStorageType` | NexusStorageType | Persistance Contrats : JSON ou MYSQL. Si null / absent → même backend que nexusStorageType (credentials MySQL partagés). |
| `altarsStorageType` | NexusStorageType | Persistance Autels : JSON ou MYSQL. Si null / absent → même backend que nexusStorageType. |
| `manaStorageType` | NexusStorageType | Persistance Mana : JSON ou MYSQL. Si null / absent → même backend que nexusStorageType. |
| `forgeronStorageType` | NexusStorageType | Persistance Forgeron (état PNJ + logs échange) : JSON ou MYSQL. Si null / absent → même backend que manaStorageType. |
| `powersStorageType` | NexusStorageType | Persistance cooldowns / pouvoir actif : JSON ou MYSQL. Si null / absent → même backend que nexusStorageType. MySQL étend la table `wizard_nexus` ; JSON → `powers.json`. |
| `erasStorageType` | NexusStorageType | Persistance Ère courante : JSON (`eras.json`) ou MYSQL (`wizard_eras`). Si null / absent → même backend que nexusStorageType. Alias historique accepté en docs : `seasonStorageType`. |
| `seasonStorageType` | NexusStorageType | Alias Gson / config legacy — synchro vers erasStorageType. |
| `shopStorageType` | NexusStorageType | Persistance boutique comptes (Gemmes / Dust) — fallback Nexus. |
| `wikiStorageType` | NexusStorageType | Persistance wiki in-game — fallback Nexus / JSON. |
| `magicStorageType` | NexusStorageType | Persistance Magie joueur (Essence / grimoire) : JSON ou MYSQL. Si null / absent → même backend que nexusStorageType. Fichier / table : `wizard_player_magic`. |

### Nexus

| Clé | Type | Ce qu'elle règle |
|---|---|---|
| `nexusShardBase` | double | Formule Éclats : floor(base level^exponent). |
| `nexusShardExponent` | double | — |
| `nexusMaxLevel` | int | — |
| `nexusSaveIntervalMinutes` | int | Intervalle de flush dirty (minutes). |
| `nexusBlockMaterial` | String | Matériau du bloc Nexus (défaut: NEXUS / ID 223). |
| `nexusDestroyChannelSeconds` | int | Canalisation destroy Nexus ennemi (secondes, défaut 60) — N-09. |
| `nexusDestroyRadius` | double | Rayon max autour du Nexus pendant la canalisation. |
| `nexusDestroyCooldownHours` | int | CD global destroy pour le Coven attaquant (heures, défaut 48). |
| `nexusDestroyMode` | String | RESET = niv1/0 shards ; MALUS = -N niveaux. |
| `nexusDestroyMalusLevels` | int | Niveaux retirés si mode MALUS (défaut 2). |
| `nexusLockDuringWar` | Boolean | Empêche le retrait amical du Nexus pendant une guerre. |

### Contrats

| Clé | Type | Ce qu'elle règle |
|---|---|---|
| `contractsPerPlayerPerDay` | int | Nombre de contrats quotidiens par joueur. |

### Autels Sacrés

| Clé | Type | Ce qu'elle règle |
|---|---|---|
| `altarShardReward` | int | Récompense Éclats à la capture (défaut 15). |
| `altarCaptureSeconds` | int | Durée capture en secondes (défaut 45). |
| `altarCaptureRadius` | double | Rayon de capture en blocs (défaut 3). |
| `altarCooldownSeconds` | int | Cooldown global en secondes (défaut 3600). |
| `altarCataclysmShardMultiplier` | int | Multiplicateur Éclats Autels pendant Cataclysme (défaut 3 → 45 si reward=15). |
| `altarSuccessPacketRadius` | double | Rayon d'envoi du packet succès Autel (blocs, défaut 100). |
| `altarBonusMaxPerType` | int | Cap stacks bonus par type d'Autel (anti-snowball, défaut 2). |
| `altarBonusGlobalMax` | int | Cap global d'Autels contribuant aux bonus (défaut 5). |

### Mana Brut et Forgeron

| Clé | Type | Ce qu'elle règle |
|---|---|---|
| `manaItemMaterial` | String | Matériau de l'item Mana Brut (ex: GLOWSTONE_DUST). |
| `manaItemMaxStack` | int | Stack max de l'item Mana Brut (défaut 64). |
| `manaGenerationIntervalMinutes` | int | Intervalle génération passive (minutes, défaut 30). |
| `manaPerClaim` | int | Mana Brut produit par claim à chaque tick de génération (défaut 1). |
| `manaStatusIntervalSeconds` | int | Intervalle d'envoi du packet statut Mana aux porteurs (secondes, défaut 2). |
| `forgeronMoveIntervalMinutes` | int | Intervalle de déplacement du Forgeron (minutes, défaut 60). |

### Pouvoirs de Coven

| Clé | Type | Ce qu'elle règle |
|---|---|---|
| `powerShieldDurationSeconds` | int | Durées d'effet (secondes). |
| `powerPortalDurationSeconds` | int | — |
| `powerCurseDurationSeconds` | int | — |
| `powerCataclysmDurationSeconds` | int | — |
| `powerShieldCooldownSeconds` | int | Cooldowns faction (secondes). |
| `powerPortalCooldownSeconds` | int | — |
| `powerCurseCooldownSeconds` | int | — |
| `powerCataclysmCooldownSeconds` | int | — |
| `powerPortalCharges` | int | Charges max du Portail des Ombres (défaut 5). |
| `powerCurseDropChancePercent` | int | Chance (%) de drop item précieux sous Malédiction (défaut 40). |
| `powerCataclysmDebuffIntervalSeconds` | int | Intervalle de re-application des debuffs Cataclysme (secondes, défaut 5). |

### Ères

| Clé | Type | Ce qu'elle règle |
|---|---|---|
| `eraCataclysmPowerCooldownDivisor` | int | Diviseur des CD pouvoirs pendant le Grand Cataclysme (E-06, défaut 2 → CD /2). |
| `eraCataclysmAnnounce` | Boolean | Broadcast joueurs à l'entrée en phase Cataclysme (défaut true). |
| `eraDarknessOmbreBonusShards` | int | S4-T4 — Ténèbres : bonus Éclats capture Autel Ombre. |
| `eraDarknessNightMobDamageFactor` | double | S4-T4 — Ténèbres : facteur dégâts mobs la nuit (1.0–1.5). |
| `eraLightClaimRegen` | Boolean | S4-T4 — Lumière : regen soft en claim (tous Covens). |
| `eraLightLumiereHomeBonus` | Boolean | S4-T4 — Lumière : bonus home si Autel Lumière possédé. |
| `eraChaosForgeronMoveMinutes` | int | S4-T4 — Chaos : intervalle Forgeron (minutes). |
| `eraChaosWarDeclareCostFactor` | double | S4-T4 — Chaos : facteur coût déclaration guerre (0.9 = −10 %). |

### Boutique

| Clé | Type | Ce qu'elle règle |
|---|---|---|
| `shopBaseHomeSlots` | int | Homes de base avant achats extra (défaut 3). |
| `shopMaxExtraHomes` | int | Max slots /home achetables (défaut 5 extras → total base+5). |
| `shopDustDailyCap` | int | Cap Poussière gagnable / jour (anti-farm, défaut 200). |
| `shopGiftDailyCap` | int | Cap cadeaux cosmétiques / jour (défaut 5). |
| `shopUtilityCooldownSeconds` | int | Cooldown /workbench et /enderchest (secondes, défaut 300). |

### Autres

| Clé | Type | Ce qu'elle règle |
|---|---|---|
| `handshakeEnabled` | Boolean | S5-T4 / C-01 — handshake launcher post-join. |
| `handshakeTimeoutSeconds` | int | Timeout secondes (défaut 5). |
| `handshakeSecret` | String | Secret SHA-256 (doit matcher `OptimusClientAuth.CLIENT_HASH` côté MCP). |
| `handshakeKickMessage` | String | Message kick (codes & ou §). |

---

## 4. Les clés qu'il faut comprendre avant de les changer

Quelques réglages ont des effets qui dépassent leur intitulé.

### `nexusShardBase` et `nexusShardExponent`

Ensemble, ils décident de toute la courbe de progression des Covens. Le coût d'un
niveau est la base multipliée par le niveau élevé à l'exposant.

| Ce qu'on change | Effet |
|---|---|
| Monter la base | Tout devient plus lent, y compris les premiers niveaux |
| Monter l'exposant | Les premiers niveaux restent rapides, les derniers deviennent hors de portée |

**Ne pas toucher en cours d'Ère.** Un Coven qui a dépensé des Éclats sous une courbe
et en dépense sous une autre n'a aucun moyen de comprendre ce qui lui arrive.

### `altarCaptureSeconds` et `altarCaptureRadius`

Quarante-cinq secondes et trois blocs. Ce sont les deux nombres qui rendent une
capture **défendable** : ils donnent à un défenseur prévenu le temps d'arriver et
l'obligent à tenir un espace réduit.

Descendre la durée rend les captures indéfendables. La monter les rend impossibles à
réussir dès qu'un seul adversaire traîne dans le secteur.

### `altarBonusMaxPerType` et `altarBonusGlobalMax`

Les deux garde-fous anti-domination. Sans eux, un Coven qui prend les sept Autels
cumule sept bonus, et la fin d'Ère n'a plus d'intérêt.

Les relever est le réglage le plus sûr pour casser l'équilibre du serveur.

### `altarCataclysmShardMultiplier`

Trois par défaut, soit quarante-cinq Éclats par capture pendant le Cataclysme. C'est
ce qui rend les trois derniers jours capables de renverser une Ère.

Le baisser supprime la fenêtre de rattrapage ; le monter rend les quarante-deux
premiers jours dérisoires.

### `nexusDestroyChannelSeconds`

Soixante secondes. C'est le temps dont dispose un défenseur pour arriver. Une
destruction instantanée serait indéfendable, donc arbitraire.

### `nexusDestroyCooldownHours`

Quarante-huit heures. Empêche le harcèlement d'un même Coven jour après jour.

### `nexusDestroyMode` et `nexusDestroyMalusLevels`

`RESET` remet le Nexus au premier niveau avec zéro Éclat ; `MALUS` lui retire
quelques niveaux.

`MALUS` est le choix doux : perdre deux niveaux coûte une campagne, perdre tout coûte
une Ère. Sur un serveur jeune, `MALUS` évite de décourager un Coven battu une fois.

### `nexusLockDuringWar`

Empêche le retrait amical du Nexus pendant une guerre. Sans lui, un Coven attaqué
retire son propre Nexus pour le mettre à l'abri, et la guerre n'a plus d'enjeu.

### `shopDustDailyCap`

Deux cents par jour. C'est ce qui empêche la Poussière d'Étoile de devenir une
monnaie qu'on farme — et un cosmétique obtenu en huit heures de grind n'est plus un
cosmétique.

### `shopGiftDailyCap`

Cinq par jour. Empêche qu'un compte serve de réservoir pour alimenter les autres.

### `shopUtilityCooldownSeconds`

Trois cents secondes sur `/workbench` et `/enderchest`. C'est ce qui fait que ces
achats restent du confort : un accès permanent et instantané à son coffre serait un
avantage tactique.

### `contractsPerPlayerPerDay`

Trois. C'est le plancher de revenu en Éclats d'un Coven — ce qu'il gagne en jouant
normalement, sans risque. Le monter réduit l'intérêt des Autels ; le baisser pénalise
les joueurs qui ne sortent pas.

---

## 5. Les modificateurs de thème d'Ère

Les clés `eraDarkness*`, `eraLight*` et `eraChaos*` portent les effets des trois
thèmes. La règle qui les encadre :

> **Légers seulement.** Jamais de butin supplémentaire, jamais de contournement de
> protection hors guerre.

Un thème doit changer l'ambiance et la tactique, pas le rapport de force. Un thème qui
donnerait trente pour cent de butin en plus rendrait les Ères précédentes dérisoires
et celles d'après décevantes.

---

## 6. Avant de changer quoi que ce soit

1. **Sauvegarder.** Toujours. Voir [Exploitation quotidienne](../exploitation.md).
2. **Un seul réglage à la fois.** Deux changements simultanés donnent un résultat
   qu'on ne sait pas attribuer.
3. **Noter la valeur précédente.** Sans relevé, on ne saura pas si le correctif a
   marché.
4. **Pas en cours d'Ère pour les courbes.** `nexusShardBase`, `nexusShardExponent`,
   `nexusMaxLevel` : ce sont les règles du jeu en cours.
5. **Écrire la raison.** Dans le fichier, à côté de la valeur. Un nombre sans
   justification sera changé au hasard dans six mois par quelqu'un qui ne saura pas
   pourquoi il valait ça.

---

## À lire ensuite

- [Les fichiers YAML de WizardCore](wizardcore-fichiers-yml.md) — Autels, Ères, magie, boutique
- [Vue d'ensemble de la configuration](README.md)
- [Équilibrage](../../03-gdd/equilibrage.md) — avant de toucher à un nombre
- [Exploitation quotidienne](../exploitation.md) — sauvegardes et incidents
