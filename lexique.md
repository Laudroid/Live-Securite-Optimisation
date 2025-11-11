**Optimisation du code** : Processus d'amélioration du code pour le rendre plus performant, sécurisé, lisible et maintenable.

**Sécurité du code** : Ensemble des pratiques visant à protéger le code contre les vulnérabilités et les attaques malveillantes.

**Backend** : Partie d'une application qui gère la logique métier, la base de données et les interactions côté serveur, invisible pour l'utilisateur final.

**Frontend** : Partie d'une application avec laquelle l'utilisateur interagit directement, incluant l'interface utilisateur côté client.

**Code optimisé** : Code qui est lisible, performant (en termes de temps d'exécution et de consommation de ressources) et maintenable.

**Lisibilité** : Qualité du code qui le rend facile à lire et à comprendre, favorisée par des conventions de nommage claires, des commentaires et une bonne structure.

**Conventions de nommage** : Ensemble de règles pour nommer les éléments du code (variables, fonctions, etc.) afin d'améliorer la cohérence et la lisibilité.

**Commentaires** : Explications textuelles intégrées dans le code pour en faciliter la compréhension par les développeurs.

**Structure (code)** : Organisation logique et physique du code d'une application.

**Performance (code)** : Mesure de l'efficacité d'un code en termes de temps d'exécution et de consommation de ressources.

**Temps d'exécution** : Durée nécessaire à un programme ou une fonction pour s'exécuter complètement.

**Consommation de ressources** : Quantité de mémoire, CPU, disque ou réseau utilisée par un programme ou une application.

**Maintenabilité** : Facilité avec laquelle un code peut être modifié, faire l'objet d'évolutions ou être débogué.

**Code sécurisé** : Code qui respecte les principes de Confidentialité, d'Intégrité et de Disponibilité pour prévenir les atteintes à la sécurité.

**Principes CIA** : Trilogie fondamentale de la sécurité de l'information : Confidentialité, Intégrité, Disponibilité.

**Confidentialité** : Garantie que les informations ne sont accessibles qu'aux personnes ou systèmes autorisés.

**Intégrité** : Garantie que les informations sont exactes, complètes et n'ont pas été altérées de manière non autorisée.

**Disponibilité** : Garantie que les systèmes et les informations sont accessibles et utilisables par les utilisateurs autorisés quand ils en ont besoin.

**Dette technique** : Ensemble des compromis faits lors du développement qui peuvent entraîner des coûts, risques et délais supplémentaires à long terme.

**Failles courantes** : Vulnérabilités logicielles répandues qui peuvent être exploitées par des attaquants.

**OWASP Top 10** : Liste des 10 catégories de failles de sécurité web les plus critiques, publiée par l'Open Web Application Security Project.

**Complexité algorithmique** : Mesure de la quantité de ressources (temps, espace) requise par un algorithme en fonction de la taille de ses entrées (ex: O(n), O(log n), O(n²)).

**Requêtes SQL optimisées** : Requêtes de base de données conçues pour être exécutées le plus rapidement possible, notamment via l'usage d'indexes, de sélections ciblées et de pagination.

**Indexes (SQL)** : Structures spéciales dans une base de données qui accélèrent la recherche de données en permettant un accès direct aux lignes.

**SELECT ciblés** : Requêtes SQL qui ne récupèrent que les colonnes de données strictement nécessaires, réduisant la charge sur la base de données et le réseau.

**Pagination** : Technique de division des grands jeux de résultats de requêtes en pages plus petites pour améliorer la performance et l'expérience utilisateur.

**Gestion efficace des ressources (backend)** : Optimisation de l'utilisation des ressources du serveur (connexions à la base de données, threads) pour éviter le surchargement et améliorer la performance.

**Connexions DB** : Liens établis entre l'application et la base de données pour permettre l'échange de données.

**Pooling de connexions de base de données** : Mécanisme qui maintient un ensemble de connexions à la base de données ouvertes et réutilisables, évitant le coût de création/fermeture répétée de connexions.

**Threads** : Unités d'exécution légères au sein d'un processus, permettant d'exécuter plusieurs tâches simultanément.

**Cache** : Mécanisme de stockage temporaire de données fréquemment accédées pour les récupérer plus rapidement lors des requêtes ultérieures.

**Caching** : Action de mettre en œuvre et d'utiliser un cache pour améliorer les performances.

**PHP PDO** : PHP Data Objects, une interface légère et cohérente pour accéder à des bases de données en PHP.

**Node.js cluster** : Module Node.js permettant de créer des processus enfants qui partagent les ports serveur, optimisant l'utilisation des cœurs CPU.

**Spring Boot Cache** : Fonctionnalité de Spring Boot pour implémenter un cache en mémoire ou distribué dans une application Java.

**Redis** : Système de stockage de données en mémoire, souvent utilisé comme base de données, cache ou courtier de messages, notamment avec Node.js.

**PHP OPcache** : Extension PHP qui améliore les performances en stockant le code bytecode pré-compilé en mémoire partagée, évitant ainsi la recompilation à chaque requête.

**Failles backend** : Vulnérabilités affectant la partie serveur d'une application.

**SQL Injection** : Faille de sécurité permettant à un attaquant d'insérer ou de modifier des requêtes SQL via des entrées utilisateur malveillantes, accédant ou altérant la base de données.

**XSS (Cross-Site Scripting)** : Faille de sécurité permettant à un attaquant d'injecter des scripts malveillants dans une page web vue par d'autres utilisateurs.

**CSRF (Cross-Site Request Forgery)** : Faille de sécurité où un attaquant force le navigateur d'un utilisateur authentifié à envoyer une requête forgée à un site web.

**RCE (Remote Code Execution)** : Faille de sécurité permettant à un attaquant d'exécuter du code arbitraire sur le serveur de l'application à distance.

**ORM (Object-Relational Mapping)** : Technique de programmation qui convertit les données entre des systèmes de types incompatibles (base de données et langages orientés objet), souvent utilisée pour prévenir les injections SQL.

**Validation des entrées** : Processus de vérification que les données fournies par l'utilisateur sont conformes aux attentes et aux règles de sécurité, avant leur traitement.

**Paramétrisation des requêtes** : Méthode de construction de requêtes SQL où les données utilisateur sont passées comme des paramètres séparés de la structure de la requête, prévenant les injections SQL.

**Requêtes préparées** : Fonctionnalité des bases de données où une requête SQL est définie une fois avec des marqueurs pour les valeurs, puis exécutée avec différentes valeurs, sécurisant les interactions avec la base de données.

**JDBC** : Java Database Connectivity, API standard de Java pour la connexion à des bases de données.

**Knex.js** : Constructeur de requêtes SQL pour Node.js.

**Gestion des secrets** : Ensemble des pratiques pour stocker et gérer en toute sécurité les informations sensibles (clés API, identifiants de bases de données).

**Fichiers `.env`** : Fichiers texte utilisés pour stocker des variables d'environnement spécifiques à un projet, souvent pour les secrets en environnement de développement local.

**Vault** : Solution (comme HashiCorp Vault) pour stocker, gérer et distribuer de manière sécurisée les secrets en environnement de production.

**Sécurité côté client** : Mesures de sécurité appliquées sur le navigateur web de l'utilisateur pour prévenir les attaques comme le XSS ou les injections JS.

**Injections JS** : Failles de sécurité permettant à un attaquant d'injecter et d'exécuter du code JavaScript malveillant dans le navigateur de l'utilisateur.

**Communication sécurisée avec API** : Utilisation de protocoles et mécanismes (HTTPS, JWT, CORS) pour protéger les échanges de données entre le frontend et les APIs.

**HTTPS** : Protocole de communication sécurisé sur Internet, utilisant SSL/TLS pour chiffrer les données échangées entre le navigateur et le serveur.

**JWT (JSON Web Tokens)** : Standard ouvert pour l'échange sécurisé d'informations entre parties sous forme d'un objet JSON, souvent utilisé pour l'authentification et l'autorisation.

**CORS (Cross-Origin Resource Sharing)** : Mécanisme de sécurité du navigateur qui contrôle les requêtes HTTP faites à des ressources provenant d'une origine différente de celle de la page web actuelle.

**Content Security Policy (CSP)** : Mesure de sécurité qui aide à prévenir les attaques XSS et d'injection de données en spécifiant les sources de contenu que le navigateur est autorisé à charger.

**Directives CSP** : Règles définies dans une Content Security Policy qui indiquent au navigateur quelles ressources (scripts, styles, images) peuvent être chargées et d'où.

**Performance côté frontend** : Amélioration de la vitesse de chargement, de l'interactivité et de la fluidité des applications web côté client.

**Minification** : Processus de suppression des caractères inutiles du code source sans en altérer la fonctionnalité, réduisant la taille des fichiers JavaScript et CSS.

**Lazy loading** : Technique de chargement différé des ressources (images, composants) uniquement lorsqu'elles sont nécessaires, améliorant le temps de chargement initial.

**Memoization (frontend)** : Technique d'optimisation qui consiste à mettre en cache les résultats d'appels de fonctions coûteux et à retourner le résultat mis en cache lorsque les mêmes entrées se présentent à nouveau.

**Code splitting** : Technique consistant à diviser le bundle JavaScript d'une application en morceaux plus petits qui peuvent être chargés à la demande, réduisant le temps de chargement initial.

**Optimisation React** : Ensemble de techniques et de bonnes pratiques pour améliorer les performances des applications développées avec le framework React.

**useMemo (React)** : Hook React qui mémorise la valeur d'une fonction si ses dépendances n'ont pas changé, évitant des calculs coûteux lors des re-renders.

**useCallback (React)** : Hook React qui mémorise la définition d'une fonction si ses dépendances n'ont pas changé, utile pour éviter les re-créations inutiles de callbacks.

**Re-renders inutiles** : Rendu de composants React qui n'ont pas réellement besoin d'être mis à jour, gaspillant des ressources et impactant les performances.

**Node.js Event Loop** : Mécanisme de Node.js qui permet d'exécuter des opérations non-bloquantes en traitant les événements et les callbacks de manière asynchrone.

**async/await** : Syntaxe JavaScript qui simplifie l'écriture de code asynchrone, rendant le code basé sur des Promises plus facile à lire et à maintenir.

**Promises** : Objets JavaScript représentant l'achèvement (ou l'échec) éventuel d'une opération asynchrone et sa valeur résultante.

**Code non-bloquant** : Code qui n'interrompt pas l'exécution du programme pendant qu'il attend la fin d'une opération (par exemple, une opération I/O), permettant au programme de continuer à traiter d'autres tâches.

**Performances I/O** : Efficacité des opérations d'entrée/sortie (Input/Output), telles que les lectures/écritures de fichiers ou les requêtes réseau, un facteur clé pour la performance des applications.

