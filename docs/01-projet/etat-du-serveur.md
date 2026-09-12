# État du serveur

Ce que WizardMC contient aujourd'hui, ce qu'il lui manque, et où les documents mentent.

**Mis à jour le 12 septembre 2026.** Cette page est la seule qui prétende dire l'état
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
| **Montures** | WizardMobs | 10 montures, 4 raretés, touche dédiée et ATH, **les dix accordées par une quête** |
| **Nexus & ville** | WizardCore | niveaux, Éclats, pose, destruction en guerre, flags |
| **Autels & Mana** | WizardCore | types, bonus soft, convois, Forgeron itinérant |
| **Covens** | WizardCovens | claims, rôles, relations, guerre, banque, migration Factions |
| **Ères** | WizardCore | 45 jours, phases, reset partiel, Panthéon, Colosse de l'Ère |
| **Cinématique** | WizardIntro | Aelindra, voix, sous-titres, choix d'école |
| **Compagnons** | WizardPets | 4 espèces, progression, accessoires, friandises |
| **Quêtes** | WizardQuest | 8 chapitres de trame, 10 annexes permanentes, 36 annexes hebdomadaires en rotation, quêtes chronométrées, forge IA, journal, ATH, suivi de chemin |
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

### Graves — corrigés le 11 septembre 2026

Les quatre défauts se tenaient les uns les autres, et aucun ne levait d'erreur. Ils sont
gardés ici le temps d'une ère : un défaut corrigé se relit utilement, et chacun a laissé
un contrôle derrière lui.

| # | Écart | État | Où |
|---|---|---|---|
| **E-01** | *Écailles et cendres* accordait `mount.drake` ; la monture s'appelle `drake_gold`. La quête n'ouvrait rien. | ✅ clé alignée, et WizardMobs avertit désormais en console d'une clé que personne n'accorde | [Q-01](../90-specifications/cdc_wizardquest.md#11-écarts-connus-entre-ce-document-et-le-code) |
| **E-02** | **Six montures sur dix ne s'ouvraient jamais** — aucune quête n'accordait leur clé, quand le manuel promettait les dix. | ✅ six quêtes neuves, et un contrôle du fichier livré | [Q-02](../90-specifications/cdc_wizardquest.md#11-écarts-connus-entre-ce-document-et-le-code) |
| **E-03** | Trois quêtes pointaient le Forgeron à `0, 64, 0`, où il n'est jamais : il est **itinérant** sur dix points. | ✅ le marqueur suit le personnage et se résout à l'envoi du journal | [Q-03](../90-specifications/cdc_wizardquest.md#11-écarts-connus-entre-ce-document-et-le-code) |
| **E-16** | **La maîtrise ne se comptait pas.** La source rendait zéro et personne ne la remplaçait : aucun seuil ne se franchissait, la trame s'arrêtait au chapitre 2, et **une seule monture sur dix** était obtenable. | ✅ comptée dans le journal, avec un avertissement au démarrage sur les seuils morts | [Q-06](../90-specifications/cdc_wizardquest.md#11-écarts-connus-entre-ce-document-et-le-code) |

> **E-16 est le plus grave des quatre, et c'est celui qui ne se voyait pas.** Les trois
> premiers se constataient en jouant ; celui-là rendait les deux autres invisibles, parce
> qu'aucune des quêtes concernées ne s'ouvrait.

### Graves — ouverts

Aucun à ce jour.

### À surveiller — la forge

Elle est **coupée par défaut** et n'a encore jamais tourné en production. Ce qui mérite
d'être regardé au premier lundi où elle sera allumée :

| Point | Pourquoi |
|---|---|
| le taux de refus au tamis | une forge qui trébuche toujours sur la même règle se corrige dans le brief, pas dans le code |
| la répétition d'une semaine sur l'autre | l'historique des identifiants est envoyé au modèle ; s'il se répète quand même, c'est le brief qu'il faut durcir |
| le coût par semaine | un appel par semaine, effort `high` — négligeable, mais à constater plutôt qu'à supposer |

Ce n'est pas un écart : rien ne contredit le code. C'est une fonction neuve dont personne
n'a encore vu le comportement réel.

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

### D'abord — rien de cassé

Les quatre défauts de progression (E-01, E-02, E-03, E-16) sont corrigés. **Aucun défaut
grave n'est ouvert à ce jour.**

### Ensuite — du contenu que le lore promet

1. **Les sorts des trois compagnons muets** (E-13). Le modèle est posé par Ignis :
   attaquer, se protéger, se soigner, rendre service, un coup d'éclat.
2. **Les trois écoles manquantes** (E-09) — ou retirer leurs fiches, si elles sont
   abandonnées. Douze fiches pour neuf écoles, c'est une promesse non tenue.
3. **Trancher les lieux** (E-11, E-12) : neuf des dix campements du Forgeron et les deux
   lieux de quête ne sont pas dans la géographie. Tant que ce n'est pas tranché, **aucune
   quête neuve ne peut poser d'objectif `REACH` honnête** — les six quêtes de montures
   s'en passent pour cette raison.
4. **Ouvrir d'autres donneurs que le Forgeron.** La trame va maintenant jusqu'au
   chapitre 8 et se conclut, mais les cinquante-quatre quêtes viennent du même
   personnage — y compris celles qui promettent un griffon.
5. **Étoffer géographie et Panthéon** (E-14, E-15).

### Greffons à finir ou à spécifier

6. **WizardHub** — en cours.
7. **WizardQueue** — statut à confirmer.
8. **CDC de WizardBungee**, et une fiche d'infrastructure pour Bot / Bridge / site /
   launcher — quatre briques vivantes sans spécification.
9. **Spawn & map** — l'esquisse de 60 lignes ne suffit pas à implémenter.

### Dette de référence

10. **Regénérer le catalogue des sorts** depuis `spells.yml` (E-10).
11. **Valider au chargement les clés `unlocks` des autres systèmes.** Les montures sont
    couvertes — WizardMobs interroge `WizardQuest.grantableKeys()` au démarrage. Le
    prochain système débloquable repartira de zéro.
12. **Un contrôle automatique** qui compare catalogues et documents, pour que cette page
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
