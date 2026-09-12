# Inventaire de la documentation

Où se trouve chaque document du projet, et ce qui n'est écrit nulle part.

**Mis à jour le 12 septembre 2026.** Cette page existe parce qu'une question revient :
« est-ce qu'on a documenté ça ? » — et que la réponse est souvent *oui, mais pas ici*.

> **Ce dépôt n'est pas la seule documentation du projet.** Sept autres dépôts portent leur
> propre dossier `docs/`, pour un total d'environ **onze mille lignes** qu'aucun sommaire de
> ce portail ne mentionne. Ce n'est pas un défaut d'organisation à corriger d'un coup : une
> documentation d'installation a sa place à côté du code qu'elle installe. C'en est un de ne
> pas savoir qu'elle existe.

---

## 1. Ce dépôt

120 fichiers, organisés par intention plutôt que par dépôt — voir
[`00-lire-cette-doc.md`](../00-lire-cette-doc.md).

| Dossier | Ce qu'on y cherche |
|---|---|
| `01-projet` | l'état, la vision, l'architecture, le glossaire, la feuille de route |
| `02-univers` | le lore, le panthéon, la géographie, le bestiaire |
| `03-gdd` | les boucles de jeu et le document de conception |
| `04-jouer` | les manuels joueur |
| `05-operer` | l'exploitation, les runbooks, la configuration greffon par greffon |
| `06-creer-du-contenu` | les procédures d'ajout de contenu |
| `07-reference` | les catalogues, les commandes, les permissions, les plages d'identifiants |
| `90-specifications` | les CDC techniques |

---

## 2. La documentation qui vit ailleurs

Chaque ligne ci-dessous est un document **réel**, à jour de son dépôt, et absent de ce
portail.

### MCP — le client

| Document | Lignes | Ce qu'il couvre |
|---|---:|---|
| `docs/3d-engine/README.md` | 279 | le moteur de rendu des modèles bbmodel |
| `docs/3d-engine/LEGACY_AUDIT.md` | 131 | ce que l'ancien moteur faisait, et pourquoi il a été remplacé |
| `docs/entities/IGNIS.md` | 255 | le compagnon Ignis côté client, cas de référence |
| `docs/intro/README.md` | 239 | la cinématique d'arrivée, côté client |
| `docs/magic/MAGIC_VFX_RUNTIME.md` | 459 | le moteur d'effets de sorts à l'exécution |
| `docs/magic/VFX_AUDIT.md` | 191 | l'audit des effets existants |

### WizardSpigot — le serveur

| Document | Lignes | Ce qu'il couvre |
|---|---:|---|
| `docs/WizardSpigot/AUDIT.md` | 701 | l'audit du fork, pièce par pièce |
| `docs/WizardSpigot/PERFORMANCE.md` | 407 | ce qui coûte, et ce qui a été mesuré |
| `docs/WizardSpigot/SECURITY.md` | 290 | la surface d'attaque du serveur |
| `docs/WizardSpigot/THREADING.md` | 278 | ce qui tourne sur quel fil, et ce qui n'a pas le droit |
| `docs/WizardSpigot/ARCHITECTURE.md` | 193 | l'organisation du fork |
| `docs/WizardSpigot/VFX_INTEGRATION.md` | 177 | comment les effets remontent au client |
| `docs/WizardSpigot/MAGIC_INTEGRATION.md` | 153 | comment la magie s'accroche au serveur |

### website — le site et la boutique

| Document | Lignes | Ce qu'il couvre |
|---|---:|---|
| `docs/cdc/01-CDC.md` | 329 | **le CDC du site**, que ce portail ne référence pas |
| `docs/cdc/02-SPEC-API.md` | 374 | la spécification de l'API |
| `docs/cdc/03-ARCHITECTURE.md` | 229 | l'architecture Laravel |
| `docs/cdc/04-CONFIG.md` · `05-INTEGRATION-SITE.md` · `06-ROADMAP.md` | 397 | configuration, intégration, feuille de route |
| `docs/PLAN-IMPLEMENTATION.md` | 520 | le plan de mise en œuvre |
| `docs/API-LAUNCHER.md` | 404 | l'API que le launcher consomme |
| `docs/API.md` · `API-DISCORD.md` | 570 | l'API publique, et celle du bot |
| `docs/INSTALLATION.md` · `CONFIGURATION.md` · `DEPLOIEMENT.md` · `COMMANDES.md` | 472 | l'exploitation |

### Les autres

| Dépôt | Documents | Lignes |
|---|---|---:|
| `launcher` | `docs/DEPLOIEMENT.md` | 509 |
| `WizardIntro` | `ARCHITECTURE.md`, `INSTALLATION.md`, `API.md` | 946 |
| `wizardmc-bridge` | `API.md`, `CONFIGURATION.md`, `INSTALLATION.md`, `COMMANDES.md`, `README.md` | 831 |
| `wizardbot` | `CHAT-LIVE.md`, `RUNBOOK.md` | 351 |

---

## 3. Les dépôts sans aucune documentation

Ni `docs/`, ni README utile. Ce sont les trous les plus coûteux, parce qu'un nouveau venu
n'a aucun point d'entrée.

| Dépôt | Ce qu'il porte | Ce qui existe | Ce qui manque |
|---|---|---|---|
| **WizardCore** / `wizardcore` | magie, Nexus, Autels, Mana, Ères, boutique — **le cœur du serveur** | rien, pas même un README | un README d'orientation, et une carte de ses sous-systèmes |
| **wizardquest** | les quêtes | rien | un README ; le CDC est dans ce portail |
| **wizardpets** | les compagnons | 2 lignes | un README ; le CDC est dans ce portail |
| **wizardhub** | le lobby | rien | un README ; le CDC est dans ce portail, marqué non livré |
| **wizardbungee** | le proxy Waterfall | 46 lignes | **un CDC** — voir le §4 |
| **ASSETS** | modèles, textures, sons | rien | un README disant comment le dépôt est rangé et ce qui s'y dépose |

> `WizardCore` est le cas le plus sérieux. C'est le greffon qui porte la magie, les Nexus,
> les Autels, le Mana, les Ères et la boutique, et il n'a pas une ligne d'orientation. Ses
> systèmes sont spécifiés un par un dans `90-specifications`, mais rien ne dit comment ils
> cohabitent dans un même greffon.

---

## 4. Ce qui n'est écrit nulle part

Vérifié contre le code, pas contre les intentions.

| # | Manque | Pourquoi ça coûte |
|---|---|---|
| **D-01** | **CDC de WizardBungee** | le proxy est une brique vivante de la production. Un fork de Waterfall sans spécification est un fork qu'on n'ose pas mettre à jour |
| **D-02** | **Fiche d'infrastructure** — qui tourne où, quels ports, quels secrets, quelles dépendances entre bot, bridge, site, launcher et serveurs | quatre briques vivantes sans vue d'ensemble. La connaissance est dans une seule tête |
| **D-03** | **Architecture de WizardCore** | le plus gros greffon de la stack, sans carte |
| **D-04** | `cdc_exp_client.md` est de l'ère V4 et fait 71 lignes | il décrit un HUD qui a doublé de surface depuis, et ne mentionne ni les quêtes, ni les montures, ni les compagnons |
| **D-05** | `cdc_spawn_map.md` fait 60 lignes | une esquisse ne suffit pas à implémenter |
| **D-06** | **Catalogue des sorts à regénérer** depuis `spells.yml` | 32 fiches pour 41 sorts, les deux ensembles divergent |
| **D-07** | **Procédure de publication du client** — build MCP, signature, manifeste, mise en ligne | la question a déjà été posée deux fois, et la réponse n'est nulle part |
| **D-08** | **Ce portail n'indexe pas les onze mille lignes du §2** | on redocumente ce qui existe déjà, ou on cherche longtemps |

---

## 5. Ce qui est hors d'atteinte

Le dossier `internal-docs/` du dépôt MCP — dont `internal-docs/smp` — est **ignoré par
Git** : c'est la première ligne du `.gitignore` du dépôt.

```
internal-docs/
```

Ces documents n'ont donc jamais quitté la machine sur laquelle ils sont écrits. Ils
n'existent ni sur GitHub, ni dans aucun clone, et aucun outil ne peut les lire à distance.

**Deux façons de les récupérer**, selon ce qu'ils contiennent :

| Si | Alors |
|---|---|
| rien de sensible dedans | retirer la ligne du `.gitignore`, committer le dossier, et les reprendre ici |
| des secrets, des clés, des mots de passe | les recopier **sans les secrets** : ce portail dit qu'une clé existe, jamais sa valeur |

La seconde colonne n'est pas une précaution de principe. Ce dépôt est lu par plus de monde
qu'un dossier local, et une documentation d'exploitation est exactement l'endroit où une clé
se retrouve par inadvertance.

---

## 6. Comment tenir cette page

Elle se met à jour **quand un document naît ou déménage**, pas à date fixe.

Trois questions :

1. Un dépôt a-t-il gagné ou perdu un document ?
2. Un manque du §4 est-il comblé — ou un nouveau est-il apparu ?
3. Un document du §2 mérite-t-il de monter dans ce portail ?

La troisième a une règle, et elle n'est pas « tout rapatrier » :

> **Ce qui explique comment faire tourner un dépôt reste dans ce dépôt.** Ce qui explique
> comment le serveur fonctionne monte ici. Une procédure d'installation suit son code ; une
> décision de conception appartient au projet.

---

## À lire ensuite

- [`00-lire-cette-doc.md`](../00-lire-cette-doc.md) — comment ce portail est rangé
- [`etat-du-serveur.md`](etat-du-serveur.md) — ce que le serveur contient, et ce qui est cassé
- [`architecture-logicielle.md`](architecture-logicielle.md) — les greffons et leurs liens
