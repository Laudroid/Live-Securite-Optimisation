# Séance 5 – Optimisation du code frontend et bonnes pratiques  
## Partie 1 – Performance côté frontend : minification, lazy loading, memoization, code splitting  
### 3. Memoization des fonctions coûteuses en ressources  

---

### A. Définition de la memoization  

La **memoization** est une technique d’optimisation qui consiste à **mettre en cache** le résultat d’une fonction pure (sans effets de bord) afin d’éviter de recalculer plusieurs fois une même opération coûteuse.  

Lorsqu’une fonction est appelée avec des arguments déjà traités, le résultat est retourné directement depuis le cache, ce qui améliore la performance particulièrement dans les calculs intensifs ou les appels répétés.  

---

### B. Pourquoi utiliser la memoization ?  

- Réduit la charge CPU sur les fonctions lourdes (calculs mathématiques, traitement de données, rendu).  
- Améliore la fluidité d’une application frontend surtout dans les interfaces réactives où une fonction peut être invoquée de nombreuses fois.  
- Optimise les performances sans modifier la logique métier.  

---

### C. Exemples de fonctions où la memoization est utile  

- Calculs récursifs (ex: Fibonacci, factorielle).  
- Filtrage ou tri de grandes listes de données.  
- Sélections ou transformations de données dans des frameworks UI (ex: sélecteurs dans Redux).  

---

### D. Implémentation simple en JavaScript  

```javascript
function memoize(fn) {
  const cache = new Map();
  return function(...args) {
    const key = JSON.stringify(args);
    if(cache.has(key)) {
      return cache.get(key);
    }
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}

// Exemple : calcul de Fibonacci avec memoization
const fib = memoize(function(n) {
  if (n <= 1) return n;
  return fib(n - 1) + fib(n - 2);
});

console.log(fib(40)); // Calcul rapide grâce à la mise en cache
```

---

### E. Memoization et frameworks frontend  

Certains frameworks comme React fournissent des hooks optimisés pour la memoization :  

- `React.useMemo` : mémorise la valeur retournée d’une fonction entre les rendus, recalcul uniquement si les dépendances changent.  

**Exemple React avec `useMemo` :**  

```jsx
import React, { useMemo } from 'react';

function ExpensiveComponent({ num }) {
  const fibValue = useMemo(() => {
    function fib(n) {
      if (n <= 1) return n;
      return fib(n - 1) + fib(n - 2);
    }
    return fib(num);
  }, [num]);

  return <div>Fibonacci de {num} : {fibValue}</div>;
}
```

---

### F. Diagramme Mermaid – fonctionnement de la memoization  

```mermaid
flowchart TD
    Invoker(Call Function)
    CacheStorage(Cache)
    FunctionProcess(Compute Result)

    Invoker -->|Appel avec argument X| CacheStorage
    CacheStorage -- Existe? -->|Oui| Invoker
    CacheStorage -- Existe? -->|Non| FunctionProcess
    FunctionProcess --> CacheStorage
    FunctionProcess --> Invoker
```

---

### G. Points d’attention  

- Memoizer une fonction pure, sans effets secondaires et dont la sortie dépend uniquement de ses arguments.  
- Gérer la taille du cache pour éviter une consommation mémoire excessive (strategies de purge nécessaires dans les cas avancés).  
- JSON.stringify peut montrer des limites pour certains types d’arguments complexes : utiliser des techniques adaptées si besoin.  
- Memoization est inefficace si les fonctions reçoivent souvent des arguments différents.  

---

### H. Sources  

- MDN Web Docs – Memoization : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map  
- React Documentation – useMemo : https://reactjs.org/docs/hooks-reference.html#usememo  
- JavaScript Info – Memoization : https://javascript.info/memoization  
- FreeCodeCamp – What is Memoization in JavaScript? : https://www.freecodecamp.org/news/memoization-in-javascript/  

---

### Synthèse  

La memoization est une stratégie efficace pour optimiser les performances frontend en évitant le recalcul inutile de fonctions coûteuses. Facile à implémenter en JavaScript, elle devient un outil précieux dès lors que les composants ou fonctions traitent des données lourdes ou répétitives. Utilisée avec discernement, elle améliore la réactivité et l’expérience utilisateur.