# CDC — Cinématique d'arrivée « L'Invocation d'Aelindra »

Ce que voit un joueur qui arrive sur WizardMC pour la première fois, et ce que le serveur en
retient.

**État : livré (1.0.0).** Le système est implémenté côté serveur (plugin `WizardIntro`) et côté
client (fork MCP 1.7.10). Ce document décrit ce qui existe et les règles qui le gouvernent.

Documents liés :

- [`cdc_magie.md`](cdc_magie.md) — les neuf écoles, dont l'une est choisie ici
- [`cdc_exp_client.md`](cdc_exp_client.md) — client MCP, HUD, FX
- [`cdc_spawn_map.md`](cdc_spawn_map.md) — spawn et map

Documentation technique : `WizardIntro/docs/` (architecture, installation, API) et
`MCP/docs/intro/` (côté client).

---

## 1. Objet et intention

Un serveur SMP se juge dans ses cinq premières minutes. La cinématique d'arrivée occupe la
première : elle dit où le joueur est tombé, qui lui parle, et lui fait poser le seul choix qui
engage sa progression — son école de magie.

Trois engagements la structurent :

1. **Une seule fois.** Elle est jouée à la première connexion, jamais ensuite. Un joueur qui la
   revit est un bug, pas une fonctionnalité.
2. **Elle se termine toujours.** Quel que soit l'incident — client muet, joueur AFK, base
   injoignable, serveur qui s'arrête — le joueur finit au spawn, avec une voie. Personne ne reste
   dans le sanctuaire.
3. **Le serveur décide, le client montre.** Le client n'émet que deux faits : son écran est prêt,
   le joueur a cliqué. Un client modifié ne peut ni se donner une école, ni sauter l'étape.

---

## 2. Déroulement

| # | Étape | Ce qui se passe |
|---|---|---|
| 1 | Connexion | Le serveur lit l'état du joueur. Intro déjà faite → rien |
| 2 | Invocation | Téléportation au Sanctuaire des Origines, joueur immobilisé, autres occupants masqués |
| 3 | Portail | Anneau de particules dorées et violettes autour du joueur |
| 4 | Apparition | Aelindra se pose devant lui, animation `Apparition` |
| 5–9 | Cinq répliques | Voix, sous-titres, animation par réplique, runes flottantes |
| 10 | Choix | Grille des neuf écoles, avec nom, couleur et vocation |
| 11 | Enregistrement | Le choix est validé, écrit, puis annoncé au reste du serveur |
| 12 | Disparition | Animation `Disparition`, fondu |
| 13 | Retour | Téléportation au spawn principal, joueur rendu à lui-même |

Durée : environ 90 secondes, dont 81 de script parlé.

### Le script

| Réplique | Durée | Texte |
|---|---:|---|
| `intro_01_apparition` | 3,2 s | « Les Arcanes t'ont choisi, Sorcier. » |
| `intro_02_presentation` | 11,2 s | « Je suis Aelindra, l'Éveilleuse des Âmes, gardienne du Sanctuaire des Origines. » |
| `intro_03_invocation` | 17,6 s | « Tu as été invoqué dans les Terres Fracturées, là où la magie saigne encore des blessures du monde. » |
| `intro_04_role` | 16,8 s | « Tu es ici pour façonner ton destin, et peut-être celui de ces terres. » |
| `intro_05_ecole` | 30,1 s | « Mais avant de commencer ton voyage, tu dois choisir ta voie. » |

Le texte vit dans la configuration du serveur, pas dans le mod : corriger une phrase ne demande
pas de redistribuer le client.

---

## 3. Règles métier

| ID | Règle |
| :--- | :--- |
| **I-01** | La cinématique est jouée **une seule fois par joueur**, à sa première connexion |
| **I-02** | Chaque étape porte une échéance. Aucune ne peut durer indéfiniment |
| **I-03** | Sans choix dans le délai imparti, l'**école par défaut** est attribuée et l'intro est close. Sinon un client muet remettrait son joueur dans le sanctuaire à chaque connexion |
| **I-04** | Une **déconnexion** en cours de route ne coûte rien : rien n'est écrit avant le choix, l'invocation se rejoue |
| **I-05** | Une **panne** (base, monde, packet) est gouvernée par `retry-on-failure` : rejouer après une panne est raisonnable, boucler dessus ne l'est pas |
| **I-06** | L'école reçue est validée **contre la liste envoyée à cette session**, jamais contre la configuration courante |
| **I-07** | Le choix est **écrit avant d'être annoncé**. Un plugin qui ouvrirait l'école avant l'écriture donnerait une voie que le serveur oublierait |
| **I-08** | Le monde de cinématique n'est **jamais généré** : absent, la cinématique ne démarre pas et le joueur reste au spawn |
| **I-09** | Aelindra n'est **pas une entité serveur**. Chaque joueur la voit seul, le client la rend |
| **I-10** | Hors cinématique, le système ne coûte rien : ni tâche, ni écouteur de mouvement |

---

## 4. Le choix d'école

Les **neuf écoles vivantes** de [`cdc_magie.md`](cdc_magie.md) §5.1 : Braises, Givre, Tempête,
Roc, Aurore, Ombre, Vide, Esprit, Arcane.

Pas douze. Le regroupement acté en §16 a dissous Sylvanie, Sang et Chronos en *traditions* :
elles n'ont ni niveau ni sort à apprendre, et les proposer offrirait une voie sans contenu
derrière. La liste vit en configuration — si l'une d'elles revenait, une entrée suffirait, la
grille du client s'y adapte sans qu'on touche au mod.

Le choix est transmis au reste du serveur par un événement, après écriture. `WizardCore` y
branche l'ouverture de l'école dans le système de magie.

---

## 5. Architecture

```
WizardIntro (Spigot)                      Client (fork MCP 1.7.10)
────────────────────                      ────────────────────────
IntroManager                              IntroManager
├── CinematicManager   ── packet 130 ──▶  ├── écran + bandes + sous-titres
│   └── CinematicSession                  ├── grille des écoles
├── DialogueManager                       ├── Aelindra (bbmodel animé)
├── ChoiceManager  ────────────────────▶  ├── portail, runes
├── TeleportManager                       └── voix (.ogg)
├── IntroDAO (SQLite | MySQL)
└── IntroAPI + PlayerIntroCompletedEvent
```

**Packet 130**, bidirectionnel, sept actions. Premier identifiant libre après le bloc 95–129 —
`WizardPackets.FIRST_FREE_ID`. Le CDC d'origine prévoyait un plugin channel `wizardmc:intro` :
la stack n'en utilise aucun, tous ses systèmes passent par des packets NMS custom. Les
identifiants `0xE0`–`0xF0` du CDC sont conservés comme **actions** à l'intérieur du 130.

**Une modification du fork** a été nécessaire : `WizardPackets` fermait son registre avant le
chargement des plugins, ce qui le rendait inutilisable par un plugin. Le verrou tombe désormais
après le chargement des mondes. `settings.late-bind: true` devient obligatoire.

---

## 6. Persistance

Une ligne par joueur, écrite une fois, au choix.

```sql
CREATE TABLE wizard_intro_data (
    player_uuid         VARCHAR(36) NOT NULL PRIMARY KEY,
    has_completed_intro TINYINT(1)  NOT NULL DEFAULT 0,
    chosen_school       VARCHAR(32) DEFAULT NULL,
    player_name         VARCHAR(16) DEFAULT NULL,
    intro_date          TIMESTAMP   NULL,
    last_updated        TIMESTAMP   NULL
);
```

`player_name` n'a aucun rôle de jeu — l'identité est l'UUID. Il existe pour que
`/intro reset <joueur>` vise un joueur hors ligne sans passer par `Bukkit.getOfflinePlayer`, qui
en 1.7.10 interroge Mojang sur le thread principal.

Tout l'accès est asynchrone : une requête SQL sur le thread de jeu gèle le tick pour tous.

---

## 7. Assets

| Asset | État |
| :--- | :--- |
| Rig d'Aelindra + animations | livré (`MCP`, huit clips) |
| Les cinq répliques | livrées (masters `.mp3` dans `ASSETS`, `.ogg` dans le mod) |
| Particules | vanilla, aucun asset |
| Icônes des neuf écoles | **à produire** |
| Map du Sanctuaire des Origines | **absente** |

Aucun des deux manques n'empêche la cinématique de se jouer : sans icônes, les boutons se lisent
par leur couleur et leur vocation ; sans la map, la cinématique se joue dans le monde configuré.

**Le MP3 ne se joue pas en 1.7.10** — le moteur audio n'embarque que Vorbis et WAV. La conversion
est documentée dans `ASSETS/sounds/Aelindra/README.md`.

---

## 8. QA

- Première connexion → cinématique ; seconde → rien
- Déconnexion en cours de route → elle se rejoue
- Aucun clic → école par défaut attribuée
- École non proposée, chaîne arbitraire, session erronée → refusées
- `/intro reset` puis reconnexion → elle se rejoue ; `/intro skip` → elle ne se joue plus
- Monde de cinématique absent → le serveur le dit, les joueurs restent au spawn

Ces points sont automatisés (`IntroLiveScenarios`, contre un serveur réel). Ce qui ne l'est pas,
et demande une session de jeu : le rendu d'Aelindra, ses animations, les sous-titres, la grille
et les particules.
