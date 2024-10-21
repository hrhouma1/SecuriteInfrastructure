# WinRM - Gestion des abonnements d’événements et Transfert des Journaux d'Événements


### Objectif: 

#####  Concepts essentiels de **WinRM**, de la gestion des abonnements d’événements et du **transfert des journaux** dans un environnement Windows Server.
##### Je vous présente un quiz QCM de 20 questions sur **WinRM** et les commandes associées, ainsi que sur le **transfert des journaux d'événements** dans un environnement Windows Server.



1. **Quelle commande permet d'activer WinRM sur une machine pour la gestion à distance ?**  
   a) `winrm enable`  
   b) `winrm quickconfig`  
   c) `winrm set config`  
   d) `winrm start`

2. **Pourquoi est-il important de centraliser les journaux d'événements dans un réseau avec plusieurs machines ?**  
   a) Simplifier l'accès aux fichiers partagés  
   b) Améliorer les performances du réseau  
   c) Faciliter la surveillance, le diagnostic, et la détection des incidents  
   d) Optimiser l’utilisation des ressources CPU

3. **Quel est le rôle de WinRM dans le transfert des journaux d'événements ?**  
   a) Assurer la sécurité des journaux stockés localement  
   b) Permettre la gestion locale des journaux d’événements  
   c) Faciliter l'envoi des journaux d'événements à un serveur collecteur à distance  
   d) Configurer des alertes d’événements

4. **Quel port est utilisé par défaut pour WinRM en HTTPS ?**  
   a) 80  
   b) 443  
   c) 5985  
   d) 5986

5. **Comment nomme-t-on une machine qui reçoit les journaux d'événements dans une architecture de centralisation des journaux ?**  
   a) Source d’événements  
   b) Collecteur  
   c) Serveur principal  
   d) Distributeur de logs

6. **Qu'est-ce qu'un abonnement d'événement dans le contexte de la collecte des journaux ?**  
   a) Une configuration permettant de générer des rapports périodiques des journaux  
   b) Une configuration qui spécifie quels événements doivent être collectés depuis plusieurs machines  
   c) Une stratégie de groupe pour bloquer les événements indésirables  
   d) Une méthode de stockage des journaux sur une machine locale

7. **Quel est l'intérêt de l'activation de l'audit des actions dans Windows Server ?**  
   a) Accélérer la vitesse de traitement des journaux  
   b) Suivre les actions importantes comme la création d'utilisateurs ou l'accès à des fichiers  
   c) Optimiser l'utilisation des ressources serveur  
   d) Réduire le volume des logs générés

8. **Quel est le rôle de la commande `winrm set` ?**  
   a) Afficher la configuration actuelle de WinRM  
   b) Modifier la configuration de WinRM  
   c) Arrêter le service WinRM  
   d) Lister les événements disponibles pour WinRM

9. **Quelle est l’étape critique avant de créer un abonnement d’événement ?**  
   a) Activer l'audit des journaux d’événements sur les machines sources  
   b) Vérifier la connectivité réseau  
   c) Installer des outils tiers de gestion des journaux  
   d) Redémarrer les services Windows sur les sources d'événements

10. **Quelle commande permet de lister l’état actuel des écouteurs (listeners) configurés dans WinRM ?**  
    a) `winrm quickconfig`  
    b) `winrm get listener`  
    c) `winrm enumerate`  
    d) `winrm list listeners`

11. **Qu'est-ce qu'un "listener" dans le cadre de WinRM ?**  
    a) Un processus qui analyse les journaux d'événements  
    b) Un processus qui surveille les connexions distantes sur un port spécifique  
    c) Une méthode de gestion des événements locaux  
    d) Une configuration de sécurité pour bloquer les connexions non autorisées

12. **Pour quelle raison la centralisation des journaux d’événements améliore-t-elle la sécurité ?**  
    a) Parce qu’elle réduit les événements d’erreur  
    b) Parce qu’elle accélère le réseau  
    c) Parce qu’elle permet de corréler les actions sur plusieurs machines pour identifier des anomalies  
    d) Parce qu’elle garantit la confidentialité des données

13. **Quelle commande permet de désactiver l'authentification de base dans WinRM pour renforcer la sécurité ?**  
    a) `winrm set auth.basic=false`  
    b) `winrm disable auth.basic`  
    c) `winrm remove basic-auth`  
    d) `winrm quickconfig disable basic-auth`

14. **Quel type de machine est configuré comme source d’événements dans un environnement Windows ?**  
    a) Uniquement les serveurs  
    b) Les machines qui envoient leurs journaux à un collecteur  
    c) Toutes les machines d'un réseau  
    d) Les machines virtuelles uniquement

15. **Pourquoi l'activation de WinRM via GPO est-elle recommandée dans un environnement Active Directory ?**  
    a) Pour garantir la sécurité des comptes d’utilisateurs  
    b) Pour faciliter la gestion centralisée et automatisée des machines  
    c) Pour distribuer automatiquement des applications  
    d) Pour appliquer des correctifs de sécurité sur toutes les machines

16. **Quelle étape suit immédiatement l’activation de WinRM sur les machines sources d’événements ?**  
    a) Créer un abonnement d’événements  
    b) Configurer les journaux d’événements locaux  
    c) Déployer une GPO pour autoriser la collecte des journaux  
    d) Installer les mises à jour du service WinRM

17. **Lors de la création d’un abonnement d’événements, quel journal est généralement utilisé pour stocker les événements transférés ?**  
    a) System Logs  
    b) Application Logs  
    c) Forwarded Events  
    d) Security Logs

18. **Pour capturer l'événement de création d'un utilisateur sur DC, quelle fonctionnalité de sécurité devez-vous activer ?**  
    a) L'audit de création d'utilisateur  
    b) Le firewall distant  
    c) La gestion des comptes locaux  
    d) La configuration des ports

19. **Que signifie l’option "Success" dans la configuration de l’audit des fichiers et répertoires ?**  
    a) Suivre les événements d'échec d'accès aux fichiers  
    b) Capturer les événements réussis d'accès à un répertoire ou à un fichier  
    c) Surveiller la suppression de fichiers  
    d) Activer le transfert de fichiers

20. **Quel journal devriez-vous vérifier sur SRV pour voir si les événements de création de l'utilisateur "Bob" sur DC ont bien été collectés ?**  
    a) Application  
    b) System  
    c) Forwarded Events  
    d) Security Logs
