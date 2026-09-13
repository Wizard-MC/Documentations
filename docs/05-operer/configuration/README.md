# Configuration

Tous les fichiers de configuration de WizardMC, où ils vivent, et ce qu'ils règlent.
Point d'entrée pour un administrateur.

---

## 1. Les fichiers, par greffon

### WizardCore

| Fichier | Ce qu'il règle | Détail |
|---|---|---|
| `config.json` | 74 clés : persistance, Nexus, Autels, Mana, Pouvoirs, Ères, magie, boutique | [Référence](wizardcore-config-json.md) |
| `altars.yml` | La position et le type des sept Autels | [Référence](wizardcore-fichiers-yml.md#2-altarsyml--les-autels) |
| `contracts.yml` | Les contrats quotidiens | [Référence](wizardcore-fichiers-yml.md#3-contractsyml--les-contrats) |
| `era.yml` | Le calendrier et le thème de l'Ère | [Référence](wizardcore-fichiers-yml.md#4-erayml--lère-courante) |
| `era_pass.yml` | Le Pass d'Ère : 50 niveaux, deux voies | [Référence](wizardcore-fichiers-yml.md#5-era_passyml--le-pass-dère) |
| `forgeron_locations.yml` | Les points d'apparition du Forgeron | [Référence](wizardcore-fichiers-yml.md#6-forgeron_locationsyml--le-forgeron) |
| `magic.yml` | Essence, incantation, régions interdites | [Référence](wizardcore-fichiers-yml.md#7-magicyml--les-règles-de-la-magie) |
| `spells.yml` | Les 41 sorts | [Référence](wizardcore-fichiers-yml.md#8-spellsyml--les-sorts) |
| `wands.yml` | Les 13 baguettes | [Référence](wizardcore-fichiers-yml.md#9-wandsyml--les-baguettes) |
| `shop.yml` | Cosmétiques et utilitaires | [Référence](wizardcore-fichiers-yml.md#10-shopyml--la-boutique) |

### WizardMobs

| Fichier | Ce qu'il règle | Détail |
|---|---|---|
| `mobs.yml` | Les 49 créatures hostiles, leur IA, leur butin, leur apparition | [Référence](wizardmobs.md) |
| `beasts.yml` | Les 24 bêtes paisibles | [Référence](wizardmobs.md#3-beastsyml--les-bêtes-paisibles) |
| `mounts.yml` | Les 10 montures | [Référence](wizardmobs.md#4-mountsyml--les-montures) |

### WizardQuest

| Fichier | Ce qu'il règle | Détail |
|---|---|---|
| `config.yml` | Où vont les journaux de quête | [Référence](wizardquest.md#2-configyml) |
| `quests.yml` | Les quêtes, leurs étapes, leurs récompenses | [Référence](wizardquest.md#3-questsyml) |

### WizardBungee — le proxy

| Fichier | Ce qu'il règle | Détail |
|---|---|---|
| `config.yml` | Écouteurs, serveurs, mode d'identité, transfert d'IP, bornes réseau | [Référence](wizardbungee.md#3-configyml--les-clés-qui-comptent) |
| `optimus.yml` | Les empreintes de client et le gonflage d'effectif | [Référence](wizardbungee.md#2-optimusyml--les-clés-wizardmc) |

> Ce greffon-ci n'est pas un greffon : c'est le proxy lui-même. Ses deux fichiers ont une
> particularité qui n'est vraie nulle part ailleurs — **une clé absente est réécrite dans
> le fichier avec sa valeur par défaut**. La règle du §2 ci-dessous ne s'y applique donc
> pas de la même façon.

### WizardIntro

La cinématique d'arrivée a sa propre configuration, documentée dans le dépôt
`WizardIntro/docs/`. Les règles sont dans
[cdc_intro](../../90-specifications/cdc_intro.md).

### Le client

Les réglages du client sont **par joueur**, dans ses options, et ne s'administrent
pas depuis le serveur. Voir
[Interface et client](../../04-jouer/interface-client.md).

---

## 2. Les trois règles communes

### Une clé absente prend sa valeur par défaut

Ce n'est pas une erreur. Un fichier de configuration **peut rester court** : on n'y
écrit que ce qu'on change.

Corollaire pratique : un fichier qui contient toutes les clés possibles est plus
difficile à relire qu'un fichier qui en contient cinq. Ne pas tout recopier « pour
voir ».

### Les commentaires sont de la documentation

Les fichiers livrés portent des commentaires qui expliquent **pourquoi** une valeur
vaut ce qu'elle vaut. Ce n'est pas du remplissage : la régénération d'Essence porte
l'histoire de ses deux baisses, précisément pour que personne ne la remonte en croyant
rendre service.

**Ne les supprimez pas.** Et quand vous changez une valeur, ajoutez la raison.

### Une dépendance absente ne doit pas empêcher le démarrage

Une base MySQL injoignable fait retomber sur JSON. Un greffon facultatif manquant fait
perdre une capacité nommée, avec un avertissement écrit **une seule fois**.

Un avertissement répété à chaque tick noie le journal et fait manquer le vrai
incident.

---

## 3. Les fins de ligne

Les fichiers YAML livrés sont en fins de ligne Unix. Un éditeur Windows qui les
réécrit en CRLF peut casser la lecture de certains outils.

Ce n'est pas théorique : des contrôles du projet ont déjà échoué sur une machine
Windows et réussi ailleurs pour cette seule raison. Le correctif était dans les
outils, mais la leçon vaut pour les fichiers de configuration aussi.

**Configurez votre éditeur en LF** pour ce dépôt.

---

## 4. Avant de changer quoi que ce soit

1. **Sauvegarder.** Voir [Exploitation quotidienne](../exploitation.md).
2. **Un seul réglage à la fois.**
3. **Noter la valeur précédente.**
4. **Pas les courbes en cours d'Ère.** `nexusShardBase`, `nexusShardExponent`,
   `nexusMaxLevel` sont les règles du jeu en cours.
5. **Écrire la raison**, dans le fichier.
6. **Relire la grille de décision** de
   [Équilibrage](../../03-gdd/equilibrage.md#6-la-grille-de-décision) si le réglage
   touche à la puissance.

---

## 5. Les secrets

| Clé | Nature |
|---|---|
| `mysqlPassword` | Mot de passe de base de données |
| `azLinkToken` | Jeton d'intégration |
| Le mot de passe MySQL de WizardQuest | Idem |
| `authorized-client-hash`, `bypass-client-hash` | Empreintes de client du proxy — **faibles par construction**, voir [Configuration du proxy §5](wizardbungee.md#5-changer-lempreinte-de-client) |
| `handshakeSecret` (WizardCore) | La **même valeur** que l'empreinte du proxy. Les trois se changent ensemble |

Aucun de ces éléments n'a sa place dans un dépôt, une capture d'écran, un rapport
d'incident ou un message de support. Cette documentation dit **qu'une** clé existe ;
jamais sa valeur.

---

## À lire ensuite

- [Installation](../installation.md) — monter le serveur depuis rien
- [Exploitation quotidienne](../exploitation.md) — sauvegardes, surveillance, fin d'Ère
- [Runbooks](../runbooks.md) — quand ça casse
- [Créer du contenu](../../06-creer-du-contenu/README.md) — ajouter une créature, un sort, une quête
