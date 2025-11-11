# Séance 2 – Optimisation des performances côté backend  

## Partie 4 – Caching (Spring Boot Cache, Node.js Redis, PHP OPcache)  

### 1. Concepts et stratégies de caching (mémoire, distribué)  

---

### Introduction  

Le **caching** est une technique visant à stocker temporairement des résultats ou des données fréquemment utilisées dans un espace rapide d’accès afin de réduire la latence et la charge sur les systèmes backend.  

---

### A. Concepts fondamentaux du caching  

- **Cache** : couche intermédiaire entre la source de données (base, calcul, API) et l'application, stockant les données récupérées.  
- **Hit** : tentative d’accès à une donnée déjà présente en cache (accès rapide).  
- **Miss** : donnée absente du cache, nécessite une requête vers la source.  

---

### B. Types de caches  

| Type de cache      | Description                                       | Exemples                         |
|--------------------|-------------------------------------------------|---------------------------------|
| Cache en mémoire    | Stockage local dans la mémoire RAM de l’application | Spring Boot Cache, OPcache PHP   |
| Cache distribué    | Cache partagé accessible par plusieurs serveurs   | Redis, Memcached                |

---

### C. Stratégies de cache  

- **Write-through** : écriture effectuée dans le cache et simultanément dans la source.  
- **Write-back** (Lazy write) : écrit dans le cache puis remet à jour la source en différé.  
- **Cache-aside** (Lazy loading) : application charge la donnée dans le cache à la demande (sur miss).  
- **Expiration** : durée de vie limitée des valeurs dans le cache pour éviter la désynchronisation.  

---

### D. Caching en mémoire – Exemple Spring Boot Cache  

Spring propose une abstraction simple avec annotations pour gérer le cache mémoire.  

```java
@Service
public class UserService {

    @Cacheable(value = "users", key = "#id")
    public User getUserById(Long id) {
        // Simulation d'un accès base lent
        simulateDelay();
        return userRepository.findById(id).orElse(null);
    }

    private void simulateDelay() {
        try { Thread.sleep(3000); } catch(InterruptedException e) {}
    }
}
```

- Annotation `@Cacheable` : stocke le résultat dans le cache identifié par `users`.  
- Premier appel subit la latence, suivants sont instantanés tant que la donnée est en cache.

---

### E. Cache distribué – Exemple Node.js avec Redis  

Redis est une base de données clé-valeur en mémoire très rapide, souvent utilisée comme cache partagé.  

```javascript
const redis = require('redis');
const client = redis.createClient();

async function getUser(id) {
  const cacheKey = `user:${id}`;

  const cachedUser = await client.get(cacheKey);
  if (cachedUser) {
    return JSON.parse(cachedUser);  // Cache hit
  }

  const user = await database.getUserById(id);  // Requête base
  await client.setEx(cacheKey, 3600, JSON.stringify(user)); // Cache-aside
  return user;
}
```

- Le client Redis stocke la donnée pour 1 heure (`setEx` avec TTL).  
- Cache-aside : demande vers la DB seulement en cas de miss.

---

### F. OPcache PHP – Cache de code opcode  

OPcache est un cache intégré compilant les scripts PHP en bytecode, évitant ainsi leur recompilation à chaque requête.  

- Diminue la latence d’exécution.  
- Réduit la charge CPU du serveur PHP.  

Activation dans `php.ini` :  
```ini
opcache.enable=1
opcache.memory_consumption=128
opcache.max_accelerated_files=10000
opcache.validate_timestamps=1
```

---

### G. Diagramme Mermaid – Architecture généraliste du caching  

```mermaid
graph LR
    Client --> Cache[Cache rapide]
    Cache -- Hit --> Client
    Cache -- Miss --> Source[Base de données / Service]
    Source --> Cache
```

---

### H. Bonnes pratiques  

- Choisir le type de cache adapté à la topologie (cache local pour petite échelle, cache distribué pour cluster).  
- Préciser une durée d’expiration adaptée pour limiter la désynchronisation.  
- Mettre en place des invalidations précises pour les données modifiées.  
- Surveiller le ratio hit/miss pour ajuster la taille et stratégie de cache.

---

### Sources  

- Spring Framework Cache, https://spring.io/guides/gs/caching/  
- Redis Documentation – Caching, https://redis.io/topics/cache  
- PHP Manual – OPcache, https://www.php.net/manual/en/book.opcache.php  
- Martin Fowler – Cache Patterns, https://martinfowler.com/articles/caching.html  

---

### Conclusion  

Le caching optimise significativement les performances backend lorsque les données stockées sont consultées fréquemment. Comprendre les différents types et stratégies de cache permet d’implémenter des solutions adaptées à chaque contexte, maximisant la rapidité et réduisant la charge sur les ressources primaires.