# Équilibrage

Les principes qui tiennent WizardMC en équilibre, les garde-fous déjà en place, et
la marche à suivre quand quelque chose casse. Document de référence pour toute
décision de réglage.

---

## 1. Les quatre principes

### 1.1. Aucune voie ne doit être obligatoire

Pour chaque source de puissance, il existe au moins **deux** façons d'y accéder, dont
une qui ne demande pas de combattre.

| Ce qu'on veut | Voie combattante | Voie non combattante |
|---|---|---|
| Éclats | Contrats de kill, Autels | Contrat de survie, occurrences |
| Mana Brut | Le récolter et le convoyer | L'acheter à un autre joueur |
| Montures | — | Les quêtes, uniquement |
| Sorts | Butin de boss (Tomes) | Apprentissage, enseignement entre joueurs |
| Équipement | Butin | Forge, hôtel des ventes |

Quand une ligne de ce tableau perd sa colonne de droite, l'équilibre est cassé même
si les chiffres sont bons.

### 1.2. Aucun avantage ne doit s'acheter

Voir [Économie](economie.md#5-les-gemmes). La frontière : **enregistrer ce qu'on peut
déjà faire** est du confort, **pouvoir faire plus** est un avantage.

Ce principe ne se négocie pas, y compris quand un achat rapporterait beaucoup.

### 1.3. Une avance ne doit pas devenir une domination

Un Coven en tête doit rester rattrapable. Les mécanismes en place :

| Garde-fou | Ce qu'il empêche |
|---|---|
| Plafond de bonus par type d'Autel | Tenir cinq Autels de Feu ne donne pas cinq fois le bonus |
| Plafond global d'Autels contribuant aux bonus | Tenir les sept ne donne pas sept fois |
| Coût croissant des niveaux de Nexus | Les derniers niveaux absorbent tout |
| Reset d'Ère | Remet les courbes collectives à égalité tous les 45 jours |
| Cataclysme | Trois jours où un retard se comble |
| Panthéon sans effet mécanique | Une victoire passée ne donne pas d'avance présente |

### 1.4. Le joueur doit comprendre ce qui lui arrive

Un adversaire doit annoncer son geste. Une capture d'Autel s'annonce au serveur. Un
pouvoir de Coven se voit de l'extérieur. Un convoi chargé porte une pastille visible.

Un dégât sans télégraphe n'est pas une difficulté, c'est un défaut. Ce principe a un
coût technique assumé : les gestes des créatures sont synchronisés sur le compteur du
serveur précisément pour que le geste et les dégâts coïncident.

---

## 2. Les garde-fous déjà en place

Inventaire de ce qui protège l'équilibre aujourd'hui. Chacun a été ajouté après un
problème réel ; aucun n'est décoratif.

| Garde-fou | Domaine | Ce qu'il a corrigé |
|---|---|---|
| Régénération d'Essence lente, avec délai après combat | Magie | Une régénération rapide rendait les fioles inutiles |
| Récupération globale après chaque sort | Magie | L'enchaînement de deux sorts dans le même instant |
| Incantation obligatoire sur tout sort | Magie | Un sort instantané n'est pas esquivable |
| Remboursement partiel d'Essence à l'interruption | Magie | Sinon interrompre un lanceur le ruinait sans contrepartie |
| Portée d'attaque mesurée de surface à surface | Créatures | Les grosses créatures touchaient à distance de leur demi-largeur |
| Plafond de groupe par espèce | Créatures | Des troupes ingérables |
| Poids d'apparition nul pour les boss | Créatures | Un boss croisé par hasard n'en est plus un |
| Dragons qui se posent pour frapper | Créatures | Un dragon resté en l'air est invincible au sol |
| Butin au point d'impact pour un vol abattu | Créatures | Un butin inatteignable à trente blocs de hauteur |
| Plafond de Poussière d'Étoile par jour | Économie | Le farm transforme un cosmétique en travail |
| Plafond de cadeaux par jour | Économie | Un compte qui alimente les autres |
| Plafonds d'achats de confort | Boutique | L'accumulation illimitée devient un avantage |
| Recharge sur `/workbench` et `/enderchest` | Boutique | Un accès permanent vaut un avantage tactique |
| Recharge longue des pouvoirs de Coven | Covens | Un pouvoir disponible en continu n'est plus une décision |
| Canalisation pour détruire un Nexus ennemi | Covens | Une destruction instantanée ne se défend pas |
| Recharge globale après destruction d'un Nexus | Covens | Le harcèlement d'un même Coven |
| Modificateurs de thème légers seulement | Ères | Un thème généreux rendrait les autres Ères dérisoires |
| Magie interdite dans certaines régions, **au lancement et à l'impact** | Magie | Sans la seconde vérification, on arrose une arène depuis le bord |

La dernière ligne est le modèle du bon garde-fou : il ferme la porte **et** la
fenêtre. Un garde-fou qui ne vérifie qu'un côté se contourne en une journée.

---

## 3. Les déséquilibres connus

Dits franchement. Un déséquilibre écrit est un déséquilibre qu'on peut corriger ;
non écrit, il devient une habitude.

| Déséquilibre | Effet | Piste |
|---|---|---|
| **Le chef de Coven n'a aucune progression propre** | Le rôle le plus lourd est le moins récompensé | Un axe de progression lié à la gestion, sans avantage de combat |
| **Le bâtisseur n'a aucune source d'Éclats propre** | Il dépend des autres | Des contrats de construction, ou des déblocages qui récompensent la ville |
| **Les Éclats n'ont qu'un seul puits** | Un Coven au plafond de Nexus accumule sans emploi | Un second puits, ou un plafond atteignable en fin d'Ère seulement |
| **Le marchand dépend entièrement du Forgeron** | Un seul réglage peut supprimer un profil | Un second débouché du Mana Brut |
| **Aucun tableau de bord** | Les indicateurs d'économie se relèvent à la main | Des relevés automatisés |

---

## 4. Arbitrer un déséquilibre signalé

La marche à suivre, dans l'ordre. Elle existe parce que la tentation constante est de
corriger le symptôme.

### Étape 1 — Établir le fait

Une plainte n'est pas un déséquilibre. Avant de toucher à une valeur :

- **Qui se plaint, et de quoi exactement ?** « Les gelées sont trop fortes » et « la
  Gelée de lave touche alors qu'elle est loin » ne mènent pas au même correctif.
- **Est-ce reproductible ?** Un joueur qui meurt trois fois contre un boss n'est pas
  une donnée.
- **Est-ce un déséquilibre ou un défaut ?** Une créature qui frappe à travers un mur
  est un bug. Une créature qui frappe trop fort est un réglage. Les deux se corrigent
  différemment.

### Étape 2 — Trouver la cause, pas le symptôme

L'exemple de référence : des espèces étaient « imbattables car elles frappaient de
très loin ». Le réflexe aurait été de baisser leur portée. La cause réelle était que
la portée se mesurait **de centre à centre**, si bien que la demi-largeur d'une
grosse créature comptait dans la distance. Baisser la portée aurait corrigé ces
espèces et laissé le défaut pour toutes les suivantes.

Le piège inverse existe aussi : passer à une mesure de surface à surface sans rien
d'autre aurait rendu les grosses créatures **plus** généreuses, leur demi-largeur
passant de « comptée dedans » à « bonus en plus ». Il a fallu convertir les 116
valeurs du catalogue pour que chaque attaque perde exactement le même bloc et
qu'aucune n'y gagne.

**La leçon :** un correctif de mécanique demande de vérifier ce qu'il fait aux
valeurs existantes, une par une.

### Étape 3 — Choisir le levier

| Type de problème | Levier à préférer | Levier à éviter |
|---|---|---|
| Une voie est trop rentable | Renforcer les autres | Affaiblir celle-là |
| Un contenu est trop dur | Ajouter une voie d'accès | Baisser la difficulté |
| Une monnaie s'accumule | Ajouter un puits | Baisser la source |
| Une matière inonde le marché | Ajouter un usage | Baisser le taux de butin |
| Un achat ressemble à un avantage | Le retirer | Le plafonner |

La colonne de droite punit le joueur pour un problème de conception. La colonne de
gauche coûte plus cher à développer et vaut mieux à tous les coups — sauf la dernière
ligne, où il n'y a rien à négocier.

### Étape 4 — Mesurer avant, pas après

Noter les valeurs actuelles avant de changer quoi que ce soit. Sans relevé
préalable, on ne saura pas si le correctif a marché, et le suivant se fera au hasard.

### Étape 5 — Un seul changement à la fois

Deux réglages modifiés en même temps donnent un résultat qu'on ne sait pas attribuer.
Si l'urgence impose les deux, il faut l'écrire, et prévoir de les séparer ensuite.

### Étape 6 — Écrire la raison

Dans le fichier de configuration lui-même, à côté de la valeur. Un nombre sans
justification sera changé au hasard dans six mois.

C'est la pratique déjà en vigueur dans le projet, et elle a déjà servi : la
régénération d'Essence porte en commentaire l'histoire de ses deux baisses et la
raison de la dernière. Sans cette note, quelqu'un la remonterait en croyant rendre
service.

---

## 5. Ce qu'on ne corrige pas

| Situation | Pourquoi on ne touche à rien |
|---|---|
| Un Coven domine au jour 30 | Le Cataclysme et le reset existent pour ça. Intervenir priverait les autres de leur rattrapage. |
| Un joueur très bon gagne tous ses combats | La compétence n'est pas un déséquilibre. |
| Une espèce est évitée par les joueurs | À vérifier : si elle est évitée parce qu'elle ne rapporte rien, c'est un problème de butin, pas de puissance. |
| Un joueur se plaint d'avoir perdu son Mana Brut | C'est le système qui fonctionne. |
| Un cosmétique est très demandé | Tant qu'il ne donne rien, tout va bien. |

---

## 6. La grille de décision

À appliquer devant toute proposition de réglage ou d'ajout. On s'arrête à la première
réponse qui tranche.

1. **Est-ce que ça crée un avantage payant ?** → refusé.
2. **Est-ce que ça rend une voie obligatoire ?** → il faut une seconde voie.
3. **Est-ce que ça transforme une avance en domination ?** → il faut un plafond.
4. **Est-ce que le joueur comprendra ce qui lui arrive ?** → il manque un télégraphe.
5. **Est-ce que ça tient dans une session d'une heure ?** → à découper.
6. **Est-ce qu'un client modifié peut en tirer un avantage ?** → la décision remonte
   au serveur.
7. **Est-ce que ça rend un système existant inutile ?** → voir l'exemple de l'Essence
   et des fioles.
8. **Est-ce qu'on saura l'expliquer en trois phrases ?** → sinon c'est trop compliqué
   pour le jeu aussi.

---

## À lire ensuite

- [Vision et positionnement](../01-projet/vision-et-positionnement.md) — les principes dont ceci découle
- [Économie](economie.md) — les monnaies et leurs plafonds
- [Progression et jalons](progression-et-jalons.md) — les courbes à tenir
- [Boucles de jeu](boucles-de-jeu.md) — vérifier qu'un ajout a sa place
