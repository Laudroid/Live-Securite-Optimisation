Salut à toutes et à tous !

Aujourd'hui, on s'attaque à un sujet fondamental pour la robustesse et la sécurité de vos applications : la **validation des entrées**. C'est une compétence clé, et ce TP va vous permettre de la mettre en pratique de manière concrète.

---

## TP : Sécurisation des Entrées - Formulaire d'Authentification

### Objectif du TP

Mettre en œuvre des mécanismes robustes de validation des entrées pour un formulaire d'authentification (inscription/connexion).

### Contexte et Énoncé du Mini-Projet

Imaginez que vous développez une application web nécessitant un système d'authentification. Votre mission est de construire un formulaire d'inscription et de connexion simple, en vous concentrant sur la **validation des données** soumises par l'utilisateur.

C'est une étape fondamentale pour la sécurité et la fiabilité de toute application. Une entrée non validée est une porte ouverte à de nombreuses vulnérabilités (injections SQL, XSS, etc.). Nous allons implémenter la validation côté client (pour une meilleure expérience utilisateur) et côté serveur (pour la sécurité indispensable).

### Prérequis

*   Connaissances de base en développement web (HTML, CSS, JavaScript).
*   Connaissances d'un langage/framework côté serveur (ex: Node.js/Express, Python/Flask/Django, PHP/Symfony/Laravel, etc.).
*   Notions de base sur les requêtes HTTP (GET, POST) et les réponses JSON.

### Consignes Générales

L'utilisation d'outils d'IA générative (ChatGPT, Copilot, Gemini, etc.) est non seulement autorisée, mais encouragée. Cependant, l'objectif n'est pas de copier-coller sans comprendre.

*   **Utilisez l'IA comme un assistant intelligent :** pour générer des ébauches de code, explorer des syntaxes spécifiques à votre langage/framework, débugger des erreurs, ou comprendre des concepts de validation.
*   **Votre capacité à *expliquer* votre code** et les choix de validation que vous avez faits sera évaluée. Ne vous contentez pas de la solution, comprenez-la.
*   Privilégiez la clarté, la lisibilité et la robustesse du code.

### Mini-Projet : Formulaire d'Authentification

Vous allez créer deux formulaires (ou un formulaire avec un toggle inscription/connexion) :

#### 1. Formulaire d'Inscription

**Champs à valider :**

*   **Nom d'utilisateur :**
    *   Requis.
    *   Minimum 3 caractères, maximum 20 caractères.
    *   Doit être alphanumérique (lettres, chiffres) et peut inclure des tirets (-) ou des underscores (\_).
    *   (Optionnel mais recommandé) Doit être unique (vérification côté serveur).
*   **Adresse Email :**
    *   Requis.
    *   Doit avoir un format d'email valide (ex: `utilisateur@domaine.com`).
    *   (Optionnel mais recommandé) Doit être unique (vérification côté serveur).
*   **Mot de passe :**
    *   Requis.
    *   Minimum 8 caractères.
    *   Doit contenir au moins une lettre majuscule, une lettre minuscule, un chiffre et un caractère spécial (ex: `!@#$%^&*`).
*   **Confirmation du mot de passe :**
    *   Requis.
    *   Doit correspondre exactement au champ "Mot de passe".

#### 2. Formulaire de Connexion

**Champs à valider :**

*   **Identifiant (Email ou Nom d'utilisateur) :**
    *   Requis.
*   **Mot de passe :**
    *   Requis.

#### Gestion des Erreurs

*   Pour chaque champ non valide, un message d'erreur clair et spécifique doit être affiché à l'utilisateur, à côté du champ concerné.
*   Les messages d'erreur doivent être conviviaux (ex: "Le nom d'utilisateur doit contenir entre 3 et 20 caractères alphanumériques.").

### Étapes du TP

1.  **Mise en place de l'environnement :**
    *   Créez un nouveau projet pour votre application web.
    *   Configurez un serveur web simple avec votre langage/framework choisi (si ce n'est pas déjà fait).

2.  **Conception de l'interface (HTML/CSS) :**
    *   Créez les fichiers HTML pour vos formulaires d'inscription et de connexion.
    *   Appliquez un style CSS minimaliste pour rendre les formulaires utilisables et les messages d'erreur visibles.

3.  **Validation Côté Client (JavaScript) :**
    *   Implémentez les règles de validation définies ci-dessus pour les formulaires d'inscription et de connexion.
    *   Utilisez l'API Constraint Validation de HTML5 (attributs `required`, `minlength`, `maxlength`, `pattern`, `type="email"`) pour les validations de base.
    *   Complétez avec du JavaScript personnalisé pour les règles plus complexes (ex: correspondance des mots de passe, complexité du mot de passe).
    *   Assurez-vous que les messages d'erreur sont affichés dynamiquement et disparaissent lorsque l'entrée devient valide.
    *   Le formulaire ne doit pas être soumis si la validation côté client échoue.

4.  **Mise en place du Serveur et de l'API (Langage/Framework choisi) :**
    *   Créez un endpoint HTTP (ex: `/register` pour l'inscription, `/login` pour la connexion) qui recevra les données du formulaire via une requête POST.
    *   Pour ce TP, vous n'avez pas besoin d'une base de données réelle. Vous pouvez simuler le stockage des utilisateurs dans une simple variable en mémoire (un tableau d'objets par exemple) pour tester l'unicité des emails/noms d'utilisateur.

5.  **Validation Côté Serveur :**
    *   **C'est la partie la plus critique pour la sécurité.** Répliquez *toutes* les règles de validation définies côté client pour les formulaires d'inscription et de connexion.
    *   Utilisez les outils de validation de votre framework (ex: `express-validator` ou `Joi` en Node.js; `WTForms` en Flask; `Validator` en PHP; `Django Forms` en Django). Si votre framework n'en propose pas, implémentez la logique manuellement.
    *   En cas d'échec de la validation, le serveur doit renvoyer une réponse JSON claire (ex: code HTTP 400 Bad Request) contenant les messages d'erreur spécifiques pour chaque champ invalide.
    *   En cas de succès, renvoyez une réponse JSON appropriée (ex: code HTTP 200 OK avec un message de succès).

6.  **Intégration Client-Serveur :**
    *   Modifiez votre JavaScript côté client pour envoyer les données du formulaire au serveur via `fetch` ou `XMLHttpRequest`.
    *   Interceptez les réponses du serveur. Si le serveur renvoie des erreurs de validation, affichez-les à l'utilisateur de la même manière que les erreurs côté client.

7.  **Tests et Robustesse :**
    *   Testez votre formulaire avec des entrées valides, invalides, et des tentatives d'injection simples (ex: `<script>alert('XSS')</script>` dans un champ texte, ou des caractères spéciaux non autorisés).
    *   Vérifiez que la validation côté client et côté serveur fonctionne comme attendu dans tous les scénarios.

### Ressources Utiles

*   Documentation de votre langage/framework sur la validation des entrées.
*   MDN Web Docs pour l'API Constraint Validation et les expressions régulières (regex).
*   Exemples de bibliothèques de validation pour votre langage/framework.

### Critères de Réussite

*   Les formulaires sont fonctionnels et esthétiques.
*   Toutes les règles de validation sont implémentées côté client et côté serveur.
*   Les messages d'erreur sont clairs, spécifiques et bien affichés.
*   Le code est propre, organisé et facile à comprendre.
*   Vous êtes capable d'expliquer les choix techniques et de sécurité que vous avez faits.

---

Pas de panique si tout ne vous semble pas évident au premier abord. C'est un excellent exercice pour monter en compétence. Prenez le temps de bien comprendre chaque étape.

Amusez-vous bien et n'hésitez pas à poser des questions si vous êtes bloqués !