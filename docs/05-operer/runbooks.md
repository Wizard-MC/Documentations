# Runbooks

Quand ça casse : symptôme → diagnostic → geste. Chaque entrée décrit un incident déjà
survenu ou clairement possible, avec la cause et la réparation.

**Statut : livré.** À compléter à chaque nouvel incident.

---

## Comment se servir de ce document

1. Trouver le **symptôme** observé, pas la cause qu'on suppose.
2. Suivre le diagnostic dans l'ordre. Il va du plus fréquent au plus rare.
3. **Noter ce qu'on a fait.** Un incident non tracé se reproduit à l'identique.
4. Si l'incident n'est pas ici, l'ajouter après l'avoir résolu.

> **Avant tout geste qui modifie l'état : sauvegarder.** Même en urgence. Surtout en
> urgence.

---

## 1. Le serveur ne démarre pas

### Diagnostic

| Vérifier | Si c'est ça |
|---|---|
| La version de Java | Le serveur 1.7.10 exige Java 8. Un JDK plus récent produit des classes que la machine virtuelle refuse. |
| Les greffons obligatoires de WizardCore | GlobalAPI, Essentials, Vault, WizardCovens. L'un manque → WizardCore ne charge pas. |
| Le journal, première erreur | La **première**, pas la dernière : les suivantes en découlent |
| La place disque | Un disque plein empêche l'écriture des fichiers de persistance |

### Geste

Installer ce qui manque, ou revenir au jar précédent. **Ne pas supprimer de fichier de
configuration pour « repartir propre »** : on perd l'état des Nexus.

---

## 2. Un greffon tourne en mode dégradé

### Symptôme

Un avertissement au démarrage, et une capacité qui manque : les créatures ont toutes le
même niveau, les récompenses de quête ne sont pas versées, les régions interdites à la
magie ne s'appliquent pas.

### Diagnostic

| Avertissement | Ce qui manque |
|---|---|
| WizardMobs sans WizardCore | Le niveau des créatures ne suit plus la puissance du joueur |
| WizardMobs sans WorldGuard | Le découpage en régions est ignoré |
| WizardQuest sans WizardMobs | Les objectifs visant une espèce custom ne se valident pas |
| WizardQuest sans WizardCore | Les récompenses en Éclats ne sont pas versées |
| WizardIntro sans WizardCore | L'école choisie n'est pas lue par la magie |
| WizardCore sans WorldGuard | Les régions interdites à la magie ne s'appliquent pas |

### Geste

Installer la dépendance, redémarrer. **C'est normal une seule fois ;** si l'avertissement
se répète à chaque tick, c'est un défaut et il faut le signaler : un journal noyé fait
manquer le vrai incident.

---

## 3. La base de données est injoignable

### Symptôme

Le serveur démarre, mais les données ne correspondent plus à ce qu'il y avait hier.

### Diagnostic

C'est le comportement prévu : **une base injoignable fait retomber sur JSON** plutôt
que d'empêcher le démarrage. Le greffon écrit donc dans des fichiers locaux, et les
tables MySQL ne bougent plus.

Vérifier dans le journal de démarrage : le repli est annoncé.

### Geste

1. **Arrêter le serveur tout de suite.** Chaque minute de jeu écrit dans les fichiers
   locaux des données qui divergent des tables.
2. Réparer l'accès à la base.
3. Décider quelle source fait foi : les fichiers locaux contiennent ce qui s'est passé
   depuis le repli, les tables contiennent l'état d'avant.
4. Redémarrer, vérifier que la base est bien utilisée.

> **Ne pas laisser tourner un serveur en repli.** Les deux sources divergent, et plus
> personne ne saura laquelle garder.

---

## 4. Une quête a disparu

### Symptôme

Une quête n'apparaît plus chez le donneur, sans message d'erreur visible.

### Diagnostic

Presque toujours la même cause : **une matière mal orthographiée dans la récompense
fait écarter la quête entière au chargement.**

C'est arrivé : une faute de frappe sur un totem a fait disparaître un maillon complet
de la trame.

| Vérifier | Où |
|---|---|
| Chaque nom de matière des récompenses | `quests.yml` |
| Chaque cible d'objectif `KILL` et `COLLECT` | Idem |
| Le `minMastery` | Trop haut, la quête n'est jamais proposée |
| Le `chapter` pour une quête de trame | Deux quêtes au même chapitre s'excluent |
| Le journal de démarrage | L'écart est signalé |

### Geste

Corriger le nom, puis `/quest reload` — permission `wizardmc.quest.admin`. Lancer les
contrôles automatisés du dépôt : ils détectent ce cas.

---

## 5. Une créature apparaît en cube rose

### Symptôme

Une espèce s'affiche comme un cube magenta au lieu de son modèle.

### Diagnostic

Une ou plusieurs **faces du modèle ne nomment aucune texture**. Le moteur de rendu ne
sait pas quoi dessiner et sort du magenta.

Le correctif est en place côté moteur : une face sans texture ne se dessine plus. Si le
cube réapparaît :

| Vérifier | |
|---|---|
| Le client est-il à jour ? | Un client antérieur au correctif montre encore le cube |
| Quelle espèce exactement ? | C'est le modèle qui est en cause, pas le client |

### Geste

Remonter **l'espèce nommée** à l'équipe. Le correctif se fait dans le modèle ou le
moteur, pas en configuration.

---

## 6. Une créature frappe sans montrer son geste

### Symptôme

Les dégâts tombent, le joueur ne voit rien venir, et l'esquive est impossible à
apprendre.

### Diagnostic

**Le nom de clip déclaré dans `mobs.yml` ne correspond à aucun clip du modèle.**

C'est l'erreur la plus coûteuse qu'on puisse faire dans ce fichier : l'attaque
fonctionne côté serveur, mais aucune animation ne la montre.

### Geste

1. Relever l'espèce et l'attaque.
2. Comparer le `clip` déclaré aux clips réels du bbmodel.
3. Corriger le nom, `/mobs reload`.
4. Lancer les contrôles : un contrôle automatisé vérifie chaque nom de clip contre les
   fichiers livrés.

> C'est une **violation d'une règle de conception**, pas un défaut cosmétique. Un dégât
> sans télégraphe rend une attaque inesquivable.

---

## 7. Une créature frappe de trop loin

### Symptôme

Des joueurs disent qu'une espèce les touche alors qu'elle est visiblement à distance.

### Diagnostic

| Vérifier | |
|---|---|
| Le `maxRange` de l'attaque | Il se raisonne **de surface à surface**, pas de centre à centre |
| La largeur de la créature | Une grosse créature a une boîte large : le bord est loin du centre |
| Frappe-t-elle à travers un mur ? | Alors ce n'est pas une question de portée, c'est un bug |

### Geste

Si c'est un réglage : corriger le `maxRange` en raisonnant sur la distance entre les
deux boîtes.

Si elle frappe à travers un mur : remonter à l'équipe. Les attaques de longue portée
vérifient la ligne de vue — frapper à travers un mur ne se voit pas venir et ne
s'esquive pas.

---

## 8. Une gelée retombe au milieu de son écrasement

### Symptôme

Une créature qui bondit paraît désynchronisée : l'écrasement du clip ne tombe pas quand
elle touche le sol.

### Diagnostic

`hopTicks` ne vaut plus la durée du clip de bond. Le clip joue alors plus ou moins d'une
fois par saut.

Cause la plus fréquente : **le modeleur a retouché le clip** et la valeur du catalogue
n'a pas suivi.

### Geste

Relever la durée du clip `walk` du bbmodel, la convertir en ticks, corriger `hopTicks`.
Lancer les contrôles : l'un d'eux confronte chaque période à la durée du clip.

---

## 9. Les joueurs sont expulsés au bout de cinq secondes

### Symptôme

Tout le monde, ou certains, sont déconnectés juste après s'être connectés.

### Diagnostic

La poignée de main du client a échoué.

| Qui est touché | Cause probable |
|---|---|
| Tout le monde | Le serveur a été mis à jour, le client pas encore distribué |
| Quelques joueurs | Leur client est périmé |
| Un seul joueur | Il n'utilise pas le client WizardMC, ou pas par le lanceur |

### Geste

Si c'est général : **c'est une erreur de déploiement.** Distribuer le client, ou revenir
à la version serveur précédente le temps de le faire.

Annoncer les mises à jour de client à l'avance évite entièrement cet incident.

---

## 10. La cinématique d'arrivée ne se joue pas

### Symptôme

Un compte neuf arrive directement au spawn.

### Diagnostic

| Vérifier | |
|---|---|
| Le monde de cinématique existe-t-il ? | **Il n'est jamais généré automatiquement.** Absent, la cinématique ne démarre pas et le joueur reste au spawn. |
| Le compte est-il vraiment neuf ? | Elle est jouée **une seule fois par joueur**, jamais ensuite |
| WizardIntro est-il chargé ? | — |

### Geste

Créer le monde. C'est délibérément manuel : générer un monde sans qu'on l'ait demandé
est plus dangereux que de ne pas jouer une cinématique.

> **Un joueur qui a déjà joué la cinématique ne peut pas la rejouer.** Ce n'est pas un
> incident, c'est la règle. Lui expliquer que son choix d'école n'enferme rien.

---

## 11. Un Autel ne se capture pas

### Diagnostic

| Vérifier | |
|---|---|
| Sa recharge | Une heure après une capture. `/altar qa` |
| Y a-t-il un joueur adverse dans le rayon ? | **Deux joueurs de Covens différents mettent la progression en pause pour tous** |
| Le joueur bouge-t-il ? | Tout déplacement annule, et la progression retombe à **zéro** |
| Les coordonnées de l'Autel | Un Autel dans la pierre est inatteignable |

### Geste

Si c'est la recharge ou la contestation : rien à faire, c'est le système. Si la position
est mauvaise, la corriger dans `altars.yml` — mais **pas en cours d'Ère** si un Coven a
bâti autour.

---

## 12. Le Colosse refuse de se lancer

### Diagnostic

**On n'est pas en phase de Grand Cataclysme.** C'est le seul refus prévu : un boss de
fin d'Ère qui se présenterait au premier jour n'aurait plus rien à clore.

Vérifier avec `/era qa`.

### Geste

Attendre les trois derniers jours de l'Ère. Il n'y a pas de contournement, et il ne
doit pas y en avoir.

---

## 13. Le serveur rame quand il y a des créatures

### Diagnostic

| Vérifier | |
|---|---|
| Combien de créatures custom sont vivantes ? | `/mobs list` |
| Un boss volant est-il en cours ? | Les passes rasantes coûtent en calcul de trajectoire |
| Une nuée a-t-elle été lancée et oubliée ? | — |
| Un boss traîne-t-il depuis une semaine ? | Un oubli de game master |

### Geste

`/mobs purge` retire les créatures custom. C'est l'outil d'urgence.

Ensuite, **prendre l'habitude de purger après chaque événement** : c'est la cause la
plus fréquente de dérive lente.

---

## 14. Un claim a disparu

### Symptôme

Un joueur dit que son terrain n'est plus à son Coven.

### Diagnostic

| Vérifier | |
|---|---|
| Y a-t-il eu une clôture d'Ère ? | **Les claims ne retombent jamais.** S'ils ont disparu, c'est un incident majeur. |
| Le Coven a-t-il été dissous ? | Dissoudre libère les claims. Action de chef, confirmée, irréversible. |
| Y a-t-il eu un `unclaim` ? | Un Officier ou un Bâtisseur peut en retirer |
| Y a-t-il eu `overclaim` ? | **Il est désactivé.** Si ça s'est produit, c'est un bug. |

### Geste

Si c'est une dissolution ou un `unclaim` : rien. Le staff ne reconstitue pas.

Si des claims ont disparu à une clôture d'Ère, ou par `overclaim` : **incident
majeur.** Restaurer la sauvegarde d'avant clôture et remonter à l'équipe.

---

## 15. La clôture d'Ère a mal tourné

### Symptôme

Après une clôture : des claims manquants, des Nexus pas au premier niveau, le Panthéon
vide, ou des inventaires touchés.

### Diagnostic

Comparer à ce qui doit retomber et à ce qui doit rester — voir
[Exploitation quotidienne](exploitation.md#pendant).

| Anormal | Gravité |
|---|---|
| Des claims manquants | **Majeur** |
| Des inventaires touchés | **Majeur** |
| Le Panthéon vide | Mineur, réparable |
| Un Nexus resté à son niveau | Mineur |
| Des Autels avec un propriétaire | Mineur |

### Geste

Pour les cas majeurs : **restaurer la sauvegarde d'avant clôture.** Il n'existe pas de
retour arrière partiel — on ne peut pas annuler le reset d'un seul Coven.

C'est la raison pour laquelle la sauvegarde avant clôture n'est pas optionnelle.

---

## 16. Un joueur a profité d'un bug

### Diagnostic

Voir la procédure de [Modération](moderation.md#étape-2--distinguer-le-bug-de-la-triche).

| Indice | Plutôt un bug | Plutôt de la triche |
|---|---|---|
| Ça arrive à plusieurs joueurs | oui | non |
| Le joueur le signale | oui | non |
| Il en tire un avantage répété et le cache | non | oui |

### Geste

1. **Retirer le gain d'abord.** Une sanction qui laisse le gain en place enseigne que ça
   valait le coup.
2. Corriger le bug, ou le signaler.
3. Sanctionner selon le barème, si c'était délibéré.
4. **Remercier** le joueur qui a signalé. Il coûte moins cher qu'un joueur perdu.

---

## 17. Le modèle d'incident

À remplir pour chaque incident, et à ajouter ici s'il est nouveau.

| Champ | Contenu |
|---|---|
| **Date et heure** | — |
| **Symptôme** | Ce qu'on a observé, pas ce qu'on suppose |
| **Qui l'a signalé** | — |
| **Diagnostic** | Ce qu'on a vérifié, dans l'ordre |
| **Cause** | — |
| **Geste** | Ce qu'on a fait |
| **Trace** | Sauvegarde utilisée, commandes lancées |
| **Prévention** | Ce qui empêcherait que ça recommence |

La dernière ligne est celle qui compte. Un incident résolu sans prévention est un
incident qui reviendra.

---

## À lire ensuite

- [Exploitation quotidienne](exploitation.md) — les procédures normales
- [Configuration](configuration/README.md) — avant de changer une valeur
- [Modération](moderation.md) — quand c'est humain et non technique
- [Installation](installation.md) — les pièges de déploiement
