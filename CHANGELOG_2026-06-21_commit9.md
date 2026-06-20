# CHANGELOG — Commit 9 (2026-06-21)

## Suppression totale du système ticker (HTML + JS)

Neuvième et dernier commit de la finition propre du refactoring **voie B**.
Objectif : retirer le dernier résidu vivant — le ticker (`updateTicker` + barre
`#tickerbar`) — dont les deux sources de données sont mortes.

---

## Diagnostic

```
GET https://omnis-api-production.up.railway.app/api/polygon
→ HTTP 500 — Internal Server Error (cascade d'erreurs confirmée logs Railway)

GET https://omnis-api-production.up.railway.app/api/signal/all
→ HTTP 503 — endpoint officiellement désactivé
             (cron archivé le 18 juin, commit backend c9c15aa)
```

Les **deux** endpoints sur lesquels reposait `updateTicker()` (source Polygon +
fallback signaux) sont hors service.

Conséquences sur l'état actuel :
- Le ticker affiche `-- chargement prix --` puis `--` après échec des fetch.
- L'utilisateur ne voit jamais de vrais prix.
- Un `setInterval` frappe Railway toutes les 5 min (requêtes 500/503 inutiles).
- Le rendu du fallback affichait encore visiblement `HAUSSIER`/`BAISSIER`
  (couleurs vert/rouge) — contradictoire avec le positionnement voie B.

---

## Justification du choix (suppression totale > réparation backend)

La réparation de `/api/polygon` côté backend serait du **yak-shaving** sur un
élément cosmétique, non-différentiateur du produit OMNIS. La suppression totale est
plus simple, plus cohérente avec la voie B, et élimine les requêtes mortes vers
Railway. Si Polygon est réparé un jour, un nouveau ticker pourra être recréé
*from scratch* sur un endpoint propre voie B (prix simples, sans signaux).

---

## Inventaire pré-suppression (grep)

```
2919: // ══ TICKER BAR — vrais prix via /api/polygon, triés par |var_1j| ══
2920: async function updateTicker(){
2921:   const bar = document.getElementById('tickerbar');
2975:   const _tickerBar = document.getElementById('tickerbar');
2976:   if(_tickerBar) _tickerBar.innerHTML = ...
2979:   setTimeout(updateTicker, 3000);
2980:   setInterval(updateTicker, 5*60*1000);
3221: <!-- ══ TICKER BAR ══ -->
3222: <div id="tickerbar" ...>

polygon (avant) :
1398: grp.append('polygon')        ← SVG géométrie, CONSERVÉ
2919: commentaire updateTicker     ← supprimé
2926: fetch /api/polygon           ← supprimé
```

---

## Modifications

### Fichier modifié
- `index.html`

### Suppression 1 — Bloc JS complet (lignes 2919 → 2980)
- Commentaire `// ══ TICKER BAR — vrais prix via /api/polygon ══`
- Fonction `async function updateTicker(){ ... }` (source Polygon + fallback
  `/api/signal/all` + rendu `MKT`/`▲▼`/`HAUSSIER`/`BAISSIER`)
- `const _tickerBar = document.getElementById('tickerbar')` + innerHTML de chargement
- `setTimeout(updateTicker, 3000)`
- `setInterval(updateTicker, 5*60*1000)`

Raccord après édition :
```javascript
  CNH:'CNH=X', BRL:'BRL=X', INR:'INR=X', CHARBON:'KOL', NICKEL:'NI=F',
};

// ══ CANCEL SUBSCRIPTION ══
```

### Suppression 2 — Div HTML (lignes 3221 → 3227)
```html
<!-- ══ TICKER BAR ══ -->
<div id="tickerbar" style="position:fixed;bottom:0;left:0;right:0;height:20px;
     background:#000;border-top:1px solid #1a1a1a;display:flex;align-items:center;
     padding:0 12px;z-index:8000;flex-shrink:0;overflow:hidden">
  <span style="color:#445566;font-size:9px;margin-right:8px">MKT</span>
  <span style="font-size:9px;color:#445566">Chargement...</span>
</div>
```

Raccord après édition :
```html
  </div>
</div>

<!-- ══ LANDING PAGE ══ -->
```

---

## Non touché (volontairement)

- **Ligne 1398** `grp.append('polygon')` — polygone SVG (triangles militaires sur la
  carte), aucun rapport avec l'endpoint Polygon. **Conservé.**
- Ticker MKT **TradingView** de la vue MARCHÉS — widget externe, sans rapport.
- `signal: AbortSignal.timeout(...)` techniques ailleurs.
- BRIEF / Marcus Chen / `buildBriefingPrompt`.
- Toute autre fonction et div du fichier (modales, landing, statusbar).

---

## Vérifications

### Greps post-suppression
```
updateTicker     : 0
tickerbar        : 0
_tickerBar       : 0
TICKER BAR       : 0
/api/polygon     : 0
/api/signal/all  : 0
polygon          : 1   ← attendu (ligne 1398, SVG géométrie) ✅
```
> `grep "polygon"` retourne exactement **1** résultat, conforme à l'attendu.

### Équilibre syntaxique JS/HTML
```
{ : 883   } : 883    diff : 0
( : 2371  ) : 2371   diff : 0
```

### Lignes
```
AVANT : 3400 index.html
APRÈS : 3329 index.html   (−71 lignes)
```

---

## Sauvegarde locale

- `index.html.bak_commit9_2026-06-21` (copie intégrale avant édition, **non suivie**
  par git, conforme au pattern des commits précédents).

---

## BILAN FINAL — Refactoring frontend voie B (commits 1-9)

Le refactoring frontend voie B est désormais **structurellement ET fonctionnellement
complet**. Plus aucun résidu de l'ancien produit prédictif ne subsiste.

| Commit | Objet |
|--------|-------|
| 1 (`b267cce`) | retrait JS prédictif mort |
| 2 (`2744f44`) | suppression vue PRED |
| 3 (`03db8b0`) | reformulation slogans + meta-tags |
| 4 (`30eb4df`) | alignement visible voie B |
| 5 (`7260c0e`) | suppression vue PERFORMANCE |
| 6 (`e857bc6`) | suppression predictions.html |
| 7 (`e0ac9af`) | suppression push notifications + setInterval mort |
| 8 (`bc8b018`) | nettoyage CSS .pred-card |
| 9 (présent)   | suppression totale du ticker (HTML + JS) |

> **Note futur** : si `/api/polygon` est réparé un jour, un nouveau ticker peut être
> recréé from scratch sur un endpoint propre voie B (prix simples, sans signaux
> ni `HAUSSIER`/`BAISSIER`).

---

## À vérifier par Joachim avant commit

- Charger le site en local / preview : plus de barre fixe en bas d'écran.
- Onglet Réseau : plus aucune requête `/api/polygon` ni `/api/signal/all`.
- Le reste du `<body>` (landing, modales, statusbar, carte SVG) intact.
- La carte affiche toujours ses triangles militaires (polygone SVG préservé).
