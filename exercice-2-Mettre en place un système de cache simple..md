Salut l'équipe !

Aujourd'hui, on s'attaque à un sujet central en matière de performance et d'optimisation : le cache. Comprendre et implémenter un mécanisme de cache est une compétence fondamentale pour tout développeur soucieux de la réactivité de ses applications.

---

## TP : Optimisation par Cache - Le Catalogue Produit

### Contexte & Objectif

Dans le développement d'applications modernes, la performance est une attente non négociable. Les opérations coûteuses en temps (appels à des bases de données, à des APIs externes, calculs complexes) peuvent rapidement dégrader l'expérience utilisateur. Le cache est une technique éprouvée pour stocker temporairement les résultats de ces opérations, évitant ainsi de les refaire inutilement.

**L'objectif de ce TP est d'implémenter un système de cache simple pour une opération simulée coûteuse, en utilisant les mécanismes appropriés à votre stack technique.**

### Prérequis

*   Maîtrise des bases de Java et/ou JavaScript/TypeScript.
*   Connaissance des fondamentaux de Spring Boot (pour la stack Java) ou de Node.js avec Express (pour la stack JS).
*   Environnement de développement configuré (IDE, Maven/Gradle pour Java, npm/yarn pour Node.js).
*   Une bonne dose de curiosité et l'envie d'optimiser !

### Mini-Projet : Scénario "Catalogue Produit"

Imaginez que vous développez un service de catalogue produit pour une boutique en ligne. La récupération des détails d'un produit par son identifiant (`productId`) est une opération qui, dans un environnement réel, pourrait impliquer :
*   Une requête complexe à une base de données.
*   Un appel à une API externe de gestion de stock ou de description produit.
*   Un traitement de données lourd.

Pour les besoins de ce TP, nous allons **simuler cette latence** (par exemple, avec un `Thread.sleep` en Java ou un `setTimeout` en Node.js) afin de bien visualiser l'impact du cache.

Votre tâche sera de créer une API qui expose un endpoint pour récupérer les détails d'un produit, et d'y ajouter un mécanisme de cache.

### Consignes Générales

1.  **Choisissez votre stack :** Spring Boot (Java) ou Node.js (JavaScript/TypeScript avec Express).
2.  **Créez un projet minimal** pour votre application.
3.  **Implémentez un service ou une fonction** qui simule la récupération d'un produit par son ID. Cette fonction doit introduire une latence artificielle d'au moins 2 à 3 secondes.
    *   Le "produit" peut être un simple objet JSON avec un `id`, un `name` et un `description`.
    *   Ajoutez un log (console.log ou System.out.println) à l'intérieur de cette fonction pour indiquer quand l'opération "coûteuse" est réellement exécutée.
4.  **Exposez cette fonctionnalité via une route API** (par exemple, `/products/{id}`).
5.  **Implémentez le mécanisme de cache** pour cette fonction/route. Le cache doit être en mémoire pour ce TP (pas besoin de Redis ou autre pour l'instant).
6.  **Testez votre implémentation :**
    *   Appelez la route API plusieurs fois avec le *même* `productId`.
    *   Observez les temps de réponse et les logs pour vérifier que l'opération coûteuse n'est exécutée qu'une seule fois pour un ID donné, puis que les requêtes suivantes sont servies par le cache.
    *   Appelez la route avec un *nouvel* `productId` pour vérifier que le cache fonctionne correctement pour de nouvelles entrées.

---

### Partie 1 : Implémentation Détaillée

#### Option A : Avec Spring Boot (Java)

1.  **Initialisation du projet :**
    *   Créez un projet Spring Boot avec les dépendances `Spring Web` et `Spring Cache Abstraction`.
    *   Exemple `pom.xml` (ou `build.gradle`) :
        ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-cache</artifactId>
        </dependency>
        ```

2.  **Activation du cache :**
    *   Ajoutez l'annotation `@EnableCaching` sur votre classe principale de l'application Spring Boot.

3.  **Création du Service Produit :**
    *   Créez une classe `ProductService` annotée `@Service`.
    *   Définissez une méthode `getProductById(String id)` qui :
        *   Affiche un message dans la console indiquant que l'opération de récupération est en cours (ex: "Fetching product [ID] from source...").
        *   Simule une latence avec `Thread.sleep(2000);` (n'oubliez pas de gérer l'exception `InterruptedException`).
        *   Retourne un objet `Product` (une simple classe POJO avec `id`, `name`, `description`).

4.  **Application du cache :**
    *   Annotez la méthode `getProductById` avec `@Cacheable("products")`. Le nom "products" sera le nom de votre cache.

5.  **Création du Contrôleur API :**
    *   Créez une classe `ProductController` annotée `@RestController`.
    *   Injectez votre `ProductService`.
    *   Définissez un endpoint GET `/products/{id}` qui appelle `productService.getProductById(id)` et retourne le résultat.

6.  **Test :**
    *   Démarrez l'application.
    *   Utilisez votre navigateur ou `curl` pour appeler `http://localhost:8080/products/1` plusieurs fois.
    *   Appelez ensuite `http://localhost:8080/products/2`.
    *   Observez les logs et les temps de réponse.

#### Option B : Avec Node.js (Express)

1.  **Initialisation du projet :**
    *   Créez un nouveau dossier pour votre projet.
    *   Initialisez un projet Node.js : `npm init -y`.
    *   Installez Express : `npm install express`.
    *   Pour le cache, vous pouvez implémenter un cache simple avec une `Map` JavaScript, ou utiliser une bibliothèque comme `node-cache` (`npm install node-cache`). Nous allons partir sur `node-cache` pour plus de robustesse.

2.  **Création du Service Produit (simulé) :**
    *   Créez un fichier `productService.js` (ou `productService.ts`).
    *   Définissez une fonction asynchrone `getProductById(id)` qui :
        *   Affiche un message dans la console indiquant que l'opération de récupération est en cours (ex: `console.log(\`Fetching product ${id} from source...\`)`).
        *   Simule une latence avec `await new Promise(resolve => setTimeout(resolve, 2000));`.
        *   Retourne un objet produit (ex: `{ id, name: \`Product ${id}\`, description: \`Description for product ${id}\` }`).

3.  **Implémentation du cache :**
    *   Dans votre fichier principal `app.js` (ou `server.js`), importez `node-cache`.
    *   Initialisez une instance de `NodeCache` : `const NodeCache = require("node-cache"); const productCache = new NodeCache({ stdTTL: 60 });` (TTL de 60 secondes).
    *   Créez une fonction `getOrSetCache(key, callback)` qui :
        *   Tente de récupérer la valeur pour `key` depuis `productCache`.
        *   Si la valeur est présente, la retourne immédiatement.
        *   Sinon, exécute le `callback` (qui sera votre `productService.getProductById`), stocke le résultat dans `productCache` avec la `key`, puis retourne le résultat.

4.  **Création du Serveur Express :**
    *   Dans `app.js` (ou `server.js`) :
        *   Importez Express et votre `productService`.
        *   Créez une instance d'Express.
        *   Définissez une route GET `/products/:id` qui :
            *   Récupère l'ID depuis les paramètres de la requête.
            *   Appelle votre fonction `getOrSetCache` en lui passant l'ID comme clé et `productService.getProductById` comme callback.
            *   Retourne le produit en JSON.
        *   Démarrez le serveur sur un port (ex: 3000).

5.  **Test :**
    *   Démarrez l'application : `node app.js` (ou `npm start`).
    *   Utilisez votre navigateur ou `curl` pour appeler `http://localhost:3000/products/1` plusieurs fois.
    *   Appelez ensuite `http://localhost:3000/products/2`.
    *   Observez les logs et les temps de réponse.

---

### Partie 2 : Améliorations & Réflexion (Pour aller plus loin)

Une fois que votre cache de base fonctionne, réfléchissez aux points suivants et, si le temps le permet, tentez d'en implémenter un ou deux :

1.  **Invalidation du Cache :**
    *   Que se passe-t-il si les détails d'un produit changent ? Le cache renverra des données obsolètes.
    *   **Implémentez un mécanisme pour invalider une entrée spécifique du cache.** Par exemple, ajoutez une route `DELETE /products/{id}/cache` qui supprime l'entrée correspondante du cache.
        *   *Spring Boot :* Utilisez `@CacheEvict("products")`.
        *   *Node.js :* Utilisez `productCache.del(id)`.
2.  **Expiration du Cache (TTL - Time To Live) :**
    *   Assurez-vous que votre cache a une durée de vie (TTL) configurée. Après cette durée, l'entrée doit être automatiquement supprimée, forçant une nouvelle récupération de la source.
    *   *Spring Boot :* Cela se configure généralement via `application.properties` ou une configuration Java pour le cache manager (ex: Caffeine).
    *   *Node.js :* `node-cache` le gère via l'option `stdTTL`.
3.  **Gestion des Erreurs :**
    *   Que se passe-t-il si l'opération coûteuse échoue ? Le cache doit-il stocker l'erreur ?
4.  **Politiques d'Éviction :**
    *   Si votre cache a une taille limitée, comment gérez-vous le cas où il est plein ? Les politiques comme LRU (Least Recently Used) ou LFU (Least Frequently Used) sont courantes.
    *   *Spring Boot :* Des implémentations comme Caffeine offrent ces options.
    *   *Node.js :* Des bibliothèques comme `lru-cache` sont dédiées à cela.
5.  **Cache Distribué (Réflexion) :**
    *   Pour une application à grande échelle ou avec plusieurs instances, un cache en mémoire local n'est pas suffisant. Comment aborderiez-vous un cache partagé entre plusieurs instances de votre application (ex: avec Redis ou Memcached) ? (Pas d'implémentation requise, juste une réflexion).

### Rendu

*   Un lien vers un dépôt Git (GitHub, GitLab, Bitbucket) contenant votre code source.
*   Un fichier `README.md` à la racine du dépôt qui :
    *   Indique la stack choisie (Spring Boot ou Node.js).
    *   Décrit comment lancer l'application.
    *   Explique comment tester le cache (ex: commandes `curl` à exécuter).
    *   Décrit les améliorations que vous avez tentées ou les réflexions que vous avez eues pour la Partie 2.

### Conseils du Formateur (pour l'utilisation de l'IA)

L'intelligence artificielle est un outil formidable pour accélérer le développement et explorer des solutions. N'hésitez pas à l'utiliser pour :
*   Générer le boilerplate de votre projet.
*   Demander des exemples de code pour l'implémentation du cache dans votre stack.
*   Explorer différentes options ou bibliothèques de cache.

Cependant, rappelez-vous que votre rôle est de **comprendre** ce que l'IA produit. Ne vous contentez pas d'un simple copier-coller. Posez-vous des questions critiques :
*   "Pourquoi cette solution plutôt qu'une autre ?"
*   "Quels sont les avantages et inconvénients de ce code ?"
*   "Est-ce que je pourrais l'expliquer à quelqu'un d'autre ?"

C'est votre capacité à analyser, adapter et justifier vos choix qui est évaluée, pas seulement la production d'un code fonctionnel. L'IA est votre assistant, pas votre remplaçant.

---

Bon courage et n'hésitez pas à expérimenter ! C'est en mettant les mains dans le cambouis qu'on apprend le mieux.