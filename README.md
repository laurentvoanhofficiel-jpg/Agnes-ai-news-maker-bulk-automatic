

# 🎬 NewsReel Studio

> **Transformer l'actualité en un journal visuel généré par IA — satire, cinéma et engagement politique en 8 tableaux.**

[![Made with Love](https://img.shields.io/badge/Made%20with-%E2%9D%A4-red)](https://github.com)
[![Single File](https://img.shields.io/badge/Single%20File-100%25%20HTML-blue)]()
[![No Backend](https://img.shields.io/badge/Backend-Aucun-green)]()
[![Zero Build](https://img.shields.io/badge/Build-Aucun-orange)]()

---

## ✨ Ce qui rend ce code source fantastique

Ce projet tient dans **un seul fichier HTML de moins de 3000 lignes** et pourtant il fait ce que des plateformes entières tentent de faire avec des équipes de 20 personnes :

- **Recherche d'actualités réelles en direct** via une cascade intelligente de 3 APIs publiques gratuites (Free News API → Newsline → GDELT), sans clé, sans inscription, avec CORS ouvert. Le système **mémorise automatiquement** quel endpoint fonctionne le mieux et le rétrograde en cas d'échec (score `ok/(ok+fail)` persistant dans `localStorage`).

- **Analyse éditoriale profonde par IA** : au lieu de générer des images génériques, le modèle reçoit chaque actualité et la découpe en **8 scènes narratives distinctes** — la une, les décideurs, le citoyen lambda, les conséquences, les médias, une métaphore, un angle absurde, la conclusion. Les 8 légendes mises bout à bout forment un **véritable éditorial en 8 tableaux**.

- **Vision politique personnalisable** : l'utilisateur peut déclarer son orientation (raccourcis gauche/centre/droite/national/libertaire/communiste ou texte libre détaillé). L'IA garde les **faits exacts** mais colore **l'interprétation** dans ce sens — un vrai journaliste engagé, jamais haineux.

- **16 styles visuels pré-inclus** : Wes Anderson, Tim Burton, Bong Joon-ho, Tarantino + affiche punk, TikTok hook, affiche de cinéma, théâtre, BD franco-belge, New Yorker, Banksy, propagande soviétique, Aardman claymation, LEGO, manga, caricature Mad. **Chaque style est un simple prompt texte** — ajoutable en 5 secondes via l'interface.

- **Génération parallèle massive** : toutes les clés API Agnes saisies par l'utilisateur sont mises dans un **pool rotatif** avec gestion de cooldown 65s sur erreur 429 et détection de clé morte (3× 401/403). 16 workers consomment la queue en parallèle — 80 images générées en quelques minutes au lieu de plusieurs heures.

- **Persistance IndexedDB intégrale** : sessions, images (en Blob), métadonnées, narrations, historique des éditions. Le bouton « Tout télécharger » reconstruit de vrais noms de fichiers (`newsreel_titre_v1_20261012_143022.jpg`) et déclenche 80 téléchargements en cascade avec barre de progression.

- **Zéro dépendance, zéro backend, zéro build**. Un `index.html`, un navigateur, c'est tout. On peut l'ouvrir depuis un téléphone en `file://` et il fonctionne.

- **Design sombre élégant** : palette or/noir, overlays plein écran avec progression réelle, confirmations inline (jamais de `confirm()` laid), boutons avec spinner intégré, animations douces.

---

## 🔧 Comment ça marche

### Vue d'ensemble du pipeline

```
┌─────────────────────────────────────────────────────────────────┐
│  1. RECHERCHE ACTUALITÉS                                        │
│     Sujet libre → Free News API → parse → 25 titres français    │
│     (fallback : Newsline → GDELT)                               │
├─────────────────────────────────────────────────────────────────┤
│  2. SÉLECTION MULTIPLE                                          │
│     L'utilisateur coche les news qui l'intéressent              │
├─────────────────────────────────────────────────────────────────┤
│  3. VISION POLITIQUE                                            │
│     Raccourci + texte libre → injecté dans le prompt IA         │
├─────────────────────────────────────────────────────────────────┤
│  4. STYLE VISUEL                                                │
│     Un des 16 styles (ou style custom ajouté par l'utilisateur) │
├─────────────────────────────────────────────────────────────────┤
│  5. ANALYSE IA PAR ACTUALITÉ                                    │
│     Agnes 2.5 Flash analyse → 8 scènes narratives               │
│     Chaque scène = {titre, légende politique, prompt anglais}   │
├─────────────────────────────────────────────────────────────────┤
│  6. GÉNÉRATION IMAGES PARALLÈLE                                 │
│     Pool de clés × 16 workers → queue → Agnes Image 2.1         │
│     Chaque image stockée en Blob dans IndexedDB                 │
├─────────────────────────────────────────────────────────────────┤
│  7. GALERIE + ÉDITION                                           │
│     Affichage groupé par news + narration progressive           │
│     Édition : texte / modifier IA / insérer image / fusionner   │
├─────────────────────────────────────────────────────────────────┤
│  8. TÉLÉCHARGEMENT                                              │
│     Blob → <a download> avec nom unique par image               │
└─────────────────────────────────────────────────────────────────┘
```

### Les fichiers d'état

- **`localStorage`** : clés Agnes, styles personnalisés, scores des endpoints news, vision politique
- **`IndexedDB`** (`newsreel_studio`) : 3 stores
  - `sessions` : métadonnées de chaque session (query, style, vision, liste news)
  - `images` : pour chaque image `{id, blob, dataUrl, url, meta:{...}}`
  - `kv` : divers

### Le pool de clés Agnes

```javascript
// Rotation intelligente
function acquireAgnesKey() {
  // Priorité : clé active + pas en cooldown + délai 1.2s respecté
  // Tri par (count ASC, lastReq ASC) → répartition équitable
  // Cooldown 65s sur 429
  // Désactivation après 3 erreurs 401/403
}
```

### La cascade des endpoints news

```javascript
function sortedEndpoints() {
  // Tri par score = ok/(ok+fail)
  // → Free News API monte si elle marche, descend si elle échoue
  // → GDELT monte automatiquement en cas de panne de Free News
}
```

---

## 🚀 Installation

```bash
# 1. Cloner ou télécharger le fichier
wget https://.../newsreel-studio.html

# 2. Ouvrir dans un navigateur moderne (Chrome / Firefox / Safari / Edge)
open newsreel-studio.html

# 3. C'est tout.
```

**Aucune installation. Aucun serveur. Aucun build. Aucun `npm install`.**

### Configuration requise dans l'app

1. Coller une ou plusieurs clés Agnes (`sk-...`) — https://platform.agnes-ai.com
2. (Optionnel) Décrire sa vision politique
3. Chercher un sujet → sélectionner → générer

---

## 🐛 Bug connu : l'édition d'image ne fonctionne pas en local

**Statut** : ⚠️ **NON RÉSOLU** — à réparer en priorité

### Symptômes

Quand on clique sur ✏️ Éditer puis qu'on applique une modification (texte, modification IA, insertion, fusion), l'appel à l'API Agnes échoue silencieusement. Le journal technique affiche :

```
✕ : Échec Agnes API
✕ : Échec Agnes API
✕ : Échec Agnes API
```

### Cause probable

1. **CORS sur les URLs d'images retournées par Agnes** — Agnes renvoie parfois des URLs `https://...agnes-ai.com/...` qui ne sont pas accessibles depuis un contexte `file://` ou depuis un autre domaine. Les tentatives de `fetch(url, {mode:'cors'})` échouent avec `Failed to fetch`.

2. **Data URL trop volumineux** — Quand on essaie de contourner via `blobToDataURL()`, le data URL peut dépasser la limite de body envoyée à `/v1/images/generations` (souvent ~2-4 Mo). Le serveur rejette alors la requête.

3. **Format `image` attendu par Agnes** — L'endpoint attend probablement un tableau `["data:image/jpeg;base64,..."]`, mais la structure exacte (avec ou sans préfixe `data:`, JPEG ou PNG, dimension max) n'est pas documentée publiquement et varie selon les versions.

### Pistes de réparation

```javascript
// Test 1 : vérifier la taille du data URL
console.log('Taille envoi :', (srcData.length / 1024 / 1024).toFixed(2), 'Mo');
// Si > 2 Mo → compresser davantage avant envoi
```

```javascript
// Test 2 : forcer un format plus léger
async function getItemDataUrl(item) {
  var blob = await ensureBlob(item);
  // Recompresser en JPEG qualité 0.7 max 1024px AVANT envoi
  return await compressBlobToDataUrl(blob, 1024, 0.7);
}
```

```javascript
// Test 3 : si Agnes exige une URL, utiliser un proxy CORS pour la source
// Mais si Agnes exige vraiment une URL http accessible, il faut un backend
```

### Solution recommandée

**En local (`file://`)** : le navigateur bloque beaucoup de choses. Il faut soit :

1. **Servir le fichier via un mini-serveur local** (`python3 -m http.server` ou `npx serve`), ce qui débloque la plupart des CORS. C'est la solution la plus simple.

2. **Compresser les images à max 800px / qualité 0.65** avant envoi — le data URL passe alors sous les 300 Ko et l'API l'accepte.

3. **Vérifier la doc Agnes** — l'endpoint `/images/generations` accepte peut-être un paramètre `image_url` au lieu de `image` (base64). Il faut tester.

**En production (déployé sur un domaine HTTPS)** : tout fonctionne sauf si Agnes refuse les requêtes cross-origin depuis ton domaine. Dans ce cas, il faut un petit proxy backend Node/Python de 20 lignes qui relaie les appels.

---

## 🌐 20 idées de sites similaires inspirés de ce code

| # | Nom | Description |
|---|-----|-------------|
| 1 | **PolitiToon** | Journal satirique quotidien : chaque matin, un résumé de l'actu en 10 caricatures générées, style BD franco-belge. |
| 2 | **CryptoReal** | Décrypte les news crypto en 8 images cyberpunk — le trader, le régulateur, le hacker, le HODLer, la SEC… |
| 3 | **EcoScope** | Actualité climatique en aquarelles New Yorker, ton alarmiste mais élégant. |
| 4 | **SportZine** | Chaque match de Ligue 1 → 8 affiches façon Panini années 80 avec commentaires éditoriaux. |
| 5 | **SciFiFeed** | Vulgarisation scientifique : une news scientifique → 8 planches de BD scientifique à la Dr Stone. |
| 6 | **GéoPolis** | Géopolitique mondiale en cartes illustrées style propagande vintage, avec analyse par région. |
| 7 | **CinéOuest** | Comparateur : mêmes news, 5 styles de réalisateurs (Wes Anderson, Fincher, Nolan…) côte à côte. |
| 8 | **MangaNews** | Actualité japonaise en style manga shōnen : les politiques deviennent des héros de combats. |
| 9 | **KidsActu** | Actualité pour enfants en claymation Aardman — pédagogique, drôle, sans effrayer. |
| 10 | **VintageTimes** | Chaque actualité rejouée comme si elle se passait en 1968 — photo argentique, typo Figaro, ton gaullien. |
| 11 | **CyberScope** | Cybersécurité en images cyberpunk néon, ambiance Blade Runner, satirique. |
| 12 | **FakeReel** | Générateur de FAUSSES unes satiriques (Le Gorafi visuel) avec watermark explicite. |
| 13 | **HistorIA** | Actualité récente rejouée comme si elle se déroulait en 1789, 1914, 1968 — voyage temporel visuel. |
| 14 | **CinéRoman** | Transformation d'une news longue en roman-photo façon Nous Deux années 70. |
| 15 | **AstroScope** | Actualité spatiale en images rétro-futuristes (années 50, style NASA vintage). |
| 16 | **MemeMachine** | Transforme chaque news en 8 memes prêts à poster, style rage comic ou deep-fried. |
| 17 | **PlayMobile** | Actualité vue par des Playmobil — instantanément viral sur les réseaux. |
| 18 | **LegoNews** | Tout en LEGO : la news du jour en diorama miniature style LEGO Ideas. |
| 19 | **PixelPolitik** | Actualité en pixel art 16 bits façon RPG japonais — les politiques sont des boss. |
| 20 | **Philosophia** | Chaque news reliée à un concept philosophique, illustré en gravure ancienne. |

---

## 💡 20 idées d'amélioration du code existant

| # | Amélioration | Effort | Impact |
|---|--------------|--------|--------|
| 1 | **Réparer le bug d'édition** (voir section précédente) | Moyen | 🔥🔥🔥 |
| 2 | Mode **hors-ligne** : autoriser la génération sans clé Agnes (mode démo avec images placeholder) | Facile | 🔥 |
| 3 | **PWA** : manifest + service worker → installable sur mobile, fonctionne hors-ligne | Facile | 🔥🔥 |
| 4 | Ajouter un **bouton "Copier la légende"** sous chaque image (pour partage rapide Twitter/LinkedIn) | Trivial | 🔥 |
| 5 | **Drag & drop** des images dans la galerie pour les réordonner avant téléchargement | Moyen | 🔥 |
| 6 | Support du **format carré 1:1** en plus du 9:16 (Instagram) | Facile | 🔥 |
| 7 | **Export ZIP** de toute une session (avec JSZip) au lieu de N téléchargements | Facile | 🔥🔥 |
| 8 | **Génération d'une vidéo slideshow** à partir des 8 images (WebCodecs API) | Difficile | 🔥🔥🔥 |
| 9 | **Mode comparaison** côte à côte de deux styles sur la même actualité | Moyen | 🔥🔥 |
| 10 | **Historique des prompts** avec rejouer/modifier un ancien prompt | Facile | 🔥 |
| 11 | **Partage par URL** : encoder la config dans le hash (`#q=climat&style=wes`) | Facile | 🔥🔥 |
| 12 | **Dark/light mode** toggle | Trivial | 🔥 |
| 13 | **Compteur de tokens consommés** par session (via `usage` de l'API) | Facile | 🔥 |
| 14 | **Détection automatique de la langue** de la query → adapter le prompt système | Facile | 🔥 |
| 15 | **Regénération sélective** : ne régénérer que les images en erreur | Trivial | 🔥 |
| 16 | **Filtres de news** : par date, par source, par pertinence | Facile | 🔥 |
| 17 | **Aperçu du prompt final** avant envoi (debug) | Trivial | 🔥 |
| 18 | **Annotations manuelles** sur les images (dessin, flèches) via Canvas | Moyen | 🔥 |
| 19 | Support du **watermark** optionnel (nom du site, URL) | Facile | 🔥 |
| 20 | **Barre de recherche globale** dans toutes les sessions sauvegardées | Moyen | 🔥 |

---

## ⚡ 20 idées de très grande optimisation

| # | Optimisation | Gain estimé | Complexité |
|---|--------------|-------------|------------|
| 1 | **Web Workers** pour les appels API (UI jamais bloquée) | Fluidité × 3 | Moyen |
| 2 | **Compression AVIF/WebP** automatique des blobs avant stockage | −70 % espace IDB | Facile |
| 3 | **Cache LRU des images** en mémoire (max 50) avec éviction | RAM × 2 | Moyen |
| 4 | **Batching des requêtes Agnes** : 4 images par call au lieu de 1 | × 4 vitesse | Difficile |
| 5 | **Streaming SSE** pour voir les images apparaître au fur et à mesure | UX × 5 | Difficile |
| 6 | **Prefetch intelligent** : générer la 9e image pendant que l'user regarde les 8 premières | UX | Moyen |
| 7 | **Retry adaptatif** : si 429 persiste, backoff exponentiel par clé ET par IP | Fiabilité +40 % | Moyen |
| 8 | **Détection auto du débit max** de l'API Agnes par clé (RPM) | +20 % débit | Moyen |
| 9 | **Pool de workers dynamique** : jusqu'à 32 si assez de clés, 4 si 1 seule | Scaling | Facile |
| 10 | **IndexedDB chunked** : splitter les gros blobs > 5 Mo en chunks de 1 Mo | Fiabilité IDB | Moyen |
| 11 | **Compression LZ4** des métadonnées JSON en IDB | −50 % IDB | Moyen |
| 12 | **Service Worker** pour servir le HTML depuis le cache | Démarrage × 10 | Facile |
| 13 | **WASM (Rust)** pour le traitement d'image local (crop, watermark, resize) | × 5 vitesse | Difficile |
| 14 | **Edge functions** (Cloudflare Workers) comme proxy Agnes | Contourne CORS | Moyen |
| 15 | **Redis KV** (Upstash) pour partager les endpoints scores entre utilisateurs | Fiabilité collective | Moyen |
| 16 | **WebGPU** pour l'analyse visuelle des images générées (détection de NSFW, qualité) | Précision | Difficile |
| 17 | **Vector DB locale** (sqlite-wasm + vecteurs) pour recherche sémantique dans les anciennes sessions | UX | Moyen |
| 18 | **Diff streaming** : envoyer à l'UI chaque pixel au fur et à mesure (SSE + base64) | UX temps réel | Difficile |
| 19 | **Prédiction de scènes** : cache des analyses IA récurrentes (même sujet = même analyse) | −80 % tokens | Facile |
| 20 | **Format de sauvegarde `.newsreel`** (binaire compact signé) pour partager sessions entre users | Viralité | Moyen |

---

## 📜 Licence

MIT — Fais-en ce que tu veux. Si tu construis un site cool avec, partage-le.

## 🙏 Remerciements

- **Agnes AI** pour les modèles texte + image abordables et rapides
- **Free News API**, **Newsline**, **GDELT** pour les actualités libres
- Tous les journalistes et dessinateurs de presse dont ce projet s'inspire

---

**⭐ Si ce projet te plaît, laisse une étoile. Si tu répares le bug d'édition, ouvre une PR.**
```
