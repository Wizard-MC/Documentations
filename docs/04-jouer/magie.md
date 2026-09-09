# La magie

Comment on lance un sort, comment on en apprend, et comment on progresse. Le système
le plus profond du serveur, et le seul dont toute la progression survit à la fin
d'une Ère.

**Statut : livré.** Neuf écoles, quarante et un sorts, treize baguettes.

---

## 1. Les quatre choses à comprendre

| | |
|---|---|
| **L'école** | Votre voie. Choisie à l'arrivée, elle oriente sans rien fermer. |
| **L'Essence Arcane** | Ce qui paie les sorts. Personnelle, régénérée, lente. |
| **L'incantation** | Tout sort se prépare à vue. C'est ce qui le rend esquivable. |
| **La baguette** | Ce qui lance. Son palier ouvre des sorts, son affinité réduit les coûts. |

---

## 2. L'Essence Arcane

Ce n'est **pas** le Mana Brut. Le Mana Brut est une ressource de Coven qu'on
transporte ; l'Essence est personnelle, invisible, et ne se vole pas.

| | Valeur |
|---|---|
| Réserve de départ | 100 |
| Régénération hors combat | 4 points toutes les dix secondes |
| Régénération en combat | 1 point toutes les dix secondes |
| Délai avant reprise hors combat | 5 secondes |
| Bonus par niveau d'école moyen | +2, plafonné à +40 |
| Bonus de bibliothèque (déblocage de ville) | jusqu'à +10 |

### Pourquoi c'est si lent

Une réserve de cent points demande un peu plus de quatre minutes à se refaire. Ce
rythme a été **descendu deux fois**, et la raison n'est pas le confort : à un point
par seconde, une réserve vide se refaisait en cent secondes sans rien faire, et
personne n'allait dépenser une matière rare pour gagner une minute d'attente. **Les
fioles de régénération ne servaient à rien.**

C'est la leçon d'équilibrage la plus claire du projet : une ressource trop généreuse
ne rend pas le jeu plus agréable, elle supprime un système entier.

### Ce qu'il faut en retenir en jeu

- **Gardez de quoi lancer deux sorts.** Une réserve vide en plein combat ne se
  refait pas : un point toutes les dix secondes ne sauve personne.
- **Les fioles comptent.** Elles sont la seule façon de revenir dans un échange.
- **L'Essence est dépensée au départ du sort**, pas à sa résolution. Un sort
  interrompu rend la moitié de ce qu'il a coûté.

---

## 3. Lancer un sort

### La séquence

1. **Les contrôles.** Sort appris ? Niveau d'école suffisant ? Palier de baguette
   suffisant ? Recharge écoulée ? Pause commune écoulée ? Assez d'Essence ? Un sort
   refusé l'est **avant** la dépense — on ne paie pas un sort qui ne part pas.
2. **L'incantation.** Le cercle se trace devant vous. Vous êtes visible, et
   vulnérable.
3. **La dépense.** L'Essence part, les recharges s'arment.
4. **L'effet.**

### L'incantation

**Tout sort s'incante.** C'est la fenêtre pendant laquelle l'adversaire peut réagir
— s'écarter, couper, fuir.

| | Valeur |
|---|---|
| Durée de base | 100 ticks, soit 5 secondes |
| Réduction par niveau d'école | −4 % par niveau |
| Réduction par rang de sort | −5 % par rang au-delà du premier |
| Plancher | 20 ticks, soit 1 seconde |

Un sort qui déclare une incantation plus longue garde la sienne.

La conséquence de jeu est importante : **un mage expérimenté incante vite**, et
c'est l'une des différences les plus sensibles entre un débutant et un joueur
installé.

### Les deux recharges

| | Effet |
|---|---|
| **Recharge du sort** | Propre à chaque sort |
| **Pause commune** | Une demi-seconde après n'importe quel sort |

La pause commune empêche d'enchaîner deux sorts dans le même instant. Sans elle, un
joueur avec deux sorts prêts vide son adversaire avant qu'il ait bougé.

### Se faire interrompre

Bouger de plus d'un demi-bloc pendant une incantation l'annule. Un sort interrompu :

- rend **la moitié** de l'Essence dépensée ;
- ne déclenche **pas** la recharge du sort ;
- brise visiblement le cercle côté client.

Le remboursement existe pour que couper un lanceur ne le ruine pas sans
contrepartie — sinon une seule interruption suffirait à le mettre hors jeu.

---

## 4. Apprendre des sorts

### Les trois voies

| Voie | Ce qu'elle coûte | Où |
|---|---|---|
| **Apprentissage d'office** | rien | Quelques sorts sont connus dès le départ |
| **Le grimoire** | de l'expérience d'école | La voie normale : `/magic learn` |
| **Les Tomes** | trouver le Tome | Dix-neuf sorts ne s'obtiennent que là |

### L'expérience d'école

| | Valeur |
|---|---|
| Par lancement réussi | 8 |
| Par Essence dépensée | 0 |
| Bonus de bibliothèque | +10 % |
| Bonus d'Autel Sacré lié | +5 % par palier |

**L'expérience vient de l'usage, pas de la dépense.** Un sort coûteux lancé une
fois ne rapporte pas plus qu'un sort bon marché. C'est volontaire : autrement, la
progression se ferait en vidant son Essence sur le vide.

L'expérience est gagnée dans **l'école du sort lancé**. Progresser dans une école
demande donc d'y jouer, et pas seulement de l'avoir choisie.

### Les Tomes

Dix-neuf sorts ne s'apprennent pas au grimoire : il faut leur Tome, et les Tomes
tombent des boss et des occurrences.

Ce sont les objets les plus chers du jeu à l'hôtel des ventes, pour une raison
simple : ils sont la seule chose qu'on ne peut pas obtenir en jouant seul
patiemment.

### Enseigner

Un joueur peut enseigner un sort à un autre — `/magic teach`. C'est le seul
mécanisme de transmission entre joueurs, et il fait du savoir une monnaie sociale.

---

## 5. Les rangs et les sceaux

### Les rangs

Un sort appris peut monter en rang, jusqu'au cinquième. Chaque rang :

| Effet | Par rang |
|---|---|
| Coût en Essence | −6 % (soit −24 % au rang V) |
| Durée d'incantation | −5 % |

Le prix d'un rang est de l'**expérience d'école**, et rien d'autre. Jamais de
monnaie, jamais de Gemmes.

### Les sceaux

Un sceau modifie une caractéristique précise d'un sort. Un sort déclare quels sceaux
il accepte.

La raison d'être des sceaux : un sort qui ne progresserait que sur un seul axe
finirait par n'avoir qu'une seule bonne façon d'être monté. Les sceaux donnent des
choix — et deux joueurs avec le même sort au même rang peuvent en avoir fait deux
outils différents.

**Ce qu'un sceau ne peut jamais faire :** s'acheter. Le principe est celui de
[la vision](../01-projet/vision-et-positionnement.md#21-aucun-avantage-payant) :
on n'achète pas de la puissance.

---

## 6. Les baguettes

Treize baguettes, quatre paliers. Une baguette fait trois choses : elle permet de
lancer, son palier ouvre des sorts, son affinité réduit coût et recharge.

| Palier | Baguettes | Réduction de coût et de recharge |
|---|---|---|
| **1** | Chêne Fracturé | aucune |
| **2** | Cendre Vive, Pin Givré, Saule Sylvain, Liant-Pierre | −10 % |
| **3** | Cristal d'Arcane, Bouleau d'Aurore, Verre-Tempête, Aulne d'Écho, Épine d'Ombre, Fer-Sang, Verre-Chronos | −15 % |
| **4** | Fractale Prime | −20 % |

### L'affinité

Chaque baguette au-delà du palier 1 favorise une école. Deux baguettes peuvent
partager une affinité — ce sont alors des variantes à collectionner, pas des paliers
de puissance.

Deux baguettes portent le nom d'une **tradition absorbée** : la Baguette de
Fer-Sang (tradition Sanguine, affinité Ombre) et la Baguette de Verre-Chronos
(tradition Chronienne, affinité Vide). C'est de la mémoire : aucune règle de jeu ne
connaît les traditions.

### La fabrication

Chaque palier se fabrique **à partir du précédent**. Le prix d'une baguette de
palier 4 contient donc tous les paliers inférieurs, et on ne peut pas échanger un
palier contre un autre.

Le réactif commun est l'Essence Arcane sous forme d'objet. Les fioles de
régénération suivent la même logique : quatre recettes, une par palier.

La liste complète est dans
[Catalogue des objets](../07-reference/catalogue-objets.md).

---

## 7. La barre de sorts et la roue

### La barre

Les sorts équipés, lançables immédiatement. On y place un sort avec `/magic bind`.

Un emplacement dont le sort n'est plus valide — sort désappris, niveau d'école
retombé — est **vidé**, pas ignoré. Un emplacement qui semble fonctionner mais ne
lance rien est pire qu'un emplacement vide.

### Les presets

Une barre enregistrée. On en a plusieurs, et on en achète jusqu'à trois de plus en
boutique.

C'est le cas limite de la politique de monétisation, et il mérite d'être compris : un
emplacement de preset **n'accorde ni sort, ni niveau, ni statistique**. Le joueur
pouvait déjà composer la barre à la main. On achète le fait de ne pas la recomposer —
du confort, pas de la puissance.

### La roue

**R** par défaut. Un menu circulaire pour changer de sort sans ouvrir le grimoire.
Réglable dans les options du client.

---

## 8. Les neuf écoles en jeu

| École | Ce qu'on y trouve | Pour qui |
|---|---|---|
| **Arcane** | L'étalon : des sorts corrects partout | Celui qui ne veut pas choisir |
| **Braises** | Dégâts en aire, traces au sol. La plus bruyante. | Le combattant direct |
| **Givre** | Entraves, ralentissements. Peu de dégâts. | Celui qui joue en groupe |
| **Tempête** | Projectiles rapides, déplacements, poussées | Le mobile |
| **Roc** | Protections, cultures, récoltes | Le bâtisseur, le colon |
| **Aurore** | Soins, lumière, révélation | Le soutien visible |
| **Esprit** | Soins légers, purges, protections partagées | Le soutien de groupe |
| **Ombre** | Dissimulation, affaiblissements, sacrifice | Celui qui joue seul |
| **Vide** | Coupe la magie adverse, distord | L'anti-mage |

Chaque école a sa fiche — identité visuelle, sons, sorts — dans
[`docs/90-specifications/magie/ecoles/`](../90-specifications/magie/ecoles/).

### Les trois traditions absorbées

Sylvanie est passée dans **Roc**, Sang dans **Ombre**, Chronos dans **Vide**. Un
sort de tradition Sanguine progresse dans l'école de l'Ombre comme les autres. Le
nom de la tradition est conservé par respect pour ce qu'elle était, et ne change
rien.

---

## 9. Là où la magie ne marche pas

Certaines régions interdisent la magie — typiquement les arènes.

L'interdiction vaut **au lancement et à l'impact**. Sans la seconde vérification, il
suffirait de se placer juste en dehors d'une arène pour en arroser l'intérieur.

Si la liste des régions interdites est vide, la magie est autorisée partout.

---

## 10. Les commandes

### Pour jouer

| Commande | Ce qu'elle fait |
|---|---|
| `/magic` — ou `/magic help` | L'aide |
| `/magic info` | Essence, école, niveaux |
| `/magic learn <sort>` | Apprendre un sort |
| `/magic bind` | Placer un sort sur la barre |
| `/magic voie` — ou `/magic way` | Sa voie et sa progression |
| `/magic teach` | Enseigner un sort à un autre joueur |
| `/magic essence` | Son Essence en détail |
| `/magic tome` — ou `/magic livre` | Utiliser un Tome |
| `/magic scroll` | Les parchemins |
| `/magic potion` | Les fioles |
| `/magic faconnier` | Le façonnier de baguettes |
| `/magic presetslot` | Ses emplacements de presets |
| `/magic starter` — ou `/magic debut` | Le nécessaire de départ |

### Pour l'administration

`/magic give`, `/magic unlock`, `/magic reload`, `/magic qa` exigent la permission
`wizardmc.magic.admin`.

---

## 11. Les pièges

| Piège | Ce qui se passe | Quoi faire |
|---|---|---|
| Vider son Essence avant un combat | Un point toutes les dix secondes en combat | Garder deux sorts de marge |
| Compter sur l'incantation courte en début de partie | Cinq secondes, c'est long | Lancer à couvert, ou après avoir fixé l'adversaire |
| Bouger pendant une incantation | Annulée au-delà d'un demi-bloc | Se poser avant de lancer |
| Monter une école en lançant sur le vide | L'expérience vient du lancement **réussi** | Jouer les sorts là où ils servent |
| Acheter un preset en croyant gagner en puissance | Il n'accorde rien de plus | C'est du rangement, et c'est assumé |
| Chercher un sort de Tome au grimoire | Dix-neuf sorts n'y sont pas | Boss et occurrences |

---

## À lire ensuite

- [Catalogue des sorts](../07-reference/catalogue-sorts.md) — les 41 sorts, valeurs exactes
- [Catalogue des objets](../07-reference/catalogue-objets.md) — baguettes, fioles, réactifs
- [cdc_magie](../90-specifications/cdc_magie.md) — la spécification complète
- [Combat et créatures](combat-et-creatures.md) — s'en servir contre le bestiaire
