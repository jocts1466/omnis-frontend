# Commit 1 — Retrait des modules ML/prédictifs morts du frontend

*Date : 2026-06-18 · Fichier : `index.html` · Sauvegarde : `index.html.bak_2026-06-18`*

Premier d'une série de commits incrémentaux alignant le frontend sur la voie
« infrastructure d'intelligence sans prédictions servies ». Le backend a déjà
désactivé les endpoints prédictifs ; le frontend les appelait encore (erreurs
silencieuses en console). Ce commit supprime ces appels morts.

## Métriques avant / après

| | Avant | Après | Δ |
|---|---|---|---|
| `wc -l index.html` | 4422 | 3997 | **−425 lignes** |
| `grep -nE 'fetch\|api.omnis.finance\|/api/' \| wc -l` | 61 | 53 | **−8 appels** |

## Appels API supprimés (8 occurrences, 5 endpoints morts)

| Endpoint mort | Occurrences retirées |
|---|---|
| `/api/backtest` | 2 |
| `/api/ml/predict` | 2 |
| `/api/predict/brent` | 2 |
| `/api/ml/train` | 1 |
| `/api/historique/signal` | 1 |

Vérifié : `grep -c` = **0** pour chacun de ces 5 endpoints après modification.

## Fonctions JS supprimées (7, bloc contigu lignes 1769-2177)

| Fonction | Statut avant suppression |
|---|---|
| `initAnalyse()` | Vue « analyse » **orpheline** (jamais déclenchée — aucun onglet n'appelle `switchTab('analyse')`). Appelait `/api/backtest`, `/api/ml/predict`, `/api/predict/brent`. |
| `reloadAnalyse()` | Orpheline (aucun `onclick`), appelait `initAnalyse()`. |
| `loadMLTrain()` | Appelée seulement par `initAnalyse` (morte). Endpoint `/api/ml/train`. |
| `loadPrediction()` | Appelait `/api/predict/brent` + `/api/backtest` + `/api/ml/predict` (Promise.all). |
| `updateAnalyseView()` | Appelée seulement par `loadPrediction`. Écrivait dans des DOM `an-*` **inexistants**. |
| `loadSignalHistorique()` | Endpoint `/api/historique/signal`. |
| `renderSignalChart()` | Appelée seulement par `loadSignalHistorique`. Ciblait `#signal-chart` **inexistant** (`if(!svg) return`). |

Variable globale retirée : `analyseInited` (utilisée seulement par les fonctions ci-dessus).
Headers de section retirés : `// ── Prédiction Brent + Backtest ──`, `// ── Vue ANALYSE ──`,
`// ── SIGNAL HISTORIQUE — Courbe D3 90 jours ──`.

## Câblages (appels) supprimés

| Emplacement | Retiré |
|---|---|
| `switchTab()` | 2 lignes `if(id==='analyse') initAnalyse();` (vue orpheline) |
| init vue MARCHES | `loadSignalHistorique(); loadPrediction();` + commentaire |
| handler `resize` | `window.addEventListener('resize', …)` qui appelait `loadSignalHistorique()` |

## Éléments HTML supprimés

**Aucun.** Constat d'audit important : les zones d'affichage de ces modules
(`an-brent-prix`, `an-ml-dir`, `an-bt-prec`, `an-pred-*`, `pred-j1-val`,
`ml-predict`, `bt-precision`, `signal-chart`, `signal-hist-info`…) **n'existaient
déjà plus** dans le HTML — référencées uniquement en JS via `getElementById`
(retours `null`, gardés par `if(el)`). Un nettoyage HTML antérieur les avait
retirées sans retirer le JS associé. Il ne restait donc que du JS mort appelant
des endpoints morts. Rien à retirer côté HTML/sections/divs.

## CSS supprimé

**Aucun.** Pas de classe CSS retirée (les classes `.an-pred-*` n'étaient utilisées
que par des éléments DOM déjà inexistants ; laissées en place pour ne prendre
aucun risque sur du CSS potentiellement partagé — conforme à la consigne).

## Contrôles de non-régression effectués

- ✅ `grep -c` = 0 pour les 5 endpoints morts.
- ✅ `grep -c` = 0 pour chaque nom de fonction/variable supprimé (`initAnalyse`,
  `reloadAnalyse`, `loadMLTrain`, `loadPrediction`, `updateAnalyseView`,
  `loadSignalHistorique`, `renderSignalChart`, `analyseInited`, `testEl`) →
  **aucune référence orpheline**.
- ✅ `switchTab` intact : les 8 vues (`map`, `predictions`, `marches`, `briefing`,
  `news`, `calendrier`, `performance`, `portfolio`) toujours câblées.
  `loadPredView()` (vue PRED) **non touché** (objet du commit 2).
- ✅ Balises `<script>`/`</script>` équilibrées (4/4), fin de fichier intacte.
- ✅ Init MARCHES et zone `setInterval`/`appendChild` intactes après retrait des câblages.

## Hors périmètre (respecté)

- ❌ Vue PRED (`view-predictions`, `loadPredView`, ids `pred-sym/pred-prob/…`) — commit 2.
- ❌ `predictions.html` — non modifié (mtime inchangé).
- ❌ Slogans / meta-tags / titres — commit 3.
- ❌ Modales login/register/account — non touchées.
- ❌ CSS partagé — non touché.
- ❌ Aucun `git add/commit/push`, aucun déploiement.

## À noter pour Joachim

La « vue analyse » était entièrement morte (orpheline + DOM inexistant). Les
fonctions `loadPrediction`/`loadSignalHistorique` étaient, elles, encore *appelées*
(init MARCHES + resize) mais écrivaient dans des éléments inexistants tout en
tapant les endpoints morts — d'où les erreurs silencieuses. Tout est désormais
retiré proprement. Sauvegarde conservée : `index.html.bak_2026-06-18`.
