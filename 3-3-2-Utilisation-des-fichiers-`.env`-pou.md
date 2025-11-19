# Séance 3 – Sécurité backend (PHP, Spring Boot, Node.js)

## Partie 3 – Gestion des secrets (fichiers `.env`, Vault)

### 2. Utilisation des fichiers `.env` pour le développement local

---

### Introduction

Lors du développement local, les fichiers `.env` sont une solution simple et efficace pour gérer les configurations sensibles (clés API, identifiants de base, secrets) hors du code. Ils permettent d’isoler ces informations et d’éviter leur inclusion accidentelle dans les dépôts.  

---

### A. Qu’est-ce qu’un fichier `.env` ?  

- Texte simple contenant des variables d’environnement sous la forme `NOM=valeur`.  
- Placé à la racine du projet ou dans un dossier de configuration.  
- Chargé au démarrage de l’application pour configurer les variables d’environnement.  

**Exemple simple :**

```bash
DB_HOST=localhost
DB_USER=devuser
DB_PASS=devpass123
API_KEY=abcdef123456
```

---

### B. Avantages de `.env` pour le développement local  

| Aspect                    | Description                                   |
|---------------------------|-----------------------------------------------|
| Simplicité                | Facile à écrire, sans configuration serveur compliquée. |
| Séparation des secrets    | Détache les secrets du code source.           |
| Portabilité               | Peut être différent selon l’environnement local de chaque développeur. |
| Compatibilité             | Supporté par la majorité des frameworks et langages via librairies. |

---

### C. Intégration dans différents environnements  

#### 1. Node.js (avec `dotenv`)  

- Installation :

```bash
npm install dotenv
```

- Utilisation dans le code :

```javascript
require('dotenv').config();

console.log(process.env.DB_HOST);
```

Le fichier `.env` est lu au démarrage, et ses variables disponibles via `process.env`.

---

#### 2. PHP (avec `vlucas/phpdotenv`)  

- Installation via Composer :

```bash
composer require vlucas/phpdotenv
```

- Chargement dans le script PHP :

```php
$dotenv = Dotenv\Dotenv::createImmutable(__DIR__);
$dotenv->load();

echo $_ENV['DB_USER'];
```

---

#### 3. Spring Boot  

- Support natif des variables d’environnement dans `application.properties` ou `application.yml`.  
- Pour utiliser `.env`, il suffit d’exporter les variables avant lancement, par exemple :

```bash
export DB_USER=devuser
export DB_PASS=devpass123
./mvnw spring-boot:run
```

ou via plugins qui intègrent directement `.env`.

---

### D. Sécurité et bonnes pratiques  

- **Ne jamais versionner** : Ajouter le fichier `.env` à `.gitignore` pour ne pas l’envoyer au dépôt.  
- **Modèle public** : Versionner un fichier `.env.example` sans secrets, listant les variables attendues.  
- **Permissions** : Restreindre l’accès aux fichiers `.env` sur la machine locale pour éviter le vol accidentel.  
- **Configurer CI/CD et production** : Ne pas utiliser `.env` en production mais des systèmes de gestion de secrets (ex: Vault).  

---

### E. Exemple concret d’organisation de `.env`  

```bash
# .env.example
DB_HOST=localhost
DB_PORT=3306
DB_USER=your_user
DB_PASS=your_password
API_KEY=your_api_key
```

Les développeurs du projet copient ce fichier en `.env` et remplissent leurs propres valeurs sans compromettre la sécurité.  

---

### F. Diagramme Mermaid – Cycle d’utilisation du fichier `.env` localement

```mermaid
flowchart TD
    Dev[Développeur] -->|Crée/.modifie| EnvFile[.env local]
    EnvFile --> App[Application backend]
    App --> Config[Charge variables environnement]
    Config --> App
    Dev --> Repo[.gitignore exclut le .env]
    Repo -. Versionner .env.example .- EnvFile
```

---

### Sources  

- Dotenv (Node.js) : https://github.com/motdotla/dotenv  
- PHP dotenv : https://github.com/vlucas/phpdotenv  
- Spring Boot documentation : https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-external-config  
- OWASP Secrets Management Cheat Sheet : https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html  

---

### Résumé

Le fichier `.env` est un moyen simple et efficace pour gérer localement les secrets et configurations sensibles sans les intégrer au code source. Bien utilisé, il facilite la gestion des environnements locaux tout en préservant la sécurité. Cependant, cette approche doit rester limitée au développement et être remplacée par des systèmes spécialisés dans les environnements de production.