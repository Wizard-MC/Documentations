# Créer une monture

Ajouter une monture au catalogue. C'est le contenu le plus rapide à produire — à
condition que la bête existe déjà.

**Statut : livré.** Dix montures existent. Aucun code n'est nécessaire.

---

## 1. Avant de commencer

### Ce qu'il faut déjà avoir

| Prérequis | |
|---|---|
| **Une bête montable** dans le catalogue des bêtes | Une monture n'est pas une espèce à part : c'est une bête rendue obéissante |
| **Une apparence** parmi celles de cette bête | Ou aucune, si la bête n'en a qu'une |
| **Une quête qui la débloque** | Sinon elle ne s'obtient pas |

Si la bête n'existe pas, il faut la créer d'abord — voir
[Créer une créature](creer-une-creature.md), paragraphe 8.

### Les questions

| Question | Pourquoi |
|---|---|
| **Quelle rareté ?** | Elle ne dit que le coût d'obtention, pas la puissance |
| **Quelle vitesse ?** | Écrite monture par monture, entre 0,20 et 0,85 |
| **Vole-t-elle ?** | Trois des dix volent, et ce sont les trois plus rapides |
| **Quelle quête la débloque ?** | C'est la seule voie |

---

## 2. Les champs

| Champ | Ce qu'on y met | Obligatoire |
|---|---|---|
| `name` | Le nom affiché | oui |
| `species` | L'espèce du catalogue des bêtes | **oui** |
| `model` | L'apparence **fixe** | non, si la bête n'en a qu'une |
| `rarity` | `COMMUNE`, `RARE`, `EPIQUE` ou `LEGENDAIRE` | oui |
| `speed` | Entre 0,20 et 0,85 | oui |
| `flying` | Si elle vole | non |
| `unlock` | La clé qu'une quête accorde. Défaut : `mount.<id>` | non |
| `description` | Une phrase | non |

---

## 3. La rareté ne change rien

> **La rareté ne dit pas ce que la monture fait. Elle dit ce qu'elle a coûté à obtenir,
> et décide de sa couleur d'affichage.**

La vitesse et le vol sont écrits monture par monture. C'est délibéré : lier la vitesse à
la rareté aurait retiré le seul moyen de faire un **yak de trait** — lent et commun — ou
un **crabe pesant**.

### Ce que ça permet

| Monture | Rareté | Vitesse | Le contraste |
|---|---|---:|---|
| Yak des cimes | Commune | 0,26 | Le plus lent du jeu, et il est commun |
| Ours des glaces | Épique | 0,34 | Épique, et plus lent que le renard rare |
| Renard des braises | Rare | 0,42 | Rare, et rapide |
| Griffon | Légendaire | 0,62 | Le plus rapide |

La troisième ligne est le point : une monture rare plus rapide qu'une monture épique est
**voulue**. La rareté récompense le parcours, pas la performance.

---

## 4. Les bornes de vitesse

Entre 0,20 et 0,85, et ce n'est pas arbitraire.

| Sous 0,20 | Au-dessus de 0,85 |
|---|---|
| On dépasse sa monture à pied | On dépasse le terrain que le serveur charge devant soi : on traverse des morceaux de monde vides, et l'anti-triche renvoie en arrière |

La borne haute est une contrainte technique, pas un choix de conception. La monture la
plus rapide livrée est à 0,62 — il reste de la marge, mais la dépasser franchement
produira des retours en arrière.

---

## 5. L'apparence est fixe

C'est la différence essentielle avec une bête sauvage.

| | Bête sauvage | Monture |
|---|---|---|
| Apparence | **Tirée au sort** parmi celles de l'espèce | **Fixe** |

> Un joueur qui a mérité l'ours blanc n'accepterait pas d'en recevoir un gris un jour sur
> quatre.

### La conséquence

L'**Ours brun** et l'**Ours des glaces** sont **deux montures** de la même espèce, et non
une monture à deux apparences. Chacune a sa rareté, son déblocage, et sa description.

C'est le modèle à suivre : une nouvelle apparence d'une bête existante donne une nouvelle
monture, pas une variante.

---

## 6. Le déblocage

`unlock` vaut `mount.<id>` par défaut. **Laissez le défaut** : ça évite de l'écrire deux
fois et de se tromper une fois sur deux.

### Relier la monture à une quête

C'est la **seule** voie : la monture se déclare ici, et la quête déclare le déblocage dans
sa récompense.

| Quête livrée | Monture |
|---|---|
| *Ceux qui mènent* (trame 2) | Husky |
| *Ce qui dort dans les fanges* (trame 3) | Crabe de jade |
| *Le belvédère* (annexe) | Corbeau des augures |
| *Écailles et cendres* (annexe) | Drakelet d'or |

Six montures n'ont pas encore de quête qui les débloque. **Les relier est une tâche
ouverte** : une monture déclarée sans quête ne s'obtient pas.

### Le comportement de repli

**Sans WizardQuest, toutes les montures sont ouvertes.** C'est un repli, pas une
intention : un serveur sans quêtes ne doit pas priver ses joueurs de montures.

---

## 7. Ce qu'une monture ne peut jamais faire

| Jamais | Pourquoi |
|---|---|
| S'acheter | Les montures viennent des quêtes, jamais de la boutique |
| Venir d'un classement | Un joueur seul doit pouvoir obtenir les dix |
| Donner un avantage de combat | Elle transporte, elle ne se bat pas |
| Changer d'apparence | Voir le paragraphe 5 |
| Dépasser 0,85 de vitesse | Contrainte technique |

---

## 8. Écrire la description

Une phrase. Le registre du [lore](../02-univers/lore.md#le-ton) : sobre, et un peu sec.

Les descriptions livrées :

> « Endurant, bruyant, et content de vous voir. » — le Husky
>
> « Lent, mais rien ne le fait reculer. » — le Yak des cimes
>
> « Il marche de travers. On s'y fait. » — le Crabe de jade
>
> « Vif au point qu'on le perd de vue en descendant. » — le Renard des braises
>
> « Plus vieux que les cols qu'il traverse. » — l'Ours des glaces
>
> « Il sait où vous allez avant vous. » — le Corbeau des augures
>
> « Le premier de sa portée. Il s'en souvient. » — le Drakelet d'or
>
> « Ni tout à fait aigle, ni tout à fait lion, et fier des deux. » — le Griffon

Chacune dit quelque chose de la monture sans énoncer ses statistiques. C'est l'exercice :
**faire sentir la vitesse ou la lourdeur sans donner le chiffre.**

---

## 9. Essayer

| À vérifier | Comment |
|---|---|
| Elle apparaît dans la liste | `/monture list` |
| Elle s'appelle | `/monture <nom>`, ou la touche **M** |
| Son apparence est la bonne | La regarder |
| Sa vitesse est crédible | La monter, comparer à pied |
| Si elle vole, elle vole | — |
| Elle se renvoie | `/monture dismiss` |
| Le déblocage fonctionne | Faire la quête avec un compte neuf |
| Sa boîte de collision ne la coince pas | La faire passer dans un couloir |

### Le point à ne pas manquer

La boîte de collision vient de la **bête**, pas de la monture. Si la bête a une boîte
large — le griffon fait deux blocs et demi sur trois — la monture se coincera dans les
passages étroits.

Voir [WizardMobs](../05-operer/configuration/wizardmobs.md#3-beastsyml--les-bêtes-paisibles).

---

## 10. La liste complète

| # | Étape |
|---|---|
| 1 | Vérifier que la bête existe et est montable |
| 2 | Choisir l'apparence — elle sera **fixe** |
| 3 | Choisir la rareté, qui ne dit que le coût d'obtention |
| 4 | Choisir la vitesse, entre 0,20 et 0,85, indépendamment de la rareté |
| 5 | Écrire l'entrée, en laissant `unlock` par défaut |
| 6 | Écrire une description d'une phrase, dans le registre |
| 7 | **Relier à une quête** : déclarer le déblocage dans sa récompense |
| 8 | Essayer, y compris le passage dans un couloir |
| 9 | Documenter dans le [catalogue des créatures](../07-reference/catalogue-creatures.md) |
| 10 | Une branche, un commit, une *pull request* |

---

## À lire ensuite

- [WizardMobs](../05-operer/configuration/wizardmobs.md#4-mountsyml--les-montures) — chaque champ
- [Créer une quête](creer-une-quete.md) — pour le déblocage
- [Créer une créature](creer-une-creature.md) — si la bête n'existe pas
- [Quêtes et montures](../04-jouer/quetes-et-montures.md) — les dix montures livrées
