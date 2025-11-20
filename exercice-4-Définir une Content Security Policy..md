Bonjour à toutes et à tous,

Ce TP a pour objectif de vous familiariser avec la mise en place d'une Content Security Policy (CSP), un mécanisme de sécurité fondamental pour protéger vos applications web.

---

### **Titre du TP :** Définition et implémentation d'une Content Security Policy (CSP)

### **Contexte :**
La Content Security Policy (CSP) est une couche de sécurité qui aide à prévenir les attaques de type Cross-Site Scripting (XSS) et l'injection de données. Elle permet aux administrateurs de serveurs de spécifier les sources de contenu que le navigateur est autorisé à charger et à exécuter pour une page web donnée. En définissant des règles strictes, vous réduisez considérablement la surface d'attaque de votre application.

### **Objectif du TP :**
Définir une Content Security Policy (CSP) appropriée et restrictive pour une application web, en ciblant spécifiquement la restriction des sources de scripts et de styles.

### **Prérequis :**
*   Connaissances de base en développement web (HTML, CSS, JavaScript).
*   Notions de base sur React (ou PHP si vous choisissez cette voie).
*   Un environnement de développement avec Node.js (pour React) ou un serveur web (pour PHP).
*   Un éditeur de code et un navigateur web moderne avec les outils de développement.

---

### **Mini-Projet : Application "Mon Portfolio Simple"**

Nous allons travailler sur une application web minimale qui simule un portfolio personnel. Cette application est volontairement simple pour nous permettre de nous concentrer pleinement sur la configuration de la CSP.

**Scénario (React avec Vite) :**

Vous disposez d'une application React créée avec Vite. Elle contient :
1.  Un composant principal affichant un titre et un paragraphe.
2.  Un style CSS interne (inline) appliqué à un élément.
3.  Un fichier CSS externe (`App.css`).
4.  Le script JavaScript de l'application React elle-même.
5.  Une image de profil hébergée sur un CDN (par exemple, `https://picsum.photos/200`).
6.  Un appel à une API publique (par exemple, `https://jsonplaceholder.typicode.com/todos/1`) pour afficher une donnée simple.

**Mise en place de l'environnement (si nécessaire) :**

Si vous n'avez pas d'application existante, créez une application React simple avec Vite :
```bash
npm create vite@latest mon-portfolio-csp -- --template react
cd mon-portfolio-csp
npm install
npm run dev
```
Modifiez `src/App.jsx` et `src/App.css` pour inclure les éléments décrits dans le scénario.

*Exemple `src/App.jsx` (à adapter) :*
```jsx
import { useState, useEffect } from 'react';
import './App.css';

function App() {
  const [todo, setTodo] = useState(null);

  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/todos/1')
      .then(response => response.json())
      .then(data => setTodo(data));
  }, []);

  return (
    <div className="App">
      <header className="App-header">
        <h1 style={{ color: 'blue' }}>Mon Portfolio Simple</h1> {/* Style inline */}
        <img src="https://picsum.photos/200" alt="Profil" className="profile-pic" /> {/* Image externe */}
        <p>Bienvenue sur mon portfolio. Voici une donnée de l'API :</p>
        {todo ? <p>Tâche : {todo.title}</p> : <p>Chargement...</p>}
      </header>
    </div>
  );
}

export default App;
```

*Exemple `src/App.css` :*
```css
.App {
  text-align: center;
  font-family: sans-serif;
}

.App-header {
  background-color: #282c34;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-size: calc(10px + 2vmin);
  color: white;
}

.profile-pic {
  border-radius: 50%;
  margin: 20px;
}
```

---

### **Tâches à réaliser :**

1.  **Analyse des sources actuelles :**
    *   Lancez votre application (`npm run dev`) et ouvrez les outils de développement de votre navigateur (onglets "Réseau" et "Console").
    *   Identifiez toutes les sources de scripts, de styles, d'images et de requêtes réseau (API) utilisées par l'application. Notez leurs origines (même domaine, CDN, etc.).

2.  **Définition d'une CSP initiale (mode `report-only`) :**
    *   La CSP est généralement définie via un en-tête HTTP `Content-Security-Policy` ou `Content-Security-Policy-Report-Only`. Pour ce TP, nous allons l'ajouter via une balise `<meta>` dans `public/index.html` pour simplifier, mais gardez à l'esprit que l'en-tête HTTP est la méthode préférée en production.
    *   Commencez par une CSP en mode `report-only`. Cela permet de détecter les violations sans bloquer le contenu, les erreurs apparaîtront dans la console du navigateur.
    *   Définissez une politique permissive pour commencer, puis resserrez-la.
    *   Votre première ébauche devrait ressembler à ceci (à placer dans la section `<head>` de `public/index.html`) :
        ```html
        <meta http-equiv="Content-Security-Policy-Report-Only" content="default-src 'self'; script-src 'self'; style-src 'self'; img-src 'self'; connect-src 'self';">
        ```
    *   Actualisez votre application. Observez attentivement les violations dans la console du navigateur. Quelles sont les sources bloquées ? Pourquoi ?

3.  **Raffinement de la CSP :**
    *   **Scripts :**
        *   Votre application React est un script. Elle est chargée depuis votre propre domaine (`'self'`).
        *   Si vous aviez des scripts inline (des balises `<script>` avec du code directement dans le HTML), ils seraient bloqués par `'self'`. Comment les autoriser de manière sécurisée ? (Indices : `nonce` ou `hash` sont à privilégier par rapport à `unsafe-inline`).
    *   **Styles :**
        *   Le fichier `App.css` est `'self'`.
        *   Le style inline (`style={{ color: 'blue' }}`) sera bloqué. Comment l'autoriser ? (Mêmes indices que pour les scripts : `nonce`, `hash`, ou `unsafe-inline` en dernier recours).
    *   **Images :**
        *   L'image `https://picsum.photos/200` n'est pas `'self'`. Ajoutez la source appropriée à la directive `img-src`.
    *   **Connexions API :**
        *   L'appel à `https://jsonplaceholder.typicode.com` n'est pas `'self'`. Ajoutez la source à la directive `connect-src`.
    *   **Directives supplémentaires :**
        *   Pensez à ajouter des directives comme `object-src 'none'`, `base-uri 'self'`, `form-action 'self'` pour une sécurité accrue.

4.  **Test et validation :**
    *   Après chaque modification de votre CSP, actualisez l'application et vérifiez la console.
    *   Assurez-vous qu'aucun contenu légitime n'est bloqué et qu'il n'y a plus de violations.
    *   Une fois satisfait de votre politique en mode `report-only`, passez de `Content-Security-Policy-Report-Only` à `Content-Security-Policy` pour activer la politique de manière stricte.

5.  **Gestion des cas complexes (Réflexion) :**
    *   Comment géreriez-vous une bibliothèque tierce chargée via un CDN (par exemple, Bootstrap, Font Awesome) ?
    *   Si vous aviez des polices externes (par exemple, Google Fonts), quelle directive utiliseriez-vous ?
    *   Comment mettre en place un mécanisme de *reporting* pour recevoir les violations CSP en production ? (Indice : `report-uri` ou `report-to`).

---

### **Conseils pour l'utilisation de l'IA :**

L'IA est un excellent assistant pour ce type d'exercice. Utilisez-la intelligemment pour maximiser votre apprentissage :
*   **Pour comprendre les directives :** Demandez-lui des explications détaillées sur `script-src`, `style-src`, `nonce`, `hash`, `default-src`, etc.
*   **Pour générer une ébauche :** Vous pouvez lui donner votre liste de sources et lui demander une CSP initiale. *Attention : Ne copiez-collez pas aveuglément. L'IA peut faire des erreurs ou proposer des solutions trop permissives pour votre contexte.*
*   **Pour le débogage :** Si vous avez une violation CSP que vous ne comprenez pas, décrivez-lui le message d'erreur et votre CSP actuelle.
*   **Pour explorer des alternatives :** Demandez-lui comment gérer un cas spécifique (par exemple, "Comment autoriser un script inline sans `unsafe-inline` ?").

Votre objectif est de comprendre *pourquoi* une directive est nécessaire et *comment* elle fonctionne, pas seulement d'obtenir la bonne chaîne de caractères. L'IA est un outil pour approfondir votre compréhension, pas un substitut à celle-ci.

---

### **Livrables :**

*   Le fichier `public/index.html` (ou l'équivalent si vous avez choisi PHP) contenant la balise `<meta>` avec votre CSP finale.
*   Une brève explication (quelques phrases) pour chaque directive majeure utilisée (`script-src`, `style-src`, `img-src`, `connect-src`) justifiant les sources autorisées.
*   (Optionnel) Vos réflexions sur la gestion des cas complexes.

Bon courage pour ce TP !