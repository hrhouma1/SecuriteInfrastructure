# Analyse des journaux du Pare-feu Window

1. **Quelle commande permet d'accéder rapidement à la Console de gestion du Pare-feu Windows avec fonctions avancées de sécurité ?**
   - a) cmd.msc
   - b) firewall.msc
   - c) wf.msc
   - d) firewallcmd

2. **Dans quelle section de la Console de gestion du Pare-feu Windows peut-on configurer la journalisation ?**
   - a) Paramètres avancés
   - b) Propriétés du Pare-feu
   - c) Journal des événements
   - d) Sécurité avancée

3. **Quel type de profil réseau correspond à un réseau public ?**
   - a) Profil Domaine
   - b) Profil Privé
   - c) Profil Public
   - d) Profil Professionnel

4. **Que permet de journaliser l'option "Connexion réussie" ?**
   - a) Connexions bloquées
   - b) Connexions autorisées
   - c) Connexions en attente
   - d) Connexions réinitialisées

5. **Où est enregistré le fichier journal par défaut ?**
   - a) C:\Windows\System32\Firewall\log.txt
   - b) C:\Logs\Firewall\firewall.log
   - c) C:\Program Files\Firewall\logfile.txt
   - d) C:\Windows\System32\LogFiles\Firewall\pfirewall.log

6. **Quelle taille de fichier journal est définie par défaut ?**
   - a) 1 Mo
   - b) 4 Mo
   - c) 10 Mo
   - d) 20 Mo

7. **Quel est l'outil de recherche utilisé pour filtrer les connexions bloquées dans un fichier journal ?**
   - a) grep
   - b) findstr
   - c) netstat
   - d) searchstr

8. **Quelle commande permet de rechercher toutes les connexions bloquées dans le fichier journal ?**
   - a) findstr /i "autorisé" C:\Windows\System32\LogFiles\Firewall\pfirewall.log
   - b) findstr /i "bloqué" C:\Windows\System32\LogFiles\Firewall\pfirewall.log
   - c) findstr /a "bloqué" C:\Windows\System32\LogFiles\Firewall\pfirewall.log
   - d) find /i "bloqué" C:\Windows\System32\LogFiles\Firewall\pfirewall.log

9. **Dans l'Observateur d'événements, où peut-on consulter les journaux du Pare-feu Windows ?**
   - a) Journaux des applications et services > Microsoft > Windows > Firewall with Advanced Security
   - b) Journal des événements système > Microsoft > Pare-feu
   - c) Journal de sécurité > Firewall > Connexions
   - d) Observateur réseau > Pare-feu Windows > Sécurité

10. **Quel ID d'événement est généralement associé à une connexion bloquée dans l'Observateur d'événements ?**
    - a) 1000
    - b) 5157
    - c) 4001
    - d) 3010

11. **Quel est le rôle de l'outil Wireshark dans l'analyse des journaux du Pare-feu ?**
    - a) Analyser les connexions bloquées en temps réel
    - b) Détecter les failles dans les règles du pare-feu
    - c) Filtrer les entrées du journal en fonction des ports et des protocoles
    - d) Bloquer automatiquement les adresses IP suspectes

12. **Quel filtre utiliser dans Wireshark pour capturer le trafic vers le port 443 (HTTPS) ?**
    - a) ip.addr == 443
    - b) tcp.port == 443
    - c) port.src == 443
    - d) ip.src == 443

13. **Quel outil peut indexer et analyser les journaux du Pare-feu pour générer des alertes automatiques ?**
    - a) Wireshark
    - b) Splunk
    - c) Firewall Analyzer
    - d) Netcat

14. **Comment ajouter une nouvelle source de données dans Splunk pour analyser les journaux du pare-feu ?**
    - a) Fichiers et répertoires
    - b) Ajouter un journal
    - c) Monitorer le pare-feu
    - d) Importer fichier log

15. **Quelle commande permet de bloquer une adresse IP suspecte dans l'invite de commande en mode administrateur ?**
    - a) New-NetFirewallRule -DisplayName "Blocage IP" -Action Deny
    - b) Add-FirewallRule -RemoteAddress [IP]
    - c) New-NetFirewallRule -DisplayName "Blocage IP suspecte" -Direction Inbound -RemoteAddress [IP] -Action Block
    - d) Block-IPAddress [IP]

16. **Quel outil peut être utilisé pour des réseaux complexes nécessitant une analyse approfondie des journaux du Pare-feu ?**
    - a) Wireshark
    - b) Splunk
    - c) Event Viewer
    - d) Firewall Rules Manager

17. **Comment ajuster une règle de pare-feu après avoir identifié une connexion suspecte ?**
    - a) Ajouter une exception dans les paramètres avancés
    - b) Bloquer l’adresse IP dans l’invite de commandes
    - c) Modifier la configuration de la journalisation
    - d) Réinitialiser le fichier de journal

18. **Quel est le format de fichier par défaut du journal du pare-feu Windows ?**
    - a) .txt
    - b) .log
    - c) .xml
    - d) .csv

19. **Pourquoi est-il important d'augmenter la taille du fichier journal si l'on gère un réseau complexe ?**
    - a) Pour améliorer les performances du pare-feu
    - b) Pour éviter la perte de données lors de la journalisation
    - c) Pour accélérer l'analyse des journaux
    - d) Pour compresser les journaux de manière efficace

20. **Quel est l'objectif principal de l'analyse des journaux du Pare-feu Windows ?**
    - a) Optimiser la vitesse du réseau
    - b) Capturer les événements réseau pour identifier des menaces potentielles
    - c) Améliorer la configuration du pare-feu
    - d) Obtenir des statistiques réseau

