Voici deux solutions possibles pour le TP d'optimisation de requêtes SQL, présentées sous forme de chapitres distincts, avec les justifications et les scripts SQL associés.

---

# Solutions TP : Optimisation de Requêtes SQL - Cas Pratique E-commerce

Ce document propose des analyses et des optimisations pour les requêtes SQL fournies, en se concentrant sur les stratégies d'indexation et la réécriture de requêtes pour améliorer les performances dans un contexte de données volumineuses.

## Chapitre 1 : Solution d'Optimisation - Approche 1

### Script SQL des Indexes Proposés (indexes_solution1.sql)


```sql
-- Indexes pour la Solution 1

-- Requête 1 : Clients d'une Ville Spécifique
CREATE INDEX idx_customers_city ON Customers (city);

-- Requête 2 : Commandes Passées par des Clients d'un Pays Donné
CREATE INDEX idx_customers_country ON Customers (country);
CREATE INDEX idx_orders_customer_id_order_date ON Orders (customer_id, order_date DESC); -- Composite pour JOIN et ORDER BY

-- Requête 3 : Chiffre d'Affaires par Catégorie de Produit
CREATE INDEX idx_order_items_product_id ON Order_Items (product_id);
CREATE INDEX idx_products_category_id ON Products (product_id, category); -- Composite pour JOIN et GROUP BY

-- Requête 4 : Les 10 Produits les Plus Vendus le Mois Dernier (en valeur)
CREATE INDEX idx_orders_order_date ON Orders (order_date);
-- Les FKs sont souvent indexées par défaut, mais les ajouter explicitement ne nuit pas.
CREATE INDEX idx_order_items_order_id ON Order_Items (order_id);
CREATE INDEX idx_order_items_product_id_quantity_unit_price ON Order_Items (product_id, quantity, unit_price); -- Couvrant pour calcul et JOIN
CREATE INDEX idx_products_name_id ON Products (name, product_id); -- Pour GROUP BY et JOIN

-- Requête 5 : Clients Ayant Passé Plus de 5 Commandes
CREATE INDEX idx_orders_customer_id ON Orders (customer_id);
-- customer_id est déjà PK dans Customers, donc indexé.
```


### Script SQL des Requêtes Optimisées (optimized_queries_solution1.sql)


```sql
-- Requête 1 : Clients d'une Ville Spécifique
SELECT customer_id, first_name, last_name, email, registration_date
FROM Customers
WHERE city = 'Paris';
-- Pas de réécriture nécessaire, l'index est suffisant.

-- Requête 2 : Commandes Passées par des Clients d'un Pays Donné
SELECT o.order_id, o.order_date, c.first_name, c.last_name, o.total_amount
FROM Orders o
JOIN Customers c ON o.customer_id = c.customer_id
WHERE c.country = 'France'
ORDER BY o.order_date DESC;
-- Pas de réécriture nécessaire, les indexes sont suffisants.

-- Requête 3 : Chiffre d'Affaires par Catégorie de Produit
SELECT p.category, SUM(oi.quantity * oi.unit_price) AS total_sales
FROM Order_Items oi
JOIN Products p ON oi.product_id = p.product_id
GROUP BY p.category
ORDER BY total_sales DESC;
-- Pas de réécriture nécessaire, les indexes sont suffisants.

-- Requête 4 : Les 10 Produits les Plus Vendus le Mois Dernier (en valeur)
SELECT p.name, SUM(oi.quantity * oi.unit_price) AS total_revenue
FROM Order_Items oi
JOIN Products p ON oi.product_id = p.product_id
JOIN Orders o ON oi.order_id = o.order_id
WHERE o.order_date >= DATE('now', '-1 month')
GROUP BY p.name
ORDER BY total_revenue DESC
LIMIT 10;
-- Pas de réécriture fondamentale nécessaire, les indexes sont la clé.

-- Requête 5 : Clients Ayant Passé Plus de 5 Commandes
SELECT c.first_name, c.last_name, COUNT(o.order_id) AS num_orders
FROM Customers c
JOIN Orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.first_name, c.last_name
HAVING COUNT(o.order_id) > 5
ORDER BY num_orders DESC;
-- Pas de réécriture nécessaire, les indexes sont suffisants.
```


### Analyse et Justification des Optimisations

#### Requête 1 : Clients d'une Ville Spécifique

*   **Analyse du Problème :** Sans index sur la colonne `city`, la base de données devrait effectuer un scan complet de la table `Customers` pour trouver tous les clients résidant à 'Paris'. Avec des millions d'enregistrements, cette opération serait très coûteuse en I/O disque et en temps CPU.
*   **Proposition d'Indexes :**
    *   `CREATE INDEX idx_customers_city ON Customers (city);`
*   **Réécriture de la Requête :** Aucune réécriture n'est nécessaire.
*   **Justification :** L'index sur `Customers.city` permet au moteur de base de données de localiser directement les lignes correspondant à `'Paris'` sans avoir à parcourir toute la table. L'impact attendu est une réduction drastique du temps de réponse pour cette requête, passant d'un scan de table à une recherche d'index rapide.

#### Requête 2 : Commandes Passées par des Clients d'un Pays Donné

*   **Analyse du Problème :** La requête implique une jointure entre `Orders` et `Customers`, un filtre sur `c.country` et un tri sur `o.order_date`. Sans indexes appropriés, la recherche du pays dans `Customers` serait lente, la jointure pourrait être inefficace, et le tri nécessiterait une opération de tri en mémoire ou sur disque.
*   **Proposition d'Indexes :**
    *   `CREATE INDEX idx_customers_country ON Customers (country);`
    *   `CREATE INDEX idx_orders_customer_id_order_date ON Orders (customer_id, order_date DESC);`
*   **Réécriture de la Requête :** Aucune réécriture n'est nécessaire.
*   **Justification :**
    *   L'index sur `Customers.country` accélère la localisation des clients du pays spécifié.
    *   L'index composite `Orders(customer_id, order_date DESC)` est crucial. `customer_id` facilite la jointure avec `Customers`, et `order_date DESC` permet au moteur de base de données de récupérer les résultats déjà triés, évitant une opération de tri coûteuse. L'ordre des colonnes dans l'index composite est important : `customer_id` est utilisé dans la clause `ON` de la jointure, et `order_date` pour le tri. L'impact est une exécution beaucoup plus rapide de la requête grâce à des recherches d'index efficaces et un tri pré-calculé.

#### Requête 3 : Chiffre d'Affaires par Catégorie de Produit

*   **Analyse du Problème :** Cette requête joint `Order_Items` et `Products`, puis regroupe les résultats par `p.category` pour calculer la somme. Sans indexes, la jointure serait lente, et le regroupement nécessiterait un scan complet des tables jointes.
*   **Proposition d'Indexes :**
    *   `CREATE INDEX idx_order_items_product_id ON Order_Items (product_id);`
    *   `CREATE INDEX idx_products_category_id ON Products (product_id, category);`
*   **Réécriture de la Requête :** Aucune réécriture n'est nécessaire.
*   **Justification :**
    *   L'index sur `Order_Items.product_id` (clé étrangère) accélère la jointure avec la table `Products`.
    *   L'index composite `Products(product_id, category)` est bénéfique car `product_id` est utilisé dans la jointure et `category` est utilisé pour le regroupement. Cet index peut potentiellement "couvrir" la partie de la requête concernant la table `Products`, permettant au moteur de base de données de ne pas avoir à accéder à la table elle-même pour récupérer la catégorie. L'impact est une accélération significative de la jointure et de l'opération de regroupement.

#### Requête 4 : Les 10 Produits les Plus Vendus le Mois Dernier (en valeur)

*   **Analyse du Problème :** C'est la requête la plus complexe, impliquant trois jointures, un filtre sur une date, une agrégation, un regroupement, un tri et une limite. Sans indexes, chaque étape serait coûteuse, en particulier le filtre temporel et les jointures.
*   **Proposition d'Indexes :**
    *   `CREATE INDEX idx_orders_order_date ON Orders (order_date);`
    *   `CREATE INDEX idx_order_items_order_id ON Order_Items (order_id);` (pour la jointure avec `Orders`)
    *   `CREATE INDEX idx_order_items_product_id_quantity_unit_price ON Order_Items (product_id, quantity, unit_price);`
    *   `CREATE INDEX idx_products_name_id ON Products (name, product_id);`
*   **Réécriture de la Requête :** Aucune réécriture fondamentale n'est nécessaire, la structure est déjà efficace avec les bons indexes.
*   **Justification :**
    *   L'index sur `Orders.order_date` est essentiel pour filtrer rapidement les commandes du mois dernier.
    *   Les indexes sur les clés étrangères (`order_id` dans `Order_Items`, `product_id` dans `Order_Items`) sont cruciaux pour optimiser les jointures.
    *   L'index composite `Order_Items(product_id, quantity, unit_price)` est un index couvrant pour les colonnes utilisées dans la jointure (`product_id`) et le calcul de `total_revenue` (`quantity * unit_price`), permettant au moteur de base de données de récupérer toutes les informations nécessaires directement depuis l'index sans accéder à la table `Order_Items`.
    *   L'index `Products(name, product_id)` aide à la jointure et au regroupement par `p.name`.
    *   L'impact est une exécution beaucoup plus rapide grâce à un filtrage temporel efficace, des jointures optimisées et un calcul d'agrégat potentiellement couvert par un index.

#### Requête 5 : Clients Ayant Passé Plus de 5 Commandes

*   **Analyse du Problème :** Cette requête joint `Customers` et `Orders`, regroupe par client, filtre les groupes ayant plus de 5 commandes et trie. Sans indexes, la jointure et le regroupement seraient lents.
*   **Proposition d'Indexes :**
    *   `CREATE INDEX idx_orders_customer_id ON Orders (customer_id);`
*   **Réécriture de la Requête :** Aucune réécriture n'est nécessaire.
*   **Justification :**
    *   L'index sur `Orders.customer_id` (clé étrangère) est fondamental. Il permet au moteur de base de données de trouver rapidement toutes les commandes pour un client donné, ce qui est essentiel pour la jointure et l'opération `GROUP BY`. La colonne `customer_id` de la table `Customers` est déjà la clé primaire et donc indexée par défaut.
    *   L'impact est une accélération significative de la jointure et de l'agrégation (`COUNT`) par client, permettant au `HAVING` et au `ORDER BY` de s'exécuter sur un ensemble de données déjà préparé efficacement.

---

## Chapitre 2 : Solution d'Optimisation - Approche 2

### Script SQL des Indexes Proposés (indexes_solution2.sql)


```sql
-- Indexes pour la Solution 2

-- Requête 1 : Clients d'une Ville Spécifique
CREATE INDEX idx_customers_city_covering ON Customers (city, first_name, last_name, email, registration_date);

-- Requête 2 : Commandes Passées par des Clients d'un Pays Donné
CREATE INDEX idx_customers_country_id ON Customers (country, customer_id); -- Composite pour WHERE et JOIN
CREATE INDEX idx_orders_customer_id_date_desc ON Orders (customer_id, order_date DESC, total_amount); -- Composite couvrant pour JOIN, ORDER BY et SELECT

-- Requête 3 : Chiffre d'Affaires par Catégorie de Produit
CREATE INDEX idx_order_items_prod_id_qty_price ON Order_Items (product_id, quantity, unit_price); -- Couvrant pour JOIN et calcul
CREATE INDEX idx_products_id_category ON Products (product_id, category);

-- Requête 4 : Les 10 Produits les Plus Vendus le Mois Dernier (en valeur)
CREATE INDEX idx_orders_date_id ON Orders (order_date, order_id); -- Composite pour WHERE et JOIN
CREATE INDEX idx_order_items_order_prod_qty_price ON Order_Items (order_id, product_id, quantity, unit_price); -- Couvrant pour JOIN et calcul
CREATE INDEX idx_products_id_name ON Products (product_id, name); -- Pour JOIN et GROUP BY

-- Requête 5 : Clients Ayant Passé Plus de 5 Commandes
CREATE INDEX idx_orders_customer_id_count ON Orders (customer_id); -- Pour GROUP BY et COUNT
-- customer_id est déjà PK dans Customers, donc indexé.
```


### Script SQL des Requêtes Optimisées (optimized_queries_solution2.sql)


```sql
-- Requête 1 : Clients d'une Ville Spécifique
SELECT customer_id, first_name, last_name, email, registration_date
FROM Customers
WHERE city = 'Paris';
-- Pas de réécriture nécessaire, l'index couvrant est la clé.

-- Requête 2 : Commandes Passées par des Clients d'un Pays Donné
SELECT o.order_id, o.order_date, c.first_name, c.last_name, o.total_amount
FROM Orders o
JOIN Customers c ON o.customer_id = c.customer_id
WHERE c.country = 'France'
ORDER BY o.order_date DESC;
-- Pas de réécriture nécessaire, les indexes sont suffisants.

-- Requête 3 : Chiffre d'Affaires par Catégorie de Produit
SELECT p.category, SUM(oi.quantity * oi.unit_price) AS total_sales
FROM Order_Items oi
JOIN Products p ON oi.product_id = p.product_id
GROUP BY p.category
ORDER BY total_sales DESC;
-- Pas de réécriture nécessaire, les indexes sont suffisants.

-- Requête 4 : Les 10 Produits les Plus Vendus le Mois Dernier (en valeur)
-- Utilisation d'une CTE pour clarifier le filtre de date, bien que l'impact sur la performance soit minime.
WITH RecentOrders AS (
    SELECT order_id, customer_id, order_date
    FROM Orders
    WHERE order_date >= DATE('now', '-1 month')
)
SELECT p.name, SUM(oi.quantity * oi.unit_price) AS total_revenue
FROM Order_Items oi
JOIN RecentOrders ro ON oi.order_id = ro.order_id
JOIN Products p ON oi.product_id = p.product_id
GROUP BY p.name
ORDER BY total_revenue DESC
LIMIT 10;

-- Requête 5 : Clients Ayant Passé Plus de 5 Commandes
SELECT c.first_name, c.last_name, COUNT(o.order_id) AS num_orders
FROM Customers c
JOIN Orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.first_name, c.last_name
HAVING COUNT(o.order_id) > 5
ORDER BY num_orders DESC;
-- Pas de réécriture nécessaire, les indexes sont suffisants.
```


### Analyse et Justification des Optimisations

#### Requête 1 : Clients d'une Ville Spécifique

*   **Analyse du Problème :** Identique à la solution 1.
*   **Proposition d'Indexes :**
    *   `CREATE INDEX idx_customers_city_covering ON Customers (city, first_name, last_name, email, registration_date);`
*   **Réécriture de la Requête :** Aucune réécriture n'est nécessaire.
*   **Justification :** Au lieu d'un simple index sur `city`, nous proposons un **index couvrant**. Cet index inclut toutes les colonnes sélectionnées (`first_name`, `last_name`, `email`, `registration_date`) en plus de la colonne de filtre (`city`). Cela signifie que le moteur de base de données peut satisfaire la requête entièrement à partir de l'index, sans avoir à accéder aux lignes de la table `Customers` elles-mêmes. L'impact est une performance maximale pour cette requête, car elle évite complètement les I/O disque sur la table principale.

#### Requête 2 : Commandes Passées par des Clients d'un Pays Donné

*   **Analyse du Problème :** Identique à la solution 1.
*   **Proposition d'Indexes :**
    *   `CREATE INDEX idx_customers_country_id ON Customers (country, customer_id);`
    *   `CREATE INDEX idx_orders_customer_id_date_desc ON Orders (customer_id, order_date DESC, total_amount);`
*   **Réécriture de la Requête :** Aucune réécriture n'est nécessaire.
*   **Justification :**
    *   L'index composite `Customers(country, customer_id)` optimise à la fois la clause `WHERE` sur `country` et la jointure sur `customer_id`.
    *   L'index composite `Orders(customer_id, order_date DESC, total_amount)` est également un index couvrant pour la table `Orders` dans le contexte de cette requête. Il inclut `customer_id` pour la jointure, `order_date DESC` pour le tri, et `total_amount` qui est une colonne sélectionnée. Cela permet de récupérer toutes les données nécessaires pour la table `Orders` directement depuis l'index, minimisant les accès à la table. L'impact est une exécution très rapide grâce à des recherches d'index efficaces et un accès minimal aux tables.

#### Requête 3 : Chiffre d'Affaires par Catégorie de Produit

*   **Analyse du Problème :** Identique à la solution 1.
*   **Proposition d'Indexes :**
    *   `CREATE INDEX idx_order_items_prod_id_qty_price ON Order_Items (product_id, quantity, unit_price);`
    *   `CREATE INDEX idx_products_id_category ON Products (product_id, category);`
*   **Réécriture de la Requête :** Aucune réécriture n'est nécessaire.
*   **Justification :**
    *   L'index `Order_Items(product_id, quantity, unit_price)` est un index couvrant pour les calculs d'agrégation. Il permet au moteur de base de données de récupérer `product_id` (pour la jointure) ainsi que `quantity` et `unit_price` (pour le calcul de `SUM(quantity * unit_price)`) directement depuis l'index, sans avoir à lire les lignes de la table `Order_Items`.
    *   L'index `Products(product_id, category)` aide à la jointure et au regroupement. L'impact est une accélération significative des opérations de jointure et d'agrégation.

#### Requête 4 : Les 10 Produits les Plus Vendus le Mois Dernier (en valeur)

*   **Analyse du Problème :** Identique à la solution 1.
*   **Proposition d'Indexes :**
    *   `CREATE INDEX idx_orders_date_id ON Orders (order_date, order_id);`
    *   `CREATE INDEX idx_order_items_order_prod_qty_price ON Order_Items (order_id, product_id, quantity, unit_price);`
    *   `CREATE INDEX idx_products_id_name ON Products (product_id, name);`
*   **Réécriture de la Requête :** Utilisation d'une Common Table Expression (CTE) pour clarifier le filtre de date.
*   **Justification :**
    *   L'index composite `Orders(order_date, order_id)` est optimisé pour le filtre `WHERE o.order_date >= DATE('now', '-1 month')` et la jointure sur `order_id`.
    *   L'index composite `Order_Items(order_id, product_id, quantity, unit_price)` est un index couvrant pour la jointure et le calcul d'agrégation, permettant de récupérer toutes les données nécessaires pour `Order_Items` directement depuis l'index.
    *   L'index `Products(product_id, name)` optimise la jointure et le regroupement.
    *   La réécriture avec une CTE (`RecentOrders`) n'apporte pas nécessairement un gain de performance majeur par rapport à la requête originale (le plan d'exécution serait probablement similaire), mais elle améliore grandement la **lisibilité et la maintenabilité** de la requête en isolant la logique de filtrage des commandes récentes. L'impact global est une exécution très rapide grâce à des indexes ciblés et une meilleure clarté du code.

#### Requête 5 : Clients Ayant Passé Plus de 5 Commandes

*   **Analyse du Problème :** Identique à la solution 1.
*   **Proposition d'Indexes :**
    *   `CREATE INDEX idx_orders_customer_id_count ON Orders (customer_id);`
*   **Réécriture de la Requête :** Aucune réécriture n'est nécessaire.
*   **Justification :**
    *   L'index sur `Orders.customer_id` est essentiel pour l'efficacité de la jointure et surtout de l'opération `GROUP BY`. Le moteur de base de données peut utiliser cet index pour regrouper rapidement les commandes par client et compter le nombre de commandes pour chaque client.
    *   L'impact est une accélération significative de l'agrégation et du filtrage `HAVING`, rendant la requête performante même avec un grand nombre de commandes.

---

### Conclusion

Ces deux solutions illustrent l'importance d'une stratégie d'indexation réfléchie et, dans certains cas, d'une réécriture de requête pour optimiser les performances SQL. L'utilisation d'indexes composites et d'indexes couvrants est particulièrement puissante pour minimiser les accès disque et accélérer les requêtes complexes. Dans un environnement réel, l'outil `EXPLAIN ANALYZE` serait indispensable pour valider ces hypothèses et affiner les choix d'optimisation.