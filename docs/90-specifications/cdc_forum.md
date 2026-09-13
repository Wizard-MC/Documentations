# CDC TECHNIQUE — Forum

> Le forum de wizardmc.fr. Salons, sujets, messages, modération. Rédaction en
> texte enrichi, édition avec historique, signalements, journal des décisions.

**État : livré.** Le forum tourne en production. Ce document décrit l'existant,
y compris ses limites.

Documents liés :

- [Modération](../05-operer/moderation.md) — ce que fait le staff, et ce qu'il ne fait pas
- [Architecture logicielle](../01-projet/architecture-logicielle.md) — la place du site dans l'ensemble

Le code vit dans le dépôt `Wizard-MC/website` (Laravel 12, PHP 8.4).

---

## 1. Objet et intention

Le forum a d'abord servi à publier : un titre, un message, une file d'attente de
validation. Rien d'autre. On ne pouvait ni corriger une faute, ni citer quelqu'un,
ni signaler un abus, et le staff décidait sans laisser de trace.

Le forum v2 vise ce que les joueurs attendent d'un forum : **écrire lisiblement,
revenir sur ce qu'on a écrit, et savoir qui a décidé quoi.**

Trois principes tiennent l'ensemble.

> **1. Le serveur décide du rendu.** Ce que l'auteur voit dans son navigateur n'est
> jamais ce qui est enregistré : tout passe par un filtre unique. L'aperçu est rendu
> par le serveur pour cette raison — un aperçu local mentirait.

> **2. Rien ne disparaît.** Une modification garde la version précédente, une
> suppression est réversible, une décision est journalisée. Ce qui s'efface
> vraiment ne se discute plus.

> **3. Un signalement n'agit sur rien.** Il ouvre une ligne dans une file. Un bouton
> qui masquerait un message sur simple signalement serait une arme.

---

## 2. Le modèle de données

```
forum_categories
 └── forums                 (slug unique, locked, requires_approval, compteurs dénormalisés)
      └── forum_threads     (slug unique par salon, status, is_pinned, is_locked, views, suppression douce)
           └── forum_posts  (body, status, is_first, edited_at/by, suppression douce)
                ├── forum_post_revisions   (l'état précédent, à chaque modification)
                └── forum_reports          (signalements, unicité message + signaleur)

forum_permissions            (par couple salon + rôle)
forum_moderation_logs        (en ajout seul : qui, quoi, quand, pourquoi)
```

### Les trois tables ajoutées par le v2

| Table | Ce qu'elle porte | Pourquoi |
|---|---|---|
| `forum_post_revisions` | `post_id`, `edited_by`, `body`, `created_at` | Permet d'autoriser l'édition **sans fenêtre de temps** : l'historique est le garde-fou, pas le chronomètre. |
| `forum_moderation_logs` | `user_id`, `action`, `subject_type`, `subject_id`, `forum_id`, `subject_label`, `reason`, `context`, `created_at` | Une décision non journalisée n'existe pas. Pas d'`updated_at` : la table est en **ajout seul**. |
| `forum_reports` | `post_id`, `thread_id`, `reporter_id`, `reason`, `message`, `status`, `handled_by`, `handled_at`, `handler_note` | `unique(post_id, reporter_id)` : un double clic ne doit pas produire une erreur serveur. |

`forum_reports.thread_id` est **dénormalisé**. Il permet de filtrer les signalements
par salon sans joindre les messages, et il survit à la suppression du message.

---

## 3. Les états

### Un sujet

| État | Visible de | Ce qui l'amène là |
|---|---|---|
| `published` | tout le monde | publication dans un salon non modéré, ou approbation |
| `pending` | son auteur, la modération | publication dans un salon `requires_approval` |
| `rejected` | son auteur, la modération | décision de modération, avec motif facultatif |
| `hidden` | la modération | décision de modération |
| *supprimé* | la modération | suppression par l'auteur ou par le staff |

La suppression est **douce**. Un sujet qui s'évaporerait pour de bon emporterait
aussi les réponses des autres.

### Un message

Mêmes états. Deux règles supplémentaires :

- le **premier message** ne se supprime pas seul : c'est le sujet qu'on supprime ;
- un modérateur voit les messages supprimés dans le fil, et peut les rétablir.

### Les réponses ouvertes ou fermées

`forum_threads.is_locked` dit si le sujet accepte des réponses. La case
« Autoriser les réponses » du formulaire de création l'écrit dès la publication :
une annonce, une présentation ou un règlement n'ont pas à attendre qu'on les ferme.

---

## 4. Les règles

### Lecture et écriture

| Règle | Énoncé |
|---|---|
| `F-01` | Un salon **sans aucune ligne** dans `forum_permissions` est visible de tous, et ouvert à tout compte authentifié. |
| `F-02` | **La lecture est publique tant qu'aucune ligne ne coche `can_view`.** Dès qu'une ligne la revendique, la lecture se limite aux rôles qui l'ont. |
| `F-02bis` | L'**écriture**, elle, se restreint dès qu'une ligne existe : publier et répondre demandent alors une ligne qui l'accorde. |
| `F-03` | `forums.locked` coupe la création et la réponse avant même que les permissions soient consultées. |
| `F-04` | Un modérateur du salon passe outre `F-02` et `F-03`. |

Ces deux règles sont volontairement dissymétriques, parce que les intentions le
sont : **on lit par défaut, on écrit sur autorisation.**

La version précédente les confondait — une ligne, quelle qu'elle soit, refusait
les visiteurs. Accorder au staff le droit de publier dans *Annonces* faisait donc
disparaître le salon pour tous ses lecteurs. Voir `FO-D1`.

Conséquence à connaître, dans l'autre sens : **cocher `can_view` sur un seul rôle
ferme la lecture à tous les autres**, visiteurs compris. C'est le geste qui rend
un salon privé, et il n'y en a pas d'autre.

### Édition

| Règle | Énoncé |
|---|---|
| `F-05` | L'auteur modifie son message tant que le sujet n'est ni verrouillé ni supprimé. **Aucune limite de temps.** |
| `F-06` | Chaque modification écrit l'état précédent dans `forum_post_revisions`. |
| `F-07` | Le premier message se modifie **avec son sujet** : un seul formulaire pour le titre et le corps. |
| `F-08` | Un renommage **ne change pas le slug**. Les liens déjà partagés doivent survivre. |
| `F-09` | Un modérateur peut modifier n'importe quel message ; la modification est alors journalisée comme faite par le staff. |

### Verrouillage

| Règle | Énoncé |
|---|---|
| `F-10` | L'auteur ferme et rouvre son propre sujet. C'est son fil. |
| `F-11` | **Sauf si c'est le staff qui l'a fermé.** L'information est lue dans le journal : la dernière ligne `thread.locked` / `thread.unlocked` porte un `context['by_staff']`. |

### Signalements

| Règle | Énoncé |
|---|---|
| `F-12` | On ne signale ni son propre message, ni deux fois le même message. |
| `F-13` | Un signalement ne modifie **rien** : ni statut, ni visibilité, ni compteur. |
| `F-14` | Trancher un signalement (« retenir » ou « écarter ») est une décision journalisée. Masquer le message est une action **séparée**. |

### Compteurs

| Règle | Énoncé |
|---|---|
| `F-15` | Les compteurs dénormalisés (`thread_count`, `post_count`, `reply_count`, `last_post_at`) sont **recalculés**, jamais incrémentés. |

`F-15` n'est pas une préférence de style. Un incrément est faux dès qu'un message
est masqué, rejeté, déplacé, supprimé ou rétabli — et il l'est silencieusement.

---

## 5. Le contenu enrichi

Le corps d'un message est du HTML, filtré par `App\Support\Html\ContentSanitizer`
selon une **liste blanche** de balises et d'attributs. Le même filtre s'applique :

1. à l'enregistrement, dans la requête de formulaire ;
2. à l'aperçu, via `POST /forum/preview` ;
3. à la lecture, via l'accesseur `Post::$body_html`.

Le troisième passage n'est pas une redondance : il convertit aussi les **anciens
messages en texte brut** en paragraphes, de sorte que la vue publique n'a qu'un
seul cas à traiter.

L'éditeur côté navigateur est Quill, chargé à la demande, partagé avec
l'administration. **Le champ réellement soumis reste un `<textarea>`** : sans
JavaScript, la page fonctionne, la mise en forme s'écrit en HTML, et le filtrage
serveur est identique. L'éditeur est un confort de saisie, jamais une condition.

Le profil de filtrage n'est **pas** un paramètre de la requête d'aperçu : tout le
forum écrit en profil `rich`.

### Ce que le forum n'accepte pas

- **Le téléversement d'images.** Ouvrir un dépôt de fichiers à tout compte
  authentifié est une décision d'exploitation, pas un détail d'éditeur. Les images
  s'insèrent par URL.
- **Le BBCode.** Il n'a jamais existé sur ce forum ; introduire une seconde syntaxe
  obligerait à filtrer deux langages au lieu d'un.

---

## 6. Les actions journalisées

| Action | Ce qu'elle recouvre |
|---|---|
| `thread.approved`, `thread.rejected` | validation d'un sujet en attente |
| `thread.pinned`, `thread.unpinned` | épinglage |
| `thread.locked`, `thread.unlocked` | fermeture des réponses — `context['by_staff']` dit par qui |
| `thread.hidden`, `thread.restored` | masquage et réaffichage |
| `thread.moved` | changement de salon — `context` garde l'origine et la destination |
| `thread.renamed` | changement de titre |
| `thread.deleted`, `thread.undeleted` | suppression douce et rétablissement |
| `post.approved`, `post.rejected` | validation d'une réponse |
| `post.hidden`, `post.restored` | masquage d'un message |
| `post.edited` | modification — `context['by_staff']` dit si elle vient d'un modérateur ou de l'auteur |
| `post.deleted`, `post.undeleted` | suppression d'un message |
| `report.accepted`, `report.dismissed` | décision sur un signalement |

Le journal est **en lecture seule**, sans exception : une ligne ne se modifie pas
et ne se supprime pas, y compris par un administrateur.

---

## 7. Les écrans

### Côté public

| Écran | Adresse | Ce qu'on y fait |
|---|---|---|
| Salon | `/forum/{salon}` | lire la liste des sujets, ouvrir un sujet |
| Nouveau sujet | `/forum/{salon}/threads/create` | titre, message enrichi, réponses ouvertes ou non |
| Sujet | `/forum/{salon}/threads/{sujet}` | lire, répondre, citer, modifier, supprimer, signaler |
| Modifier un sujet | `…/threads/{sujet}/edit` | titre et premier message, ensemble |
| Modifier un message | `/forum/posts/{id}/edit` | le corps seul |
| File de modération | `/forum/moderation` | **les seuls salons que ce modérateur modère** |

La citation passe par l'adresse (`?quote=42`) et non par du JavaScript : le bouton
reste un lien, il fonctionne sans script, et le contenu cité ressort du même filtre.

### Côté administration

| Écran | Adresse | Ce qu'on y fait |
|---|---|---|
| Modération | `/admin/forum-moderation` | trois onglets : sujets, réponses, signalements |
| Sujets | `/admin/forum-threads` | **tout** — publié, masqué, rejeté, supprimé — avec filtres |
| Un sujet | `/admin/forum-threads/{id}` | messages, actions, cinquante dernières lignes du journal |
| Messages | `/admin/forum-posts` | retrouver ce qu'un joueur a écrit, où que ce soit |
| Signalements | `/admin/forum-reports` | ouverts **et traités** |
| Journal | `/admin/forum-log` | toutes les décisions, filtrables |

La différence entre la file publique et la file d'administration est le périmètre :
un modérateur de salon ne voit que ses salons, un administrateur voit tout.

L'écran « Sujets » existe pour une raison précise : **la plupart des demandes de
modération portent sur un sujet déjà en ligne**, et on ne le retrouve pas dans une
file d'attente.

---

## 8. Permissions

| Permission | Ce qu'elle ouvre |
|---|---|
| `admin.forum.view` | les six écrans d'administration, en lecture |
| `admin.forum.manage` | les actions : approuver, masquer, déplacer, supprimer, trancher |
| `forum.moderate` | la modération de **tous** les salons, depuis le site public |
| `forum_permissions.can_moderate` | la modération d'**un** salon, par rôle |

L'accès à l'administration passe d'abord par `admin.dashboard` : un joueur ordinaire
est renvoyé au portail avant même que la permission de forum soit consultée.

---

## 9. Défauts relevés

| Réf. | Défaut | Portée |
|---|---|---|
| `FO-D1` | Une ligne de permission, quelle qu'elle soit, **fermait le salon aux visiteurs** : un salon « ouvert en lecture, réservé en écriture » était impossible à exprimer. La lecture est désormais publique tant que personne ne coche `can_view` (`F-02`). | **corrigé** |
| `FO-D10` | Les seize sujets officiels, rédigés en texte brut pour un forum qui affichait les messages échappés, s'affichaient en pavé une fois le forum passé au HTML : ni titres, ni listes, ni liens. Traduits à l'écriture par `PlainTextToHtml`. | **corrigé** |
| `FO-D2` | Un message supprimé reste lisible par les modérateurs, mais le **motif** de la suppression n'est visible que dans le journal, pas au fil de la discussion. | confort |
| `FO-D3` | Les compteurs sont recalculés à chaque écriture. Sur un salon de plusieurs milliers de sujets, cela fait deux agrégats par réponse. Acceptable aujourd'hui, à surveiller. | performance |
| `FO-D4` | `POST /forum/preview` est limité à trente appels par minute et par compte. La limite est un garde-fou, pas une mesure : elle n'a pas été éprouvée sous charge réelle. | à mesurer |
| `FO-D5` | L'historique des révisions n'est **consultable par aucun écran**. Il est écrit, il n'est pas lu. | manque |
| `FO-D6` | Sur un champ **pré-rempli**, l'éditeur levait une erreur de sélection qui **interrompait sa propre boucle d'écouteurs** : la recopie vers le champ soumis ne se faisait plus, et modifier un sujet ne changeait rien. Corrigé en remplaçant l'éditeur (voir `FO-D8`). | **corrigé** |
| `FO-D7` | Une liste à puces revenait **numérotée** après édition : l'éditeur n'écrivait qu'un type de liste et distinguait les deux par un attribut que le filtre ne garde pas. Le nouvel éditeur produit directement `<ul>` et `<ol>` ; les contenus déjà abîmés, eux, ne se réparent pas tout seuls. | **corrigé** |
| `FO-D8` | L'éditeur est passé de Quill 2 à TipTap (MIT, bâti sur ProseMirror). TinyMCE, demandé, a été écarté : sa version communautaire est sous GPL-2.0-or-later, et son éditeur vend une licence commerciale pour lever l'ambiguïté sur un site marchand. | **corrigé** |
| `FO-D9` | Les téléversements d'images de la boutique et des articles écrivaient sur le disque public du site mais renvoyaient une adresse `cloud.wizardmc.fr`, où le fichier n'existait pas : toute image ainsi ajoutée était introuvable. `cloud.wizardmc.fr` ne sert que les versions du client et du launcher. | **corrigé** |

---

## 10. Reste à faire

| Réf. | Piste |
|---|---|
| `FO-B1` | Un écran d'historique des révisions d'un message (`FO-D5`). |
| `FO-B2` | Une recherche. Le forum public n'en a **aucune** ; l'administration se contente d'un `LIKE`. |
| `FO-B3` | Un abonnement à un sujet, et la notification d'une réponse. |
| `FO-B4` | Un profil public qui liste les messages d'un joueur. |
| `FO-B5` | Le marquage « lu / non lu » par lecteur. |
