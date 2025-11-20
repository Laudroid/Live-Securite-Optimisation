
# **Examen – Optimisation & Sécurisation du Code**

**Durée : 2 heures**
**Fiches de cours : autorisé**
**IA : non autorisée**
**Les temps entre paranthèses sont purement indicatifs**

Votre Nom et prénom : 

---

# **PARTIE 1 – QCM / Questions rapides (30 min)**

Cochez la bonne réponse.

1. Qu'est-ce qu'un code optimisé ?

A. Le code le plus court possible
B. Un code lisible, performant, maintenable
C. Un code qui n’utilise pas de commentaires
D. Un code sans variables locales

2. Quelle notion décrit la dette technique ?

A. Du code inutilisé
B. De la documentation obsolète
C. Une faille de sécurité
D. Un ensemble de compromis qui réduisent la qualité du code

3. Quel principe fait partie du modèle CIA ?

A. Accessibilité
B. Confidentialité
C. Auditabilité
D. Adaptabilité

4. La complexité de O(n²) signifie :

A. Temps constant
B. Temps proportionnel à n
C. Temps proportionnel à n × n
D. Temps proportionnel à log n

5. Quel élément permet d'améliorer la lisibilité ?

A. Variables “a”, “b”, “c”
B. Ajout de commentaires
C. Nommage explicite 
D. Minimiser les fonctions

6. Qu’est-ce qu’un index SQL ?

A. Un type de table
B. Une structure accélérant la recherche
C. Un backup
D. Un trigger

7. Une requête préparée sert à éviter :

A. Les injections SQL  
B. Les ralentissements d'exécution
C. Les attaques DDOS
D. Les erreurs de syntaxe SQL

8. Que signifie XSS ?

A. Cross Server Spoof
B. Cross Site Scripting
C. XML Site Script
D. Xtreme Server Security

9. Le HTTPS garantit :

A. Un trafic chiffré 
B. Un trafic plus rapide
C. Une application sans bug
D. L’absence de logs

10. Quel risque pose eval() ?

A. Aucun
B. XSS
C. RCE
D. DOS

11. L'objectif d'un cache est :

A. De supprimer les accès BDD
B. De crypter les données
C. De sauvegarder les données
D. D'accélérer les lectures fréquentes 

12. Qu’est-ce que la CSRF ?

A. Cross-Server File Reading
B. Cross-Site Request Forgery
C. Client-Side Remote Failure
D. Crypted Server Request Flow

13. Une API non protégée avec CORS permet :

A. D’accélérer les requêtes
B. D’empêcher les XSS
C. D’être appelée par n’importe quel domaine 
D. D’éviter les injections

14. Quel mécanisme protège les endpoints sensibles ?

A. Argon2
B. POST
C. HTTPS
D. Authentification JWT

15. Node.js utilise un modèle :

A. Multi-thread synchrone
B. Mono-thread asynchrone
C. Multi-process synchrone
D. Multi-thread bloquant

16. Pourquoi éviter les requêtes SELECT * ?

A. Elles provoquent des bugs
B. Elles sont bloquées par MySQL
C. Elles chargent plus de données que nécessaire
D. Elles suppriment les index

17. Un pool de connexions permet :

A. De réutiliser des connexions déjà ouvertes 
B. D’ouvrir une connexion à chaque requête
C. De fermer toutes les connexions
D. D’éviter l’authentification

18. Quel est le problème ici ?

echo $_GET["user"];

A. RCE
B. XSS
C. CSRF
D. SQL Injection

19. Le cache Redis est :

A. Basé sur la mémoire 
B. Persistant par défaut
C. Un système de fichiers compressé
D. Un CDN

20. Le code suivant souffre de quoi ?

res.send("<p>" + req.query.msg + "</p>");

A. XSS
B. CSRF
C. SQL Injection
D. Overflow

21. Quel est l’effet d’un index sur une colonne très modifiée ?

A. Aucun
B. Supprime les doublons
C. Rend les SELECT plus lents
D. Rend les insertions plus lentes 

22. CORS permet de :

A. Gérer l’accès cross-domain
B. Crypter les données
C. Optimiser les performances
D. Minifier le JavaScript

23. Une attaque XSS vole souvent :

A. Le mot de passe root
B. L’architecture du projet
C. Les fichiers du serveur
D. Les cookies 

24. Une simulation d’attaque automatisée s’appelle :

A. Audit
B. Profilage
C. Test unitaire
D. Pentest 

25. Quel problème dans le code ?

JSON.parse(userInput);

A. Aucun
B. RCE
C. Peut provoquer un crash (JSON invalide)
D. XSS

26. Quel est l’avantage du minifying ?

A. Améliore la sécurité
B. Rend le code plus lisible
C. Optimise la mémoire serveur
D. Réduit le poids des fichiers 

27. Un JWT stocké dans localStorage :

A. Est chiffré
B. Est vulnérable à XSS
C. Est protégé contre toutes attaques
D. Expire immédiatement

28. Une route non authentifiée :

A. Est sans risque
B. Favorise XSS
C. Est plus rapide
D. Peut exposer des données sensibles

29. Les ORM permettent :

A. De supprimer le SQL
B. De supprimer la DB
C. De limiter les injections 
D. De chiffrer les données

30. Le SHA256 :

A. Est réversible
B. Est un hash
C. Est un chiffrement
D. Stocke les mots de passe

31. Quel est le problème avec forEach + opérations async ?

A. Ne fonctionne jamais
B. Les erreurs ne sont pas catchées
C. La boucle s’arrête
D. Le résultat est trié automatiquement

32. Un CDN optimise :

A. La localisation du contenu 
B. Les scripts internes
C. Le SQL
D. Le CSS interne

33. Le code suivant génère quoi ?

console.log("A");
Promise.resolve().then(() => console.log("B"));
console.log("C");

A. ABC
B. ACB
C. BAC
D. CAB

34. Un cache distribué permet :

A. De partager des données entre plusieurs serveurs
B. De remplacer la base SQL
C. D’exécuter des scripts PHP
D. D’accélérer les uploads

35. Une attaque CSRF vise :

A. Le serveur d’authentification
B. Le certificat TLS/SSL
C. L’utilisateur authentifié 
D. Le réseau

36. Le lazy loading des images améliore :

A. La vitesse perçue 
B. Le SEO uniquement
C. Le rendu CSS
D. L'intégrité des données

37. Un bon mot de passe doit être :

A. Long et aléatoire
B. Court et mémorisable
C. Basé sur le prénom
D. Identique partout

38. Une fuite mémoire front-end peut venir de :

A. Un composant non démonté
B. Une mauvaise indentation
C. Un script minifié
D. Un fichier CSS trop lourd

39. Un mot de passe hashé avec sel :

A. Est impossible à brute-forcer
B. Est plus sécurisé qu'un hash simple
C. Remplace le chiffrement
D. Est réversible

40. Le CORS Access-Control-Allow-Origin: * 

A. Est sécurisé
B. Bloque les requêtes
C. Permet toutes les origines 
D. Chiffre les données

41. Le Node cluster permet :

A. Multi-thread
B. Multi-process
C. Compilation
D. Chiffrement

42. Une protection brute-force efficace :

A. Limite de tentatives
B. Stocke en clair
C. Enlève les logs
D. Désactive HTTPS

43. Une API mal paginée peut :

A. Accélérer le backend
B. Favoriser les SQL Injection
C. Favoriser les XSS
D. Saturer la mémoire 

44. Un secret dans Git est dangereux car :

A. Git les supprime
B. Ils ne sont pas encryptés
C. Ils sont publics pour toujours dans l’historique 
D. Ils n'expirent pas automatiquement

45. Le code suivant effectue :

setInterval(() => {}, 0);

A. Rien
B. Une promesse
C. Un arrêt du serveur
D. Une boucle infinie 

---

# **PARTIE 2 – Analyse de code (25 min)**

Choisissez un exercice à votre convenance entre : exerxice 1 OU exercice 2

## **Exercice 1 – Sécurité (Node.js)**

Voici un extrait de code en Node.js :

```js
app.get("/user", async (req, res) => {
    const id = req.query.id;
    const result = await db.query(`SELECT * FROM users WHERE id = ${id}`);
    res.send(result);
});
```

1. **Identifiez la ou les failles de sécurité.**
2. **Expliquez comment les exploiter.**
3. **Proposez une version sécurisée du code.**

---

## **Exercice 2 – Performance SQL (Java/Spring Boot)**

On vous donne le code suivant :

```java
@Query("SELECT p FROM Product p")
List<Product> getAll();
```

La table `product` contient 500 000 lignes.

1. Expliquez pourquoi ce code pose un problème de performance.
2. Proposez une version optimisée incluant :

   * pagination
   * select ciblé (ne renvoyant que `id` et `name`)
3. Donne un exemple de création d'index pertinent pour accélérer l’accès aux produits par leur nom.

---

# **PARTIE 3 – Sécurité Backend & API (25 min)**

Choisissez un exercice à votre convenance entre : exerxice 3 OU exercice 4

## **Exercice 3 – JWT & CORS **

Expliquez :

1. Le fonctionnement d’un JWT en 4 étapes.
2. Ce que signifie une mauvaise configuration CORS.
3. Donnez un exemple concret d’en-têtes CORS corrects pour autoriser seulement :

   * le domaine `https://app.example.com`
   * les méthodes GET et POST
   * les en-têtes `Authorization` et `Content-Type`

---

## **Exercice 4 – Validation des entrées**

On veut sécuriser un formulaire de création d’utilisateur :

* `email`
* `password`
* `role`

1. Donnez **4 validations backend essentielles** pour éviter XSS et SQL Injection.
2. Proposez un exemple de validation en Java/Spring ou Node.js.
3. Expliquez pourquoi valider seulement côté frontend n’est pas suffisant.

---

# **PARTIE 4 – Mini-projet (40 min)**

**Sujet :**
On souhaite sécuriser et optimiser un endpoint d’API `/login` utilisé par une application mobile.

L'endpoint actuel :

```js
app.post("/login", async (req, res) => {
  const { email, password } = req.body;
  const user = await db.query(`SELECT * FROM users WHERE email = '${email}' AND password = '${password}'`);
  res.send(user);
});
```

### **Travail attendu :**

1. **Lister les problèmes** (au moins 5 : sécurité + performance).

2. **Proposer une version sécurisée incluant :**

   * requêtes paramétrées
   * vérification par hash du mot de passe
   * génération d’un JWT
   * variables d’environnement pour les secrets

3. **Proposer une (ou +) amélioration de performance utile pour cet endpoint.**

