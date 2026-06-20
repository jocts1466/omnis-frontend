# CHANGELOG — Commit 8 (2026-06-20)

## Suppression de la règle CSS morte `.pred-card`

Huitième et dernier commit de la finition propre du refactoring **voie B**.
Objectif : retirer le dernier vestige CSS de l'ancienne vue PRED.

---

## Justification

- La règle CSS `.pred-card { ... }` était définie (ligne 2830) mais le sélecteur
  n'était appliqué **nulle part** dans le HTML (`class="pred-card"` → aucun résultat).
- C'est de la **dette CSS pure**, vestige de la vue PRED supprimée au commit 2.
- La règle était logée dans le bloc `<style>` interne du spinner de chargement de
  `generateBriefing()` (« Marcus Chen analyse les marchés... »), mélangée avec
  l'animation légitime `@keyframes bar`.

---

## Modifications

### Fichier modifié
- `index.html`

### Règle CSS retirée (lignes 2830 → 2836)
```css
.pred-card {
  background: #111;
  border: 1px solid #1e1e1e;
  border-radius: 2px;
  padding: 4px 8px;
  min-width: 60px;
}
```

### Préservé explicitement dans le même bloc `<style>`
```css
@keyframes bar{from{transform:scaleY(.3);opacity:.4}to{transform:scaleY(1);opacity:1}}
```
> `@keyframes bar` anime le spinner du BRIEF (barres `animation:bar ...`) — **conservé**.

Contexte après édition :
```javascript
    <style>@keyframes bar{from{transform:scaleY(.3);opacity:.4}to{transform:scaleY(1);opacity:1}}
</style>`;
```

---

## Non touché (volontairement)

- `@keyframes bar` (même `<style>`) — préservé.
- Le reste du template literal de `generateBriefing()` (spinner, prompt, fetch BRIEF).
- Les autres blocs `<style>` du fichier.

---

## Vérifications

### Greps
```
pred-card        : 0   (était 1 avant)
@keyframes bar   : 1   (préservé)
<style>          : 3   (inchangé)
</style>         : 3   (inchangé)
```

### Équilibre syntaxique JS/CSS
```
{ : 908   } : 908   diff : 0
```
> Passage de 909 → 908 paires : exactement la paire d'accolades de `.pred-card`.

### Lignes
```
AVANT : 3407 index.html
APRÈS : 3400 index.html   (−7 lignes)
```

---

## Sauvegarde locale

- `index.html.bak_commit8_2026-06-20` (copie intégrale avant édition, **non suivie**
  par git, conforme au pattern des commits précédents).

---

## Bilan — Refactoring frontend voie B (commits 1-8)

La finition propre du refactoring voie B est **structurellement terminée** :

| Commit | Objet |
|--------|-------|
| 1 (`b267cce`) | retrait JS prédictif mort |
| 2 (`2744f44`) | suppression vue PRED |
| 3 (`03db8b0`) | reformulation slogans + meta-tags |
| 4 (`30eb4df`) | alignement visible voie B |
| 5 (`7260c0e`) | suppression vue PERFORMANCE |
| 6 (`e857bc6`) | suppression predictions.html |
| 7 (`e0ac9af`) | suppression push notifications + setInterval mort |
| 8 (présent)   | suppression CSS mort `.pred-card` |

> Résidu connu hors périmètre (documenté commit 7) : `/api/signal/all` subsiste dans
> le fallback de `updateTicker` (ligne ~2946), à reformuler dans un commit ultérieur.
