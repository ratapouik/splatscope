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
