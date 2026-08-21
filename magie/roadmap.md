# Roadmap Magie — chantiers en cours

> Une ligne par chantier, une branche par chantier. Chaque entrée dit ce qui ne
> va pas, ce qui a été **vérifié dans le code** (par opposition à supposé), et à
> quoi on reconnaîtra que c'est fini.

Dernière révision : audit du 21/08.

---

## Comment lire ce document

Chaque chantier porte un **état**, et c'est la première chose à regarder :

| État | Ce que ça veut dire |
|---|---|
| 🔴 **Bug confirmé** | Reproduit et expliqué dans le code. Prêt à corriger. |
| 🟠 **À concevoir** | Le besoin est clair, la solution demande des décisions. |
| 🟣 **Déjà corrigé** | Le code sur `main` est bon. Ce qui se voit en jeu vient d'un binaire plus ancien. |

Les chantiers sont **ordonnés par dépendance**, pas par importance : le 0 débloque
la lecture de tous les autres.

---

## Chantier 0 — Le serveur ne tourne pas le code de `main` 🟣

**Priorité : à faire avant tout le reste.**

Trois des symptômes signalés sont déjà corrigés sur `main`. Vérifié fichier par
fichier :

| Symptôme signalé | Ce que dit `main` |
|---|---|
| « les sorts ne partent pas où on vise » | `CastService:635` — `SpellAim.resolve(...)` est câblé |
| « les sorts n'ont pas de portée » | Le plafond `min(portée, 8)` a été retiré ; la portée déclarée est utilisée |
| « rien n'est touché sur la ligne » | `CastService:687` — `SpellAim.entityAt(...)` désigne la victime à l'impact |
| « la Torche pose une torche de redstone » | `ember_torch` ne déclare que `light_aura` ; **aucun sort** n'utilise `place_torch` |

En revanche les **glyphes s'affichent** en jeu (mal placés — voir chantier 1), et
ce changement-là n'est arrivé que dans la même vague. Le client MCP est donc à
jour.

**Conclusion : le client est à jour, le plugin WizardCore ne l'est pas.** Ce qui
colle avec le dernier build, qui s'est arrêté sur `:test` — le jar produit n'a
pas été redéployé.

**Action** : reconstruire et redéployer `WizardCore.jar` depuis `main`, puis
re-tester les quatre lignes du tableau. Ce qui persiste après ça est un vrai bug
et rejoint la liste ci-dessous.

> Tant que ce point n'est pas levé, on ne peut pas distinguer un bug d'un
> binaire périmé — et on risque de « corriger » du code déjà correct.

---

## Chantier 1 — Les glyphes ne sont pas dans le cercle 🔴

**Branche : `fix/circle-glyph-placement`** · dépôt MCP

### Le défaut

Le cercle est dessiné par **deux** choses superposées : un modèle bbmodel qui
trace l'anneau, et un cercle procédural qui inscrit les glyphes et les runes.
Leurs géométries ne sont pas accordées.

| | Rayon | Hauteur |
|---|---|---|
| Modèle (`CIRCLE_UPRIGHT`) | **1,00** bloc (2,0 de large) | −0,25 |
| Procédural, sort incanté | 0,95 → glyphes à **0,83** | −0,15 |
| Procédural, sort instantané | 0,62 → glyphes à **0,54** | −0,10 |

Les glyphes portent en plus un `radiusScale` de 0,87. Pour un sort instantané ils
sont donc inscrits à **0,54 de rayon dans un cercle qui en fait 1,00** : ils
flottent au milieu, et dix à quinze centimètres trop haut.

### Ce qui le corrige

Accorder le cercle procédural au modèle quand il y en a un : reprendre son rayon
(moitié de `targetSizeBlocks`) et ses décalages, au lieu des valeurs par mode qui
datent d'avant les modèles.

### Fini quand

- les glyphes tombent sur l'anneau du modèle, quel que soit le mode de cast ;
- un test gèle l'accord des deux géométries, pour qu'un changement de taille du
  modèle ne les redésaccorde pas en silence.

---

## Chantier 2 — La ligne de tir 🟠

**Branche : `fix/spell-line-of-fire`** · dépôt WizardCore

À traiter **après** le chantier 0 : le code actuel fait déjà l'essentiel, il faut
d'abord savoir ce qui reste vraiment cassé une fois le serveur à jour.

### Ce qui existe

`SpellAim` s'arrête au premier bloc plein ou à la première entité, jusqu'à la
portée déclarée du sort.

### Ce qui manque, quoi qu'il arrive

- **les PNJ et les entités non vivantes** ne sont pas testés. Le raycast ne
  retient que les `LivingEntity` ; un PNJ porté par une autre implémentation
  passe au travers ;
- **rien ne dit au lanceur ce qu'il a touché**. Un sort qui rate et un sort qui
  frappe un mur se ressemblent ;
- **la première chose sur la ligne** doit gagner, entité *ou* bloc, selon ce qui
  vient en premier — à vérifier explicitement, c'est aujourd'hui implicite.

### Fini quand

- un test couvre « bloc avant entité » et « entité avant bloc » ;
- les PNJ du serveur sont touchés comme les joueurs ;
- le lanceur voit à quoi son sort s'est arrêté.

---

## Chantier 3 — La Torche compagne 🟣 puis 🔴

**Branche : `fix/torch-companion`** · dépôts WizardCore + MCP

La torche de redstone à l'impact vient du binaire périmé (chantier 0).

Reste un vrai nettoyage : `place_torch` est **encore enregistré** comme handler
alors qu'aucun sort ne l'utilise. C'est du code que personne n'exécute, donc que
rien ne protège — et qui peut ressurgir dans un fichier de données par accident.

### Fini quand

- `place_torch` est retiré du registre, ou explicitement documenté comme réservé ;
- la boule de lumière apparaît à hauteur d'épaule et suit le joueur pour la durée
  annoncée, vérifié en jeu après redéploiement.

---

## Chantier 4 — Régénération de mana, rythme RPG 🔴

**Branche : `fix/mana-regen-rpg`** · dépôt WizardCore

### Le défaut

Le rythme actuel est de **10 points par 10 secondes**, soit 1 point par seconde.
Demandé : **1 à 2 points toutes les 2 à 3 secondes**, c'est-à-dire environ trois
fois plus lent.

Conséquence directe, et c'est l'argument qui compte : à un point par seconde, les
potions de régénération ne servent à rien — la réserve se refait toute seule
avant qu'on ait besoin d'en boire.

### Ce qui le corrige

Descendre le rythme de base à ~4 points par 10 secondes hors combat, et
quasiment zéro en combat. Le mécanisme de report de reste existe déjà et permet
n'importe quel rythme lent sans arrondi.

### Fini quand

- une réserve de 100 met environ quatre minutes à se refaire hors combat ;
- les quatre paliers de potion redeviennent le moyen normal de repartir au
  combat ;
- le barème des potions est relu à l'aune du nouveau rythme (elles vont paraître
  bien plus fortes).

---

## Chantier 5 — Progression : on ne gagne jamais d'XP 🟠

**Branche : `feature/progression-xp`** · dépôts WizardCore + MCP

Le plus gros chantier de fond. Trois problèmes distincts, à ne pas confondre :

1. **on ne gagne pas d'XP** — à instrumenter avant de conclure : le barème existe
   (`xpPerSuccessfulCast: 8`), reste à vérifier qu'il est réellement crédité ;
2. **on ne sait pas comment en gagner** — aucune information nulle part, ni dans
   le grimoire ni en jeu ;
3. **on ne peut jamais améliorer** un sort ni une école — coût, condition et
   geste d'amélioration ne sont pas exposés.

### Décisions à prendre avant de coder

- que rapporte de l'XP, exactement ? lancer un sort, toucher, tuer, découvrir ?
- l'XP d'école et l'XP de sort sont-elles la même monnaie ?
- où se dépense-t-elle : grimoire seul, ou aussi un PNJ ?

### Fini quand

- un joueur voit son XP monter en jouant, et sait pourquoi ;
- le grimoire montre le coût de la prochaine amélioration et le geste pour la
  payer ;
- une session de test permet de monter une école d'un niveau sans commande.

---

## Chantier 6 — Fiche de sort complète dans le grimoire 🟠

**Branche : `feature/grimoire-spell-info`** · dépôt MCP

L'écran « Sorts » ne montre pas ce qu'un joueur a besoin de savoir pour choisir :
**dégâts, portée, coût en Essence, temps d'incantation, cooldown**, et ce que
change le rang suivant.

Toutes ces valeurs existent déjà côté serveur et transitent en partie dans la
synchronisation du grimoire — à compléter plutôt qu'à inventer.

### Fini quand

- chaque sort affiche portée, coût, incantation, cooldown et effet chiffré ;
- les valeurs viennent du serveur, jamais d'une table recopiée côté client.

---

## Chantier 7 — Roue de sélection 🔴

**Branche : `fix/radial-menu`** · dépôt MCP

Quatre défauts, dont un qui coûte des morts :

1. **le cast se fige quand on ouvre la roue.** L'animation du sort en cours ne se
   joue qu'après la fermeture. En combat, l'adversaire frappe pendant ce temps.
   C'est le point le plus grave et le premier à traiter ;
2. **pas de changement de preset à la molette** — demandé ;
3. **pas d'indication du preset courant** au centre de la roue ;
4. **les noms affichés ne correspondent pas aux sorts réels** — à vérifier contre
   le catalogue, comme on l'a fait pour les animations de compagnons.

### Fini quand

- un sort lancé continue son animation, roue ouverte ou fermée ;
- la molette fait défiler les presets, et le centre dit lequel est actif ;
- un test gèle la correspondance nom affiché ↔ sort du catalogue.

---

## Chantier 8 — Le facteur « WOW » 🟠

**Branche : `feature/vfx-impact`** · dépôt MCP

Le chantier le plus ouvert, et celui qu'il ne faut pas commencer en premier : il
demande des décisions artistiques, et il repose sur les chantiers 1 et 2 (un
sort mal placé ne sera pas plus impressionnant en étant plus gros).

### Pistes, par rapport valeur/effort décroissant

| Piste | Pourquoi ça compte |
|---|---|
| **Plusieurs instances d'un même clip** | Un seul pic de givre, c'est un accessoire. Cinq pics qui jaillissent en cascade, c'est un sort. Vaut pour Prison de Givre, Vague de Roche, Frappe de Foudre |
| **Anticipation avant l'impact** | Un télégraphe une demi-seconde avant : ombre au sol, air qui tremble. C'est ce qui fait ressentir la puissance |
| **Secousse de caméra dosée** | Existe déjà, sous-utilisée |
| **Éclair de lumière à l'impact** | La lueur portée existe déjà ; la réutiliser en flash bref |
| **Couches sonores** | Un grave à l'impact change plus la perception qu'un doublement des particules |
| **Traînée persistante** | Le sort laisse une marque une seconde après son passage |

### Fini quand

C'est le seul chantier sans critère mesurable — il se juge à l'œil. On le
découpera en sous-branches une fois les pistes arbitrées.

---

## Chantier 9 — Modèles bbmodel enrichis 🟠

**Branche : `feature/vfx-models`** · dépôt MCP + dépôt ASSETS

Dépend du chantier 8 pour les arbitrages, et des **assets** pour la matière.

Prison de Givre ne contient qu'**un** pic. Deux voies, à trancher :

- **côté code** — rejouer le même clip à plusieurs positions et à plusieurs
  instants, ce qui ne demande aucun nouvel asset et sert toutes les écoles ;
- **côté asset** — un modèle contenant cinq pics avec leur propre animation, plus
  contrôlable mais à refaire pour chaque sort.

La première voie est à essayer d'abord : elle est réutilisable, la seconde ne
l'est pas.

---

## Ordre d'exécution proposé

```
0  Redéploiement            ← débloque la lecture de tout le reste
│
├─ 1  Glyphes du cercle     ← isolé, rapide, visible
├─ 4  Rythme du mana        ← isolé, rapide, débloque l'intérêt des potions
├─ 7  Roue de sélection     ← contient un bug qui coûte des morts en combat
│
├─ 2  Ligne de tir          ← après 0, pour savoir ce qui reste
├─ 3  Torche compagne       ← après 0
│
├─ 6  Fiche de sort         ← indépendant, gros mais balisé
├─ 5  Progression XP        ← demande des décisions de game design
│
└─ 8  Facteur WOW           ← en dernier : repose sur 1 et 2
   └─ 9  Modèles enrichis
```

Les trois premiers après le chantier 0 sont volontairement des chantiers courts
et visibles : ils remettent du terrain sûr sous les pieds avant d'attaquer la
progression et les VFX, qui sont longs.
