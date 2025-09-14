## Contexte rapide

Ce dépôt contient un site front-end statique (HTML/CSS/vanilla JS) pour "Masco Énergie et Construction".
- Pages principales en HTML dans la racine (`index.html`, `article_2.html`) et dans le dossier `pages/` (`article-1.html`, `project-kisangani.html`, ...).
- Styles personnalisés dans `pages/style.css` et `pages/Galerie.css` + Tailwind chargé via CDN dans `index.html`.
- Ressources images dans `images/` et vidéos dans `vidéos/`.
- Fichiers JSON `data/*.json` présents mais actuellement vides / non utilisés.

## But pour un agent IA

Fournir des modifications sûres et ciblées au front-end statique : ajouter/mettre à jour pages HTML, optimiser CSS/JS, corriger l'accessibilité, ajouter images, et documenter conventions. Éviter de lancer des outils de build inexistants.

## Architecture & principaux patterns (à connaître)

- Page unique "hub" : `index.html` est la page centrale; beaucoup de scripts et comportements (carrousel hero, lightbox, modals, mobile menu) sont enlined dedans. Préférer modifier `index.html` pour changements fonctionnels globaux.
- CSS : `pages/style.css` contient la majeure partie des styles (layouts, animations, breakpoints). Tailwind est utilisé ponctuellement via CDN — mais des classes utilitaires sont souvent complétées par CSS personnalisé ; ne pas remplacer aveuglément le CSS personnalisé par Tailwind.
- Galerie : `pages/Galerie.css` contient le pattern du composant carousel/selection (inputs radio + labels). Garder la structure HTML/CSS existante si vous modifiez la galerie.
- Animations : attributs `data-anim` (valeurs vues: `zoom-in`, `fade-up`, `fade-left`, `fade-right`) et classes `stagger` / `stagger-item` sont utilisés par l'observer JS pour orchestrer entrées animées ; ajouter ces attributs/classes pour nouveau contenu à animer.
- Hero backgrounds : éléments `.hero-background` (stackés) et l'interval JS contrôlent leur rotation. Pour ajouter une image, insérer un nouveau `<div class="hero-background" style="background-image:url('../images/xxx')"></div>`.
- Modals & lightbox : pattern répété — wrapper `.modal-backdrop` (display flex/none) + `.modal` interne. Les scripts attendent des IDs spécifiques (`partner-modal`, `gallery-lightbox`, `gallery-current`, etc.). Respecter les IDs existants quand vous ajoutez des modals.
- Navigation : deux zones (desktop `#main-nav` et mobile `#mobile-menu`). Si vous ajoutez un lien de page, mettez-le dans les deux emplacements et conservez les ancres `href="#xxx"` pour le comportement smooth-scroll.

## Flux de données et contenu

- Articles / blog : les articles visibles proviennent de fichiers HTML dans `pages/` (ex. `pages/article-1.html`) plutôt que des JSON. Les JSON dans `data/` sont présents mais vides — ne pas compter dessus sans les remplir.
- Images : déposer dans `images/` et référencer par chemins relatifs (depuis `pages/` : `../images/nom.jpg`; depuis la racine `index.html` parfois `../images/` ou `images/` selon contexte). Vérifier le chemin relatif avant de committer.

## Workflow développeur (local)

- Pas de build : servir simplement le dossier racine comme site statique.
  - Option rapide (PowerShell) : `python -m http.server 8000` depuis `d:\frontend` puis ouvrir `http://localhost:8000/index.html`.
  - Recommandé en édition : utiliser l'extension VS Code "Live Server" pour rechargements automatiques.

## Règles et conventions spécifiques

- JS inline : beaucoup de logique est inlined dans `index.html`. Pour changements importants, extraire proprement dans `pages/scripts.js` et inclure-le, mais d'abord rechercher usages d'ID/classe (ex. `gallery-grid`, `partner-modal`, `mini-ad-track`).
- Accessibilité : les modals utilisent `aria-hidden` et gèrent `Escape` — maintenir ces attributs lors de modifications. Les images importantes ont `alt`; conservez-les.
- Classes d'animation : appliquez `data-anim` et `stagger`/`stagger-item` pour obtenir comportements existants.
- Responsive : breakpoints CSS sont `@media (min-width: 768px)` (md) et `1024px` (lg) ; vérifier ces règles dans `pages/style.css` lorsque vous modifiez la mise en page.

## Exemples concrets (copy-paste utiles)

- Ajouter une hero image :
  `<div class="hero-background" style="background-image:url('../images/nouvelle.jpg');"></div>` (puis vérifier rotation automatique dans le script hero).
- Ajouter un élément animé en séquence :
  Parent: `<section class="stagger" data-anim="fade-up">` enfants: `<div class="stagger-item">…</div>`.
- Créer une nouvelle page article :
  1) Ajouter `pages/article-x.html` en copiant `pages/article-1.html`.
  2) Lier depuis `index.html` (ajouter dans `#blog` un `article` similaire) et dans `#main-nav` si nécessaire.

## Tests / validation rapide

- Pas de suite de tests intégrée. Validation minimale recommandée :
  - Ouvrir `index.html` via serveur local et vérifier console navigateur pour erreurs JS.
  - Vérifier affichage mobile (DevTools) et l'interaction du menu mobile.

## Limitations & notes

- Ne pas supprimer le CSS personnalisé sans tester — le site compte sur des styles sur-mesure malgré la présence de Tailwind.
- `data/*.json` est présent mais vide : si une tâche veut migrer le blog vers JSON-driven, commencer par backfilling et modifier les scripts pour charger ces fichiers.

Si une partie est incomplète (ex. mapping des IDs nouveaux/attendus ou comportement JS non documenté), dites-moi quelle zone vous voulez que j'inspecte ou améliore et j'itérerai.
