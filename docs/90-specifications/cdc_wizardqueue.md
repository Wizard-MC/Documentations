# CDC TECHNIQUE — WizardQueue

> File d'attente de connexion au SMP, tenue **sur le proxy**. Priorité VIP bornée,
> contournement staff, position exacte, et survie au redémarrage.

**État : spécifié.** Le cahier des charges est arrêté, le code n'existe pas.

Documents liés :

- [`cdc_wizardhub.md`](cdc_wizardhub.md) — le Seuil, qui affiche cette file au joueur
- [`cdc_boutique.md`](cdc_boutique.md) — pourquoi la priorité payante a besoin d'un garde-fou
- [`cdc_exp_client.md`](cdc_exp_client.md) — client MCP

---

## 1. Objet et intention

Quand le SMP est plein, un joueur doit **attendre quelque part**, en sachant où il en
est, sans perdre sa place pour une coupure de deux secondes, et sans que les joueurs
prioritaires affament les autres.

Trois engagements structurent le système :

1. **On n'attend jamais dans le vide.** Un joueur en file est connecté au Seuil, il voit
   sa position, et il peut partir. Une file qui retient un joueur devant un écran de
   chargement est une file cassée.
2. **La position est exacte, pas estimée.** Un joueur à qui on annonce « 12<sup>e</sup> »
   est douzième. Une position approchée détruit la confiance en une seule occasion.
3. **La priorité ne peut pas affamer.** Une part fixe des admissions est réservée aux
   joueurs sans priorité. La file des non-prioritaires avance **toujours**.

### Le problème qu'on ne veut pas résoudre

**Une file de quarante minutes n'est pas un problème d'animation, c'est un problème de
capacité.** Ce cahier des charges ne prévoit aucun divertissement de salle d'attente :
si la file devient longue, la réponse est un slot de plus sur le SMP, pas un parkour.

Ce que le système garantit en revanche : qu'on puisse **attendre sans regarder**. Le
joueur peut passer sur une autre fenêtre, et l'Appel est assez bruyant pour le ramener.

---

## 2. Décision d'architecture

### 2.1. La file vit sur le proxy

| | |
|---|---|
| **Décision** | WizardQueue est un greffon **WizardBungee** (Travertine), pas WizardSpigot |

Cinq raisons, dans l'ordre :

1. **Un joueur en file n'est sur aucun backend.** La file existe parce que le SMP est
   plein : son détenteur ne peut pas être le SMP.
2. **Le proxy est le seul dans le chemin de chaque tentative de connexion.**
   `ServerConnectEvent` est annulable et sa cible est modifiable : c'est là, et seulement
   là, qu'on peut intercepter et rediriger.
3. **Le proxy sait ce qui a réellement abouti.** `connect(..., Callback<Boolean>, ...)`
   rapporte le succès de la connexion. Sans ce retour, on ne peut pas distinguer une
   admission réussie d'un slot perdu.
4. **Le proxy peut rattraper une expulsion.** `ServerKickEvent` porte un serveur de
   repli : un joueur expulsé par un SMP plein ou en redémarrage revient au Seuil au lieu
   d'être déconnecté.
5. **Une file sur un backend serait liée à une instance.** Dès qu'il y a deux Seuils, la
   file se scinde en deux — et il n'existe pas de bonne façon de les recoller.

### 2.2. Redis, pas RabbitMQ

| | |
|---|---|
| **Décision** | Protocole Redis. **Dragonfly** recommandé pour une infrastructure neuve, Redis si elle existe déjà. Le greffon ne connaît que le protocole. |

La réponse intuitive est mauvaise, et il faut dire pourquoi. Une file de joueurs demande
cinq opérations :

| Opération | Ce qu'il faut |
|---|---|
| Donner la position de **n'importe quel** joueur, à tout moment | un accès indexé |
| Retirer un joueur qui part, depuis **n'importe quelle** position | une suppression au milieu |
| Admettre N joueurs de façon **atomique** entre plusieurs proxies | une opération atomique partagée |
| Réordonner selon la priorité | un réordonnancement |
| Survivre au redémarrage d'un proxy | une persistance **hors** du proxy |

**RabbitMQ est une file de messages**, consommés une fois, dans l'ordre, sans accès
indexé. On ne peut pas lui demander la position d'un joueur sans la vider, ni retirer un
élément du milieu, ni réordonner sans tout republier. Il faudrait maintenir un index en
parallèle — et à ce moment-là le courtier n'apporte plus rien.

**Un ensemble ordonné Redis est exactement la structure** : `ZADD` et `ZRANK` en
O(log N), `ZREM` depuis n'importe où, `ZPOPMIN` pour la tête, et Lua pour l'atomicité
sans verrou distribué.

**Dragonfly** parle le même protocole et répartit mieux sur plusieurs cœurs. Pour
quelques milliers d'entrées, les deux suffisent ; Dragonfly est le meilleur défaut si
l'infrastructure est à monter.

> **Où RabbitMQ serait légitime** : un bus d'événements durable entre serveurs — un
> événement de Coven qui doit atteindre chaque serveur même si l'un est éteint — ou une
> file de **tâches** de fond, ce que [`cdc_covens.md`](cdc_covens.md) §1 évoque déjà. Pas
> la file de joueurs. Le refus n'est pas une méconnaissance de l'outil, c'est un choix de
> structure.

### 2.2 bis. Ne pas confondre deux choses qui s'appellent « file »

| | File de **joueurs** | File de **tâches** |
|---|---|---|
| Ce qu'elle contient | des gens qui attendent | du travail à faire |
| Ce qu'on lui demande | « où en est ce joueur ? » | « donne-moi le prochain travail » |
| Accès indexé | **indispensable** | inutile |
| Retrait au milieu | **indispensable** | rare |
| Bon outil | ensemble ordonné Redis | courtier de messages |

WizardQueue est la première. Une éventuelle file de tâches est un autre système, et elle
peut très bien employer RabbitMQ sans que cela change quoi que ce soit ici.

### 2.3. Pourquoi pas en mémoire du proxy

Tentant, et suffisant avec un seul proxy. Refusé pour deux raisons : un redémarrage de
proxy effacerait une file de deux cents personnes, et le jour où un second proxy arrive
il n'y a aucun chemin de migration. Le coût d'un Redis est très inférieur à celui de
cette réécriture.

---

## 3. Règles métier

| ID | Règle |
| :--- | :--- |
| **Q-01** | Une file par **destination** (`target`). Une seule destination livrée : `smp`. |
| **Q-02** | Trois niveaux de priorité : `HIGH`, `MEDIUM`, `NONE`. **Un ensemble ordonné par niveau**, pas une priorité encodée dans le score. |
| **Q-03** | Rang dans un niveau = **numéro de séquence** issu d'un compteur partagé (`INCR`), jamais un horodatage. Deux proxies ont des horloges décalées, et deux arrivées dans la même milliseconde seraient à égalité. |
| **Q-04** | La position annoncée est `ZRANK` **au niveau du joueur**, plus le nombre d'entrées des niveaux supérieurs. Exacte, jamais estimée. |
| **Q-05** | Admission selon un **motif fixe et publié**, qui réserve une part des places aux joueurs sans priorité (défaut : 1 sur 3). Voir §4. |
| **Q-06** | Un niveau vide **passe son tour** : le motif glisse au niveau suivant plutôt que de perdre une place. |
| **Q-07** | **Débit d'admission borné** (défaut 1 toutes les 2 s par destination), même quand il reste de la place. Rejoindre coûte du chargement de chunks et de données. |
| **Q-08** | Une admission place le joueur en état **`PENDING`** avec une échéance (défaut 20 s). Le slot n'est libéré qu'à la confirmation de connexion, ou à l'échéance. |
| **Q-09** | Une admission qui échoue ou expire remet le joueur **en tête de son niveau**, pas en queue. Il n'a rien fait de mal. |
| **Q-10** | **Grâce de reconnexion** (défaut 60 s) : une déconnexion déplace l'entrée dans une clef à expiration qui conserve son score. Une reconnexion dans le délai restitue la position **exacte**. |
| **Q-11** | Le backend publie un **battement de cœur** : en ligne, joueurs, maximum, slots réservés. La file lit cette valeur, elle ne la devine pas. |
| **Q-12** | **Battement de cœur périmé → la file retient tout le monde** et annonce que la destination est injoignable. Elle ne se vide jamais par défaut de surveillance. |
| **Q-13** | `wizardmc.queue.bypass` contourne la file **et consomme un slot réservé** du backend. Les deux, sinon le backend refuse après que la file a été sautée. |
| **Q-14** | Toute tentative de connexion directe à une destination en file est **redirigée vers le Seuil** puis mise en file. On n'attend jamais dans le vide (Q-01 de l'intention). |
| **Q-15** | Une expulsion du backend pour cause de saturation ou de redémarrage renvoie au Seuil avec **remise en tête de file**, jamais une déconnexion. |
| **Q-16** | Un joueur inactif est retiré de la file après un délai (défaut 10 min), **après un avertissement** à 1 minute. Détecté par le Seuil, appliqué par la file. |
| **Q-17** | Un joueur n'est dans **qu'une seule** file à la fois. Une nouvelle mise en file remplace la précédente. |
| **Q-18** | Les opérations composées — admettre, réordonner, promouvoir, reprendre une grâce — sont des **scripts Lua**. Pas de verrou distribué. |
| **Q-19** | La part réservée aux non-prioritaires est **configurable et publiée aux joueurs**. Une file qui paraît injuste est pire qu'une file longue. |
| **Q-20** | Aucune priorité ne s'achète au-delà des niveaux déclarés. Pas d'achat de place, pas de saut ponctuel, pas d'enchère. |

---

## 4. L'admission, et l'anti-famine

### 4.1. Le problème, posé franchement

WizardMC interdit tout avantage payant. **La priorité de file en est-elle un ?**

| Argument | Poids |
|---|---|
| Elle donne de l'**accès**, pas de la puissance. Même famille que `/workbench` permanent : du confort. | pour |
| Mais pendant le **Grand Cataclysme**, un VIP qui entre pendant qu'un autre attend quarante minutes joue la phase décisive, et l'autre non. | **contre** |

Le second argument est réel : ce n'est pas de la puissance achetée, c'est du **temps de
jeu au moment décisif**. Sans garde-fou, la priorité de file franchit la ligne que
[`cdc_boutique.md`](cdc_boutique.md) trace.

### 4.2. La réponse : une part réservée, pas un vieillissement

**Motif d'admission fixe.** Avec la valeur par défaut, une place sur trois va aux
joueurs sans priorité :

| Tour | Niveau servi |
|---:|---|
| 1 | `HIGH`, sinon `MEDIUM`, sinon `NONE` |
| 2 | `MEDIUM`, sinon `HIGH`, sinon `NONE` |
| 3 | **`NONE`**, sinon `HIGH`, sinon `MEDIUM` |

Conséquences :

| Propriété | Pourquoi elle compte |
|---|---|
| La file des non-prioritaires **avance toujours** | Pas de famine, quel que soit le flux de VIP |
| Le pire cas est **borné** | Un non-prioritaire progresse au tiers du débit, au minimum |
| Ça s'explique en une phrase | « Une place sur trois est réservée aux joueurs sans priorité » |
| C'est **bon marché** | Trois ensembles ordonnés, un `ZPOPMIN` sur le bon, un curseur de motif |

### 4.3. Pourquoi pas un vieillissement des scores

L'autre solution classique est de faire **vieillir** la priorité : le score d'un joueur
s'améliore avec l'attente, et il finit par dépasser un VIP arrivé après lui.

Refusé pour trois raisons :

1. Un score qui change dans le temps demande de **réécrire l'ensemble** périodiquement.
   Borné, mais inutilement coûteux.
2. Le pire cas devient **difficile à énoncer** : il dépend du taux de vieillissement et
   du flux d'arrivées.
3. Un joueur ne peut plus **vérifier** qu'on l'a traité correctement. Le motif fixe, lui,
   se compte à l'œil.

### 4.4. Le Grand Cataclysme

C'est le moment où la priorité pèse le plus. La part réservée étant une clef de
configuration, la recommandation est de l'**élargir pendant le Cataclysme** — une place
sur deux au lieu d'une sur trois.

Ce n'est pas un réglage automatique : c'est une décision d'exploitation, à prendre avec
la clôture d'Ère. Voir [`cdc_eres.md`](cdc_eres.md).

### 4.5. Le débit, et pourquoi il est lent même quand il reste de la place

Rejoindre un serveur coûte : chargement de chunks, lecture du profil, du grimoire, du
journal de quêtes, de l'état du Coven. Libérer vingt places d'un coup fait arriver vingt
joueurs en même temps, et le serveur s'étrangle sur ce qu'il n'a pas le temps de charger.

D'où **Q-07** : un débit borné, indépendant de la place restante. Une place toutes les
deux secondes remplit cent slots en trois minutes, ce qui est largement assez rapide et
n'a jamais fait tomber un serveur.

### 4.6. Le slot fantôme

Un joueur admis qui ne se connecte pas — il a fermé le client, il est parti — tiendrait
une place indéfiniment.

D'où l'état **`PENDING`** de **Q-08** : le slot lui est réservé pour vingt secondes. À
l'échéance, il est rendu, et le joueur **retourne en tête de son niveau** (**Q-09**) :
l'échec peut venir du serveur, pas de lui.

---

## 5. Stockage

Toutes les clefs sont préfixées `wq:`. Les durées sont en secondes.

```text
wq:seq                        STRING   compteur monotone partagé (INCR)
wq:q:<target>:<tier>          ZSET     membre = UUID, score = numéro de séquence
wq:p:<uuid>                   HASH     target, tier, seq, state, proxy, joinedAt
wq:pending:<target>           ZSET     membre = UUID, score = échéance (epoch ms)
wq:grace:<uuid>               HASH+TTL target, tier, seq — position conservée
wq:srv:<target>               HASH+TTL online, players, max, reserved, updatedAt
wq:cursor:<target>            STRING   position dans le motif d'admission
wq:events                     PUBSUB   admissions, retraits, changements de capacité
```

### Ce que chaque clef règle

| Clef | Rôle | Pourquoi cette forme |
|---|---|---|
| `wq:seq` | L'ordre d'arrivée | Un compteur partagé est insensible aux horloges décalées et ne produit jamais d'égalité |
| `wq:q:*` | La file elle-même | Un ensemble ordonné donne position, retrait au milieu et tête en O(log N) |
| `wq:p:*` | L'état d'un joueur | Permet de retrouver sa file sans parcourir les trois ensembles |
| `wq:pending:*` | Les admissions en vol | Un ensemble ordonné par échéance : le balayage est un `ZRANGEBYSCORE` |
| `wq:grace:*` | La grâce de reconnexion | Une expiration native fait le ménage sans balayage |
| `wq:srv:*` | La capacité réelle | **Avec expiration** : une clef périmée est la façon dont la file apprend que le backend est muet |
| `wq:cursor:*` | Le motif d'admission | Partagé, pour que deux proxies ne servent pas le même niveau |
| `wq:events` | La notification | Permet au Seuil de réagir à l'instant, sans interrogation régulière |

### L'expiration de `wq:srv:*` est une décision

Le battement de cœur du backend porte une expiration légèrement supérieure à sa période
d'écriture. **Une clef absente signifie « je ne sais pas », pas « zéro joueur ».**

C'est ce qui permet **Q-12** : sans nouvelle du backend, la file retient tout le monde et
le dit. Une file qui se viderait par défaut de surveillance enverrait quatre cents
joueurs sur un serveur peut-être éteint.

### Les scripts Lua

| Script | Ce qu'il fait atomiquement |
|---|---|
| `enqueue` | Retire le joueur de toute file, lit une séquence, insère dans le bon niveau, écrit son état |
| `admit` | Lit la capacité, vérifie le débit, avance le curseur de motif, dépile le bon niveau, inscrit en `PENDING` |
| `confirm` | Retire de `PENDING`, efface l'état du joueur |
| `expire` | Balaye `PENDING`, remet les expirés **en tête** de leur niveau |
| `hold` | Déplace une entrée vers sa clef de grâce |
| `resume` | Restitue une entrée depuis sa grâce, au score exact |

Un script, une opération composée, aucune course. Pas de verrou distribué, donc pas de
verrou à libérer quand un proxy meurt au mauvais moment.

---

## 6. Protocole et intégration

### 6.1. Avec le Seuil

Deux canaux, et le partage n'est pas arbitraire.

| Canal | Pour quoi | Pourquoi celui-là |
|---|---|---|
| **Messages de greffon** | Les actions du joueur : rejoindre, quitter, signaler une inactivité | Ils passent par **sa propre connexion** : il ne peut pas mettre un autre en file. L'authentification est gratuite. |
| **Redis** | L'état que le Seuil affiche : positions, capacité, admissions | Le Seuil en a besoin pour **tous** ses joueurs à la fois, et la publication donne la notification immédiate |

Une action du joueur par message de greffon, un état par Redis. Faire passer les actions
par Redis obligerait à réinventer une authentification que la connexion fournit déjà.

### 6.2. Avec le backend

| Sens | Contenu |
|---|---|
| Backend → Redis | Battement de cœur : en ligne, joueurs, maximum, slots réservés |
| Redis → file | Lecture de la capacité à chaque tour d'admission |
| Proxy → backend | La connexion elle-même, avec le rappel de succès |

### 6.3. Avec le client

**Aucun échange direct.** La file ne parle jamais au client : le Seuil s'en charge sur
son propre paquet.

C'est la règle de propriété du projet — le paquet appartient au greffon qui l'émet — et
elle évite d'avoir à enregistrer un paquet client depuis le proxy.

---

## 7. Permissions

| Nœud | Ce qu'il donne |
|---|---|
| `wizardmc.queue.bypass` | Contourne la file **et** consomme un slot réservé du backend |
| `wizardmc.queue.priority.high` | Niveau `HIGH` |
| `wizardmc.queue.priority.medium` | Niveau `MEDIUM` |
| `wizardmc.queue.admin` | Voir l'état des files, promouvoir, retirer, purger |

Un joueur prend le **meilleur** niveau qu'il possède. Aucun nœud ne donne `NONE` : c'est
le défaut.

### Le contournement doit être honoré deux fois

C'est le détail que **Q-13** protège, et c'est le bug classique de ce genre de système.

Un membre du staff qui saute la file arrive sur un backend dont le maximum est atteint,
et **le backend le refuse**. Il conclut que la file est cassée.

D'où les **slots réservés** du battement de cœur : le backend garde quelques places
utilisables uniquement par les porteurs du contournement. Le contournement saute la file,
les slots réservés lui ouvrent la porte. Les deux, ou aucun des deux.

---

## 8. Configuration

| Clef | Ce qu'elle règle | Défaut |
|---|---|---|
| `redis.host`, `redis.port`, `redis.password` | Accès à Redis ou Dragonfly | — |
| `redis.database` | Base logique | 0 |
| `targets.<id>.server` | Le serveur proxy visé | — |
| `targets.<id>.admissionIntervalMs` | Débit d'admission | 2000 |
| `targets.<id>.pendingTimeoutMs` | Échéance d'une admission | 20000 |
| `targets.<id>.reservedShare` | Part réservée aux non-prioritaires, en nombre de tours | 3 |
| `targets.<id>.graceSeconds` | Grâce de reconnexion | 60 |
| `targets.<id>.idleTimeoutSeconds` | Retrait pour inactivité | 600 |
| `targets.<id>.idleWarningSeconds` | Avertissement avant retrait | 60 |
| `heartbeat.staleAfterSeconds` | Au-delà, le backend est réputé muet | 15 |
| `positionRefreshMs` | Période de rafraîchissement des positions | 2000 |

### Les deux clefs à ne pas toucher sans réfléchir

**`admissionIntervalMs`.** La baisser fait arriver les joueurs plus vite et étrangle le
backend sur le chargement. Deux secondes remplissent cent places en trois minutes, ce
qui n'a jamais été un problème. **Le symptôme d'une valeur trop basse n'est pas une file
lente : c'est un serveur qui rame à chaque vague d'admissions.**

**`reservedShare`.** C'est le garde-fou anti-famine, et c'est la clef qui décide si la
priorité payante respecte le principe du projet. La monter à 2 élargit la part des
non-prioritaires ; la descendre à 1 supprime la part réservée et **rend la priorité
illimitée**. Ne jamais la mettre à 1.

---

## 9. Performance

### Les coûts par opération

| Opération | Coût |
|---|---|
| Mise en file | O(log N), un script |
| Position d'un joueur | O(log N) |
| Retrait | O(log N) |
| Admission | O(log N), un script, une fois par intervalle |
| Balayage des admissions expirées | O(log N + k), k = expirés |

### L'optimisation qui compte

Rafraîchir la position de deux mille joueurs avec deux mille `ZRANK` par cycle fait deux
mille allers-retours. À la place : **un `ZRANGE` complet par niveau et par cycle**, puis
le calcul des index en local.

| Approche | Pour 2 000 joueurs en file, toutes les 2 s |
|---|---|
| Un `ZRANK` par joueur | 2 000 allers-retours |
| Un `ZRANGE` par niveau | **3 allers-retours**, une réponse de 2 000 éléments |

### L'émission vers le client

Une position n'est envoyée que **lorsqu'elle a changé**, et au plus une fois par cycle.
Un joueur dont la position n'a pas bougé ne reçoit rien.

Une admission, elle, est poussée **immédiatement** par la publication : c'est le seul
événement qui ne peut pas attendre deux secondes.

### L'ordre de grandeur visé

| Grandeur | Cible |
|---|---|
| Joueurs en file simultanés | 2 000 sans dégradation |
| Charge Redis | quelques dizaines d'opérations par seconde |
| Latence d'une admission | la publication, soit quelques millisecondes |
| Mémoire Redis | quelques mégaoctets |

Ces chiffres sont confortables : la file n'est pas le composant qui limitera le serveur.

---

## 10. Modes de panne

| Panne | Comportement attendu |
|---|---|
| **Redis injoignable au démarrage** | Le greffon démarre, annonce le défaut **une seule fois**, et laisse passer les connexions sans file. Une file cassée ne doit pas fermer le serveur. |
| **Redis tombe en service** | Les files en mémoire du proxy continuent d'être servies en lecture ; aucune nouvelle admission. Reprise à la reconnexion. |
| **Backend muet** | La file retient tout le monde et l'annonce (**Q-12**). Aucune admission. |
| **Backend qui redémarre** | Les joueurs expulsés reviennent au Seuil, en tête de file (**Q-15**) |
| **Un proxy meurt** | Ses joueurs sont déconnectés, donc en grâce : ils retrouvent leur place en revenant (**Q-10**). Aucun verrou à libérer, les scripts Lua n'en prennent pas. |
| **Deux proxies** | Le curseur de motif et le compteur de séquence sont partagés : aucun double service, aucune égalité |
| **Horloges décalées** | Sans effet : l'ordre vient du compteur, pas de l'heure (**Q-03**) |
| **Le Seuil tombe** | Les joueurs sont déconnectés et entrent en grâce. La file reste intacte. |

### Le mode de panne le plus dangereux

**Redis qui répond mais avec des données d'une autre instance** — une base logique
partagée avec un autre usage, ou un préfixe oublié. Symptôme : des positions absurdes,
des joueurs admis sans être en file.

Garde-fou : un préfixe de clef obligatoire, une base logique dédiée, et un refus de
démarrer si la base contient des clefs inattendues au premier lancement.

---

## 11. Commandes

| Commande | Ce qu'elle fait | Permission |
|---|---|---|
| `/queue` | Sa position, son niveau, l'attente estimée | — |
| `/queue leave` | Quitter la file | — |
| `/queue status` | L'état de toutes les files | `wizardmc.queue.admin` |
| `/queue promote <joueur>` | Placer en tête de son niveau | `wizardmc.queue.admin` |
| `/queue remove <joueur>` | Retirer de la file | `wizardmc.queue.admin` |
| `/queue purge <target>` | Vider une file | `wizardmc.queue.admin` |
| `/queue pause <target>` | Suspendre les admissions | `wizardmc.queue.admin` |

`/queue promote` est un outil de réparation, pas de faveur. Comme toute correction, son
usage se note — voir [Modération](../05-operer/moderation.md).

---

## 12. L'attente estimée

Annoncée, mais **présentée comme une estimation**, contrairement à la position.

Calcul : position divisée par le débit d'admission observé sur les dernières minutes, et
non sur le débit configuré. Le débit réel dépend des départs du SMP, qu'on ne contrôle
pas.

| Règle | Pourquoi |
|---|---|
| Jamais une valeur précise à la seconde | « 12 min » est crédible, « 11 min 43 s » ne l'est pas |
| Arrondir à la minute au-dessus de 2 minutes | — |
| Ne rien annoncer si le débit observé est nul | Une estimation infinie vaut mieux tue |
| Ne jamais afficher une estimation qui remonte | Un joueur qui voit son attente augmenter perd confiance, même si c'est vrai |

La dernière règle impose de **lisser vers le bas seulement** : l'estimation affichée ne
remonte pas, elle stagne.

---

## 13. QA

### Le nominal

- [ ] Un joueur sans priorité entre en file et voit une position exacte
- [ ] La position décroît à mesure que les admissions se font
- [ ] L'admission téléporte vers le SMP, avec son et titre
- [ ] `/queue leave` sort de la file

### La priorité

- [ ] Un `MEDIUM` passe devant un `NONE` arrivé avant lui
- [ ] Un `HIGH` passe devant un `MEDIUM`
- [ ] **Une place sur trois va à un `NONE`**, même avec une file de VIP continue
- [ ] Un joueur prend le meilleur niveau qu'il possède

### Le contournement

- [ ] Un porteur de `bypass` ne voit jamais la file
- [ ] **Il entre même quand le backend est à son maximum** (slot réservé)
- [ ] Un non-porteur ne peut pas consommer un slot réservé

### La robustesse

- [ ] Une coupure de 5 s rend la position **exacte** au retour
- [ ] Une coupure de 2 min fait perdre la place, après expiration de la grâce
- [ ] Une admission non suivie de connexion rend le slot au bout de 20 s
- [ ] Et remet le joueur **en tête** de son niveau
- [ ] Une connexion directe au SMP est redirigée vers le Seuil puis mise en file
- [ ] Une expulsion du SMP renvoie au Seuil, en tête de file

### Les pannes

- [ ] Redis éteint au démarrage : le serveur démarre, l'avertissement est écrit **une fois**
- [ ] Redis éteint en service : aucune admission, aucune perte de file
- [ ] Backend muet : la file retient tout le monde et l'annonce
- [ ] Deux proxies : aucun joueur admis deux fois
- [ ] Redémarrage du proxy : les files sont intactes

### L'échelle

- [ ] 2 000 joueurs en file : positions correctes, charge Redis négligeable
- [ ] Le rafraîchissement consomme **3 allers-retours par cycle**, pas 2 000
- [ ] Un joueur dont la position n'a pas changé ne reçoit aucun paquet

---

## 14. Hors périmètre

| Hors périmètre | Pourquoi |
|---|---|
| Un divertissement de salle d'attente | Une file longue est un problème de capacité, pas d'animation |
| Une place achetable à l'unité | Q-20 : la priorité se limite aux niveaux déclarés |
| Plusieurs destinations | Une seule est livrée. Le modèle en accepte d'autres sans changement. |
| Une file par monde ou par région | Le SMP est un seul serveur |
| Un transfert d'état entre backends | Hors sujet : la file déplace un joueur, pas une partie |

---

## À lire ensuite

- [`cdc_wizardhub.md`](cdc_wizardhub.md) — le Seuil, qui affiche cette file
- [`cdc_boutique.md`](cdc_boutique.md) — la ligne que la priorité ne doit pas franchir
- [Plages d'identifiants](../07-reference/plages-d-identifiants.md) — la file n'en consomme aucun
- [Runbooks](../05-operer/runbooks.md) — les pannes en exploitation
