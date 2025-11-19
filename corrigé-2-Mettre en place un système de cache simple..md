Voici deux solutions pour le TP d'optimisation par cache, une pour chaque stack technique proposée, présentées sous forme de chapitres distincts.

---

# Solutions TP : Optimisation par Cache - Le Catalogue Produit

Ce document présente des implémentations concrètes d'un mécanisme de cache en mémoire pour un service de catalogue produit, illustrant l'impact sur la performance et la réactivité des applications.

## Chapitre 1 : Solution avec Spring Boot (Java)

Cette solution utilise Spring Boot avec l'abstraction de cache de Spring, implémentée ici avec le cache en mémoire par défaut (ConcurrentHashMap) ou Caffeine (si configuré).

### 1. Structure du Projet et Dépendances (`pom.xml`)


```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.5</version> <!-- Version actuelle de Spring Boot -->
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>product-cache-app</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>product-cache-app</name>
    <description>Demo project for Spring Boot Cache TP</description>
    <properties>
        <java.version>17</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-cache</artifactId>
        </dependency>
        <!-- Optionnel : Pour une implémentation de cache plus avancée comme Caffeine -->
        <dependency>
            <groupId>com.github.ben-manes.caffeine</groupId>
            <artifactId>caffeine</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```


### 2. Classe Principale de l'Application (`ProductCacheAppApplication.java`)

L'annotation `@EnableCaching` est essentielle pour activer le support du cache de Spring.


```java
package com.example.productcacheapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cache.annotation.EnableCaching;

@SpringBootApplication
@EnableCaching // Active le support du cache de Spring
public class ProductCacheAppApplication {

    public static void main(String[] args) {
        SpringApplication.run(ProductCacheAppApplication.class, args);
    }

}
```


### 3. Modèle de Données (`Product.java`)

Un simple POJO pour représenter un produit.


```java
package com.example.productcacheapp.model;

import java.io.Serializable; // Important pour le cache si le cache est distribué ou sérialisé

public class Product implements Serializable { // Implémenter Serializable est une bonne pratique pour le cache
    private String id;
    private String name;
    private String description;

    public Product() {
    }

    public Product(String id, String name, String description) {
        this.id = id;
        this.name = name;
        this.description = description;
    }

    // Getters et Setters
    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    @Override
    public String toString() {
        return "Product{" +
               "id='" + id + '\'' +
               ", name='" + name + '\'' +
               ", description='" + description + '\'' +
               '}';
    }
}
```


### 4. Service Produit (`ProductService.java`)

La méthode `getProductById` simule une opération coûteuse et est annotée avec `@Cacheable`.


```java
package com.example.productcacheapp.service;

import com.example.productcacheapp.model.Product;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.cache.annotation.CacheEvict;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Service;

@Service
public class ProductService {

    private static final Logger log = LoggerFactory.getLogger(ProductService.class);

    /**
     * Simule la récupération d'un produit par son ID.
     * Cette opération est coûteuse et mise en cache.
     * Le cache est nommé "products". La clé du cache est l'ID du produit.
     *
     * @param id L'ID du produit.
     * @return Le produit correspondant.
     */
    @Cacheable(value = "products", key = "#id")
    public Product getProductById(String id) {
        log.info("Fetching product {} from source (simulated costly operation)...", id);
        try {
            Thread.sleep(2000); // Simule une latence de 2 secondes
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            log.error("Product fetching interrupted", e);
        }
        return new Product(id, "Product " + id, "Description for product " + id);
    }

    /**
     * Invalide une entrée spécifique du cache "products".
     * Utile lorsque les données du produit changent et que le cache doit être mis à jour.
     *
     * @param id L'ID du produit à évincer du cache.
     */
    @CacheEvict(value = "products", key = "#id")
    public void evictProductCache(String id) {
        log.info("Evicting product {} from cache.", id);
    }
}
```


### 5. Contrôleur API (`ProductController.java`)

Expose les endpoints pour récupérer des produits et invalider le cache.


```java
package com.example.productcacheapp.controller;

import com.example.productcacheapp.model.Product;
import com.example.productcacheapp.service.ProductService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/products")
public class ProductController {

    private final ProductService productService;

    public ProductController(ProductService productService) {
        this.productService = productService;
    }

    /**
     * Endpoint pour récupérer un produit par son ID.
     * Le résultat est mis en cache par le ProductService.
     *
     * @param id L'ID du produit.
     * @return Le produit.
     */
    @GetMapping("/{id}")
    public ResponseEntity<Product> getProduct(@PathVariable String id) {
        Product product = productService.getProductById(id);
        return ResponseEntity.ok(product);
    }

    /**
     * Endpoint pour invalider une entrée spécifique du cache d'un produit.
     *
     * @param id L'ID du produit à invalider.
     * @return Une réponse de succès.
     */
    @DeleteMapping("/{id}/cache")
    public ResponseEntity<String> evictProductCache(@PathVariable String id) {
        productService.evictProductCache(id);
        return ResponseEntity.ok("Cache for product " + id + " evicted successfully.");
    }
}
```


### 6. Configuration du Cache (Optionnel, pour TTL avec Caffeine) (`application.properties`)

Pour configurer un TTL (Time To Live) global ou spécifique pour un cache, on peut utiliser `application.properties` si Caffeine est présent.


```properties
# Configuration de base pour le cache (si Caffeine est sur le classpath)
spring.cache.type=caffeine
spring.cache.cache-names=products
spring.cache.caffeine.spec=expireAfterWrite=60s,maximumSize=100
```


### 7. Test de l'Implémentation

1.  **Lancer l'application :** Exécutez la classe `ProductCacheAppApplication`.
2.  **Tester le cache :**
    *   Première requête (coûteuse) : `curl http://localhost:8080/products/1` (prend environ 2 secondes, log "Fetching product...")
    *   Requêtes suivantes (servies par cache) : `curl http://localhost:8080/products/1` (réponse quasi immédiate, pas de log "Fetching product...")
    *   Nouvel ID (coûteux) : `curl http://localhost:8080/products/2` (prend environ 2 secondes, log "Fetching product...")
    *   Invalider le cache : `curl -X DELETE http://localhost:8080/products/1/cache` (log "Evicting product...")
    *   Requête après invalidation (coûteuse à nouveau) : `curl http://localhost:8080/products/1` (prend environ 2 secondes, log "Fetching product...")

### 8. Améliorations & Réflexion (Partie 2)

*   **Invalidation du Cache :** Implémentée via `@CacheEvict` dans `ProductService` et exposée par un endpoint `DELETE /products/{id}/cache`. Cela permet de rafraîchir manuellement une entrée spécifique.
*   **Expiration du Cache (TTL) :** Configurée dans `application.properties` avec `expireAfterWrite=60s` pour le cache `products`. Après 60 secondes, l'entrée est automatiquement retirée, forçant une nouvelle récupération de la source.
*   **Gestion des Erreurs :** Dans cette implémentation, si `Thread.sleep` échoue, l'exception est logguée. Si la récupération réelle échouait, l'erreur ne serait pas mise en cache par défaut. Pour cacher les échecs, il faudrait une logique plus complexe (ex: `@Cacheable(unless="#result == null")`).
*   **Politiques d'Éviction :** Avec Caffeine (configuré via `spring.cache.caffeine.spec`), on peut définir `maximumSize` pour limiter la taille du cache. Caffeine utilise par défaut une politique de type LRU (Least Recently Used) ou LFU (Least Frequently Used) pour évincer les éléments lorsque le cache est plein.
*   **Cache Distribué :** Pour une application à grande échelle, un cache en mémoire local n'est pas suffisant. On utiliserait des solutions comme Redis ou Hazelcast. Spring Cache Abstraction peut être configurée pour utiliser ces fournisseurs de cache distribués en ajoutant les dépendances appropriées et en configurant le `CacheManager`. Cela permet aux différentes instances de l'application de partager le même cache, évitant les données obsolètes entre les instances et réduisant la charge sur la source de données.

## Chapitre 2 : Solution avec Node.js (Express)

Cette solution utilise Node.js avec le framework Express pour l'API et la bibliothèque `node-cache` pour le cache en mémoire.

### 1. Initialisation du Projet et Dépendances

Créez un dossier `product-cache-node`, puis :


```bash
npm init -y
npm install express node-cache
```


### 2. Service Produit Simulé (`services/productService.js`)

Simule la récupération d'un produit avec une latence.


```javascript
// services/productService.js
const products = {}; // Simule une base de données ou une source de données

/**
 * Simule la récupération d'un produit par son ID.
 * Cette opération est coûteuse.
 * @param {string} id L'ID du produit.
 * @returns {Promise<Object>} Le produit correspondant.
 */
async function getProductById(id) {
    console.log(`[ProductService] Fetching product ${id} from source (simulated costly operation)...`);
    await new Promise(resolve => setTimeout(resolve, 2000)); // Simule une latence de 2 secondes

    // Si le produit n'existe pas dans notre "base de données" simulée, le créer
    if (!products[id]) {
        products[id] = {
            id: id,
            name: `Product ${id}`,
            description: `Description for product ${id}`
        };
    }
    return products[id];
}

/**
 * Simule la mise à jour d'un produit.
 * @param {string} id L'ID du produit.
 * @param {Object} updates Les mises à jour à appliquer.
 * @returns {Object} Le produit mis à jour.
 */
async function updateProduct(id, updates) {
    console.log(`[ProductService] Updating product ${id} in source...`);
    await new Promise(resolve => setTimeout(resolve, 500)); // Simule une latence
    if (products[id]) {
        Object.assign(products[id], updates);
    } else {
        products[id] = { id, ...updates };
    }
    return products[id];
}

module.exports = {
    getProductById,
    updateProduct
};
```


### 3. Serveur Express avec Cache (`app.js`)

Implémente le cache avec `node-cache` et expose les routes API.


```javascript
// app.js
const express = require('express');
const NodeCache = require('node-cache');
const productService = require('./services/productService');

const app = express();
const PORT = 3000;

// Initialisation du cache pour les produits
// stdTTL: durée de vie standard en secondes (ici 60 secondes)
// checkperiod: intervalle en secondes pour vérifier et supprimer les entrées expirées
const productCache = new NodeCache({ stdTTL: 60, checkperiod: 10 });

app.use(express.json()); // Pour parser les corps de requêtes JSON

/**
 * Fonction utilitaire pour récupérer une valeur du cache ou l'exécuter et la cacher.
 * @param {string} key La clé du cache.
 * @param {Function} callback La fonction à exécuter si la clé n'est pas dans le cache.
 * @returns {Promise<any>} La valeur du cache ou le résultat du callback.
 */
async function getOrSetCache(key, callback) {
    const value = productCache.get(key);
    if (value) {
        console.log(`[Cache] Cache hit for key: ${key}`);
        return value;
    }
    console.log(`[Cache] Cache miss for key: ${key}. Executing callback...`);
    const result = await callback();
    productCache.set(key, result);
    return result;
}

// Route pour récupérer un produit par son ID
app.get('/products/:id', async (req, res) => {
    const productId = req.params.id;
    try {
        const product = await getOrSetCache(productId, () => productService.getProductById(productId));
        res.json(product);
    } catch (error) {
        console.error(`Error fetching product ${productId}:`, error);
        res.status(500).json({ message: 'Error fetching product' });
    }
});

// Route pour invalider une entrée spécifique du cache
app.delete('/products/:id/cache', (req, res) => {
    const productId = req.params.id;
    const deleted = productCache.del(productId); // Supprime l'entrée du cache
    if (deleted > 0) {
        console.log(`[Cache] Evicted product ${productId} from cache.`);
        res.json({ message: `Cache for product ${productId} evicted successfully.` });
    } else {
        console.log(`[Cache] Product ${productId} not found in cache.`);
        res.status(404).json({ message: `Product ${productId} not found in cache.` });
    }
});

// Route pour simuler la mise à jour d'un produit et invalider son cache
app.put('/products/:id', async (req, res) => {
    const productId = req.params.id;
    const updates = req.body;
    try {
        const updatedProduct = await productService.updateProduct(productId, updates);
        productCache.del(productId); // Invalide le cache après la mise à jour
        console.log(`[Cache] Evicted product ${productId} from cache after update.`);
        res.json({ message: `Product ${productId} updated and cache evicted.`, product: updatedProduct });
    } catch (error) {
        console.error(`Error updating product ${productId}:`, error);
        res.status(500).json({ message: 'Error updating product' });
    }
});


app.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`);
});
```


### 4. Test de l'Implémentation

1.  **Lancer l'application :** `node app.js`
2.  **Tester le cache :**
    *   Première requête (coûteuse) : `curl http://localhost:3000/products/1` (prend environ 2 secondes, log "[ProductService] Fetching...")
    *   Requêtes suivantes (servies par cache) : `curl http://localhost:3000/products/1` (réponse quasi immédiate, log "[Cache] Cache hit...")
    *   Nouvel ID (coûteux) : `curl http://localhost:3000/products/2` (prend environ 2 secondes, log "[ProductService] Fetching...")
    *   Invalider le cache : `curl -X DELETE http://localhost:3000/products/1/cache` (log "[Cache] Evicted...")
    *   Requête après invalidation (coûteuse à nouveau) : `curl http://localhost:3000/products/1` (prend environ 2 secondes, log "[ProductService] Fetching...")
    *   Tester la mise à jour et l'invalidation automatique :
        *   `curl -X PUT -H "Content-Type: application/json" -d '{"name":"Updated Product 1"}' http://localhost:3000/products/1`
        *   `curl http://localhost:3000/products/1` (devrait être coûteux et montrer le nouveau nom)

### 5. Améliorations & Réflexion (Partie 2)

*   **Invalidation du Cache :** Implémentée via la route `DELETE /products/:id/cache` qui utilise `productCache.del(id)`. De plus, la route `PUT /products/:id` invalide automatiquement le cache du produit après une mise à jour, garantissant la fraîcheur des données.
*   **Expiration du Cache (TTL) :** Configurée lors de l'initialisation de `NodeCache` avec `stdTTL: 60` (60 secondes). Les entrées sont automatiquement supprimées après cette période.
*   **Gestion des Erreurs :** La fonction `getOrSetCache` et les routes API incluent des blocs `try-catch` pour gérer les erreurs lors de la récupération des produits ou de l'accès au cache, renvoyant une réponse 500 et logguant l'erreur. Le cache ne stocke pas les erreurs par défaut.
*   **Politiques d'Éviction :** `node-cache` est un cache simple qui gère l'expiration par TTL. Pour des politiques d'éviction plus sophistiquées comme LRU ou LFU lorsque le cache atteint une taille maximale, des bibliothèques comme `lru-cache` seraient plus appropriées.
*   **Cache Distribué :** Pour une architecture distribuée, un cache en mémoire local n'est pas viable. Des solutions comme Redis ou Memcached seraient utilisées. Ces systèmes de cache distribués permettent à plusieurs instances de l'application de partager le même pool de données mises en cache, assurant la cohérence et évitant les requêtes redondantes à la source de données, quel que soit l'instance qui reçoit la requête. L'intégration se ferait en remplaçant `node-cache` par un client Redis (ex: `ioredis`) et en utilisant ses méthodes `get`, `set`, `del`.