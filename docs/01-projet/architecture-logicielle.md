# Architecture logicielle

Les dépôts de WizardMC, ce que chacun possède, et dans quel ordre ils se
chargent. À lire avant de toucher à quoi que ce soit : savoir **qui possède une
donnée** évite la moitié des bugs de ce projet.

**Statut : livré.** Cette page décrit l'existant.

---

## 1. Les dépôts

| Dépôt | Nature | Ce qu'il possède |
|---|---|---|
| **WizardSpigot** | Fork serveur Bukkit/Spigot 1.7.10 | Les entités natives (créatures, compagnons, montures, PNJ, projectiles), l'enregistrement des paquets maison, l'API `fr.wizardmc.api` |
| **WizardCore** | Greffon serveur | Magie, Nexus, Autels, Mana Brut, Pouvoirs, Ères, contrats, boutique, coffres, enchères, occurrences, chat, profils |
| **WizardMobs** | Greffon serveur | Le catalogue des créatures hostiles, les bêtes paisibles, les montures, l'apparition naturelle, le butin, les nuées |
| **WizardQuest** | Greffon serveur | Les quêtes, le journal, le suivi, les récompenses, les déblocages de montures |
| **WizardPets** | Greffon serveur | Les compagnons : les quatre espèces, la progression, l'équipement, les friandises, l'apprivoisement, la reproduction |
| **WizardIntro** | Greffon serveur | La cinématique d'arrivée et le choix de l'école |
| **WizardCovens** | Greffon serveur | Le socle social : Covens, claims, rôles, relations, guerre, banque |
| **MCP** | Fork client Minecraft 1.7.10 | Tout l'affichage : moteur 3D, modèles animés, HUD, interfaces, sons, réception des paquets |
| **ASSETS** | Dépôt de ressources | Modèles Blockbench, textures, sons, et leur inventaire |

### Le proxy

| Dépôt | Nature | Ce qu'il possède |
|---|---|---|
| **WizardBungee** | Fork Travertine — Waterfall avec le support du protocole 1.7 | Le routage entre serveurs, la poignée de main, le repli sur expulsion |

C'est lui qui est dans le chemin de **chaque** tentative de connexion, et c'est la raison
pour laquelle la file d'attente y vit plutôt que sur un backend — voir
[cdc_wizardqueue](../90-specifications/cdc_wizardqueue.md) §2.1.

### Autour du jeu

| Dépôt | Nature | Ce qu'il possède |
|---|---|---|
| **Launcher** | Application Rust | L'authentification, l'installation et la mise à jour du client |
| **WizardCloud** | Service de distribution | Les manifestes signés et le téléchargement différentiel du client |
| **Website** | Application Laravel | Le site public, la boutique, le forum, l'administration, l'API du launcher et du bot |
| **WizardBot** | Bot Discord TypeScript | La liaison des comptes et le salon en miroir du chat en jeu |
| **WizardMC-Bridge** | Greffon serveur | Le pont HTTP signé entre le serveur, le site et Discord |

Ces cinq-là **n'ont pas de CDC dans ce dépôt** : le launcher et le site documentent
chez eux, les trois autres ne sont pas encore spécifiés. Voir
[`etat-du-serveur.md`](etat-du-serveur.md) §1.

### En cours d'écriture

| Nom | Rôle | Spécification |
|---|---|---|
| **WizardHub** | Le serveur lobby et le repli du proxy, greffon **WizardSpigot** | [cdc_wizardhub](../90-specifications/cdc_wizardhub.md) |
| **WizardQueue** | La file d'attente de connexion, greffon **WizardBungee** | [cdc_wizardqueue](../90-specifications/cdc_wizardqueue.md) |

---

## 2. Qui dépend de qui

```text
              WizardSpigot  (fork serveur : entités, paquets, API)
                     │
   ┌──────┬──────┴──┬──────────┬──────────┬──────────┐
   │      │         │          │          │          │
WizardCore│    WizardMobs  WizardQuest WizardPets WizardIntro
   │  WizardCovens  │          │          │          │
   └──────┴─────────┴────┬─────┴──────────┴──────────┘
                            │  paquets maison 95–132
                            ▼
                          MCP  (client : tout l'affichage)
                            ▲
                            │  modèles, textures, sons
                          ASSETS
```

### Dépendances déclarées

| Greffon | Obligatoire | Facultatif |
|---|---|---|
| **WizardCore** | GlobalAPI, Essentials, Vault, WizardCovens | WorldGuard, WizardMobs |
| **WizardMobs** | *(rien)* | WorldGuard, WizardCore |
| **WizardQuest** | *(rien)* | WizardCore, WizardMobs |
| **WizardIntro** | *(rien)* | WizardCore |

Trois des quatre greffons tournent **seuls**. Ce n'est pas un hasard et il faut le
préserver : un greffon qui refuse de démarrer parce qu'un autre manque rend le
serveur indémarrable au pire moment. Quand une dépendance facultative est absente,
le greffon perd une capacité nommée et le dit au démarrage.

| Greffon | Sans sa dépendance facultative | Ce qu'on perd |
|---|---|---|
| WizardMobs | sans WizardCore | Le niveau des créatures ne suit plus la puissance du joueur ; la difficulté devient uniforme |
| WizardMobs | sans WorldGuard | Le découpage en régions est ignoré ; plus de décalage d'hostilité par zone |
| WizardQuest | sans WizardMobs | Les objectifs qui visent une espèce custom ne peuvent plus se valider |
| WizardQuest | sans WizardCore | Les récompenses en Éclats ne sont plus versées |
| WizardIntro | sans WizardCore | L'école choisie est stockée par WizardIntro seul, la magie ne la lit pas |
| WizardCore | sans WorldGuard | Les régions où la magie est interdite ne s'appliquent pas ; un avertissement est écrit une fois |

---

## 3. Qui possède quelle donnée

C'est la table la plus utile de ce document. Une donnée a **un seul** propriétaire ;
tous les autres la lisent à travers une interface.

| Donnée | Propriétaire | Lue par |
|---|---|---|
| Boîte de collision d'une créature | WizardMobs (catalogue) → WizardSpigot (entité) → MCP (via le réseau) | — |
| École de magie d'un joueur | WizardCore (magie) | WizardIntro écrit une fois, MCP affiche |
| Essence Arcane | WizardCore | MCP affiche la jauge |
| Niveau et Éclats du Nexus | WizardCore (nexus) | WizardMobs pour le niveau des créatures, MCP pour le HUD |
| Propriétaire d'un Autel | WizardCore (autels) | MCP pour le HUD |
| Stock de Mana Brut | WizardCore (mana) | MCP pour la pastille de danger |
| Ère courante et phase | WizardCore (eras) | tous, pour les modificateurs de thème |
| Appartenance à un Coven | WizardCovens, via `FactionAdapter` | tous |
| Journal de quêtes | WizardQuest | MCP pour le journal et le faisceau |
| Montures débloquées | WizardMobs (catalogue) + WizardQuest (les clés de déblocage) | MCP pour la roue d'invocation |
| Compagnon actif et son évolution | **WizardPets** | WizardSpigot porte les entités ; MCP le rendu et la barre de sorts |
| Animations et modèles 3D | MCP (moteur) à partir de ASSETS | — |

**La règle qui en découle :** le serveur décide, le client montre. Quand une valeur
existe des deux côtés — une boîte de collision, une taille, une durée d'animation —
c'est le serveur qui la fixe et le client qui s'y aligne. Deux valeurs écrites à la
main de chaque côté finissent toujours par diverger, et la divergence ne se voit
pas dans un journal : elle se voit quand les coups passent à travers une créature.

---

## 4. Le protocole maison

Le client et le serveur se parlent par des paquets à identifiant réservé, en plus
du protocole vanilla. Chaque paquet est enregistré dans les deux sens.

| Plage | Usage |
|---|---|
| 95–99 | Tableau de score, casseurs, coffres, relations, échanges |
| 100–119 | Systèmes historiques (voir le registre détaillé) |
| 120–123 | Diplomatie, HUD de Coven, minuteur d'Ère, reset d'Ère |
| 124 | Poignée de main du lanceur — sans elle, le joueur est expulsé |
| 125–127 | Boutique, wiki, interface de Coven |
| 128 | Magie (catalogue, lancement, retours) |
| 129 | Compagnons |
| 130 | Cinématique d'arrivée |
| 131 | Journal de quêtes |
| 132 | Montures |

Le détail exact, y compris les identifiants d'entités et d'objets, est dans
[Plages d'identifiants](../07-reference/plages-d-identifiants.md). **Toute
nouvelle plage se réserve là, avant d'écrire la moindre ligne** : une collision
d'identifiant ne produit pas d'erreur, elle produit un paquet lu de travers.

Le paquet appartient au greffon qui l'émet ; WizardSpigot ne fournit que
l'enregistrement. C'est ce qui permet d'ajouter un système sans modifier le fork.

---

## 5. Ordre de chargement et de démarrage

1. **WizardSpigot** démarre : les entités natives sont enregistrées, les
   identifiants de paquets réservés.
2. **GlobalAPI**, **Vault**, **Essentials**, **WorldGuard** si présents.
3. **WizardCovens** — WizardCore en dépend formellement.
4. **WizardCore** : il lit `config.json` puis ses fichiers YAML, ouvre la
   persistance (JSON ou MySQL), et publie ses interfaces.
5. **WizardMobs**, **WizardQuest**, **WizardIntro**, dans n'importe quel ordre :
   ils branchent ce qu'ils trouvent et se passent du reste.
6. Un joueur se connecte : la poignée de main du client est exigée dans les cinq
   secondes, puis la cinématique est jouée si c'est sa première fois.

Un greffon qui ne trouve pas une dépendance facultative **écrit un avertissement
une seule fois** et continue. Un avertissement répété à chaque tick noie le
journal et fait manquer le vrai incident.

---

## 5 bis. Redis, et ce qu'il porte

Le lobby et la file partagent une instance **Redis ou Dragonfly** — le même protocole,
et le greffon n'a pas à savoir lequel répond.

| Donnée | Qui écrit | Qui lit |
|---|---|---|
| Les files d'attente | la file, sur le proxy | la file |
| La capacité du SMP | le SMP, par battement de cœur | la file |
| Les admissions | la file | le lobby, par publication |

**Dragonfly est recommandé pour une infrastructure neuve**, Redis si elle existe déjà. Le
refus de RabbitMQ pour la file est argumenté dans
[cdc_wizardqueue](../90-specifications/cdc_wizardqueue.md) §2.2 : une file de messages ne
sait pas donner la position d'un joueur ni retirer un élément du milieu.

> **Redis n'est jamais une dépendance dure.** Injoignable, le lobby reste jouable et la
> file laisse passer les connexions. C'est la même règle que partout ailleurs : une
> dépendance manquante coûte une capacité, pas le démarrage.

---

## 6. Persistance

Chaque module de WizardCore choisit son support indépendamment, par une clé
`*StorageType` dans `config.json` : `JSON` ou `MYSQL`.

| Support | Quand le choisir | Limite |
|---|---|---|
| **JSON** | Un seul serveur, petite population, mise en route | Un fichier par module ; pas de partage entre serveurs |
| **MYSQL** | Plusieurs serveurs partageant les mêmes joueurs | Demande une base disponible ; une base injoignable doit retomber sur JSON plutôt qu'empêcher le démarrage |

Les identifiants MySQL sont partagés entre modules. Un module dont le
`*StorageType` n'est pas renseigné suit celui du Nexus.

Le détail clé par clé est dans
[Configuration](../05-operer/configuration/README.md).

---

## 7. Chaînes de compilation

Elles n'ont rien en commun, et c'est la source d'erreur la plus fréquente pour
quelqu'un qui arrive sur le projet.

| Dépôt | Outil | Particularité |
|---|---|---|
| WizardSpigot | Maven | Produit le jar serveur ; les autres greffons compilent contre lui |
| WizardCore | Maven | Dépend du jar WizardSpigot et de GlobalAPI |
| WizardMobs | Gradle | Recopie le jar WizardSpigot dans ses dépendances avant de compiler |
| WizardQuest | Gradle | Idem |
| WizardIntro | Maven | — |
| MCP | script dédié | **Tous** les fichiers sources sont passés au compilateur d'un coup |

Deux pièges documentés :

- **Le client MCP ne se compile pas fichier par fichier.** Compiler quelques
  fichiers en s'appuyant sur le chemin des sources charge leurs dépendances
  *implicitement*, et le processeur d'annotations ne tourne alors pas sur elles :
  toutes les classes qui portent un accesseur généré paraissent en être dépourvues,
  et on obtient des centaines d'erreurs qui n'existent pas pendant que la vraie se
  perd au milieu. Il existe un script pour ça ; il faut s'en servir.
- **Un greffon ne voit pas une nouvelle interface de WizardSpigot** tant que le
  jar du fork n'a pas été reconstruit *et recopié*. Une erreur « symbole
  introuvable » sur une classe qu'on vient d'écrire vient presque toujours de là.

---

## 8. Où vit la documentation technique

Cette documentation-ci couvre la conception, le jeu et l'exploitation. Les notes
d'implémentation restent avec le code, au plus près de ce qu'elles décrivent.

| Dépôt | Documentation interne |
|---|---|
| WizardIntro | `docs/` — architecture, installation, interfaces |
| MCP | `docs/` — moteur 3D, cinématique, procédures de test en jeu |
| ASSETS | inventaire des modèles, textures et sons |

---

## À lire ensuite

- [Plages d'identifiants](../07-reference/plages-d-identifiants.md) — avant d'ajouter quoi que ce soit
- [Installation](../05-operer/installation.md) — monter tout ça
- [Configuration](../05-operer/configuration/README.md) — chaque clé de chaque fichier
- [Glossaire](glossaire.md) — le vocabulaire
