# Commit 3 — Reformulation slogans (meta-tags) + bullet modale REGISTER

*Date : 2026-06-19 · Fichier : `index.html` · Sauvegarde : `index.html.bak_commit3_2026-06-19`*

Troisième commit du refactoring voie B. Reformulation des 4 meta-tags marketing
+ 1 bullet de la modale REGISTER. Aucune autre modification.

## Métriques avant / après

| | Avant | Après |
|---|---|---|
| `wc -l index.html` | 3557 | 3557 (inchangé — remplacements mono-ligne) |
| Encodage | UTF-8 | UTF-8 (validé — accents é/à et em-dash — préservés) |

## Les 5 modifications (avant → après)

| # | Ligne | Avant | Après |
|---|---|---|---|
| 1 | 6 (`<title>`) | `OMNIS — Intelligence Financiere Institutionnelle` | `OMNIS — Le monde et les marchés, dans le même écran` |
| 2 | 7 (`meta description`) | `OMNIS — Intelligence financiere institutionnelle. 30 actifs, signaux geopolitiques, maritime, aerien.` | `Géopolitique mondiale, chocs macro et marchés financiers croisés. L'intelligence institutionnelle pour particuliers actifs.` |
| 3 | 8 (`og:title`) | `OMNIS — Intelligence Financiere` | `OMNIS — Le monde et les marchés, dans le même écran` |
| 4 | 9 (`og:description`) | `30 actifs analyses en temps reel. Geopolitique, maritime, donnees macro.` | `Croisez géopolitique, calendrier macro et marchés dans un seul écran. L'intelligence institutionnelle, pensée pour les particuliers actifs.` |
| 5 | 3454 (bullet REGISTER) | `• Historique des predictions` | `• Calendrier économique avec surprise factor` |

## Vérification grep — pré-modification (chacune = 1) ✅

```
Intelligence Financiere Institutionnelle : 1
30 actifs, signaux geopolitiques         : 1
OMNIS — Intelligence Financiere"         : 1
30 actifs analyses en temps reel         : 1
Historique des predictions               : 1
```

## Vérification grep — post-modification

Anciennes chaînes (chacune = 0) ✅ :
```
Intelligence Financiere Institutionnelle : 0
30 actifs, signaux geopolitiques         : 0
OMNIS — Intelligence Financiere"         : 0
30 actifs analyses en temps reel         : 0
Historique des predictions               : 0
```

Nouvelles chaînes ✅ :
```
Le monde et les marchés, dans le même écran : 2   (title + og:title — attendu)
Géopolitique mondiale, chocs macro          : 1
Croisez géopolitique, calendrier macro      : 1
Calendrier économique avec surprise factor  : 1
```

## Résidus identifiés mais NON modifiés (hors périmètre — traçabilité commit futur)

Conformément au brief (« lister sans modifier »). Ces éléments portent encore
l'ancien positionnement ou le vocabulaire « prédictions », mais sont **hors du
périmètre strict du commit 3** :

### A. Slogan ON-PAGE visible (à traiter en priorité — recommandation)
- **Ligne 3393** : `INTELLIGENCE FINANCIERE INSTITUTIONNELLE` — slogan **affiché**
  dans la page (un `<div>`, pas un meta-tag). Les meta-tags sont reformulés mais
  ce texte visible conserve l'ancien positionnement. **Non modifié** (non listé
  dans les 5 mods). → Candidat n°1 pour un commit de reformulation visuelle.

### B. Second bullet « prédictions » (autre liste de features)
- **Ligne 3217** : `✓ Historique des prédictions & taux de précision` — bullet
  dans une **autre** liste de features (distincte de la modale REGISTER du bullet
  #5 déjà modifié, ligne 3454). Même incohérence « voie B » mais non listé au brief.
  **Non modifié.**

### C. Vue PERFORMANCE (résidus prédictifs — commit dédié déjà prévu)
- Labels affichés : **618** `// PERFORMANCE // PREDICTIONS VERIFIEES`,
  **624** `PRÉDICTIONS VÉRIFIÉES`, **625** `MEILLEURE PRÉDICTION`,
  **635** `10 DERNIÈRES PRÉDICTIONS VÉRIFIÉES`, **638** `Les prédictions sont
  vérifiées J+14 après émission.`
- Code JS (champs API, **interdit de toucher**) : **2544/2559** `d.total_predictions`,
  **2560/2561** `d.meilleure_prediction`, **2579** `nb_predictions`, **2585** commentaire.
- **Non modifié** (vue PERFORMANCE = commit dédié futur, cf. commit 2).

### D. CSS `.pred-card`
- **Ligne ~2950** : `.pred-card { … }` — CSS inutilisé mixé dans le loader de la vue
  BRIEFING. Laissé en place (décision commit 2 : non isolé). **Non modifié.**

### E. Vue BRIEF — variables `*.signal` (statuts techniques)
- Non listées ici en détail (explicitement hors périmètre au brief : ce sont des
  statuts techniques, pas des prédictions). **Non touchées.**

## Hors périmètre (respecté)

- ❌ Aucun code JavaScript modifié (y compris `signal: AbortSignal.timeout(...)`).
- ❌ Vue PERFORMANCE, vue BRIEF, variables `*.signal` — non touchées.
- ❌ Slogan on-page (3393), 2e bullet (3217), `.pred-card` — listés, non modifiés.
- ❌ Aucun `git add/commit/push`, aucun déploiement Vercel.
- ✅ Sauvegardes précédentes conservées (`*.bak_2026-06-18`, `*.bak_commit2_*`, nouveau `*.bak_commit3_*`).

## Note pour Joachim

Le site affiche encore, **dans la page** (pas seulement les meta), l'ancien slogan
« INTELLIGENCE FINANCIERE INSTITUTIONNELLE » (ligne 3393) et un bullet « Historique
des prédictions » (ligne 3217). Les meta-tags sont alignés voie B, mais l'alignement
**visible** nécessitera un commit supplémentaire (A + B ci-dessus). Signalé sans
action, conformément au périmètre strict du commit 3.
