# Panthéon et figures

Les personnages de WizardMC, les neuf traditions, et les puissances dont on ne
dit rien. Référence pour écrire un dialogue, nommer un personnage, ou décider ce
qu'une figure peut et ne peut pas faire.

**Statut : fiction établie** pour Aelindra et le Forgeron ; **à étoffer** pour le
reste.

---

## 1. Aelindra, l'Éveilleuse des Âmes

| | |
|---|---|
| **Rôle** | Gardienne du Sanctuaire des Origines. Invoque les sorciers et leur fait choisir leur voie. |
| **Apparaît** | Une fois par joueur, à sa première connexion. Jamais ensuite. |
| **Statut** | Vue par le client seul — ce n'est pas une entité du serveur. Chaque joueur la voit seul. |
| **Modèle** | `aelindra/aelindra_eveilleuse_des_ames` |

### Ce qu'elle dit

Cinq répliques, toujours les mêmes, toujours dans cet ordre. Le texte complet et
les durées sont dans [cdc_intro](../90-specifications/cdc_intro.md).

| Moment | Ce qu'elle établit |
|---|---|
| Apparition | Le joueur a été *choisi*, pas recruté. « Les Arcanes t'ont choisi, Sorcier. » |
| Présentation | Son nom, son titre, son lieu. |
| Invocation | Où le joueur est tombé, et que la magie y saigne. C'est la phrase qui porte tout le lore. |
| Rôle | Le joueur façonne son destin, « et peut-être celui de ces terres ». *Peut-être* : rien ne lui est promis. |
| École | La seule question qu'elle pose. |

### Ce qu'elle ne fait jamais

- Elle ne revient pas. Aucune mécanique, aucun objet, aucune quête ne doit la rappeler.
- Elle ne protège personne et ne punit personne.
- Elle ne donne rien — ni objet, ni pouvoir, ni information sur les autres joueurs.
- Elle ne dit pas ce qu'elle est.

La raison est dans [le lore](lore.md#6-aelindra-et-le-sanctuaire) : un monde où une
figure bienveillante et puissante peut être rappelée n'a plus d'enjeu.

### Ses animations

Son modèle porte onze clips, dont plusieurs servent uniquement à la cinématique.
Ils sont nommés en français, ce qui est une exception dans le projet — son modèle
est antérieur aux conventions.

| Clip | Usage |
|---|---|
| `Apparition` | Sa venue. Jouée une seule fois. |
| `Disparition` | Son départ. |
| `Dialogue`, `Ecoute` | Pendant les répliques |
| `Idle_Flottement`, `Attendre_Observation` | Repos |
| `Benediction`, `Resurrection` | Non utilisés par la cinématique |
| `Couronne_Rotation`, `Grimoire_Flottement`, `Pendentif_Oscillation` | Animations d'accessoires, jouées en continu |

---

## 2. Le Forgeron Itinérant

| | |
|---|---|
| **Rôle** | Achète le Mana Brut. Le seul débouché fiable des convois. |
| **Apparaît** | En permanence, à l'un des points déclarés. Se déplace régulièrement. |
| **Statut** | Entité serveur, visible de tous. |
| **Donne aussi** | Les quêtes de la trame et la plupart des annexes. |

C'est le seul personnage que les joueurs revoient, ce qui lui donne deux fonctions
qu'il faut garder distinctes.

**Fonction économique.** Il convertit le Mana Brut en valeur. Comme il bouge, la
route vers lui change, et comme tout le monde doit y aller, les routes se croisent.
C'est de là que vient tout le risque des convois — pas d'une règle de PvP, mais
d'une contrainte géographique.

**Fonction narrative.** Il est le donneur de la trame. Un joueur qui débarque n'a
personne à qui parler ; le Forgeron est cette personne. Son registre est bourru et
transactionnel : il ne fait pas de discours, il a du travail.

> « Les gelées s'installent partout. Personne ne les regrette. »
> — le Forgeron, quête annexe *Vermine des caves*

### Ce qu'on ne sait pas, et qu'on ne dira pas

Ce qu'il fait du Mana Brut. C'est une question ouverte volontairement : tant
qu'elle n'a pas de réponse, chaque joueur a la sienne.

### Son déplacement

Les points d'apparition sont déclarés en configuration, avec un nom de région pour
chacun. Le rythme est réglable, et le thème **Chaos** d'une Ère le raccourcit — un
Forgeron qui bouge plus souvent rend les routes moins prévisibles.

Voir [Autels et Mana Brut](../04-jouer/autels-et-mana.md) et
[cdc_mana_brut](../90-specifications/cdc_mana_brut.md).

---

## 3. Les neuf traditions

Ce ne sont pas des factions, et elles ne l'ont jamais été. Ce sont **neuf façons
d'écouter les Arcanes** — aujourd'hui, neuf façons d'y puiser.

Six sont encore des écoles vivantes au sens plein. Trois ont été absorbées : il ne
restait plus assez de praticiens pour transmettre seules.

### 3.1. Les neuf écoles vivantes

Le joueur en choisit une à l'arrivée. Ce choix **oriente** sa progression sans
fermer les autres : il apprendra plus vite dans sa voie, il peut tout apprendre.

| École | Identifiant | Ce qu'elle est | Palette |
|---|---|---|---|
| **Arcane** | `ARCANE` | L'étalon. Ni la plus rapide ni la plus forte ; c'est à elle qu'on compare les autres. Géométrie pure. | violet |
| **Braises** | `FIRE` | La plus directe, la plus bruyante. Frappe en aire et laisse une trace au sol. | orangé |
| **Givre** | `FROST` | Le contrôle. Peu de dégâts, beaucoup d'entraves. Formes anguleuses, sons de verre. | bleu pâle |
| **Tempête** | `STORM` | La vitesse. Projectiles rapides, déplacements, poussées. | violine |
| **Roc** | `EARTH` | Protège, bâtit, nourrit. Effets lourds et lents, ancrés : les particules retombent au lieu de monter. | terre |
| **Aurore** | `LIGHT` | Soigne, éclaire, révèle. Ses sorts annoncent leur intention plutôt que de surprendre. | doré |
| **Esprit** | `SPIRIT` | Le soutien. La seule école pensée pour le groupe avant l'individu. | pâle |
| **Ombre** | `SHADOW` | Dissimule, affaiblit, sacrifie. Ses effets absorbent la lumière au lieu d'en émettre. | noir |
| **Vide** | `VOID` | Nie. Coupe la magie, affaiblit, distord. Formes en spirale. | presque noir |

Chaque école a sa fiche détaillée — identité visuelle, sons, sorts — dans
[`docs/90-specifications/magie/ecoles/`](../90-specifications/magie/ecoles/).

### 3.2. Les trois traditions absorbées

| Tradition | Identifiant | Absorbée par | Ce qui les rapproche |
|---|---|---|---|
| **Sylvanie** | `NATURE` | Roc | Cultures, récoltes et pierre relèvent du même rapport à la terre |
| **Sang** | `BLOOD` | Ombre | Payer de sa personne est une facette de la dissimulation |
| **Chronos** | `TIME` | Vide | Déformer le temps et déformer l'espace sont le même geste |

Un sort ou une baguette peut porter le nom de sa tradition d'origine — la Baguette
de Fer-Sang, la Baguette de Verre-Chronos. **C'est de la mémoire et rien d'autre :**
le niveau se gagne dans l'école qui a absorbé, et aucune règle de jeu ne connaît les
traditions.

On n'en crée plus de nouvelles, et on ne ressuscite pas les trois qui restent.

---

## 4. Le Panthéon

Le monument du spawn. Il porte le nom des Covens vainqueurs des Ères passées — le
Coven au plus haut niveau de Nexus à la clôture.

| | |
|---|---|
| **Où** | Au spawn, à une position déclarée en configuration |
| **Forme** | Hologrammes, un par vainqueur |
| **Combien** | Les cinq derniers par défaut |
| **Ce qu'il donne** | Un titre au Coven vainqueur, et un cosmétique. Rien de plus. |

C'est la seule trace persistante qu'une Ère a eu une issue. Tout le reste retombe.

Le Panthéon ne donne **aucun avantage**. Un monument qui rendrait plus fort ferait
d'une victoire passée une avance présente, et les Ères n'auraient plus de sens.

---

## 5. Le Colosse de l'Ère

Ce n'est pas une créature : c'est la saignée qui prend une forme assez longtemps
pour qu'on puisse la frapper.

| | |
|---|---|
| **Apparaît** | En phase de Grand Cataclysme uniquement. Lancé hors de cette phase, il est refusé. |
| **Durée** | Deux heures, puis il se retire |
| **Vainqueur** | Celui qui porte le dernier coup |
| **Nom** | Suit le thème de l'Ère : Colosse des Ténèbres, d'Aurore, des Fractures |

S'il se retire de lui-même, personne n'a rien gagné : ni vainqueur, ni butin. Le
laisser traîner ferait d'un événement une décoration ; le faire mourir seul
récompenserait l'attente.

Le critère du dernier coup est assumé. Compter la contribution de chacun sur deux
heures demanderait un tableau qui vivrait en mémoire, disparaîtrait au
redémarrage, et se disputerait au premier écart d'un point.

Son modèle a son propre cahier des charges :
[eres/bbmodel_colosse](../90-specifications/eres/bbmodel_colosse.md).

---

## 6. Les compagnons

Quatre familiers, chacun lié à une sensibilité différente. Ils ne sont pas des
figures du lore au même titre que les précédentes : ce sont des créatures
personnelles, et leur histoire est celle que leur joueur leur fait.

| Compagnon | Ce qu'il est | Modèle |
|---|---|---|
| **Ignis**, le Dragonnet de Braise | Niche sur les crêtes, nocturne, rare | `ignis` |
| **Nox**, le Dragonnet d'Ébène | Son pendant d'ombre | `dragonnet_ebene` |
| **Aelindra** *(esprit)* | Un esprit d'âmes, sans rapport avec l'Éveilleuse | `aelindra` |
| **L'Esprit Cristallin** | Né de l'Aether, cristal et lumière | `esprit_cristallin` |

Les deux dragonnets existent aussi à l'**état sauvage**, et ce sont alors des
adversaires de la taille d'un dragon adolescent. Un dragonnet sauvage apprivoisé
rétrécit : c'est une contrainte de jeu, pas une fiction, et elle est assumée comme
telle.

Voir [Compagnons](../04-jouer/compagnons.md).

---

## 7. Les puissances muettes

Ce dont le lore parle sans jamais le montrer. Elles existent pour que le monde ait
une profondeur qu'on ne peut pas épuiser.

| Puissance | Ce qu'on en sait |
|---|---|
| **Les Arcanes** | La nappe. Elles ne parlent pas, ne choisissent pas — « les Arcanes t'ont choisi » est une façon de parler d'Aelindra, pas une volonté. |
| **Celui qui a puisé le premier** | Personne. Aucun texte ne le nomme, aucun ne le nommera. |
| **Ceux qui ont taillé les Autels** | Inconnus. On ne sait pas non plus s'ils étaient sept à l'origine. |
| **Ce qu'il y a au-delà des Terres connues** | Rien d'écrit. C'est la réserve d'Ères à venir. |

**Règle absolue :** on n'écrit pas de texte qui répond à l'une de ces questions.
Une porte fermée ne se rouvre pas.

---

## 8. Écrire un nouveau personnage

Il faut quatre choses, dans cet ordre :

1. **Une fonction de jeu.** Un personnage qui ne donne rien, ne vend rien et
   n'ouvre rien n'a pas besoin d'exister. Le Forgeron achète du Mana Brut ; c'est
   sa raison d'être, et son caractère vient après.
2. **Un registre.** Deux phrases suffisent à le fixer : comment il parle, ce qui
   l'agace. Sobre, comme tout le reste — voir les
   [règles d'écriture du lore](lore.md#9-les-règles-décriture-du-lore).
3. **Une place.** Où on le trouve, et s'il bouge.
4. **Ce qu'il ne sait pas.** Aussi important que ce qu'il sait : un personnage qui
   répond à tout supprime le besoin de chercher.

### Ce qu'un personnage ne peut jamais faire

- Désigner un joueur comme élu.
- Accorder un avantage de combat qui ne s'obtient pas autrement.
- Révéler la position d'un autre joueur.
- Donner une explication à l'une des questions ouvertes du paragraphe 7.

---

## À lire ensuite

- [Le lore](lore.md) — l'histoire dont tout ceci découle
- [Géographie](geographie.md) — où ces figures se trouvent
- [La magie](../04-jouer/magie.md) — comment un joueur pratique sa tradition
- [Créer une quête](../06-creer-du-contenu/creer-une-quete.md) — faire parler un personnage
