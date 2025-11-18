Voici deux propositions de solutions pour le TP sur l'exploration des vecteurs d'attaque OWASP, structurées en chapitres distincts.

---

# Solutions TP : Exploration des Vecteurs d'Attaque OWASP sur une Application Métier

Ce document explore comment des failles du Top 10 OWASP peuvent se manifester et être exploitées au sein d'une application comme le "Gestionnaire de Ressources Internes (GRI)", et les impacts associés.

## Chapitre 1 : Analyse de Vulnérabilités OWASP - Solution 1

### Faille 1 : A03:2021 – Injection (SQL Injection)

1.  **Identification et Contexte GRI :**
    *   **Explication de la faille :** L'injection est une vulnérabilité où des données non fiables sont envoyées à un interpréteur dans le cadre d'une commande ou d'une requête. L'attaquant peut ainsi exécuter des commandes arbitraires ou accéder à des données non autorisées.
    *   **Contexte GRI :** Cette faille pourrait se manifester dans toute fonctionnalité du GRI qui interagit avec la base de données en utilisant des entrées utilisateur non validées ou non "échappées" correctement. Les points vulnérables incluent :
        *   La recherche de projets ou de tâches.
        *   Les filtres de rapports.
        *   La modification de profil utilisateur (nom, description).
        *   La messagerie interne (corps du message, destinataire).

2.  **Scénario d'Attaque Détaillé :**
    *   **Type d'attaquant :** Un collaborateur interne malveillant.
    *   **Objectif :** Accéder aux données de tous les utilisateurs, y compris les administrateurs, et potentiellement modifier des informations de projet sans autorisation.
    *   **Étapes :**
        1.  L'attaquant, en tant que collaborateur, accède à la fonctionnalité de recherche de projets.
        2.  Au lieu de taper un nom de projet normal, il entre une chaîne de caractères malveillante dans le champ de recherche, par exemple : `' OR 1=1 --`.
        3.  Si l'application construit la requête SQL de manière vulnérable (ex: `SELECT * FROM projects WHERE name = '` + `userInput` + `'`), la requête devient : `SELECT * FROM projects WHERE name = '' OR 1=1 --'`.
        4.  Le `OR 1=1` rend la condition toujours vraie, et `--` commente le reste de la requête, ignorant la partie finale. La base de données retourne alors tous les projets, même ceux auxquels l'utilisateur n'est pas censé avoir accès.
        5.  L'attaquant peut ensuite affiner son attaque pour exfiltrer des données sensibles (ex: `UNION SELECT username, password FROM users`) ou modifier des enregistrements.

3.  **Conséquences et Impact :**
    *   **Techniques :** Exfiltration de données sensibles (identifiants, informations personnelles des employés, détails de projets confidentiels), corruption ou suppression de données (projets, tâches), contournement des mécanismes d'authentification et d'autorisation.
    *   **Opérationnels :** Arrêt ou perturbation des opérations si des données critiques sont altérées. Perte de confiance des employés et des clients. Nécessité d'une enquête forensique coûteuse.
    *   **Financiers :** Coûts de remédiation (correction de la faille, restauration des données), amendes réglementaires (RGPD en cas de fuite de données personnelles), perte de revenus due à l'indisponibilité ou à la perte de confiance.
    *   **Réputationnels :** Dommage considérable à l'image de l'entreprise, perte de crédibilité auprès des partenaires et des clients.

### Faille 2 : A01:2021 – Broken Access Control (Contrôle d'Accès Défaillant)

1.  **Identification et Contexte GRI :**
    *   **Explication de la faille :** Le contrôle d'accès défaillant survient lorsque les restrictions sur ce que les utilisateurs authentifiés peuvent faire ne sont pas correctement appliquées. Les attaquants peuvent exploiter ces failles pour accéder à des fonctionnalités ou des données non autorisées.
    *   **Contexte GRI :** Le GRI gère différents rôles (administrateur, chef de projet, collaborateur). Des contrôles d'accès défaillants pourraient permettre à un utilisateur de rôle inférieur d'accéder à des fonctionnalités ou des données réservées à un rôle supérieur. Exemples :
        *   Un collaborateur accédant à la page de gestion des utilisateurs (réservée aux administrateurs).
        *   Un chef de projet modifiant un projet dont il n'est pas responsable.
        *   Un utilisateur consultant les rapports d'un autre chef de projet.

2.  **Scénario d'Attaque Détaillé :**
    *   **Type d'attaquant :** Un collaborateur interne curieux ou malveillant.
    *   **Objectif :** Accéder aux rapports de performance de tous les projets, y compris ceux gérés par d'autres chefs de projet, ou modifier des projets sans en avoir l'autorisation.
    *   **Étapes :**
        1.  L'attaquant se connecte en tant que collaborateur.
        2.  Il observe que l'URL pour consulter un rapport de projet est de la forme `/reports?projectId=123`.
        3.  Il tente de modifier le `projectId` dans l'URL (ex: `/reports?projectId=456`) ou d'accéder à une page d'administration (`/admin/users`).
        4.  Si le serveur ne vérifie pas côté backend que l'utilisateur a les droits nécessaires pour accéder au `projectId=456` ou à la page `/admin/users`, l'attaquant pourra consulter ou manipuler des informations auxquelles il n'est pas autorisé.
        5.  L'attaquant pourrait également tenter d'utiliser une API pour modifier un projet en envoyant une requête POST/PUT à `/api/projects/456` avec des données modifiées, sans que le serveur ne vérifie son rôle ou son appartenance au projet.

3.  **Conséquences et Impact :**
    *   **Techniques :** Accès non autorisé à des données sensibles (rapports de performance, salaires, informations personnelles), modification ou suppression de données critiques (projets, tâches), escalade de privilèges.
    *   **Opérationnels :** Prise de décisions basée sur des informations altérées, fuite d'informations confidentielles sur les projets ou les employés, perturbation des workflows.
    *   **Financiers :** Coûts liés à la correction des failles, à la restauration des données, et potentiellement à des amendes pour non-conformité (RGPD).
    *   **Réputationnels :** Perte de confiance des employés et des partenaires, atteinte à l'image de l'entreprise.

### Faille 3 : A07:2021 – Identification and Authentication Failures (Échecs d'Identification et d'Authentification)

1.  **Identification et Contexte GRI :**
    *   **Explication de la faille :** Ces failles sont liées à des implémentations incorrectes des fonctions d'identification et d'authentification. Elles peuvent permettre à des attaquants de compromettre des comptes d'utilisateurs.
    *   **Contexte GRI :** Le GRI gère l'authentification des utilisateurs. Des échecs pourraient inclure :
        *   Politiques de mots de passe faibles (permettant des mots de passe simples).
        *   Absence de verrouillage de compte après plusieurs tentatives échouées (permettant des attaques par force brute).
        *   Utilisation de mécanismes de récupération de mot de passe non sécurisés.
        *   Absence de MFA (Multi-Factor Authentication).
        *   Stockage non sécurisé des mots de passe (ex: en clair ou hachage faible).

2.  **Scénario d'Attaque Détaillé :**
    *   **Type d'attaquant :** Un attaquant externe ciblant un compte administrateur.
    *   **Objectif :** Obtenir un accès administrateur au GRI pour exfiltrer des données ou saboter l'application.
    *   **Étapes :**
        1.  L'attaquant identifie des noms d'utilisateur potentiels (ex: `admin`, `john.doe`, `prenom.nom` via LinkedIn ou des fuites de données publiques).
        2.  Il lance une attaque par force brute ou par dictionnaire sur le formulaire de connexion du GRI, essayant des milliers de combinaisons de mots de passe.
        3.  Si le GRI n'implémente pas de verrouillage de compte après un certain nombre de tentatives échouées, ou si le taux de tentatives n'est pas limité, l'attaquant peut continuer indéfiniment.
        4.  Si un utilisateur a un mot de passe faible (ex: `password123`), l'attaquant finira par le trouver.
        5.  Une fois connecté avec les identifiants d'un administrateur, l'attaquant a un contrôle total sur le GRI.

3.  **Conséquences et Impact :**
    *   **Techniques :** Compromission totale de comptes utilisateurs, y compris les administrateurs. Accès non autorisé à toutes les fonctionnalités et données de l'application. Possibilité d'installer des backdoors ou de modifier le comportement de l'application.
    *   **Opérationnels :** Arrêt complet des opérations du GRI, perte de contrôle sur les données et les processus métier. Nécessité de réinitialiser tous les mots de passe et de renforcer les politiques de sécurité.
    *   **Financiers :** Coûts de remédiation massifs, amendes réglementaires, perte de productivité due à l'indisponibilité de l'outil.
    *   **Réputationnels :** Catastrophe réputationnelle majeure, perte de confiance des employés, des clients et des partenaires, pouvant entraîner des départs d'employés ou des pertes de contrats.

---

## Chapitre 2 : Analyse de Vulnérabilités OWASP - Solution 2

### Faille 1 : A02:2021 – Cryptographic Failures (Défaillances Cryptographiques)

1.  **Identification et Contexte GRI :**
    *   **Explication de la faille :** Cette faille survient lorsque des données sensibles ne sont pas protégées correctement par des méthodes cryptographiques robustes, ou lorsque la cryptographie est mal implémentée.
    *   **Contexte GRI :** Le GRI gère des informations sensibles. Les points de vulnérabilité incluent :
        *   Stockage des mots de passe en clair ou avec des fonctions de hachage faibles (ex: MD5, SHA-1 sans salage).
        *   Transmission de données sensibles (mots de passe, informations de projet) via HTTP non sécurisé au lieu de HTTPS.
        *   Clés API ou jetons d'authentification stockés de manière non sécurisée.
        *   Absence de chiffrement pour les sauvegardes de la base de données.

2.  **Scénario d'Attaque Détaillé :**
    *   **Type d'attaquant :** Un attaquant externe ayant compromis un serveur web ou interceptant le trafic réseau.
    *   **Objectif :** Obtenir les mots de passe des utilisateurs ou des informations de projet confidentielles.
    *   **Étapes :**
        1.  L'attaquant réussit à obtenir un dump de la base de données du GRI (par exemple, via une injection SQL ou une mauvaise configuration du serveur).
        2.  Il découvre que les mots de passe des utilisateurs sont stockés en clair ou hachés avec une fonction faible (ex: MD5) sans sel unique par utilisateur.
        3.  Si les mots de passe sont en clair, il a un accès immédiat. S'ils sont hachés faiblement, il utilise des tables arc-en-ciel (rainbow tables) ou des attaques par force brute sur les hachages pour récupérer les mots de passe originaux.
        4.  Alternativement, si le GRI ne force pas l'utilisation de HTTPS, l'attaquant peut intercepter le trafic réseau (attaque "Man-in-the-Middle") et capturer les identifiants de connexion ou d'autres données sensibles en clair lors de leur transmission.

3.  **Conséquences et Impact :**
    *   **Techniques :** Compromission massive de comptes utilisateurs, y compris les administrateurs. Vol de données sensibles (informations de projet, données personnelles des employés). Utilisation des identifiants volés pour accéder à d'autres systèmes (credential stuffing).
    *   **Opérationnels :** Perte de confiance totale dans la sécurité des données. Nécessité de réinitialiser tous les mots de passe et de mettre en place des mesures de sécurité cryptographiques robustes.
    *   **Financiers :** Amendes réglementaires (RGPD), coûts de notification des victimes, coûts de remédiation et d'audit de sécurité.
    *   **Réputationnels :** Dommage irréparable à la réputation de l'entreprise, perte de clients et de partenaires.

### Faille 2 : A05:2021 – Security Misconfiguration (Mauvaise Configuration de Sécurité)

1.  **Identification et Contexte GRI :**
    *   **Explication de la faille :** Cette faille est due à une configuration incorrecte des paramètres de sécurité au niveau du système d'exploitation, du serveur web, du serveur d'application, de la base de données, du framework ou de l'application elle-même.
    *   **Contexte GRI :** Le GRI, comme toute application, repose sur une pile technologique. Les mauvaises configurations pourraient inclure :
        *   Utilisation de comptes par défaut avec des mots de passe faibles ou connus (ex: `admin/admin`).
        *   Répertoires d'administration ou de configuration accessibles publiquement.
        *   Messages d'erreur détaillés exposant des informations sensibles (chemins de fichiers, versions de logiciels).
        *   Services inutiles activés sur le serveur.
        *   Permissions de fichiers ou de répertoires trop permissives.

2.  **Scénario d'Attaque Détaillé :**
    *   **Type d'attaquant :** Un attaquant externe cherchant un point d'entrée facile.
    *   **Objectif :** Accéder à des interfaces d'administration ou obtenir des informations sur l'infrastructure pour préparer une attaque plus sophistiquée.
    *   **Étapes :**
        1.  L'attaquant utilise un scanner de vulnérabilités ou effectue une reconnaissance manuelle pour trouver des répertoires ou des fichiers par défaut (ex: `/phpmyadmin`, `/admin`, `.git`).
        2.  Il découvre une interface d'administration du GRI (ou d'un composant sous-jacent comme la base de données) accessible publiquement.
        3.  Il tente de se connecter en utilisant des identifiants par défaut courants (ex: `admin/admin`, `root/root`).
        4.  Si ces identifiants fonctionnent, l'attaquant obtient un accès administrateur à une partie ou à l'ensemble du système, lui permettant de modifier des données, de créer de nouveaux comptes ou d'exécuter des commandes.
        5.  Alternativement, l'attaquant pourrait déclencher une erreur dans l'application (ex: en fournissant une entrée invalide) et observer un message d'erreur détaillé qui révèle la version du serveur web, le chemin complet de l'application ou des détails sur la base de données, informations précieuses pour d'autres attaques.

3.  **Conséquences et Impact :**
    *   **Techniques :** Accès non autorisé à des interfaces d'administration, divulgation d'informations sensibles sur l'infrastructure, exécution de code arbitraire si des services mal configurés sont exploités.
    *   **Opérationnels :** Prise de contrôle du système, perturbation des services, fuite de données. Nécessité de revoir et de sécuriser toutes les configurations système et applicatives.
    *   **Financiers :** Coûts de remédiation, amendes, perte de productivité.
    *   **Réputationnels :** Atteinte à la crédibilité de l'entreprise, perte de confiance.

### Faille 3 : A04:2021 – Insecure Design (Conception Non Sécurisée)

1.  **Identification et Contexte GRI :**
    *   **Explication de la faille :** Cette catégorie met l'accent sur les défauts de conception et d'architecture qui peuvent conduire à des vulnérabilités. Il ne s'agit pas d'erreurs d'implémentation, mais de lacunes dans la conception même de l'application.
    *   **Contexte GRI :** La conception du GRI pourrait présenter des failles si :
        *   Les workflows métier ne sont pas suffisamment sécurisés (ex: un utilisateur peut contourner une étape d'approbation).
        *   Les limites de confiance ne sont pas clairement définies ou respectées (ex: le frontend fait confiance aux données envoyées par le client).
        *   Les fonctionnalités de "réinitialisation de mot de passe" sont mal conçues.
        *   Il n'y a pas de validation côté serveur pour les données critiques.

2.  **Scénario d'Attaque Détaillé :**
    *   **Type d'attaquant :** Un collaborateur interne cherchant à contourner les processus métier.
    *   **Objectif :** Modifier l'état d'une tâche ou d'un projet sans l'approbation requise, ou s'attribuer des ressources non autorisées.
    *   **Étapes :**
        1.  Dans le GRI, la création d'un nouveau projet nécessite l'approbation d'un administrateur. Cependant, la conception de l'API permet à un utilisateur de soumettre directement un statut "approuvé" pour un projet lors de sa création, sans passer par le workflow d'approbation.
        2.  L'attaquant, en tant que chef de projet, crée un nouveau projet via l'interface web.
        3.  Au lieu d'attendre l'approbation, il intercepte la requête HTTP envoyée à l'API (par exemple, avec un proxy comme Burp Suite) et modifie le statut du projet de "en attente" à "approuvé" avant d'envoyer la requête au serveur.
        4.  Si la logique métier côté serveur ne vérifie pas les permissions de l'utilisateur pour définir le statut "approuvé" et fait confiance à l'entrée du client, le projet est créé directement avec le statut "approuvé", contournant ainsi le processus de validation.
        5.  L'attaquant peut alors créer des projets non autorisés ou modifier des tâches sans supervision.

3.  **Conséquences et Impact :**
    *   **Techniques :** Contournement des contrôles métier et des workflows, manipulation de données sans autorisation, création de ressources non validées.
    *   **Opérationnels :** Chaos dans la gestion des projets et des tâches, non-respect des procédures internes, prise de décisions basée sur des informations incorrectes ou non validées.
    *   **Financiers :** Perte de temps et de ressources due à la gestion de projets non approuvés, coûts de remédiation pour revoir et sécuriser la conception de l'application.
    *   **Réputationnels :** Perte de confiance dans l'intégrité des données et des processus de l'entreprise.

---