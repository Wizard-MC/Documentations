# Modération

Les outils du staff, le barème, la procédure, et les pièges propres à un serveur SMP.
À lire en entier avant de prendre sa première décision.

**Statut : livré** pour les outils. Le barème ci-dessous est une proposition de cadre,
à arrêter par l'équipe.

---

## 1. Le principe

> **Le staff fait respecter les règles. Il n'arbitre pas le jeu.**

Cette phrase règle la moitié des situations. Perdre une guerre, se faire voler un
convoi, se faire exclure de son Coven, se faire prendre un Autel : **ce sont des
événements de jeu**, pas des incidents.

| Ce que le staff traite | Ce que le staff ne traite pas |
|---|---|
| La triche, l'exploitation d'un bug | Une défaite |
| Le harcèlement, les propos haineux | Un différend interne à un Coven |
| L'usurpation d'identité | Une trahison, une alliance rompue |
| La publicité, le recrutement pour un autre serveur | Un vol de Mana Brut |
| Les comptes multiples utilisés pour contourner un plafond | Une embuscade |
| Les constructions choquantes | Une construction laide |

### Le cas des litiges internes à un Coven

Un joueur vient dire qu'un Officier a vidé la banque. Ce n'est **pas** un incident de
modération : c'est une conséquence des rôles que le chef a distribués.

Ce que le staff peut faire : rappeler que la banque est journalisée, et que le chef peut
consulter le journal. Ce qu'il ne fait pas : rendre l'argent.

La seule exception est la triche caractérisée — un compte compromis, par exemple.

---

## 2. Les outils

### L'information

| Commande | Ce qu'elle donne |
|---|---|
| `/nexus info` | L'état d'un Nexus |
| `/altar qa` | L'état des Autels — permission `wizardmc.altars.admin` |
| `/era qa` | L'état de l'Ère — `wizardmc.eras.admin` |
| `/mana` | Les stocks de Mana |
| `/forgeron info` | Le Forgeron — `wizardmc.forgeron.admin` |
| `/power qa` | L'état des pouvoirs — `wizardmc.powers.admin` |
| `/magic qa` | L'état de la magie d'un joueur — `wizardmc.magic.admin` |
| `/hdv log` | Le journal de l'hôtel des ventes — `core.auctions` |
| `/combattag` | L'état de marquage en combat |

### Le chat

| Commande | Ce qu'elle fait | Permission |
|---|---|---|
| `/chat clear` | Vide le chat | `core.chat.*` |
| `/chat toggle` | Ouvre ou ferme le chat | `core.chat.*` |

`/chat clear` est utile après un débordement : il ne supprime rien côté journal, il
nettoie l'affichage.

### Les corrections

À n'utiliser qu'en connaissance de cause, et **toujours en notant ce qu'on a fait** :

| Commande | Ce qu'elle fait | Permission |
|---|---|---|
| `/nexus set`, `/nexus addshards`, `/nexus give` | Modifie un Nexus | `wizardmc.nexus.admin` |
| `/altar reset`, `/altar setcooldown` | Remet un Autel à zéro | `wizardmc.altars.admin` |
| `/mana generate` | Crée du Mana Brut | `wizardmc.mana.admin` |
| `/magic give`, `/magic unlock` | Donne un sort ou une école | `wizardmc.magic.admin` |
| `/contrat complete` | Valide un contrat | `wizardmc.contracts.admin` |
| `/loot edit`, `/loot list` | Les tables de butin | `core.loots` |

> **Toute correction se note** : qui, quoi, quand, pourquoi. Sans trace, une correction
> devient indiscernable d'un abus, et c'est le staff qui y perd.

---

## 3. La procédure

### Étape 1 — Établir le fait

Avant toute sanction :

| À réunir | Pourquoi |
|---|---|
| Ce qui s'est passé, en une phrase | Si on ne peut pas le résumer, on ne l'a pas compris |
| **Une preuve** | Capture, extrait de journal, témoignage recoupé |
| La date et l'heure | Pour retrouver le journal |
| Les joueurs concernés | Tous, pas seulement celui qui se plaint |

> **Une plainte n'est pas une preuve.** Un joueur qui meurt trois fois de suite est
> convaincu qu'on triche contre lui, et il a presque toujours tort.

### Étape 2 — Distinguer le bug de la triche

C'est l'étape que le staff saute le plus souvent, et celle qui coûte le plus cher.

| Indice | Plutôt un bug | Plutôt de la triche |
|---|---|---|
| Ça arrive à plusieurs joueurs | oui | non |
| Ça arrive à un joueur contre son gré | oui | non |
| Le joueur en tire un avantage répété | non | oui |
| Le joueur le cache | non | oui |
| Ça se reproduit en essayant | oui | — |

Un joueur qui **signale** un comportement anormal dont il bénéficie n'est pas un
tricheur : c'est un testeur. Le remercier coûte moins cher que de le perdre.

### Étape 3 — Sanctionner, ou non

Voir le barème ci-dessous.

### Étape 4 — Écrire

Chaque sanction laisse une trace : joueur, date, motif, preuve, sanction, qui a décidé.
Sans registre, les récidives sont invisibles et les décisions incohérentes.

---

## 4. Le barème proposé

À arrêter par l'équipe. Le principe : **la sanction répare ou empêche, elle ne punit
pas pour punir.**

| Comportement | Première fois | Récidive |
|---|---|---|
| Insultes, propos agressifs | Avertissement | Réduction au silence temporaire |
| Propos haineux, discriminatoires | Exclusion temporaire longue | Exclusion définitive |
| Harcèlement d'un joueur | Avertissement ferme, puis exclusion temporaire | Exclusion définitive |
| Publicité pour un autre serveur | Réduction au silence | Exclusion temporaire |
| Exploitation d'un bug, avec gain | Retrait du gain + avertissement | Exclusion temporaire |
| Exploitation d'un bug, répétée et cachée | Exclusion temporaire + retrait | Exclusion définitive |
| Triche logicielle | Exclusion définitive | — |
| Comptes multiples pour contourner un plafond | Retrait du gain, fusion ou suppression des comptes | Exclusion temporaire |
| Usurpation d'identité, y compris du staff | Exclusion temporaire longue | Exclusion définitive |
| Construction choquante | Suppression + avertissement | Exclusion temporaire |

### Le retrait du gain passe avant la sanction

Un joueur qui a obtenu cinq cents Éclats par un bug doit d'abord **les perdre**. Une
exclusion d'une semaine qui laisse le gain en place enseigne que ça valait le coup.

---

## 5. Les pièges propres à ce serveur

### Le Mana Brut volé n'est pas un vol

Le Mana Brut se porte, est **signalé par une pastille visible**, et se perd à la mort.
Se le faire prendre est la mécanique, pas un incident.

Un joueur qui s'en plaint n'a pas compris le système. Lui expliquer vaut mieux que de
le plaindre.

### Le Coven dissous ne se reconstitue pas

Dissoudre libère les claims et la banque est perdue ou redistribuée selon la
configuration. C'est une action de chef, confirmée, et **irréversible**.

Le staff ne reconstitue pas un Coven dissous par erreur : la confirmation existe
précisément pour ça.

### Le Nexus détruit n'est pas une perte de ville

Seul le Nexus retombe. Les claims, les constructions et les inventaires sont intacts.
Un joueur qui dit « on m'a tout pris » se trompe presque toujours.

### Les claims ne se prennent pas sans guerre

L'`overclaim` est désactivé. Si un joueur dit qu'un autre Coven a pris son terrain sans
guerre, **c'est un bug** et il faut l'examiner.

### La cinématique ne se rejoue pas

Un joueur qui demande à refaire le choix d'école demande une chose que le système ne
sait pas faire : elle est jouée une fois, jamais ensuite.

La bonne réponse : le choix **n'enferme rien**. Les neuf écoles restent apprenables, et
il progressera dans celle qu'il veut.

### Un client modifié n'a pas d'avantage

La poignée de main vérifie le client, et le serveur décide de tout ce qui compte. Un
joueur qui accuse un autre d'avoir « un client modifié » décrit presque toujours de la
compétence.

---

## 6. Le support technique

Les questions les plus fréquentes, et leur réponse. Voir
[Interface et client](../04-jouer/interface-client.md#7-quand-ça-cloche).

| Plainte | Réponse |
|---|---|
| « Je suis expulsé au bout de cinq secondes » | La poignée de main échoue : mauvais client, ou version périmée |
| « Les créatures sont des cubes roses » | Un modèle : demander **laquelle**, et remonter |
| « Les animations saccadent » | Demander laquelle et dans quelle situation, et remonter |
| « Je ne vois pas les frontières de claim » | Un réglage du client |
| « Le chemin de quête ne s'affiche pas » | La quête n'est pas suivie, ou l'objectif n'a pas de marqueur |
| « Ça rame avec beaucoup de créatures » | Réduire la distance d'affichage |
| « Mon HUD est mal placé » | Chaque élément se place séparément ; pas de réinitialisation globale |

### Ce qui se remonte tout de suite à l'équipe

| Signalement | Pourquoi |
|---|---|
| Une créature qui frappe **sans geste visible** | Une règle de conception est violée : l'esquive devient impossible |
| Une créature qui frappe **à travers un mur** | Un bug |
| Un claim pris **sans guerre** | Un bug |
| Une quête qui n'apparaît plus | Probablement une matière mal orthographiée au chargement |
| Un avertissement **répété à chaque tick** dans le journal | Le journal se noie, et le vrai incident se perd |

---

## 7. Ce que le staff ne fait jamais

| Jamais | Pourquoi |
|---|---|
| Donner un objet, un Éclat, un sort à un joueur qui le demande | C'est un avantage, et le principe n'a pas d'exception |
| Intervenir dans une guerre | Même en spectateur armé |
| Révéler la position d'un joueur | Y compris « pour aider » |
| Arbitrer un litige interne à un Coven | Voir le paragraphe 1 |
| Sanctionner sans preuve | Voir l'étape 1 |
| Corriger sans le noter | Une correction non tracée est indiscernable d'un abus |
| Promettre une fonctionnalité | Seule l'équipe s'engage |

### Le piège du staff qui joue

Un membre du staff qui joue dans un Coven est dans une position intenable dès la
première guerre. Deux règles, au choix de l'équipe :

- Soit le staff ne joue pas en Coven ;
- Soit il joue, et **un autre membre du staff** traite tout incident impliquant son
  Coven.

La seconde demande une équipe d'au moins deux personnes disponibles.

---

## À lire ensuite

- [Game master](game-master.md) — animer plutôt que surveiller
- [Runbooks](runbooks.md) — quand c'est technique
- [Permissions](../07-reference/permissions.md) — les nœuds, et des rôles prêts à l'emploi
- [Commandes](../07-reference/commandes.md) — les 129 commandes
