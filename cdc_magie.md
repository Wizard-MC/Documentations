# CDC — Système de Magie (WizardMC)

Cahier des charges complet du système de magie : ressource, écoles,
progression, baguettes, lancement des sorts, effets, équilibrage, présentation
(VFX / audio), réseau, performance et persistance.

**État : livré.** Le système est implémenté côté serveur (`WizardCore`) et côté
client (fork MCP 1.7.10). Ce document décrit ce qui existe, les règles qui le
gouvernent, et la proposition de réalignement de la §16.

Documents liés :

- [`magie/README.md`](magie/README.md) — index des fiches par école et par sort
- [`magie/conception/vfx_attaques.md`](magie/conception/vfx_attaques.md) — conception des VFX d'attaque
- [`magie/conception/bbmodel_attaque.md`](magie/conception/bbmodel_attaque.md) — fabrication des modèles
- [`cdc_pouvoirs.md`](cdc_pouvoirs.md), [`cdc_covens.md`](cdc_covens.md), [`cdc_autels_sacres.md`](cdc_autels_sacres.md) — systèmes voisins

---

## 1. Objet et intention

WizardMC est un SMP semi-RPG. La magie y est **un métier avant d'être une
arme** : elle sert à construire, récolter, prospecter, soigner et se déplacer,
et elle sait se battre — mais sans jamais devenir la façon la plus rapide de
tuer un joueur.

Trois engagements structurent tout le reste :

1. **Le combat magique est « soft ».** Dégâts plafonnés, cooldowns longs sur
   les sorts hostiles, aucun enchaînement létal. Un mage ne bat pas un guerrier
   équipé en le brûlant ; il gagne en préparant le terrain.
2. **Rien ne s'achète.** Aucune école, aucun sort, aucune baguette n'est
   vendable en boutique. La progression passe par l'usage et par le Grimoire.
3. **Tout se voit.** Un sort lancé est visible de loin par tout le monde :
   cercle d'incantation dans le monde, son d'incantation, projectile, impact.
   Un état subi (entrave, soin, silence) se lit depuis l'extérieur. La magie
   n'est jamais une information privée.

---

## 2. Vocabulaire

| Terme | Définition |
|---|---|
| **Essence** | Ressource de lancement, propre au joueur, régénérée avec le temps |
| **École** | Famille de magie (Braises, Givre, Aurore…), progressant indépendamment |
| **Sort** | Entrée du catalogue : coût, mode, portée, effets, présentation |
| **Effet** | Brique de gameplay appliquée à la résolution (`damage_target`, `crop_boost`…) |
| **Baguette** | Objet requis pour lancer ; porte un tier et parfois une affinité d'école |
| **Grimoire** | Interface d'apprentissage des sorts non appris d'office |
| **Cast** | Session de lancement en cours, du départ à la résolution |
| **Phase VFX** | Événement de présentation annoncé au client (`CAST_START`, `IMPACT`…) |

---

## 3. Architecture

```
WizardCore (Spigot)                        Client (fork MCP 1.7.10)
─────────────────────                      ────────────────────────
MagicManager                               MagicVFXRuntime
├── EssenceService        ── packet 128 ──▶ ├── cercle d'incantation
├── SchoolProgressionService                ├── runes / particules
├── SpellLearnService                       ├── modèles bbmodel animés
├── CastService  ─────────────────────────▶ ├── projectiles + trails
│   └── CastSession                         ├── impacts / effets sur cible
├── SpellEffectRegistry                     └── audio (OGG spatialisé)
├── MagicClaimGate                          MagicRadial / HUD / Grimoire
└── MagicFxService
```

**Règle d'autorité, non négociable.**

- Le **serveur** décide de tout ce qui touche au jeu : coût, cooldown, portée,
  cible touchée, dégâts, effets appliqués, interruption.
- Le **client** décide de tout ce qui touche à la présentation : quelles
  particules, quel modèle, quelle animation, quel son, à quelle qualité.

Un client modifié peut mentir sur ce qu'il affiche ; il ne peut pas mentir sur
ce qui se passe. Aucun effet de jeu n'est déclenché par le client.

---

## 4. Essence

| Paramètre | Valeur par défaut |
|---|---|
| Réserve de base | 100 |
| Régénération hors combat | 4 / s |
| Régénération en combat | 1 / s |
| Sortie de combat | 5 s après le dernier échange |
| Bonus par niveau d'école (moyenne) | +2, plafonné à +40 |
| Bonus Bibliothèque (Nexus / Coven) | +10 |
| Remboursement sur interruption | 50 % |

La réserve maximale d'un joueur est donc `100 + min(40, 2 × niveau moyen)`,
plus 10 si son Coven dispose du flag `library`. Un mage avancé plafonne autour
de 150 — assez pour enchaîner, jamais assez pour ne plus choisir.

L'Essence est dépensée **au départ du cast**, pas à sa résolution : un sort
interrompu coûte, avec remboursement partiel. C'est ce qui rend l'interruption
intéressante à provoquer.

---

## 5. Écoles et progression

### 5.1 Les douze écoles

| Code | Nom | Vocation |
|---|---|---|
| `LIGHT` | Aurore | Lumière, soin |
| `NATURE` | Sylvanie | Cultures, récolte |
| `EARTH` | Roc | Terrain, construction, stabilité |
| `ARCANE` | Arcane | Prospection, projectile générique |
| `FROST` | Givre | Conservation, ralentissement, entrave |
| `FIRE` | Braises | Feu contrôlé, dégâts de zone |
| `STORM` | Tempête | Déplacement, poussée, foudre |
| `SHADOW` | Ombre | Furtivité, affaiblissement |
| `SPIRIT` | Esprit | Soin, purge, protection |
| `BLOOD` | Sang | Sacrifice de vie contre puissance |
| `TIME` | Chronos | Accélération, ralentissement |
| `VOID` | Vide | Résistance, silence, faille |

Chaque école progresse **séparément** : un joueur peut être niveau 6 en Roc et
niveau 0 en Ombre. Il n'y a pas de niveau global de mage.

### 5.2 XP et niveaux

| Paramètre | Valeur |
|---|---|
| XP par lancement réussi | 8 |
| XP par Essence dépensée | 0 |
| Niveau maximum | 10 |
| Bonus Bibliothèque | +10 % d'XP d'école |
| Bonus Autel Sacré lié | +5 % par palier |

Seuils cumulés par niveau : 50, 150, 300, 500, 750, 1050, 1400, 1800, 2250,
2750. Atteindre le niveau 10 dans une école demande environ 344 lancements
réussis — c'est une progression de fond, pas un palier qu'on passe en une
session.

Seul un lancement **réussi** rapporte. Un sort interrompu, bloqué par un claim
ou raté ne progresse pas — sinon l'optimum serait de spammer un sort utilitaire
dans le vide.

L'XP est gagnée dans l'école du sort lancé. Progresser dans une école demande
donc d'en jouer les sorts, pas d'en acheter le niveau.

### 5.3 Apprentissage

Douze sorts sont **appris d'office** (`autoLearn`) : `light_glimmer`,
`nature_growth`, `arcane_sense`, `frost_preserve`, `ember_torch`,
`earth_brace`, `storm_breeze`, `shadow_veil`, `spirit_soothe`, `blood_rite`,
`time_haste`, `void_ward` — un par école. Un joueur qui reçoit sa baguette de
départ a de quoi jouer immédiatement partout.

Les dix-neuf autres s'obtiennent par l'une des trois voies :

| Voie | Fonctionnement |
|---|---|
| `GRIMOIRE` | Achat contre de l'XP d'école — voie normale |
| `SCROLL` | Parchemin consommable — ignore le niveau requis et le coût |
| `ADMIN` | Commande ou récompense de quête — contournement complet |

Coût Grimoire : `20 + 15 × niveau requis + 8 × sorts déjà appris dans l'école`.
Le troisième terme est volontaire : **se spécialiser coûte de plus en plus
cher**. Un mage qui veut tout apprendre dans une école paie chaque sort plus
que le précédent, ce qui rend le touche-à-tout compétitif face au spécialiste.

Un sort ne peut être lancé que si les trois conditions sont réunies : sort
appris, niveau d'école suffisant, tier de baguette suffisant.

---

## 6. Baguettes

La baguette est l'outil obligatoire : sans elle en main, aucun sort ne part.

| Attribut | Rôle |
|---|---|
| `tier` | 1 à 3 — plafonne les sorts accessibles |
| `affinity` | École favorisée (peut être vide) |
| `essenceCostFactor` | Multiplicateur du coût |
| `cooldownFactor` | Multiplicateur du cooldown |

Progression type : `fracture_oak` (tier 1, polyvalente, donnée au premier
join) → baguette d'affinité tier 2 (−10 % coût et cooldown) → tier 3
(−15 %). L'affinité ne débloque rien : elle rend une école plus confortable.
Un mage peut jouer toutes les écoles avec une seule baguette, moins bien.

Perdre sa baguette pendant une incantation interrompt le sort
(`InterruptReason.WAND_LOST`).

---

## 7. Modes de lancement

| Mode | Comportement | Interruptible |
|---|---|---|
| `INSTANT` | Résolution immédiate | non |
| `CAST_TIME` | Incantation de N ticks, puis résolution | oui |
| `CHANNEL` | Canalisation : pulses réguliers jusqu'à la fin | oui |
| `PROJECTILE` | Départ immédiat d'un projectile, résolution à l'impact | non |

### 7.1 Cycle de vie d'un cast

```
1. Contrôles      sort appris ? niveau ? tier ? cooldown ? GCD ? Essence ?
2. Gate claim     zone autorisée pour ce sort et cette cible ?
3. Dépense        Essence débitée, cooldown et GCD armés
4. Annonce        phase CAST_START au client  ──▶  cercle + son + voix
5. Attente        CAST_TIME / CHANNEL — phases CHANNEL périodiques
6. Résolution     phase RELEASE, effets appliqués, phase IMPACT
7. Fin            phase END
```

À tout moment entre 4 et 6, une interruption coupe la séquence : phase
`INTERRUPT` ou `CANCEL`, cercle brisé côté client, 50 % de l'Essence rendue.

### 7.2 Interruption

| Cause | Déclencheur |
|---|---|
| `DAMAGE` | Le lanceur subit des dégâts |
| `MOVE` | Le lanceur s'éloigne de plus de 0,5 bloc |
| `WAND_LOST` | La baguette quitte la main |
| `QUIT` | Déconnexion |
| `CANCEL` | Annulation volontaire |
| `FORCE` | Commande d'administration |

Un sort à incantation est donc toujours une prise de risque. C'est le levier
principal d'équilibrage du combat magique : plus un sort est fort, plus son
incantation est longue, plus il est facile à couper.

### 7.3 Global cooldown

Un GCD de 10 ticks s'applique à tout lancement, quel que soit le sort. Il
empêche l'enchaînement instantané de plusieurs sorts et rend les rotations
lisibles pour l'adversaire.

---

## 8. Ciblage et gates

**Portée.** Chaque sort déclare la sienne (1 à 24 blocs). Le raycast serveur
fait foi ; le client ne participe pas à la résolution.

**Cible d'un sort à incantation.** Elle est cherchée **à la résolution**, pas
au lancement. Sortir de la ligne de visée pendant l'incantation suffit à
échapper au sort — la contre-mesure est gratuite et disponible pour tout le
monde.

**Gates claim et guerre.** `MagicClaimGate` contrôle deux familles :

- les **sorts hostiles** — refusés si la cible est protégée par un claim ou par
  l'état de guerre entre Covens ;
- les **utilitaires qui touchent le monde** (`place_torch`, `lift_block`,
  `crop_boost`, `preserve_zone`) — refusés hors des zones où le joueur a le
  droit de construire.

Un sort bloqué par un gate est refusé **avant** la dépense d'Essence. On ne
paie jamais pour un refus.

---

## 9. Effets

Un sort applique une liste ordonnée d'effets. Chaque effet est une brique
paramétrée dans `spells.yml`.

| Effet | Rôle |
|---|---|
| `damage_target` | Dégâts à la cible, plafonnés par `softCap` |
| `cone_damage` | Dégâts dans un cône devant le lanceur |
| `slow_target` / `potion_target` | Effet de potion sur la cible |
| `silence_target` | Empêche la cible de lancer des sorts |
| `frost_prison` | Immobilisation totale + cage de glace visible |
| `heal_target` / `heal_self` | Soin |
| `cleanse_debuffs` | Retire les effets négatifs |
| `potion_self` / `potion_area` | Effet de potion sur soi / sur les alliés proches |
| `fall_resist` | Absorbe une chute courte |
| `gust_push` | Pousse les entités devant le lanceur |
| `crop_boost` / `harvest_boost` | Croissance / rendement |
| `lift_block` | Aide au levage d'un bloc |
| `place_torch` | Pose une source de lumière |
| `preserve_zone` | Zone de conservation (fonte, feu) |
| `ore_sense` | Révèle brièvement les minerais proches |
| `self_hurt` / `essence_grant` | Sacrifice de vie, gain d'Essence |
| `self_message` | Retour texte au lanceur |

Ajouter un effet, c'est ajouter une implémentation de `SpellEffect` et
l'enregistrer : aucun sort n'a de code dédié dans le moteur de cast.

### 9.1 Effets d'état inventés

Deux effets ne correspondent à rien de vanilla et existent pour la lisibilité
du combat :

- **`silence_target`** — la cible ne peut plus lancer de sort pendant la durée.
  Contre-jeu direct contre un mage, sans dégâts.
- **`frost_prison`** — la cible est figée sur place, sans subir de dégâts, le
  temps annoncé. **Aucun bloc n'est posé dans le monde** : emmurer réellement
  la victime modifierait le terrain, poserait un problème de grief en zone
  claim et laisserait des blocs orphelins après un redémarrage. L'immobilisation
  est appliquée par effets de potion extrêmes, et la gangue de glace est
  affichée par le client autour de la victime, pour toute la durée. L'état est
  total à l'écran et entièrement réversible en jeu.

C'est le modèle à suivre pour les états futurs : **la lisibilité vient du
client, la mécanique reste réversible côté serveur.**

---

## 10. Règles d'équilibrage

Elles sont vérifiées automatiquement au chargement du catalogue
(`SpellBalanceRules`) : un `spells.yml` qui les enfreint est signalé.

| Règle | Valeur |
|---|---|
| Coût maximum d'un sort | 40 Essence |
| Cooldown minimum d'un sort hostile | 50 ticks |
| Cooldown minimum d'un sort hostile V2 (Sang / Chronos / Vide) | 70 ticks |
| Plafond de dégâts d'un effet | 6.0 |
| Plafond de dégâts V2 | 5.0 |
| Dégâts déclarés ≤ plafond déclaré | obligatoire |
| Un sort hostile doit porter un effet de dégâts | obligatoire |

S'y ajoute un **soft cap PvP global** de 6.0 appliqué à la résolution : quelle
que soit la configuration, un sort ne peut pas dépasser ce seuil contre un
joueur. Les écoles V2, arrivées plus tard, sont volontairement plus contraintes
que les écoles historiques.

---

## 11. Catalogue actuel — 31 sorts

Chaque sort a sa fiche détaillée dans [`magie/sorts/`](magie/sorts/).
`spells.yml` fait foi pour les valeurs.

| Sort | École | Mode | Essence | CD | Niv. | Tier | Hostile |
|---|---|---|---|---|---|---|---|
| `light_glimmer` | Aurore | Instant | 8 | 100 | 0 | 1 | non |
| `light_mend` | Aurore | Incantation | 18 | 180 | 2 | 1 | non |
| `nature_growth` | Sylvanie | Incantation | 14 | 200 | 0 | 1 | non |
| `nature_harvest` | Sylvanie | Instant | 10 | 300 | 1 | 1 | non |
| `earth_lift` | Roc | Canalisation | 16 | 120 | 1 | 1 | non |
| `earth_brace` | Roc | Instant | 10 | 200 | 0 | 1 | non |
| `arcane_sense` | Arcane | Instant | 18 | 600 | 0 | 1 | non |
| `arcane_bolt` | Arcane | Projectile | 14 | 55 | 1 | 1 | **oui** |
| `frost_preserve` | Givre | Instant | 12 | 240 | 0 | 1 | non |
| `frost_shard` | Givre | Projectile | 15 | 65 | 1 | 1 | **oui** |
| `frost_prison` | Givre | Incantation | 26 | 600 | 3 | 2 | **oui** |
| `ember_torch` | Braises | Projectile | 10 | 90 | 0 | 1 | non |
| `ember_flare` | Braises | Incantation | 22 | 140 | 2 | 2 | **oui** |
| `storm_breeze` | Tempête | Instant | 10 | 160 | 0 | 1 | non |
| `storm_gust` | Tempête | Instant | 14 | 100 | 1 | 1 | non |
| `storm_spark` | Tempête | Projectile | 14 | 55 | 1 | 2 | **oui** |
| `shadow_veil` | Ombre | Instant | 16 | 280 | 0 | 1 | non |
| `shadow_step` | Ombre | Instant | 12 | 200 | 1 | 2 | non |
| `shadow_hex` | Ombre | Projectile | 16 | 70 | 2 | 2 | **oui** |
| `spirit_soothe` | Esprit | Instant | 12 | 160 | 0 | 1 | non |
| `spirit_cleanse` | Esprit | Incantation | 18 | 220 | 1 | 1 | non |
| `spirit_ward` | Esprit | Instant | 16 | 240 | 2 | 1 | non |
| `blood_rite` | Sang | Instant | 6 | 180 | 0 | 1 | non |
| `blood_surge` | Sang | Instant | 8 | 200 | 2 | 2 | non |
| `blood_lance` | Sang | Projectile | 16 | 80 | 2 | 2 | **oui** |
| `time_haste` | Chronos | Instant | 12 | 160 | 0 | 1 | non |
| `time_slow` | Chronos | Projectile | 14 | 75 | 1 | 2 | **oui** |
| `time_warp` | Chronos | Incantation | 18 | 240 | 2 | 2 | non |
| `void_ward` | Vide | Instant | 12 | 200 | 0 | 1 | non |
| `void_rift` | Vide | Projectile | 16 | 80 | 1 | 2 | **oui** |
| `void_mute` | Vide | Projectile | 18 | 90 | 2 | 2 | **oui** |

Lecture : 11 sorts hostiles sur 31. Le catalogue reste majoritairement
utilitaire, conformément au §1.

---

## 12. Présentation

La présentation est décrite en détail dans
[`magie/conception/vfx_attaques.md`](magie/conception/vfx_attaques.md). En
résumé :

**Le cercle d'incantation** est dessiné **dans le monde, devant la baguette du
lanceur** — jamais sur l'ATH. Tous les joueurs à portée le voient. Il se trace
progressivement : le sceau apparaît, les runes s'inscrivent une à une, la lueur
centrale monte. L'inscription est étirée pour se terminer aux trois quarts de
l'incantation, quelle qu'en soit la durée : un sort de 2 s et un sort de 5 s
ont tous deux un cercle « fini » juste avant la résolution.

**Chaque sort a ses étapes** : cercle, concentration, projectile, libération,
impact, effet sur la cible, aura. Une étape absente n'est pas jouée.

**Le déterminisme passe par une graine** générée par le serveur au départ du
cast et transmise dans chaque phase. Tous les clients tirent les mêmes glyphes,
les mêmes trajectoires de particules, les mêmes variantes. Aucune particule
n'est envoyée par le réseau.

**Les modèles** sont des `.bbmodel` chargés et animés par le client. Leur
fabrication est décrite dans
[`magie/conception/bbmodel_attaque.md`](magie/conception/bbmodel_attaque.md).

---

## 13. Audio

37 sons personnalisés, tous en **OGG/Vorbis mono** — le format stéréo n'est pas
spatialisé par le moteur 1.7.10, un son stéréo se jouerait « dans la tête » du
joueur au lieu de venir du lanceur.

| Famille | Contenu |
|---|---|
| Séquence | `cast_start`, `charge_loop`, `channel_loop`, `release`, `cancel`, `interrupt` |
| Projectile | `projectile_launch`, `projectile_loop` |
| Impacts | 8 variantes, une par famille d'impact (`flame`, `ice`, `spark`, `glow`…) |
| Voix | 21 chants — 3 variantes × 7 écoles |

La **voix d'incantation** accompagne le tracé du cercle : elle démarre avec lui
et se tait à la résolution. Chaque école a son timbre. La variante jouée est
tirée de la graine du cast — deux joueurs qui lancent le même sort au même
moment n'ont pas la même voix.

Chaque son a un repli vanilla déclaré : si l'asset manque (pack de ressources
non chargé), le sort reste audible.

---

## 14. Réseau

Un seul canal : le packet **128**, avec les actions `SYNC`, `SELECT`, `CAST`,
`LEARN`, `ERROR`, `FX_PLAY`, `CAST_STATE`.

Un événement de présentation porte : phase, portée, graine, horodatage serveur,
durée en ticks, entité concernée, entité ciblée. Onze phases sont définies :

`PREPARE`, `CAST_START`, `CHARGE`, `CHANNEL`, `RELEASE`, `PROJECTILE`,
`IMPACT`, `END`, `CANCEL`, `INTERRUPT`, `FAIL`.

Quatre portées : `SELF`, `TARGET`, `OBSERVERS`, `PUBLIC` — un même sort ne
montre pas la même chose au lanceur, à la victime et aux spectateurs.

**Compatibilité.** L'extension VFX est écrite en fin de trame, précédée d'un
marqueur. Un client qui ne la connaît pas lit la trame historique et ignore la
suite ; un serveur qui ne l'émet pas laisse le client retomber sur une
présentation par défaut. Les deux sens dégradent proprement.

**Coût.** Un cast complet représente une poignée de paquets — un par phase.
Jamais un paquet par particule, jamais un paquet par frame.

---

## 15. Performance, persistance, exploitation

### 15.1 Budgets client

| Ressource | Plafond |
|---|---|
| Effets simultanés | 96 |
| Particules | 3000 (pool fixe) |
| Apparitions par tick | 320 |
| Particules vanilla par tick | 48 |

Les niveaux de détail s'appliquent par distance (32 / 64 / 128 blocs) et par
niveau de qualité (`LOW`, `MEDIUM`, `HIGH`, `ULTRA`). En combat massif la
densité baisse ; **aucun effet déjà commencé n'est supprimé** — un effet qui
disparaît en cours de route est pire qu'un effet moins dense.

### 15.2 Persistance

Profil de magie par joueur : écoles et XP, sorts appris, barre de sorts,
Essence. Stockage `JSON` ou `MySQL` selon `magicStorageType`.

### 15.3 Exploitation

| Commande | Usage |
|---|---|
| `/magic help`, `/magic info` | Aide et état du joueur |
| `/magic learn`, `/magic teach`, `/magic unlock` | Apprentissage |
| `/magic bind` | Barre de sorts |
| `/magic essence`, `/magic give`, `/magic starter` | Administration |
| `/magic reload` | Rechargement du catalogue |
| `/magic qa` | Contrôles d'intégrité (équilibrage, effets, checklist) |
| `/magicvfx debug`, `/magicvfx test <sort> <phase>` | Diagnostic client |

`/magicvfx test` rejoue la présentation d'un sort localement, sans le lancer ni
dépenser d'Essence : c'est l'outil de travail des VFX.

---

## 16. Proposition — réalignement du catalogue sur les VFX

**Constat.** Sur les douze écoles, **huit disposent aujourd'hui de VFX
dédiés** : Braises, Givre, Tempête, Roc, Aurore, Ombre, Vide, Esprit. Les
quatre autres — Sylvanie, Arcane, Sang, Chronos — retombent sur la présentation
générique. Elles sont jouables, mais elles ne sont pas *reconnaissables* : rien
à l'écran ne distingue une Lance de Sang d'un Éclair d'Arcane.

C'est un vrai problème d'identité, et il ne se règle pas en produisant douze
jeux de VFX : le lot d'assets disponible n'en couvre pas autant, et une école
qui n'a rien à montrer n'a probablement pas assez à dire.

### 16.1 Option retenue — regrouper sans rien supprimer

Ramener le système à **neuf écoles**, en réaffectant les sorts orphelins plutôt
qu'en les supprimant :

| École dissoute | Absorbée par | Sorts déplacés | Cohérence |
|---|---|---|---|
| Sylvanie | **Roc** (« Roc & Sylve ») | `nature_growth`, `nature_harvest` | Les deux écoles agissent sur le terrain et la récolte ; les VFX de Roc (pointe, vague, anneau) portent aussi bien la pousse |
| Sang | **Ombre** | `blood_rite`, `blood_surge`, `blood_lance` | Le sacrifice est une facette de l'Ombre ; l'impact critique teinté de rouge suffit à le distinguer |
| Chronos | **Vide** (« Faille ») | `time_haste`, `time_slow`, `time_warp` | Distorsion du temps et de l'espace : même famille, mêmes VFX de faille |

Arcane est **conservée** : son identité visuelle est précisément le cercle
d'incantation nu — c'est d'ailleurs de ses modèles que viennent les sceaux du
runtime. Elle devient l'école « pure magie », sans élément.

**Résultat : 9 écoles, 31 sorts, aucun contenu perdu.** Chaque école a alors
une présentation dédiée et un rôle distinct.

### 16.2 Ce que cela coûte

- **Migration de progression.** L'XP et les niveaux sont stockés par école. Il
  faut décider du report : la piste raisonnable est de créditer l'école
  d'accueil du **maximum** des deux niveaux, jamais de leur somme — sinon la
  fusion offre gratuitement un niveau élevé.
- **Sorts appris.** Aucun impact : ils sont identifiés par leur id, qui ne
  change pas.
- **Baguettes d'affinité.** Les affinités `NATURE`, `BLOOD`, `TIME` doivent
  être repointées vers l'école d'accueil, sans changer les objets déjà en main
  des joueurs.
- **Équilibrage.** Les seuils « V2 » (cooldown 70, dégâts 5.0) suivent les
  sorts déplacés, pas leur nouvelle école : `blood_lance` reste contraint comme
  aujourd'hui.

### 16.3 Option écartée

Réduire à **huit écoles et vingt-quatre sorts** en supprimant purement et
simplement les sorts orphelins donnerait un catalogue plus serré, mais
retirerait à des joueurs des sorts déjà appris et payés en XP. Le gain de
lisibilité ne justifie pas cette perte tant que le regroupement de la §16.1
atteint le même objectif visuel.

### 16.4 Décision attendue

Le regroupement touche à la progression de joueurs existants : **il n'est pas
appliqué**. Le catalogue livré reste celui de la §11, douze écoles. La §16.1
est une proposition à arbitrer.

---

## 17. Hors périmètre

- Enchantements et objets magiques autres que les baguettes
- Magie de Coven collective (rituels à plusieurs lanceurs)
- Sorts de déplacement longue distance (téléportation)
- Équilibrage PvP compétitif — le combat magique reste « soft » par conception
