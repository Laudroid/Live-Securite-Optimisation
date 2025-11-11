# Cours Securite Optimisation

## Objectifs de la formation

- Comprendre les principes fondamentaux de l'optimisation et de la sécurité du code.
- Appliquer les meilleures pratiques pour écrire du code performant et sécurisé.
- Identifier et prévenir les failles de sécurité courantes.
- Maîtriser les techniques d'optimisation spécifiques aux environnements backend et frontend (PHP, Java/Springboot, React/NodeJS).
## Séance 1 – Introduction à l’optimisation et à la sécurité

### Objectifs pédagogiques

- Définir ce qu'est un code optimisé et sécurisé.
- Comprendre la notion de dette technique et ses impacts.
- Connaître le panorama des failles courantes (OWASP Top 10).
### Contenus

#### Qu’est-ce qu’un code optimisé ?

- Lisibilité : conventions de nommage, commentaires, structure.
- Performance : temps d'exécution, consommation de ressources.
- Maintenabilité : facilité de modification, d'évolution et de débogage.
#### Qu’est-ce qu’un code sécurisé ?

- Principes CIA : Confidentialité, Intégrité, Disponibilité.
- Exemples concrets d'atteintes à ces principes.
#### Notion de dette technique et d’impact sur la performance / sécurité.

- Définition et causes de la dette technique.
- Conséquences sur le projet : coûts, risques, délais.
#### Panorama des failles courantes (OWASP Top 10).

- Présentation détaillée des 10 catégories de failles les plus critiques.
- Exemples simples pour PHP, Java et Node.js.
## Séance 2 – Optimisation des performances côté backend

### Objectifs pédagogiques

- Appliquer les concepts de complexité algorithmique pour l'optimisation.
- Optimiser les requêtes SQL via des indexes et des sélections ciblées.
- Gérer efficacement les ressources (connexions DB, threads).
- Implémenter des stratégies de caching pour améliorer les performances.
### Contenus

#### Complexité algorithmique : rappel rapide (O(n), O(log n), O(n²)…).

- Revue des notations asymptotiques et leur signification.
- Impact de la complexité sur la performance des applications (ex: tri, recherche).
#### Requêtes SQL optimisées (indexes, SELECT ciblés, pagination).

- Création et gestion des indexes pour accélérer les recherches.
- Utilisation de SELECT ciblés pour ne récupérer que les données nécessaires.
- Implémentation de la pagination pour les grands jeux de résultats.
#### Gestion efficace des ressources (connexions DB, threads, cache).

- Pooling de connexions de base de données (Spring Boot DataSource, PHP PDO).
- Gestion des threads et processus pour éviter le surchargement (Java, Node.js cluster).
#### Caching (Spring Boot Cache, Node.js Redis, PHP OPcache).

- Concepts et stratégies de caching (mémoire, distribué).
- Mise en œuvre de Spring Boot Cache.
- Utilisation de Redis avec Node.js pour un cache distribué.
- Présentation de PHP OPcache pour l'optimisation du bytecode.
## Séance 3 – Sécurité backend (PHP, Spring Boot, Node.js)

### Objectifs pédagogiques

- Comprendre et prévenir les principales failles de sécurité backend.
- Appliquer les bonnes pratiques de validation des entrées et de paramétrisation des requêtes.
- Gérer les secrets et les informations sensibles de manière sécurisée.
### Contenus

#### Principales failles backend : SQL Injection, XSS, CSRF, RCE.

- Explication détaillée de chaque faille avec des vecteurs d'attaque.
- Démonstrations de ces failles en PHP, Java et Node.js.
#### Bonnes pratiques : ORM (Hibernate, Sequelize), validation des entrées, paramétrisation des requêtes.

- Utilisation des ORM pour prévenir les injections SQL et faciliter la manipulation des données.
- Techniques de validation des entrées côté serveur.
- Implémentation des requêtes préparées pour sécuriser les interactions avec la base de données (JDBC, Knex.js, PDO).
#### Gestion des secrets (fichiers `.env`, Vault).

- Stockage sécurisé des clés API, identifiants de base de données, etc.
- Utilisation des fichiers `.env` pour le développement local.
- Introduction à des solutions plus robustes comme HashiCorp Vault pour la production.
## Séance 4 – Sécurité frontend et APIs

### Objectifs pédagogiques

- Comprendre les risques de sécurité côté client (XSS, injections JS).
- Sécuriser la communication entre le frontend et les APIs.
- Mettre en œuvre des Content Security Policies (CSP).
### Contenus

#### Sécurité côté client : XSS, injections JS.

- Explication des failles Cross-Site Scripting (XSS) et JavaScript.
- Méthodes de prévention : échappement, sanitisation des entrées utilisateur.
#### Communication sécurisée avec API : HTTPS, JWT, CORS.

- Importance de HTTPS pour le chiffrement des communications.
- Fonctionnement et utilisation des JSON Web Tokens (JWT) pour l'authentification.
- Configuration des Cross-Origin Resource Sharing (CORS) pour contrôler l'accès aux ressources.
#### Règles Content Security Policy (CSP).

- Introduction aux CSP et leur rôle dans la prévention des injections.
- Définition et implémentation de directives CSP.
## Séance 5 – Optimisation du code frontend et bonnes pratiques

### Objectifs pédagogiques

- Améliorer la performance des applications frontend via diverses techniques.
- Appliquer des techniques d'optimisation spécifiques à React.
- Comprendre le fonctionnement de l'Event Loop de Node.js et écrire du code non-bloquant.
### Contenus

#### Performance côté frontend : minification, lazy loading, memoization, code splitting.

- Minification et uglify du code JavaScript et CSS.
- Lazy loading des composants et des images pour réduire le temps de chargement initial.
- Memoization des fonctions coûteuses en ressources.
- Code splitting pour diviser le bundle JavaScript en morceaux plus petits.
#### Optimisation React : `useMemo`, `useCallback`, éviter les re-renders inutiles.

- Utilisation des hooks `useMemo` pour mémoriser les valeurs calculées.
- Utilisation des hooks `useCallback` pour mémoriser les fonctions.
- Identification et résolution des causes de re-renders inutiles des composants.
#### Node.js : event loop, async/await vs Promises, éviter le blocage.

- Approfondissement du fonctionnement de l'Event Loop de Node.js.
- Comparaison et bonnes pratiques d'utilisation de `async/await` et des `Promises`.
- Techniques pour écrire du code non-bloquant et optimiser les performances I/O.
