# Séance 5 – Optimisation du code frontend et bonnes pratiques  
## Partie 3 – Node.js : event loop, async/await vs Promises, éviter le blocage  
### 2. Comparaison et bonnes pratiques d'utilisation de `async/await` et des `Promises`  

---

### A. Concepts de base : Promises et async/await  

- **Promise** est un objet représentant une opération asynchrone qui peut aboutir à une valeur (résolue) ou à une erreur (rejetée).  
- **async/await** est une syntaxe introduite en ES2017 qui permet d’écrire du code asynchrone de façon plus lisible et séquentielle, en "attendant" la résolution d’une Promise.  

---

### B. Syntaxe et fonctionnement  

#### Exemple avec Promises (chaînage classique)  

```js
fetchData()
  .then(data => {
    console.log('Données reçues', data);
    return processData(data);
  })
  .then(result => {
    console.log('Données traitées', result);
  })
  .catch(err => {
    console.error('Erreur', err);
  });
```

#### Exemple avec async/await  

```js
async function run() {
  try {
    const data = await fetchData();
    console.log('Données reçues', data);
    const result = await processData(data);
    console.log('Données traitées', result);
  } catch (err) {
    console.error('Erreur', err);
  }
}

run();
```

---

### C. Avantages et inconvénients  

| Aspect                | Promises                                 | async/await                          |
|-----------------------|-----------------------------------------|------------------------------------|
| Lisibilité            | Plus verbeux, chaînage parfois lourd    | Syntaxe plus claire, proche du synchrone |
| Gestion des erreurs   | `.catch()` global sur la chaîne          | `try/catch` plus facile et localisé      |
| Parallelisme          | Facilite l’exécution parallèle (`Promise.all`) | Peut être moins intuitif pour paralléliser |
| Debugging             | Erreurs parfois moins traçables          | Stack traces plus naturelles             |

---

### D. Paralléliser des appels asynchrones  

Avec Promises, la parallélisation s'obtient avec `Promise.all` :  

```js
Promise.all([fetchA(), fetchB(), fetchC()])
  .then(([a, b, c]) => {
    console.log(a, b, c);
  });
```

Avec async/await, ne pas écrire ce qui force la séquentialité :  

```js
async function run() {
  const promiseA = fetchA();
  const promiseB = fetchB();
  const promiseC = fetchC();

  const a = await promiseA;
  const b = await promiseB;
  const c = await promiseC;

  console.log(a, b, c);
}
```

---

### E. Bonnes pratiques d'utilisation  

- **Préférer async/await** pour simplifier la lisibilité, surtout quand le flot asynchrone est séquentiel.  
- **Utiliser Promise.all** pour traiter des tâches asynchrones indépendantes en parallèle, même en async/await.  
- Toujours **gérer les erreurs** avec `try/catch` pour async/await et `catch` pour les Promises pour éviter les erreurs non capturées.  
- Éviter d’utiliser `await` dans des boucles `forEach` qui ne supportent pas les promesses, préférer `for...of` ou `Promise.all`.  

---

### F. Exemple d’erreur classique avec `forEach` et async/await  

```js
// Mauvais usage (ne fonctionne pas comme attendu)
items.forEach(async item => {
  await process(item);
});
console.log('Terminé'); // S’exécute avant la fin des process
```

Correction avec `for...of` :  

```js
async function processItems(items) {
  for (const item of items) {
    await process(item);
  }
  console.log('Terminé'); // Attendu après tous les process
}
```

---

### G. Diagramme Mermaid – Flux async/await vs Promise chaining  

```mermaid
flowchart TD
    Start[Début]
    AsyncAwait[Async/Await] --> Await1[await Promise 1]
    Await1 --> Await2[await Promise 2]
    Await2 --> End2[Fin]

    PromiseChain[Promise chaining] --> Then1[.then() Promise 1]
    Then1 --> Then2[.then() Promise 2]
    Then2 --> End1[Fin]

    Start --> AsyncAwait
    Start --> PromiseChain
```

---

### H. Sources  

- MDN Web Docs – Promises : https://developer.mozilla.org/fr/docs/Web/JavaScript/Guide/Utiliser_les_promesses  
- MDN Web Docs – Async/await : https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Statements/async_function  
- Node.js Documentation – Async_hooks : https://nodejs.org/api/async_hooks.html  
- JavaScript Info – Async/await : https://javascript.info/async-await  
- LogRocket Blog – Promises vs async/await : https://blog.logrocket.com/comparing-async-await-vs-promises-javascript/  

---

### Synthèse  

Les **Promises** et **async/await** sont deux facettes du même modèle asynchrone JavaScript. `async/await` offre une syntaxe plus lisible et naturelle pour les flux séquentiels, tandis que `Promises` offrent un contrôle précis pour le parallélisme et la gestion complexe des états. Une bonne maîtrise du mix des deux, combinée aux outils comme `Promise.all` et une gestion rigoureuse des erreurs, est la clé pour écrire du code Node.js performant, fiable et facile à maintenir.