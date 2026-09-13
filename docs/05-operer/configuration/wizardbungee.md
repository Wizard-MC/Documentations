# Configuration de WizardBungee

Les deux fichiers du proxy, clé par clé, et ce qu'il faut savoir avant d'y toucher.
Référence pour un administrateur.

**Statut : livré.** Le proxy tourne en production.

---

## 1. Les deux fichiers

Ils vivent à la racine du dossier du proxy, à côté du jar, et sont **créés au premier
démarrage** s'ils manquent.

| Fichier | Ce qu'il règle | Origine |
|---|---|---|
| `config.yml` | Écouteurs, serveurs, mode d'identité, transfert d'IP, bornes réseau | BungeeCord et Waterfall |
| `optimus.yml` | Les empreintes de client et le gonflage d'effectif | WizardMC |

### Le piège à connaître en premier

> **Une clé absente n'est pas laissée absente : elle est écrite dans le fichier avec sa
> valeur par défaut.**

C'est le comportement du chargeur de configuration du proxy. Un fichier vide devient
donc un fichier complet au premier démarrage, **empreintes par défaut comprises**.

Deux conséquences pratiques :

1. Un `optimus.yml` supprimé par erreur ne produit pas une erreur : il produit un proxy
   qui redémarre avec l'**empreinte publique par défaut**, sans le dire.
2. Ce n'est donc pas la peine de pré-remplir le fichier « pour voir les clés » : il se
   remplit seul.

---

## 2. `optimus.yml` — les clés WizardMC

### 2.1. Les empreintes de client

| Clé | Ce qu'elle règle | Défaut |
|---|---|---|
| `authorized-client-hash` | L'empreinte du client de production. Un client qui n'envoie pas cette valeur est refusé | une valeur publique, **à remplacer** |
| `bypass-client-hash` | Une seconde empreinte acceptée, pour un client compilé localement | une valeur publique, **à remplacer** |

**Les deux sont comparées en temps constant**, et l'une ou l'autre suffit. Il n'y a pas
de notion de privilège attachée à l'empreinte de contournement : elle ouvre exactement
les mêmes droits.

> ⚠️ **Ces deux valeurs doivent rester identiques à celles du client et du serveur de
> jeu.** La même valeur sert d'empreinte de connexion côté proxy **et** de secret de
> poignée de main côté serveur de jeu (`handshakeSecret`, dans la configuration de
> WizardCore). En changer une sans les autres coupe tous les joueurs.

La procédure de changement est au [§5](#5-changer-lempreinte-de-client).

### 2.2. Le gonflage d'effectif

Le proxy peut annoncer plus de joueurs connectés qu'il n'y en a réellement.

| Clé | Ce qu'elle règle | Défaut |
|---|---|---|
| `enable-boost` | Active le gonflage | `false` |
| `boost-mode` | La formule : `STATIC`, `RANDOM`, `DIVISION` ou `MULTIPLICATION` | `DIVISION` |
| `static-boost-player-count` | `STATIC` : le nombre fixe ajouté | 50 |
| `random-boost-player-count` | `RANDOM` : borne haute du tirage, entre 1 et cette valeur | 6 |
| `division-boost-player-count` | `DIVISION` : l'effectif réel divisé par ce nombre est ajouté | 4 |
| `boost-player-multiplication` | `MULTIPLICATION` : l'effectif réel est multiplié par ce facteur | 1.2 |

Les trois dernières clés ne servent que dans leur mode. Changer
`static-boost-player-count` alors que `boost-mode` vaut `DIVISION` n'a aucun effet, et
n'émet aucun avertissement.

> ⚠️ **Le gonflage ferme le serveur plus tôt.** Le compteur gonflé est aussi celui que
> le proxy compare à `player_limit` de `config.yml`. En mode `DIVISION` sur 4 avec une
> limite de 100 places, le proxy commence à refuser des joueurs à **80 joueurs réels**.
>
> C'est un défaut connu, suivi sous `PX-D2` dans le
> [CDC du proxy](../../90-specifications/cdc_wizardbungee.md#9-défauts-relevés). Tant
> qu'il n'est pas corrigé : si vous activez le gonflage, relevez `player_limit` de la même
> proportion.

---

## 3. `config.yml` — les clés qui comptent

Le fichier est celui de BungeeCord et Waterfall, et il est long. Voici les clés qui ont
un effet sur WizardMC, et seulement celles-là.

### 3.1. Identité et routage

| Clé | Ce qu'elle règle | Valeur attendue ici |
|---|---|---|
| `online_mode` | Le proxy vérifie-t-il la session Mojang du joueur | `false` — les joueurs s'authentifient sur le site, pas chez Mojang |
| `ip_forward` | Le proxy transmet-il l'adresse réelle du joueur au serveur de jeu | `true` — sans cela le serveur de jeu voit tous les joueurs à la même adresse |
| `player_limit` | Au-delà, le proxy refuse — voir l'avertissement du §2.2 | selon la capacité |
| `prevent_proxy_connections` | Refuse les joueurs derrière un proxy réseau | au choix |
| `forge_support` | Active les canaux de poignée de main Forge | selon le client |

> ⚠️ **`ip_forward: true` rend le port du serveur de jeu indéfendable.** Avec le
> transfert d'IP, le serveur de jeu croit ce que le proxy lui dit de l'adresse et de
> l'identité du joueur — et il le croirait de n'importe qui d'autre. **Le port du serveur
> de jeu ne doit jamais être joignable depuis l'extérieur.** C'est une règle de pare-feu,
> et c'est la plus importante de cette page.

### 3.2. Bornes réseau

| Clé | Ce qu'elle règle | Défaut |
|---|---|---|
| `timeout` | Délai avant de considérer une connexion morte, en millisecondes | 30000 |
| `connection_throttle` | Délai minimal entre deux connexions d'une même adresse, en millisecondes | — |
| `connection_throttle_limit` | Nombre de connexions tolérées dans ce délai | — |
| `network_compression_threshold` | Taille à partir de laquelle un paquet est compressé | — |

`connection_throttle` est la **vraie** limitation de débit par adresse du proxy. Le
serveur de jeu en a une seconde, indépendante, décrite dans
[le durcissement de WizardSpigot](https://github.com/Wizard-MC/wizardspigot/blob/main/docs/WizardSpigot/SECURITY.md).

La compression ne concerne pas les clients 1.7 : le paquet qui la négocie est apparu en
1.8. La clé reste sans effet sur le parc actuel.

### 3.3. Journalisation

| Clé | Ce qu'elle règle | Recommandation |
|---|---|---|
| `log_commands` | Journalise les commandes passées par les joueurs | `true` |
| `log_pings` | Journalise chaque *ping* reçu | `false` — un scanner noie le journal |

---

## 4. Recharger la configuration

Deux commandes, et elles ne font pas la même chose.

| Commande | Permission | Ce qu'elle recharge réellement |
|---|---|---|
| `/rlconfig` | `bungeecord.command.rlconfig` | **`optimus.yml` seulement** |
| `/greload` | `bungeecord.command.reload` | `config.yml`, les messages, et redémarre les écouteurs |

> ⚠️ **`/rlconfig` annonce « Config reloaded. » y compris pour `config.yml`, alors qu'il
> ne le recharge pas.** Le fichier est bien relu sur le disque, mais ses valeurs ne sont
> pas réappliquées : le proxy garde celles de son démarrage. Défaut suivi sous `PX-D4`.
>
> Pour une modification de `config.yml`, utilisez `/greload` — ou mieux, redémarrez.

`/greload` affiche en retour un avertissement de l'amont qui dit, en substance, que
recharger un proxy n'est pas conseillé et qu'il faut redémarrer dès que possible. **Il
est à prendre au sérieux** : le rechargement ne recrée pas les connexions en cours.

---

## 5. Changer l'empreinte de client

C'est l'opération la plus délicate du proxy, parce qu'elle touche **trois programmes à la
fois**. Lisez la séquence entière avant de commencer.

### Ce qui doit changer ensemble

| Où | Quoi |
|---|---|
| Le client | la constante d'empreinte, compilée dans le jar du client |
| Le proxy | `authorized-client-hash` dans `optimus.yml` |
| Le serveur de jeu | `handshakeSecret` dans la configuration de WizardCore |

### La séquence

1. **Choisir la nouvelle valeur** hors de tout dépôt et de tout document. Une chaîne
   hexadécimale longue, tirée au hasard.
2. **Mettre l'ancienne valeur en empreinte de contournement** sur le proxy
   (`bypass-client-hash`) et **la nouvelle en empreinte autorisée**
   (`authorized-client-hash`). Recharger avec `/rlconfig`. À cet instant, **les deux
   clients fonctionnent** : c'est ce qui évite la coupure.
3. **Publier le nouveau client** par le launcher.
4. **Attendre que le parc soit à jour.** Le launcher impose la mise à jour au
   démarrage, mais une session déjà lancée ne la reçoit pas : laissez passer une nuit.
5. **Changer `handshakeSecret`** sur le serveur de jeu, pour la nouvelle valeur.
6. **Retirer l'ancienne valeur** de `bypass-client-hash`. Recharger.

### Ce qu'il ne faut pas faire

- **Changer les trois en même temps.** Tous les joueurs connectés tombent, et ceux qui
  n'ont pas relancé leur launcher ne peuvent plus entrer.
- **Écrire la valeur dans un message de support, une capture d'écran ou un rapport
  d'incident.** Elle est déjà faible par nature ; inutile de l'affaiblir davantage.
- **Croire que cela authentifie les joueurs.** L'empreinte voyage dans le jar du client,
  que tout joueur télécharge. C'est un filtre de client, pas une authentification. Voir
  `CX-D1` dans [le CDC de la connexion](../../90-specifications/cdc_connexion.md#10-défauts-relevés).

---

## 6. Les secrets de ce proxy

| Clé | Nature |
|---|---|
| `authorized-client-hash` | Empreinte de client — **faible par construction**, voir §5 |
| `bypass-client-hash` | Idem |
| `stats` | Identifiant d'instance, inoffensif |

Cette documentation dit **qu'une** clé existe, où elle se règle et ce qu'elle garantit.
Jamais sa valeur.

---

## 7. Diagnostiquer un refus de connexion

Les causes, dans l'ordre où elles se produisent.

| Ce que le joueur voit | Cause probable | Vérifier |
|---|---|---|
| « Client obsolète » | le client n'annonce pas le bon numéro de protocole | la version du client ; le launcher est-il à jour |
| Rien du tout, déconnexion sèche | la trame a été rejetée par le décodeur : client vanilla, ou champ en trop | le journal du proxy, qui nomme le garde déclenché |
| « Votre client n'est pas à jour, redémarrez votre launcher » | l'empreinte est absente, ou ne correspond à aucune des deux | `optimus.yml`, et la version publiée du client |
| « Serveur plein » à un effectif anormalement bas | le gonflage d'effectif est actif | `enable-boost`, et l'avertissement du §2.2 |
| « Tu dois utiliser le launcher WizardMC pour jouer sur ce serveur ! » après ~5 s **en jeu** | la poignée de main d'après-connexion a échoué | `handshakeSecret` côté serveur de jeu ; les trois valeurs coïncident-elles |

Le dernier cas est le plus instructif : **le joueur est entré**, puis est expulsé cinq
secondes plus tard. Cela veut dire que le proxy l'a accepté et que le serveur de jeu l'a
refusé — donc que les deux valeurs ont divergé.

Le détail de chaque étape est dans
[la connexion de bout en bout](../../90-specifications/cdc_connexion.md).

---

## À lire ensuite

- [CDC WizardBungee](../../90-specifications/cdc_wizardbungee.md) — ce que fait le proxy, et ses défauts
- [La connexion de bout en bout](../../90-specifications/cdc_connexion.md) — les cinq poignées de main
- [Configuration](README.md) — les autres fichiers
- [Runbooks](../runbooks.md) — quand ça casse
