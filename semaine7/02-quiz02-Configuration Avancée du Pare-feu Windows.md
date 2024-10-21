# Configuration Avancée du Pare-feu Windows

1. **Quelle fonctionnalité est spécifique au Pare-feu Windows avec fonctions avancées de sécurité ?**
   - A. Gestion des périphériques connectés
   - B. Règles de filtrage basées sur des ports et protocoles
   - C. Antivirus intégré
   - D. Analyse des fichiers systèmes

2. **Quelle est la différence entre une règle entrante et une règle sortante dans le Pare-feu Windows ?**
   - A. La règle entrante bloque tout le trafic réseau
   - B. La règle sortante contrôle le trafic qui quitte l'ordinateur
   - C. La règle entrante ne peut pas être personnalisée
   - D. La règle sortante ne s'applique qu'aux réseaux privés

3. **Quel est l'avantage principal de la journalisation des événements dans le Pare-feu Windows ?**
   - A. Améliorer la vitesse du réseau
   - B. Analyser les tentatives d'accès bloquées
   - C. Détecter les virus présents sur le réseau
   - D. Bloquer automatiquement les attaques réseau

4. **Quel type de profil réseau est utilisé pour un ordinateur connecté à un réseau d'entreprise contrôlé par Active Directory ?**
   - A. Profil privé
   - B. Profil public
   - C. Profil d'invité
   - D. Profil de domaine

5. **Quelle règle permet de bloquer tout trafic sortant pour une application spécifique ?**
   - A. Règle de profil public
   - B. Règle entrante pour l'application
   - C. Règle sortante basée sur l'exécutable
   - D. Règle de filtrage d'adresses IP

6. **Quelles informations peuvent être utilisées pour créer une règle personnalisée dans le Pare-feu Windows ?**
   - A. Le modèle de l'ordinateur
   - B. L'adresse IP, le protocole et le port
   - C. La marque de l'ordinateur
   - D. La vitesse de la connexion Internet

7. **Quel est l'objectif principal d'une règle de filtrage par port dans un pare-feu ?**
   - A. Filtrer les paquets en fonction de leur taille
   - B. Contrôler le type de données qui passent par le port
   - C. Autoriser ou bloquer le trafic sur un port spécifique
   - D. Modifier les paramètres de sécurité de l'ordinateur

8. **Que fait une règle de pare-feu configurée pour bloquer tout le trafic sauf HTTP et HTTPS ?**
   - A. Elle bloque toutes les connexions à Internet
   - B. Elle autorise uniquement les connexions sur les ports 80 et 443
   - C. Elle bloque toutes les connexions sortantes
   - D. Elle désactive les mises à jour automatiques du système

9. **Quel est l'intérêt de configurer des exceptions dans le pare-feu ?**
   - A. Permettre à toutes les applications de passer à travers le pare-feu
   - B. Autoriser certaines applications ou services à ignorer les règles de filtrage
   - C. Désactiver le pare-feu temporairement
   - D. Chiffrer toutes les connexions réseau

10. **Quelle commande PowerShell permet de créer une règle de pare-feu pour bloquer une application spécifique ?**
    - A. `New-NetFirewallRule`
    - B. `Start-NetFirewallRule`
    - C. `Block-NetApp`
    - D. `Disable-ApplicationRule`

11. **Dans quel cas utiliseriez-vous une règle de filtrage basée sur l’adresse IP source ?**
    - A. Pour restreindre l'accès à un réseau interne uniquement aux adresses IP autorisées
    - B. Pour accélérer la connexion Internet
    - C. Pour empêcher l'accès au réseau depuis des ordinateurs portables
    - D. Pour réduire la consommation de bande passante

12. **Quel est le principal objectif d'une inspection de paquets par le pare-feu ?**
    - A. Vérifier la compatibilité des paquets avec les applications installées
    - B. Analyser si les paquets respectent les règles de sécurité configurées
    - C. Bloquer tout le trafic sortant
    - D. Filtrer les paquets en fonction de leur taille

13. **Quelles sont les principales fonctionnalités offertes par le Pare-feu Windows avec sécurité avancée ?**
    - A. Gestion des imprimantes réseau
    - B. Filtrage de paquets, inspection, et journalisation des événements
    - C. Surveillance des utilisateurs connectés au réseau
    - D. Protection contre les virus

14. **Qu'est-ce qu'une règle de filtrage par application ?**
    - A. Une règle qui surveille le temps d'exécution des programmes
    - B. Une règle qui autorise ou bloque les connexions réseau d'une application spécifique
    - C. Une règle qui désactive l'application automatiquement après une période d'inactivité
    - D. Une règle qui contrôle les ressources système utilisées par l'application

15. **Comment configurez-vous une règle pour bloquer un fichier `.bat` dans le Pare-feu Windows ?**
    - A. En bloquant le service réseau associé
    - B. En bloquant `cmd.exe`, qui exécute les fichiers `.bat`
    - C. En désactivant les scripts sur l'ordinateur
    - D. En activant le mode hors-ligne pour l'ordinateur

16. **Que permet de faire une règle sortante sur le Pare-feu Windows ?**
    - A. Empêcher une application d'envoyer des données à l'extérieur du réseau
    - B. Empêcher une application de recevoir des données
    - C. Bloquer tout le trafic entrant
    - D. Désactiver les notifications du pare-feu

17. **Pourquoi créer des règles basées sur les utilisateurs ou les groupes ?**
    - A. Pour simplifier la configuration réseau
    - B. Pour appliquer des règles spécifiques selon le niveau d'accès des utilisateurs
    - C. Pour désactiver temporairement le pare-feu
    - D. Pour bloquer tous les utilisateurs du réseau

18. **Quelle est l'utilité principale des journaux du Pare-feu Windows ?**
    - A. Diagnostiquer des problèmes de connexion réseau
    - B. Créer des profils utilisateur personnalisés
    - C. Bloquer automatiquement les tentatives d'intrusion
    - D. Améliorer la vitesse de traitement des paquets

19. **Pourquoi définir des règles de sécurité spécifiques pour les profils réseau (Domaine, Privé, Public) ?**
    - A. Parce que chaque profil a des exigences de sécurité différentes
    - B. Pour désactiver le pare-feu dans certains réseaux
    - C. Pour augmenter la vitesse de connexion dans les réseaux publics
    - D. Pour autoriser toutes les connexions dans les réseaux privés

20. **Quel est l'objectif d'une règle de blocage basée sur le protocole et le port dans le Pare-feu Windows ?**
    - A. Filtrer les connexions en fonction du type de protocole (TCP, UDP) et du port
    - B. Activer toutes les connexions réseau
    - C. Empêcher l’accès à certains fichiers
    - D. Désactiver la sécurité du réseau

