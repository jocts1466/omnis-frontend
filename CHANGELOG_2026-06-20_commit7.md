# CHANGELOG — Commit 7 (2026-06-20)

## Suppression du système de push notifications + setInterval mort

Septième étape de la finition du refactoring **voie B**.
Objectif : retirer le système de notifications navigateur, qui annonçait des
« signaux forts » disparus du produit (commits 1-5) et bouclait toutes les 15 min
sur un endpoint désormais en panne.

---

## Justification

1. **Notifications « OMNIS — Signal fort » obsolètes** : le titre des notifications
   navigateur annonce des signaux, alors que tous les signaux ont été retirés du
   produit dans les commits 1 à 5. Message en contradiction directe avec la voie B.

2. **`setInterval` mort qui frappe un endpoint 503** : une boucle toutes les 15 min
   appelait `GET /api/signal/all?token=...` (filtre `s.probability >= 80`) pour
   déclencher les notifications. Or cet endpoint retourne **503 depuis le 18 juin**
   (cron DXY désactivé). Le code faisait donc des requêtes inutiles à Railway toutes
   les 15 min pour chaque utilisateur connecté.

3. **`requestPushPermission()`** : poussait l'utilisateur à accepter des notifications
   navigateur qui ne viendraient jamais.

---

## Modifications

### Fichier modifié
- `index.html`

### Bloc retiré (lignes 2989 → 3017, soit 29 lignes + 1 ligne vide attenante)

Contexte AVANT (conservé) :
```javascript
setTimeout(updateTicker, 3000);
setInterval(updateTicker, 5*60*1000);   // ← ticker prix, CONSERVÉ
```

Bloc SUPPRIMÉ :
```javascript
/* ── AMÉLIORATION 3 : Push Notifications navigateur ── */
function requestPushPermission(){
  if('Notification' in window && Notification.permission === 'default'){
    Notification.requestPermission();
  }
}
function sendPushNotification(signal){
  if(!('Notification' in window) || Notification.permission !== 'granted') return;
  new Notification('OMNIS — Signal fort', {
    body: `${signal.symbol} ${signal.direction} ${signal.probability}%`,
    icon: '/favicon.ico',
    tag:  signal.symbol,
  });
}
// Vérification alertes toutes les 15 min (push + email)
setInterval(async ()=>{
  const tok = localStorage.getItem('omnis_token');
  if(!tok || !_currentUser) return;
  try{
    const r = await fetch(API_BASE+'/api/signal/all?token='+encodeURIComponent(tok));
    const d = await r.json();
    const minProb = 80;
    (d.signals||[]).forEach(s=>{
      if(s.probability >= minProb && s.direction !== 'NEUTRE'){
        sendPushNotification(s);
      }
    });
  } catch(e){}
}, 15*60*1000);
```

Contexte APRÈS (conservé) :
```javascript
// ══ CANCEL SUBSCRIPTION ══
async function confirmCancel(){
```

> Le commentaire structurant `/* ── AMÉLIORATION 3 ── */` était isolé (il ne
> coiffait que ce bloc) : retiré avec le bloc.

---

## Non touché (volontairement)

- **Alertes email** (`submitAlertSubscription`, `/api/alerts/subscribe`,
  `min_probability=` ligne 1013, modale ALERT) — système distinct, reformulé en
  commit 4 (« événement macro ou géopolitique majeur »). **Reste en place.**
- `Notification` côté Stripe / billing / payment-toast — autre contexte.
- `signal: AbortSignal.timeout(...)` — technique, sans rapport.
- BRIEF / Marcus Chen — chantier séparé.
- CSS `.pred-card` — prévu commit 8.

---

## Résidus identifiés (NON touchés, à traiter en commit séparé futur)

- **Ligne 2953** : `/api/signal/all` subsiste dans le fallback de `updateTicker` :
  ```javascript
  const rs = await fetch(API_BASE + '/api/signal/all?lang=fr' + tokP, {signal: AbortSignal.timeout(6000)});
  ```
  Cette logique de fallback « signaux » du ticker sera reformulée dans un commit
  ultérieur. Non modifiée ici (hors périmètre commit 7).

---

## Vérifications

### Greps post-suppression (tous à 0)
```
requestPushPermission   : 0
sendPushNotification    : 0
OMNIS — Signal fort     : 0
AMÉLIORATION 3          : 0
Signal fort             : 0
minProb                 : 0
```

### Équilibre syntaxique JS
```
{ : 909   } : 909   diff : 0
( : 2412  ) : 2412  diff : 0
```

### Lignes
```
AVANT : 3437 index.html
APRÈS : 3407 index.html   (−30 lignes)
```

---

## Sauvegarde locale

- `index.html.bak_commit7_2026-06-20` (copie intégrale avant édition, **non suivie**
  par git, conforme au pattern des commits précédents).

---

## À vérifier par Joachim avant commit

- Charger le site en local / preview : plus aucune requête `/api/signal/all` toutes
  les 15 min dans l'onglet Réseau (hors fallback ticker au chargement).
- Plus aucune demande de permission de notification navigateur.
- Le ticker de prix (`updateTicker`) continue de fonctionner.
- Les alertes email restent fonctionnelles (modale ALERT).
