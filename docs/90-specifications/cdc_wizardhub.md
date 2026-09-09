# CDC TECHNIQUE — WizardHub

> Le **serveur lobby** : serveur de repli du proxy, et interface de navigation vers le
> SMP. Greffon WizardSpigot.

**État : spécifié.** Le cahier des charges est arrêté, le code n'existe pas.

Documents liés :

- [`cdc_wizardqueue.md`](cdc_wizardqueue.md) — la file d'attente, tenue par le proxy
- [`cdc_exp_client.md`](cdc_exp_client.md) — client MCP, HUD, effets
- [`cdc_intro.md`](cdc_intro.md) — la cinématique, qui appartient **au SMP** et pas au lobby

---

## 1. Objet et intention

Le lobby est une **brique d'infrastructure**. Il remplit deux rôles, et le premier
commande tout le reste.

| Rôle | Ce que c'est |
|---|---|
| **1. Serveur de repli du proxy** | Là où un joueur atterrit quand un SMP tombe, le refuse, ou l'expulse. |
| **2. Interface de navigation** | L'écran depuis lequel on demande à rejoindre le SMP. |

### La contrainte qui commande tout

Le proxy déclare une **liste** de serveurs de repli. Quand un joueur est expulsé d'un
backend, le proxy essaie ces serveurs dans l'ordre. **Si aucun ne répond, le joueur est
déconnecté.**

> **La disponibilité du lobby *est* la disponibilité du réseau.**

Tout ce qui suit en découle :

| Conséquence | Détail |
|---|---|
| **Le moins de dépendances possible** | Aucune base de données pour sa fonction principale. Redis est facultatif. |
| **Un démarrage rapide** | Après un incident, on veut le lobby debout tout de suite |
| **Aucune simulation** | Pas de mobs, pas de génération, pas de physique. S'il consomme du CPU, c'est un défaut. |
| **Plusieurs instances** | Le proxy accepte une liste de replis ; il faut en déclarer plus d'un |
| **Il doit tenir même si tout le reste est tombé** | Un lobby qui ne marche que lorsque le SMP marche ne sert à rien, puisque c'est précisément quand le SMP tombe qu'on en a besoin |

La dernière ligne est la plus importante, et c'est celle qu'on oublie : **le lobby est le
plus utile au pire moment.**

### Trois engagements

1. **Il ne perd jamais un joueur.** Un joueur qui arrive parce que quelque chose a mal
   tourné doit comprendre quoi, et repartir sans avoir à deviner.
2. **Il ne ment pas sur l'état du réseau.** Un serveur injoignable est affiché
   injoignable. Un bouton éteint avec une explication vaut mieux qu'un bouton absent.
3. **Il ne retient personne.** Un clic suffit pour demander à rejoindre, et rien n'oblige
   à rester devant l'écran.

---

## 2. Ce que le lobby n'est pas

Ce paragraphe existe parce qu'une première version de ce cahier des charges s'était
trompée sur ce point, et que l'erreur est naturelle.

| Ce n'est pas… | Pourquoi |
|---|---|
| **Un lieu de la fiction** | Les Terres Fracturées, le Sanctuaire des Origines, les Ères : rien de tout cela n'est au lobby. Le lobby est hors du monde raconté. |
| **Une antichambre narrative** | Le joueur ne traverse pas un lieu de l'univers pour entrer dans le SMP. Il clique sur un bouton. |
| **L'endroit de la cinématique** | L'Invocation d'Aelindra se joue **sur le SMP**, à la première connexion, et uniquement là. Voir [`cdc_intro.md`](cdc_intro.md). |
| **Une vitrine de la progression SMP** | Pas de carte du sorcier, pas de Panthéon, pas d'état d'Ère interprété. Ce sont des données du SMP, et les y chercher créerait une dépendance au plus mauvais endroit. |
| **Un serveur de jeu** | Aucune progression, aucune monnaie, aucun objet ne s'y gagne |
| **Un hébergeur de minijeux** | Voir §12 |

### La règle qui en découle

> **Le lobby affiche l'état du *réseau*, jamais l'état d'un *joueur*.**

Un nom de serveur, un nombre de joueurs, un état de disponibilité, une position en file.
Rien qui demande de lire la progression de qui que ce soit.

### Ce qui reste cohérent avec le projet

L'**interface**, elle, doit ressembler à WizardMC : la même palette, les mêmes primitives
graphiques, le même soin que les autres écrans du client. La cohérence est **visuelle**,
pas narrative. Voir §6.

---

## 3. Règles métier

| ID | Règle |
| :--- | :--- |
| **H-01** | WizardHub est un greffon **WizardSpigot**. Il ne tient aucune file et ne décide d'aucune admission. |
| **H-02** | Le lobby **fonctionne sans aucune dépendance externe**. Redis absent, SMP absent, base absente : il démarre, il accueille, et il le dit. |
| **H-03** | L'état des destinations vient **du proxy**, par message de greffon. Le proxy sait ce qui est joignable et combien de joueurs y sont : c'est la source la plus fiable et la seule toujours disponible. |
| **H-04** | Le SMP **enrichit** cet état par un battement de cœur (maximum, ligne d'état libre). Enrichissement facultatif : son absence dégrade l'affichage, elle ne le casse pas. |
| **H-05** | Le lobby **n'interprète aucun concept du SMP**. La ligne d'état est une chaîne qu'il affiche sans la comprendre. |
| **H-06** | Une demande de passage part en **message de greffon** sur la connexion du joueur. Il ne peut pas mettre un autre joueur en file. |
| **H-07** | L'interface est portée par le **paquet 133**, seul paquet du greffon. La file ne parle jamais au client. |
| **H-08** | Un client sans le paquet 133 reste pleinement utilisable : mêmes actions en commandes et en interface de coffre. |
| **H-09** | L'interface réutilise la palette et les primitives du client existant. **Aucun second langage visuel.** |
| **H-10** | Le monde du lobby est **durci** : aucune casse, aucune pose, aucun PvP, aucun dégât, aucune faim, aucun lâcher ni ramassage d'objet, aucune interaction, heure et météo fixes, aucun mob. |
| **H-11** | Une chute dans le vide **téléporte au point d'apparition**. Elle ne tue pas. |
| **H-12** | Le lobby **annonce la raison de l'arrivée** quand il y en a une, et décide d'une remise en file selon le §5. |
| **H-13** | La remise en file automatique n'a lieu **que** sur une interruption involontaire. Jamais sur une expulsion délibérée. |
| **H-14** | Aucune progression, aucune monnaie, aucun objet ne se gagne au lobby. |
| **H-15** | Le chat du lobby est **séparé** de celui du SMP. |
| **H-16** | Les messages de connexion et de déconnexion sont **désactivés par défaut**. |
| **H-17** | Le lobby ne tient **aucun état de joueur en mémoire**. Plusieurs instances sont interchangeables. |
| **H-18** | L'inactivité est détectée par le lobby et **signalée à la file**, qui décide. Le lobby ne retire personne de la file. |
| **H-19** | Si **toutes** les destinations sont injoignables, l'interface le dit franchement et n'offre rien. C'est un état conçu, pas un accident. |
| **H-20** | Le lobby n'expulse jamais un joueur vers une déconnexion. S'il n'a rien à lui proposer, il le garde. |

---

## 4. La disponibilité, en détail

### 4.1. Pourquoi le lobby ne doit dépendre de rien

Reprenons le scénario qui justifie le plugin : **le SMP tombe avec trois cents joueurs
dessus.**

| Si le lobby dépend de… | Ce qui se passe |
|---|---|
| Rien | Les 300 joueurs atterrissent, voient ce qui s'est passé, et sont remis en file |
| D'une base de données | Si elle est tombée avec le SMP — même hôte, même panne — le lobby ne démarre pas, et les 300 joueurs sont **déconnectés** |
| Du SMP lui-même | Absurde par construction |
| De Redis | Si Redis est tombé, la file ne marche pas non plus. Le lobby doit quand même accueillir. |

D'où **H-02**. Le lobby démarre avec sa seule configuration, un monde, et rien d'autre.

### 4.2. Ce qui est dégradé quand une dépendance manque

| Absent | Ce qu'on perd | Ce qui marche encore |
|---|---|---|
| **Redis** | La position en file, l'attente estimée | Le lobby, l'interface, la demande de passage par message de greffon |
| **Le battement de cœur du SMP** | Le maximum, la ligne d'état | Le nom, la joignabilité et le compte, qui viennent du proxy |
| **Le SMP** | La destination est injoignable | Tout le reste ; l'interface le dit |
| **Le paquet 133 côté client** | L'interface graphique | Les commandes et l'interface de coffre |

Chaque ligne est une dégradation **nommée**, pas une panne. C'est la même règle que
partout dans le projet : une dépendance manquante coûte une capacité, pas le démarrage.

### 4.3. Plusieurs lobbies

Le proxy accepte une **liste** de serveurs de repli, essayés dans l'ordre. Il faut en
déclarer au moins deux : un lobby unique est un point de défaillance unique, et sa chute
déconnecte tout le monde.

**H-17** le rend possible : aucun état de joueur en mémoire, donc deux instances sont
interchangeables et un joueur ne perd rien à passer de l'une à l'autre.

---

## 5. L'arrivée, et ce que le lobby en fait

C'est le cœur du rôle de repli, et la partie la plus facile à rater.

### 5.1. Ce que le proxy sait dire

Le proxy distingue la **cause** d'une expulsion et l'**état** de la connexion au moment
où elle survient :

| Cause | Signification |
|---|---|
| `SERVER` | Le backend a délibérément expulsé le joueur |
| `LOST_CONNECTION` | Le lien avec le backend est tombé |
| `EXCEPTION` | Une erreur a rompu la connexion |
| `UNKNOWN` | — |

| État | Signification |
|---|---|
| `CONNECTING` | Le joueur n'est jamais arrivé sur le backend |
| `CONNECTED` | Il y était |

### 5.2. La matrice de décision

| Raison d'arrivée | Cause et état | Remise en file | Ce qu'on affiche |
|---|---|---|---|
| Connexion au réseau | — | non | L'interface, destination prête |
| Retour volontaire au lobby | commande du joueur | non | L'interface, destination prête |
| Le SMP était complet | `SERVER` + `CONNECTING` | **oui** | « Le serveur est complet — vous êtes en file » |
| Le SMP est tombé | `LOST_CONNECTION` ou `EXCEPTION` | **oui, avec reprise prioritaire** | « Le serveur a été interrompu — remise en file prioritaire » |
| Échec de connexion | `*` + `CONNECTING` | **oui** | « Connexion impossible — nouvelle tentative en file » |
| Expulsion par un membre du staff | `SERVER` + `CONNECTED` | **non** | La raison, en clair |
| Bannissement | `SERVER` + `CONNECTED` | **non** | La raison |
| Maintenance annoncée | `SERVER` + `CONNECTED` | non | La ligne d'état du battement de cœur |

### 5.3. La règle qui évite d'annuler une sanction

> **`SERVER` + `CONNECTED` = expulsion délibérée = jamais de remise en file.**

C'est **H-13**, et c'est une règle de sûreté. Un joueur expulsé pour une infraction ne
doit pas être remis en file par le lobby : ce serait le lobby qui défait le travail de la
modération.

Le choix de ne **pas** lire le texte de l'expulsion est volontaire. Reconnaître « vous
avez été banni » dans une chaîne de caractères est fragile : une reformulation et la règle
tombe. La cause et l'état suffisent, et ils ne changent pas.

### 5.4. La reprise prioritaire après une interruption

Quand un SMP tombe, ses joueurs atterrissent au lobby en quelques secondes. Sans rien de
plus, ils cliquent tous « rejoindre » en même temps, et l'ordre récompense **celui qui
regardait son écran**.

D'où deux mesures :

| Mesure | Effet |
|---|---|
| **Remise en file automatique** | Dans l'ordre où le proxy les a déposés, sans clic |
| **Fenêtre de reprise prioritaire** | Pendant quelques minutes, ils passent avant les nouveaux arrivants |

### Pourquoi la reprise prioritaire est juste

Un joueur qui attendait déjà dans la file avant l'incident va attendre un peu plus. C'est
défendable, et il faut pouvoir l'expliquer :

> Les joueurs interrompus **occupaient les places** que celui qui attendait convoitait. Les
> leur rendre d'abord ne lui coûte rien qu'il n'ait déjà payé : sans l'incident, ils y
> seraient toujours.

La fenêtre est **bornée en temps** et le nombre de reprises est **borné par construction**
— au plus le nombre de joueurs qui étaient connectés. Elle ne met donc pas en danger la
part réservée aux joueurs sans priorité de
[`cdc_wizardqueue.md`](cdc_wizardqueue.md) §4.

### 5.5. Ce que le joueur voit

Un bandeau, en haut de l'interface, qui disparaît au premier clic. Pas une fenêtre à
fermer : un joueur dont le serveur vient de tomber n'a pas envie de cliquer sur « OK ».

| Ton | Exemple |
|---|---|
| Factuel | « Le serveur a été interrompu. Vous êtes 43<sup>e</sup> en file, avec priorité de reprise. » |
| Jamais rassurant à vide | Pas de « ne vous inquiétez pas » |
| Jamais technique | Pas de trace d'erreur, pas de nom de classe |

---

## 6. L'interface de navigation

### 6.1. Le langage visuel existe déjà

Le client porte une boîte à outils graphique et une palette. **L'interface les réutilise
telles quelles** (**H-09**).

| Couleur | Valeur | Usage |
|---|---|---|
| Or parchemin | `0xFFC5A87A` | Titres, bordures, accents |
| Or atténué | `0xB3C5A87A` | Texte secondaire |
| Bleu nuit | `0xF00F121D` | Fond des panneaux |
| Barre | `0xCC1A1D28` | En-têtes et pieds |
| Violet arcane | `0xCC1C1240` | Cœur des lueurs |
| Violet médian | `0x661C1240` | Halo |
| Violet bord | `0x221C1240` | Extrémité du halo |

Primitives à employer : remplissage, lignes, bordures, **équerres d'angle**, lueur en
couches, adoucissements d'animation.

> Les **équerres d'angle** plutôt qu'un cadre plein sont la signature du projet.

### 6.2. La maquette

Une liste de destinations. Conçue comme une liste **même avec une seule entrée**, pour
qu'en ajouter une seconde ne coûte rien.

```text
┌────────────────────────────────────────────────────────────────┐
│  WIZARDMC                                      ⬤ 312 en ligne  │
├────────────────────────────────────────────────────────────────┤
│  ⚠ Le serveur a été interrompu — remise en file prioritaire    │  ← bandeau, §5.5
├────────────────────────────────────────────────────────────────┤
│                                                                │
│   ┌──────────────────────────────────────────────────────┐     │
│   │  ⬤  SMP                                   287 / 300  │     │
│   │     Les Terres Fracturées                            │     │
│   │     Ère III · Chaos · jour 31          ← ligne d'état │     │
│   │                                                      │     │
│   │                      ┌──────────────┐                │     │
│   │                      │  REJOINDRE   │                │     │
│   │                      └──────────────┘                │     │
│   └──────────────────────────────────────────────────────┘     │
│                                                                │
│   ( les autres destinations, quand il y en aura )              │
│                                                                │
├────────────────────────────────────────────────────────────────┤
│  Boutique · Wiki · Discord            ☐ masquer les joueurs    │
└────────────────────────────────────────────────────────────────┘
```

La **ligne d'état** — ici « Ère III · Chaos · jour 31 » — est une chaîne que le SMP
publie et que le lobby affiche **sans la comprendre** (**H-05**). C'est ce qui lui permet
d'être utile sans rien connaître des Ères.

### 6.3. Les sept états d'une destination

| État | Ce qu'on voit | Le bouton |
|---|---|---|
| **En ligne** | Compte, ligne d'état | « Rejoindre », actif |
| **Complet** | Compte au maximum | « Rejoindre », actif — met en file |
| **En file** | Position exacte, attente estimée, barre | « Quitter la file » |
| **Appelé** | Compte à rebours court, son et titre | Rien : le passage se fait |
| **Maintenance** | La ligne d'état du SMP | Éteint, avec l'explication |
| **Hors ligne** | « Ne répond pas » | Éteint, avec l'explication |
| **Passage ouvert** | Pour un porteur de contournement | « Rejoindre », immédiat |

### Les trois états qu'on rate toujours

**Hors ligne.** Un bouton qui **disparaît** fait croire à un bug du client. Un bouton
éteint avec une phrase qui dit pourquoi est la seule présentation acceptable (engagement 2).

**Appelé.** Le compte à rebours correspond à l'échéance réelle de l'admission côté file,
pas à une animation. Un joueur qui voit « 18, 17, 16… » comprend qu'il doit être là.

**Tout est hors ligne.** **H-19** : l'interface le dit franchement, en une phrase, et
n'offre rien. C'est le pire moment du réseau, et c'est celui où une interface confuse fait
le plus de dégâts.

### 6.4. Les mouvements

| Moment | Mouvement |
|---|---|
| Ouverture | Les cartes montent et se révèlent en décalé, adoucissement quartique |
| Changement de position en file | Le nombre glisse d'un cran. **Jamais de clignotement.** |
| Appel | La lueur violette monte en couches, le titre s'inscrit |
| Changement d'état d'une destination | Transition de couleur, pas de saut |

Le décalage à l'ouverture est ce qui distingue une interface soignée d'une interface qui
apparaît d'un bloc.

### 6.5. Ce que l'interface ne fait jamais

| Jamais | Pourquoi |
|---|---|
| Bloquer sa fermeture | On doit pouvoir être au lobby sans elle |
| S'ouvrir plus d'une fois par arrivée | À la connexion, et c'est tout |
| Afficher une position estimée | La position est exacte, ou absente |
| Afficher une attente qui remonte | Un joueur qui voit son attente augmenter perd confiance |
| Demander le passage automatiquement | Sauf remise en file du §5, qui est annoncée |
| Montrer la liste des joueurs en file | Un client modifié en tirerait la composition de la file |

---

## 7. Le monde du lobby

### 7.1. Ce qu'il doit être

| Exigence | Pourquoi |
|---|---|
| **Petit** | Il se charge vite, et il se recharge vite après un incident |
| **Fini** | Aucune génération. Les bords sont des murs ou du vide protégé. |
| **Sans entité** | Aucun mob, aucun objet au sol, aucun cadre |
| **Fixe** | Heure et météo constantes |
| **Beau** | C'est la première chose qu'un joueur voit, et la dernière avant de partir |

### 7.2. Le durcissement

**H-10**, en détail. Chaque ligne a déjà été un incident sur un lobby quelque part.

| Interdit | Conséquence si on l'oublie |
|---|---|
| Casser, poser | Un lobby devient une ruine en une soirée |
| PvP, dégâts de toute nature | Un joueur tué au lobby, c'est un ticket |
| Lâcher, ramasser un objet | Le sol se couvre d'objets, le serveur rame |
| Faim | Un joueur affamé au lobby ne peut rien y faire |
| Feu, explosions, propagation | — |
| Interaction avec les blocs | Portes, leviers, coffres : tout inerte |
| Mobs, y compris paisibles | Le lobby n'est pas un monde vivant |
| Commandes du SMP | Elles n'ont aucun sens ici |
| Mort dans le vide | **H-11** : téléportation au point d'apparition |

### 7.3. Le point d'apparition et le retour

| Règle | Détail |
|---|---|
| Point d'apparition fixe | Déclaré en configuration |
| Réapparition au même endroit | Pas de lit, pas de point de retour |
| Sortie du monde | Téléportation au point d'apparition |
| Arrivée | Toujours au point d'apparition, quelle que soit la raison |

---

## 8. Le système de lobby, au complet

Ce que le greffon gère en plus de l'interface.

### 8.1. La visibilité des joueurs

Masquer les autres joueurs, en une case à cocher et en commande.

Ce n'est pas un confort : c'est une **mesure de performance**. Un lobby à trois cents
joueurs visibles fait chuter la fréquence d'images de tout le monde, et c'est exactement
la situation qui suit une interruption du SMP — le pire moment.

| Mode | Effet |
|---|---|
| Tous | Par défaut |
| Aucun | Personne n'est affiché |
| Staff | Seuls les membres du staff sont affichés |

Le réglage est **retenu** pour la session, pas persisté : le lobby ne tient pas d'état
(**H-17**).

### 8.2. Le chat

| Règle | Détail |
|---|---|
| Séparé du SMP (**H-15**) | Un message du lobby ne part pas dans les Terres |
| Anti-répétition | Un même message refusé deux fois de suite |
| Anti-inondation | Un délai minimal entre deux messages |
| Filtre de publicité | Adresses de serveurs et liens d'invitation refusés |
| Journalisé | Pour la modération |

Le filtre de publicité n'est pas une option : un lobby est l'endroit où l'on vient
recruter pour ailleurs.

### 8.3. Les messages de connexion

**Désactivés par défaut** (**H-16**). Un lobby qui voit passer trois cents arrivées après
une interruption produit un mur de texte qui noie le bandeau d'explication — c'est-à-dire
exactement le message qui compte.

Une connexion silencieuse est prévue pour le staff.

### 8.4. Les annonces

Une rotation de messages, configurable. Contenu typique : liens, rappel du règlement,
information sur une maintenance à venir.

| Règle | Pourquoi |
|---|---|
| Jamais pendant les dix premières secondes d'une arrivée | Le bandeau d'explication passe avant |
| Pas plus d'une toutes les deux minutes | Au-delà, on ne les lit plus |
| Jamais de promesse de fonctionnalité | Seule l'équipe s'engage |

### 8.5. L'anti-inactivité

Un joueur inactif au lobby occupe une place en file, puis un slot d'admission qu'il ne
consommera jamais.

| Étape | Délai par défaut |
|---|---|
| Avertissement | après 9 minutes |
| Signalement à la file | après 10 minutes |

Le lobby **détecte** et **signale** ; la file décide (**H-18**). Un lobby qui retirerait
lui-même un joueur de la file écrirait dans un état qui ne lui appartient pas.

> L'avertissement une minute avant n'est pas une politesse : sans lui, un joueur qui
> revient après dix minutes a perdu son attente sans comprendre pourquoi.

### 8.6. Le tableau d'affichage

Léger. Nom du réseau, joueurs en ligne, et la position en file quand il y en a une.

Ce qu'il ne contient **pas** : aucune donnée de progression, aucun classement, aucun
compteur personnel. Voir la règle du §2.

### 8.7. Les raccourcis

`/boutique`, `/wiki`, `/discord` : des relais vers ce qui existe déjà. Le lobby ne porte
ni boutique ni wiki, il ouvre ce que le client ou le SMP fournissent.

### 8.8. Les déplacements de lobby

Deux agréments, facultatifs, sans aucune dépendance :

| Agrément | Ce que c'est |
|---|---|
| **Double saut** | Un second saut en l'air, sans dégât de chute |
| **Plaques de propulsion** | Des zones déclarées qui projettent le joueur |

Purement cosmétiques, ils ne donnent accès à rien et ne peuvent pas sortir du monde. Ils
existent parce qu'un lobby où l'on attend gagne à ne pas être inerte, et parce qu'ils ne
coûtent rien — ni dépendance, ni état, ni calcul.

### 8.9. Le mode maintenance

Quand une destination est en maintenance, l'interface le dit et le bouton est éteint. La
valeur vient de la ligne d'état du battement de cœur : **le lobby ne décide pas d'une
maintenance**, il en rend compte.

---

## 9. Le protocole

### 9.1. Paquet 133 — l'interface de navigation

Le seul paquet du greffon (**H-07**), dans les deux sens.

| Sens | Message | Contenu |
|---|---|---|
| S → C | `OPEN` | Ouvre l'interface |
| S → C | `DESTINATIONS` | La liste : identifiant, nom, état, compte, maximum, ligne d'état |
| S → C | `QUEUE` | Position exacte, attente estimée, niveau |
| S → C | `CALLED` | L'admission : échéance, et l'ordre de jouer l'annonce |
| S → C | `ARRIVAL` | La raison de l'arrivée, pour le bandeau du §5.5 |
| C → S | `JOIN` | Demande de passage vers une destination |
| C → S | `LEAVE` | Quitter la file |
| C → S | `VISIBILITY` | Changement de visibilité des joueurs |
| C → S | `CLOSE` | L'interface a été fermée |

L'identifiant **133** est réservé — voir
[Plages d'identifiants](../07-reference/plages-d-identifiants.md). Le prochain libre est
**134**.

### Ce qui n'est jamais envoyé au client

| Jamais | Pourquoi |
|---|---|
| La liste des joueurs en file | Un client modifié en tirerait la composition de la file |
| La position d'un autre joueur | — |
| Les slots réservés restants | Un client saurait quand le contournement est à saturation |
| L'identité des porteurs de priorité | — |

### 9.2. Avec le proxy

| Sens | Canal | Contenu |
|---|---|---|
| Proxy → lobby | message de greffon | L'état des destinations : joignabilité, compte |
| Proxy → lobby | message de greffon | La raison de l'arrivée d'un joueur |
| Lobby → proxy | message de greffon | Demande de passage, retrait de file, signalement d'inactivité |

**C'est le proxy qui est la source de l'état des destinations** (**H-03**), et non le SMP
ni Redis. Il sait ce qui est joignable parce qu'il y route, et il est disponible par
construction — un lobby qui lui parle ne peut pas être coupé de sa source.

Les actions du joueur passent par **sa propre connexion** : l'authentification est
gratuite, et il ne peut pas mettre un autre joueur en file (**H-06**).

### 9.3. Avec le SMP

Aucun échange direct. Le SMP publie un battement de cœur — maximum, ligne d'état — que le
lobby lit pour enrichir l'affichage (**H-04**). Son absence dégrade, elle ne casse pas.

---

## 10. Le repli sans client

**H-08** : un client qui ne connaît pas le paquet 133 doit rester pleinement utilisable.

| Action | Repli |
|---|---|
| Ouvrir la navigation | `/hub`, `/lobby`, ou une interface de coffre |
| Voir les destinations | `/servers` |
| Rejoindre | `/join <destination>` |
| Voir sa position | `/queue`, porté par la file |
| Quitter la file | `/queue leave` |
| Masquer les joueurs | `/visibility` |

### Pourquoi ce repli existe alors que le client est obligatoire

Le client **est** obligatoire pour jouer sur le SMP, et la poignée de main l'impose. Mais
le lobby est le premier contact : un joueur dont le client vient d'échouer à se mettre à
jour doit pouvoir arriver, comprendre, et lire le message qui le lui explique.

> **Un joueur expulsé sans explication est un joueur perdu.** Le repli n'est pas une
> concession à la compatibilité, c'est le canal de diagnostic.

---

## 11. Configuration

| Clef | Ce qu'elle règle | Défaut |
|---|---|---|
| `world` | Le monde du lobby | — |
| `spawn` | Le point d'apparition | — |
| `time`, `weather` | Heure et météo fixes | — |
| `destinations.<id>.display` | Le nom affiché | — |
| `destinations.<id>.server` | Le serveur proxy visé | — |
| `destinations.<id>.description` | Une ligne de présentation | — |
| `interface.openOnJoin` | Ouvrir l'interface à l'arrivée | vrai |
| `interface.refreshMs` | Période de rafraîchissement | 2000 |
| `arrival.bannerSeconds` | Durée du bandeau d'explication | 15 |
| `arrival.requeueOnInterruption` | Remise en file après interruption | vrai |
| `arrival.requeueWindowSeconds` | Fenêtre de reprise prioritaire | 300 |
| `idle.warningSeconds` | Avertissement d'inactivité | 540 |
| `idle.reportSeconds` | Signalement à la file | 600 |
| `visibility.default` | Visibilité par défaut | tous |
| `chat.minIntervalMs` | Délai minimal entre deux messages | 1500 |
| `announcements.intervalSeconds` | Période de rotation | 120 |
| `movement.doubleJump` | Double saut | vrai |
| `redis.*` | Facultatif, pour la position en file | — |

### Les trois clefs à ne pas toucher sans réfléchir

**`arrival.requeueOnInterruption`.** La mettre à faux fait que trois cents joueurs
interrompus cliquent tous en même temps, et que l'ordre récompense celui qui regardait son
écran. Voir §5.4.

**`arrival.requeueWindowSeconds`.** Trop courte, la reprise ne sert à rien — le SMP n'a
pas fini de redémarrer. Trop longue, elle pèse sur la part réservée de la file. Cinq
minutes couvre un redémarrage ordinaire.

**`interface.refreshMs`.** Doit rester aligné sur le rythme de la file. La descendre
multiplie les paquets sans rien améliorer : la position ne change pas plus souvent que les
admissions.

---

## 12. Hors périmètre

| Hors périmètre | Pourquoi |
|---|---|
| **Des minijeux** | Ils entrent en concurrence avec le SMP pour l'attention, demandent leur propre équilibrage et leur propre modération. **Et surtout : ils ajoutent du code au serveur dont la disponibilité est celle du réseau.** |
| **Un parkour chronométré** | Idem, et un classement est un état à persister |
| **Le vestiaire de cosmétiques** | Il demande de lire les cosmétiques possédés, donc une dépendance à la base du SMP. Possible en phase 2, **avec cette dépendance assumée et dégradable**. |
| **La carte du sorcier** | Donnée du SMP. Voir §2. |
| **Le Panthéon, l'état d'Ère interprété** | Idem |
| **Une progression propre au lobby** | **H-14**. Un lobby qui récompense l'attente récompense de ne pas jouer. |
| **Un chat commun avec le SMP** | **H-15** |
| **Des échanges entre joueurs** | Le lobby n'a pas d'économie |
| **La cinématique d'arrivée** | Elle appartient au SMP et se joue une seule fois, là-bas |

### La tentation à nommer

Mettre un minijeu dans un lobby est l'idée la plus fréquente. Dans ce réseau-ci elle a un
coût particulier : **le lobby est le serveur de repli.** Chaque ligne de code qu'on y
ajoute est une ligne qui peut le faire tomber, et sa chute déconnecte tout le monde.

La bonne réponse à une file longue est un slot de plus. La bonne réponse à un joueur qui
s'ennuie est qu'il puisse **partir sans perdre sa place** — ce que la grâce de reconnexion
de la file garantit.

---

## 13. Performance

Le lobby n'est pas un composant chaud, mais il peut le devenir par inadvertance.

| Risque | Mesure |
|---|---|
| Trois cents joueurs visibles après une interruption | La visibilité réglable du §8.1 |
| Un paquet par joueur par cycle | N'envoyer `DESTINATIONS` et `QUEUE` que **lorsque l'état a changé** |
| Une lecture par joueur par cycle | **Une** lecture par destination et par cycle, index calculés en local |
| Des entités au sol | Aucune n'est permise |
| Un monde qui se génère | Aucun |

### L'ordre de grandeur visé

| Grandeur | Cible |
|---|---|
| Joueurs au lobby simultanés | 2 000 |
| Paquets par seconde en régime stable | quelques dizaines |
| Charge CPU | négligeable : rien ne simule |
| Temps de démarrage | quelques secondes |

Le temps de démarrage est une exigence, pas une statistique : après un incident, le lobby
doit être debout avant que les joueurs ne reviennent.

---

## 14. Modes de panne

| Panne | Comportement attendu |
|---|---|
| **Redis injoignable** | Le lobby accueille, l'interface fonctionne, la demande de passage part par message de greffon. La position en file n'est pas affichée. |
| **Battement de cœur absent** | Le nom, la joignabilité et le compte viennent du proxy. Le maximum et la ligne d'état manquent. |
| **Toutes les destinations hors ligne** | **H-19** : l'interface le dit, n'offre rien, et le joueur reste (**H-20**) |
| **Client sans le paquet 133** | Le repli du §10 |
| **Monde du lobby absent** | Le greffon refuse de démarrer et le dit. Générer un monde sans qu'on l'ait demandé est plus dangereux. |
| **Plusieurs lobbies** | Aucun état en mémoire : interchangeables (**H-17**) |
| **Le lobby lui-même tombe** | Le proxy passe au lobby suivant de sa liste. **Si la liste est épuisée, les joueurs sont déconnectés** — c'est la raison pour laquelle il en faut plus d'un. |

### Le mode de panne le plus grave

**Un lobby unique qui tombe.** Le proxy n'a plus de repli, et chaque joueur expulsé d'un
backend est déconnecté au lieu d'être rattrapé.

Ce n'est pas un défaut du greffon : c'est un défaut de déploiement. Le garde-fou est dans
la configuration du proxy — **au moins deux serveurs dans la liste de repli** — et il doit
être vérifié à chaque mise en service.

---

## 15. Commandes et permissions

| Commande | Ce qu'elle fait | Permission |
|---|---|---|
| `/hub`, `/lobby` | Ouvre l'interface de navigation | — |
| `/servers` | La liste des destinations, en texte | — |
| `/join <destination>` | Demande le passage | — |
| `/visibility` | Masquer ou afficher les joueurs | — |
| `/hub reload` | Recharge la configuration | `wizardmc.hub.admin` |
| `/hub announce <texte>` | Ajoute une annonce | `wizardmc.hub.admin` |
| `/hub setspawn` | Fixe le point d'apparition | `wizardmc.hub.admin` |

| Nœud | Ce qu'il donne |
|---|---|
| `wizardmc.hub.admin` | Recharger, annoncer, fixer le point d'apparition |
| `wizardmc.hub.build` | Construire au lobby : désactive le durcissement pour son porteur |
| `wizardmc.hub.silent` | Arriver sans annonce |

`wizardmc.hub.build` est un nœud d'aménagement, pas de jeu. À ne donner que le temps d'un
chantier, et à retirer après — un lobby où un bâtisseur a gardé ses droits est un lobby qui
finira modifié par accident.

`/queue` et `/queue leave` appartiennent à la file, pas au lobby.

---

## 16. QA

### Le nominal

- [ ] La connexion au réseau pose le joueur au lobby, l'interface s'ouvre une fois
- [ ] La carte de destination montre nom, compte, état et ligne d'état
- [ ] « Rejoindre » met en file ; la position est **exacte** et identique à `/queue`
- [ ] L'admission joue un son et un titre, et le passage aboutit
- [ ] À la première connexion, la cinématique se joue **sur le SMP**, après le passage

### Le rôle de repli

- [ ] Un SMP arrêté brutalement dépose ses joueurs au lobby, **aucun n'est déconnecté**
- [ ] Le bandeau dit que le serveur a été interrompu
- [ ] Ils sont **remis en file automatiquement**, dans l'ordre du dépôt
- [ ] Ils passent devant les nouveaux arrivants pendant la fenêtre de reprise
- [ ] Un SMP complet renvoie au lobby avec mise en file
- [ ] **Une expulsion par le staff n'est jamais suivie d'une remise en file**
- [ ] La raison de l'expulsion est affichée en clair
- [ ] Un bannissement n'est jamais suivi d'une remise en file

### L'indépendance

- [ ] Redis éteint : le lobby démarre, accueille, et la demande de passage fonctionne
- [ ] Battement de cœur absent : compte et joignabilité restent affichés
- [ ] SMP éteint : la destination est « hors ligne », bouton **éteint avec explication**
- [ ] Toutes les destinations éteintes : l'interface le dit et le joueur **reste connecté**
- [ ] Le lobby démarre en quelques secondes

### L'interface

- [ ] Les cartes montent en décalé à l'ouverture
- [ ] La position glisse d'un cran, sans clignoter
- [ ] L'attente estimée **ne remonte jamais**
- [ ] La mise en page tient de 1024×768 à 4K
- [ ] La fermeture ne bloque rien
- [ ] Aucun paquet ne contient la liste des joueurs en file

### Le durcissement

- [ ] Impossible de casser, poser, interagir
- [ ] Impossible de se faire du mal ou d'en faire
- [ ] Impossible de lâcher ou ramasser un objet
- [ ] Pas de faim, pas de dégâts de chute
- [ ] La chute dans le vide **téléporte**, ne tue pas
- [ ] Heure et météo fixes, aucun mob
- [ ] Les commandes du SMP sont refusées

### Le système de lobby

- [ ] Masquer les joueurs les fait disparaître, et la fréquence d'images remonte
- [ ] Un message du lobby ne part pas sur le SMP
- [ ] Une adresse de serveur dans le chat est refusée
- [ ] Les messages de connexion sont absents par défaut
- [ ] Le double saut fonctionne et ne permet pas de sortir du monde
- [ ] Un avertissement d'inactivité tombe une minute avant le signalement

### Le déploiement

- [ ] **Deux lobbies déclarés** dans la liste de repli du proxy
- [ ] L'arrêt du premier bascule les joueurs sur le second
- [ ] Les deux affichent la même chose

---

## À lire ensuite

- [`cdc_wizardqueue.md`](cdc_wizardqueue.md) — la file, et la décision de la mettre sur le proxy
- [`cdc_exp_client.md`](cdc_exp_client.md) — le client, le HUD, les effets
- [`cdc_intro.md`](cdc_intro.md) — la cinématique, qui appartient au SMP
- [Plages d'identifiants](../07-reference/plages-d-identifiants.md) — le paquet 133
