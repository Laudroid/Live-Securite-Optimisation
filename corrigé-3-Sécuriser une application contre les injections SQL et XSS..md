Voici deux solutions pour le TP de sécurisation d'applications web, chacune utilisant une stack technologique différente et présentée sous forme de chapitre.

---

# Solutions TP : Sécurisation d'une Application Web

Ce document explore la mise en œuvre de défenses contre les injections SQL et les failles XSS au sein d'une application web simple de gestion de notes.

## Chapitre 1 : Solution avec PHP et PDO (SQLite)

Cette solution utilise PHP avec l'extension PDO pour l'interaction avec une base de données SQLite, et des fonctions PHP natives pour l'encodage HTML.

### 1. Description de l'Application et Stack

L'application est un "Gestionnaire de Notes" simple. Elle permet aux utilisateurs de se connecter, de visualiser leurs notes, et d'en ajouter ou modifier.
*   **Stack :** PHP 8.x, SQLite 3, PDO.
*   **Fonctionnalités :**
    *   `index.php` : Page de connexion.
    *   `dashboard.php` : Affiche les notes de l'utilisateur connecté.
    *   `add_edit_note.php` : Formulaire pour ajouter ou modifier une note.
    *   `db.php` : Fichier de configuration et de connexion à la base de données.

### 2. Base de Données (SQLite)

Créez un fichier `database.sqlite` et exécutez les commandes SQL suivantes pour initialiser la structure et insérer un utilisateur de test.


```sql
-- users table
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL UNIQUE,
    password TEXT NOT NULL
);

-- notes table
CREATE TABLE notes (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    user_id INTEGER NOT NULL,
    title TEXT NOT NULL,
    content TEXT,
    FOREIGN KEY (user_id) REFERENCES users(id)
);

-- Insert un utilisateur de test (mot de passe non haché pour la démonstration de SQLi)
INSERT INTO users (username, password) VALUES ('testuser', 'password');
```


### 3. Fichier de Connexion à la DB (`db.php`)


```php
<?php
session_start(); // Démarre la session pour gérer l'authentification

$db_file = __DIR__ . '/database.sqlite';

try {
    $pdo = new PDO("sqlite:$db_file");
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    $pdo->setAttribute(PDO::ATTR_DEFAULT_FETCH_MODE, PDO::FETCH_ASSOC);
} catch (PDOException $e) {
    die("Erreur de connexion à la base de données : " . $e->getMessage());
}

// Fonction utilitaire pour rediriger si non authentifié
function require_auth() {
    if (!isset($_SESSION['user_id'])) {
        header('Location: index.php');
        exit();
    }
}
?>
```


### 4. Application Vulnérable

#### a. Vulnérabilité SQL Injection (Formulaire de Connexion)

**Fichier :** `index.php` (partie traitement du formulaire)


```php
<?php
require_once 'db.php';

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $username = $_POST['username'];
    $password = $_POST['password'];

    // VULNÉRABLE : Concaténation directe des entrées utilisateur
    $sql = "SELECT id, username FROM users WHERE username = '$username' AND password = '$password'";
    $result = $pdo->query($sql); // Exécution directe

    if ($user = $result->fetch()) {
        $_SESSION['user_id'] = $user['id'];
        $_SESSION['username'] = $user['username'];
        header('Location: dashboard.php');
        exit();
    } else {
        $error = "Nom d'utilisateur ou mot de passe incorrect.";
    }
}
?>
<!-- Reste du HTML du formulaire de connexion -->
```


**Payload de test :**
*   **Username :** `' OR '1'='1`
*   **Password :** `password` (ou n'importe quoi)
*   **Résultat attendu :** Connexion réussie sans connaître le vrai mot de passe.

#### b. Vulnérabilité XSS (Affichage des Notes)

**Fichier :** `dashboard.php` (partie affichage des notes)


```php
<?php
require_once 'db.php';
require_auth();

// ... (récupération des notes depuis la DB) ...
$notes = $pdo->query("SELECT id, title, content FROM notes WHERE user_id = {$_SESSION['user_id']}")->fetchAll();

foreach ($notes as $note) {
    echo "<div>";
    echo "<h2>" . $note['title'] . "</h2>"; // VULNÉRABLE : Affichage direct
    echo "<p>" . $note['content'] . "</p>"; // VULNÉRABLE : Affichage direct
    echo "<a href='add_edit_note.php?id=" . $note['id'] . "'>Modifier</a>";
    echo "</div><hr>";
}
?>
```


**Payload de test :**
1.  Créez une note avec :
    *   **Titre :** `Ma note <script>alert('XSS Titre !');</script>`
    *   **Contenu :** `Contenu de ma note <img src=x onerror=alert('XSS Contenu !')>`
2.  **Résultat attendu :** Des boîtes d'alerte s'affichent lors de la visualisation de la note.

### 5. Application Sécurisée

#### a. Correction SQL Injection (Requêtes Préparées)

**Fichier :** `index.php` (partie traitement du formulaire)


```php
<?php
require_once 'db.php';

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $username = $_POST['username'];
    $password = $_POST['password'];

    // SÉCURISÉ : Utilisation de requêtes préparées avec des paramètres nommés
    $sql = "SELECT id, username FROM users WHERE username = :username AND password = :password";
    $stmt = $pdo->prepare($sql);
    $stmt->bindParam(':username', $username);
    $stmt->bindParam(':password', $password);
    $stmt->execute();

    if ($user = $stmt->fetch()) {
        $_SESSION['user_id'] = $user['id'];
        $_SESSION['username'] = $user['username'];
        header('Location: dashboard.php');
        exit();
    } else {
        $error = "Nom d'utilisateur ou mot de passe incorrect.";
    }
}
?>
<!-- Reste du HTML du formulaire de connexion -->
```


**Justification :** Les requêtes préparées séparent la requête SQL des données. Le moteur de base de données compile la requête une fois et traite les paramètres comme des données brutes, jamais comme du code exécutable. Cela neutralise toute tentative d'injection SQL. L'impact est une protection robuste contre les injections.

#### b. Correction XSS (Encodage HTML)

**Fichier :** `dashboard.php` (partie affichage des notes)


```php
<?php
require_once 'db.php';
require_auth();

// ... (récupération des notes depuis la DB) ...
$stmt = $pdo->prepare("SELECT id, title, content FROM notes WHERE user_id = :user_id");
$stmt->bindParam(':user_id', $_SESSION['user_id']);
$stmt->execute();
$notes = $stmt->fetchAll();

foreach ($notes as $note) {
    echo "<div>";
    // SÉCURISÉ : Utilisation de htmlspecialchars() pour encoder les sorties utilisateur
    echo "<h2>" . htmlspecialchars($note['title'], ENT_QUOTES, 'UTF-8') . "</h2>";
    echo "<p>" . htmlspecialchars($note['content'], ENT_QUOTES, 'UTF-8') . "</p>";
    echo "<a href='add_edit_note.php?id=" . htmlspecialchars($note['id'], ENT_QUOTES, 'UTF-8') . "'>Modifier</a>";
    echo "</div><hr>";
}
?>
```


**Justification :** La fonction `htmlspecialchars()` convertit les caractères spéciaux HTML (`<`, `>`, `&`, `"`, `'`) en leurs entités HTML correspondantes. Ainsi, un script `<script>alert('XSS !');</script>` sera affiché comme `&lt;script&gt;alert('XSS !');&lt;/script&gt;` et ne sera pas exécuté par le navigateur. L'impact est une protection efficace contre les attaques XSS.

### 6. Utilisation de l'IA

J'ai utilisé l'IA pour obtenir des exemples de code boilerplate pour l'application PHP simple et pour confirmer la syntaxe correcte des requêtes préparées avec PDO et de la fonction `htmlspecialchars()`. L'IA a également aidé à générer des payloads de test XSS variés.

---

## Chapitre 2 : Solution avec Node.js, Express et SQLite

Cette solution utilise Node.js avec le framework Express pour le serveur web, le module `sqlite3` pour l'interaction avec la base de données SQLite, et le moteur de template EJS pour le rendu HTML.

### 1. Description de l'Application et Stack

L'application est un "Gestionnaire de Notes" similaire à la solution PHP.
*   **Stack :** Node.js 20.x, Express 4.x, `sqlite3` module, EJS (Embedded JavaScript) pour les templates.
*   **Fichiers :**
    *   `server.js` : Fichier principal du serveur Express.
    *   `views/login.ejs` : Template du formulaire de connexion.
    *   `views/dashboard.ejs` : Template pour afficher les notes.
    *   `views/add_edit_note.ejs` : Template du formulaire d'ajout/modification.

### 2. Base de Données (SQLite)

La même structure de base de données que pour la solution PHP est utilisée. Le fichier `database.sqlite` sera créé et initialisé au démarrage du serveur si nécessaire.

### 3. Initialisation du Projet


```bash
mkdir note-manager-node
cd note-manager-node
npm init -y
npm install express sqlite3 ejs express-session
```


### 4. Application Vulnérable (`server.js`)


```javascript
// server.js (version vulnérable)
const express = require('express');
const sqlite3 = require('sqlite3').verbose();
const session = require('express-session');
const app = express();
const port = 3000;

// Configuration de la base de données
const db = new sqlite3.Database('./database.sqlite', (err) => {
    if (err) {
        console.error(err.message);
    } else {
        console.log('Connected to the SQLite database.');
        db.run(`CREATE TABLE IF NOT EXISTS users (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            username TEXT NOT NULL UNIQUE,
            password TEXT NOT NULL
        )`);
        db.run(`CREATE TABLE IF NOT EXISTS notes (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            user_id INTEGER NOT NULL,
            title TEXT NOT NULL,
            content TEXT,
            FOREIGN KEY (user_id) REFERENCES users(id)
        )`);
        // Insérer un utilisateur de test si non existant
        db.run(`INSERT OR IGNORE INTO users (username, password) VALUES ('testuser', 'password')`);
    }
});

// Configuration Express
app.set('view engine', 'ejs');
app.use(express.urlencoded({ extended: true })); // Pour parser les données de formulaire
app.use(express.static('public')); // Pour les fichiers statiques (CSS, JS)

// Configuration de session
app.use(session({
    secret: 'supersecretkey', // À changer pour une clé forte en production
    resave: false,
    saveUninitialized: true,
    cookie: { secure: false } // Mettre à true avec HTTPS
}));

// Middleware d'authentification
function requireAuth(req, res, next) {
    if (!req.session.user_id) {
        return res.redirect('/');
    }
    next();
}

// Routes
app.get('/', (req, res) => {
    res.render('login', { error: null });
});

app.post('/login', (req, res) => {
    const { username, password } = req.body;

    // VULNÉRABLE : Concaténation directe des entrées utilisateur
    const sql = `SELECT id, username FROM users WHERE username = '${username}' AND password = '${password}'`;
    db.get(sql, (err, user) => {
        if (err) {
            console.error(err.message);
            return res.render('login', { error: 'Erreur serveur.' });
        }
        if (user) {
            req.session.user_id = user.id;
            req.session.username = user.username;
            res.redirect('/dashboard');
        } else {
            res.render('login', { error: "Nom d'utilisateur ou mot de passe incorrect." });
        }
    });
});

app.get('/dashboard', requireAuth, (req, res) => {
    // VULNÉRABLE : Concaténation directe pour la récupération des notes
    const sql = `SELECT id, title, content FROM notes WHERE user_id = ${req.session.user_id}`;
    db.all(sql, (err, notes) => {
        if (err) {
            console.error(err.message);
            return res.render('dashboard', { error: 'Erreur lors de la récupération des notes.', notes: [] });
        }
        res.render('dashboard', { username: req.session.username, notes: notes, error: null });
    });
});

app.get('/add_note', requireAuth, (req, res) => {
    res.render('add_edit_note', { note: null, error: null });
});

app.post('/add_note', requireAuth, (req, res) => {
    const { title, content } = req.body;
    const user_id = req.session.user_id;

    // VULNÉRABLE : Concaténation directe des entrées utilisateur
    const sql = `INSERT INTO notes (user_id, title, content) VALUES (${user_id}, '${title}', '${content}')`;
    db.run(sql, function(err) {
        if (err) {
            console.error(err.message);
            return res.render('add_edit_note', { note: req.body, error: 'Erreur lors de l\'ajout de la note.' });
        }
        res.redirect('/dashboard');
    });
});

app.get('/logout', (req, res) => {
    req.session.destroy(() => {
        res.redirect('/');
    });
});

app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}`);
});
```


#### b. Vulnérabilité XSS (Template EJS)

**Fichier :** `views/dashboard.ejs`


```ejs
<!-- views/dashboard.ejs (version vulnérable) -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard</title>
</head>
<body>
    <h1>Bienvenue, <%= username %>!</h1>
    <a href="/add_note">Ajouter une nouvelle note</a> | <a href="/logout">Déconnexion</a>
    <hr>
    <% if (error) { %>
        <p style="color: red;"><%= error %></p>
    <% } %>
    <h2>Mes Notes</h2>
    <% if (notes.length === 0) { %>
        <p>Aucune note pour le moment.</p>
    <% } else { %>
        <% notes.forEach(note => { %>
            <div>
                <!-- VULNÉRABLE : Utilisation de <%- ... %> qui n'échappe PAS le HTML -->
                <h3><%- note.title %></h3>
                <p><%- note.content %></p>
                <a href="/edit_note/<%= note.id %>">Modifier</a>
            </div>
            <hr>
        <% }); %>
    <% } %>
</body>
</html>
```


**Payloads de test :** Identiques à la solution PHP.

### 5. Application Sécurisée (`server.js` et `views/dashboard.ejs`)

#### a. Correction SQL Injection (Requêtes Paramétrées)

**Fichier :** `server.js` (extraits modifiés)


```javascript
// ... (début du fichier inchangé) ...

app.post('/login', (req, res) => {
    const { username, password } = req.body;

    // SÉCURISÉ : Utilisation de requêtes paramétrées avec des placeholders (?)
    const sql = `SELECT id, username FROM users WHERE username = ? AND password = ?`;
    db.get(sql, [username, password], (err, user) => { // Les paramètres sont passés dans un tableau
        if (err) {
            console.error(err.message);
            return res.render('login', { error: 'Erreur serveur.' });
        }
        if (user) {
            req.session.user_id = user.id;
            req.session.username = user.username;
            res.redirect('/dashboard');
        } else {
            res.render('login', { error: "Nom d'utilisateur ou mot de passe incorrect." });
        }
    });
});

app.get('/dashboard', requireAuth, (req, res) => {
    // SÉCURISÉ : Utilisation de requêtes paramétrées
    const sql = `SELECT id, title, content FROM notes WHERE user_id = ?`;
    db.all(sql, [req.session.user_id], (err, notes) => { // Le user_id est passé comme paramètre
        if (err) {
            console.error(err.message);
            return res.render('dashboard', { error: 'Erreur lors de la récupération des notes.', notes: [] });
        }
        res.render('dashboard', { username: req.session.username, notes: notes, error: null });
    });
});

app.post('/add_note', requireAuth, (req, res) => {
    const { title, content } = req.body;
    const user_id = req.session.user_id;

    // SÉCURISÉ : Utilisation de requêtes paramétrées
    const sql = `INSERT INTO notes (user_id, title, content) VALUES (?, ?, ?)`;
    db.run(sql, [user_id, title, content], function(err) { // Les paramètres sont passés dans un tableau
        if (err) {
            console.error(err.message);
            return res.render('add_edit_note', { note: req.body, error: 'Erreur lors de l\'ajout de la note.' });
        }
        res.redirect('/dashboard');
    });
});

// ... (reste du fichier inchangé) ...
```


**Justification :** Le module `sqlite3` (et la plupart des pilotes de base de données Node.js) prend en charge les requêtes paramétrées. En utilisant des placeholders (`?`) et en passant les valeurs dans un tableau séparé, le pilote s'assure que les données sont correctement échappées avant d'être envoyées à la base de données, empêchant ainsi les injections SQL.

#### b. Correction XSS (Encodage HTML dans EJS)

**Fichier :** `views/dashboard.ejs` (extraits modifiés)


```ejs
<!-- views/dashboard.ejs (version sécurisée) -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard</title>
</head>
<body>
    <h1>Bienvenue, <%= username %>!</h1>
    <a href="/add_note">Ajouter une nouvelle note</a> | <a href="/logout">Déconnexion</a>
    <hr>
    <% if (error) { %>
        <p style="color: red;"><%= error %></p>
    <% } %>
    <h2>Mes Notes</h2>
    <% if (notes.length === 0) { %>
        <p>Aucune note pour le moment.</p>
    <% } else { %>
        <% notes.forEach(note => { %>
            <div>
                <!-- SÉCURISÉ : Utilisation de <%= ... %> qui échappe AUTOMATIQUEMENT le HTML dans EJS -->
                <h3><%= note.title %></h3>
                <p><%= note.content %></p>
                <a href="/edit_note/<%= note.id %>">Modifier</a>
            </div>
            <hr>
        <% }); %>
    <% } %>
</body>
</html>
```


**Justification :** Le moteur de template EJS (comme la plupart des moteurs de templates modernes) fournit une syntaxe pour l'encodage HTML automatique. L'utilisation de `<%= variable %>` (avec le signe égal) échappe automatiquement les caractères HTML spéciaux, convertissant par exemple `<script>` en `&lt;script&gt;`. En revanche, `<%- variable %>` (avec le tiret) est utilisé pour afficher du HTML non échappé, ce qui est dangereux si la source est une entrée utilisateur. L'impact est une protection efficace contre les attaques XSS.

### 6. Utilisation de l'IA

L'IA a été utile pour générer le boilerplate de l'application Express, pour rappeler la syntaxe des requêtes paramétrées avec `sqlite3`, et pour confirmer les conventions d'encodage HTML dans les templates EJS. Elle a également aidé à structurer les fichiers et les routes de l'application.