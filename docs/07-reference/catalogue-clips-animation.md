# Catalogue des clips d'animation

Les noms de clips que le moteur de rendu reconnaît, et ce qu'il en fait. Référence pour
les modeleurs et pour tout ajout de créature.

**Statut : livré.** Cette table est dérivée du moteur d'animation.

---

## 1. La règle

> **Le moteur cherche des noms précis.** Un clip au bon geste mais au mauvais nom ne sera
> jamais joué.

La casse est ignorée. Les noms sont cherchés **dans l'ordre du tableau** : le premier
trouvé gagne.

---

## 2. Les douze familles reconnues

| Famille | Noms acceptés, dans l'ordre |
|---|---|
| **Repos** | `Idle`, `idle`, `attente`, `waiting`, `vol_stationnaire` |
| **Marche** | `marche`, `walk`, `walking`, `Walk`, `Marche` |
| **Course** | `sprint`, `run`, `course` |
| **Douleur** | `hurt`, `hurt_1`, `damage`, `hit`, `hurt1`, `hit_shell` |
| **Mort** | `death`, `dying`, `mort`, `die` |
| **Mort noyée** | `water_death`, `death_water` |
| **Mort au sol** | `land_death`, `death_land` |
| **Nage** | `walk_swim`, `swim`, `nage` |
| **Flottaison** | `idle_swim`, `float`, `flottaison` |
| **Vol stationnaire** | `idle_fly`, `vol_stationnaire`, `hover` |
| **Vol** | `walk_fly`, `glide`, `fly`, `vol` |
| **Passe rasante** | `fly_low`, `dive`, `swoop`, `rase` |

---

## 3. L'ordre des canaux

Le moteur choisit un clip en suivant cet ordre. Le premier qui répond gagne.

| # | Canal | Ce qui le déclenche |
|---|---|---|
| 1 | **Action du serveur** | Le serveur annonce une attaque, avec son compteur de ticks |
| 2 | **Agonie** | L'entité est en train de mourir |
| 3 | **Douleur** | L'entité vient d'être touchée |
| 4 | **Locomotion** | Ce qui reste |

### Pourquoi cet ordre, et pas un autre

**L'action du serveur passe en premier**, et son temps de lecture est **celui que le
serveur compte**. C'est ce calage qui fait correspondre le geste et le moment où les
dégâts tombent — donc qui rend une attaque esquivable.

**L'agonie passe après l'action**, et c'est contre-intuitif. La raison : la mort en vol
d'un dragon est une chorégraphie que le serveur envoie clip par clip. Un clip de mort
générique placé avant l'action l'aurait remplacée par une chute molle.

**La douleur passe avant la locomotion**, pour qu'un coup reçu se voie même en courant.

### L'ordre de la locomotion

| # | Condition | Famille cherchée |
|---|---|---|
| 1 | En vol, en passe rasante | Passe rasante, puis vol ou vol stationnaire |
| 2 | En vol | Vol si elle avance, vol stationnaire sinon |
| 3 | Dans l'eau | Nage si elle avance, flottaison sinon |
| 4 | Rapide | Course |
| 5 | En mouvement | Marche |
| 6 | Sinon | Repos |

Si aucune famille ne répond, le **repos** est joué même pendant un déplacement. C'est
volontaire : beaucoup de créatures n'ont pas de clip de marche — celles qui planent,
celles qui bondissent, celles qui sont enracinées. Sans ce repli, elles se figeaient dans
leur pose de montage dès qu'elles bougeaient.

---

## 4. Les clips d'attaque

Ils ne suivent **aucune convention** : c'est le catalogue des créatures qui nomme le clip
de chaque attaque, et le nom doit correspondre exactement à celui du modèle.

> Un clip mal orthographié donne une **attaque sans geste** : les dégâts tombent, le
> joueur ne voit rien venir, et l'esquive devient impossible à apprendre.

Un contrôle automatisé vérifie chaque nom contre les fichiers livrés.

---

## 5. La cadence

Un clip de locomotion est joué **à la vitesse réelle de la créature**, entre 0,65 et 1,60
fois sa cadence d'auteur. La vitesse de référence est d'environ 0,16 bloc par tick.

> **Animez un clip de marche à une allure moyenne.** Un clip animé comme une course sera
> joué au ralenti quand la créature marche, et paraîtra traîner.

### Les deux exceptions

| Exception | Cadence |
|---|---|
| Un clip d'action annoncé par le serveur | Synchronisé sur le compteur du serveur |
| Un clip de bond, pendant un bond | **1,0** — sa durée est celle de l'arc |

La seconde mérite l'explication : la vitesse au sol d'une gelée varie énormément à
l'intérieur d'un même bond. L'accorder à la vitesse ferait battre l'écrasement au rythme
de l'élan au lieu de celui de la retombée.

---

## 6. Le fondu

Le moteur fond les clips l'un dans l'autre sur **un huitième de seconde**.

| Ce que ça change pour un modeleur | |
|---|---|
| Les poses de début et de fin comptent moins | Le moteur comble l'écart |
| Un clip d'attaque ne doit pas commencer par un temps mort | Le fondu en ajoute déjà |
| Un bone animé par un seul des deux clips revient au repos | Il n'y reste pas collé |

La durée est courte exprès : au-delà, un coup porté paraît mou parce que le geste démarre
en retard.

---

## 7. Les clips de bond

Pour une créature en démarche `HOP`, le clip de **marche** est le saut entier : détente,
vol, écrasement.

| Règle | Pourquoi |
|---|---|
| Un saut entier, et un seul, par clip | Le moteur le relance à chaque détente |
| Sa durée doit égaler la période de bond du catalogue | Le clip joue alors exactement une fois par saut |

| Modèle | Durée du clip `walk` | Période déclarée |
|---|---:|---:|
| Six gelées | 1,75 s | 35 ticks |
| Gelée ailée | 1,50 s | 30 ticks |
| Gelée tricéphale | 1,46 s | 29 ticks |

Un contrôle confronte chaque période à la durée du clip. **Si vous retouchez la durée, la
période doit suivre.**

---

## 8. Les cas particuliers livrés

Quatre situations réelles qui expliquent pourquoi les listes sont si longues.

| Cas | Ce qui s'est passé |
|---|---|
| **Les quatre ours de monture** | Leur modeleur a nommé tous les clips au participe présent : `waiting`, `dying`. Le moteur les accepte. C'est un contrôle qui en avait une copie périmée et les déclarait cassés. |
| **Le crocodile** | Il porte `land_death` et `water_death`, dont **aucun** ne s'appelle `death`. Sa mort n'était jamais jouée. |
| **105 modèles** | Ils portaient un clip de mort que rien ne déclenchait |
| **38 modèles** | Ils portent un clip de douleur qui n'était jamais joué : les créatures encaissaient sans que rien ne le montre |

### Ce qu'il faut en retenir

Les variantes françaises et les participes présents sont acceptés **parce que des modèles
livrés les emploient** — ce n'est pas une raison d'en ajouter.

Pour un modèle neuf, préférer les noms anglais simples : `idle`, `walk`, `sprint`,
`death`, `hurt`.

---

## 9. Le minimum pour une créature

| Clip | Obligatoire ? |
|---|---|
| **Repos** | oui |
| Déplacement | non — sans lui, le repos est joué pendant le déplacement |
| **Au moins une attaque** | oui, pour une créature hostile |
| Mort | non, mais c'est dommage : 105 modèles en ont un |
| Douleur | non, mais 38 modèles en ont un |

---

## 10. Ce qui n'est pas supporté

| Non supporté | Conséquence |
|---|---|
| `box_uv` | Le chargement **échoue**, avec une erreur explicite |
| Les *meshes* | Ignorés, avec un avertissement |
| Les *locators* | Ignorés |
| L'interpolation `bezier` | Retombe sur linéaire, avec un avertissement |

L'interpolation « smooth » de Blockbench est supportée : elle est lue comme une spline.

---

## À lire ensuite

- [Pipeline Blockbench](../06-creer-du-contenu/pipeline-bbmodel.md) — les conventions du modèle
- [Créer une créature](../06-creer-du-contenu/creer-une-creature.md) — la chaîne complète
- [WizardMobs](../05-operer/configuration/wizardmobs.md) — les champs d'attaque
- [Catalogue des créatures](catalogue-creatures.md) — les 49 espèces
