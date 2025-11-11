# Séance 2 – Optimisation des performances côté backend  

## Partie 1 – Complexité algorithmique : rappel rapide (O(n), O(log n), O(n²)…)  

### 2. Impact de la complexité sur la performance des applications (ex: tri, recherche)  

---

### Introduction  

La complexité algorithmique influe directement sur la rapidité d’exécution d’opérations fondamentales telles que la recherche ou le tri. L’impact est critique lorsque les volumes de données augmentent, influençant la performance perçue et la charge serveur.  

---

### A. Impact de la complexité dans la recherche  

#### Recherche linéaire \(O(n)\) vs recherche binaire \(O(\log n)\)

- **Recherche linéaire** : parcourt chaque élément jusqu’à trouver la cible. Temps moyen proportionnel à \(n/2\).
- **Recherche binaire** : divise successivement la liste triée en deux, réduisant drastiquement le nombre de comparaisons.

**Exemple concret :**  

Pour une liste de 1 million d’éléments :

| Algorithme       | Nombre maximal d’opérations approximatives |
|------------------|---------------------------------------------|
| Recherche linéaire| 1 000 000                                   |
| Recherche binaire | \(\log_2(1 000 000) \approx 20\)           |

L’écart est immense pour des datasets volumineux.

---

### B. Impact de la complexité dans le tri  

#### Tri à bulle \(O(n^2)\) vs Tri rapide \(O(n \log n)\)

- Tri bulle : compare et échange successivement les éléments, souvent inefficace pour plus de quelques milliers de données.  
- Tri rapide : partitionne la liste pour trier récursivement avec une complexité moyenne bien plus faible.

**Exemple chiffré (temps relatif) pour \(n=10\,000\) :**

| Algorithme   | Complexité  | Durée estimée (approx.)          |
|--------------|-------------|---------------------------------|
| Tri bulle    | \(O(n^2)\)  | Très lent (exemple : plusieurs minutes)  |
| Tri rapide   | \(O(n \log n)\) | De l’ordre de quelques secondes |

---

### C. Diagramme Mermaid : Comparaison pratique de la complexité

```mermaid
graph LR
    A[Taille dataset]
    B1[Recherche linéaire O(n)]
    B2[Recherche binaire O(log n)]
    B3[Tri bulle O(n^2)]
    B4[Tri rapide O(n log n)]

    A --> B1
    A --> B2
    A --> B3
    A --> B4
```

Ce schéma souligne que les algorithmes à complexité moindre restent performants même avec la montée en charge.

---

### D. Importance pour le backend

- **Réduction des coûts serveurs** : moins de CPU, moins d’énergie consommée.  
- **Scalabilité** : capacité à gérer un nombre croissant d’utilisateurs et de données.  
- **Meilleure expérience utilisateur** : réponses plus rapides, évitant les blocages ou timeout.  

---

### E. Exemples de code  

#### Tri rapide (Python)

```python
def quicksort(arr):
    if len(arr) <= 1:
        return arr
    pivot = arr[len(arr)//2]
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return quicksort(left) + middle + quicksort(right)
```

Ce tri sera plus adapté même pour des données considérables.  

---

### Références  

- Big-O Cheat Sheet, https://www.bigocheatsheet.com/  
- GeeksforGeeks, *Time Complexity of Algorithms*, https://www.geeksforgeeks.org/time-complexity-of-algorithms/  
- Wikipedia, *Sorting Algorithm*, https://en.wikipedia.org/wiki/Sorting_algorithm  
- Stack Overflow, *Why is Binary Search so efficient?*, https://stackoverflow.com/questions/5134373/why-is-binary-search-fast  

---

### Conclusion  

La maîtrise de la complexité algorithmique conditionne la performance réelle d’une application backend, surtout pour les opérations essentielles comme la recherche ou le tri. Choisir des algorithmes aux complexités adaptées permet d’optimiser la réactivité et la charge serveur.