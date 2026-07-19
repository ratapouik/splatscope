# SPLATSCOPE

Viewer web de **Gaussian Splatting** (photogrammétrie par vidéo), hébergeable gratuitement sur **GitHub Pages**, avec un notebook **Google Colab** pour la reconstruction.

Le rendu et la visualisation tournent à 100 % dans le navigateur. La reconstruction (GPU) se fait une fois, hors-ligne, sur Colab gratuit.

---

## 1. Déployer le viewer sur GitHub Pages

1. Crée un dépôt (ex. `splatscope`) et pousse le contenu de ce dossier à la racine.
2. `Settings ▸ Pages ▸ Build and deployment` → Source : **Deploy from a branch**, branche `main`, dossier `/ (root)`.
3. Après ~1 min, l'appli est en ligne :
   `https://<TON-PSEUDO>.github.io/splatscope/`

Le fichier `.nojekyll` est déjà présent pour éviter tout traitement Jekyll.

## 2. Reconstruire une scène (Colab gratuit)

Ouvre `reconstruction.ipynb` dans Colab — remplace `<TON-PSEUDO>` dans ce badge une fois le repo en ligne :

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/<TON-PSEUDO>/splatscope/blob/main/reconstruction.ipynb)

Le notebook déroule : vidéo → images → COLMAP → `splatfacto` → `splat.ply`.

## 3. Publier une scène

Les fichiers de splats sont volumineux. **GitHub bloque tout fichier > 100 Mo**, et **Pages ne sert pas les fichiers Git LFS** (ne compte pas dessus).

- **< ~50 Mo** (après compression sur [superspl.at/editor](https://superspl.at/editor) → `.splat`/`.spz`) : dépose-le dans `models/`, puis charge-le dans l'appli via son URL ou le bouton *Ouvrir un fichier*.
- **Plus lourd** : attache-le comme *asset* d'une **Release GitHub** (jusqu'à 2 Go/fichier) et colle l'URL de l'asset dans le champ URL du viewer.

Formats acceptés par le viewer : `.ply`, `.splat`, `.ksplat`.

Le bouton **Nettoyer ↗** ouvre la scène courante directement dans [SuperSplat](https://superspl.at/editor) (bruit, points isolés, rognage...). Pour une scène chargée via URL, elle s'ouvre automatiquement ; pour un fichier local, l'éditeur s'ouvre nu et il suffit d'y glisser-déposer le fichier.

## 4. Visualiser un mesh (ex. export SuGaR)

Le bouton **Mesh SuGaR** (à côté d'*Ouvrir un fichier*) charge un `.ply` de **maillage** (triangles, normales, couleurs par sommet) — typiquement une reconstruction de surface via [SuGaR](https://github.com/Anttwo/SuGaR), à ne pas confondre avec un `.ply` de Gaussian Splatting (nuage de splats). Le rendu passe par un pipeline Three.js dédié (`PLYLoader` + `OrbitControls`), indépendant du viewer de splats.

## 5. Visualiser un nuage de points classique

Le bouton **Nuage de points** charge un `.ply` de sommets seuls (position + couleur, sans triangles ni propriétés Gaussian Splatting) — ex. un export COLMAP sparse/dense ou un scan LiDAR exporté en PLY. Même pipeline Three.js que le mode Mesh, mais rendu en `THREE.Points` plutôt qu'en surface pleine.

Ces trois modes (splat / mesh / nuage de points) lisent tous des fichiers `.ply`, mais avec des propriétés différentes : utilise le bon bouton selon le contenu réel du fichier, l'appli ne devine pas automatiquement.

## 6. Navigation et mesure

Une fois une scène chargée, deux panneaux apparaissent en haut à droite :

- **Navigation** — `Accueil` recentre sur le cadrage initial, `Dessus`/`Face`/`Profil` sautent à des vues prédéfinies autour du point de pivot courant (orientées par rapport au `up` de la caméra active, pas un Y-monde fixe, pour rester cohérentes en mode splat comme en mode mesh/points).
- **Mesure** — `Mesurer` active le mode clic : deux clics sur la scène tracent une ligne et affichent la distance dans le HUD (en unités de scène). `Calibrer` convertit cette distance en mètres en indiquant sa valeur réelle connue (utile en particulier pour les scènes Gaussian Splatting, reconstruites sans échelle réelle par défaut). `Effacer` retire la mesure.

Aire et volume ne sont pas encore supportés — pour le volume en particulier, la notion n'a de sens direct que sur un mesh fermé (pas un simple nuage de points/splats), donc à traiter séparément si besoin.

---

## Structure

```
index.html            # le viewer (Three.js + @mkkellogg/gaussian-splats-3d, via CDN)
reconstruction.ipynb  # pipeline Colab vidéo → .ply
models/               # y déposer les splats légers
.nojekyll
```

## Chaîne d'outils

- **COLMAP** — poses caméra (Structure-from-Motion)
- **Nerfstudio / splatfacto (gsplat)** — entraînement du splat, licence Apache-2.0
- **SuperSplat** — nettoyage/compression navigateur
- **mkkellogg/GaussianSplats3D** — rendu Three.js, licence MIT

## Note

Le rendu est **visuel, pas métrique**. Pour des mesures (volumes, distances géoréférencées), passer par une chaîne maillage : OpenDroneMap / OpenMVS.
