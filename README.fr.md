[English](README.md) · **Français**

# Générateur de manifestation : une foule en 1-bit, générative

Un outil d'animation générative et procédurale qui remplit l'écran d'une foule de personnages en pixel art noir et blanc, qui défilent de gauche à droite. Chaque manifestant est unique : visage, corps, tenue, démarche et gestes sont générés à partir de règles et d'un hasard à graine. Certains agitent un bras, d'autres tiennent une pancarte vierge.

C'est un seul fichier HTML, sans compilation ni dépendance à installer. Il fonctionne dans n'importe quel navigateur récent, sur ordinateur comme sur téléphone.

L'outil fait partie de **Ball from the Block**, un jeu vidéo de foot cyberpunk pour la console portable TrimUI Brick. C'est pour cela qu'il dessine en noir et blanc pur et qu'il exporte des planches de sprites prêtes à découper dans un jeu.

> 🔗 **Démo en ligne :** https://guillaumiemartin-lgtm.github.io/generateur-manifestation/

## Deux pages

| Page | Rôle |
|---|---|
| **Défilé** (page d'accueil) | La manifestation. Sur téléphone : en plein écran, réglages dans une barre transparente en bas. Sur tablette à l'horizontale ou ordinateur : réglages à gauche, aperçu à droite. |
| **Personnages** | L'éditeur de personnages : réglages à gauche, aperçus à droite (empilés sur téléphone). On y va avec « 👤 Personnages », on revient avec « ▶ Défilé ». |

Une seule page tourne à la fois : la manifestation se met en pause pendant qu'on édite les personnages, et reprend là où elle en était.

## Page Défilé (page d'accueil)

- Manifestation en plein écran, sur un fond de la couleur de ton choix, avec en haut un titre gribouillé qui tremble. Le titre est modifiable ; par défaut, c'est « Bismillah c'est les ténèbres ».
- **Agrandissement entier, sans perte** (×1, ×2, ×3…). En mode auto, la foule occupe environ un tiers de la hauteur de l'image.
- **Résolution de sortie** (tablette à l'horizontale et ordinateur) : taille de la zone d'aperçu, 1920×1080, 1280×720, 4K, 1080×1920 vertical, 1080×1350, 1080×1080, ou personnalisée. Si la résolution dépasse l'écran, seul **l'aperçu** est réduit (le pourcentage est affiché). L'image et la vidéo exportées gardent la pleine résolution. Sur téléphone, la résolution de sortie est celle de l'écran, en pixels physiques.
- **Barre de réglages** transparente en bas de l'écran :
  - **toujours visibles :** fond, taille d'affichage (− / + / auto), position, lignes de sol, titre, « ⚙ foule » (affiche ou masque le bloc foule), « 👤 Personnages » ;
  - **bloc foule :**
    - source des passants (aléatoires, bibliothèque ou personnage actuel), vitesse ;
    - nombre de passants (1 à 200), rangées (1 à 8), décalages vertical et horizontal des rangées ;
    - cadence aléatoire, **variété de démarche**, part de bras levés, part de pancartes, part de silhouettes féminines ;
    - taille des personnages **Petit / Moyen / Grand** (32×48, 48×72, 64×96 px), réglages du cycle de marche (images, fps, pas, genou, balancier des bras, affaissement) ;
    - vrai plein écran du navigateur ;
  - **⇩ Télécharger**, en bas du bloc foule, avec son échelle d'export (×1 à ×4) ;
  - **📷 Image** (PNG de toute la scène) et **● Vidéo** (5 à 60 s, MP4 ou WebM selon le navigateur), à la résolution de sortie.
- Sur téléphone, la barre se **replie** jusqu'à ne garder qu'un curseur : le nombre de passants. Sur grand écran, le panneau de réglages reste ouvert à gauche.
- **Transitions douces :**
  - moins de passants : la foule s'éclaircit sur plusieurs cycles de marche, car ceux qui sortent ne sont pas remplacés ;
  - plus de passants : les nouveaux arrivent par la gauche ;
  - les décalages de rangées glissent jusqu'à leur nouvelle valeur ;
  - le renouvellement complet de la foule se fait en fondu tramé 1-bit (trame de Bayer 4×4).

### Ce que contient « Télécharger »

Un ZIP nommé `manifestation_<n>_passants.zip` qui contient, pour **chaque passant visible à l'écran** :

- `passant_<numéro>_<nom>_<anim>.gif` : un cycle de marche complet en **GIF animé** qui boucle, noir et blanc sur fond transparent, à la vitesse propre du passant ;
- `passant_<numéro>_<nom>_<anim>.png` : les mêmes images côte à côte, en **planche de sprites** pour un jeu ou un logiciel d'animation ;

et un fichier `manifeste.json` qui donne, pour chaque passant, son animation, ses fps, son facteur de vitesse, son avance horizontale par cycle (pour que les pieds ne glissent pas si on le fait avancer ailleurs) et tous ses paramètres.

## Page Personnages

- Éditeur de personnages. Chaque paramètre a une valeur actuelle, une plage min/max pour le tirage 🎲 et un verrou.
  - Têtes génératives (superellipses irrégulières), yeux, nez, sourcils, bouche, extras cyber (implants, antennes, crêtes…).
  - Gabarit du corps, tenue, silhouette standard ou féminine (jupe, taille fine, cheveux longs).
- Trois animations : **marche**, **marche + bras agité**, **marche + pancarte** (pancarte vierge sur un bâton).
- Aperçu animé avec pelure d'oignon, images nommées (contact, descente, passage, montée), grille de variantes, mutations.
- Bibliothèque des personnages gardés (import et export JSON).
- Export d'un personnage :
  - une planche de sprites avec une ligne par animation ;
  - un portrait qui tremble (3 images) ;
  - des métadonnées JSON pour découper la planche dans un jeu.

## Comment ça marche

C'est de la création générative et procédurale : on ne dessine aucun personnage, on fixe des règles et des plages, et le système produit des variations à l'infini.

- **Têtes** : polygones en superellipse (du rond au carré), déformés par un bruit à graine, avec un *boil* sur 3 images (tremblé du trait).
- **Marche** : interpolation Catmull-Rom périodique entre les poses clés classiques de l'animation. Les membres sont calculés par trigonométrie. La vitesse de chaque passant découle de la longueur de son pas, pour que le pied d'appui ne glisse pas.
- **Variété de démarche** : chaque passant reçoit des tirages fixes qui modulent son pas, la flexion du genou, le balancier, l'affaissement et le rythme du bras agité autour des réglages communs. Le nombre de battements reste entier à chaque cycle, pour que les boucles soient parfaites.
- **Rendu** : rasteriseur 1-bit maison (encre / papier / transparent), avec un contour extérieur et des coutures entre les parties du corps.
- **Scène** : un seul canvas à la résolution de sortie, composé du fond, de la foule (dessinée en pixels de sprite puis agrandie sans lissage) et du titre. La vidéo est enregistrée directement depuis ce canvas avec `MediaRecorder`.
- **Encodeur GIF et écriture du ZIP** : écrits dans la page, sans bibliothèque (GIF en LZW, 1-bit avec transparence et boucle infinie ; ZIP non compressé avec CRC-32). Les GIF ont été vérifiés pixel par pixel avec un décodeur de référence, et les ZIP avec `unzip -t`.

## Lancer en local

Aucune installation. Au choix :

- ouvrir `index.html` directement dans un navigateur ;
- ou servir le dossier (recommandé, pour que la police web se charge partout) :

  ```bash
  python3 -m http.server 8000
  # puis ouvrir http://localhost:8000
  ```

## Publier avec GitHub Pages

1. Pousse ce dépôt sur GitHub. GitHub Pages est gratuit pour les dépôts publics.
2. Va dans **Settings → Pages**.
3. Dans **Source**, choisis **Deploy from a branch**, puis la branche `main` et le dossier `/ (root)`.
4. Au bout d'une minute, l'outil est en ligne sur `https://<ton-pseudo>.github.io/<nom-du-depot>/`.

### Sur téléphone

- **Android et ordinateur :** le bouton « ⛶ plein écran » passe le navigateur en vrai plein écran.
- **iPhone :** Safari n'autorise pas le vrai plein écran pour une page web. Utilise plutôt **Partager → Sur l'écran d'accueil**. La page se déclare comme une application web : lancée depuis l'écran d'accueil, elle s'ouvre sans les barres de Safari, sous le nom « Manif ».
- Avec beaucoup de passants en taille Grand, un téléphone ancien peut ralentir. Baisse le nombre de passants ou passe en Petit.

## Données et vie privée

Tout se passe dans ton navigateur. Les réglages et la bibliothèque de personnages sont enregistrés dans le `localStorage` du navigateur, et rien n'est envoyé à un serveur. Chaque visiteur a sa propre bibliothèque ; rien n'est partagé. La seule ressource externe est la police **Rock Salt** de Google Fonts, utilisée pour le titre gribouillé. Sans connexion, le titre utilise une police manuscrite locale.

## Structure du projet

```
index.html     tout l'outil (HTML + CSS + JavaScript)
README.md      présentation en anglais
README.fr.md   ce fichier (français)
LICENSE        licence MIT
```

## Crédits

- Conception et direction : **Martin Guillaumie**.
- Police : [Rock Salt](https://fonts.google.com/specimen/Rock+Salt) de Sideshow (Font Diner), chargée depuis Google Fonts. Elle est publiée sous licence Apache 2.0 et n'est pas incluse dans ce dépôt. Rock Salt est une marque de Font Diner, Inc DBA Sideshow.

## Licence

Le code est publié sous [licence MIT](LICENSE) © 2026 Martin Guillaumie.
