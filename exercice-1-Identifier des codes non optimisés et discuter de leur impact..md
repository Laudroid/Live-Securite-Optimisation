Salut à toutes et à tous !

Bienvenue dans ce TP axé sur l'optimisation et la sécurité du code. Notre objectif est de développer votre œil critique pour identifier les zones d'amélioration dans des extraits de code courants.

---

### **TP : Chasse aux Inefficacités – Audit de Code PHP & JS**

**Objectif du TP :**
Identifier et analyser des extraits de code PHP et JavaScript présentant des problèmes de lisibilité, de performance, de maintenabilité ou de sécurité, et discuter de l'impact concret de ces problèmes.

**Contexte du Mini-Projet :**
Imaginez que vous intégrez une équipe de développement et que l'on vous confie la tâche d'auditer un module existant. Ce module est responsable de la récupération et de l'affichage d'un catalogue de produits sur un site e-commerce. Des retours utilisateurs font état de lenteurs occasionnelles, et les développeurs précédents ont eu du mal à faire évoluer cette partie du code. Votre mission est de débusquer les points faibles.

**Consignes Générales :**

1.  **Analyse Approfondie :** Pour chaque extrait de code, votre mission est d'identifier tous les problèmes potentiels. Ne vous limitez pas à un seul type de problème par extrait.
2.  **Solutions et Justifications :** Pour chaque problème identifié, proposez une ou plusieurs solutions concrètes. Expliquez clairement pourquoi votre solution est meilleure et quel est l'impact positif attendu (en termes de performance, lisibilité, maintenabilité, sécurité, etc.).
3.  **Utilisation de l'IA :** **L'utilisation d'outils d'IA (ChatGPT, Copilot, Gemini, etc.) est non seulement autorisée, mais encouragée.** Considérez ces outils comme des assistants puissants pour vous aider à identifier des pistes, générer des exemples de code corrigé ou explorer des concepts. Cependant, votre valeur ajoutée réside dans votre capacité à *analyser*, *critiquer* et *synthétiser* leurs suggestions, puis à les *reformuler* avec vos propres mots et votre compréhension. Ne vous contentez pas de copier-coller.
4.  **Pragmatisme :** Concentrez-vous sur des améliorations réalistes et applicables dans un contexte de projet.

---

### **Énoncé des Exercices**

#### **Exercice 1 : Backend PHP - Récupération et Traitement des Produits**

Voici le code PHP responsable de la récupération des produits depuis une base de données et de l'ajout de détails supplémentaires.

```php
<?php

// Fichier config.php (simulé)
define('DB_HOST', 'localhost');
define('DB_USER', 'root');
define('DB_PASS', 'root123'); // Mot de passe en dur, oh là là !
define('DB_NAME', 'ecommerce_db');

// Fichier products_api.php
function getProductsWithDetails() {
    $conn = new mysqli(DB_HOST, DB_USER, DB_PASS, DB_NAME);
    if ($conn->connect_error) {
        die("Connection failed: " . $conn->connect_error);
    }

    $products = [];
    $sql = "SELECT * FROM products"; // Récupère toutes les colonnes, même inutiles
    $result = $conn->query($sql);

    if ($result->num_rows > 0) {
        while($row = $result->fetch_assoc()) {
            $product = $row;
            // Requête N+1 : Récupère le nom de la catégorie pour chaque produit individuellement
            $cat_sql = "SELECT name FROM categories WHERE id = " . $product['category_id']; // Risque d'injection SQL
            $cat_result = $conn->query($cat_sql);
            if ($cat_result && $cat_row = $cat_result->fetch_assoc()) {
                $product['category_name'] = $cat_row['name'];
            } else {
                $product['category_name'] = 'N/A';
            }
            $products[] = $product;
        }
    }
    $conn->close();
    return $products;
}

// Utilisation (simulée pour l'API)
// header('Content-Type: application/json');
// echo json_encode(getProductsWithDetails());

?>
```

**Votre mission pour cet exercice :**

1.  **Identifier les problèmes :** Listez tous les problèmes que vous détectez dans ce code, en les classant par catégorie (performance, sécurité, lisibilité, maintenabilité).
2.  **Proposer des solutions :** Pour chaque problème, proposez une solution concrète et fournissez le code PHP corrigé correspondant.
3.  **Expliquer l'impact :** Décrivez l'impact positif de chaque optimisation sur le comportement global du module.

#### **Exercice 2 : Frontend JavaScript - Affichage Dynamique des Produits**

Voici le code JavaScript chargé de récupérer les données des produits via l'API PHP (simulée) et de les afficher sur la page.

```javascript
// Fichier script.js
let productContainer = document.getElementById('product-list'); // Variable globale, sélecteur en dur

function displayProducts(products) {
    if (!products || products.length === 0) {
        productContainer.innerHTML = '<p>Aucun produit à afficher.</p>';
        return;
    }

    // Boucle inefficace pour de nombreux éléments
    for (let i = 0; i < products.length; i++) {
        let product = products[i];
        let productDiv = document.createElement('div');
        productDiv.className = 'product-item'; // Manipulation directe du DOM
        productDiv.style.border = '1px solid #ccc'; // Styles inline, pas top
        productDiv.style.padding = '10px';
        productDiv.style.marginBottom = '10px';

        let title = document.createElement('h3');
        title.textContent = product.name;
        productDiv.appendChild(title);

        let category = document.createElement('p');
        category.textContent = 'Catégorie: ' + product.category_name;
        productDiv.appendChild(category);

        let price = document.createElement('p');
        price.textContent = 'Prix: ' + product.price + ' €';
        productDiv.appendChild(price);

        productContainer.appendChild(productDiv); // Ajout au DOM à chaque itération
    }
}

// Simulation de récupération de données
async function fetchAndDisplayProducts() {
    console.time('JS_Display'); // Mesure de performance
    try {
        const response = await fetch('/api/products.php'); // URL en dur
        const data = await response.json();
        displayProducts(data);
    } catch (error) {
        console.error("Erreur lors de la récupération des produits:", error); // Gestion d'erreur minimale
        productContainer.innerHTML = '<p>Erreur de chargement des produits.</p>';
    }
    console.timeEnd('JS_Display');
}

// Appel initial
document.addEventListener('DOMContentLoaded', fetchAndDisplayProducts);
```

**Votre mission pour cet exercice :**

1.  **Identifier les problèmes :** Listez tous les problèmes que vous détectez dans ce code, en les classant par catégorie (performance, sécurité, lisibilité, maintenabilité).
2.  **Proposer des solutions :** Pour chaque problème, proposez une solution concrète et fournissez le code JavaScript corrigé correspondant.
3.  **Expliquer l'impact :** Décrivez l'impact positif de chaque optimisation sur l'expérience utilisateur et la maintenabilité du frontend.

#### **Exercice 3 : Discussion et Synthèse**

Au-delà des corrections techniques, prenez du recul et discutez de l'impact global de ces types de code non optimisés sur :

1.  **L'expérience utilisateur :** Comment ces problèmes affectent-ils directement l'utilisateur final ?
2.  **Les coûts d'infrastructure :** Quel est l'impact sur les ressources serveur, la bande passante, etc. ?
3.  **La productivité des équipes de développement :** Comment un tel code ralentit-il le travail des développeurs et augmente-t-il les risques d'erreurs ?
4.  **La sécurité du système :** Quels sont les risques majeurs introduits par des pratiques de codage non sécurisées ?
5.  **Prévention :** Comment une culture de l'optimisation, de la revue de code et l'utilisation d'outils peuvent-elles prévenir l'apparition de ces problèmes ?

---

### **Rendu Attendu**

Un document (PDF, Markdown, ou équivalent) structuré par exercice, incluant :

*   Le code original de chaque section.
*   La liste des problèmes identifiés, classés par type (performance, sécurité, lisibilité, maintenabilité).
*   Pour chaque problème, une explication claire et une proposition de solution (avec le code corrigé).
*   L'explication de l'impact des optimisations.
*   Les réponses détaillées aux questions de l'Exercice 3.
*   *(Optionnel mais apprécié)* : Mentionnez si et comment l'IA vous a aidé pour chaque section, et comment vous avez validé ou adapté ses suggestions.

### **Critères d'Évaluation**

*   **Précision :** Identification exhaustive et correcte des problèmes.
*   **Pertinence :** Qualité et efficacité des solutions proposées.
*   **Clarté :** Explications concises et compréhensibles des problèmes et des impacts.
*   **Compréhension :** Maîtrise des concepts d'optimisation et de sécurité démontrée dans l'Exercice 3.
*   **Rigueur :** Qualité de la rédaction, de la structuration du document et du code corrigé.

Bonne chasse aux inefficacités ! N'hésitez pas à poser des questions si vous bloquez.