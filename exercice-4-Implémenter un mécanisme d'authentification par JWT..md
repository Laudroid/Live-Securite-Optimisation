Bonjour à toutes et à tous !

Ce TP est conçu pour vous permettre de mettre en pratique des concepts essentiels en sécurité des applications web. Nous allons nous concentrer sur l'authentification et l'autorisation via les JSON Web Tokens (JWT), un standard largement utilisé.

---

## TP : Authentification JWT pour une Application Full-Stack Sécurisée

### Objectif du TP

Implémenter un mécanisme d'authentification robuste basé sur les JSON Web Tokens (JWT) pour sécuriser les échanges entre une API backend et un client frontend. Vous apprendrez à générer, valider et utiliser des JWT pour protéger des ressources.

### Contexte / Mini-Projet : Gestionnaire de Notes Sécurisé

Vous allez développer un petit gestionnaire de notes personnel. Chaque utilisateur pourra créer, consulter, modifier et supprimer *ses propres* notes. L'application sera composée de deux parties :

1.  **Une API Backend** (Node.js avec Express ou Spring Boot) qui gérera les utilisateurs, l'authentification (login/register) et les opérations CRUD sur les notes.
2.  **Un Client Frontend** (React) qui permettra aux utilisateurs de s'inscrire, se connecter, puis de gérer leurs notes.

Ce mini-projet est volontairement simple sur la logique métier pour que nous puissions nous concentrer pleinement sur les aspects d'authentification et de sécurité.

### Prérequis

Avant de commencer, assurez-vous d'avoir les outils et connaissances de base suivants :

*   **Node.js et npm** (si vous choisissez Node.js) ou **Java JDK, Maven/Gradle** (si vous choisissez Spring Boot).
*   **React et `npx create-react-app`**.
*   Connaissances de base en développement web (HTTP, REST, JavaScript/Java, React).
*   Un éditeur de code (VS Code, IntelliJ, etc.).
*   Un outil pour tester les API (Postman, Insomnia, ou l'extension VS Code "REST Client").

### Architecture Générale

```
+-------------------+       +-------------------+       +-------------------+
|   Client React    | <---> |    API Backend    | <---> |   Base de Données |
| (Stocke le JWT)   |       | (Génère/Valide JWT)|       | (Utilisateurs, Notes) |
+-------------------+       +-------------------+       +-------------------+
        ^                               ^
        |                               |
        +------- Requêtes Protégées (avec JWT) --------+
        +------- Requêtes d'Auth (Login/Register) -----+
```

Pour ce TP, la "Base de Données" pourra être simplifiée : un tableau en mémoire ou un fichier JSON pour stocker les utilisateurs et les notes, afin de ne pas ajouter la complexité d'une base de données persistante complète.

### Étapes du TP

#### Partie 1 : API Backend (Node.js avec Express ou Spring Boot)

Votre API sera le cœur de l'authentification.

1.  **Initialisation du Projet Backend :**
    *   Créez un nouveau projet (Express ou Spring Boot).
    *   Installez les dépendances nécessaires :
        *   **Node.js :** `express`, `jsonwebtoken`, `bcryptjs` (pour le hachage des mots de passe), `dotenv` (pour les variables d'environnement).
        *   **Spring Boot :** `spring-boot-starter-web`, `spring-boot-starter-security`, `jjwt-api`, `jjwt-impl`, `jjwt-jackson` (pour JWT), `spring-boot-starter-data-jpa` (si vous voulez une vraie DB, sinon, juste `web` et `security`).

2.  **Modèle Utilisateur :**
    *   Définissez une structure pour vos utilisateurs (ex: `User` avec `id`, `username`, `passwordHash`).
    *   Pour la simplicité, vous pouvez stocker ces utilisateurs dans un tableau en mémoire ou un fichier JSON pour ce TP.

3.  **Routes d'Authentification :**
    *   **`/api/register` (POST) :**
        *   Prend un `username` et un `password`.
        *   Hachez le mot de passe avant de le "stocker" (utilisez `bcryptjs` en Node.js, ou `BCryptPasswordEncoder` en Spring Boot).
        *   Retournez un message de succès ou d'erreur.
    *   **`/api/login` (POST) :**
        *   Prend un `username` et un `password`.
        *   Vérifiez si l'utilisateur existe et si le mot de passe correspond au hachage stocké.
        *   Si les identifiants sont valides, **générez un JWT**. Le payload du JWT doit contenir des informations non sensibles (ex: `userId`, `username`).
        *   Retournez le JWT au client.

4.  **Middleware / Filtre de Protection JWT :**
    *   Créez une fonction (Node.js) ou un filtre (Spring Boot) qui sera exécutée avant l'accès aux routes protégées.
    *   Cette fonction/filtre doit :
        *   Extraire le JWT de l'en-tête `Authorization` (format `Bearer <token>`).
        *   Vérifier la validité du token (signature, expiration).
        *   Si le token est valide, décoder le payload et attacher les informations de l'utilisateur à l'objet `request` (ex: `req.user` en Node.js).
        *   Si le token est invalide ou manquant, renvoyer une erreur `401 Unauthorized` ou `403 Forbidden`.

5.  **Routes Protégées (Gestion des Notes) :**
    *   Définissez une structure pour vos notes (ex: `Note` avec `id`, `title`, `content`, `userId`).
    *   Créez les routes CRUD pour les notes (ex: `/api/notes`, `/api/notes/:id`).
    *   **Appliquez le middleware/filtre de protection JWT à toutes ces routes.**
    *   Assurez-vous que chaque utilisateur ne peut accéder, modifier ou supprimer *que ses propres notes* en utilisant le `userId` extrait du JWT.

6.  **Configuration des CORS :**
    *   Configurez votre API pour accepter les requêtes provenant de votre client React (qui tournera sur un port différent, ex: `http://localhost:3000`).

7.  **Gestion des Erreurs :**
    *   Implémentez une gestion basique des erreurs pour les cas d'identifiants invalides, de token manquant/expiré, etc.

#### Partie 2 : Client Frontend (React)

Votre client React interagira avec l'API.

1.  **Initialisation du Projet Frontend :**
    *   Créez un nouveau projet React (`npx create-react-app mon-app-notes`).

2.  **Composants d'Authentification :**
    *   **`Login.js` :** Un formulaire pour saisir le `username` et le `password`.
        *   Lors de la soumission, envoyez une requête POST à `/api/login` de votre backend.
        *   Si la connexion réussit, **stockez le JWT reçu** (par exemple, dans `localStorage` ou `sessionStorage`).
        *   Redirigez l'utilisateur vers la page des notes.
    *   **`Register.js` (optionnel) :** Un formulaire similaire pour l'inscription, si vous avez implémenté la route `/api/register`.

3.  **Composant de Gestion des Notes :**
    *   **`NotesList.js` :** Affiche la liste des notes de l'utilisateur connecté.
        *   Lors du chargement, envoyez une requête GET à `/api/notes`.
        *   **Important :** Incluez le JWT stocké dans l'en-tête `Authorization` de *toutes les requêtes protégées*.
    *   **`NoteForm.js` :** Un formulaire pour ajouter ou modifier une note.
        *   Envoyez des requêtes POST/PUT à `/api/notes` (avec le JWT).
    *   **Boutons de suppression :** Pour chaque note, un bouton qui envoie une requête DELETE à `/api/notes/:id` (avec le JWT).

4.  **Navigation et État Global :**
    *   Utilisez `react-router-dom` pour la navigation (pages Login, Register, Notes).
    *   Gérez l'état d'authentification (présence du token, informations utilisateur) via le Context API de React ou une bibliothèque de gestion d'état (Redux, Zustand, etc.).
    *   Affichez conditionnellement les composants (ex: formulaire de connexion si non authentifié, liste des notes si authentifié).

5.  **Déconnexion (Logout) :**
    *   Un bouton "Déconnexion" qui, lorsqu'il est cliqué, supprime le JWT du `localStorage`/`sessionStorage` et redirige l'utilisateur vers la page de connexion.

#### Partie 3 : Intégration et Tests

1.  **Lancement :**
    *   Démarrez votre API backend.
    *   Démarrez votre client React.

2.  **Scénarios de Test :**
    *   Accédez à une route publique de l'API (si vous en avez une, ex: `/api/status`).
    *   Tentez d'accéder à une route protégée de l'API sans être connecté (doit échouer avec 401/403).
    *   Inscrivez un nouvel utilisateur via le client React (si implémenté).
    *   Connectez-vous avec cet utilisateur. Vérifiez que le JWT est stocké.
    *   Accédez à la page des notes : les notes doivent s'afficher (si l'utilisateur en a).
    *   Créez, modifiez, supprimez des notes.
    *   Déconnectez-vous. Tentez d'accéder à la page des notes (doit rediriger vers la connexion).
    *   Testez avec un token invalide ou expiré (simulez-le manuellement si besoin).

### Consignes Spécifiques : Utilisation de l'Intelligence Artificielle

L'utilisation d'outils d'IA (ChatGPT, GitHub Copilot, etc.) est non seulement autorisée, mais encouragée dans ce TP. Considérez l'IA comme un assistant de développement puissant.

**Votre rôle n'est pas de copier-coller aveuglément, mais de comprendre, d'adapter et de critiquer le code généré.**

*   **Utilisez l'IA pour :**
    *   Générer des squelettes de code pour les routes, les middlewares, les composants React.
    *   Comprendre des concepts spécifiques (ex: "comment hacher un mot de passe en Node.js avec bcryptjs", "comment ajouter un en-tête Authorization dans une requête fetch en React").
    *   Débugger des erreurs ou des comportements inattendus.
    *   Explorer des alternatives ou des bonnes pratiques.
*   **Soyez prêt à expliquer vos choix et le code que vous avez produit, même s'il a été assisté par l'IA.** La compréhension est primordiale.
*   **Le but est d'apprendre à collaborer efficacement avec l'IA**, en tirant parti de sa rapidité tout en gardant une maîtrise technique de votre projet.

### Rendu

Vous devrez fournir :

1.  **Un dépôt Git** (GitHub, GitLab, Bitbucket) contenant :
    *   Le code source complet de votre API backend.
    *   Le code source complet de votre client React.
    *   Un fichier `README.md` clair à la racine du dépôt, incluant :
        *   Les instructions pour cloner le projet.
        *   Les commandes pour installer les dépendances et lancer l'API et le client.
        *   Les identifiants d'un utilisateur de test (si vous n'avez pas de route d'inscription).
        *   Une brève description des choix techniques importants (ex: pourquoi `localStorage` vs `sessionStorage` pour le token).
2.  **(Optionnel mais apprécié) Un court document ou une section dans le README** expliquant comment vous avez utilisé l'IA pour ce TP, quelles ont été les requêtes les plus utiles, et comment vous avez adapté les réponses.

### Évaluation

Votre travail sera évalué sur les points suivants :

*   **Fonctionnalité :** L'API et le client fonctionnent-ils comme attendu ?
*   **Sécurité :** Le JWT est-il correctement implémenté (génération, validation, protection des routes) ? Les mots de passe sont-ils hachés ? Les utilisateurs ne peuvent-ils accéder qu'à leurs propres données ?
*   **Qualité du code :** Le code est-il lisible, bien structuré et commenté si nécessaire ?
*   **Compréhension :** Votre capacité à expliquer les concepts et les choix techniques derrière votre implémentation.
*   **Autonomie et adaptation :** Votre capacité à résoudre les problèmes rencontrés et à adapter les solutions (y compris celles générées par l'IA).

Bon courage à toutes et à tous ! N'hésitez pas à poser des questions si vous rencontrez des difficultés.