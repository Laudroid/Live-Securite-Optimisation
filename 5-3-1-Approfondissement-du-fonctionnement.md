# Séance 5 – Optimisation du code frontend et bonnes pratiques  
## Partie 3 – Node.js : event loop, async/await vs Promises, éviter le blocage  
### 1. Approfondissement du fonctionnement de l'Event Loop de Node.js  

---

### A. Introduction à l’Event Loop  

L’**Event Loop** est au cœur de la gestion des opérations asynchrones en Node.js. C’est un mécanisme qui permet à Node.js d’exécuter du code non bloquant, malgré son modèle à thread unique, en orchestrant les tâches synchrones, les opérations asynchrones, les callbacks et les micro-tâches.  

---

### B. Architecture générale de l'Event Loop  

Node.js s’appuie sur la bibliothèque libuv qui gère une boucle d’événements divisée en plusieurs phases :  

1. **Timers** : exécution des callbacks programmés par `setTimeout` et `setInterval` lorsque leur délai est écoulé.  
2. **Pending Callbacks** : callbacks I/O différés (par exemple, erreurs de sockets TCP).  
3. **Idle, prepare** : phase d’entretien interne.  
4. **Poll** : récupération des événements nouveaux I/O et exécution de leurs callbacks.  
5. **Check** : exécution des callbacks de `setImmediate`.  
6. **Close callbacks** : fermeture d’événements (ex: socket fermé).  

Chaque phase s’exécute complètement avant de passer à la suivante, et l’Event Loop tourne en continu.  

---

### C. Priorité des phases et file d’attente  

- Les callbacks de **micro-tâches** (ex: résolutions de Promises) ont une priorité élevée et sont exécutés immédiatement après la phase en cours, avant de passer à la phase suivante de l’Event Loop.  
- Les callbacks réguliers (timers, I/O) font partie des phases décrites ci-dessus.  

---

### D. Exemple d'illustration du cycle d’Event Loop  

```js
console.log('Début');

setTimeout(() => {
  console.log('setTimeout');
}, 0);

setImmediate(() => {
  console.log('setImmediate');
});

Promise.resolve().then(() => {
  console.log('Promise');
});

console.log('Fin');
```

**Résultat probable :**  
```
Début  
Fin  
Promise  
setImmediate  
setTimeout  
```

- Le `Promise` est une micro-tâche, exécutée juste après le code synchrone `console.log('Fin')`.  
- `setImmediate` est exécuté dans la phase `Check`.  
- `setTimeout` (avec délai 0) est traité dans la phase `Timers` et souvent exécuté après `setImmediate`.  

---

### E. Diagramme Mermaid – Cycle simplifié de l’Event Loop  

```mermaid
flowchart TD
    Start[Début exécution script]
    SyncCode[Code synchrone]
    Microtasks[Microtasks (Promises)]
    Timers[Phase Timers (setTimeout, setInterval)]
    PendingCallbacks[Pending Callbacks I/O]
    Poll[Poll Phase (I/O)]
    Check[Check Phase (setImmediate)]
    CloseCallbacks[Close Callbacks]

    Start --> SyncCode --> Microtasks
    Microtasks --> Timers --> PendingCallbacks --> Poll --> Check --> CloseCallbacks --> Timers
```

---

### F. Comportement et implications  

- L’Event Loop empêche le blocage grâce au traitement asynchrone continu des opérations.  
- Toutefois, des fonctions synchrones lourdes peuvent bloquer la boucle et dégrader les performances globales.  
- La compréhension des phases est utile notamment pour optimiser l’ordre d’exécution des callbacks et réduire la latence.  

---

### G. Sources fiables et à jour  

- Node.js Documentation – Event Loop : https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/  
- Node.js Collaborator Blog – Understanding the Node.js Event Loop : https://nodejs.medium.com/understanding-the-nodejs-event-loop-74cd408419ff  
- MDN Web Docs – Event Loop : https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop  

---

### Synthèse  

L’Event Loop est le moteur asynchrone de Node.js qui permet d'exécuter en parallèle des opérations I/O sans bloquer le thread principal. Sa compréhension détaillée, notamment des phases internes, est indispensable pour écrire des applications performantes et réactives, en particulier lors de la manipulation des timers, des Promises et des callbacks.