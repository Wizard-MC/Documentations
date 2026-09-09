# CDC TECHNIQUE — WizardHub

> Le **Seuil** : le serveur lobby. Interface client du passage vers le SMP, carte du
> sorcier, état du monde, vestiaire, et durcissement complet.

**État : spécifié.** Le cahier des charges est arrêté, le code n'existe pas.

Documents liés :

- [`cdc_wizardqueue.md`](cdc_wizardqueue.md) — la file d'attente, tenue par le proxy
- [`cdc_intro.md`](cdc_intro.md) — la cinématique, qui se joue **après** le Seuil
- [`cdc_exp_client.md`](cdc_exp_client.md) — client MCP, HUD, effets
- [`cdc_boutique.md`](cdc_boutique.md) — ce que le vestiaire peut montrer

---

## 1. Objet et intention

Un joueur qui se connecte à WizardMC n'arrive pas dans les Terres Fracturées : il arrive
**au Seuil**. C'est de là qu'il demande le passage, qu'il attend l'Appel, et qu'il
regarde ce qu'il a déjà accompli.

Trois engagements :

1. **Le Seuil est un lieu, pas un écran de chargement.** Il a un décor, une ambiance, et
   des choses à y faire. Un lobby qui n'est qu'un bouton est une occasion perdue.
2. **Il ne retient personne.** Un clic suffit pour demander le passage, et rien n'oblige
   à attendre devant l'écran : l'Appel est assez bruyant pour ramener un joueur parti
   ailleurs.
3. **Il ne décide de rien.** Le Seuil affiche la file, il ne la tient pas. Il affiche la
   progression, il ne la modifie pas. Tout ce qui compte vit ailleurs.

### Ce que le Seuil est dans la fiction

Le Sanctuaire des Origines n'est **pas** le Seuil : c'est le lieu hors du monde où
Aelindra reçoit un sorcier une fois, et où personne ne retourne
([`cdc_intro.md`](cdc_intro.md)).

Le **Seuil** est ce qui précède : l'antichambre entre le Sanctuaire et les Terres. Un
endroit qui n'est pas encore le monde — d'où l'absence de PvP, de construction, et de
quoi que ce soit de permanent.

Et la file d'attente devient **l'Appel**. Le lore dit déjà qu'un sorcier est *invoqué*
dans les Terres Fracturées ; quand les Terres ne peuvent plus en recevoir, on attend
d'être appelé. Le vocabulaire du jeu suit :

| Mécanique | Ce que le joueur lit |
|---|---|
| Le lobby | **le Seuil** |
| La file d'attente | **l'Appel** |
| Être en file | « en attente de l'Appel » |
| Être admis | « l'Appel vous est adressé » |

L'ordre du passage, une fois pour toutes :

```text
client  →  le Seuil  →  l'Appel  →  SMP  →  le Sanctuaire (première fois seulement)
          (WizardHub)  (WizardQueue)       (WizardIntro)
```

---

## 2. Règles métier

| ID | Règle |
| :--- | :--- |
| **H-01** | WizardHub est un greffon **WizardSpigot**. Il ne tient aucune file et ne décide d'aucune admission. |
| **H-02** | Le Seuil est **durci** : aucune casse, aucune pose, aucun PvP, aucun lâcher d'objet, aucune faim, aucun dégât, heure et météo fixes, protection contre le vide. |
| **H-03** | Une demande de passage part en **message de greffon** sur la connexion du joueur. Il ne peut pas mettre un autre joueur en file. |
| **H-04** | L'état de la file est lu dans **Redis**, jamais déduit. Le Seuil affiche ce que le proxy publie. |
| **H-05** | L'interface du Seuil est portée par le **paquet 133**, et c'est le seul paquet du greffon. La file ne parle jamais au client. |
| **H-06** | Un client sans le paquet 133 reste jouable : le Seuil offre les mêmes actions en commandes et en interfaces de coffre. **Le client n'est obligatoire que pour la beauté, pas pour l'accès.** |
| **H-07** | L'interface réutilise la palette et les primitives graphiques du client existant. **Aucun second langage visuel.** |
| **H-08** | La carte du sorcier est **en lecture seule**, et reflète l'état du SMP publié dans Redis. Le Seuil n'écrit jamais dans la progression d'un joueur. |
| **H-09** | L'Appel est annoncé par **un son, un titre et une secousse courte**, même si l'interface est fermée. Un joueur parti sur une autre fenêtre doit être ramené. |
| **H-10** | L'inactivité est détectée par le Seuil et **signalée à la file**, qui décide. Le Seuil n'expulse personne de la file lui-même. |
| **H-11** | Le vestiaire ne montre que des cosmétiques **déjà possédés**. Pas de démonstration de ce qui est à vendre. |
| **H-12** | Le chat du Seuil est **séparé** de celui du SMP. Un message du Seuil ne part pas dans les Terres. |
| **H-13** | Aucune progression ne se gagne au Seuil : ni expérience, ni monnaie, ni cosmétique, ni Poussière d'Étoile. |
| **H-14** | Le Panthéon du Seuil est un **miroir** de celui du spawn SMP. Il n'a pas sa propre vérité. |
| **H-15** | Si Redis est injoignable, le Seuil reste jouable et **le dit** : le passage se demande quand même, la file est alors gérée sans position affichée. |
| **H-16** | Plusieurs Seuils peuvent tourner en parallèle. Aucun état de joueur n'est tenu en mémoire d'une instance. |

---

## 3. Le décor du Seuil

Ce n'est pas du remplissage : un lobby sans lieu ne tient pas la promesse de l'engagement 1.

| Élément | Intention |
|---|---|
| **Une plate-forme suspendue** | Le Seuil n'est pas posé sur la terre. Les Terres Fracturées se voient en dessous, loin, cassées. |
| **Un arc de passage** | L'endroit où l'Appel se produit. Les joueurs admis disparaissent par là. |
| **Le Panthéon du Seuil** | Les Covens vainqueurs des Ères passées. Ce qu'on voit avant d'entrer. |
| **Un cadran d'Ère** | L'Ère en cours, son thème, sa phase, les jours restants |
| **Le vestiaire** | Un coin pour essayer ses cosmétiques |
| **Des bannières de Coven** | Celles des Covens les mieux classés de l'Ère en cours |

### Ce que le décor ne fait pas

| Jamais | Pourquoi |
|---|---|
| Des coffres, des établis, des fours | Ce n'est pas un lieu de jeu |
| Des PNJ marchands | La boutique est une interface, pas un marché |
| Des minijeux | Voir §11 |
| Un parkour | Idem |
| Le Sanctuaire des Origines | Il est hors du monde et ne se visite pas |

---

## 4. L'interface du Seuil

### 4.1. Le langage visuel est déjà écrit

Le client porte une boîte à outils graphique et une palette. **L'interface du Seuil les
réutilise telles quelles** (**H-07**).

| Couleur | Valeur | Usage |
|---|---|---|
| Or parchemin | `0xFFC5A87A` | Titres, bordures, accents |
| Or atténué | `0xB3C5A87A` | Texte secondaire, bordures au repos |
| Bleu nuit | `0xF00F121D` | Fond des panneaux |
| Barre | `0xCC1A1D28` | En-têtes et pieds de panneau |
| Violet arcane | `0xCC1C1240` | Cœur des lueurs |
| Violet médian | `0x661C1240` | Halo |
| Violet bord | `0x221C1240` | Extrémité du halo |

Les primitives disponibles et à employer : remplissage, lignes, bordures, **équerres
d'angle**, lueur en couches, et les adoucissements d'animation (cubique, quartique, et un
léger dépassement pour une apparition).

> Les **équerres d'angle** plutôt qu'un cadre plein sont la signature du projet. Un
> panneau du Seuil s'en sert, comme les autres écrans.

### 4.2. La mise en page

Trois colonnes sur une verticale de titre, en proportions, jamais en pixels absolus — le
client tourne de 1024×768 à 4K.

```text
┌──────────────────────────────────────────────────────────────────┐
│                        LE SEUIL                                  │  ← titre, or
│              « Les Terres vous attendent »                       │  ← sous-titre
├────────────────┬──────────────────────────┬──────────────────────┤
│ CARTE DU       │      L'APPEL             │  L'ÉTAT DU MONDE     │
│ SORCIER        │                          │                      │
│                │   ┌──────────────────┐   │  Ère III · Chaos     │
│ Nom            │   │                  │   │  Jour 31 / 45        │
│ Voie : Givre   │   │   DEMANDER LE    │   │  ───────────         │
│ Écoles …       │   │    PASSAGE       │   │  Panthéon            │
│ Coven …        │   │                  │   │  1. Les Cendres      │
│ Nexus 6        │   └──────────────────┘   │  2. …                │
│ Montures 4/10  │                          │                      │
│ Quêtes 5/7     │   ou, en attente :       │  Annonces            │
│                │   ┌──────────────────┐   │  …                   │
│ [aperçu du     │   │  43ᵉ sur 91      │   │                      │
│  personnage]   │   │  ~9 min          │   │                      │
│                │   │  [====      ]    │   │                      │
│                │   │  Quitter l'Appel │   │                      │
│                │   └──────────────────┘   │                      │
├────────────────┴──────────────────────────┴──────────────────────┤
│  Vestiaire   ·   Boutique   ·   Wiki   ·   Discord               │  ← pied
└──────────────────────────────────────────────────────────────────┘
```

| Zone | Contenu |
|---|---|
| **Titre** | « LE SEUIL », et une ligne d'ambiance qui change selon la phase d'Ère |
| **Colonne gauche** | La carte du sorcier, et l'aperçu du personnage avec ses cosmétiques |
| **Colonne centrale** | Le passage : un seul bouton, ou l'état de l'Appel |
| **Colonne droite** | L'Ère, le Panthéon, les annonces |
| **Pied** | Vestiaire, boutique, wiki, lien externe |

### 4.3. Les six états de la colonne centrale

C'est la seule partie dont l'état change, et chacun doit être lisible sans texte
d'explication.

| État | Ce qu'on voit | Ce qu'on peut faire |
|---|---|---|
| **Prêt** | Un bouton « Demander le passage », en or, avec une lueur lente | Demander |
| **En attente** | Position exacte, attente estimée, une barre de progression | Quitter |
| **Appelé** | L'arc s'ouvre, un compte à rebours court, son et titre | Rien : le passage se fait |
| **Plein sans file** | « Les Terres sont au complet » et le nombre de joueurs | Demander quand même, ce qui met en file |
| **Injoignable** | « Les Terres ne répondent pas » | Rien. Le bouton est éteint, pas absent. |
| **Contournement** | « Le passage vous est ouvert », sans position | Passer immédiatement |

### Les deux états qu'on rate toujours

**Injoignable.** Un bouton qui disparaît fait croire à un bug du client. Un bouton éteint
avec une phrase qui dit pourquoi est la seule présentation acceptable.

**Appelé.** Le compte à rebours n'est pas décoratif : il correspond à l'échéance réelle
de l'admission côté file. Un joueur qui voit « 18, 17, 16… » comprend qu'il doit être là,
et la valeur vient du proxy, pas d'une animation.

### 4.4. Les mouvements

| Moment | Mouvement |
|---|---|
| Ouverture | Les trois colonnes montent et se révèlent en décalé, adoucissement quartique |
| Changement de position | Le nombre glisse d'un cran, la barre s'anime. **Jamais de clignotement.** |
| Appel | La lueur violette de l'arc monte en couches, le titre s'inscrit |
| Fermeture | Descente simple, plus rapide que l'ouverture |

Le décalage à l'ouverture — chaque colonne un peu après la précédente — est ce qui
distingue une interface soignée d'une interface qui apparaît d'un bloc.

### 4.5. Ce que l'interface ne fait pas

| Jamais | Pourquoi |
|---|---|
| Bloquer la fermeture | On doit pouvoir jouer au Seuil sans l'interface |
| S'ouvrir toute seule plus d'une fois | À la connexion, et c'est tout |
| Afficher une position estimée | La position est exacte, ou absente |
| Afficher une attente qui remonte | Un joueur qui voit son attente augmenter perd confiance |
| Demander le passage automatiquement | La demande est un geste du joueur |

---

## 5. La carte du sorcier

Un résumé en lecture seule de ce que le joueur a accompli sur le SMP. C'est ce qui donne
au Seuil une raison d'exister au-delà d'une salle d'attente.

| Champ | Source |
|---|---|
| Nom et aperçu du personnage | Le client |
| Voie, et niveaux des neuf écoles | Magie |
| Sorts connus, sur 41 | Magie |
| Coven, rôle, tag | Covens |
| Niveau du Nexus et Éclats | Nexus |
| Autels tenus par son Coven | Autels |
| Montures débloquées, sur 10 | Montures |
| Quêtes terminées, sur 7 | Quêtes |
| Compagnon actif | Compagnons |

### Comment elle arrive

Le SMP publie un **instantané** par joueur dans Redis à la déconnexion, et le rafraîchit
périodiquement pour les joueurs connectés. Le Seuil le lit.

| Décision | Pourquoi |
|---|---|
| Un instantané, pas une requête | Le Seuil ne doit pas pouvoir ralentir le SMP en demandant des données |
| Publié à la déconnexion | C'est le moment où la valeur est juste, et où le joueur va la voir |
| **Lecture seule** (**H-08**) | Le Seuil qui écrirait dans la progression serait une seconde source de vérité |
| Un instantané absent n'est pas une erreur | Un joueur qui n'a jamais joué voit une carte vierge, pas un message d'erreur |

### Ce qu'elle ne montre pas

| Jamais | Pourquoi |
|---|---|
| La position d'un joueur dans le monde | Un lobby ne révèle pas où sont les gens |
| Le contenu d'un inventaire | — |
| Les Autels tenus par les **autres** Covens | Ce serait du renseignement gratuit |
| Le stock de Mana Brut d'un Coven | Idem |
| La progression d'un **autre** joueur | La carte est la sienne |

La ligne est claire : la carte dit ce que **le joueur** a fait, jamais ce que les autres
font en ce moment.

---

## 6. L'état du monde

| Élément | Contenu |
|---|---|
| **Ère** | Numéro, nom, thème, phase, jours restants |
| **Grand Cataclysme** | Une bannière franche pendant les trois derniers jours |
| **Panthéon** | Les cinq derniers Covens vainqueurs |
| **Annonces** | Les entrées récentes du journal des Ères |
| **Fréquentation** | Joueurs sur le SMP, et joueurs au Seuil |

### La bannière de Cataclysme

Pendant les trois derniers jours d'une Ère, elle occupe le haut de l'interface. C'est le
seul élément autorisé à interrompre la mise en page.

La raison est de conception : le Cataclysme est la fenêtre de rattrapage, et un joueur
qui se connecte sans savoir qu'on y est la rate. Voir [`cdc_eres.md`](cdc_eres.md).

---

## 7. Le vestiaire

Essayer ses cosmétiques — auras, traces, titres, apparences de baguette et de compagnon —
avec un aperçu du personnage.

| Règle | Pourquoi |
|---|---|
| Seulement ce qui est **déjà possédé** (**H-11**) | Un vestiaire qui montre ce qui est à vendre est une vitrine, et le projet ne vend pas d'avantage : inutile d'en faire une pression |
| Aucun achat depuis le vestiaire | La boutique est une interface distincte |
| Le choix est **écrit sur le SMP**, pas au Seuil | Sinon le cosmétique choisi au Seuil ne suivrait pas |

Le dernier point impose un écrit : le Seuil publie le choix, le SMP l'applique à la
connexion. C'est la **seule** écriture que le Seuil produit, et elle ne touche qu'un
cosmétique.

---

## 8. Le durcissement

**H-02**, en détail. Chaque ligne a déjà été un incident sur un lobby quelque part.

| Interdit | Conséquence si on l'oublie |
|---|---|
| Casser, poser | Un lobby devient une ruine en une soirée |
| PvP, dégâts de toute nature | Un joueur tué au lobby, c'est un ticket |
| Lâcher, ramasser un objet | Le sol se couvre d'objets, le serveur rame |
| Faim | Un joueur affamé au lobby ne peut rien y faire |
| Feu, explosions, propagation | — |
| Interaction avec les blocs | Portes, leviers, coffres : tout inerte |
| Commandes du SMP | Elles n'ont aucun sens ici, et certaines écriraient dans la progression |
| Chute dans le vide | Renvoi au point d'apparition, pas de mort |
| Heure et météo variables | Le Seuil a une ambiance fixe, c'est un décor |
| Mobs, y compris paisibles | Le Seuil n'est pas un monde vivant |

### L'anti-inactivité

Un joueur inactif au Seuil est le pire cas d'une file : il occupe une place, puis il
occupe un slot d'admission qu'il ne consommera jamais.

| Étape | Délai par défaut |
|---|---|
| Avertissement | après 9 minutes |
| Signalement à la file | après 10 minutes |

Le Seuil **détecte** et **signale** ; la file décide (**H-10**). Un lobby qui retirerait
lui-même un joueur de la file écrirait dans un état qui ne lui appartient pas.

> L'avertissement une minute avant n'est pas une politesse : sans lui, un joueur qui
> revient après dix minutes a perdu quarante minutes d'attente sans comprendre pourquoi.

---

## 9. Le protocole

### 9.1. Paquet 133 — le Seuil

Le seul paquet du greffon (**H-05**), dans les deux sens.

| Sens | Message | Contenu |
|---|---|---|
| S → C | `OPEN` | Ouvre l'interface |
| S → C | `STATE` | L'état de la colonne centrale, la position, l'attente, le niveau |
| S → C | `CARD` | La carte du sorcier |
| S → C | `WORLD` | Ère, phase, Panthéon, annonces, fréquentation |
| S → C | `CALLED` | L'Appel : échéance, et l'ordre de jouer l'annonce |
| S → C | `WARDROBE` | Les cosmétiques possédés |
| C → S | `REQUEST_PASSAGE` | Demande de passage |
| C → S | `LEAVE` | Quitter l'Appel |
| C → S | `WARDROBE_SET` | Choix de cosmétique |
| C → S | `CLOSE` | L'interface a été fermée |

**Le prochain identifiant de paquet libre est 133** — voir
[Plages d'identifiants](../07-reference/plages-d-identifiants.md). Après celui-ci : 134.

### Ce qui n'est jamais envoyé au client

| Jamais | Pourquoi |
|---|---|
| La liste des joueurs en file | Un client modifié en tirerait la composition de la file |
| La position d'un autre joueur | — |
| Les slots réservés restants | Un client saurait quand le contournement est à saturation |
| L'identité des porteurs de priorité | — |

### 9.2. Avec la file

| Canal | Pour quoi |
|---|---|
| **Message de greffon** | Les actions du joueur : demander, quitter, signaler une inactivité (**H-03**) |
| **Redis** | L'état affiché : positions, capacité, admissions (**H-04**) |

Le partage n'est pas arbitraire. Un message de greffon passe par **la connexion du
joueur** : l'authentification est gratuite, et il ne peut pas mettre un autre en file.
Redis donne l'état pour **tous** les joueurs d'un coup, et la publication donne la
notification immédiate de l'Appel.

Le détail du contrat est dans [`cdc_wizardqueue.md`](cdc_wizardqueue.md) §6.

### 9.3. Avec le SMP

Aucun échange direct. Le SMP publie des instantanés dans Redis ; le Seuil les lit. Les
deux ne se connaissent pas.

---

## 10. Le repli sans client

**H-06** : un client qui ne connaît pas le paquet 133 doit rester jouable.

| Action | Repli |
|---|---|
| Demander le passage | `/passage`, ou un PNJ à l'arc |
| Voir sa position | `/queue`, porté par la file |
| Quitter l'Appel | `/queue leave` |
| Sa carte du sorcier | `/carte`, en texte |
| L'état du monde | `/era`, déjà existant |
| Le vestiaire | Une interface de coffre |

### Pourquoi ce repli existe alors que le client est obligatoire

Le client **est** obligatoire pour jouer sur le SMP, et la poignée de main l'impose. Mais
le Seuil est le premier contact : un joueur dont le client vient d'échouer à se mettre à
jour doit pouvoir arriver au Seuil, comprendre ce qui se passe, et lire le message qui le
lui explique.

> **Un joueur expulsé sans explication est un joueur perdu.** Le repli n'est pas une
> concession à la compatibilité, c'est le canal de diagnostic.

---

## 11. Hors périmètre

| Hors périmètre | Pourquoi |
|---|---|
| **Des minijeux** | Ils entrent en concurrence avec le SMP pour l'attention, demandent leur propre équilibrage et leur propre modération. Un lobby se juge à la vitesse avec laquelle on le quitte. |
| **Un parkour** | Idem, en plus petit |
| **Un divertissement d'attente** | Une file longue est un problème de capacité. Voir [`cdc_wizardqueue.md`](cdc_wizardqueue.md) §1. |
| **Une progression propre au Seuil** | **H-13**. Un lobby qui récompense l'attente récompense de ne pas jouer. |
| **Un chat global avec le SMP** | **H-12**. Deux lieux, deux conversations. |
| **Un classement des joueurs** | Le Panthéon classe des Covens, et c'est assez |
| **Des échanges entre joueurs** | Le Seuil n'a pas d'économie |
| **Le choix de l'école** | Il appartient à la cinématique, et il se fait une seule fois |

### La tentation à nommer

Mettre un parkour ou un minijeu dans un lobby est l'idée la plus fréquente, et la plus
coûteuse. Elle suppose que le problème est l'ennui, alors que le problème est l'attente.

**La bonne réponse à une file de quarante minutes est un slot de plus**, et la bonne
réponse à un joueur qui s'ennuie est qu'il puisse partir sans perdre sa place — ce que la
grâce de reconnexion et l'annonce sonore garantissent.

---

## 12. Configuration

| Clef | Ce qu'elle règle | Défaut |
|---|---|---|
| `redis.*` | Accès à Redis ou Dragonfly, partagé avec la file | — |
| `seuil.world` | Le monde du Seuil | — |
| `seuil.spawn` | Le point d'apparition | — |
| `seuil.arch` | La position de l'arc de passage | — |
| `seuil.pantheon` | La position du Panthéon du Seuil | — |
| `seuil.time`, `seuil.weather` | Heure et météo fixes | — |
| `target` | La destination demandée par le bouton | `smp` |
| `interface.openOnJoin` | Ouvrir l'interface à la connexion | vrai |
| `interface.refreshMs` | Période de rafraîchissement | 2000 |
| `idle.warningSeconds` | Avertissement d'inactivité | 540 |
| `idle.reportSeconds` | Signalement à la file | 600 |
| `card.snapshotTtlSeconds` | Durée de vie d'un instantané de carte | 86400 |
| `announcements.max` | Nombre d'annonces affichées | 3 |

### Les deux clefs à ne pas toucher sans réfléchir

**`interface.refreshMs`.** La descendre multiplie les paquets sans rien améliorer : la
position ne change pas plus souvent que les admissions. Deux secondes est le même rythme
que celui de la file, et les deux doivent rester alignés.

**`idle.reportSeconds`.** Le descendre retire de la file des joueurs qui lisent le
Panthéon. Le monter laisse des places occupées par des absents. Dix minutes avec
avertissement est le compromis qui ne produit ni l'un ni l'autre.

---

## 13. Performance

Le Seuil n'est pas un composant chaud, mais il peut le devenir par inadvertance.

| Risque | Mesure |
|---|---|
| Un paquet par joueur par cycle | N'envoyer `STATE` que **lorsque l'état a changé** |
| Une lecture Redis par joueur par cycle | **Une** lecture par destination et par cycle, index calculés en local |
| Un aperçu de personnage rendu par joueur | Rendu **client**, aucun coût serveur |
| Des instantanés de carte illimités | Expiration d'un jour |
| Un monde du Seuil chargé en continu | Un seul monde, petit, sans génération |

### L'ordre de grandeur visé

| Grandeur | Cible |
|---|---|
| Joueurs au Seuil simultanés | 2 000 |
| Paquets par seconde en régime stable | quelques dizaines, pas quelques milliers |
| Lectures Redis par seconde | une poignée |
| Charge CPU du Seuil | négligeable : rien ne simule |

Le Seuil ne simule aucune entité, aucun mob, aucune physique. S'il consomme du CPU, c'est
un défaut.

---

## 14. Modes de panne

| Panne | Comportement attendu |
|---|---|
| **Redis injoignable** | Le Seuil reste jouable, annonce le défaut **une fois**, et la demande de passage part quand même en message de greffon. La position n'est pas affichée (**H-15**). |
| **Proxy injoignable** | Impossible en pratique : le joueur y est connecté |
| **SMP muet** | L'état « Injoignable », bouton éteint avec explication |
| **Instantané de carte absent** | Une carte vierge, pas un message d'erreur |
| **Client sans le paquet 133** | Le repli du §10 |
| **Deux Seuils** | Aucun état en mémoire : les deux affichent la même chose (**H-16**) |
| **Monde du Seuil absent** | Le greffon refuse de démarrer et le dit. Générer un monde sans qu'on l'ait demandé est plus dangereux. |

Le dernier point reprend la règle déjà en vigueur pour le monde de la cinématique
([`cdc_intro.md`](cdc_intro.md), **I-08**).

---

## 15. Commandes

| Commande | Ce qu'elle fait | Permission |
|---|---|---|
| `/seuil` | Ouvre l'interface | — |
| `/passage` | Demande le passage | — |
| `/carte` | Sa carte du sorcier, en texte | — |
| `/vestiaire` | Le vestiaire | — |
| `/seuil reload` | Recharge la configuration | `wizardmc.hub.admin` |
| `/seuil announce <texte>` | Ajoute une annonce | `wizardmc.hub.admin` |

`/queue` et `/queue leave` appartiennent à la file, pas au Seuil.

---

## 16. Permissions

| Nœud | Ce qu'il donne |
|---|---|
| `wizardmc.hub.admin` | Recharger, annoncer |
| `wizardmc.hub.build` | Construire au Seuil, pour l'aménager |

`wizardmc.hub.build` est un nœud d'aménagement, pas de jeu : il désactive le durcissement
pour son porteur. À ne donner que le temps d'un chantier, et à retirer après — un lobby
où un bâtisseur a gardé ses droits est un lobby qui finira modifié par accident.

---

## 17. QA

### Le nominal

- [ ] La connexion pose le joueur au Seuil, l'interface s'ouvre une fois
- [ ] « Demander le passage » met en file, l'état passe à « En attente »
- [ ] La position affichée est **exacte**, et identique à `/queue`
- [ ] L'Appel joue un son, un titre, et une secousse courte
- [ ] Le passage aboutit sur le SMP
- [ ] À la première connexion, la cinématique se joue **après** le passage

### L'interface

- [ ] Les trois colonnes montent en décalé à l'ouverture
- [ ] La position glisse d'un cran, sans clignoter
- [ ] L'attente estimée **ne remonte jamais**
- [ ] L'état « Injoignable » montre un bouton **éteint**, pas absent
- [ ] L'état « Appelé » affiche un compte à rebours qui vient du proxy
- [ ] La mise en page tient de 1024×768 à 4K
- [ ] La fermeture de l'interface ne bloque rien
- [ ] La bannière de Cataclysme occupe le haut pendant les trois derniers jours

### La carte

- [ ] Elle reflète l'état du SMP du joueur
- [ ] Un joueur qui n'a jamais joué voit une carte vierge
- [ ] Elle ne montre **rien** d'un autre joueur
- [ ] Elle ne montre aucune position dans le monde

### Le durcissement

- [ ] Impossible de casser ou poser
- [ ] Impossible de se faire du mal ou d'en faire
- [ ] Impossible de lâcher un objet
- [ ] Pas de faim, pas de dégâts de chute
- [ ] La chute dans le vide renvoie au point d'apparition
- [ ] Heure et météo fixes
- [ ] Aucun mob
- [ ] Les commandes du SMP sont refusées

### L'inactivité

- [ ] Un avertissement tombe une minute avant le signalement
- [ ] Le Seuil **signale**, la file décide
- [ ] Bouger ou cliquer remet le compteur à zéro

### Le repli

- [ ] Un client sans le paquet 133 peut demander le passage en commande
- [ ] `/carte` donne la carte en texte
- [ ] Redis éteint : le Seuil reste jouable, l'avertissement est écrit **une fois**
- [ ] Redis éteint : la demande de passage fonctionne quand même

### La sécurité

- [ ] Un client ne peut pas mettre **un autre joueur** en file
- [ ] Aucun paquet ne contient la liste des joueurs en file
- [ ] Aucun paquet ne contient la position d'un autre joueur
- [ ] Le vestiaire refuse un cosmétique non possédé

---

## À lire ensuite

- [`cdc_wizardqueue.md`](cdc_wizardqueue.md) — la file, et la décision de la mettre sur le proxy
- [`cdc_intro.md`](cdc_intro.md) — ce qui se passe après le passage, la première fois
- [`cdc_exp_client.md`](cdc_exp_client.md) — le client, le HUD, les effets
- [Plages d'identifiants](../07-reference/plages-d-identifiants.md) — le paquet 133
