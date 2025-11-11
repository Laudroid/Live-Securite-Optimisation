# Séance 2 – Optimisation des performances côté backend  

## Partie 4 – Caching (Spring Boot Cache, Node.js Redis, PHP OPcache)  

### 2. Mise en œuvre de Spring Boot Cache  

---

### Introduction  

Spring Boot Cache fournit une abstraction simple pour intégrer le caching dans une application Java, réduisant la latence des appels fréquents aux ressources lentes (bases de données, services externes) via des caches en mémoire ou distribués.  

---

### A. Activer le caching dans Spring Boot  

Pour activer cette fonctionnalité, il suffit d’ajouter l’annotation `@EnableCaching` dans une classe de configuration ou la classe principale :  

```java
@SpringBootApplication
@EnableCaching
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

---

### B. Configurer un cache simple  

Par défaut, Spring utilise un cache en mémoire via `ConcurrentMapCacheManager`, suffisant pour un environnement de développement ou un petit service.  

---

### C. Utilisation des annotations de cache  

- **@Cacheable** : Sauvegarde en cache le résultat d’une méthode. Si appelé à nouveau avec les mêmes arguments, récupère la valeur dans le cache.  
- **@CachePut** : Met à jour le cache sans empêcher l’exécution de la méthode.  
- **@CacheEvict** : Supprime des entrées du cache (utile lors des mises à jour/suppressions).

---

### D. Exemple complet  

```java
@Service
public class ProductService {

    // Méthode cacheable : le résultat est mis en cache avec la clé #id
    @Cacheable(value = "products", key = "#id")
    public Product getProduct(Long id) {
        simulateSlowService();
        return productRepository.findById(id).orElse(null);
    }

    // Mise à jour du cache après modification
    @CachePut(value = "products", key = "#product.id")
    public Product updateProduct(Product product) {
        return productRepository.save(product);
    }

    // Suppression du cache lors d'une suppression de produit
    @CacheEvict(value = "products", key = "#id")
    public void deleteProduct(Long id) {
        productRepository.deleteById(id);
    }

    private void simulateSlowService() {
        try {
            Thread.sleep(3000); // Simule une opération lente
        } catch (InterruptedException e) {
            throw new IllegalStateException(e);
        }
    }
}
```

---

### E. Architecture simplifiée du cache avec Spring Boot  

```mermaid
graph LR
    Client --> Service[Service Spring Boot]
    Service --> Cache[Cache (ex: ConcurrentMapCache)]
    Cache -- Hit --> Service
    Cache -- Miss --> Database[Base de données]
    Database --> Cache
    Cache --> Client
```

---

### F. Configurer un cache distribué (ex : Redis)  

Ajouter la dépendance Redis dans `pom.xml` :  

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

Configurer `application.properties` :  

```properties
spring.cache.type=redis
spring.redis.host=localhost
spring.redis.port=6379
```

Spring Boot utilisera alors Redis comme gestionnaire de cache, offrant un cache partagé performant adapté aux environnements distribués.

---

### G. Bonnes pratiques  

- Définir des clés précises pour éviter les collisions dans le cache.  
- Contrôler la durée de vie des entrées via la configuration du cache (TTL).  
- Nettoyer ou invalider le cache après mise à jour des données (via `@CachePut` et `@CacheEvict`).  
- Monitorer l’efficacité du cache (taux-hit, mémoire consommée).

---

### Sources  

- Spring Boot Reference Guide – Caching, https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-caching  
- Spring Framework Caching, https://spring.io/guides/gs/caching/  
- Baeldung – Spring Cache Tutorial, https://www.baeldung.com/spring-cache-tutorial  

---

### Conclusion  

La mise en œuvre de Spring Boot Cache permet d’ajouter facilement un cache dans une application Java, offrant un gain significatif de performance sur les appels fréquemment répétés. Le framework propose des annotations intuitives et supporte divers fournisseurs de cache, de la mémoire locale à Redis, permettant une montée en charge adaptée à l’environnement.