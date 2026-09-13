# CDC TECHNIQUE — La connexion, de bout en bout

> Ce qui se passe entre le moment où un joueur clique « Jouer » dans le launcher et le
> moment où il peut bouger. **Cinq poignées de main**, trois programmes, et un secret
> partagé qui mérite une conversation.

**État : livré.** Tout ce qui est décrit ici tourne. Les défauts relevés au
[§10](#10-défauts-relevés) tournent aussi.

Documents liés :

- [`cdc_wizardbungee.md`](cdc_wizardbungee.md) — le proxy, et ses modifications
- [`cdc_exp_client.md`](cdc_exp_client.md) — le client MCP
- [`SECURITY.md` de WizardSpigot](https://github.com/Wizard-MC/wizardspigot/blob/main/docs/WizardSpigot/SECURITY.md) — le durcissement du serveur de jeu
- [Plages d'identifiants](../07-reference/plages-d-identifiants.md) — le paquet 124

---

## 1. Pourquoi ce document existe

La connexion est le seul chemin de code que **100 % des joueurs empruntent**, à chaque
session, et c'est celui qui est réparti sur le plus de dépôts : le launcher, le client,
le proxy, le serveur de jeu. Une modification d'un seul champ y oblige à redéployer
quatre choses dans le bon ordre.

C'est aussi le chemin où les erreurs sont les plus silencieuses. Un joueur qui se voit
refuser l'entrée lit « Votre client n'est pas à jour, redémarrez votre launcher », quelle
que soit la vraie cause — protocole, empreinte, ou un octet de trop dans une trame.

> **Ce document est la carte à ouvrir avant de toucher à quoi que ce soit dans le chemin
> de connexion.**

---

## 2. La vue d'ensemble

```
   launcher (Rust)                 client MCP              WizardBungee            WizardSpigot
        │                               │                       │                       │
  ① authentifie sur le site             │                       │                       │
     pseudo + UUID + jeton              │                       │                       │
        └── lance le client ───────────▶│                       │                       │
                                        │                       │                       │
                                  ② C00Handshake                │                       │
                                     protocole 10 ─────────────▶│ version connue ?      │
                                        │                       │                       │
                                  ③ LoginStart                  │                       │
                                     [pseudo][empreinte] ──────▶│ empreinte valide ?    │
                                        │                       │                       │
                                  ④ (chiffrement)               │                       │
                                     sauté en mode hors-ligne   │                       │
                                        │                       │                       │
                                        │                 ⑤ Handshake forgé             │
                                        │                    hôte + IP + profil ───────▶│
                                        │                    LoginStart(pseudo, null) ─▶│
                                        │                       │                       │
                                  ◀─────┴───────── LoginSuccess ─┴───────────────────────┘
                                        │                                               │
                                        │           canal PLAY établi, le joueur apparaît
                                        │                                               │
                                  ⑥ paquet 124 ◀───────────────────────── CHALLENGE (nonce)
                                     RESPONSE (jeton) ─────────────────────────────────▶│
                                        │                                   vérifie, ou expulse
```

Les cinq poignées de main numérotées ② à ⑥ sont détaillées ci-dessous. L'étape ① est du
ressort du launcher et du site, et n'apparaît plus jamais ensuite — c'est tout le
problème du [§10](#10-défauts-relevés).

---

## 3. La poignée de main de protocole

**Étape ② du schéma.**

| | |
|---|---|
| **Paquet** | `C00Handshake` — le premier octet du premier paquet de toute connexion Minecraft |
| **Contenu** | numéro de protocole, hôte demandé, port, état suivant (`LOGIN` ou `STATUS`) |
| **Modifié** | oui : le numéro annoncé est **10**, pas 5 |

Le client annonce le protocole **10**. Le proxy n'accepte que les numéros de sa liste, et
5 — le vrai numéro de 1.7.6–1.7.10 — n'y est plus : la constante a été redéfinie à 10.

**Un client Minecraft vanilla est donc refusé ici**, avant d'avoir dit son nom, avec
`outdated_client`. C'est la première barrière, la moins coûteuse, et celle qui écarte
l'essentiel du bruit : les scanners de serveurs, les *bots* écrits contre le protocole
public, les joueurs qui tombent sur l'adresse par hasard.

Les six emplacements où le numéro 10 est écrit, et les conséquences sur les tables de
paquets, sont au [§3 du CDC du proxy](cdc_wizardbungee.md#3-le-protocole-10).

> **Règle `CX-01`.** Un client qui annonce un autre numéro n'a **aucun message
> explicatif** : il lit « client obsolète ». C'est la première chose à vérifier quand une
> connexion échoue sans trace côté serveur.

---

## 4. La poignée de main d'empreinte

**Étape ③ du schéma.**

| | |
|---|---|
| **Paquet** | `LoginStart` — côté client `C00PacketLoginStart`, côté proxy `LoginRequest` |
| **Contenu vanilla** | le pseudo, et rien d'autre |
| **Contenu Optimus** | le pseudo, **puis une seconde chaîne** : l'empreinte du client |

### Côté client

`C00PacketLoginStart.writePacketData` écrit le pseudo puis
`OptimusClientAuth.CLIENT_HASH`. Une constante, la même pour tous les joueurs, compilée
dans le jar.

La lecture est **tolérante** : `readPacketData` relit le pseudo puis, *s'il reste des
octets*, consomme et jette la seconde chaîne. Ce n'est pas de la négligence — c'est ce qui
permet au **serveur intégré** (un monde solo, le mode LAN) de rester joignable par le
même client, sans que la partie serveur du jeu ait besoin de connaître Optimus.

### Côté proxy

`LoginRequest.read` est au contraire **strict** : si le tampon est épuisé après le
pseudo, il lève `BadPacketException("Missing Optimus client hash")`. La trame est rejetée
et la connexion tombe. Un client vanilla 1.7.2 — dont le protocole 4 est encore accepté
à l'étape ② — meurt ici.

Si l'empreinte est présente, `InitialHandler` la compare **en temps constant**
(`MessageDigest.isEqual`) à deux valeurs d'`optimus.yml` : l'empreinte autorisée et une
empreinte de contournement, qui existe pour les clients compilés localement. Aucune des
deux ne correspond : expulsion avec le message au joueur.

### Ce qui n'est pas transmis plus loin

> **Règle `CX-02`.** L'empreinte **s'arrête au proxy**. `ServerConnector` envoie au
> backend `LoginRequest(pseudo, null)`. Le `PacketLoginInStart` du serveur de jeu vient de
> l'artefact NMS non modifié et lit **une** chaîne ; lui en envoyer deux casserait la
> connexion.

---

## 5. Le chiffrement, et pourquoi il n'a pas lieu

**Étape ④ du schéma.**

BungeeCord décide à cet instant, dans le *callback* de `PreLoginEvent` :

- `online_mode: true` → il envoie `EncryptionRequest`, le client répond, la session est
  vérifiée chez Mojang, **et le reste de la liaison est chiffré** ;
- `online_mode: false` → il appelle directement `finish()`.

Le launcher WizardMC calcule lui-même l'UUID du joueur par
`UUID.nameUUIDFromBytes("OfflinePlayer:<pseudo>")` et vérifie qu'il correspond à celui
que le site lui a donné. **Ce calcul n'a de sens qu'en mode hors-ligne** : en mode en
ligne, l'UUID vient de Mojang et ne dépend pas du pseudo.

Il faut donc en tirer la conséquence, franchement :

> **La liaison entre le client et le proxy n'est pas chiffrée.** Tout ce qui y circule
> circule en clair — l'empreinte de l'étape ③ comprise.

Cela ne se corrige pas en activant `online_mode` : les joueurs n'ont pas de session
Mojang valide, le launcher authentifie sur le site. C'est un choix d'architecture
assumé, pas un oubli. Mais il a une conséquence directe sur l'étape ⑥, détaillée en
`CX-D2`.

> **À confirmer.** Le `config.yml` déployé n'est pas versionné : la valeur réelle
> d'`online_mode` est à lire sur le serveur. Tout ce paragraphe découle du comportement
> du launcher.

---

## 6. La poignée de main proxy vers le serveur de jeu

**Étape ⑤ du schéma.**

Le proxy ouvre sa propre connexion vers le backend et **forge** le handshake.

| Champ | Ce que le proxy y met |
|---|---|
| protocole | celui du joueur, tel quel — le backend doit parler la même version |
| hôte | l'hôte demandé, **puis** l'IP réelle du joueur, son UUID et ses propriétés de profil, séparés par des `\0` |
| port, état | inchangés |

C'est le **transfert d'IP** de BungeeCord (`ip_forward: true`). Il explique deux choses
qui surprennent souvent :

1. **Le serveur de jeu voit la vraie adresse du joueur**, pas celle du proxy. Le quota
   de connexions par adresse de WizardSpigot (`WizardLoginThrottle`) porte donc bien sur
   le joueur. Voir
   [`SECURITY.md`](https://github.com/Wizard-MC/wizardspigot/blob/main/docs/WizardSpigot/SECURITY.md).
2. **Le champ `hôte` du handshake devient une donnée de confiance.** N'importe qui
   capable de parler directement au port du backend avec `settings.bungeecord: true`
   peut s'y déclarer n'importe quelle IP et n'importe quel UUID.

> **Règle `CX-03`.** Le port du serveur de jeu ne doit **jamais** être joignable depuis
> l'extérieur. Avec `settings.bungeecord: true`, il n'a aucune défense : c'est le proxy
> qui en tient lieu. C'est une règle de pare-feu, pas une règle de code, et c'est la plus
> importante de ce document.

---

## 7. La poignée de main d'après-connexion, sur le paquet 124

**Étape ⑥ du schéma.**

Le joueur est en jeu. Le canal `PLAY` est ouvert. Et c'est seulement là qu'a lieu la
seule vérification **à défi** de toute la chaîne.

| | |
|---|---|
| **Paquet** | 124, dans les deux sens — `HandshakePacket` |
| **Actions** | `CHALLENGE` (serveur → client), `RESPONSE` (client → serveur), `OK` (serveur → client) |
| **Tenu par** | `HandshakeManager`, dans WizardCore |
| **Jeton** | `SHA-256(secret + "|" + nonce + "|" + pseudo)`, en hexadécimal minuscule |

### Le déroulé

1. À la connexion (`PlayerJoinEvent`, priorité `MONITOR`), le serveur attend **5 ticks** —
   le temps que le canal `PLAY` soit prêt — puis envoie un `CHALLENGE` contenant un
   **nonce de 16 octets** tiré d'un `SecureRandom`.
2. Le client calcule le jeton et répond par `RESPONSE`.
3. Le serveur recalcule le jeton de son côté et compare **en temps constant**
   (`MessageDigest.isEqual`). Égal : il répond `OK`. Différent : expulsion.
4. Un chien de garde tourne **toutes les 10 ticks**. Passé le délai — **5 secondes** par
   défaut — un joueur qui n'a pas répondu est expulsé.

Le message d'expulsion est configurable, et il dit la vérité :
« Tu dois utiliser le launcher WizardMC pour jouer sur ce serveur ! »

### Ce que cette étape attrape vraiment

Elle attrape **un client sans le mod qui parlerait directement au serveur de jeu** : un
tel client ne connaît pas le paquet 124, ne répond rien, et part au bout de cinq
secondes. C'est le filet de sécurité derrière `CX-03` : si le port du backend fuit, la
fuite ne donne pas cinq secondes de jeu, elle donne cinq secondes d'attente.

Elle **n'attrape pas** un client qui a le mod, ni un client qui a lu le mod. Voir
`CX-D2`.

### Les deux contournements prévus

| Contournement | Par quoi | Pourquoi |
|---|---|---|
| Permission `wizardmc.handshake.bypass` | LuckPerms / le gestionnaire de permissions | un outil d'administration sans client complet |
| `isOp()` | l'état d'opérateur | même raison, plus brutale |

Le module se désactive aussi entièrement par `handshakeEnabled: false`.

> **Règle `CX-04`.** Le module **refuse de se désactiver** par la commande ordinaire —
> `setDisactivable(false)`. Seule la configuration peut l'éteindre. L'intention est
> qu'une fausse manœuvre en console n'ouvre pas le serveur.

> **Règle `CX-05`.** Le secret du défi est la **même valeur** que l'empreinte de
> l'étape ③. Le changer oblige à redéployer le client, le proxy **et** le serveur de jeu
> ensemble. C'est le couplage le plus serré de la pile, et c'est aussi un défaut :
> `CX-D2`.

---

## 8. Ce qui a été customisé côté client

Le client MCP porte six modifications directement liées à la connexion. Les quatre
premières la rendent possible ; les deux dernières la rendent supportable.

| # | Où | Quoi | Raison |
|---|---|---|---|
| 1 | `OptimusClientAuth` | le numéro de protocole et l'empreinte, en un seul endroit | une constante dupliquée dans quatre fichiers dérive ; celle-ci est citée, pas recopiée |
| 2 | `C00PacketLoginStart` | écrit l'empreinte après le pseudo ; la relit en tolérant son absence | la tolérance en lecture garde le serveur intégré fonctionnel |
| 3 | `ServerData`, `RealmsSharedConstants`, `MinecraftServer` | le protocole 10 partout où le jeu le compare | sans cela, le client se croit lui-même obsolète |
| 4 | `HandshakePacket` (124) | répond au défi sans interaction du joueur | la réponse doit tomber en moins de cinq secondes |
| 5 | `ServerListEntryNormal` | compare la version du serveur à **notre** numéro | sinon la liste des serveurs affiche « serveur obsolète » en permanence |
| 6 | `PacketUtils.readSafeEnum` | renvoie `null` sur une valeur inconnue au lieu de lever | un serveur plus récent que le client ne fait pas planter le client |

### Ce qui n'a pas été customisé, et qui aurait dû l'être

Le serveur de jeu borne la longueur des chaînes qu'il accepte sur les paquets montants —
c'est `PacketStringLimits`, avec ses paliers justifiés : 64 pour un identifiant, 96 pour
un libellé, 128 pour un jeton de handshake.

**Le client n'a pas d'équivalent.** `PacketUtils.readString` lit jusqu'à
999 999 caractères, et `readSafeEnum` jusqu'à 9 999. Le seul garde-fou restant est le
plafond de trame du décodeur 1.7.10, de l'ordre de deux mégaoctets.

L'exposition est réelle mais limitée : il faut déjà être le serveur auquel le client est
connecté, ou être sur le chemin réseau — ce qui, la liaison n'étant pas chiffrée
([§5](#5-le-chiffrement-et-pourquoi-il-na-pas-lieu)), n'est pas une hypothèse de
laboratoire. Voir `CX-D3`.

---

## 9. Ce qui sécurise la connexion, et ce qui ne la sécurise pas

C'est le tableau à lire si on ne lit qu'une chose de ce document.

| Barrière | Arrête | N'arrête pas |
|---|---|---|
| **Protocole 10** | clients vanilla, scanners, *bots* publics | un client qui lit une constante |
| **Empreinte de client** | tout ce qui n'embarque pas le mod | tout ce qui l'embarque, ou l'a ouvert |
| **Décodeur durci** (3 gardes) | trames malformées, paquets sur-remplis | des paquets valides mal intentionnés |
| **Handshake 124** | un client sans mod parlant au backend | un client avec le mod |
| **Quota de connexions** (proxy + backend) | l'épuisement par connexions répétées | une connexion légitime |
| **Pare-feu sur le port du backend** | l'usurpation d'IP et d'UUID | rien si le port est ouvert |
| **Authentification du joueur** | — | **rien : il n'y en a pas** |

La dernière ligne est le sujet de `CX-D1`. Les six premières forment un filtre de client
efficace, et ce n'est pas rien : elles sont la raison pour laquelle le serveur n'est pas
noyé de *bots*. Mais **un filtre de client n'est pas une authentification de joueur**, et
les confondre est l'erreur que ce document existe pour éviter.

---

## 10. Défauts relevés

| Identifiant | Défaut | Gravité |
|---|---|---|
| **`CX-D1`** | L'identité du joueur n'est jamais vérifiée dans le chemin réseau | **élevée** |
| **`CX-D2`** | Le secret du défi circule en clair deux paquets plus tôt | **élevée** |
| **`CX-D3`** | Le client ne borne pas les chaînes qu'il lit | moyenne |
| **`CX-D4`** | Un seul message pour cinq causes de refus | faible |
| **`CX-D5`** | L'écran d'authentification du client n'est jamais ouvert | faible |

### `CX-D1` — L'identité du joueur n'est jamais vérifiée

Le site authentifie le joueur, le launcher reçoit un pseudo et un UUID, et **rien de
cette authentification n'est transmis au réseau de jeu**. Le client déclare un pseudo ;
le proxy, en mode hors-ligne, le croit.

Ce que les barrières du §9 garantissent : que le logiciel qui se connecte est notre
client. Ce qu'elles ne garantissent pas : que la personne derrière est bien celle qu'elle
dit. Les trois constantes en jeu voyagent toutes dans le jar distribué.

**Correctif proposé.** Un jeton par session, pas une quatrième constante :

1. le site émet, à la connexion du launcher, un jeton court à durée de vie limitée, lié
   au pseudo ;
2. le launcher le passe au client ;
3. le client le présente — soit en troisième champ du `LoginStart`, soit sur le
   paquet 124, qui est déjà fait pour ça ;
4. **le proxy le vérifie auprès du site** et refuse si la réponse n'est pas bonne.

Les briques 1 et 2 existent déjà : le launcher stocke un jeton de session dans le
trousseau du système, et le site expose une API. La pièce manquante est l'étape 4.

La variante « vérifier sur le serveur de jeu plutôt que sur le proxy » est tentante parce
que le paquet 124 existe, mais elle est moins bonne : un joueur refusé aurait déjà été
ajouté au monde, avec ses chargements de chunks et ses événements de connexion.

### `CX-D2` — Le secret du défi circule en clair deux paquets plus tôt

Le défi du paquet 124 prouve que le client connaît un secret. Ce secret est **exactement
la valeur** que le client vient d'envoyer en clair dans son `LoginStart`, deux étapes
plus tôt, sur une liaison non chiffrée.

Quiconque observe une connexion obtient donc le secret, et peut ensuite répondre
correctement à n'importe quel défi, pour n'importe quel nonce et n'importe quel pseudo.
Le défi ne prouve rien de plus que l'étape ③.

Le nonce n'est pas inutile — il empêche de rejouer une **réponse** capturée, puisque le
jeton dépend du nonce et du pseudo. Mais il protège contre le rejeu, pas contre la
connaissance du secret.

**Correctif.** Deux valeurs distinctes : l'empreinte de client, qui est publique par
construction et doit être traitée comme telle, et un secret de défi qui ne transite
jamais. Cela ne rend pas le système solide pour autant — le secret de défi serait encore
dans le jar — et la vraie réponse reste `CX-D1`.

### `CX-D3` — Le client ne borne pas les chaînes qu'il lit

Voir [§8](#8-ce-qui-a-été-customisé-côté-client). Correctif : transposer
`PacketStringLimits` côté client et remplacer les appels à `readString`. Les paliers sont
déjà justifiés et mesurés côté serveur ; il n'y a rien à redécider, juste à porter.

### `CX-D4` — Un seul message pour cinq causes de refus

« Votre client n'est pas à jour, redémarrez votre launcher » est affiché pour une
empreinte vide, une empreinte fausse, et une empreinte de contournement périmée. Le
protocole refusé donne, lui, « client obsolète ». Une trame rejetée ne donne **rien du
tout**.

Un joueur ne peut pas distinguer « ton launcher a une vieille version » de « ton
launcher est bon, c'est le serveur qui a changé de clé sans prévenir ». Le support non
plus.

**Correctif** : des messages distincts — le joueur n'a pas besoin de savoir laquelle des
clés a échoué, mais il a besoin de savoir s'il doit relancer son launcher ou attendre. Et
une trace côté proxy qui, elle, dit précisément laquelle.

### `CX-D5` — L'écran d'authentification du client n'est jamais ouvert

`AuthPacket` (paquet 101) et `GuiAuth` existent dans le client : un écran
d'authentification, ouvert sur réception d'un `OPEN`, fermé sur `CLOSE`. **Aucun greffon
serveur n'émet jamais ce paquet** — la recherche ne trouve aucun producteur dans
WizardCore, WizardHub, le bridge, ni aucun des greffons.

Ce n'est pas grave en soi, c'est du code mort. Mais c'est un indice utile : le besoin de
`CX-D1` avait été vu, et la moitié cliente avait été écrite.

---

## 11. Déployer une modification du chemin de connexion

> **Règle `CX-06`.** Toute modification du chemin de connexion se déploie **client et
> proxy ensemble**, et dans cet ordre : proxy tolérant d'abord, client ensuite, proxy
> strict en dernier.

La raison est le garde `PX-07` du décodeur : depuis qu'un paquet sur-rempli ferme la
connexion, un client qui envoie un champ que le proxy ne lit pas encore **ne se connecte
plus**. L'ordre naïf — client d'abord — coupe tous les joueurs qui ont déjà mis à jour.

La séquence sûre, pour ajouter un champ :

1. **Proxy** : lire le nouveau champ, le rendre **optionnel**. Déployer.
2. **Client** : écrire le nouveau champ. Publier la version par le launcher.
3. Attendre que le parc soit à jour — le launcher force la mise à jour, mais une session
   déjà lancée ne la reçoit pas.
4. **Proxy** : rendre le champ obligatoire. Déployer.

Pour retirer un champ, l'ordre est exactement l'inverse.

---

## 12. Hors périmètre

- **L'authentification du launcher auprès du site** : c'est l'étape ①, et elle est du
  ressort du launcher et du site.
- **Le durcissement du serveur de jeu** une fois le joueur en jeu : paquets inconnus,
  tab-complete, décompression, registre verrouillé. C'est
  [`SECURITY.md` de WizardSpigot](https://github.com/Wizard-MC/wizardspigot/blob/main/docs/WizardSpigot/SECURITY.md).
- **La file d'attente** quand le SMP est plein : [`cdc_wizardqueue.md`](cdc_wizardqueue.md).
- **Les adresses, les ports, les clés.** Ce document dit qu'une empreinte existe, où
  elle se règle et ce qu'elle garantit. Jamais sa valeur.

---

## À lire ensuite

- [CDC WizardBungee](cdc_wizardbungee.md) — le proxy, et le détail de ses modifications
- [Configuration du proxy](../05-operer/configuration/wizardbungee.md) — les clés
- [CDC du client](cdc_exp_client.md) — ce que le client fait du reste
- [Plages d'identifiants](../07-reference/plages-d-identifiants.md) — avant d'ajouter un paquet
