# CDC TECHNIQUE — WizardBungee

> Le proxy. Un fork de **Travertine** (Waterfall + protocole 1.7) portant les
> modifications **Optimus** : un protocole à nous, une poignée de main de connexion,
> un décodeur durci et un compteur de joueurs gonflable.

**État : livré.** Le proxy tourne en production. Ce document décrit l'existant, y
compris ce qui y est cassé.

Documents liés :

- [`cdc_connexion.md`](cdc_connexion.md) — **la connexion de bout en bout** : les quatre
  poignées de main, ce qui est customisé côté client, et ce qui sécurise — ou non — la liaison
- [`cdc_wizardqueue.md`](cdc_wizardqueue.md) — la file d'attente, greffon de ce proxy
- [`cdc_wizardhub.md`](cdc_wizardhub.md) — le lobby, repli du proxy
- [Configuration du proxy](../05-operer/configuration/wizardbungee.md) — les clés, sans le code
- [Plages d'identifiants](../07-reference/plages-d-identifiants.md) — le protocole 10 et le paquet 124

---

## 1. Objet et intention

WizardBungee est **le seul point d'entrée du réseau**. Tout ce qu'un joueur envoie
passe par lui avant d'atteindre un serveur de jeu, et tout ce qu'un serveur de jeu
renvoie repasse par lui. Cette position lui donne trois rôles, et un seul d'entre eux
est celui d'un BungeeCord ordinaire.

| Rôle | Qui le remplit d'ordinaire | Ici |
|---|---|---|
| Router un joueur d'un serveur à l'autre, survivre à la chute d'un backend | BungeeCord | inchangé |
| Parler le protocole 1.7 alors que BungeeCord ne le parle plus | Travertine | patch amont |
| **Refuser tout client qui n'est pas le client WizardMC** | personne | modifications Optimus |

Le troisième rôle est la raison d'être de ce fork, et c'est celui que ce document
détaille. Il repose sur deux choses : un **numéro de protocole qui n'existe pas**
ailleurs, et une **empreinte de client** ajoutée au paquet de connexion.

### Ce que le proxy ne fait pas

> **Il n'authentifie personne.** Le proxy vérifie que le client est le bon client ;
> il ne vérifie pas que le joueur est le bon joueur.

C'est une distinction de fond, et elle est détaillée au [§9](#9-défauts-relevés) sous
`PX-D1`. L'identité du joueur est établie par le site au moment où le launcher s'y
connecte ; elle n'est ensuite **jamais revérifiée** dans le chemin réseau.

---

## 2. Filiation

```
BungeeCord (md_5)
 └── Waterfall (PaperMC)          55 patches — stabilité, configuration, bornes
      └── Travertine (PaperMC)     3 patches — protocole 1.7.x
           └── WizardBungee        modifications Optimus (fr.optimus + éditions en place)
```

L'artefact produit s'appelle encore `Travertine.jar`, la version Maven encore
`1.15-SNAPSHOT`, et le *branding* affiché au démarrage encore « Travertine ». Rien de
cela n'a été renommé : le nom sert de repère pour savoir quelle base amont suivre.

**Java 8 obligatoire**, comme partout dans la pile. Vingt modules Maven.

### 2.1. Ce que contient le dépôt

| Dossier | Contenu | Compilé ? |
|---|---|---|
| `Waterfall/BungeeCord/` | les sources de BungeeCord amont, telles quelles | non |
| `Waterfall/BungeeCord-Patches/` | les 55 patches Waterfall | non |
| `Waterfall-Proxy-Patches/` | les 3 patches Travertine | non |
| **`Travertine-Proxy/`** | **l'arbre des sources patches appliqués** | **oui** |

Le `pom.xml` de la racine ne déclare qu'un module : `Travertine-Proxy`. **C'est le seul
code qui compile, et c'est là que vivent toutes les modifications WizardMC.**

### 2.2. Le piège : les modifications maison ne sont dans aucun patch

> ⚠️ **`./travertine p` effacerait tout le travail WizardMC.**

`scripts/applyPatches.sh` fait, pour chaque étage : `git reset --hard upstream/upstream`
puis `git am` les patches du dossier correspondant. Les modifications Optimus n'étant
dans **aucun** fichier `.patch`, elles ne seraient pas réappliquées : le `reset --hard`
les supprimerait et rien ne les remettrait.

La commande ne peut de toute façon pas s'exécuter en l'état, pour trois raisons
cumulatives et vérifiables :

1. `scripts/build.sh` commence par `git submodule update --recursive --init` ; il n'y a
   **pas de `.gitmodules`** dans le dépôt.
2. `Waterfall/` est versionné en fichiers ordinaires et **ne contient pas de `.git`** ;
   `applyPatch` y appelle `git fetch` et `git am`.
3. L'étage intermédiaire `Waterfall/Waterfall-Proxy/`, que le script produit puis
   consomme, **n'existe pas** dans le dépôt.

**Conséquence pratique :** le dépôt n'est plus un fork à patches, c'est un fork à
sources. L'outillage `travertine` est décoratif, et dangereux. La décision à prendre est
au [§10](#10-backlog) sous `PX-B1`.

---

## 3. Le protocole 10

### 3.1. La décision

| | |
|---|---|
| **Décision** | Le client WizardMC annonce le protocole **10**, et le proxy n'accepte que les versions qu'il connaît |

Le protocole 1.7.6–1.7.10 de Mojang porte le numéro **5**. Dans
`ProtocolConstants`, la constante `MINECRAFT_1_7_6` a été **redéfinie à 10**. Le numéro
5 n'apparaît donc plus nulle part, et en particulier plus dans
`SUPPORTED_VERSION_IDS`.

`InitialHandler` refuse toute version absente de cette liste. Un client Minecraft
vanilla 1.7.10 est donc **expulsé à la poignée de main**, avec le message
`outdated_client`, avant même d'avoir envoyé son pseudo.

### 3.2. Ce que cela implique, et qu'il faut savoir avant d'y toucher

**Les identifiants de paquets restent ceux de 1.7.** Toutes les tables de correspondance
de `Protocol` sont ancrées sur `MINECRAFT_1_7_2` (= 4), et une version se résout dans la
tranche ouverte par l'ancre immédiatement inférieure. Comme 10 est supérieur à 4 et
inférieur à 47 (1.8), le protocole 10 tombe dans la tranche 1.7. **Rien à faire** : c'est
la raison pour laquelle la redéfinition fonctionne sans toucher à cent tables.

**La réécriture d'entités suit.** `EntityMap.getEntityMap` aiguille sur
`case MINECRAFT_1_7_6`, devenu `case 10` : le client WizardMC reçoit bien
`EntityMap_1_7_6`.

**Un vrai client protocole 5 ferait exploser `getEntityMap`** — la méthode finit par
`throw new RuntimeException("Version 5 has no entity map")`. Il ne peut pas y arriver : il
est refusé plus tôt. Mais la borne est là, et elle tombera le jour où quelqu'un
remettra 5 dans `SUPPORTED_VERSION_IDS` sans ajouter la table.

**Un client vanilla 1.7.2 passe la poignée de main.** Le protocole 4 est toujours dans la
liste. Il n'est arrêté qu'ensuite, par l'empreinte de client ([§4](#4-lempreinte-de-client)).

### 3.3. Les six endroits où 10 est écrit

Le numéro est une constante partagée entre deux dépôts. Il apparaît dans le client à
quatre endroits et dans le proxy à un seul — le serveur de jeu l'annonce aussi pour son
propre *ping*.

| Dépôt | Emplacement | Rôle |
|---|---|---|
| MCP | `OptimusClientAuth.PROTOCOL_VERSION` | la source : la poignée de main sortante |
| MCP | `ServerData` | la version annoncée dans la liste des serveurs |
| MCP | `RealmsSharedConstants` | cohérence, sinon l'écran Realms compare à 5 |
| MCP | `MinecraftServer` (*ping* du serveur intégré) | un monde solo reste joignable |
| wizardbungee | `ProtocolConstants.MINECRAFT_1_7_6` | l'acceptation |
| WizardSpigot | `NetHandlerHandshakeTCP` | le backend accepte 10 |

> **Règle `PX-01`.** Le numéro de protocole se change dans ces six emplacements **en un
> seul lot**. Un client qui annonce 10 à un proxy qui attend 11 n'a pas de message
> d'erreur utile : il a `outdated_client`.

---

## 4. L'empreinte de client

### 4.1. Le mécanisme

Le paquet de connexion vanilla (`LoginStart`) contient **un** champ : le pseudo. Optimus
y ajoute **une seconde chaîne**, l'empreinte du client.

```
client MCP                      proxy                          backend Spigot
C00PacketLoginStart       →   LoginRequest.read()
  [pseudo][empreinte]            data = pseudo
                                 hash = empreinte          (obligatoire)
                                      ↓
                            InitialHandler.handle(LoginRequest)
                              comparaison en temps constant
                                      ↓
                                           LoginRequest(pseudo, null)  →
                                           (l'empreinte n'est pas relayée)
```

Trois points méritent d'être lus deux fois.

**L'empreinte est obligatoire au décodage, pas seulement à la validation.**
`LoginRequest.read` lève une `BadPacketException("Missing Optimus client hash")` si le
tampon est épuisé après le pseudo. Un client vanilla ne se fait donc pas expulser
poliment : sa trame est **rejetée**, ce qui ferme la connexion sans message. C'est moins
aimable, et c'est plus robuste : la validation ne peut pas être contournée par un
paquet tronqué.

**La comparaison est à temps constant.** `MessageDigest.isEqual` sur les octets UTF-8,
des deux côtés. Une comparaison `String.equals` aurait rendu l'empreinte devinable
caractère par caractère à la milliseconde près.

**Deux empreintes sont acceptées**, `authorized-client-hash` et `bypass-client-hash`.
La seconde existe pour qu'un développeur puisse se connecter avec un client compilé
localement sans republier l'empreinte de production.

> **Règle `PX-02`.** L'empreinte n'est **jamais** relayée au backend.
> `ServerConnector` envoie explicitement `new LoginRequest(user.getName(), null)`. Le
> `PacketLoginInStart` du serveur de jeu vient de l'artefact NMS non modifié : il lit une
> seule chaîne. Lui en envoyer deux casserait la connexion au backend.

> **Règle `PX-03`.** L'empreinte est exposée aux greffons du proxy par
> `PreLoginEvent.getHash()`. Un greffon peut la lire pour distinguer un client de
> développement d'un client de production. Il ne doit pas la réémettre.

### 4.2. Ce que l'empreinte protège, et ce qu'elle ne protège pas

| Elle empêche | Elle n'empêche pas |
|---|---|
| un client Minecraft vanilla de se connecter | un client modifié de se connecter |
| un joueur qui n'a pas le launcher d'entrer | quelqu'un qui a extrait la constante du jar |
| un *bot* écrit contre le protocole vanilla | un *bot* écrit contre **notre** protocole |

**L'empreinte voyage dans le jar du client, que tout joueur télécharge.** Ce n'est donc
pas un secret au sens cryptographique : c'est une clé de porte distribuée à tous les
locataires. Elle fait un excellent filtre et une mauvaise authentification. Voir
`PX-D1`.

> **Règle `PX-04`.** Les valeurs d'empreinte ne s'écrivent **dans aucune
> documentation**. Les documents disent qu'une clé existe, où elle se règle, et ce
> qu'elle garantit — jamais sa valeur.

---

## 5. Le décodeur durci

`MinecraftDecoder` a reçu trois gardes Optimus, en plus de ceux de Waterfall. Les trois
ferment le canal, retirent le *handler* du *pipeline*, et lèvent une
`BadPacketException` nommée.

| Garde | Condition | Ce qu'elle arrête |
|---|---|---|
| **`PX-05`** | `writerIndex() < 1` ou `readerIndex() >= writerIndex()` | une trame vide ou déjà consommée — un paquet de connexion non traité |
| **`PX-06`** | `read0` lève `IndexOutOfBoundsException` | un paquet qui annonce plus de données qu'il n'en porte |
| **`PX-07`** | il reste des octets lisibles après décodage | un paquet sur-rempli, qui tente de faire lire le paquet suivant de travers |

`PX-07` est le plus utile des trois. Un paquet conforme est intégralement consommé par
son lecteur ; s'il reste quelque chose, soit le lecteur est faux, soit l'émetteur
bricole. Dans les deux cas la connexion ne doit pas continuer.

> **Règle `PX-08`.** Un lecteur de paquet qui ne consomme pas tout son tampon fait
> désormais **tomber la connexion**. C'est la contrainte à garder en tête en ajoutant un
> champ à un paquet : le client et le proxy doivent être déployés ensemble.

Le décodeur porte aussi une liste `blockeds` d'adresses, alimentée par les trois gardes.
Elle ne sert à rien — voir `PX-D3`.

---

## 6. Le compteur de joueurs gonflable

`BungeeCord.getOnlineCount()` peut renvoyer plus de joueurs qu'il n'y en a, selon
`optimus.yml` :

| Mode | Ce qu'il ajoute | Clé |
|---|---|---|
| `STATIC` | un nombre fixe | `static-boost-player-count` |
| `RANDOM` | un tirage entre 1 et N | `random-boost-player-count` |
| `DIVISION` | l'effectif divisé par N | `division-boost-player-count` |
| `MULTIPLICATION` | l'effectif multiplié par un facteur | `boost-player-multiplication` |

Le gonflage est **désactivé par défaut** (`enable-boost: false`), et c'est bien ainsi :
activé, il a un effet de bord qui n'a rien à voir avec l'affichage. Voir `PX-D2`.

> **Règle `PX-09`.** Le gonflage est une décision de communication, pas une mécanique
> de jeu. Aucun système ne doit lire `getOnlineCount()` pour décider quoi que ce soit —
> ni un seuil d'événement, ni un palier de récompense, ni une limite de file.

---

## 7. Configuration

Deux fichiers, à la racine du proxy, créés au premier démarrage s'ils manquent.

| Fichier | Contenu | Rechargeable à chaud |
|---|---|---|
| `config.yml` | BungeeCord et Waterfall : écouteurs, serveurs, `online_mode`, `ip_forward`, bornes | par `/greload` seulement |
| `optimus.yml` | les empreintes de client et le gonflage d'effectif | par `/rlconfig` |

La référence complète des clés est dans
[Configuration du proxy](../05-operer/configuration/wizardbungee.md). Deux mécanismes
méritent une explication ici, parce qu'ils ne se devinent pas à la lecture du YAML.

**Les défauts sont réécrits dans le fichier.** `YamlConfig.get(path, def)` fait, quand la
clé manque : `submap.put(path, def)` puis `save()`. Un fichier vide devient donc un
fichier complet au premier démarrage — **empreintes par défaut comprises**. C'est la
raison pour laquelle `optimus.yml` n'est pas livré dans le dépôt : il s'écrit seul.

**`optimus.yml` ne reçoit ni permissions ni groupes.** `YamlConfig.load` a été
paramétré en `load(doPermissions, doGroups)` ; `OptimusConfig` appelle
`load(false, false)`. Sans cela, le chargeur aurait injecté les blocs `permissions:` et
`groups:` de BungeeCord dans un fichier qui n'en a que faire.

> **Règle `PX-10`.** `/rlconfig` recharge `optimus.yml` **et lui seul**, en pratique.
> Pour `config.yml`, c'est `/greload`. La raison est un défaut, pas une intention :
> `PX-D4`.

---

## 8. Qualité

### 8.1. Ce que la compilation dit

La compilation est `mvn clean package` avec un JDK 8, à la racine. Elle produit
`Travertine-Proxy/bootstrap/target/Travertine.jar`.

**Elle échoue aujourd'hui.** Le module `travertine-protocol` dépend de
`net.md-5:brigadier:1.0.16-SNAPSHOT`, un instantané qui n'est plus publié : le dépôt
d'instantanés de Sonatype ne le sert plus, et celui de md_5 répond 404 sur ses
métadonnées. Aucune version de remplacement n'est déclarée.

Le contournement est connu et vérifié — il est décrit dans
[`BUILD.md`](https://github.com/Wizard-MC/wizardbungee/blob/main/docs/WizardBungee/BUILD.md)
du dépôt. Une fois la dépendance résolue, la compilation passe en une trentaine de
secondes.

> **Règle `PX-11`.** `brigadier` ne sert qu'aux paquets de commandes de 1.13 et
> au-delà (`Commands`, `TabCompleteResponse`). WizardMC ne sert que 1.7. La dépendance
> est donc **retirable**, et c'est la bonne réponse à long terme — voir `PX-B2`.

### 8.2. Ce que les tests ne disent pas

74 tests automatisés passent. **Tous viennent de l'amont.**

| Ce qui est testé | Par qui |
|---|---|
| composants de chat, configuration YAML, bus d'événements, analyse d'adresses, UUID, ordonnanceur, chiffre et compression natifs, limitation de débit amont | BungeeCord / Waterfall |
| **l'empreinte de client** | personne |
| **les trois gardes du décodeur** | personne |
| **le gonflage d'effectif** | personne |
| **la redéfinition du protocole** | personne |

> **Règle `PX-12`.** Une modification Optimus sans test est une modification qu'on ne
> saura pas avoir cassée. Les quatre lignes ci-dessus sont la dette à combler en
> premier — voir `PX-B3`.

---

## 9. Défauts relevés

| Identifiant | Défaut | Gravité |
|---|---|---|
| **`PX-D1`** | Rien n'authentifie le joueur | **élevée** |
| **`PX-D2`** | Le gonflage d'effectif ferme le serveur trop tôt | moyenne |
| **`PX-D3`** | La liste d'adresses bloquées du décodeur ne bloque rien | faible |
| **`PX-D4`** | `/rlconfig` ne recharge pas `config.yml` | moyenne |
| **`PX-D5`** | Les empreintes sont écrites en clair dans trois dépôts | **élevée** |
| **`PX-D6`** | Le dépôt versionne ses artefacts de compilation | faible |
| **`PX-D7`** | Le proxy ne se recompile pas | **élevée** |

### `PX-D1` — Rien n'authentifie le joueur

Le launcher calcule lui-même l'UUID du joueur, par
`UUID.nameUUIDFromBytes("OfflinePlayer:<pseudo>")`, et vérifie qu'il correspond à celui
que le site lui a donné. Ce calcul n'a de sens que si le réseau tourne en
**`online_mode: false`** : en mode en ligne, l'UUID vient de Mojang.

Dans cette configuration, **l'identité d'un joueur est le pseudo qu'il déclare**. Les
trois barrières du chemin de connexion — le protocole 10, l'empreinte de client, la
poignée de main du paquet 124 — reposent toutes sur des constantes qui voyagent dans le
jar du client, distribué à tous les joueurs. Elles arrêtent un client vanilla et un
curieux ; elles n'arrêtent pas quelqu'un qui a ouvert le jar.

Il existe par ailleurs un écran d'authentification dans le client
(`AuthPacket`, paquet 101) : **aucun greffon serveur ne l'ouvre jamais**. L'intention
était là, l'implémentation n'y est pas.

La réponse n'est pas une quatrième constante partagée. C'est un **jeton par session**,
émis par le site au moment où le launcher s'y connecte, transmis par le client et
vérifié par le proxy auprès du site. Le launcher détient déjà un jeton de session ; le
site expose déjà une API. La pièce manquante est la vérification côté proxy.

> **À confirmer avant toute action** : le `config.yml` déployé n'est pas dans le dépôt.
> La valeur réelle d'`online_mode` est à lire sur le serveur. Tout ce qui précède
> découle du comportement du launcher, pas d'une lecture de ce fichier.

### `PX-D2` — Le gonflage d'effectif ferme le serveur trop tôt

`InitialHandler` refuse une connexion quand `bungee.getOnlineCount() > player_limit`. Le
compteur est celui du §6 : **gonflé**. Avec `DIVISION` sur 4 et une limite de 100
places, le proxy commence à refuser des joueurs à **80 joueurs réels**.

Le défaut est latent — `enable-boost: false` par défaut — et il mord le jour où on
l'active, c'est-à-dire le jour d'une ouverture, au pire moment.

Correctif : séparer le compteur affiché du compteur de décision. L'effectif annoncé dans
le *ping* et l'effectif comparé à `player_limit` ne sont pas la même grandeur.

### `PX-D3` — La liste d'adresses bloquées du décodeur ne bloque rien

`MinecraftDecoder` porte un champ `blockeds`, alimenté par les trois gardes, et testé en
tête de `decode`. Mais **un décodeur est créé par connexion** — trois emplacements le
font : `PipelineUtils`, `UserConnection`, `PingHandler`. La liste d'une connexion meurt
avec elle. Elle ne contient jamais que l'adresse du canal qu'on est en train de fermer,
et le test en tête de `decode` ne peut donc jamais être vrai pour une adresse déjà
punie.

Le champ a par ailleurs un effet de bord : `@AllArgsConstructor` génère désormais un
constructeur à cinq arguments là où l'amont en a quatre.

Correctif : ou bien rendre la liste statique et bornée dans le temps — avec un verrou,
parce qu'elle serait alors partagée entre les fils d'exécution de Netty — ou bien la
retirer. La retirer est plus honnête : la limitation de débit par adresse existe déjà,
dans `connection_throttle`.

### `PX-D4` — `/rlconfig` ne recharge pas `config.yml`

`Configuration.reloadConfig()` appelle `adapter.load()`, ce qui relit le fichier YAML
dans la carte de l'adaptateur. Mais **rien ne relit ensuite cette carte dans les champs
de `Configuration`** : `player_limit`, `timeout`, `online_mode` et les autres gardent
leur valeur de démarrage. La commande annonce pourtant « Config reloaded. »

`/greload`, lui, appelle `config.load()`, qui relit les champs, recharge les messages et
redémarre les écouteurs — et affiche en retour l'avertissement de l'amont, qui vaut
d'être lu.

Correctif : que `reloadConfig()` appelle `load(ProxyServer, adapter)`, ou que la commande
ne prétende recharger que `optimus.yml`.

### `PX-D5` — Les empreintes sont écrites en clair dans trois dépôts

La même constante est écrite en dur dans les sources de **trois dépôts** : le client
(`OptimusClientAuth.CLIENT_HASH`), le proxy (le défaut de `OptimusConfig`) et le serveur
de jeu (le défaut de `HandshakeManager`, et celui de `Config`).

Deux problèmes distincts, qui ne se corrigent pas de la même façon.

1. **Un secret en clair dans l'historique Git** y reste après correction. La rotation est
   donc le premier geste, pas le dernier.
2. **Un défaut codé en dur est un défaut qui s'applique en silence.** Un proxy dont
   l'`optimus.yml` a été effacé redémarre avec l'empreinte publique, et personne n'est
   averti.

Correctif : la valeur se lit dans une variable d'environnement côté proxy et côté
serveur de jeu, sans défaut ; le démarrage **échoue** si elle manque. Côté client, le
problème est structurel — une constante dans un jar distribué n'est jamais secrète — et
sa vraie réponse est `PX-D1`.

### `PX-D6` — Le dépôt versionne ses artefacts de compilation

507 des 1250 fichiers suivis sont sous un dossier `target/` : 406 `.class` et 18 `.jar`,
dont `Travertine.jar` lui-même. Un simple `mvn package` modifie **73 fichiers suivis**,
ce qui rend toute relecture de différences illisible et tout *merge* pénible.

Le seul service que cela rend est accidentel, et il est réel : le jar livré étant
versionné, on a encore un artefact déployable alors que la compilation est cassée
(`PX-D7`). C'est ce qui a permis de retrouver `brigadier`.

Correctif : `target/` dans le `.gitignore` et les fichiers retirés du suivi — **après**
avoir archivé le jar livré ailleurs que dans l'arbre des sources.

### `PX-D7` — Le proxy ne se recompile pas

Voir §8.1. Mesuré : `mvn clean package` échoue sur la résolution de
`net.md-5:brigadier:1.0.16-SNAPSHOT`.

C'est le défaut le plus coûteux de la liste, parce qu'il bloque la correction de tous les
autres. Il est contournable immédiatement, et réparable proprement par `PX-B2`.

---

## 10. Backlog

| Identifiant | Chantier | Pourquoi maintenant |
|---|---|---|
| **`PX-B1`** | Trancher : fork à patches ou fork à sources | tant que c'est ambigu, une commande de l'outillage amont peut effacer le travail |
| **`PX-B2`** | Retirer `brigadier` et le support 1.9+ | rend la compilation reproductible sans dépendance fantôme |
| **`PX-B3`** | Tester les quatre modifications Optimus | voir `PX-12` |
| **`PX-B4`** | Jeton de session vérifié auprès du site | voir `PX-D1` |
| **`PX-B5`** | Sortir les empreintes des sources | voir `PX-D5` |

### `PX-B1`, et pourquoi le fork à sources est la bonne réponse

Le dépôt est déjà un fork à sources : `Travertine-Proxy/` est la vérité, l'outillage ne
tourne pas, et `Waterfall/` n'est qu'une copie de référence. Deux chemins en sortent.

**Reconstituer la chaîne de patches** rendrait les mises à jour amont confortables. Mais
Travertine n'est plus maintenu, Waterfall a été remplacé par Velocity côté PaperMC, et la
mise à jour qu'on attendrait n'arrivera pas. On paierait la rigueur sans en toucher le
bénéfice.

**Assumer le fork à sources** coûte une chose : retrouver, le jour où on veut un
correctif amont, ce qui est à nous. C'est exactement ce que documente
[`MODIFICATIONS.md`](https://github.com/Wizard-MC/wizardbungee/blob/main/docs/WizardBungee/MODIFICATIONS.md).
Le coût est payé.

**Recommandation : assumer le fork à sources**, retirer `scripts/` et le script
`travertine`, garder `Waterfall/` en lecture seule comme référence de comparaison, et
tenir la liste des modifications à jour.

---

## 11. Hors périmètre

Ce document ne couvre pas :

- **la file d'attente**, qui est un greffon du proxy et a son propre cahier des charges
  ([`cdc_wizardqueue.md`](cdc_wizardqueue.md)) ;
- **le durcissement du serveur de jeu**, qui est une autre couche
  ([`SECURITY.md` de WizardSpigot](https://github.com/Wizard-MC/wizardspigot/blob/main/docs/WizardSpigot/SECURITY.md)) ;
- **le détail des quatre poignées de main**, qui est dans
  [`cdc_connexion.md`](cdc_connexion.md) ;
- **l'infrastructure** : adresses, ports, hébergement. Rien de cela n'a sa place dans
  cette documentation.

---

## À lire ensuite

- [La connexion, de bout en bout](cdc_connexion.md) — les quatre poignées de main
- [Configuration du proxy](../05-operer/configuration/wizardbungee.md) — les clés
- [CDC WizardQueue](cdc_wizardqueue.md) — la file d'attente
- [Architecture logicielle](../01-projet/architecture-logicielle.md) — la place du proxy
