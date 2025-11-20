# Séance 5 – Optimisation du code frontend et bonnes pratiques  
## Partie 1 – Performance côté frontend : minification, lazy loading, memoization, code splitting  
### 2. Lazy loading des composants et des images pour réduire le temps de chargement initial  

---

### A. Qu’est-ce que le Lazy Loading ?  

Le **lazy loading** (chargement paresseux) est une technique d’optimisation consistant à différer le chargement de certaines ressources (composants UI, images, scripts) jusqu’au moment où elles sont réellement nécessaires, plutôt que de les charger immédiatement au démarrage de la page.  

Cela permet de réduire le poids initial de la page, d’accélérer le rendu visible (Time to Interactive) et d’améliorer l’expérience utilisateur, surtout sur les réseaux à faible bande passante.  

---

### B. Lazy loading des images  

Les images représentent souvent une majorité du poids total d’une page. En reportant leur chargement au moment où elles deviennent visibles (exemple : quand l’utilisateur fait défiler la page), on économise la bande passante et on accélère le temps de chargement initial.  

#### Approches courantes :  

- **Attribut HTML natif `loading="lazy"`** : supporté par la plupart des navigateurs modernes, simple à implémenter.  
- **Bibliothèques JavaScript** (ex : `lazysizes`) : pour supporter des cas plus avancés et des navigateurs anciens.  
- **Intersection Observer API** : implémenter un chargement conditionné sur la visibilité réelle d’un élément dans la fenêtre.  

#### Exemple simple avec `loading="lazy"` :

```html
<img src="photo-haute-res.jpg" loading="lazy" alt="Exemple d'image">
```

---

### C. Lazy loading des composants JavaScript  

Dans les frameworks modernes comme React, Vue ou Angular, les applications sont souvent composées de nombreux composants. Le lazy loading permet de découper l’application en modules et de ne charger un composant que lorsque celui-ci est affiché ou utilisé.  

#### Exemple avec React (React.lazy + Suspense) :

```jsx
import React, { Suspense, lazy } from 'react';

const LazyComponent = lazy(() => import('./MyComponent'));

function App() {
  return (
    <div>
      <h1>Page d’accueil</h1>
      <Suspense fallback={<div>Chargement...</div>}>
        <LazyComponent />
      </Suspense>
    </div>
  );
}
```

- `React.lazy` charge dynamiquement `MyComponent` uniquement quand il est rendu.  
- `Suspense` affiche un fallback pendant le chargement asynchrone.  

---

### D. Diagramme Mermaid – Lazy loading dans une application frontend  

```mermaid
sequenceDiagram
    participant User as Utilisateur
    participant Browser as Navigateur
    participant Server as Serveur

    User->>Browser: Requête initiale page
    Browser->>Server: Charge HTML + JS initial
    Server-->>Browser: Envoie bundle initial
    Browser->>User: Affiche contenu visible

    User->>Browser: Scroll / Interaction
    Browser->>Server: Requête composants et images lazy
    Server-->>Browser: Envoie ressources à la demande
    Browser->>User: Affiche contenus chargés dynamiquement
```

---

### E. Avantages du lazy loading  

- Réduction du **temps de chargement initial** et meilleurs scores Core Web Vitals (First Contentful Paint, Largest Contentful Paint).  
- Diminution de la consommation de **bande passante** et du trafic inutile.  
- Amélioration de la **réactivité** des interfaces utilisateur.  
- Meilleure expérience sur mobile ou réseaux lent.  

---

### F. Points de vigilance  

- Gérer proprement les **états de chargement** pour éviter des interfaces vides ou "sauts" visuels (placer des placeholders ou spinners).  
- Le lazy loading ne doit pas nuire à l’**accessibilité** (ex: lecteurs d’écran).  
- Tester la compatibilité navigateur notamment pour les images sans `loading="lazy"` natif.  
- Surveiller la **SEO** pour les contenus importants car certains moteurs de recherche ne chargent pas le contenu différé de façon automatique.  

---

### G. Sources  

- MDN Web Docs – Lazy loading images : https://developer.mozilla.org/fr/docs/Web/Performance/Lazy_loading  
- React Documentation – Code Splitting with React.lazy : https://reactjs.org/docs/code-splitting.html#reactlazy  
- Google Developers Web Fundamentals – Lazy loading images and video : https://web.dev/lazy-loading/  
- Googles Lighthouse Docs – Lazy Loading Impact : https://developers.google.com/web/tools/lighthouse/audits/lazy-loading  

---

### Synthèse  

Le lazy loading optimise la performance frontend en reportant le chargement des ressources non immédiatement nécessaires. Que ce soit pour les images ou les composants, cette méthode allège la charge initiale et fluidifie la navigation. Son implémentation, désormais facilitée par des APIs natives et des frameworks modernes, nécessite néanmoins une attention particulière à l’expérience utilisateur et à l’accessibilité.