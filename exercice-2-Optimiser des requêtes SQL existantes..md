Bonjour à toutes et à tous !

Aujourd'hui, nous allons plonger au cœur de la performance des bases de données avec un TP axé sur l'optimisation des requêtes SQL. C'est un domaine où chaque milliseconde compte et où une bonne compréhension des mécanismes internes peut faire une différence colossale.

---

## TP : Optimisation de Requêtes SQL - Cas Pratique E-commerce

### Objectif du TP

L'objectif de ce TP est de vous permettre d'identifier les goulots d'étranglement potentiels dans des requêtes SQL existantes, de proposer des stratégies d'indexation pertinentes et de réécrire ces requêtes pour en améliorer significativement les performances.

### Contexte du Mini-Projet : Une Boutique en Ligne

Nous allons travailler sur un schéma de base de données simplifié représentant une boutique en ligne. Comme toute application, celle-ci génère des requêtes qui, avec le temps et l'augmentation du volume de données, peuvent devenir lentes et impacter l'expérience utilisateur. Votre mission est de les rendre plus efficaces.

### Base de Données Fournie

Voici la structure de la base de données que nous allons utiliser. Pour les besoins de ce TP, imaginez que ces tables contiennent déjà un volume conséquent de données (plusieurs centaines de milliers, voire millions d'enregistrements).

```sql
-- Table des Clients
CREATE TABLE Customers (
    customer_id INTEGER PRIMARY KEY AUTOINCREMENT,
    first_name TEXT NOT NULL,
    last_name TEXT NOT NULL,
    email TEXT UNIQUE NOT NULL,
    registration_date DATE NOT NULL,
    city TEXT,
    country TEXT
);

-- Table des Produits
CREATE TABLE Products (
    product_id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    description TEXT,
    price REAL NOT NULL,
    category TEXT NOT NULL,
    stock_quantity INTEGER NOT NULL
);

-- Table des Commandes
CREATE TABLE Orders (
    order_id INTEGER PRIMARY KEY AUTOINCREMENT,
    customer_id INTEGER NOT NULL,
    order_date DATE NOT NULL,
    total_amount REAL NOT NULL,
    status TEXT NOT NULL, -- Ex: 'Pending', 'Shipped', 'Delivered', 'Cancelled'
    FOREIGN KEY (customer_id) REFERENCES Customers(customer_id)
);

-- Table des Articles de Commande (détail)
CREATE TABLE Order_Items (
    order_item_id INTEGER PRIMARY KEY AUTOINCREMENT,
    order_id INTEGER NOT NULL,
    product_id INTEGER NOT NULL,
    quantity INTEGER NOT NULL,
    unit_price REAL NOT NULL,
    FOREIGN KEY (order_id) REFERENCES Orders(order_id),
    FOREIGN KEY (product_id) REFERENCES Products(product_id)
);
```

*(Note : Dans un environnement réel, des données de test massives seraient fournies pour simuler les performances. Ici, nous nous concentrons sur l'analyse théorique et la proposition.)*

---

### Exercice : Analyse et Optimisation des Requêtes

Pour chaque requête ci-dessous, suivez les étapes suivantes :

1.  **Analyse du Problème :** Identifiez le ou les problèmes potentiels de performance de la requête telle qu'elle est écrite, en considérant le volume de données.
2.  **Proposition d'Indexes :** Proposez un ou plusieurs index pertinents pour améliorer les performances de cette requête. Précisez sur quelle(s) table(s) et quelle(s) colonne(s) l'index doit être créé.
3.  **Réécriture de la Requête (si nécessaire) :** Réécrivez la requête si vous estimez qu'une modification de sa structure peut la rendre plus performante, en tirant parti des index proposés ou en améliorant sa logique.
4.  **Justification :** Expliquez pourquoi vous avez choisi ces indexes et/ou cette réécriture. Quel est l'impact attendu sur les performances ?

---

#### Requête 1 : Clients d'une Ville Spécifique

```sql
SELECT customer_id, first_name, last_name, email, registration_date
FROM Customers
WHERE city = 'Paris';
```

#### Requête 2 : Commandes Passées par des Clients d'un Pays Donné

```sql
SELECT o.order_id, o.order_date, c.first_name, c.last_name, o.total_amount
FROM Orders o
JOIN Customers c ON o.customer_id = c.customer_id
WHERE c.country = 'France'
ORDER BY o.order_date DESC;
```

#### Requête 3 : Chiffre d'Affaires par Catégorie de Produit

```sql
SELECT p.category, SUM(oi.quantity * oi.unit_price) AS total_sales
FROM Order_Items oi
JOIN Products p ON oi.product_id = p.product_id
GROUP BY p.category
ORDER BY total_sales DESC;
```

#### Requête 4 : Les 10 Produits les Plus Vendus le Mois Dernier (en valeur)

```sql
SELECT p.name, SUM(oi.quantity * oi.unit_price) AS total_revenue
FROM Order_Items oi
JOIN Products p ON oi.product_id = p.product_id
JOIN Orders o ON oi.order_id = o.order_id
WHERE o.order_date >= DATE('now', '-1 month')
GROUP BY p.name
ORDER BY total_revenue DESC
LIMIT 10;
```

#### Requête 5 : Clients Ayant Passé Plus de 5 Commandes

```sql
SELECT c.first_name, c.last_name, COUNT(o.order_id) AS num_orders
FROM Customers c
JOIN Orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.first_name, c.last_name
HAVING COUNT(o.order_id) > 5
ORDER BY num_orders DESC;
```

---

### Livrables

Vous devrez soumettre :

1.  Un script SQL (`indexes.sql`) contenant toutes les commandes `CREATE INDEX` que vous proposez.
2.  Un script SQL (`optimized_queries.sql`) contenant les versions optimisées de chaque requête (si vous les avez réécrites).
3.  Un document texte ou Markdown (`rapport_optimisation.md` ou `.txt`) détaillant pour chaque requête :
    *   L'analyse du problème initial.
    *   Les indexes proposés et leur justification.
    *   La requête optimisée (si différente de l'originale) et la justification de la réécriture.

### Conseils du Formateur

*   **Pensez `EXPLAIN` (ou `EXPLAIN ANALYZE`) :** Dans un environnement réel, c'est votre meilleur ami pour comprendre comment la base de données exécute une requête et où se situent les coûts. Même si vous ne pouvez pas l'exécuter ici, imaginez son résultat.
*   **Indexes composites :** Parfois, un index sur plusieurs colonnes est plus efficace qu'un index sur une seule. Réfléchissez à l'ordre des colonnes dans un index composite.
*   **Couverture d'index :** Un index peut parfois "couvrir" une requête entière, évitant ainsi l'accès à la table elle-même.
*   **Amusez-vous !** L'optimisation est un art autant qu'une science. C'est l'occasion d'aiguiser votre sens critique et votre logique.

Bon courage à toutes et à tous !