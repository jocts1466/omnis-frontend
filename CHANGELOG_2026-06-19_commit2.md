# Commit 2 — Suppression pure de la vue PRED

*Date : 2026-06-19 · Fichier : `index.html` · Sauvegarde : `index.html.bak_commit2_2026-06-19`*

Deuxième commit du refactoring voie B. Suppression **pure** (sans remplacement) de
la vue PRED : onglet de menu, bloc HTML de la vue, et toutes les fonctions/globales
JS qui la servaient.

## Métriques avant / après

| | Avant (post-commit 1) | Après | Δ |
|---|---|---|---|
| `wc -l index.html` | 3997 | 3557 | **−440 lignes** |
| Onglets de navigation (`nav-tab`) | 8 | **7** | −1 (PRED) |

## HTML supprimé

| Élément | Ligne d'origine | Détail |
|---|---|---|
| Bouton de menu PRED | 221 | `<button class="nav-tab" onclick="switchTab('predictions',this)">PRED</button>` |
| Bloc complet de la vue | 473–559 (88 lignes) | `<!-- ══ PRÉDICTIONS ══ -->` + `<div class="view" id="view-predictions">` … `</div>` (cols gauche/centre/droite : `pred-left`, `pred-center`, `pred-right`, et tous les ids `pred-sym`, `pred-dir`, `pred-prob`, `pred-horizon`, `pred-amp`, `pred-share-btn`, `pred-tv-container`, `pred-causes`, `pred-macro`, `pred-maritime`, `pred-corr`, `pred-events`, `pred-wstates`, `pred-accuracy`, `pred-search`, `pred-asset-list`, `pred-header`) |

## Routage `switchTab` modifié

- Retrait de `'predictions'` du tableau de masquage des vues (ligne 804).
  Nouveau : `['map','marches','briefing','news','calendrier','performance','portfolio']`.
- Suppression de la ligne `if(id==='predictions') loadPredView();` (ligne 822).
- MAP reste la vue par défaut (`class="view active"`, dispatch intact). Les 6 autres
  vues (marches, briefing, news, calendrier, performance, portfolio) **inchangées**.

## Fonctions JS supprimées (3 groupes, 13 fonctions + 4 globales)

Les fonctions PRED étaient **interleavées** avec des fonctions non-PRED (ticker,
push, compte) qui ont été **préservées**. Retrait par groupe :

**Groupe 1 (HTML chart, ~3136–3214) :**
- `_drawPredChart(prices, direction, containerId)`
- `_loadPredChart(omnisSym, direction)`
- global `let _chartRefreshTimer` (utilisé uniquement par le chart PRED)

**Groupe 2 (partage + liste, ~3308–3370) :**
- global `let _currentPredSig`
- `shareCurrentSignal()`
- `filterPredAssets(query)`
- `_loadPredAccuracy(symbol)`
- `_loadPredChartServer(symbol, direction)`

**Groupe 3 (vue + rendu, ~3426–3633) :**
- globals `let _predSignals`, `let _predSelected`
- `loadPredView()`
- `_renderPredList()`
- `_selectPredAsset(sym)`
- `_renderPredDetail(sig)` (incl. fonction imbriquée `_renderCauses(cs)`)

**Préservées (non-PRED, entre les groupes) :** `updateTicker`, `requestPushPermission`,
`sendPushNotification`, `confirmCancel`, `openChangePassword`, `submitChangePassword`,
`toggleNewsletter` — toutes vérifiées présentes après coup.

## CSS supprimé

**Aucun.** Un seul sélecteur `.pred-card` subsiste (ligne ~2950) — voir « Résidus »
ci-dessous. Aucun CSS dédié à la vue PRED dans le `<style>` de `<head>`.

## Vérification anti-régression (Étape 4)

`grep -c` = **0** pour : `view-predictions`, `loadPredView`, `pred-sym`, `pred-prob`,
`_drawPredChart`, `_loadPredChart`, `shareCurrentSignal`, `filterPredAssets`,
`_renderPredDetail`, `_predSignals`, `_chartRefreshTimer`, `switchTab('predictions`.

Intégrité structurelle :
- `<script>`/`</script>` équilibrés (4/4), points de jonction propres aux 5 sites de retrait.
- 7 boutons `nav-tab` (MAP, MKT, BRIEF, NEWS, CAL, PERF, PTF), MAP toujours `active`.
- Fonctions non-PRED préservées (vérifiées une à une).

## Résidus « predictions » NON retirés — hors périmètre (à noter pour Joachim)

`grep -nE 'predictions|pred-'` retourne encore **5 hits**, tous **hors du périmètre
du commit 2** :

| Ligne (≈) | Contenu | Raison de conservation |
|---|---|---|
| 2544, 2559 | `d.total_predictions` | Champ de réponse API de la **vue PERFORMANCE** (perf-metrics). Retirer casserait PERF. |
| 2579 | `${a.nb_predictions} prédictions` | Affichage **vue PERFORMANCE** (précision par actif). |
| ~624, ~635 | labels `PRÉDICTIONS VÉRIFIÉES` | Copie de la **vue PERFORMANCE**. |
| ~3454 | `• Historique des predictions` | Puce marketing dans la **modale register/pricing** → interdit ce commit (modales + slogans = commit 3). |
| ~2950 | `.pred-card { … }` | CSS **inutilisé** (aucun élément `class="pred-card"`), mais **mixé** dans le `<style>` du loader de la vue BRIEFING (avec `@keyframes bar` utilisé). Le brief (Étape 5) dit de ne retirer le CSS PRED que **s'il est isolé** → il ne l'est pas → conservé. |

**Observation (sans action) :** la vue PERFORMANCE affiche encore des statistiques
« prédictions vérifiées ». C'est cohérent avec l'ancien backend prédictif, désormais
désactivé — un futur commit pourrait revoir cette vue. **Hors périmètre du commit 2.**

## Hors périmètre (respecté)

- ❌ Slogans / meta-tags / og:title / og:description — non touchés (commit 3).
- ❌ `predictions.html` — non touché.
- ❌ Modales login/register/account — non touchées.
- ❌ Vue MAP (par défaut) — non touchée, reste `class="view active"`.
- ❌ Aucun `git add/commit/push`, aucun déploiement Vercel.
- ✅ Sauvegarde conservée : `index.html.bak_commit2_2026-06-19`.
