# Installation

Monter un serveur WizardMC depuis rien, dans l'ordre qui marche. À lire en entier
avant de commencer : l'ordre compte, et deux étapes plus loin il est trop tard pour
revenir en arrière.

**Statut : livré.**

---

## 1. Ce qu'il faut avant de commencer

| Élément | Version | Pourquoi cette version |
|---|---|---|
| **Java** | 8 | Le serveur 1.7.10 ne tourne pas au-delà. Un JDK plus récent compile parfois, et produit des classes que la machine virtuelle refuse de charger — l'échec arrive alors au lancement, loin de sa cause. |
| **Maven** | 3.x | Pour WizardSpigot, WizardCore, WizardIntro |
| **Gradle** | fourni par les dépôts | Pour WizardMobs, WizardQuest |
| **MySQL** | facultatif | Seulement si plusieurs serveurs partagent les joueurs |

### Les greffons tiers

| Greffon | Nécessaire à | Obligatoire ? |
|---|---|---|
| **GlobalAPI** | WizardCore | oui |
| **Essentials** | WizardCore | oui |
| **Vault** | WizardCore | oui |
| **WorldGuard** | WizardCore, WizardMobs | non, mais recommandé |

Sans WorldGuard, deux choses cessent de fonctionner : les régions où la magie est
interdite, et le découpage en régions qui donne la difficulté par zone. Le serveur
démarre quand même, et le dit.

---

## 2. L'ordre de construction

Il n'est pas négociable : chaque greffon compile **contre le jar du fork serveur**, et
un greffon ne voit pas une nouvelle interface tant que ce jar n'a pas été reconstruit.

### Étape 1 — WizardSpigot

Le fork serveur. Il porte les entités natives, l'enregistrement des paquets maison, et
l'interface `fr.wizardmc.api` contre laquelle tout le reste compile.

Construire avec Maven, en installant dans le dépôt local. **`JAVA_HOME` doit pointer
sur un JDK 8.**

Le produit est le jar serveur. C'est lui qu'on lance, et c'est lui contre lequel les
greffons compilent.

### Étape 2 — WizardCore

Maven. Il dépend du jar WizardSpigot et de GlobalAPI.

> Une erreur « symbole introuvable » sur une classe qu'on vient d'écrire vient presque
> toujours du fait que le jar du fork n'a pas été reconstruit **et recopié**.

### Étape 3 — WizardMobs et WizardQuest

Gradle. Les deux **recopient** le jar WizardSpigot dans leurs dépendances avant de
compiler. Si le jar du fork a changé, il faut que la copie soit à jour : c'est une
tâche du build, mais elle ne se déclenche que si le fichier source a bougé.

### Étape 4 — WizardIntro

Maven. Indépendant.

### Étape 5 — Le client MCP

Le client se compile avec le script fourni dans le dépôt, et **pas autrement**.

> **Pourquoi un script pour une seule commande de compilation :** parce que la commande
> évidente est fausse.
>
> Compiler quelques fichiers en s'appuyant sur le chemin des sources fait charger leurs
> dépendances *implicitement*, et le processeur d'annotations ne tourne alors pas sur
> elles. Toutes les classes qui portent un accesseur généré paraissent en être
> dépourvues, et on obtient des centaines d'erreurs qui n'existent pas, dans des
> fichiers qu'on n'a pas touchés, pendant que la vraie erreur se perd au milieu.
>
> Le seul moyen fiable est de passer **tous** les fichiers source au compilateur. C'est
> ce que fait le script.

Le script vérifie lui-même la version du compilateur et refuse un JDK supérieur à 8.

---

## 3. L'installation sur le serveur

### Étape 1 — Le serveur seul

Lancer le jar WizardSpigot une première fois, sans aucun greffon. Il génère son
arborescence et s'arrête.

Vérifier dans le journal qu'il a bien démarré : les entités natives s'enregistrent à ce
moment-là, et une erreur ici rend tout le reste inutile.

### Étape 2 — Les greffons tiers

Déposer GlobalAPI, Essentials, Vault, et WorldGuard si on le veut. Redémarrer.
Vérifier qu'ils se chargent.

### Étape 3 — WizardCovens

WizardCore en dépend formellement. Sur un serveur qui n'a pas encore le greffon natif,
c'est l'adaptateur historique qui tient ce rôle.

### Étape 4 — WizardCore

Déposer le jar, démarrer. Au premier démarrage il crée :

| Ce qu'il crée | Où |
|---|---|
| `config.json` | `plugins/WizardCore/` |
| Les neuf fichiers YAML | Idem |
| Les fichiers de persistance, si `JSON` | Idem |

**Arrêter le serveur** et passer à la configuration avant d'aller plus loin.

### Étape 5 — Configurer

Voir [Configuration](configuration/README.md). Le minimum indispensable :

| À régler | Pourquoi |
|---|---|
| Les `*StorageType` | `JSON` suffit pour un serveur seul |
| Les identifiants MySQL, si `MYSQL` | Sans eux, le greffon retombe sur JSON |
| `altars.yml` | **Les positions livrées sont des exemples.** Elles ne correspondent à aucun terrain réel. |
| `forgeron_locations.yml` | Même remarque |
| `era.yml` | La position du Panthéon, et le thème de la première Ère |

> Les positions d'Autels et de points du Forgeron livrées sont des **valeurs
> d'exemple**. Les laisser telles quelles place les sept Autels au hasard sur votre
> carte, parfois dans la pierre.

### Étape 6 — Les autres greffons

Déposer WizardMobs, WizardQuest, WizardIntro. L'ordre entre eux n'a pas d'importance :
ils branchent ce qu'ils trouvent et se passent du reste.

Au démarrage, lire les avertissements. Chacun dit **ce qu'il a perdu** faute d'une
dépendance :

| Avertissement | Ce qu'on perd |
|---|---|
| WizardMobs sans WizardCore | Le niveau des créatures ne suit plus la puissance du joueur |
| WizardMobs sans WorldGuard | Le découpage en régions est ignoré |
| WizardQuest sans WizardMobs | Les objectifs visant une espèce custom ne se valident plus |
| WizardQuest sans WizardCore | Les récompenses en Éclats ne sont plus versées |
| WizardIntro sans WizardCore | L'école choisie n'est pas lue par la magie |
| WizardCore sans WorldGuard | Les régions interdites à la magie ne s'appliquent pas |

Un avertissement est écrit **une seule fois**. S'il se répète à chaque tick, c'est un
défaut : signalez-le.

### Étape 7 — Le monde de la cinématique

WizardIntro a besoin d'un monde dédié pour le Sanctuaire des Origines.

> **Ce monde n'est jamais généré automatiquement.** S'il est absent, la cinématique ne
> démarre pas et le joueur reste au spawn.

C'est délibéré : générer un monde sans qu'on l'ait demandé est plus dangereux que de ne
pas jouer une cinématique.

### Étape 8 — Le client

Construire le client, le distribuer aux joueurs. Sans lui, personne ne se connecte : la
poignée de main échoue au bout de cinq secondes.

Le système de distribution prévu est [WizardCloud](../90-specifications/cdc_wizardcloud.md).

---

## 4. La vérification

Une liste à passer avant d'ouvrir. Chaque ligne a déjà été un incident.

### Le serveur

| À vérifier | Comment |
|---|---|
| Les entités natives sont enregistrées | Le journal de démarrage du fork |
| Les greffons se chargent tous | La liste des greffons |
| La persistance fonctionne | Les fichiers JSON existent, ou les tables sont créées |
| Aucun avertissement répété | Le journal après quelques minutes |

### Le contenu

| À vérifier | Comment |
|---|---|
| Les sept Autels sont sur du terrain praticable | Se rendre à chacun |
| Les points du Forgeron aussi | Idem, et vérifier qu'aucun n'est à la fois proche du spawn et sûr |
| Le Panthéon est visible | Au spawn |
| Les créatures apparaissent | Attendre la nuit en wilderness |
| Les quêtes sont chargées | Parler au Forgeron |

### Le client

| À vérifier | Comment |
|---|---|
| La poignée de main passe | Se connecter |
| La cinématique se joue | Avec un compte neuf |
| Les créatures ont un modèle 3D | En croiser |
| Aucun cube rose | Idem |
| Le HUD s'affiche | À la connexion |
| Le journal s'ouvre | La touche **L** |

### L'essai de bout en bout

Avec un compte neuf, jouer la première heure décrite dans
[Premiers pas](../04-jouer/premiers-pas.md). C'est le seul test qui valide la chaîne
entière, et il trouve plus de défauts que tout le reste.

---

## 5. Les pièges d'installation

| Piège | Symptôme | Cause |
|---|---|---|
| JDK plus récent que 8 | Le serveur démarre puis refuse de charger une classe | Les classes produites sont d'une version que la machine virtuelle 1.7.10 ne connaît pas |
| Jar du fork non recopié | « Symbole introuvable » sur une classe qu'on vient d'écrire | Le greffon compile contre une version périmée |
| Client compilé fichier par fichier | Des centaines d'erreurs sur du code non modifié | Le processeur d'annotations n'a pas tourné |
| Positions d'Autels laissées par défaut | Des Autels dans la pierre ou dans le vide | Les valeurs livrées sont des exemples |
| Monde de cinématique absent | Aucune cinématique, et personne ne comprend pourquoi | Il n'est jamais généré automatiquement |
| Fichiers YAML réécrits en CRLF | Des lectures qui échouent sans message clair | Configurer l'éditeur en LF |
| Base MySQL déclarée mais injoignable | Le greffon retombe sur JSON sans qu'on le remarque | Lire le journal de démarrage |

---

## 6. Le premier démarrage public

Avant d'ouvrir aux joueurs :

1. **Sauvegarder.** Un état propre et connu, avant que quiconque y touche.
2. **Choisir le thème de la première Ère.** Voir
   [Chronologie des Ères](../02-univers/chronologie-des-eres.md#3-les-trois-thèmes).
3. **Vérifier `autoResetOnExpiry`.** Si le calendrier n'est pas sûr, clôturer à la main
   est plus prudent.
4. **Nommer l'Ère.** « Ère I » est un défaut, pas un nom.
5. **Écrire l'annonce d'ouverture.** Deux ou trois phrases sur ce qui a changé dans le
   monde, pas dans les règles.

---

## À lire ensuite

- [Configuration](configuration/README.md) — chaque fichier, chaque clé
- [Exploitation quotidienne](exploitation.md) — sauvegardes, surveillance, fin d'Ère
- [Runbooks](runbooks.md) — quand ça casse
- [Architecture logicielle](../01-projet/architecture-logicielle.md) — qui dépend de qui
