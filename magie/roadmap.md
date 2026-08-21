# Roadmap Magie — chantiers en cours

> Une ligne par chantier, une branche par chantier. Chaque entrée dit ce qui ne
> va pas, ce qui a été **vérifié dans le code** (par opposition à supposé), et à
> quoi on reconnaîtra que c'est fini.

Dernière révision : 21/08 — chantiers 1, 4, 5, 6, 7 livrés, 8 aux trois quarts.

---

## Comment lire ce document

Chaque chantier porte un **état**, et c'est la première chose à regarder :

| État | Ce que ça veut dire |
|---|---|
| 🔴 **Bug confirmé** | Reproduit et expliqué dans le code. Prêt à corriger. |
| 🟠 **À concevoir** | Le besoin est clair, la solution demande des décisions. |
| 🟣 **Déjà corrigé** | Le code sur `main` est bon. Ce qui se voit en jeu vient d'un binaire plus ancien. |
| ✅ **Livré** | Corrigé sur sa branche, tests à l'appui. Reste à fusionner et redéployer. |

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

## Chantier 1 — Les glyphes ne sont pas dans le cercle ✅

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

### Livré

Le cercle procédural reprend la géométrie du modèle quand il y en a un : rayon
égal à la moitié de `targetSizeBlocks`, mêmes décalages. Les valeurs par mode
dataient d'avant les modèles et n'avaient plus de raison de prévaloir.

La distinction de taille qui les justifiait — un cercle d'amorce plus petit pour
un sort instantané qu'un sort incanté, parce que la taille du cercle dit le poids
du sort — n'a pas été perdue : elle est passée dans le modèle, avec
`CIRCLE_UPRIGHT_QUICK`. C'était le choix à faire plutôt que d'assouplir le test
qui la gardait.

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

## Chantier 4 — Régénération de mana, rythme RPG ✅

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

### Livré

Le rythme passe de **10 à 4 points par 10 secondes** hors combat, et de 2 à 1 en
combat. Une réserve de 100 se refait en **250 secondes** — un peu plus de quatre
minutes — au lieu de cent.

Deux options avaient été posées : renchérir les sorts, ou ralentir la
régénération. C'est la seconde qui a été retenue, et pour une raison précise :
les fioles rendent de l'Essence. Tant que la régénération naturelle reste rapide,
elles restent dominées par le fait d'attendre — **quel que soit le prix des
sorts**. Renchérir un sort rend le mage plus pauvre ; ça ne rend pas la fiole
plus utile que la patience. Seul le rythme le fait.

`EssenceRegenTest` gèle les deux bouts : les 250 secondes de remplissage, et le
fait que boire l'une des quatre fioles vaut au moins deux fois attendre.

---

## Chantier 5 — Progression : rendre visible ce qui existe déjà ✅

**Branche : `fix/progression-visibility`** · dépôts WizardCore + MCP

**Il n'y a rien à concevoir.** Le CDC §5.3 à §5.5 définit déjà tout le système,
et le code l'implémente. Vérifié :

| Règle du CDC | Où elle vit dans le code |
|---|---|
| 8 XP par lancement réussi | `SchoolProgressionService.grantSuccessfulCastXp` |
| Crédit à chaque cast réussi | `CastService:135` (instantané) et `:380` (incanté) |
| Seuils 50 · 150 · 300 … 2750 | `SchoolProgressionSettings`, repris dans `magic.yml` |
| Bonus Bibliothèque +10 % | `applyLibraryXpBonus` |
| Bonus Autel lié +5 %/palier | `AltarSchoolLink.applySchoolXpBonus` |
| Coût d'apprentissage `20 + 15×niv + 8×appris` | `SpellLearnService` |
| Coût d'amélioration `30 + 25×(rang−1) + 10×niv` | `SpellRankService` |

Le premier niveau demande 50 XP, soit **sept lancements réussis**. Un joueur
devrait donc le voir arriver dans sa première session. S'il ne le voit pas, ce
n'est pas que l'XP ne monte pas — c'est que **rien ne la montre**.

### Le vrai problème est donc l'affichage, pas le calcul

Trois manques, dans l'ordre où le joueur les rencontre :

1. **aucun retour au moment du gain.** Rien ne dit « +8 XP Braises » quand un
   sort part. Le joueur n'a aucune raison de croire qu'il progresse ;
2. **aucune information sur la règle.** Le grimoire ne dit nulle part que
   lancer un sort rapporte de l'XP dans son école, ni qu'un sort interrompu ou
   bloqué ne rapporte rien (§5.3) ;
3. **le coût de la prochaine étape n'est pas exposé.** Apprendre et améliorer
   ont des formules précises ; le joueur ne voit ni le prix, ni combien il lui
   manque.

### À vérifier avant de coder

Une seule question ouverte, et elle est mesurable : l'XP arrive-t-elle jusqu'au
client ? La synchronisation du grimoire transporte `schoolXps` — reste à
confirmer qu'elle est lue et affichée. À instrumenter en jeu **après le chantier
0**, sur un serveur à jour.

### Fini quand

- un gain d'XP se voit à l'instant où il est gagné ;
- le grimoire dit comment on gagne de l'XP, et ce que coûte la prochaine
  amélioration ;
- sept lancements font monter une école d'un niveau, vérifié sans commande.

### Livré

La question ouverte ci-dessus a trouvé sa réponse, et elle était pire que
prévu : l'XP arrivait bien jusqu'au client, mais **ce n'était pas la bonne**.

#### Un quatrième défaut, non listé, et le plus grave

Un profil porte deux compteurs. L'**XP cumulée** monte à chaque sort réussi et
ne redescend jamais : c'est elle qui décide du niveau. L'**XP dépensable** monte
de la même façon mais *baisse* quand on apprend ou améliore un sort : c'est la
bourse.

La trame de synchronisation n'envoyait que la seconde, et le client s'en servait
pour dessiner sa jauge de niveau. Deux erreurs s'y superposaient :

- la jauge **reculait** après un achat, alors qu'un niveau acquis ne se reperd
  pas ;
- elle ne retranchait pas le palier déjà franchi. Au niveau 1 avec 50 d'XP, elle
  affichait 50 / 100 : une barre à moitié pleine vers le niveau 2 sans en avoir
  fait un pas.

Autrement dit, le joueur qui allait chercher sa progression dans le grimoire y
trouvait un chiffre faux. C'est pire que l'absence d'information des trois
points listés plus haut, et ça explique une part du « on ne gagne jamais d'XP ».

#### Ce qui a été fait

Un bloc d'extension `PRG1` s'ajoute à la trame `SYNC`, après le bloc grimoire et
selon le même principe — signature, version, longueur — donc sans casser les
clients ni les serveurs d'une autre version. Il porte l'XP cumulée, les paliers
de la courbe, le barème par sort réussi et le dernier gain.

1. **La jauge dit la vérité.** Elle se calcule sur l'XP cumulée, palier courant
   retranché. La bourse garde sa ligne, et la fiche d'école explique désormais
   en une phrase ce qui distingue les deux nombres.
2. **Les paliers viennent du serveur.** Ils étaient recopiés à la main côté
   client et devenaient faux dès qu'une courbe était reconfigurée. La table
   écrite dans le client n'est plus qu'un repli, et une courbe incohérente est
   refusée plutôt qu'adoptée.
3. **Le gain se voit.** Une annonce « +8 XP Braises » flotte au-dessus de la
   jauge d'Essence, en face du « -12 » de la dépense : même endroit, même
   idiome, signe opposé. Elle ne se lève que lorsque le numéro de séquence
   change — la trame `SYNC` part aussi pour une dépense ou un changement de
   preset, et transporterait alors le même gain. Trois sorts rapprochés dans une
   même école s'additionnent en « +24 XP » plutôt que de se chasser l'un
   l'autre.
4. **La règle est écrite**, sous les neuf jauges et une seule fois : elle vaut
   pour les neuf écoles, et la répéter sur chaque fiche prendrait la place de
   l'arbre de sorts. Le barème vient du serveur. Les bonus de Bibliothèque et
   d'Autel sont **nommés sans être chiffrés** — leurs pourcentages vivent dans
   `magic.yml` et ne voyagent pas jusqu'au client ; les recopier réintroduirait
   exactement le défaut corrigé au point 2.
5. **Le prix d'un rang est traduit en lancements.** « 43 XP » ne dit rien ;
   « environ six sorts réussis » répond à la seule question qu'on se pose devant
   ce chiffre.

Une note sur l'horloge de l'annonce : elle compte en millisecondes, pas en
images. Le flash de dépense voisin décrémente un compteur dans la boucle de
rendu, ce qui le fait durer deux fois moins longtemps à cent images par seconde
qu'à cinquante — un comportement que personne n'a voulu, et qu'il ne fallait pas
reproduire.

`XpVisibilityTest` (WizardCore) et `ProgressionTest` (MCP) figent la même
arithmétique des deux côtés, y compris les sept lancements du premier niveau.

---

## Chantier 6 — Fiche de sort complète dans le grimoire ✅

**Branche : `feature/grimoire-spell-info`** · dépôts WizardCore + MCP

L'écran « Sorts » ne montre pas ce qu'un joueur a besoin de savoir pour choisir :
**dégâts, portée, coût en Essence, temps d'incantation, cooldown**, et ce que
change le rang suivant.

Toutes ces valeurs existent déjà côté serveur et transitent en partie dans la
synchronisation du grimoire — à compléter plutôt qu'à inventer.

### Fini quand

- chaque sort affiche portée, coût, incantation, cooldown et effet chiffré ;
- les valeurs viennent du serveur, jamais d'une table recopiée côté client.

### Livré

Le second critère n'était pas une précaution de style : **la table recopiée
avait déjà divergé**. `SpellUpgradePreview.cooldownAtRank`, côté client,
appliquait le facteur de rang au cooldown sans le plancher que
`SpellRankService` impose aux sorts hostiles. Sur un sort déjà proche de ce
plancher — et le catalogue en contient — la fiche promettait une amélioration
que le joueur n'obtiendrait jamais. C'est le second nombre écrit des deux côtés
qui se révèle faux en deux chantiers, après les paliers d'XP du chantier 5.

#### Le packet `CATALOG`

Une nouvelle action, `CATALOG(10)`, porte les fiches de tous les sorts : portée,
temps d'incantation et de canalisation, GCD, palier de baguette, et **les cinq
rangs déroulés** — coût en Essence, cooldown plancher compris, prix en XP,
niveau d'école exigé. Le client ne recalcule plus rien.

Elle part **une fois** à la connexion, et à chaque rechargement de
configuration. Ces valeurs ne dépendent pas du joueur et ne changent jamais en
partie : les glisser dans le bloc `SYNC` — qui part à chaque dépense d'Essence —
ferait réémettre plusieurs kilo-octets figés à chaque sort lancé. C'est la
différence de nature qui justifie une action plutôt qu'un bloc d'extension de
plus.

Un client antérieur reçoit un identifiant d'action qu'il ne connaît pas :
`fromId` lui rend `null` et il abandonne la trame sans rien casser.

#### Les phrases d'effet

`SpellEffectSummary` écrit côté serveur ce que fait chaque sort, une phrase par
effet — « Inflige 6 dégâts à la cible », « Emprisonne la cible dans la glace
pendant 4 s ». Ce travail lui revient parce que le sens des clés de
`spells.yml`, et surtout **les valeurs par défaut de chaque paramètre quand
elles sont absentes**, n'existent que dans le handler qui les lit. Un client qui
voudrait composer « 6 dégâts » devrait connaître les deux, c'est-à-dire recopier
une table de plus.

Un effet non décrit sort sous son identifiant technique plutôt que d'être passé
sous silence — une fiche muette dirait au joueur que le sort ne fait rien — et
un test interdit ce repli sur le catalogue livré.

#### Ce que la fiche affiche

Portée, Essence, cooldown, incantation, canalisation *quand il y en a une*,
niveau d'école et palier de baguette ; puis les effets en toutes lettres. La
ligne du rang suivant annonce ce qu'il change réellement — le barème ne touche
qu'au coût et au cooldown — et se tait quand il n'apporte plus rien, notamment
quand le cooldown bute sur son plancher.

L'infobulle de la liste reprend le triplet portée / coût / cooldown, celui sur
lequel on tranche entre deux sorts sans ouvrir chaque fiche.

Ce que le client ne sait pas, il l'écrit en **tiret**. La portée et le cooldown
ne sont nulle part côté client : les afficher à zéro ferait passer un sort de
vingt blocs pour un sort de contact. Un tiret dit « on ne sait pas encore », ce
qui est exactement le cas d'un client à jour face à un serveur qui ne l'est pas.

#### Ce qui n'a pas changé

Le nom, la description et l'incantation restent dans le catalogue client : ce
sont des textes, déjà vérifiés contre `spells.yml` par les tests VFX. Le
chantier demandait que les *valeurs* viennent du serveur — c'est ce que la fiche
livre, et rien de plus.

`SpellSheetTest` existe des deux côtés : côté serveur pour l'arithmétique des
rangs et la complétude du catalogue livré, côté client pour la mise en forme et
le comportement en l'absence de fiche.

---

## Chantier 7 — Roue de sélection ✅

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

### Livré

**Le gel de l'animation.** `MagicVFXRuntime.tick()` vivait dans le bloc de
saisie de `Minecraft.runTick`, gardé par « aucun écran ouvert, ou écran qui
laisse passer la saisie ». `GuiScreen.allowUserInput` vaut `false` par défaut :
ouvrir la roue sautait donc tout le bloc, et le sort en vol se figeait jusqu'à
la fermeture. L'appel est remonté hors de ce bloc. Un sort en vol est une
simulation du monde au même titre qu'une entité ; son avancement n'a aucune
raison de dépendre de ce que le joueur tape.

**Les noms.** La roue affichait le dernier segment de l'identifiant technique :
« Fireball », « Preserve », « Hex ». Ce n'est pas une abréviation mais un autre
nom, en anglais, absent partout ailleurs dans le jeu — le grimoire et les fiches
disent « Boule de Feu ». `RadialLabels` interroge le catalogue client, qui porte
le nom exact des trente-deux sorts. Le découpage d'identifiant subsiste en
**repli**, pour le cas où le serveur enverrait un sort qu'un client plus ancien
ne connaît pas.

**La molette.** `RadialPresetCycle` la fait défiler en boucle, dans les deux
sens, parmi les presets **débloqués et non vides**. La seconde condition est
celle qui protège : charger un preset vide viderait la barre de sorts, et la
molette deviendrait un moyen de se désarmer en plein combat. Rien n'est envoyé
quand il n'y a rien à changer, et un garde de 150 ms évite qu'un geste vif ne
déclenche dix synchronisations complètes du profil.

**Le centre.** Une ligne annonce le preset chargé — par son nom, ou par son
numéro s'il n'en a pas — et se met à jour dès le coup de molette, sans attendre
le retour serveur. Les lignes du centre sont désormais découpées à la largeur du
cercle intérieur : « Garde Spirituelle » débordait sur les icônes voisines.

`RadialMenuTest` gèle la correspondance identifiant → nom pour tout le catalogue
et les cas limites du défilement — boucle, presets vides, presets verrouillés,
rien à changer, pas de molette fine ignoré.

---

## Chantier 8 — Le facteur « WOW » ✅ (4 pistes sur 6)

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

### Livré

Quatre pistes sur six. Les mécanismes ont bien fini par avoir des critères
mesurables — pas sur le rendu, qui se juge à l'œil, mais sur les nombres qui le
pilotent, et c'est là que se cachait le seul vrai défaut du lot.

#### Les salves

Un clip peut déclarer `volley(nombre, dispersion, décalage)`. Le givre en tire
**cinq** sur un mètre et demi à trois ticks d'intervalle, la roche **quatre**
plus lentement — la pierre est lourde, un écart de cinq ticks s'entend autant
qu'il se voit — et la foudre **trois** très vite et très près, assez près pour
qu'on ne doute jamais de l'endroit frappé.

Deux choix méritent d'être notés parce qu'ils ne sont pas évidents :

- **la disposition suit une spirale d'angle d'or**, pas un tirage uniforme. Au
  hasard, les exemplaires font des paquets et des trous : on obtient un tas, pas
  une gerbe. La spirale garantit que deux exemplaires consécutifs — ceux qui
  partent l'un après l'autre, donc ceux qu'on regarde ensemble — sont toujours
  nettement séparés ;
- **tout dérive de la graine de l'événement**, partagée par tous les clients.
  Deux joueurs côte à côte voient la même gerbe, aux mêmes endroits, dans le même
  ordre. Un tirage local donnerait à chacun sa version du même sort, ce qui se
  remarque immédiatement et rend impossible de décrire ce qu'on a vu.

L'exemplaire de tête reste sur l'ancre et sans retard : c'est lui qui marque le
point d'impact réel, et c'est donc lui qu'on garde quand la qualité graphique ou
le budget de VFX force à écrêter. La gerbe se voit alors plus petite, jamais
trouée.

Le geste se fait dans le code plutôt que dans le `.bbmodel` : le même modèle
servira à toutes les densités, et chaque exemplaire reste une instance séparée,
donc cullée, éclairée et budgétée pour elle-même.

#### L'annonce de l'impact

Pendant le vol, une couronne au sol se resserre vers le point visé et le touche
exactement quand le sort arrive. Elle **rétrécit** au lieu de s'ouvrir : une onde
qui s'ouvre raconte ce qui vient d'arriver — c'est déjà ce que fait l'impact —
là où une couronne qui se referme raconte ce qui converge, et son rayon dit
combien de temps il reste.

Elle ne devine rien : le point visé et la durée du vol arrivent tous deux dans
l'événement de trajet. Et comme le serveur cherche sa victime à l'arrivée et non
au départ, l'avertissement sert vraiment à quelque chose — s'écarter marche.

Les vols de moins de six ticks ne sont pas annoncés : l'anneau naîtrait et
mourrait dans le même souffle, ce qui donnerait un clignotement au lieu d'un
avertissement.

#### La secousse de caméra était à l'envers

C'est le défaut du lot, et il ne figurait pas dans les pistes. La secousse était
dérivée de **l'intensité du flash**, ce qui confond deux choses sans rapport : la
lumière d'un impact et sa masse. Le résultat était contraire au bon sens —

| | flash | ce que ça donnait |
|---|---|---|
| `heal` (soin) | 0,80 | ébranlait l'écran… |
| `dust` (éboulement) | 0,70 | …plus qu'un éboulement |

Se faire soigner secouait la caméra. Une nuée de feuilles la faisait trembler
autant qu'un éclat de givre. Le poids est désormais déclaré à part, famille par
famille : un soin ne secoue rien, parce que rien ne frappe ; la poussière secoue
le plus, parce que c'est de la roche qui tombe.

#### La couche sonore grave

Réservée aux familles qui ont une masse — feu, glace, roche. Un halo de lumière
ou une nuée de feuilles n'ont aucune raison de faire vibrer le sol. Un test
vérifie la cohérence des deux réglages : ce qui pèse assez pour secouer la caméra
pèse assez pour porter un grave.

#### Les deux pistes restantes

L'**éclair de lumière à l'impact** et la **traînée persistante** attendent. La
première demande une lumière dynamique positionnelle, que la lueur portée ne sait
pas faire — elle éclaire son porteur, pas un point du monde. La seconde relève
plutôt du chantier 9, qui touche aux modèles eux-mêmes.

`ImpactWeightTest` gèle les trois mécanismes chiffrés, dont les deux inversions
concrètes qui ne doivent pas revenir.

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

---

## Audit de cohérence catalogue ↔ documents

Fait le 21/08, mécaniquement, contre `cdc_magie.md` et `magie/sorts/`.

| Contrôle | Résultat |
|---|---|
| Sorts dans `spells.yml` | 32 |
| Fiches dans `magie/sorts/` | 32 — **aucun orphelin dans un sens ni dans l'autre** |
| `autoLearn` CDC §5.4 ↔ catalogue | 13 des deux côtés, **identiques** |
| Coût, cooldown, niveau, tier (table §11) | **concordent pour les 32 sorts** |
| Coût, niveau, tier (fiches individuelles) | **concordent pour les 32 sorts** |

Trois écarts trouvés, tous dans le CDC — le code avait raison, le document a été
corrigé :

- l'en-tête du §11 annonçait « 31 sorts » pour une table qui en listait 32 ;
- `storm_gust` y était déclaré non hostile alors qu'il l'est devenu ;
- la lecture finale disait « 10 sorts hostiles sur 31 » au lieu de 12 sur 32.

> Ce contrôle est mécanique et mérite d'être rejoué à chaque ajout de sort. Les
> fiches individuelles, elles, sont **générées** par `MagicDocGenerator` depuis
> le catalogue et les profils VFX réels : elles ne peuvent pas dériver tant
> qu'on les régénère.

---

## Ordre d'exécution proposé

```
0  Redéploiement            ← débloque la lecture de tout le reste
│
├─ 1  Glyphes du cercle     ✅ livré — fix/circle-glyph-placement
├─ 4  Rythme du mana        ✅ livré — fix/mana-regen-rpg
├─ 7  Roue de sélection     ✅ livré — fix/radial-menu
│
├─ 2  Ligne de tir          ← après 0, pour savoir ce qui reste
├─ 3  Torche compagne       ← après 0
│
├─ 5  Progression visible   ✅ livré — fix/progression-visibility
├─ 6  Fiche de sort         ✅ livré — feature/grimoire-spell-info
│
└─ 8  Facteur WOW           ✅ 4 pistes sur 6 — feature/vfx-impact
   └─ 9  Modèles enrichis
```

Les trois premiers après le chantier 0 sont volontairement des chantiers courts
et visibles : ils remettent du terrain sûr sous les pieds avant d'attaquer la
progression et les VFX, qui sont longs.

Les trois sont faits, ainsi que les chantiers 5, 6 et l'essentiel du 8. Restent
les chantiers 2 et 3, qui ne se jugeront qu'une fois le serveur redéployé sur le
code de `main` — c'est-à-dire après le chantier 0 — puis le chantier 9 et les
deux pistes de VFX laissées de côté.

Le chantier 8 a été pris avant le 2 alors que la roadmap le disait dépendant de
lui. C'était un pari raisonnable et il faut le noter : les quatre pistes traitées
sont toutes des mécanismes du client, indépendants de la visée. La sixième — la
traînée persistante — a en revanche été renvoyée au chantier 9, où elle a sa
place.

Deux fois en trois chantiers, le défaut de fond s'est révélé être **un nombre
écrit des deux côtés qui avait fini par diverger** : les paliers d'XP au
chantier 5, le plancher de cooldown au chantier 6. Les deux corrections vont
dans le même sens — le serveur envoie la valeur, le client la met en page — et
c'est la règle à appliquer au reste.
