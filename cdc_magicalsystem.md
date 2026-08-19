# CDC TECHNIQUE — Magie (Coven SMP V1+)

> **Statut** : CDC complet (**S7-T0** ✅) — **bloquant levé** pour le code S7-T1+.  
> Remplace le stub. Aligné [`gdd.md`](gdd.md) + [`ROADMAP.md`](ROADMAP.md#phase-s7--magie).  
> Inspiration UX : mods type *Arcanum* (baguette, roue de sorts, projectiles) + jauge / GCD type **WoW / FF** — **lore & noms 100 % WizardMC** (pas d’IP Harry Potter).  
> Réf. technique interne : [`../archive/arcanum-inspect/`](../archive/arcanum-inspect/) + [`notes_arcanum.md`](../archive/notes_arcanum.md).  
> Packet NMS : **128** (Wiki = **126**, Compagnons = **129**).

---

## Changelog — divergences code livré

**Août 2026 — VFX d'incantation**

| Écart | Détail |
| :--- | :--- |
| Actions packet 128 | Le §10 en liste 9. Sept sont implémentées : `SYNC`, `ERROR`, `SELECT`, `CAST`, `LEARN`, `FX_PLAY`, `CAST_STATE`. **`OPEN_GRIMOIRE` et `WAND_SYNC` n'existent pas** — le grimoire s'ouvre côté client sur l'instantané `SYNC`, et le changement de baguette est détecté localement. |
| `FX_PLAY` élargi | Le payload du §10 ne mentionne ni orientation ni durée. Il porte désormais aussi le **yaw du lanceur** et la **durée du FX en ticks** : sans yaw, aucun FX orienté n'est possible, et sans durée le client se rabattait sur des constantes (un cast de 40 ticks s'éteignait au bout de 12). Lecture client tolérante : un serveur antérieur reste compatible. |
| `CAST_STATE` diffusé | Le §10 le décrit S→C sans préciser la portée. Il est **diffusé à tous les joueurs dans 64 blocs**, lanceur inclus. Sans ça, une interruption restait invisible pour les témoins. Phases émises : `START`, `TICK`, `SUCCESS`, `INTERRUPT` (`FAIL` déclaré, non émis). |
| Cercle d'incantation | Le §11 le suppose couvert par `IncantationOverlay`, qui est un overlay **local** au lanceur. Le rendu monde est un ajout : `fx/CastCircleRenderer`, branché dans le rendu du monde. Le cercle est un **plan vertical face à la direction du cast**, non un disque au sol — c'est la forme de l'asset retenu. |
| FX de cast sur tous les modes | Le §6 laisse entendre que le wind-up est propre aux modes temporisés. Il est désormais émis aussi sur `INSTANT` et `PROJECTILE`, faute de quoi 7 des 12 sorts V1 n'avaient aucune phase visible. |
| Assets VFX | Le §11 prévoyait de recréer les textures sous `textures/wizardmc/`. Les modèles viennent du lot d'attaques animées interne, importés sous `models/magic/` : `incantation_circle`, `projectile_fireball`, `projectile_frostshard`, `impact_burst`, `impact_bolt` (une animation par fichier). Correspondance dans `fx/SpellFxManifest`. |

---

## 1. Objectif produit

Donner aux joueurs un **système de magie individuelle jouable** :

1. **Baguette en main** pour lancer des sorts (focus obligatoire).  
2. **Essence Arcane** (jauge perso, type mana MMORPG) séparée du **Mana Brut** Coven.  
3. **Incantation + cast time / channel + GCD** + barre / roue de sorts.  
4. **FX & animations client** (bras, particules, projectile, texte d’incantation).  
5. **12 écoles** progressives ; V1 = utilitaire + soft combat ; pas de oneshot.  
6. **Zéro P2W** : puissance / XP / sorts hors boutique Gemmes.

Le feeling cible : *tenir sa baguette, choisir un sort, voir la jauge baisser, lancer un rayon / projectile avec une vraie animation* — pas un simple clic droit potion.

---

## 2. Vocabulaire (critique)

| Terme | Rôle | ≠ |
| :--- | :--- | :--- |
| **Mana Brut** | Ressource **Coven** (convois, Forgeron) | [cdc_mana_brut.md](cdc_mana_brut.md) |
| **Essence Arcane** (ou *Essence*) | Pool **joueur** pour caster | Ce CDC |
| **Baguette** | Item focus de cast (NBT WizardMC) | Cosmétiques boutique = skin only |
| **Grimoire** | Progression + sorts appris | GUI livre + item optionnel |
| **École** | Branche de magie (niv 0–10) | Autel / flag `library` soft-bonus |
| **Pouvoirs Coven** | Skills **ville** (Bouclier, Cataclysme…) | [cdc_pouvoirs.md](cdc_pouvoirs.md) — orthogonal |

Messages joueur : « Essence », « baguette », « grimoire », « école ». Ne jamais confondre avec Mana Brut.

---

## 3. Règles métier

| ID | Règle |
| :--- | :--- |
| **MG-01** | Cast uniquement si **baguette WizardMC** en main principale (marker NBT/lore). |
| **MG-02** | Coût **Essence** + **cooldown sort** + **GCD global** (ex. 0,5–1,0 s, config). |
| **MG-03** | Essence : `current` / `max` ; regen hors combat + regen lente en combat ; cap augmenté par niveau d’école / flag `library`. |
| **MG-04** | Sort **appris** dans le grimoire + niveau d’école ≥ `requiredSchoolLevel` + baguette `tier` ≥ `requiredWandTier`. |
| **MG-05** | Modes de cast : `INSTANT`, `CAST_TIME` (interruptable), `CHANNEL`, `PROJECTILE`. |
| **MG-06** | Sélection sort : roue (maintenir touche) **ou** slots 1–6 hotbar magique ; sync serveur. |
| **MG-07** | Clic droit baguette = lancer le sort sélectionné (si pas GUI ouverte). |
| **MG-08** | Annulation cast : dégâts / move (selon `interruptible`) → 50 % Essence remboursée (config). |
| **MG-09** | Soft-cap PvP : dégâts magiques plafonnés ; **aucun** sort lethality type « mort instantanée ». |
| **MG-10** | Wilderness / guerre : combat magique OK ; claims hors guerre : sorts **hostiles** bloqués (comme PvP claim). |
| **MG-11** | Utilitaires (build / récolte / détection) : CD + portée limités ; pas de bypass claims ennemis. |
| **MG-12** | XP grimoire : usage réussi, quêtes, Autels (bonus), **jamais** achat Gemmes / Dust. |
| **MG-13** | Boutique : **skins baguette / auras cast** uniquement — pas de +dégâts / −CD / +Essence. |
| **MG-14** | Flag Nexus `library` : +% XP école + +max Essence soft (config, cap global). |
| **MG-15** | Autel typé (ex. Arcane / Nature) : petit bonus école liée tant que le Coven tient l’Autel. |
| **MG-16** | Persist Essence + grimoire + CD restants (restart-safe). |
| **MG-17** | Admin : `/magic` give wand, set essence, unlock school, teach spell, reload defs. |

---

## 4. Les 12 écoles

| ID | École | Couleur | Fantasy |
| :--- | :--- | :--- | :--- |
| `FIRE` | Braises | #FF6A3D | Feu contrôlé, forge, éclairage |
| `FROST` | Givre | #7EC8FF | Ralentissement, conservation |
| `STORM` | Tempête | #B8A0FF | Vent, foudre douce |
| `EARTH` | Roc | #C4A574 | Construction, ancrage |
| `NATURE` | Sylvanie | #6BCB77 | Récolte, croissance |
| `ARCANE` | Arcane | #D4A5FF | Utilitaires purs, détection |
| `LIGHT` | Aurore | #FFE66D | Soin léger, vision |
| `SHADOW` | Ombre | #6B5B95 | Furtivité soft, sabotage soft |
| `SPIRIT` | Esprit | #A0E7E5 | Buffs alliés, cleanse soft |
| `BLOOD` | Sang | #C23B22 | Risque/récompense (self-cost) — **V2** combat |
| `TIME` | Chronos | #E8D5B7 | Haste / slow utilitaire — **V2** |
| `VOID` | Vide | #2D2A32 | Anti-magie, silence soft — **V2** |

**V1 (S7-T15..T18)** : `FIRE`, `FROST`, `NATURE`, `ARCANE`, `LIGHT`, `EARTH` (6 écoles, ~3–4 sorts chacune).  
**V1.5 (S7-T34)** : `STORM`, `SHADOW`, `SPIRIT`.  
**V2 (S7-T36)** : `BLOOD`, `TIME`, `VOID` + combat durci sous QA PvP.

Niveau d’école : **0–10**. Seuil de sorts typiques : 0 / 2 / 4 / 6 / 8 / 10.

---

## 5. Baguettes

### 5.1 Types (bois × cœur WizardMC)

| `wandId` | Affinité école | Tier | Notes |
| :--- | :--- | ---: | :--- |
| `fracture_oak` | aucune (polyvalente) | 1 | Starter |
| `ember_ash` | FIRE | 2 | Craft / PNJ |
| `frost_pine` | FROST | 2 | |
| `grove_willow` | NATURE | 2 | |
| `arcane_crystal` | ARCANE | 3 | |
| `aurora_birch` | LIGHT | 3 | |
| `stonebinder` | EARTH | 2 | |
| `stormglass` | STORM | 3 | V1.5 |
| `shadowthorn` | SHADOW | 3 | V1.5 |
| `bloodiron` | BLOOD | 3 | V2 |
| `chronoglass` | TIME | 3 | V2 |
| `fracture_prime` | poly + soft all | 4 | Rare craft / Ère — **pas boutique puissance** |

> Ce tableau est celui de la spécification d'origine, écrite avant le
> regroupement à neuf écoles : il ne contient pas `echo_alder` (Esprit, tier 3,
> id 684). La liste tenue à jour, avec les ids d'items et les matériaux, est
> dans `cdc_magie.md` §6.1.

- Affinité : −10 % coût Essence / −10 % CD sur l’école liée (config).  
- Tier : débloque sorts `requiredWandTier`.  
- Skin boutique : même stats, autre texture client.

### 5.2 Item technique (1.7.10)

Pattern **NexusItems** :

- Base material : **`Material.WAND_*` (IDs 669–681)** full-3D client — fallback `BLAZE_ROD` si jar Spigot non sync. Marker lore : `WIZARDMC_WAND` + keys `wandId`, `tier`, `affinity`, `skinId`.  
- Client : textures `textures/items/wand*.png` (+ polish art = vague assets).

### 5.3 Obtention

1. Tutoriel / premier Autel / PNJ **Façonnier d’Éclats** (spawn) → `fracture_oak`.  
2. Craft Forgeron (recettes Mana Brut + bois / cristal).  
3. Quêtes / drop Autel (faible).  
4. **Pas** d’achat de tier via Gemmes.

---

## 6. Cast pipeline (serveur)

```
input (RMB / packet CAST)
  → validate wand + learned + schoolLvl + wandTier
  → check GCD + spell CD + Essence >= cost
  → spend Essence (reserve)
  → start CAST_TIME / CHANNEL (task) OR fire INSTANT / spawn PROJECTILE
  → on success: apply effect + grant XP + start CDs + broadcast FX packet
  → on interrupt: partial refund
```

### 6.1 Paramètres sort (`spells.yml`)

```yaml
id: arcane_spark
school: ARCANE
name: "Étincelle d'Arcane"
incantation: "Arcanum scintilla"
mode: PROJECTILE          # INSTANT | CAST_TIME | CHANNEL | PROJECTILE
manaCost: 12              # Essence
castTicks: 0
channelTicks: 0
cooldownTicks: 40
gcdTicks: 10
requiredSchoolLevel: 0
requiredWandTier: 1
range: 24
hostile: false
interruptible: true
fx: { color: 0xD4A5FF, trail: "arcane", impact: "spark", anim: "wand_thrust" }
effects: [...]            # handlers Java enregistrés
```

### 6.2 Catalogue V1 (exemples figés)

| Sort | École | Mode | Rôle | Hostile |
| :--- | :--- | :--- | :--- | :--- |
| `light_glimmer` | LIGHT | INSTANT | Lumière 30 s | non |
| `nature_growth` | NATURE | CAST_TIME | Accélère croissance cultures zone 3×3 | non |
| `earth_lift` | EARTH | CHANNEL | Lève un bloc (build assist, claim OK only) | non |
| `arcane_sense` | ARCANE | INSTANT | Highlight minerais proches 8 s (CD long) | non |
| `frost_preserve` | FROST | INSTANT | Ralentit fonte / feu items zone | non |
| `ember_torch` | FIRE | PROJECTILE | Torche magique / petit feu contrôlé | soft |
| `arcane_bolt` | ARCANE | PROJECTILE | Dégâts soft PvE / PvP plafonné | oui |
| `frost_shard` | FROST | PROJECTILE | Slow 2 s + dégâts soft | oui |
| `light_mend` | LIGHT | CAST_TIME | Soin léger self/ally (cap / CD) | non |
| `nature_harvest` | NATURE | INSTANT | Bonus drop récolte 1 coup | non |
| `earth_brace` | EARTH | INSTANT | Résistance chute courte | non |
| `ember_flare` | FIRE | CAST_TIME | Cone soft PvE | oui |

Équilibrage chiffres = `spells.yml` + `/magic qa`. Pas hardcodés hors defaults.

---

## 7. Essence Arcane

| Param | Default (config) |
| :--- | :--- |
| Max de base | 100 |
| Regen hors combat | 4 / s |
| Regen combat | 1 / s |
| Délai « hors combat » | 5 s sans dégâts magiques / reçus |
| Bonus `library` | +10 max, +10 % XP école |
| Bonus niv école moyenne | +2 max / niveau (cap +40) |

Affichage : **barre HUD** (sous hotbar ou colonne SMP) + flash coût au cast.

---

## 8. Progression grimoire

- XP par école (`schoolXp`) + liste `learnedSpells[]`.  
- Apprentissage :  
  - **Gratuit** sorts seuil (niv école atteint),  
  - **Parchemin** (loot / quête) → teach,  
  - **XP grimoire** dépensable dans GUI (coût croissant).  
- Reset Ère : **conserve** sorts appris (soft) ou archive selon `eraMagicReset` (default : conserve, nerf CD seulement).

---

## 9. Anti-P2W (checklist)

| Interdit boutique / Pass | Autorisé |
| :--- | :--- |
| +dégâts, −CD, +Essence max, unlock école | Skin baguette, trail cast cosmétique |
| One-shot / invuln payante | Titres / particules grimoire |
| Skip progression | — |

Revue QA : **S7-T31 / S7-T32**.

---

## 10. Packets — **128**

Canal NMS WizardMC id **128** (`MagicPacket`).

| Action | Dir | Payload |
| :--- | :--- | :--- |
| `SYNC_PROFILE` | S→C | essence, max, schools[], learned[], gcd, cds, selectedSpell |
| `SELECT_SPELL` | C→S | spellId |
| `CAST` | C→S | spellId, yaw, pitch, optional targetEntityId / block |
| `CAST_STATE` | S→C | spellId, state(START/TICK/SUCCESS/FAIL/INTERRUPT), ticksLeft |
| `FX_PLAY` | S→C | spellId, casterUuid, from, to, fxId, color |
| `OPEN_GRIMOIRE` | S→C | snapshot + canLearn |
| `LEARN` | C→S | spellId |
| `WAND_SYNC` | S→C | wandId, tier, skinId (si held change) |
| `ERROR` | S→C | code + message |

Compagnons : packet **129** (réservé). Wiki : **126**. Boutique : **125**.

---

## 11. Client MCP (`wizardmc-clean`)

```
fr.wizardmc.client.magic/
├── MagicManager.java          # cache profil
├── MagicPacket.java           # 128
├── hud/ElementEssenceBar.java
├── hud/ElementSpellBar.java   # 6 slots + CD overlays
├── gui/GuiGrimoire.java       # livre DA Wiki/Boutique
├── gui/GuiSpellRadial.java    # hold key → roue
├── fx/SpellFxRenderer.java    # trails, impact, beam
├── fx/CastAnimation.java      # bras / baguette swing custom
├── fx/IncantationOverlay.java # texte flottant
└── render/WandItemRenderer.java
```

### UX

1. **Jauge Essence** toujours visible si baguette tenue (sinon pastille off).  
2. **Barre de sorts** (6) + keybinds ; roue alternative (maintenir `R` config).  
3. Pendant `CAST_TIME` : barre de channel + incantation.  
4. Settings : disable shake / réduire particules (`cdc_exp_client`).  
5. Assets : textures baguettes (ref. archive Arcanum **style only** → **recréer** sous `textures/wizardmc/` ; pas de shipping IP HP).

### Animations

| Phase | Client |
| :--- | :--- |
| Wind-up | Pose bras + particules school color |
| Release | Thrust baguette + son |
| Projectile | Entité légère **ou** FX pur interpolé (préférer FX client si pas d’entité Spigot V1) |
| Impact | Burst + son |

Serveur autoritatif sur hit ; client prédiction visuelle OK si reconcilié par `FX_PLAY`.

---

## 12. Serveur — WizardCore

```
fr.wizardmc.magic/
├── api/MagicAPI.java
├── data/
│   ├── SpellSchool.java
│   ├── SpellDefinition.java
│   ├── WandDefinition.java
│   ├── PlayerMagicProfile.java
│   └── dao/JsonMagicDAO.java | MySqlMagicDAO.java
├── items/WandItems.java
├── cast/CastSession.java, CastService.java
├── effects/SpellEffectRegistry.java + impl V1
├── managers/MagicManager.java
├── listeners/WandListener.java, MagicCombatListener.java
├── packets/MagicPacket.java
├── commands/MagicCommand.java
└── config/spells.yml, wands.yml, magic.yml
```

```java
// MagicAPI (extrait)
boolean cast(Player p, String spellId);
boolean learn(Player p, String spellId);
int getEssence(Player p);
int getMaxEssence(Player p);
int getSchoolLevel(Player p, SpellSchool school);
boolean hasLearned(Player p, String spellId);
ItemStack createWand(String wandId);
boolean isWand(ItemStack stack);
```

`magicStorageType: JSON|MYSQL`.

```sql
CREATE TABLE IF NOT EXISTS wizard_player_magic (
  uuid VARCHAR(36) PRIMARY KEY,
  essence INT NOT NULL,
  essence_max INT NOT NULL,
  selected_spell VARCHAR(64) NULL,
  schools_json TEXT NOT NULL,   -- { "ARCANE": {"lvl":2,"xp":120}, ... }
  learned_json TEXT NOT NULL,   -- ["light_glimmer", ...]
  cooldowns_json TEXT NOT NULL,
  updated_at TIMESTAMP NOT NULL
);
```

---

## 13. WizardSpigot (besoins)

| Besoin | V1 | Notes |
| :--- | :--- | :--- |
| Items baguette custom (IDs / models) | **Oui si possible** | Sinon `BLAZE_ROD` + marker + renderer client |
| Entité `SpellProjectile` | V1.5 (S7-T35) | Entité NMS id **207** / spawn object **107** ; fallback raycast si `projectile.useEntity: false` |
| API `MagicItems` / register material | Soft | Align Nexus block pattern |

Branche Spigot : seulement si IDs custom requis avant T2 ; sinon Core-only + client textures.

---

## 14. Intégrations

| Module | Lien |
| :--- | :--- |
| Nexus `library` | MG-14 |
| Autels | Bonus école liée |
| Covens / claims | MG-10 / MG-11 |
| Pouvoirs | Orthogonal (pas d’Essence) |
| Mana Brut | Orthogonal (économie) |
| Boutique pkt 125 | Skins only |
| Ères | Soft theme FX ; pas wipe sorts default |
| Wiki | Pages école / tutoriel baguette |

---

## 15. Mapping ROADMAP S7

Voir détail granulaires dans [`ROADMAP.md`](ROADMAP.md#phase-s7--magie). Synthèse :

| Vague | IDs | Contenu |
| :--- | :--- | :--- |
| **S7.0 Design** | T0 | CDC ✅ |
| **S7.1 Fondation** | T1–T5 | API, storage, yml, pkt 128, `/magic` — **T1–T5 ✅** |
| **S7.2 Baguettes / Essence** | T6–T9 | Items, Spigot IDs, regen, starter |
| **S7.3 Cast** | T10–T14 | GCD, INSTANT/CAST/CHANNEL/PROJECTILE, claims |
| **S7.4 Sorts V1** | T15–T18 | 6 écoles + XP/learn |
| **S7.5 Client** | T19–T25 | HUD, barre, roue, grimoire, FX, anims, assets |
| **S7.6 Intégrations** | T26–T30 | `library`, Autels, Forgeron, skins boutique, Wiki |
| **S7.7 QA** | T31–T33 | qa + soft-cap PvP + polish |
| **S7.8 Post-V1** | T34–T37 | backlog V1.5 / V2 |

Ancienne maille T1–T4 (agrégée) :

| Ancien | Devient |
| :--- | :--- |
| S7-T1 (gros) | T1–T9 |
| S7-T2 (gros) | T10–T18 |
| S7-T3 (gros) | T19–T25 |
| S7-T4 (gros) | T26–T33 |

---

## 16. QA

| ID | Cas |
| :--- | :--- |
| **Q-MG-01** | Cast sans baguette → refuse |
| **Q-MG-02** | Essence insuffisante → refuse + msg |
| **Q-MG-03** | GCD / CD respectés après relog |
| **Q-MG-04** | Interrupt CAST_TIME |
| **Q-MG-05** | Projectile / FX visibles autres joueurs |
| **Q-MG-06** | Hostile bloqué en claim hors guerre |
| **Q-MG-07** | `library` augmente XP / max Essence |
| **Q-MG-08** | Boutique skin ne change pas stats |
| **Q-MG-09** | `/magic qa` balaye sorts V1 |
| **Q-MG-10** | Soft-cap dégâts PvP (pas oneshot full fer) |

---

## 17. Hors scope V1

- Maisons type « sorting hat » / IP HP  
- Balai volant, créatures HP  
- 12 écoles combat complètes  
- PvP esport-perfect  
- Crafting ultra profond cœurs de baguette (V2)

---

## 18. Décisions figées (S7-T0)

1. Packet Magie = **128** (pas 126 — pris par Wiki).  
2. Essence joueur ≠ Mana Brut Coven.  
3. Feeling **baguette + jauge + cast/FX** prioritaire sur quantité de sorts.  
4. Noms / lore **originaux WizardMC**.  
5. Assets = à récupérer chercher dans le repo https://github.com/Wizard-MC/ASSETS et à ajouté dans les assets wizardmc du MCP ! 
