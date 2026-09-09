# Interface et client

Le HUD, les raccourcis, les réglages, et quoi faire quand ça cloche. WizardMC exige un
client modifié ; ce document dit ce qu'il apporte et comment le régler.

**Statut : livré.**

---

## 1. Pourquoi un client obligatoire

C'est une barrière à l'entrée, et elle ne se justifie que si elle rend le jeu
meilleur. Ce qu'elle apporte :

| | |
|---|---|
| Les créatures en **modèles 3D animés** | Le jeu de base ne sait afficher que ses propres modèles |
| L'interface de magie | Grimoire, roue des sorts, cercle d'incantation |
| Le journal de quêtes et son **chemin à l'écran** | — |
| Les **frontières de claim** visibles | Sans aucune entité posée dans le monde |
| Les effets de sorts et de pouvoirs | — |
| Les sons propres au serveur | — |
| Un HUD complet | Coven, Nexus, Ère, Essence, Mana, compagnon, monture, quêtes |

### La règle qui encadre tout ça

> **Le client montre, le serveur décide.**

Aucune mécanique ne dépend du client pour être **équitable**. Un client modifié ne
peut ni se donner une école, ni connaître la position d'un ennemi caché, ni sauter une
étape de la cinématique.

Conséquence visible : la poignée de main. Le serveur exige la preuve du bon client dans
les **cinq secondes**, puis expulse. Ce n'est pas une sanction, c'est un échange qui
échoue.

---

## 2. Les raccourcis

| Touche | Ce qu'elle ouvre |
|---|---|
| **R** | La roue des sorts |
| **L** | Le journal de quêtes |
| **K** | Appeler le compagnon |
| **M** | Appeler la monture |

Tous se changent dans les options du client, comme n'importe quelle touche. Le client
ajoute aussi des bascules de sprint et d'accroupissement, et un raccourci d'affichage
d'équipement et de potions.

---

## 3. Le HUD

### Ce qui s'affiche

| Élément | Ce qu'il montre |
|---|---|
| **HUD SMP unifié** | Une colonne : Coven → Nexus → Ère → Mana |
| **Coven** | Le tag, le territoire utilisé et maximal, l'état de guerre, les Autels tenus |
| **Nexus** | Le niveau, les Éclats, le prochain seuil |
| **Ère** | L'Ère en cours, sa phase, le temps restant |
| **Jauge d'Essence** | L'Essence Arcane |
| **Barre de sorts** | Les sorts équipés et leurs recharges |
| **Barre de sorts du compagnon** | Quand il est actif |
| **Compagnon** | Son état |
| **Monture** | Quand elle est là |
| **Quêtes** | Les quêtes suivies et leur étape |
| **Capture d'Autel** | Une jauge, pendant une capture |
| **Danger Mana** | Une pastille dès qu'on porte du Mana Brut |
| **Bannière de guerre** | Pendant une guerre déclarée |
| **Destruction du Nexus** | La progression d'une canalisation, des deux côtés |
| **Armure et potions** | Équipement et effets |
| **Tableau de score** | L'affichage latéral |
| **Notifications** | Les messages importants |

### L'ordre de priorité

Quand plusieurs choses veulent l'attention, elles passent dans cet ordre :

1. Guerre déclarée, ou Grand Cataclysme
2. Capture d'Autel
3. Montée de niveau du Nexus, ou déblocage de bâtiment
4. Danger Mana
5. Le HUD statique — Coven, Ère

C'est ce qui évite qu'une notification de niveau masque une déclaration de guerre.

### La pastille de danger Mana

Elle n'est pas une option. Dès qu'un joueur porte du Mana Brut, elle est visible — et
c'est toute la mécanique du convoi. Voir
[Autels et Mana Brut](autels-et-mana.md#la-pastille-de-danger).

---

## 4. Les réglages

Vingt-deux réglages, un par élément ou presque. Ils se trouvent dans les options du
client.

| Réglage | Ce qu'il fait |
|---|---|
| **HUD SMP unifié** | La colonne unique, ou les pastilles séparées d'avant |
| **Armure, Coven, Ère, Essence, Mana, Nexus, Compagnon, Monture, Quêtes** | Activer et placer chaque élément |
| **Barre de sorts**, **Roue des sorts** | Affichage et comportement |
| **Faisceau de quête** | Le chemin à l'écran |
| **Bannière de guerre** | — |
| **Tableau d'information** | — |
| **Frontières de claim** | Les afficher |
| **Frontières denses** | Une version plus fournie — plus lisible, plus chargée |
| **Tremblement de caméra** | À désactiver si ça gêne |
| **Notifications** et **sons de notification** | — |
| **Bascule de sprint**, **bascule d'accroupissement** | — |

### Les deux réglages qu'on change le plus

**Le tremblement de caméra.** Certains effets en produisent. Si ça gêne, il se coupe —
et ça ne désavantage en rien.

**Les frontières denses.** La version dense montre mieux où s'arrête un claim, au prix
d'un affichage plus chargé. Utile en guerre, pénible au quotidien.

### Les frontières de claim

Elles sont **entièrement côté client** : aucune entité n'est posée dans le monde pour
les dessiner. Les couleurs distinguent son propre territoire, celui des alliés, celui
des ennemis, et celui d'un Coven en guerre.

---

## 5. Le wiki en jeu

`/wiki` — aussi `/help` et `/aide`. L'aide consultable sans quitter le jeu.

---

## 6. Les interfaces

| Interface | Comment l'ouvrir |
|---|---|
| **Grimoire** | Les sorts connus, leur progression, leurs paliers |
| **Roue des sorts** | **R** |
| **Journal de quêtes** | **L** |
| **Roue des montures** | **M** |
| **Boutique** | `/boutique` |
| **Pass d'Ère** | `/pass` |
| **Interface de Coven** | Liste des membres, rôles, état |
| **Stock de Mana** | `/mana` |
| **Réserves** | `/box` |
| **Hôtel des ventes** | `/hdv` |
| **Échange du Forgeron** | En lui parlant |

---

## 7. Quand ça cloche

### Le serveur m'expulse au bout de cinq secondes

La poignée de main a échoué. Trois causes :

| Cause | Ce qu'il faut faire |
|---|---|
| Ce n'est pas le client WizardMC | L'installer |
| Le client est d'une version périmée | Le mettre à jour |
| Le lanceur n'a pas démarré le bon client | Relancer par le lanceur |

### Les créatures apparaissent en cube rose

Le modèle n'a pas pu s'afficher correctement. C'était un défaut connu — des faces sans
texture ressortaient en magenta — et il est corrigé. S'il réapparaît sur une créature
précise, **signalez laquelle** : c'est le modèle qui est en cause, pas le client.

### Les créatures n'ont pas de modèle 3D

Même chose : un modèle manquant ou illisible. Le client affiche alors un repère
temporaire. Signalez l'espèce.

### Les animations saccadent

Les transitions entre animations sont fondues depuis une mise à jour récente. Si une
créature saccade encore, notez **laquelle et dans quelle situation** — en marchant, en
attaquant, en mourant.

### Je ne vois pas les frontières de claim

Vérifier le réglage. Elles sont désactivables.

### Le HUD est mal placé ou se superpose

Chaque élément se place indépendamment dans les réglages. Il n'existe pas de
réinitialisation globale : il faut reprendre les éléments un par un.

### Le chemin de quête ne s'affiche pas

Deux causes : la quête n'est pas **suivie** — `/quest track` — ou l'objectif courant
n'a pas de marqueur. Un objectif sans marqueur n'affiche rien, c'est normal.

### Le jeu rame quand il y a beaucoup de créatures

Les modèles animés coûtent plus cher que les modèles du jeu de base. Réduire la
distance d'affichage aide plus que tout le reste.

---

## 8. Mettre à jour le client

Le client n'est pas un mod : c'est un client complet, et une mise à jour se
distribue comme tel. Le système prévu pour ça est **WizardCloud** —
voir [cdc_wizardcloud](../90-specifications/cdc_wizardcloud.md).

En attendant, les mises à jour se récupèrent par le lanceur.

---

## 9. Ce qu'il faut signaler, et comment

Un rapport utile contient quatre choses :

1. **Quoi** — « la Gelée de lave s'affiche en cube rose »
2. **Où** — l'espèce, la région, l'interface
3. **Quand** — en marchant, en attaquant, à la connexion
4. **Si ça se reproduit** — une fois, ou chaque fois

Les deux défauts qui méritent un signalement immédiat :

| Défaut | Pourquoi c'est grave |
|---|---|
| Une créature qui frappe **sans aucun geste visible** | C'est l'esquive qui devient impossible : une règle de conception est violée |
| Une créature qui frappe **à travers un mur** | Ce n'est pas un réglage, c'est un bug |

---

## À lire ensuite

- [Premiers pas](premiers-pas.md) — la première heure
- [La magie](magie.md) — ce que le grimoire et la roue servent
- [cdc_exp_client](../90-specifications/cdc_exp_client.md) — la spécification du client
- [cdc_wizardcloud](../90-specifications/cdc_wizardcloud.md) — la distribution
