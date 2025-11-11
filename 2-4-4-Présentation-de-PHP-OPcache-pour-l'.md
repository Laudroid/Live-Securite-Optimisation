# Séance 2 – Optimisation des performances côté backend  

## Partie 4 – Caching (Spring Boot Cache, Node.js Redis, PHP OPcache)  

### 4. Présentation de PHP OPcache pour l'optimisation du bytecode  

---

### Introduction  

PHP est un langage interprété, ce qui signifie que chaque script PHP doit passer par un processus de compilation en bytecode à chaque requête, ralentissant ainsi l’exécution. **OPcache** est une extension native de PHP qui optimise cette étape en mettant en cache le bytecode compilé et en évitant ainsi les recompilations inutiles.  

---

### A. Comment fonctionne OPcache ?  

Lorsqu’un script PHP est exécuté :  

1. Le moteur PHP lit le script source.  
2. Il compile ce script en bytecode (opcodes), une représentation intermédiaire optimisée pour la machine virtuelle PHP.  
3. OPcache stocke ce bytecode en mémoire partagé.  
4. Lors des requêtes suivantes, PHP charge directement le bytecode depuis OPcache sans recompilation.  

Ce fonctionnement réduit considérablement la charge CPU liée à la compilation et améliore la rapidité d'exécution.  

---

### B. Activation et configuration d’OPcache  

OPcache est inclus nativement dans PHP depuis la version 5.5.  

1. Vérifier que OPcache est activé :  

```bash
php -i | grep opcache.enable
```

2. Configuration courante dans le fichier `php.ini` :  

```ini
opcache.enable=1
opcache.memory_consumption=128        ; Mémoire allouée en Mo
opcache.interned_strings_buffer=8    ; Buffer pour les chaînes internes
opcache.max_accelerated_files=10000  ; Nombre max de fichiers en cache
opcache.revalidate_freq=2             ; Fréquence (en secondes) pour vérifier les fichiers modifiés
opcache.validate_timestamps=1        ; Activation de la validation automatique des fichiers modifiés
```

- `memory_consumption` limite la mémoire dédiée au cache.  
- `validate_timestamps` permet de recompiler les scripts modifiés automatiquement.  

---

### C. Impact sur les performances  

Selon des benchmarks, OPcache peut réduire le temps d’exécution d’un script PHP de **jusqu’à 80%** sur des scénarios typiques, notamment dans des environnements à trafic important.  

---

### D. Exemple illustratif (sans OPcache vs avec OPcache)  

| Situation                          | Temps d’exécution (ms) |
|----------------------------------|-----------------------|
| Script PHP sans OPcache           | 120                   |
| Script PHP avec OPcache activé    | 25                    |

*Source*: Benchmarks multiples collectés sur PHP official docs et site phoronix.com.

---

### E. Visualisation du cache OPcache  

L’extension PHP **OPcache GUI** (ex: opcache-gui, opcache-status) permet d’observer en temps réel :  

- Le nombre de scripts compilés.  
- La mémoire utilisée.  
- Les hits et misses du cache.  

---

### F. Diagramme Mermaid – Fonctionnement d’OPcache  

```mermaid
flowchart LR
    ScriptPHP[Script PHP]
    PHPParser[PHP compile en bytecode]
    OPcache[Cache OPcache]
    Execution[Moteur PHP exécute bytecode]

    ScriptPHP --> PHPParser --> OPcache
    OPcache -- Contient bytecode? -->|Oui| Execution
    OPcache -- Contient bytecode? -->|Non| PHPParser
    Execution --> Résultat[Sortie appli]
```

---

### G. Bonnes pratiques  

- Ajuster la taille mémoire selon la taille de votre application et le nombre de fichiers PHP.  
- Gérer le paramètre `opcache.validate_timestamps` en fonction de la fréquence des déploiements (mettre à 0 en production avec redéploiement manuel du cache).  
- Surveiller l’utilisation via des outils comme **opcache-status** pour éviter une saturation du cache.  
- En cas de debug, désactiver temporairement OPcache ou forcer la recompilation.  

---

### Sources  

- PHP Official Documentation – OPcache, https://www.php.net/manual/en/book.opcache.php  
- PHP OPcache Configuration, https://www.php.net/manual/en/opcache.configuration.php  
- Sitepoint – Guide OPcache, https://www.sitepoint.com/introduction-to-php-opcache/  
- Benchmarks Phoronix – PHP OPcache performance, https://www.phoronix.com/scan.php?page=news_item&px=PHP-OPcache-Benchmarks  

---

### Conclusion  

OPcache améliore la rapidité d’exécution des applications PHP en évitant la recompilation répétée du code source et en stockant le bytecode en mémoire. Sa configuration adéquate maximise les gains de performance, en particulier sur des applications web à fort trafic.