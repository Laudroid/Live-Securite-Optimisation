# Séance 5 – Optimisation du code frontend et bonnes pratiques  
## Partie 2 – Optimisation React : `useMemo`, `useCallback`, éviter les re-renders inutiles  
### 1. Utilisation des hooks `useMemo` pour mémoriser les valeurs calculées  

---

### A. Comprendre le hook `useMemo`  

Le hook React **`useMemo`** permet de **mémoriser le résultat d’un calcul** afin d’éviter de le recalculer à chaque rendu si les dépendances n'ont pas changé.  

C’est un outil d’optimisation qui aide à réduire les coûts de calcul dans les composants, en particuliers quand les valeurs sont utilisées pour le rendu ou pour des opérations lourdes.  

---

### B. Syntaxe et fonctionnement  

```jsx
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

- La fonction passée à `useMemo` est exécutée uniquement lors du premier rendu ou lorsque l’une des dépendances `[a, b]` change.  
- Si les dépendances restent identiques, la valeur mémorisée est réutilisée au lieu de recalculer.  

---

### C. Pourquoi utiliser `useMemo` ?  

- **Réduire la surcharge CPU** lors d’opérations coûteuses (tri, filtrage, calculs complexes).  
- **Éviter le recalcul inutile** des valeurs quand le composant se re-render pour d’autres raisons (props ou état différents).  
- En complément de `React.memo` sur les composants enfants pour améliorer l’efficacité globale du rendu.  

---

### D. Exemple concret  

Un composant affichant un tableau filtré à partir d’une grande liste :  

```jsx
import React, { useState, useMemo } from 'react';

function ExpensiveListFilter({ items }) {
  const [query, setQuery] = useState('');

  const filteredItems = useMemo(() => {
    console.log('Filtrage...');
    return items.filter(item => item.toLowerCase().includes(query.toLowerCase()));
  }, [query, items]);

  return (
    <div>
      <input value={query} onChange={e => setQuery(e.target.value)} placeholder="Rechercher..." />
      <ul>
        {filteredItems.map(item => (<li key={item}>{item}</li>))}
      </ul>
    </div>
  );
}
```

- Sans `useMemo`, le filtrage aurait lieu à chaque render, même si `query` n'a pas changé.  
- Avec `useMemo`, le filtrage n’est recomputé que si `query` ou `items` changent, économisant des ressources.  

---

### E. Diagramme Mermaid – Cycle de vie avec `useMemo`

```mermaid
flowchart TD
    start[Début du render]
    checkDeps{Dépendances inchangées ?}
    compute[Exécuter fonction de calcul]
    returnMemo[Retourner valeur mémorisée]
    render[Utiliser la valeur dans le composant]
    end[Fin du render]

    start --> checkDeps
    checkDeps -- Non --> compute --> render --> end
    checkDeps -- Oui --> returnMemo --> render --> end
```

---

### F. Bonnes pratiques  

- Utiliser `useMemo` uniquement pour des calculs coûteux.  
- Ne pas chercher à optimiser prématurément chaque petite valeur (le hook a un coût en mémoire).  
- Toujours fournir les dépendances correctes pour éviter des résultats obsolètes ou des bugs.  
- Coupler avec `React.memo` pour la mémorisation des composants enfants à props stables.  

---

### G. Sources  

- React Documentation – useMemo : https://fr.reactjs.org/docs/hooks-reference.html#usememo  
- Blog LogRocket – A practical guide to React useMemo and useCallback : https://blog.logrocket.com/practical-guide-to-usememo-and-usecallback-in-react/  
- Kent C. Dodds – When to use useMemo : https://kentcdodds.com/blog/usememo-and-usecallback    

---

### Synthèse  

Le hook `useMemo` offre un mécanisme simple pour mémoriser des valeurs calculées en fonction des dépendances, limitant ainsi les recomputations inutiles lors des rerenders React. Utilisé à bon escient, il améliore la performance des applications en rendant les calculs coûteux plus efficaces tout en conservant la réactivité attendue.