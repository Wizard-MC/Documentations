# Conception du grimoire

L'interface où le joueur gère tout ce qui touche à sa magie : sa progression,
ses sorts, sa barre, ses configurations.

Voir aussi : [`../../cdc_magie.md`](../../cdc_magie.md) (règles du système),
[`vfx_attaques.md`](vfx_attaques.md) (présentation des sorts).

---

## 1. Le parti pris

Le grimoire est **un livre**, pas un panneau d'options. Cela n'est pas
décoratif : c'est ce qui dicte la mise en page, la navigation et jusqu'au choix
des boutons.

Trois conséquences concrètes :

- **Deux pages en vis-à-vis**, jamais une seule colonne. La page de gauche
  liste, la page de droite détaille. Le regard fait l'aller-retour sans
  changer d'écran.
- **Des signets**, pas des onglets. On feuillette avec les flèches ← →, comme
  on tourne les pages d'un livre.
- **Pas de boutons gris.** Le fond est un parchemin peint ; les boutons
  standard du client, alignés par dizaines, écrasent complètement sa
  typographie.

---

## 2. Les quatre chapitres

Le découpage suit ce que le joueur vient faire, pas la structure des données.

| Chapitre | On y vient pour | Page de gauche | Page de droite |
|---|---|---|---|
| **Écoles** | voir où j'en suis | les neuf écoles, niveau et jauge | l'arbre des sorts de l'école, par niveau requis |
| **Sorts** | apprendre, améliorer | sélecteur d'école puis répertoire | la fiche du sort, apprendre / monter d'un rang |
| **Équipement** | choisir mes six sorts | les six emplacements | les sorts assignables |
| **Presets** | changer de jeu de sorts | les huit configurations | le détail, enregistrer / charger / effacer |

Un chapitre par intention évite l'écran unique où tout se dispute la place.

### L'arbre de compétences

Il n'y a pas d'arbre au sens graphique — pas de nœuds reliés par des traits.
La progression d'une école *est* une échelle : chaque sort déclare un niveau
requis, et l'ordre de lecture suffit à la montrer.

La page range donc les sorts par niveau croissant, avec un séparateur par
palier, et trois états lisibles d'un coup d'œil :

| Marque | Sens |
|---|---|
| ✔ vert | appris |
| · encre | niveau atteint, pas encore appris |
| × pâle | niveau pas encore atteint |

Un chiffre romain en marge indique le rang d'un sort amélioré.

---

## 3. La géométrie

Le fond est `grimoire_interface.png` : un livre ouvert photographié. Ses deux
pages de parchemin ne sont **ni centrées ni symétriques**, et la reliure n'est
pas au milieu. Écrire du texte à des coordonnées devinées le ferait déborder
sur la tranche ou sur le cadre doré.

Les zones utiles ont donc été **mesurées sur l'image source** (1200×800), en
cherchant ligne par ligne où le parchemin clair commence et s'arrête :

| Zone | Sur la source | En fraction |
|---|---|---|
| Page gauche | x 116 → 565 | 0,097 → 0,471 |
| Page droite | x 644 → 1100 | 0,537 → 0,917 |
| Hauteur des pages | y 34 → 689 | 0,043 → 0,861 |
| Reliure | x ≈ 604 | 0,504 |

Ces fractions restent valables quelle que soit la taille d'affichage : changer
l'échelle du panneau ne demande aucun recalcul. Une marge intérieure de neuf
points tient le texte à l'écart du liseré doré.

---

## 4. L'animation de page

Le tournement utilise `grimoire_interface_page.png`, la feuille seule.

**Ce que ce n'est pas.** Un rectangle qui rétrécit puis grandit de l'autre
côté. Cet effet-là ressemble à un store vénitien, pas à du papier.

**Ce que c'est.** La feuille est découpée en **quatorze bandes verticales**,
placées le long d'un arc :

- la position horizontale de chaque bande suit le cosinus de l'angle de
  rotation — la feuille traverse la reliure et ressort de l'autre côté, en
  passant par une largeur nulle à mi-course ;
- la hauteur enfle légèrement vers le milieu de l'arc : c'est ce renflement qui
  donne la sensation de volume ;
- chaque bande s'assombrit proportionnellement à l'inclinaison, ce qui simule
  l'ombre du pli.

**Le temps.** L'avancement est mesuré en millisecondes réelles, pas en frames.
Une animation cadencée sur les frames serait deux fois plus lente à 30 FPS
qu'à 60 — et le grimoire est justement l'écran où le jeu tourne au ralenti
derrière. La progression est adoucie en entrée et en sortie : une feuille
lâchée part lentement, bascule vite, puis se pose.

**Le basculement du contenu** se fait à mi-course, quand la feuille est sur la
tranche et masque ce qu'il y a derrière. C'est le seul instant où l'on peut
changer le texte sans que le joueur voie la substitution.

**Pendant le tournement, aucun bouton n'est actif** : cliquer sur une page en
train de disparaître serait imprévisible.

Le tournement se déclenche à chaque changement de chapitre — vers l'avant si
l'on avance dans le livre, vers l'arrière sinon — et à chaque changement
d'école, chaque école étant un chapitre du répertoire.

---

## 5. Les deux sortes de boutons

| Élément | Aspect | Pour quoi |
|---|---|---|
| **Zone de page** | invisible au repos, voile chaud au survol | naviguer : choisir une école, un sort, un emplacement |
| **Plaque d'action** | fond encré, filet doré, texte centré | engager quelque chose : apprendre, améliorer, enregistrer, charger, effacer |

La distinction n'est pas cosmétique. Une navigation ne coûte rien et doit
s'effacer devant le texte ; une action dépense de l'XP ou écrase une
configuration, et doit se voir.

Le texte des zones de page n'est pas dessiné par le bouton mais par la page
elle-même, ce qui laisse chaque chapitre libre de sa mise en page : deux
colonnes, une icône, une jauge, un rang en marge.

---

## 6. Ce que le client ne décide pas

Le grimoire n'applique **rien** localement. Cliquer « Apprendre », « Rang III »
ou « Charger » n'envoie qu'une intention ; le serveur vérifie, débite, applique,
puis renvoie l'état complet.

C'est plus lent d'un aller-retour, et c'est voulu : un rang affiché puis retiré
parce que le serveur a refusé serait pire qu'un rang qui met un instant à
apparaître. Les aperçus affichés — coût d'un rang, raison d'un refus — sont des
**miroirs** des formules serveur, présents uniquement pour informer avant le
clic. En cas de divergence, c'est l'aperçu qui a tort.

---

## 7. Les verrous s'expliquent

Un emplacement de preset fermé n'est pas simplement grisé : la page dit
pourquoi il l'est et ce qu'il faudrait pour l'ouvrir — un niveau d'école moyen,
ou la boutique. Un joueur qui ne comprend pas un verrou le prend pour une
panne.

Les emplacements d'agrément affichent en clair ce qu'ils sont : ils n'accordent
ni sort, ni niveau, ni statistique, et font gagner des clics. Le joueur a le
droit de le lire avant d'acheter, pas de le découvrir après.

---

## 8. Ajouter un chapitre

1. Ajouter une valeur à `GrimoireSection` (libellé, couleur du signet).
2. Écrire une page dans `magic/grimoire/pages/` avec `build` (les boutons) et
   `draw` (le texte).
3. La brancher dans les deux `switch` de `GuiGrimoire`.

L'écran n'est qu'une coquille : il pose le livre, les signets et l'animation.
Rien d'autre n'est à toucher.
