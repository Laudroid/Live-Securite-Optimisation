Bonjour à toutes et à tous,

Ce TP a pour objectif de vous faire pratiquer la sécurisation d'applications web. Nous allons nous concentrer sur deux vulnérabilités majeures et très courantes : les injections SQL et les failles XSS.

---

### **TP : Sécurisation d'une Application Web contre les Injections SQL et XSS**

**Objectif du TP :**
Comprendre et appliquer les mécanismes de défense pour sécuriser une application web contre les injections SQL et les failles XSS.

**Contexte et Prérequis :**
Vous êtes développeur et devez garantir la robustesse d'une application. Ce TP vous demande de prendre en main une application simple, d'identifier ses faiblesses et de les corriger.
Vous devez avoir des bases en développement web (PHP, Spring Boot ou Node.js), une compréhension des interactions client-serveur et des notions élémentaires sur les bases de données relationnelles.

**Matériel et Environnement :**
*   Un environnement de développement (IDE comme VS Code, IntelliJ, etc.).
*   Le runtime de votre choix (PHP, JVM, Node.js).
*   Un système de gestion de base de données (SQLite, MySQL, PostgreSQL, etc. – SQLite est souvent le plus simple pour un TP).
*   Un navigateur web.
*   Un outil pour tester les requêtes HTTP (Postman, Insomnia, ou les outils de développement du navigateur).

---

**Énoncé du TP :**

Votre mission est de sécuriser une application web simple que vous allez créer ou adapter. Cette application doit initialement être vulnérable aux injections SQL et aux failles XSS.

**Mini-projet : Un Gestionnaire de Notes (ou de Tâches)**

Développez une application web simple qui permet :
1.  **Authentification utilisateur :** Un formulaire de connexion (username/password).
2.  **Affichage des notes/tâches :** Une page listant les notes ou tâches de l'utilisateur connecté.
3.  **Ajout/Modification de note/tâche :** Un formulaire pour créer une nouvelle note ou modifier une existante. Une note a au moins un titre et un contenu.

**Consignes Détaillées :**

**Étape 1 : Mise en place de l'application vulnérable**

1.  **Choix technologique :** Choisissez votre stack (PHP avec PDO/MySQL, Spring Boot avec Spring Data JPA/H2, Node.js avec Express/Sequelize/SQLite, etc.).
2.  **Développement de l'application :**
    *   Implémentez les fonctionnalités décrites ci-dessus.
    *   **Volontairement, introduisez des vulnérabilités :**
        *   **Injection SQL :** Pour le formulaire de connexion ou la récupération des notes, utilisez la concaténation de chaînes pour construire vos requêtes SQL, sans échapper les entrées utilisateur.
            *   *Exemple PHP :* `SELECT * FROM users WHERE username = '$_POST[username]' AND password = '$_POST[password]'`
            *   *Exemple Node.js :* `db.query("SELECT * FROM users WHERE username = '" + req.body.username + "' AND password = '" + req.body.password + "'")`
        *   **XSS (Cross-Site Scripting) :** Lors de l'affichage du titre ou du contenu d'une note, affichez directement l'entrée utilisateur sans aucun encodage HTML.
            *   *Exemple PHP :* `echo $_POST['content'];`
            *   *Exemple Node.js (avec un template engine) :* `<div><%= note.content %></div>` (si l'engine n'encode pas par défaut).
3.  **Vérification des vulnérabilités :**
    *   **SQL Injection :** Testez votre formulaire de connexion avec des payloads comme `' OR '1'='1` ou `' OR 1=1 -- ` pour contourner l'authentification. Testez la recherche de notes avec des payloads similaires.
    *   **XSS :** Créez une note avec un contenu comme `<script>alert('XSS !');</script>` et vérifiez si l'alerte s'affiche lors de la visualisation de la note.

**Étape 2 : Sécurisation de l'application**

1.  **Correction des Injections SQL :**
    *   Modifiez toutes les requêtes SQL qui utilisent des entrées utilisateur.
    *   **Utilisez des requêtes préparées (Prepared Statements) :** C'est la méthode recommandée pour la plupart des langages et frameworks.
    *   **Alternative (si vous utilisez un ORM) :** Assurez-vous que votre ORM est utilisé correctement et qu'il gère automatiquement la protection contre les injections SQL (ce qui est généralement le cas par défaut, mais vérifiez que vous ne le contournez pas).
    *   Testez à nouveau avec vos payloads d'injection SQL pour confirmer que les vulnérabilités sont corrigées.
2.  **Correction des failles XSS :**
    *   Identifiez tous les endroits où des données provenant de l'utilisateur sont affichées dans le HTML.
    *   **Appliquez un encodage HTML (HTML entity encoding) :** Convertissez les caractères spéciaux (`<`, `>`, `&`, `"`, `'`) en leurs entités HTML correspondantes (`&lt;`, `&gt;`, `&amp;`, etc.) avant de les afficher.
    *   La plupart des frameworks web et des moteurs de templates ont des fonctions ou des syntaxes dédiées pour cela (ex: `htmlspecialchars()` en PHP, `{{variable}}` ou `{% autoescape %}` dans certains templates Python/Node.js).
    *   Testez à nouveau avec vos payloads XSS pour confirmer que le script n'est plus exécuté mais affiché comme du texte.

**Étape 3 : Validation et Documentation**

1.  **Tests finaux :** Vérifiez que toutes les fonctionnalités de l'application fonctionnent correctement après les modifications de sécurité et que les vulnérabilités initiales sont bien corrigées.
2.  **Rapport de sécurisation :** Rédigez un court rapport (format Markdown ou PDF) détaillant :
    *   La description de votre application et de la stack utilisée.
    *   Les vulnérabilités initiales identifiées (avec les payloads utilisés et les résultats).
    *   Les solutions implémentées pour chaque type de vulnérabilité (avec des extraits de code pertinents).
    *   Les preuves de la correction (captures d'écran montrant les tests après correction).

---

**Utilisation de l'IA (ChatGPT, Copilot, etc.) :**

L'IA est un excellent assistant. N'hésitez pas à l'utiliser pour :
*   Obtenir des exemples de code pour la mise en place de l'application simple.
*   Comprendre la syntaxe des requêtes préparées dans votre langage/framework.
*   Rechercher les fonctions d'encodage HTML spécifiques à votre environnement.
*   Demander des explications sur les principes de sécurité sous-jacents.
*   Générer des idées de payloads pour tester les vulnérabilités.

**Cependant, gardez à l'esprit :**
*   **Vérifiez toujours le code généré :** L'IA peut faire des erreurs ou proposer des solutions non optimales.
*   **Comprenez ce que vous copiez/collez :** L'objectif est d'apprendre, pas de simplement reproduire.
*   **Formulez des questions précises :** Plus votre requête est claire, plus la réponse de l'IA sera pertinente.

**Dans votre rapport, mentionnez comment l'IA vous a aidé** (ex: "J'ai utilisé ChatGPT pour obtenir la syntaxe des requêtes préparées avec PDO en PHP", "Copilot m'a aidé à générer le boilerplate de mon application Express").

---

**Livrables :**

1.  Le code source complet de votre application sécurisée (dans un dépôt Git ou une archive ZIP).
2.  Le rapport de sécurisation (PDF ou Markdown).

---

Bon courage et amusez-vous bien à rendre votre code plus robuste !