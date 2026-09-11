# État du serveur

Ce que WizardMC contient aujourd'hui, ce qu'il lui manque, et où les documents mentent.

**Mis à jour le 11 septembre 2026.** Cette page est la seule qui prétende dire l'état
réel de l'ensemble. Elle est vérifiée contre le code, pas contre les intentions : quand
une ligne dit « livré », c'est qu'on a lu le code qui le fait.

> **À relire à chaque fin de chantier.** Une page d'état qui date est pire qu'absente :
> elle donne confiance dans du faux. La [roadmap](roadmap.md) l'a appris à ses dépens.

---

## 1. Les dix-neuf dépôts

| Dépôt | Ce que c'est | État | Sa spécification |
|---|---|---|---|
| **MCP** | Le client Minecraft 1.7.10 compilé | livré | [`cdc_exp_client`](../90-specifications/cdc_exp_client.md) — mince |
| **WizardSpigot** | Le fork serveur (entités NMS, items, paquets) | livré | doc dans son dépôt |
| **WizardCore** | Magie, Nexus, Autels, Mana, Pouvoirs, Ères, Covens | livré | 7 CDC |
| **WizardMobs** | 49 hostiles, 24 paisibles, 10 montures, 4 dragons | livré | [`cdc_mobs`](../90-specifications/cdc_mobs.md) |
| **WizardPets** | Compagnons : progression, équipement, reproduction | livré | [`cdc_compagnons`](../90-specifications/cdc_compagnons.md) |
| **WizardQuest** | 7 quêtes, journal, suivi, récompenses | livré | [`cdc_wizardquest`](../90-specifications/cdc_wizardquest.md) |
| **WizardIntro** | La cinématique d'arrivée | livré | [`cdc_intro`](../90-specifications/cdc_intro.md) |
| **WizardCovens** | Le socle social | livré | [`cdc_covens`](../90-specifications/cdc_covens.md) |
| **WizardHub** | Le lobby, repli du proxy | **en cours** — 89 classes | [`cdc_wizardhub`](../90-specifications/cdc_wizardhub.md) |
| **WizardQueue** | La file d'attente du proxy | **en cours** | [`cdc_wizardqueue`](../90-specifications/cdc_wizardqueue.md) |
| **WizardBungee** | Le proxy | livré | **aucune** |
| **Launcher** | Le launcher Rust | livré | doc dans son dépôt |
| **WizardCloud** | La distribution du client | livré | [`cdc_wizardcloud`](../90-specifications/cdc_wizardcloud.md) |
| **Website** | Le site, la boutique, l'administration | livré | **aucune** — doc dans son dépôt |
| **WizardBot** | Le bot Discord | livré | **aucune** |
| **WizardMC-Bridge** | Le pont serveur ↔ site ↔ Discord | livré | **aucune** |
| **ASSETS** | Modèles, textures, sons | livré | [`pipeline-bbmodel`](../06-creer-du-contenu/pipeline-bbmodel.md) |
| **Documentations** | Ce dépôt | livré | — |
| **Wiki-WizardMC** | Le wiki public | inconnu | **aucune** |

**Quatre dépôts vivants n'ont aucune spécification** : WizardBungee, WizardBot,
WizardMC-Bridge, Wiki-WizardMC. Le site et le launcher documentent chez eux, ce qui est
acceptable tant que cette page renvoie vers eux.

---

## 2. Les systèmes, un par un

### Livré et cohérent

| Système | Porté par | Ce qui existe |
|---|---|---|
| **Magie** | WizardCore | 9 écoles, 41 sorts, 13 baguettes, sceaux, Tomes, roue, cercle d'incantation |
| **Créatures** | WizardMobs + WizardSpigot | 49 hostiles dont 9 gelées et 4 dragons de donjon, 24 paisibles, butin, nuées |
| **Montures** | WizardMobs | 10 montures, 4 raretés, touche dédiée et ATH |
| **Nexus & ville** | WizardCore | niveaux, Éclats, pose, destruction en guerre, flags |
| **Autels & Mana** | WizardCore | types, bonus soft, convois, Forgeron itinérant |
| **Covens** | WizardCovens | claims, rôles, relations, guerre, banque, migration Factions |
| **Ères** | WizardCore | 45 jours, phases, reset partiel, Panthéon, Colosse de l'Ère |
| **Cinématique** | WizardIntro | Aelindra, voix, sous-titres, choix d'école |
| **Compagnons** | WizardPets | 4 espèces, progression, accessoires, friandises |
| **Quêtes** | WizardQuest | 7 quêtes, journal, ATH, suivi de chemin |
| **Boutique & site** | Website | offres, Stripe/PayPal, liaison Discord, administration |
| **Distribution** | Launcher + WizardCloud | manifeste signé, mise à jour différentielle |
| **Discord** | WizardBot + Bridge | liaison des comptes, chat en miroir |

### En cours

| Système | État |
|---|---|
| **WizardHub** | Le code existe et avance ; son CDC dit encore « le code n'existe pas ». Corrigé dans cette livraison. |
| **WizardQueue** | Dépôt actif, CDC complet de 541 lignes. Statut réel à confirmer par l'équipe. |

### Spécifié, non commencé

| Système | CDC | Note |
|---|---|---|
| **Spawn & map** | [`cdc_spawn_map`](../90-specifications/cdc_spawn_map.md) | 60 lignes, esquisse |

---

## 3. Les écarts entre le code et les documents

C'est la section à lire avant de croire un autre document. Chaque ligne a été vérifiée.

### Graves — un joueur le voit

| # | Écart | Où |
|---|---|---|
| **E-01** | *Écailles et cendres* accorde `mount.drake` ; la monture s'appelle `drake_gold`. **La quête n'ouvre rien.** | [Q-01](../90-specifications/cdc_wizardquest.md#11-écarts-connus-entre-ce-document-et-le-code) |
| **E-02** | **Six montures sur dix ne s'ouvrent jamais** — aucune quête n'accorde leur clé. Le manuel promet pourtant les dix. | [Q-02](../90-specifications/cdc_wizardquest.md#11-écarts-connus-entre-ce-document-et-le-code) |
| **E-03** | Trois quêtes pointent le Forgeron à `0, 64, 0`, où il n'est jamais : il est **itinérant** sur dix points. | [Q-03](../90-specifications/cdc_wizardquest.md#11-écarts-connus-entre-ce-document-et-le-code) |

### Documents qui mentent

| # | Écart | Correction |
|---|---|---|
| **E-04** | [`roadmap.md`](roadmap.md) annonce « Magie : 12 écoles, 31 sorts ». Le code en a **9 et 41**. | corrigé ici |
| **E-05** | [`roadmap.md`](roadmap.md) donne la Boutique « À faire » ; [`boutique-et-pass.md`](../04-jouer/boutique-et-pass.md) la dit « livrée ». | corrigé ici |
| **E-06** | [`roadmap.md`](roadmap.md) range les Compagnons en « Backlog design ». WizardPets est un greffon complet. | corrigé ici |
| **E-07** | [`architecture-logicielle.md`](architecture-logicielle.md) ne liste que **4 greffons** et place les compagnons dans « WizardCore (pets) ». | corrigé ici |
| **E-08** | [`cdc_wizardhub.md`](../90-specifications/cdc_wizardhub.md) dit « le code n'existe pas ». Il existe. | corrigé ici |

### Lore contre code

| # | Écart | Nature |
|---|---|---|
| **E-09** | **Trois écoles spécifiées n'existent pas** : `blood`, `nature`, `time`. Douze fiches, neuf écoles jouables. | contenu manquant |
| **E-10** | **32 fiches de sorts pour 41 sorts** : les deux ensembles divergent dans les deux sens. | référence à regénérer |
| **E-11** | Neuf des dix lieux du Forgeron nomment des régions absentes de la géographie. | lore à trancher |
| **E-12** | `mushy_mare` et `belvedere`, visés par des quêtes, ne sont pas des régions du lore. | lore à trancher |
| **E-13** | **Trois compagnons sur quatre n'ont aucun sort** (Nox, Aelindra, l'Esprit Cristallin). | contenu manquant |
| **E-14** | La géographie est « livrée partiellement » : les six régions ne sont qu'un découpage. | contenu manquant |
| **E-15** | Le Panthéon est « à étoffer » hors Aelindra et le Forgeron. | contenu manquant |

---

## 4. Ce qu'il reste à faire

Classé par ce que ça coûte au joueur, pas par ce que ça coûte à écrire.

### D'abord — des choses cassées

1. **Aligner la clé du Drake doré** (E-01). Une ligne.
2. **Résoudre le marqueur du Forgeron à l'exécution** (E-03). Trois quêtes réparées.
3. **Valider les clés `unlocks` au chargement du catalogue.** E-01 serait mort au
   démarrage au lieu de passer six mois inaperçu.
4. **Trancher les six montures fermées** (E-02) : écrire les quêtes, ou retirer la porte.

### Ensuite — du contenu que le lore promet

5. **Les sorts des trois compagnons muets** (E-13). Le modèle est posé par Ignis :
   attaquer, se protéger, se soigner, rendre service, un coup d'éclat.
6. **Les trois écoles manquantes** (E-09) — ou retirer leurs fiches, si elles sont
   abandonnées. Douze fiches pour neuf écoles, c'est une promesse non tenue.
7. **Étendre la trame** au-delà du chapitre 3, et ouvrir d'autres donneurs que le
   Forgeron.
8. **Étoffer géographie et Panthéon** (E-14, E-15), et réconcilier les lieux (E-11, E-12).

### Greffons à finir ou à spécifier

9. **WizardHub** — en cours.
10. **WizardQueue** — statut à confirmer.
11. **CDC de WizardBungee**, et une fiche d'infrastructure pour Bot / Bridge / site /
    launcher — quatre briques vivantes sans spécification.
12. **Spawn & map** — l'esquisse de 60 lignes ne suffit pas à implémenter.

### Dette de référence

13. **Regénérer le catalogue des sorts** depuis `spells.yml` (E-10).
14. **Un contrôle automatique** qui compare catalogues et documents, pour que cette page
    n'ait plus à être tenue à la main.

---

## 5. Comment tenir cette page

Elle se met à jour **quand un chantier se termine**, dans la même livraison que le
chantier. Pas après.

Trois questions, à chaque fois :

1. Un dépôt a-t-il été ajouté, ou un système changé d'état ?
2. Un écart du §3 est-il résolu — ou un nouveau est-il apparu ?
3. Le §4 reflète-t-il encore les priorités ?

La [règle de préséance](../00-lire-cette-doc.md#2-la-règle-de-préséance) s'applique ici
comme ailleurs : **le code a toujours raison**. Quand cette page et le code divergent,
c'est cette page qu'il faut corriger — et l'écart mérite d'être noté au §3 le temps
qu'il vive.
