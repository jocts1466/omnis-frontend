# Commit 4 — Alignement visible voie B (landing + pricing + alerts + statusbar)

*Date : 2026-06-19 · Fichier : `index.html` · Sauvegarde : `index.html.bak_commit4_2026-06-19`*

Quatrième commit : reformulation de **tout le marketing visible** (statusbar, modale
ALERT, landing header/stats/bullets, modale PRICING) pour aligner la copie publique
sur la voie B (« infrastructure d'intelligence macro & géopolitique, sans prédictions
servies »). 7 zones, aucune autre modification.

## Métriques avant / après

| | Avant | Après |
|---|---|---|
| `wc -l index.html` | 3557 | 3556 (−1 : ligne `RULES 113` retirée) |
| Encodage | UTF-8 | UTF-8 (validé — accents, em-dash —, entités `&amp;`/`&nbsp;` préservés) |
| Indicateurs statusbar | 5 (LIVE/FEEDS/VESSELS/AIRCRAFT/RULES) | 4 (LIVE/FEEDS/VESSELS/AIRCRAFT) |

## Les 7 modifications (avant → après)

**M1 — Statusbar (retrait pur, ligne 210)** : suppression de
`<span id="sb-rules">RULES 113</span>`. (Aucun JS ne remplissait `sb-rules` : grep=0.)

**M2 — Modale ALERT (ligne 274)** :
`Recevez une alerte quand un signal fort est détecté.` →
`Recevez une alerte quand un événement macro ou géopolitique majeur est détecté.`

**M3 — Landing header (3393-3394)** :
- `INTELLIGENCE FINANCIERE INSTITUTIONNELLE` → `LE MONDE ET LES MARCHÉS, DANS LE MÊME ÉCRAN`
- `GEOPOLITIQUE // MARCHES // MARITIME // AERIEN` → `GEOPOLITIQUE // MACRO // MARCHES // MARITIME`
- (1re `<div>` logo OMNIS intacte.)

**M4 — Landing stats (3405-3406)** :
- `id="lp-rules">113` → `id="lp-zones">16`
- `REGLES CAUSALES` → `ZONES SURVEILLEES`
- (id renommé `lp-rules`→`lp-zones` ; aucun JS ne remplissait `lp-rules` : grep=0.)

**M5 — Bullets GRATUIT (3427-3431)** :
- `• Signaux basiques` → `• Géopolitique mondiale (zones principales)`
- `• Explications causales —` → `• Calendrier macro complet —`
- `• MAP maritime / aerien —` → `• MAP maritime / aérien —`
- `• Alertes email —` → `• Alertes macro & géopol —`

**M6 — Bullets PREMIUM (3449-3454)** :
- `• 30 actifs complets` → `• 30 actifs suivis (matières, indices, crypto, FX)`
- `• Explications causales detaillees` → `• Géopolitique mondiale temps réel`
- `• MAP maritime et aerienne` → `• Calendrier macro avec surprise factor`
- `• Alertes email temps reel` → `• MAP maritime & aérienne`
- `• Newsletter quotidienne` → `• Briefing quotidien IA`
- `• Calendrier économique avec surprise factor` → `• Newsletter quotidienne`
  (réordonnancement + remplacement « Alertes email » par « Briefing quotidien IA »)

**M7 — Modale PRICING (3212-3217)** : 6 `<li>` reformulés (30 actifs suivis /
Géopolitique mondiale GDELT+tension / Calendrier macro surprise factor / MAP
maritime & aérienne anomalies / Briefing quotidien IA / Newsletter). Le bullet
`Historique des prédictions & taux de précision` est **supprimé** (remplacé).

## Vérification grep — pré-modification (chacune = 1) ✅

```
">RULES 113</span>                            : 1
Recevez une alerte quand un signal fort       : 1
INTELLIGENCE FINANCIERE INSTITUTIONNELLE      : 1
GEOPOLITIQUE // MARCHES // MARITIME // AERIEN  : 1
REGLES CAUSALES                               : 1
• Signaux basiques                            : 1
• Explications causales detaillees            : 1   (NB : sans accents — cf. note)
• Alertes email temps reel                    : 1
Alertes email sur signaux forts               : 1
Historique des prédictions &amp; taux…        : 1
```
> **Note** : la chaîne de vérif du brief `• Explications causales détaillées` (avec
> accents) renvoyait 0 — c'est une coquille du brief : le bullet PREMIUM est
> `detaillees` (sans accents), et `Explications causales détaillées` (avec accents)
> est le `<li>` PRICING. Les deux blocs existaient bien (1 chacun), modifiés en M6/M7.

## Vérification grep — post-modification

Anciennes chaînes (chacune = 0) ✅ : `RULES 113`, `signal fort est détecté`,
`INTELLIGENCE FINANCIERE INSTITUTIONNELLE`, `GEOPOLITIQUE // MARCHES // MARITIME // AERIEN`,
`REGLES CAUSALES`, `• Signaux basiques`, `• Explications causales detaillees`,
`• Alertes email temps reel`, `Alertes email sur signaux forts`, `Historique des prédictions`.

Ids orphelins (= 0) ✅ : `sb-rules` = 0, `lp-rules` = 0, `RULES 113` = 0.

Nouvelles chaînes ✅ :
```
LE MONDE ET LES MARCHÉS, DANS LE MÊME ÉCRAN  : 1
GEOPOLITIQUE // MACRO // MARCHES // MARITIME  : 1
ZONES SURVEILLEES / lp-zones                  : 1 / 1
Briefing quotidien IA                         : 2   (landing premium + pricing)
Calendrier macro avec surprise factor         : 2   (landing premium + pricing)
Géopolitique mondiale temps réel              : 2   (landing premium + pricing)
```

Statusbar vérifiée : LIVE, FEEDS, VESSELS, AIRCRAFT (+ horloge) — 4 indicateurs.

## Résidus restants après commit 4 (NON modifiés — traçabilité commits futurs)

`grep -niE "signal|prédiction|prediction"` — tous **hors périmètre** (catégories
explicitement exclues par le brief) :

### A. Vue PERFORMANCE (commit dédié futur)
- Labels : **617** `// PERFORMANCE // PREDICTIONS VERIFIEES`, **623** `PRÉDICTIONS VÉRIFIÉES`,
  **624** `MEILLEURE PRÉDICTION`, **634** `10 DERNIÈRES PRÉDICTIONS VÉRIFIÉES`,
  **637** `Les prédictions sont vérifiées J+14…`
- JS (champs API, interdits) : **2543/2558** `total_predictions`, **2559/2560**
  `meilleure_prediction`, **2578** `nb_predictions`, **2584/2590** commentaires/`signal_date`.

### B. Vue BRIEF / signal-strip (projet dédié — interdit)
- **494-499** `briefing-signal-strip`, `SIGNAL OMNIS`, `strip-signal`
- **1681/1737/1739-1740** `updateSignalStrip`, `intel.signal`
- **2788** `_intel.signal`, **2794** `SIGNAL OMNIS TEMPS REEL`

### C. JS technique `signal:` (sans rapport — interdit)
- `signal: AbortSignal.timeout(...)` : lignes **914, 955, 987, 1053, 1098, 1178,
  1298, 1678, 1696, 2250, 2315, 2378, 2401, 2672** (paramètre d'annulation fetch).
- `maritimeData.signal` / `flightData.signal` (**1305-1306**) : statuts techniques.
- **1281** commentaire « endpoints Premium (maritime, flights, signals) ».

### D. CSS `.pred-card` (déjà connu, non isolé — laissé)

## Hors périmètre (respecté)

- ❌ Vue PERFORMANCE, vue BRIEF, push notifications, alertes backend, variables
  `*.signal` / `AbortSignal` — non touchés.
- ❌ Aucun `git add/commit/push`, aucun déploiement Vercel.
- ✅ Sauvegardes conservées : `*.bak_2026-06-18`, `*.bak_commit2/3/4_2026-06-19`.

## Bilan visible

Après ce commit, un visiteur sur omnis.finance voit un positionnement cohérent voie B :
slogan « LE MONDE ET LES MARCHÉS, DANS LE MÊME ÉCRAN », stats « ZONES SURVEILLEES »,
bullets centrés géopolitique/macro/MAP/briefing, sans aucune mention de « signaux
prédictifs » ou « historique des prédictions » dans le marketing public. Restent à
traiter en commits dédiés : la **vue PERFORMANCE** (stats « prédictions vérifiées »)
et le **projet BRIEF**.
