# Comment lire cette documentation

Ce document dit comment la documentation est faite, pour qu'on puisse s'y fier
sans avoir à deviner. Il se lit une fois.

---

## 1. Ce que chaque section promet

| Section | Promesse | Public |
|---|---|---|
| **01 — Projet** | Ce qu'est WizardMC et comment il est construit | tout le monde |
| **02 — Univers** | La fiction : ce que le joueur doit pouvoir croire | joueurs, rédacteurs, builders |
| **03 — Conception** | L'intention derrière chaque mécanique | game designers, lead dev |
| **04 — Jouer** | Ce qu'un joueur fait, et comment il le fait | joueurs, support |
| **05 — Exploiter** | Faire tourner et animer le serveur | admins, staff, GM |
| **06 — Créer** | Ajouter du contenu sans toucher au code | créateurs, modeleurs |
| **07 — Référence** | Les valeurs exactes, sans narration | tous, en consultation |
| **90 — Spécifications** | Ce que le code doit faire et pourquoi | développeurs |

Les sections 01 à 07 **ne contiennent aucun extrait de code**. Les valeurs sont
en tableaux, les procédures en étapes numérotées. Un administrateur n'a pas à
lire du Java pour savoir ce que fait une clé de configuration.

La section 90 en contient : c'est la référence du développeur, et lui en a besoin.

---

## 2. La règle de préséance

Quand deux documents se contredisent, l'ordre est celui-ci, du plus fort au plus
faible :

1. **Le code et les fichiers de configuration livrés.** Ce qui tourne a toujours
   raison sur ce qui est écrit.
2. **Les spécifications (section 90).** Elles portent les règles numérotées
   (`N-09`, `E-06`, `C-08`…) auxquelles tout le reste renvoie.
3. **La référence (section 07).** Elle est dérivée du code ; si elle diverge, elle
   est à regénérer.
4. **Les manuels (sections 04 à 06).** Ils expliquent, ils ne décident pas.
5. **La conception (section 03) et l'univers (section 02).** Ils disent l'intention.
   Une intention non implémentée reste une intention.

Un écart entre le code et la documentation n'est jamais à contourner en silence :
c'est un défaut, soit du code, soit du document. Signalez-le.

---

## 3. Les statuts

Chaque système porte un statut en tête de son document. Il dit ce qu'on peut
attendre, pas ce qu'on espère.

| Statut | Signification |
|---|---|
| **Livré** | Implémenté, testé, en production. Le document décrit l'existant. |
| **Livré partiellement** | Le cœur tourne ; des pans annoncés manquent. Le document dit lesquels. |
| **Spécifié** | Le cahier des charges est arrêté, le code n'existe pas. |
| **Esquissé** | L'intention est posée, les règles ne le sont pas. À ne pas implémenter en l'état. |
| **Abandonné** | Conservé pour mémoire. Ne pas s'en servir. |

Un document sans statut décrit de la fiction ou de la méthode, pas une mécanique.

---

## 4. Les conventions d'écriture

**Langue.** Français. Les identifiants techniques restent dans leur langue
d'origine (`slime_lava`, `nexusShardBase`) parce que ce sont des clés, pas des mots.

**Vocabulaire joueur.** On écrit « Ère » et jamais « saison », « Coven » et jamais
« faction », « Éclat » et jamais « shard », même quand les tables de base de
données gardent l'ancien nom. Le glossaire tranche les cas douteux.

**Nombres.** Les durées sont en secondes ou en ticks selon ce que la configuration
accepte, et l'unité est toujours écrite. Vingt ticks font une seconde. Les
distances sont en blocs.

**Tableaux.** Une colonne « défaut » donne la valeur livrée. Une case vide veut
dire « pas de valeur par défaut, le champ est obligatoire » — jamais « zéro ».

**Couleurs.** Les codes de couleur Minecraft sont notés tels qu'ils s'écrivent
dans les fichiers (`&6`, `&c`). Leur signification est fixée dans les
[conventions de nommage](06-creer-du-contenu/conventions-et-nommage.md).

**Liens.** Tous relatifs. Un lien cassé est un défaut : il existe un contrôle qui
les vérifie, décrit plus bas.

---

## 5. Contribuer

### Ce qui se corrige sans demander

- Une faute, une tournure, un lien cassé.
- Une valeur de tableau qui ne correspond plus au fichier livré : on corrige le
  tableau d'après le fichier.
- Un statut périmé.

### Ce qui demande un arbitrage

- Changer une **règle numérotée** d'une spécification : c'est une décision de
  conception, elle se discute avant d'être écrite.
- Ajouter un système : il lui faut d'abord une spécification en section 90, puis
  un chapitre en section 04 et des entrées en section 07.
- Modifier l'univers (section 02) d'une façon qui contredit ce qui est déjà en
  jeu : le lore déjà lu par les joueurs ne se réécrit pas sans raison.

### La marche à suivre

1. Une branche par sujet. Jamais d'écriture directe sur `main`.
2. Un commit par idée, message en [Conventional Commits](https://www.conventionalcommits.org/fr/)
   — `docs(magie): ...`, `docs(operer): ...`.
3. Une *pull request*, même pour une correction d'une ligne. C'est la trace.
4. Avant d'ouvrir la *pull request*, vérifier que **tous les liens relatifs
   résolvent**. Un lien mort dans une documentation de référence coûte plus cher
   qu'une phrase maladroite : il apprend au lecteur à ne plus cliquer.

### La forme attendue d'un document

Un document de cette documentation :

- commence par une phrase qui dit ce qu'il règle, et pour qui ;
- porte son statut s'il décrit une mécanique ;
- **donne la raison** des valeurs qu'il fixe. « Quarante-cinq secondes » n'apprend
  rien ; « quarante-cinq secondes, parce qu'en dessous un défenseur prévenu n'a
  pas le temps d'arriver » se retient et se discute ;
- finit par les liens vers ce qu'il faut lire ensuite.

La dernière règle est la plus importante. Cette documentation existe pour qu'on
n'ait pas à demander au lead dev *pourquoi* une valeur vaut ce qu'elle vaut. Une
table de chiffres sans justification ne remplit pas ce contrat : elle déplace la
question sans y répondre.

---

## 6. Ce que cette documentation ne couvre pas

- **Le code source.** Chaque dépôt a son propre `docs/` technique : architecture
  interne, interfaces, notes d'implémentation. Voir
  [Architecture logicielle](01-projet/architecture-logicielle.md).
- **Les secrets.** Aucun mot de passe, jeton, hôte de base de données ou adresse
  d'infrastructure n'a sa place ici, même en exemple. Les fichiers de
  configuration documentés indiquent *qu'une* clé existe, jamais sa valeur réelle.
- **Les assets.** Les modèles, textures et sons vivent dans le dépôt `ASSETS` avec
  leur propre inventaire. Cette documentation dit comment s'en servir, pas ce
  qu'ils contiennent.
