Bonjour à toutes et à tous !

Aujourd'hui, nous allons nous attaquer à un sujet fondamental pour toute application web moderne : la gestion des requêtes inter-origines. Comprendre et maîtriser CORS est essentiel pour construire des architectures robustes et sécurisées.

---

## TP : Maîtrise de CORS pour une API Sécurisée

### Objectif du TP

Configurer les règles CORS (Cross-Origin Resource Sharing) sur une API existante pour autoriser l'accès depuis une application frontend spécifique, en comprenant les mécanismes sous-jacents et les implications de sécurité.

### Contexte

Dans une architecture web moderne, il est courant de séparer l'API backend (qui fournit les données et la logique métier) de l'application frontend (qui gère l'interface utilisateur). Cette séparation implique souvent que le frontend et le backend résident sur des "origines" différentes (domaine, protocole ou port).

La politique de même origine (Same-Origin Policy - SOP) des navigateurs web est une mesure de sécurité cruciale qui empêche par défaut les scripts exécutés sur une page web de faire des requêtes vers une autre origine. C'est là que CORS intervient : il fournit un mécanisme standardisé pour permettre aux serveurs d'indiquer quelles origines sont autorisées à accéder à leurs ressources.

### Prérequis

Avant de commencer, assurez-vous d'avoir les éléments suivants installés sur votre machine :

*   **Node.js et npm** (ou yarn) : Pour exécuter l'API backend.
*   Un **éditeur de code** (VS Code, Sublime Text, etc.).
*   Un **navigateur web** moderne (Chrome, Firefox, Edge, Safari) avec ses outils de développement (console, onglet réseau).

---

### Mini-Projet : Mise en place de l'environnement

Pour illustrer notre propos, nous allons créer une API backend simple et une application frontend basique.

#### 1. API Backend (Node.js / Express)

Créez un dossier nommé `api-backend`. À l'intérieur, initialisez un nouveau projet Node.js et installez Express :

```bash
mkdir api-backend
cd api-backend
npm init -y
npm install express
```

Créez un fichier `server.js` dans ce dossier avec le contenu suivant :

```javascript
const express = require('express');
const app = express();
const port = 3000; // Port de notre API

// Un endpoint simple qui renvoie des données
app.get('/data', (req, res) => {
    console.log('Requête reçue sur /data');
    res.json({
        message: 'Ceci sont des données de l\'API.',
        timestamp: new Date().toISOString(),
        source: 'API Backend'
    });
});

// Démarrage du serveur
app.listen(port, () => {
    console.log(`API backend démarrée sur http://localhost:${port}`);
});
```

Démarrez l'API :

```bash
node server.js
```

Vous devriez voir le message `API backend démarrée sur http://localhost:3000`.

#### 2. Frontend Client (HTML / JavaScript)

Créez un dossier nommé `frontend-client`. À l'intérieur, créez un fichier `index.html` :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Client Frontend CORS</title>
    <style>
        body { font-family: sans-serif; margin: 20px; }
        button { padding: 10px 15px; font-size: 16px; cursor: pointer; }
        pre { background-color: #f4f4f4; padding: 15px; border-radius: 5px; margin-top: 20px; white-space: pre-wrap; word-wrap: break-word; }
        .error { color: red; font-weight: bold; }
    </style>
</head>
<body>
    <h1>Application Frontend</h1>
    <p>Cette application tente de récupérer des données depuis l'API.</p>
    <button id="fetchDataButton">Récupérer les données de l'API</button>
    <pre id="output"></pre>

    <script>
        document.getElementById('fetchDataButton').addEventListener('click', async () => {
            const outputElement = document.getElementById('output');
            outputElement.textContent = 'Chargement des données...';
            outputElement.classList.remove('error');

            try {
                // L'URL de notre API backend
                const apiUrl = 'http://localhost:3000/data';
                const response = await fetch(apiUrl);

                if (!response.ok) {
                    throw new Error(`Erreur HTTP: ${response.status}`);
                }

                const data = await response.json();
                outputElement.textContent = JSON.stringify(data, null, 2);
            } catch (error) {
                outputElement.textContent = `Erreur lors de la récupération des données : ${error.message}\n\n`;
                outputElement.textContent += "Vérifiez la console de votre navigateur (F12) pour les détails de l'erreur CORS.";
                outputElement.classList.add('error');
                console.error('Détail de l\'erreur :', error);
            }
        });
    </script>
</body>
</html>
```

Pour que le navigateur applique la politique de même origine, il est crucial que ce fichier `index.html` soit servi par un serveur web, et non simplement ouvert directement depuis le système de fichiers (`file://`).

Installez un serveur HTTP simple :

```bash
cd frontend-client
npm install -g http-server # Installe http-server globalement
```

Démarrez le serveur HTTP dans le dossier `frontend-client` sur un port différent de l'API (par exemple, 8080) :

```bash
http-server -p 8080
```

Vous devriez voir le message `Starting up http-server, serving ./ on port: 8080`. Ouvrez votre navigateur et accédez à `http://localhost:8080`.

---

### Exercice : Configuration CORS

Maintenant que notre environnement est prêt, nous allons reproduire le problème CORS et le résoudre.

#### Phase 1 : Observer l'erreur CORS

1.  Assurez-vous que votre API (`http://localhost:3000`) et votre frontend (`http://localhost:8080`) sont tous deux en cours d'exécution.
2.  Dans votre navigateur, sur la page `http://localhost:8080`, cliquez sur le bouton "Récupérer les données de l'API".
3.  Ouvrez la console développeur de votre navigateur (généralement `F12`) et observez l'erreur. Vous devriez voir un message similaire à :
    ```
    Access to fetch at 'http://localhost:3000/data' from origin 'http://localhost:8080' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.
    ```
    **Question :** Expliquez avec vos mots pourquoi cette erreur apparaît.

#### Phase 2 : Implémenter la solution CORS

Notre objectif est d'autoriser spécifiquement l'origine `http://localhost:8080` à accéder à notre API.

1.  Arrêtez votre API backend (Ctrl+C dans le terminal où `node server.js` est lancé).
2.  Dans le dossier `api-backend`, installez le package `cors` :
    ```bash
    npm install cors
    ```
3.  Modifiez votre fichier `server.js` comme suit :

    ```javascript
    const express = require('express');
    const cors = require('cors'); // Importez le package cors
    const app = express();
    const port = 3000;

    // --- Configuration CORS ---
    const allowedOrigin = 'http://localhost:8080'; // L'origine spécifique de votre frontend

    const corsOptions = {
        origin: allowedOrigin, // Seule cette origine sera autorisée
        methods: 'GET,HEAD,PUT,PATCH,POST,DELETE', // Les méthodes HTTP que nous autorisons
        credentials: true, // Permet l'envoi de cookies ou d'en-têtes d'authentification (si nécessaire)
        optionsSuccessStatus: 204 // Pour les requêtes preflight (nous y reviendrons)
    };

    app.use(cors(corsOptions)); // Appliquez le middleware CORS à toutes les routes
    // --- Fin de la configuration CORS ---

    // Un endpoint simple qui renvoie des données
    app.get('/data', (req, res) => {
        console.log('Requête reçue sur /data');
        res.json({
            message: 'Ceci sont des données de l\'API.',
            timestamp: new Date().toISOString(),
            source: 'API Backend'
        });
    });

    // Démarrage du serveur
    app.listen(port, () => {
        console.log(`API backend démarrée sur http://localhost:${port}`);
    });
    ```
4.  Redémarrez votre API backend :
    ```bash
    node server.js
    ```

#### Phase 3 : Tester et valider

1.  Dans votre navigateur, sur la page `http://localhost:8080`, rafraîchissez la page.
2.  Cliquez à nouveau sur le bouton "Récupérer les données de l'API".
3.  **Observation :** Les données de l'API devraient maintenant s'afficher correctement dans la zone de texte.
4.  **Validation :** Ouvrez l'onglet "Réseau" (Network) de vos outils de développement.
    *   Recherchez la requête vers `http://localhost:3000/data`.
    *   Examinez les en-têtes de réponse (Response Headers) de cette requête. Vous devriez y trouver l'en-tête `Access-Control-Allow-Origin` avec la valeur `http://localhost:8080`.

---

### Questions de Réflexion et Approfondissement

Ces questions sont conçues pour vous aider à consolider votre compréhension et à explorer des scénarios plus complexes. N'hésitez pas à utiliser l'IA comme un assistant pour rechercher des informations ou des exemples, mais l'objectif est de formuler vos propres réponses et analyses.

1.  **Wildcard (`*`) vs Origine Spécifique :**
    *   Que se passerait-il si vous configuriez `origin: '*'` dans `corsOptions` ?
    *   Quels sont les avantages et les inconvénients de cette approche par rapport à l'utilisation d'une origine spécifique comme `http://localhost:8080` ? Dans quels contextes l'une serait-elle préférable à l'autre ?

2.  **Gestion de Multiples Origines :**
    *   Imaginez que votre API doive être accessible depuis deux applications frontend différentes : `http://localhost:8080` pour le développement et `https://monapp.com` pour la production. Comment modifieriez-vous la configuration `corsOptions` pour autoriser ces deux origines ? (Indice : le paramètre `origin` peut accepter un tableau ou une fonction).

3.  **Requêtes Preflight (OPTIONS) :**
    *   Expliquez le concept de "requête preflight" (ou requête de pré-vérification) dans CORS.
    *   Quand une requête preflight est-elle déclenchée par le navigateur ? (Donnez des exemples de conditions).
    *   Quel est le rôle de `optionsSuccessStatus: 204` dans notre configuration CORS ?

4.  **`Access-Control-Allow-Credentials` :**
    *   Quel est l'impact de l'option `credentials: true` dans la configuration CORS ?
    *   Dans quel cas précis auriez-vous besoin d'activer cette option ? (Pensez aux cookies, aux en-têtes d'authentification).
    *   Quelle est la contrainte majeure lorsque `credentials: true` est activé concernant l'en-tête `Access-Control-Allow-Origin` ?

5.  **Sécurité au-delà de CORS :**
    *   CORS est un mécanisme de sécurité côté navigateur. Est-ce suffisant pour sécuriser complètement votre API ?
    *   Quelles autres mesures de sécurité (authentification, autorisation, validation des entrées, etc.) devraient être mises en place sur l'API pour garantir sa robustesse ? (Connectez cela à notre cours "Sécurité-Optimisation").

6.  **Déploiement en Production :**
    *   Si vous deviez déployer cette API en production, comment géreriez-vous la valeur de `allowedOrigin` ? (Pensez aux variables d'environnement).

---

### Ressources Utiles

*   **MDN Web Docs - CORS :** [https://developer.mozilla.org/fr/docs/Web/HTTP/CORS](https://developer.mozilla.org/fr/docs/Web/HTTP/CORS)
*   **Documentation du package `cors` pour Express :** [https://www.npmjs.com/package/cors](https://www.npmjs.com/package/cors)

Prenez votre temps pour explorer, tester et comprendre. La pratique est la clé de la maîtrise ! Si vous rencontrez des difficultés, n'hésitez pas à consulter les ressources ou à poser des questions.

Bon travail !