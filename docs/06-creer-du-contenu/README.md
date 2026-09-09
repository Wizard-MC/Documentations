# Créer du contenu

Ce qu'on peut ajouter à WizardMC sans écrire une ligne de code, ce qui demande un
développeur, et la chaîne complète pour chaque cas.

---

## 1. Qui peut ajouter quoi

| Contenu | Compétence requise | Faut-il un développeur ? |
|---|---|---|
| **Une quête** | Éditer un fichier YAML | non |
| **Une monture** | Idem | non |
| **Un sort** | Idem, plus comprendre les effets disponibles | non |
| **Un Autel, un point de Forgeron** | Éditer un YAML, connaître la carte | non |
| **Une entrée de boutique** | Idem | non |
| **Un palier de Pass d'Ère** | Idem | non |
| **Une créature** | YAML **plus** un modèle Blockbench **plus** lancer un générateur | non, mais il faut un modeleur |
| **Une bête paisible** | Idem | non |
| **Un effet de sort inédit** | — | **oui** |
| **Un type d'attaque de créature** | — | **oui** |
| **Un type d'objectif de quête** | — | **oui** |
| **Une école de magie** | — | **non, et ça ne se fait pas** : les neuf sont établies |

### La règle générale

> **Ce qui se décrit dans un fichier existant s'ajoute sans développeur. Ce qui demande
> un comportement nouveau demande du code.**

Un sort qui compose des effets déjà disponibles s'écrit en configuration. Un sort qui
fait quelque chose qu'aucun effet ne sait faire demande un développeur.

---

## 2. Les procédures

| Je veux… | Procédure |
|---|---|
| Ajouter une créature hostile ou une bête | [Créer une créature](creer-une-creature.md) |
| Ajouter un sort | [Créer un sort](creer-un-sort.md) |
| Écrire une quête | [Créer une quête](creer-une-quete.md) |
| Ajouter une monture | [Créer une monture](creer-une-monture.md) |
| Préparer un modèle 3D | [Pipeline Blockbench](pipeline-bbmodel.md) |
| Nommer correctement quelque chose | [Conventions et nommage](conventions-et-nommage.md) |

---

## 3. Ce qu'il faut avoir lu avant

Trois documents, et ce n'est pas une formalité : chacun contient une règle qu'on ne
devine pas et qui fait perdre une journée.

| Document | La règle qu'on y trouve |
|---|---|
| [Pipeline Blockbench](pipeline-bbmodel.md) | Le cube `hitbox`, les noms de clips que le moteur reconnaît, et les faces sans texture |
| [Plages d'identifiants](../07-reference/plages-d-identifiants.md) | Ce qui est déjà pris. Une collision ne produit pas d'erreur, elle produit un paquet lu de travers. |
| [Équilibrage](../03-gdd/equilibrage.md) | La grille de décision à passer avant d'ajouter quoi que ce soit |

---

## 4. Les trois règles qui s'appliquent à tout

### 4.1. Le serveur décide, le client montre

Aucune mécanique ne doit dépendre du client pour être **équitable**. Quand une valeur
existe des deux côtés — une boîte de collision, une taille, une durée — c'est le serveur
qui la fixe et le client qui s'y aligne.

Deux valeurs écrites à la main de chaque côté finissent toujours par diverger, et la
divergence ne se voit pas dans un journal : elle se voit quand les coups passent à
travers une créature.

### 4.2. Écrire la raison

Une valeur sans justification sera changée au hasard dans six mois par quelqu'un qui ne
saura pas pourquoi elle valait ça.

C'est la pratique en vigueur dans le projet, et elle a déjà servi : la régénération
d'Essence porte en commentaire l'histoire de ses deux baisses. Sans cette note,
quelqu'un la remonterait en croyant rendre service — et supprimerait tout l'intérêt des
fioles.

### 4.3. Lancer les contrôles

Le projet porte des contrôles automatisés qui confrontent la configuration aux fichiers
livrés :

| Contrôle | Ce qu'il vérifie |
|---|---|
| Les noms de clips | Chaque `clip` déclaré existe dans le bbmodel |
| Les boîtes de collision | Elles correspondent au cube `hitbox` du modèle |
| Les périodes de bond | `hopTicks` vaut la durée du clip |
| Les catalogues | Chaque espèce déclarée est bien chargée |
| Les quêtes | Chaque matière et chaque cible existe |

Ils ne sont pas là pour ralentir : chacun a été ajouté après un défaut réel qui avait
atteint le jeu.

---

## 5. Ce qu'on n'ajoute pas

| Jamais | Pourquoi |
|---|---|
| Une école de magie | Les neuf sont établies, et les trois traditions absorbées ne ressuscitent pas |
| Quelque chose d'achetable qui donne un avantage | Le principe n'a pas d'exception |
| Un emprunt à une œuvre protégée | Nom, texture, son, formule. Y compris en clin d'œil. |
| Un texte qui désigne un joueur comme élu | Les Terres ne doivent rien à personne |
| Un texte qui répond à une question ouverte du lore | Voir [les puissances muettes](../02-univers/pantheon-et-figures.md#7-les-puissances-muettes) |
| Une créature sans télégraphe d'attaque | Un dégât sans geste visible est un défaut |
| Un contenu qui n'a de sens qu'après dix heures d'affilée | Voir [Boucles de jeu](../03-gdd/boucles-de-jeu.md) |

---

## 6. La marche à suivre, quel que soit le contenu

1. **Vérifier que ça a une place.** Voir la grille de
   [Boucles de jeu](../03-gdd/boucles-de-jeu.md#7-vérifier-quun-ajout-a-sa-place).
2. **Réserver ce qui doit l'être.** Identifiant d'entité, d'objet, de paquet.
3. **Écrire la configuration.** Avec la raison de chaque valeur.
4. **Lancer le générateur**, si c'est une créature.
5. **Lancer les contrôles.**
6. **Essayer en jeu.** Aucun contrôle ne remplace le fait de regarder.
7. **Documenter.** Une entrée dans le catalogue de référence correspondant.
8. **Une branche, un commit par idée, une *pull request*.** Jamais sur `main`.

---

## À lire ensuite

- [Pipeline Blockbench](pipeline-bbmodel.md) — à lire avant de toucher à un modèle
- [Conventions et nommage](conventions-et-nommage.md) — les identifiants, les couleurs, les textes
- [Configuration](../05-operer/configuration/README.md) — chaque champ de chaque fichier
- [Équilibrage](../03-gdd/equilibrage.md) — la grille de décision
