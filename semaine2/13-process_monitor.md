
## 2.2 Process Monitor

### Description :
Process Monitor est un outil avancé de la suite Sysinternals qui permet de surveiller en temps réel les activités du système, notamment les accès au registre, aux fichiers, et les processus. Il est particulièrement utile pour diagnostiquer les problèmes système, analyser les comportements suspects, et identifier les activités malveillantes.

### Fonctionnalités Principales :
1. **Surveillance en Temps Réel :** Process Monitor capture et affiche les événements en temps réel, offrant une vue détaillée des opérations effectuées par chaque processus.
2. **Filtrage Avancé :** Il permet de filtrer les événements en fonction de divers critères, tels que le nom du processus, le type d'opération, ou le chemin d'accès aux fichiers, pour se concentrer sur les informations pertinentes.
3. **Enregistrement des Journaux :** Les événements capturés peuvent être enregistrés dans des fichiers de log pour une analyse ultérieure ou pour être partagés avec d'autres membres de l'équipe.

### Avantages de Process Monitor :
- **Diagnostic Précis :** En capturant chaque détail des opérations système, Process Monitor aide à diagnostiquer les problèmes complexes, comme les erreurs d'accès aux fichiers ou les conflits de registre.
- **Détection des Logiciels Malveillants :** Il permet de détecter les comportements anormaux des processus, comme l'accès non autorisé à des fichiers système critiques ou la modification des clés de registre.
- **Facilité d'Utilisation :** Bien qu'il soit très puissant, Process Monitor est conçu pour être accessible, avec une interface intuitive qui permet de rapidement configurer et surveiller les événements.

### Limites :
- **Volume de Données :** Comme Sysmon, Process Monitor génère une grande quantité de données, ce qui peut être difficile à gérer sans un filtrage adéquat.
- **Performance :** L'exécution continue de Process Monitor peut avoir un impact sur les performances du système, surtout lors de la capture d'un grand nombre d'événements.

### Utilisations Typiques :
- **Analyse de Processus :** Suivi de l'activité des processus suspects pour identifier d'éventuels comportements malveillants.
- **Résolution de Problèmes :** Identification des causes d'erreurs système, comme les fichiers manquants ou les permissions incorrectes.
- **Audit de Sécurité :** Surveillance des accès aux fichiers et au registre pour détecter des actions non autorisées.

Process Monitor est un outil indispensable pour toute analyse approfondie du système, que ce soit pour la détection des intrusions, la résolution des problèmes ou l'audit de sécurité.
