# Séance 4 – Sécurité frontend et APIs  
## Partie 2 – Communication sécurisée avec API : HTTPS, JWT, CORS  
### 1. Importance de HTTPS pour le chiffrement des communications  

---

### A. Qu’est-ce que HTTPS ?  

HTTPS (HyperText Transfer Protocol Secure) est la version sécurisée du protocole HTTP. Il utilise le protocole TLS (Transport Layer Security) pour chiffrer les échanges entre un client (navigateur, application mobile, frontend) et un serveur (API backend).  

**Objectifs principaux :**  
- **Confidentialité :** protéger les données échangées contre toute écoute passive (interception par un tiers).  
- **Authentification :** garantir que le client communique avec le bon serveur via les certificats SSL/TLS.  
- **Intégrité :** assurer que les données ne sont pas modifiées ou altérées inopinément durant le transit.  

---

### B. Fonctionnement général de HTTPS

1. **Négociation TLS (handshake)** : Établissement d’une connexion sécurisée via échange de certificats X.509 et négociation de clés de chiffrement symétriques.  
2. **Chiffrement symétrique** : Toutes les données sont chiffrées avec les clés négociées, rapides et sécurisées.  
3. **Vérification du certificat** : Le client vérifie que le certificat présenté par le serveur est valide, signé par une autorité reconnue et non expiré.  

---

### C. Pourquoi HTTPS est-il indispensable pour les APIs ?

| Risques sans HTTPS                        | Impacts                                            |
|-----------------------------------------|---------------------------------------------------|
| **Interception (attaque man-in-the-middle - MITM)** | Vol ou modification des données sensibles (tokens, mots de passe) en transit |
| **Usurpation d’identité**                | Attaquant peut se faire passer pour le serveur ou le client |
| **Injection de contenu**                 | Scripts malveillants injectés dans les réponses |
| **Non conformité réglementaire**         | Exigences RGPD, PCI-DSS, HIPAA, etc., imposent l’usage de TLS |

---

### D. Exemple d’appel API via HTTPS en JavaScript (Fetch API)

```javascript
fetch('https://api.example.com/data', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...'
  }
})
.then(response => response.json())
.then(data => {
  console.log('Données sécurisées reçues:', data);
})
.catch(error => console.error('Erreur:', error));
```

Le prefixe `https://` garantit que la communication est chiffrée et sécurisée.

---

### E. Diagramme Mermaid – Échange sécurisé via HTTPS

```mermaid
sequenceDiagram
    participant Client
    participant Serveur

    Client->>Serveur: ClientHello (début TLS handshake)
    Serveur->>Client: ServerHello + Certificat
    Client->>Serveur: Vérifie certificat, génère clé session
    Serveur->>Client: Confirm clé session
    Client->>Serveur: Données chiffrées (requête API)
    Serveur->>Client: Réponse chiffrée
```

---

### F. Mise en place technique

- Obtention d’un certificat SSL/TLS via une autorité de certification (AC) reconnue (ex: Let’s Encrypt, Digicert).  
- Configuration du serveur web (Apache, Nginx) ou API Gateway pour activer HTTPS.  
- Redirection automatique des requêtes HTTP vers HTTPS pour éviter les accès non sécurisés.  

---

### G. Points supplémentaires

- **HSTS (HTTP Strict Transport Security)** : En-tête HTTP qui force le client à n’utiliser que HTTPS pour ce domaine.  
- **TLS 1.2 minimum** recommandé, TLS 1.3 étant la version la plus récente et performante.  
- **Renouvellement et gestion rigoureuse des certificats** nécessaires pour maintenir la confiance.  

---

### Sources  

- IETF RFC 8446 – TLS 1.3 : https://tools.ietf.org/html/rfc8446  
- Mozilla Developer Network (MDN) – HTTPS : https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview#security  
- OWASP Transport Layer Protection Cheat Sheet : https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html  
- Let’s Encrypt : https://letsencrypt.org  

---

### Synthèse  

HTTPS est la base de la sécurité des communications entre frontend et APIs. Il fournit un chiffrement robuste qui protège les données sensibles en transit, garantit l’authenticité des serveurs, et assure l’intégrité des échanges. Son adoption est indispensable pour toute application ayant une partie frontend communiquant avec des APIs backend, répondant à la fois aux menaces techniques et aux exigences réglementaires.