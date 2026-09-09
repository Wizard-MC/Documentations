# CDC TECHNIQUE — WizardCloud (distribution & mise à jour du client)

> Remplace la distribution par archive unique décrite dans `cdc_exp_client.md`.
> Le launcher reste maître du lancement ; WizardCloud ne fait qu'une chose :
> amener le bon contenu, vérifié, sur le disque du joueur.

---

## 1. Objectif

Distribuer le client MCP compilé et ses données, en garantissant trois choses :

1. **le joueur obtient exactement ce que le serveur a publié** — pas une version antérieure, pas un fichier corrompu, pas un fichier substitué ;
2. **il ne télécharge que ce qui a changé** ;
3. **publier est une opération atomique et vérifiée** — il doit être impossible de mettre en ligne un état incohérent.

Le troisième point est le vrai sujet. Les deux premiers sont des conséquences.

---

## 2. Pourquoi refondre plutôt que corriger

Le système actuel — une archive `.zip`, un numéro de version, une empreinte, le tout dans le `.env` du site — a produit en exploitation la panne suivante, constatée le 17/08/2026 :

| Constat | Cause |
| :--- | :--- |
| `/api/launcher/versions` annonçait `1.0.0` avec une empreinte valide | le `.env` avait été renseigné à la main |
| l'URL annoncée répondait `200` avec **zéro octet** | l'archive n'avait jamais été déposée à cet emplacement |
| le launcher ne détectait aucune nouvelle version | le numéro n'avait pas été incrémenté |
| le joueur voyait un chargement sans fin | le délai total de 30 s coupait le téléchargement de 25 Mo |

Trois de ces quatre points ont été corrigés dans le launcher. **Le quatrième ne peut pas l'être** : rien, dans le modèle actuel, ne relie le manifeste publié aux fichiers réellement présents sur l'hébergement. Le manifeste est une déclaration d'intention saisie à la main ; les fichiers sont déposés séparément. Les deux peuvent diverger, et divergeront de nouveau.

**Défauts structurels, indépendants de cet incident :**

| ID | Défaut |
| :--- | :--- |
| **D-01** | Toute correction refait télécharger l'archive entière (26 Mo) — le jar change d'un octet, le joueur paie 26 Mo. |
| **D-02** | Aucune vérification de l'installation : un fichier effacé ou corrompu n'est jamais détecté ni réparé. Seul `version.txt` compte. |
| **D-03** | Aucune reprise : une coupure à 90 % recommence à zéro. |
| **D-04** | La publication est manuelle, en plusieurs gestes non transactionnels (déposer, calculer, recopier, vider le cache). |
| **D-05** | Le retour arrière suppose de réécrire le `.env` et de vider le cache de configuration. |
| **D-06** | Le format du manifeste est décrit en PHP côté serveur et réimplémenté en Rust côté launcher : deux sources de vérité pour un seul contrat. |

`D-06` mérite qu'on s'y arrête : c'est la raison pour laquelle `platforms: []` a rendu le manifeste illisible au launcher pendant plusieurs jours sans que personne ne le voie.

---

## 3. Choix technique : Rust, monodépôt

| Composant | Nature |
| :--- | :--- |
| `wizardcloud-core` | bibliothèque — types du manifeste, empreintes, sûreté des chemins |
| `wizardcloud-sdk` | bibliothèque — client consommé par le launcher |
| `wizardcloud-server` | binaire — API HTTP et service des blobs |
| `wizardcloud-cli` | binaire — publication depuis un poste ou la CI |
| `dashboard/` | interface web, servie par le serveur |

**Rust plutôt que Laravel**, pour une seule raison qui prime sur toutes les autres : le format du manifeste doit être **écrit une fois**. `wizardcloud-core` est compilé à la fois dans le serveur qui produit le manifeste et dans le SDK que le launcher utilise pour le lire. Un changement de format qui casserait le launcher ne compile pas. C'est `D-06` réglé par construction, et non par discipline.

Les autres arguments suivent : le service est essentiellement du calcul d'empreintes et du transfert de fichiers ; le launcher est déjà en Rust, le SDK est donc une dépendance directe sans passerelle HTTP à maintenir en double ; l'empreinte mémoire tient sans peine à côté de nginx sur la VM.

> Le site Laravel **conserve** son rôle : comptes, jetons de session, boutique. WizardCloud ne touche pas à l'authentification des joueurs.

---

## 4. Modèle de données

### 4.1 Blobs adressés par contenu

Chaque fichier distribué est stocké sous le nom de son empreinte :

```
blobs/58/3f/583faa5f1e750de7295ba62e1522b5f95af693959683bc6ba309fcb2dedce0ed
```

Conséquences directes :

- **déduplication** — un jar inchangé entre deux versions n'est stocké et transféré qu'une fois ;
- **cache trivialement sûr** — un blob ne change jamais de contenu, donc `Cache-Control: immutable` sans risque ;
- **substitution impossible** — remplacer un blob invalide sa propre empreinte, donc son nom.

### 4.2 Manifeste de version

```json
{
  "schema": 1,
  "channel": "stable",
  "version": "1.4.2",
  "published_at": "2026-08-17T14:03:00Z",
  "minecraft": "1.7.10",
  "java": "8",
  "vanilla_jar": false,
  "entry": {
    "main_class": "net.minecraft.client.main.Main",
    "tweak_class": null,
    "asset_index": "1.7.10",
    "libraries_dirs": ["libraries", "lib"],
    "natives_dir": "natives",
    "client_jar": "wizardmc-client.jar",
    "jvm_args": [],
    "game_args": []
  },
  "files": [
    {
      "path": "wizardmc-client.jar",
      "size": 26771419,
      "sha256": "583faa…",
      "executable": false
    }
  ]
}
```

`entry` reprend le `client.json` actuel : le profil de lancement reste **fourni avec le client**, jamais codé dans le launcher.

L'URL d'un fichier n'est pas stockée : elle se déduit de `sha256`. Un manifeste ne peut donc pas désigner un emplacement qui n'existe pas — `D-04` et l'incident du 17/08 disparaissent avec le champ.

### 4.3 Canaux

Un canal est un **pointeur nommé** vers un manifeste.

| Canal | Usage |
| :--- | :--- |
| `stable` | tous les joueurs |
| `beta` | testeurs, `Ctrl` au démarrage ou réglage dédié |

Publier = déposer les blobs, puis déplacer le pointeur. Revenir en arrière = redéplacer le pointeur. Les deux sont instantanés et réversibles (`D-05`).

---

## 5. Règles métier

| ID | Règle |
| :--- | :--- |
| **WC-01** | Un manifeste est **signé Ed25519**. Le launcher refuse tout manifeste dont la signature est invalide ou absente — il n'existe pas de mode dégradé. |
| **WC-02** | La signature portant les empreintes de tous les fichiers, l'hébergement des blobs **n'a pas à être de confiance**. |
| **WC-03** | Chaque fichier téléchargé est vérifié par SHA-256 **avant** d'être posé dans le dossier client. |
| **WC-04** | Un chemin de fichier est refusé s'il est absolu, contient `..`, ou sort du dossier client une fois résolu. |
| **WC-05** | La publication est atomique : le pointeur de canal n'est déplacé qu'après vérification que **tous** les blobs référencés sont présents et intègres côté serveur. |
| **WC-06** | Le launcher ne télécharge que les fichiers dont l'empreinte locale diffère. Un fichier absent ou corrompu est **réparé**, pas ignoré (`D-02`). |
| **WC-07** | Le nettoyage porte **uniquement sur ce que WizardCloud a lui-même posé**, d'après l'état local — jamais sur le contenu du dossier. S'en remettre au dossier emporterait `libraries/`, `natives/` et `versions/`, soit les cent sept mégaoctets que le launcher tire de chez Mojang, à retélécharger à chaque publication. Une liste d'exclusion protège en outre les données du joueur (`options.txt`, `saves/`, `screenshots/`, `resourcepacks/`, `shaderpacks/`) et, par sécurité, les dossiers gérés par le launcher. |
| **WC-08** | Les téléchargements reprennent via `Range` (`D-03`). |
| **WC-09** | Un manifeste déclarant plus de 10 000 fichiers ou plus de 4 Gio cumulés est refusé. |
| **WC-10** | La publication exige un jeton porté par l'en-tête `Authorization`, **cantonné à un canal**. Un jeton `beta` ne peut pas publier sur `stable`. |
| **WC-11** | Les jetons sont tirés au sort sur 256 bits et stockés hachés en SHA-256. Une fonction lente type Argon2 protège d'une attaque par dictionnaire sur un secret devinable, ce qu'un tirage aléatoire n'est pas ; elle ne ferait ici que ralentir chaque publication. Le secret n'est affiché qu'à la création. |
| **WC-12** | Toute publication est journalisée : canal, version, auteur, empreinte du manifeste, horodatage. Le journal est en ajout seul. |
| **WC-13** | Les blobs ne sont jamais supprimés par une publication. Le ramassage se fait par une commande distincte, avec période de rétention. |
| **WC-14** | HTTPS exclusivement. Aucun repli en clair, y compris sur redirection. |

---

## 6. API

### 6.1 Publique — consommée par le launcher

| Méthode | Route | Rôle |
| :--- | :--- | :--- |
| `GET` | `/v1/channels/{canal}` | manifeste courant + signature détachée |
| `GET` | `/v1/manifests/{sha256}` | manifeste par empreinte (retour arrière, diagnostic) |
| `GET` | `/blobs/{aa}/{bb}/{sha256}` | contenu d'un fichier, `Range` accepté |

`GET /v1/channels/stable` :

```json
{
  "manifest": { "...": "voir §4.2" },
  "manifest_sha256": "9c1f…",
  "signature": "base64(Ed25519 sur les octets canoniques du manifeste)"
}
```

> La signature porte sur la **sérialisation canonique** du manifeste (JSON trié, sans espaces), et non sur le corps de la réponse : une reformulation par un intermédiaire ne doit pas invalider une signature légitime, ni une signature légitime couvrir un contenu reformulé.

### 6.2 Authentifiée — publication

| Méthode | Route | Rôle |
| :--- | :--- | :--- |
| `POST` | `/v1/blobs/{sha256}` | dépose un blob (corps brut, empreinte vérifiée contre celle de l'URL) |
| `HEAD` | `/v1/blobs/{sha256}` | le blob est-il déjà présent ? évite de renvoyer l'existant |
| `POST` | `/v1/channels/{canal}/publish` | publie un manifeste et déplace le pointeur |
| `GET` | `/v1/channels/{canal}/history` | historique des publications |
| `POST` | `/v1/channels/{canal}/rollback` | repointe sur une publication antérieure |

> **L'authentification précède l'analyse du corps.** Un cadriciel qui décode le
> corps avant d'exécuter le gestionnaire répondrait « 422 » en décrivant le
> schéma attendu à un appelant qui n'a présenté aucun jeton. Le corps est donc
> reçu brut et analysé une fois le porteur reconnu.

---

## 7. Algorithme de synchronisation (SDK)

```
1. GET /v1/channels/{canal}
2. Vérifier la signature Ed25519.           → échec : arrêt, rien n'est touché
3. Valider tous les chemins (WC-04, WC-09). → échec : arrêt
4. Pour chaque fichier du manifeste :
     empreinte locale connue (cache) et à jour ?  → rien à faire
     sinon calculer le SHA-256 du fichier local
     identique ?                                  → rien à faire
     sinon → à télécharger
5. Télécharger les blobs manquants, en parallèle borné (8),
   avec reprise Range, vers <fichier>.part
6. Vérifier chaque .part par SHA-256.       → échec : rejet, pas d'installation
7. Renommer les .part en place.
8. Retirer les fichiers hors manifeste (WC-07).
9. Écrire l'état local (version, empreinte du manifeste, cache).
```

**Cache d'empreintes locales.** Recalculer le SHA-256 de 26 Mo à chaque démarrage est inutile : le cache retient `(chemin, taille, date de modification) → sha256` et n'invalide que si taille ou date changent. Le cache n'est jamais une preuve — il n'évite qu'un recalcul, et l'étape 6 vérifie toujours ce qui vient d'être écrit.

> La date est retenue **à la nanoseconde**. À la seconde, un fichier corrompu juste après son installation garderait taille et date inchangées : le cache le donnerait pour intact, et il le resterait à chaque démarrage sans que rien ne le signale.

**Reprise.** Un `.part` conservé entre deux lancements reprend au bon offset. Sa validité n'est jamais présumée : l'empreinte est vérifiée sur le fichier complet.

---

## 8. Publication

```bash
export WIZARDCLOUD_TOKEN=…

wizardcloud publish \
  --server https://cloud.wizardmc.fr/wizardcloud \
  --channel stable \
  --version 1.4.2 \
  --from ./dist/client \
  --entry ./client.json \
  --key ~/.wizardcloud/publish.pem
```

Le jeton passe par l'environnement plutôt que par la ligne de commande : un argument figure dans l'historique du shell et dans la table des processus, visible de tout compte de la machine.

Déroulement :

1. parcourt `--from`, calcule les empreintes, compose le manifeste ;
2. `HEAD` chaque blob — ne téléverse que les absents ;
3. téléverse les blobs manquants ;
4. signe le manifeste **localement**, avec la clé privée qui ne quitte jamais le poste ;
5. `POST /v1/channels/stable` — le serveur revérifie la présence et l'intégrité de chaque blob avant de déplacer le pointeur.

Si l'étape 5 échoue, **rien n'a bougé** : les blobs déposés sont inertes tant qu'aucun manifeste ne les référence.

---

## 9. Dashboard

Interface minimale, servie en statique par le serveur, cohérente avec la charte du launcher (`#0A0A12`, `#D4AF37`, `#9B59B6`, Cinzel).

| Écran | Contenu |
| :--- | :--- |
| Canaux | version courante, date, taille, nombre de fichiers, bouton de retour arrière |
| Historique | publications, auteur, empreinte, différentiel avec la précédente |
| Fichiers | arborescence de la version courante, taille, empreinte |
| Jetons | création, portée, révocation |
| Santé | espace disque, nombre de blobs, blobs orphelins |

Le dashboard **n'expose aucune écriture qui ne passe pas par l'API publique** : il n'est qu'un client de plus.

---

## 10. Intégration au launcher

`wizardcloud-sdk` remplace `launcher::update::ensure_client`. Ce qui ne change pas :

- `vanilla::ensure_vanilla` continue de tirer bibliothèques, natives et ressources chez Mojang ;
- `minecraft::ClientProfile` continue de décrire le lancement, alimenté par `entry` au lieu de `client.json` ;
- l'authentification et la session de jeu restent au site Laravel.

**Transition.** Le launcher accepte les deux formats pendant une version : si le site annonce une URL WizardCloud, il l'emprunte ; sinon il retombe sur l'archive `.zip`. Le repli est retiré une fois tous les joueurs migrés.

---

## 11. Sécurité — résumé des propriétés

| Menace | Parade |
| :--- | :--- |
| CDN compromis, blob substitué | empreinte SHA-256 signée dans le manifeste (`WC-02`, `WC-03`) |
| Manifeste falsifié | signature Ed25519 vérifiée avant toute écriture (`WC-01`) |
| Rejeu d'une version vulnérable | `published_at` et historique signé ; le launcher refuse un manifeste antérieur à celui installé, sauf retour arrière explicite |
| Traversée de répertoire | validation des chemins (`WC-04`) |
| Épuisement disque | plafonds (`WC-09`) |
| Jeton de publication volé | portée par canal (`WC-10`), stockage haché (`WC-11`), révocation immédiate, journal en ajout seul (`WC-12`) |
| Interception réseau | HTTPS strict (`WC-14`) |
| Publication incohérente | atomicité (`WC-05`) |

La clé privée de signature **ne réside jamais sur le serveur**. Compromettre WizardCloud permet de servir des fichiers ; cela ne permet pas de les faire accepter.

---

## 12. Packages

```
wizardcloud/
├── crates/
│   ├── wizardcloud-core/
│   │   ├── manifest.rs      Manifest, FileEntry, Entry, sérialisation canonique
│   │   ├── hash.rs          SHA-256 en flux, disposition des blobs
│   │   ├── path.rs          validation des chemins (WC-04)
│   │   └── signature.rs     signature et vérification Ed25519
│   ├── wizardcloud-sdk/
│   │   ├── client.rs        accès HTTP, reprise Range
│   │   ├── sync.rs          algorithme du §7
│   │   └── state.rs         état local et cache d'empreintes
│   ├── wizardcloud-server/
│   │   ├── routes/          §6
│   │   ├── storage/         dépôt de blobs, pointeurs de canaux
│   │   └── auth/            jetons, portées, journal
│   └── wizardcloud-cli/
└── dashboard/
```

---

## 13. QA

- [ ] un manifeste dont la signature est invalide ne pose **aucun** fichier
- [ ] un blob substitué en cours de transfert est rejeté par son empreinte
- [ ] un fichier effacé à la main est réparé au lancement suivant
- [ ] un fichier corrompu à la main est réparé au lancement suivant
- [ ] un mod retiré du manifeste disparaît du disque
- [ ] `saves/` et `options.txt` survivent à une mise à jour
- [ ] un chemin `../` dans un manifeste est refusé
- [ ] une coupure à 90 % reprend au lieu de recommencer
- [ ] republier un contenu différent sous la même version est détecté
- [ ] un jeton `beta` ne publie pas sur `stable`
- [ ] une publication dont un blob manque ne déplace pas le pointeur
- [ ] le retour arrière remet les joueurs sur la version antérieure au lancement suivant
- [ ] une version inchangée ne provoque aucun téléchargement
- [ ] `wizardcloud-core` compile à l'identique dans le serveur et dans le launcher

---

## 14. Étapes

| Phase | Contenu |
| :--- | :--- |
| **C0** | `wizardcloud-core` : manifeste, empreintes, chemins, signature — **fait** |
| **C1** | `wizardcloud-server` : blobs, canaux, publication atomique, jetons — **fait** |
| **C2** | `wizardcloud-cli` : `keygen`, `token`, `publish`, `rollback`, `history` — **fait** |
| **C3** | `wizardcloud-sdk` : synchronisation, reprise, réparation — **fait** |
| **C4** | Intégration launcher, avec repli sur l'archive `.zip` — **fait** |
| **C5** | Dashboard — **fait** |
| **C6** | Migration du client 1.0.0, retrait du repli |
