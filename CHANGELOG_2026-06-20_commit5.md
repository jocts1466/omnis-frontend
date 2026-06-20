# Commit 5 — Suppression pure de la vue PERFORMANCE

*Date : 2026-06-20 · Fichier : `index.html` · Sauvegarde : `index.html.bak_commit5_2026-06-20`*

Dernier gros chantier du refactoring voie B : suppression de la vue PERFORMANCE
(promesse non tenue — l'endpoint `/api/performance/public` ne renvoie que l'état
« accumulation », le backend prédictif étant désactivé). Suppression **pure**,
sans remplacement.

## Métriques avant / après

| | Avant | Après |
|---|---|---|
| `wc -l index.html` | 3556 | 3437 (**−119 lignes**) |
| Onglets de navigation | 7 | **6** (MAP, MKT, BRIEF, NEWS, CAL, PTF) |
| Références « prédiction/prediction » dans tout le fichier | plusieurs | **0** |

## Inventaire pré-modification (grep)

```
switchTab('performance'  : 224 (bouton menu)
id="view-performance"    : 615
id="perf-…"              : 621,622,623,624,630,635,643 (metrics/precision/total/best/by-asset/recent/accumulation)
loadPerformance          : 618 (bouton ACTUALISER), 725 (dispatch), 2532 (def)
OMNIS_API                : 2533 (uniquement dans loadPerformance)
'performance' (hors abortsignal) : 224, 615, 714 (hide-array), 725
```

## Éléments supprimés

| # | Élément | Lignes d'origine | Détail |
|---|---|---|---|
| 1 | Bouton menu PERF | 224 | `<button … switchTab('performance',this)>PERF</button>` |
| 2 | Bloc HTML de la vue | 614–649 (37 l.) | `<!-- ══ PERFORMANCE ══ -->` + `<div id="view-performance">` … `</div>` (tous les `perf-*` : metrics, precision, total, best, by-asset, recent, accumulation + bouton ACTUALISER) |
| 3 | `switchTab` hide-array | 714 | retrait de `'performance'` → `['map','marches','briefing','news','calendrier','portfolio']` |
| 4 | `switchTab` dispatch | 725 | `if(id==='performance') loadPerformance();` |
| 5 | Fonction JS `loadPerformance()` | 2531–2609 (80 l.) | commentaire `// ── PERFORMANCE ─` + `async function loadPerformance(force=false)` … `}` (incl. `window.OMNIS_API`, fetch `/api/performance/public`, rendu by_asset/recent) |

## Vérification post-modification — tous à 0 ✅

```
perf-                    : 0
loadPerformance          : 0
view-performance         : 0
switchTab('performance   : 0
OMNIS_API                : 0
/api/performance/public  : 0
```

## Cohérence & intégrité ✅

- **6 onglets** : MAP, MKT, BRIEF, NEWS, CAL, PTF. (PERF retiré.)
- Vue MAP toujours `class="view active" id="view-map"` (1 occurrence).
- `switchTab` : dispatch intact pour marches/news/calendrier/portfolio/map ; plus
  aucune branche `performance`.
- **Équilibre des accolades/parenthèses** sur tout le fichier : `{}` 921/921 (diff 0),
  `()` 2432/2432 (diff 0). Balises `<script>` 4/4. UTF-8 valide.
- Points de jonction propres : HTML (vue portfolio → marqueur PRICING), JS
  (`savePositions()` → `initPortfolio()`).

## Bug pré-existant résolu automatiquement

Le bug `loadPerformance` ligne ~2546 (`aEl.closest('.padding')` → `TypeError` car
aucun parent `.padding` n'existe) est **supprimé de fait** avec la fonction. Plus
aucun chemin de code ne peut le déclencher.

## Résidus restants après commit 5 (NON modifiés — hors périmètre)

- **Aucun** résidu « prédiction/prediction » : `grep -niE "prédiction|prediction"`
  retourne **0**. Le frontend ne contient plus aucune référence visible ni JS à des
  « prédictions vérifiées » ou « stats de signaux prédictifs ».
- **Vue BRIEF / signal-strip** (chantier dédié, interdit) : ~14 occurrences
  (`briefing-signal-strip`, `updateSignalStrip`, `strip-signal`, `SIGNAL OMNIS`,
  `intel.signal`). **Non touchées.**
- **JS technique** : ~20 `signal: AbortSignal.timeout(...)` (annulation fetch) +
  `maritimeData.signal`/`flightData.signal` (statuts techniques). **Non touchés.**
- **Push notifications** (`sendPushNotification`) — commit dédié futur. Non touché.
- **CSS `.pred-card`** (loader BRIEFING, non isolé) — laissé.
- **`predictions.html`** à la racine — chantier séparé, non touché.

## Hors périmètre (respecté)

- ❌ BRIEF / Marcus Chen / `buildBriefingPrompt`, push notifs, `signal: AbortSignal`,
  `predictions.html`, vue MAP — non touchés.
- ❌ Aucun `git add/commit/push`, aucun déploiement Vercel.
- ✅ Sauvegardes conservées : `*.bak_2026-06-18`, `*.bak_commit2/3/4/5_2026-06-*`.

## Bilan refactoring voie B (commits 1→5)

Le frontend est désormais **structurellement et visiblement** aligné voie B : plus
aucune vue, fonction JS, ni copie marketing évoquant des « prédictions servies » ou
« signaux prédictifs vérifiés ». Restent comme chantiers dédiés explicitement
hors-scope : le **projet BRIEF** (persona Marcus Chen / signal-strip) et les **push
notifications**.
