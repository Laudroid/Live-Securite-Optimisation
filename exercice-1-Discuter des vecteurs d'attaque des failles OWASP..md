Bonjour à toutes et à tous,

Ce TP a pour objectif de vous immerger concrètement dans la réalité des menaces qui pèsent sur les applications web. Nous allons aborder les failles du Top 10 OWASP non pas de manière théorique, mais en les contextualisant sur un exemple d'application.

---

### **TP : Exploration des Vecteurs d'Attaque OWASP sur une Application Métier**

**Contexte : Le Gestionnaire de Ressources Internes (GRI)**

Imaginez une application web interne, le "Gestionnaire de Ressources Internes (GRI)", utilisée quotidiennement par une PME. Cette application, bien que simplifiée pour les besoins de ce TP, est le cœur de la gestion opérationnelle.

Ses fonctionnalités clés incluent :

*   **Authentification et Gestion des Utilisateurs :** Connexion sécurisée, création de comptes (avec des rôles distincts : administrateur, chef de projet, collaborateur), modification de profils.
*   **Gestion de Projets :** Création, modification, suppression de projets. Chaque projet a un nom, une description, un chef de projet assigné et une liste de collaborateurs.
*   **Gestion des Tâches :** Attribution de tâches à des collaborateurs au sein d'un projet, suivi de leur avancement.
*   **Consultation de Rapports :** Les chefs de projet et administrateurs peuvent générer des rapports sur l'avancement des projets et la charge de travail des équipes.
*   **Messagerie Interne Simplifiée :** Un système basique de messagerie permettant aux utilisateurs d'échanger des messages liés aux projets.


**Énoncé de l'Exercice :**

Sélectionnez **trois (3) failles distinctes** parmi le Top 10 OWASP actuel.

Pour chacune des failles choisies, décrivez :

1.  **Identification et Contexte GRI :**
    *   Expliquez brièvement la faille choisie.
    *   Où et comment cette faille pourrait-elle se manifester spécifiquement dans le "Gestionnaire de Ressources Internes (GRI)" ? Identifiez une ou plusieurs fonctionnalités du GRI qui seraient vulnérables.

2.  **Scénario d'Attaque Détaillé :**
    *   Élaborez un scénario d'attaque réaliste et plausible.
    *   Décrivez les étapes que l'attaquant suivrait, depuis l'identification de la vulnérabilité jusqu'à l'atteinte de son objectif.
    *   Précisez le type d'attaquant (ex: utilisateur interne malveillant, attaquant externe, etc.) et son objectif (ex: vol de données, prise de contrôle, dégradation de service, etc.).

3.  **Conséquences et Impact :**
    *   Quelles seraient les répercussions concrètes de cette attaque pour l'entreprise utilisant le GRI ?
    *   Considérez les impacts techniques (ex: corruption de données, indisponibilité), opérationnels (ex: arrêt de production, perte de confiance), financiers (ex: amendes, coûts de remédiation) et réputationnels.

**Consignes et Modalités :**

*   **Livrable :** Un document structuré (PDF ou Word) présentant l'analyse pour chacune des trois failles choisies.
*   **Format :** Pour chaque faille, utilisez des titres clairs pour les trois sections demandées (Identification, Scénario, Conséquences). Soyez concis mais précis.

---

Prenez le temps de bien choisir vos failles et de construire des scénarios qui vous parlent. C'est en visualisant ces attaques que l'on comprend le mieux l'importance de la sécurité.

Bon travail !