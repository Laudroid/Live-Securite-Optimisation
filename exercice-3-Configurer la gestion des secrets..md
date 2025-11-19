Bonjour à toutes et à tous,

Ce TP est conçu pour vous faire manipuler un aspect fondamental de la sécurité et de la robustesse des applications : la gestion des secrets. Nous allons nous concentrer sur des méthodes simples et efficaces pour éviter d'exposer des informations sensibles directement dans votre code.

---

### **TP : Gestion des Secrets – Sécuriser les Configurations d'Application**

**Objectif Pédagogique :**
Mettre en œuvre des pratiques de gestion des secrets (clés API, chaînes de connexion) via des variables d'environnement ou des fichiers `.env` pour éviter leur exposition directe dans le code source.

**Contexte du Mini-Projet :**
Nous allons développer une micro-application Python qui récupère la météo actuelle pour une ville donnée. Cette application nécessitera une clé API pour interroger un service externe (par exemple, OpenWeatherMap). L'enjeu est de s'assurer que cette clé API ne soit jamais directement codée dans l'application, mais gérée de manière sécurisée.

**Prérequis :**
*   Python 3.x installé.
*   `pip` (gestionnaire de paquets Python).
*   Connaissances de base en Python (fonctions, modules, requêtes HTTP).
*   Un éditeur de code (VS Code, PyCharm, Sublime Text, etc.).
*   Un compte OpenWeatherMap (gratuit) pour obtenir une clé API. Vous pouvez en créer un rapidement et générer votre clé API ici : [https://openweathermap.org/api](https://openweathermap.org/api) (cherchez "API keys").

---

**Énoncé Détaillé du TP :**

**Étape 1 : Initialisation du Projet**

1.  Créez un nouveau dossier pour votre projet (ex: `tp_secrets_meteo`).
2.  Ouvrez un terminal dans ce dossier.
3.  Créez et activez un environnement virtuel (bonne pratique pour isoler les dépendances) :
    ```bash
    python -m venv venv
    # Sur Windows :
    .\venv\Scripts\activate
    # Sur macOS/Linux :
    source venv/bin/activate
    ```
4.  Installez les bibliothèques nécessaires :
    ```bash
    pip install requests python-dotenv
    ```
    *   `requests` : Pour faire des requêtes HTTP à l'API météo.
    *   `python-dotenv` : Pour charger les variables d'environnement depuis un fichier `.env`.
5.  Créez un fichier `app.py` dans votre dossier de projet.

**Étape 2 : Implémentation Initiale (Non Sécurisée)**

Pour comprendre le problème que nous cherchons à résoudre, nous allons d'abord implémenter la logique *sans* gestion de secrets.

1.  Ouvrez `app.py` et ajoutez le code suivant :

    ```python
    import requests

    # ATTENTION : C'est la méthode NON SÉCURISÉE !
    # Remplacez "VOTRE_CLE_API_OPENWEATHERMAP" par votre vraie clé API.
    API_KEY = "VOTRE_CLE_API_OPENWEATHERMAP"
    BASE_URL = "http://api.openweathermap.org/data/2.5/weather"

    def get_weather(city_name):
        params = {
            'q': city_name,
            'appid': API_KEY,
            'units': 'metric', # Pour obtenir la température en Celsius
            'lang': 'fr'       # Pour avoir la description en français
        }
        try:
            response = requests.get(BASE_URL, params=params)
            response.raise_for_status() # Lève une exception pour les codes d'erreur HTTP
            weather_data = response.json()

            if weather_data.get("cod") == 200:
                main_info = weather_data['main']
                weather_desc = weather_data['weather'][0]['description']
                city = weather_data['name']
                temp = main_info['temp']
                humidity = main_info['humidity']

                print(f"Météo actuelle à {city} :")
                print(f"  Température : {temp}°C")
                print(f"  Conditions : {weather_desc.capitalize()}")
                print(f"  Humidité : {humidity}%")
            else:
                print(f"Erreur lors de la récupération de la météo : {weather_data.get('message', 'Erreur inconnue')}")

        except requests.exceptions.RequestException as e:
            print(f"Erreur de connexion ou de requête : {e}")
        except Exception as e:
            print(f"Une erreur inattendue est survenue : {e}")

    if __name__ == "__main__":
        city = input("Entrez le nom d'une ville : ")
        get_weather(city)
    ```
2.  **Remplacez `"VOTRE_CLE_API_OPENWEATHERMAP"` par votre *vraie* clé API OpenWeatherMap.**
3.  Exécutez l'application : `python app.py`
4.  Entrez une ville (ex: `Paris`). Vérifiez que la météo s'affiche correctement.
5.  **Identifiez clairement pourquoi cette approche est problématique :** Imaginez que ce code soit poussé sur un dépôt Git public (GitHub, GitLab). Quels seraient les risques ?

**Étape 3 : Sécurisation avec les Variables d'Environnement / `.env`**

Nous allons maintenant corriger l'exposition de la clé API.

1.  **Créez un fichier `.env` :**
    *   Dans le même dossier que `app.py`, créez un nouveau fichier nommé `.env` (notez le point au début).
    *   Ajoutez-y votre clé API sous la forme `NOM_DE_LA_VARIABLE=valeur` :
        ```
        OPENWEATHER_API_KEY=votre_vraie_cle_api_ici
        ```
        (Remplacez `votre_vraie_cle_api_ici` par votre clé API OpenWeatherMap).

2.  **Ajoutez `.env` à votre `.gitignore` :**
    *   Créez un fichier `.gitignore` dans le dossier racine de votre projet (si vous n'en avez pas déjà un).
    *   Ajoutez la ligne suivante pour vous assurer que votre fichier `.env` ne sera jamais commité dans votre dépôt Git :
        ```
        .env
        venv/
        __pycache__/
        *.pyc
        ```
    *   *Pourquoi est-ce crucial ?* Expliquez l'importance de cette étape pour la sécurité.

3.  **Modifiez `app.py` pour utiliser `python-dotenv` :**
    *   Ouvrez `app.py`.
    *   Ajoutez les importations nécessaires au début du fichier :
        ```python
        import os
        from dotenv import load_dotenv
        ```
    *   Juste après les importations, ajoutez la ligne pour charger les variables du fichier `.env` :
        ```python
        load_dotenv() # Charge les variables d'environnement du fichier .env
        ```
    *   Modifiez la ligne où `API_KEY` est définie pour qu'elle lise la variable d'environnement :
        ```python
        # Ancien : API_KEY = "VOTRE_CLE_API_OPENWEATHERMAP"
        API_KEY = os.getenv("OPENWEATHER_API_KEY") # Lit la clé depuis l'environnement
        ```
    *   Votre `app.py` devrait maintenant ressembler à ceci (avec les modifications en gras) :

        ```python
        import requests
        **import os**
        **from dotenv import load_dotenv**

        **load_dotenv() # Charge les variables d'environnement du fichier .env**

        # La clé API est maintenant lue de manière sécurisée
        API_KEY = os.getenv("OPENWEATHER_API_KEY")
        BASE_URL = "http://api.openweathermap.org/data/2.5/weather"

        def get_weather(city_name):
            # ... (le reste de la fonction reste inchangé) ...
            params = {
                'q': city_name,
                'appid': API_KEY,
                'units': 'metric',
                'lang': 'fr'
            }
            try:
                response = requests.get(BASE_URL, params=params)
                response.raise_for_status()
                weather_data = response.json()

                if weather_data.get("cod") == 200:
                    main_info = weather_data['main']
                    weather_desc = weather_data['weather'][0]['description']
                    city = weather_data['name']
                    temp = main_info['temp']
                    humidity = main_info['humidity']

                    print(f"Météo actuelle à {city} :")
                    print(f"  Température : {temp}°C")
                    print(f"  Conditions : {weather_desc.capitalize()}")
                    print(f"  Humidité : {humidity}%")
                else:
                    print(f"Erreur lors de la récupération de la météo : {weather_data.get('message', 'Erreur inconnue')}")

            except requests.exceptions.RequestException as e:
                print(f"Erreur de connexion ou de requête : {e}")
            except Exception as e:
                print(f"Une erreur inattendue est survenue : {e}")

        if __name__ == "__main__":
            if not API_KEY:
                print("Erreur : La clé API OpenWeatherMap n'est pas configurée.")
                print("Assurez-vous d'avoir un fichier .env avec OPENWEATHER_API_KEY=votre_cle ou que la variable d'environnement est définie.")
            else:
                city = input("Entrez le nom d'une ville : ")
                get_weather(city)
        ```

**Étape 4 : Test et Validation**

1.  Exécutez à nouveau l'application : `python app.py`
2.  Entrez une ville. Vérifiez que la météo s'affiche toujours correctement.
3.  **Test de robustesse :**
    *   Supprimez temporairement la ligne `load_dotenv()` dans `app.py` (ou renommez votre fichier `.env` en `.env.bak`).
    *   Exécutez `python app.py`. Que se passe-t-il ? Pourquoi ?
    *   Remettez la ligne `load_dotenv()` ou renommez votre fichier `.env` correctement.
4.  Vérifiez que votre clé API n'apparaît nulle part en clair dans `app.py`.

**Étape 5 : Réflexion et Questions**

1.  Quels sont les risques concrets si une clé API ou une chaîne de connexion à une base de données est commise dans un dépôt Git public ?
2.  Quand privilégier l'utilisation de variables d'environnement système (définies directement dans le shell ou par l'environnement de déploiement) par rapport à un fichier `.env` ?
3.  Comment géreriez-vous plusieurs secrets pour différentes environnements (développement, staging, production) ?
4.  Quels autres types de secrets pourraient bénéficier de cette approche (au-delà des clés API et chaînes de connexion) ?
5.  Recherchez d'autres outils ou services dédiés à la gestion des secrets (ex: HashiCorp Vault, AWS Secrets Manager, Azure Key Vault). Quand seraient-ils pertinents par rapport à `.env` ?

---

**Ressources (IA & Autres) :**

N'hésitez pas à solliciter des outils d'IA (ChatGPT, Bard, Copilot, etc.) pour :
*   Générer des extraits de code Python pour l'appel API si vous avez des difficultés.
*   Comprendre la syntaxe de `python-dotenv` ou `os.getenv`.
*   Obtenir des explications sur les concepts de sécurité liés aux secrets.
*   Rechercher des bonnes pratiques de gestion des `.gitignore`.
*   Explorer les réponses aux questions de réflexion.

**Documentation utile :**
*   [Documentation officielle de `python-dotenv`](https://pypi.org/project/python-dotenv/)
*   [Documentation de l'API OpenWeatherMap](https://openweathermap.org/api)
*   [Documentation Python sur le module `os`](https://docs.python.org/3/library/os.html)

---

**Conseils du Formateur :**

L'objectif de ce TP n'est pas de recopier, mais de comprendre le *pourquoi* et le *comment* de la gestion des secrets. Expérimentez, faites des erreurs, c'est comme ça qu'on apprend le mieux. La gestion des secrets est une compétence fondamentale en sécurité logicielle et en déploiement d'applications. Prenez le temps de bien l'assimiler. Si vous bloquez, n'hésitez pas à poser des questions, même si l'IA vous a déjà donné une piste !