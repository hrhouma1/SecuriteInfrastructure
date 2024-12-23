# Quiz sur VIP, VGW, Keepalived et le protocole VRRP (suite de la partie 02)

---

![image](https://github.com/user-attachments/assets/14b14be9-42a1-4e64-8366-5a003e854a5d)

```
                          +----------------+
                          |    Internet    |
                          +----------------+
                                 |
                                 |
                           +-------------+
                           |   Machine 1 |
                           |   Routeur   |
                           |-------------|
                           | DNS, NTP    |
                           | Routage     |
                           +-------------+
                           | NIC1 (NAT)  |
                           | NIC2 (Interne 20.21.22.254/24)  |
                           | NIC3 (DMZ 10.11.12.254/24)      |
                                 |
               ------------------+--------------------
               |                                       |
       +----------------+                     +------------------+
       |   Machine 2    |                     |   Switch (DMZ)  |
       | Mail Server    |                     +------------------+
       |----------------|                           |
       | 20.21.22.10/24 |              --------------------------
       +----------------+              |                    |
                                  +----------------+    +----------------+
                                  |   Machine 3   |    |    Machine 4   |
                                  |    Master     |    |    Backup      |
                                  |---------------|    |----------------|
                                  | NIC1: 10.11.12.1   | NIC1: 10.11.12.2
                                  | NIC2: 192.168.100.1| NIC2: 192.168.100.2
                                  | NIC3: 172.168.1.1  | NIC3: 172.168.1.2
                                  | VIP: 10.11.12.99   | VGW: 192.168.100.99
                                  +----------------+    +----------------+
                                            |                    |
                                            |                    |
                                     +----------------+   +----------------+
                                     |   Machine 5    |   |    Machine 6   |
                                     |    Noeud1      |   |     Noeud2     |
                                     |--------------- |   |---------------- |
                                     | NIC1: 192.168.100.10 | NIC1: 192.168.100.20
                                     | Def. Gateway: 192.168.100.99 |
                                     +----------------+   +----------------+
```

### Informations de chaque machine

1. **Machine 1** : Routeur
   - Rôles : DNS, NTP, Routage
   - Interfaces :
     - **NIC1** : NAT (Internet)
     - **NIC2** : Réseau Interne (20.21.22.254/24)
     - **NIC3** : Réseau DMZ (10.11.12.254/24)

2. **Machine 2** : Mail Server
   - Rôle : Serveur Mail avec Postfix
   - Interface :
     - **NIC** : Réseau Interne (20.21.22.10/24)

3. **Machine 3** : Master (Directeur principal)
   - Rôle : Serveur de haute disponibilité avec Keepalived
   - Interfaces :
     - **NIC1** : DMZ (10.11.12.1/24)
     - **NIC2** : Interne pour gestion de haute disponibilité (192.168.100.1/24)
     - **NIC3** : Réseau de synchronisation (172.168.1.1/30)
   - VIP (adresse IP virtuelle) : 10.11.12.99/24

4. **Machine 4** : Backup (Directeur secondaire)
   - Rôle : Serveur de haute disponibilité avec Keepalived
   - Interfaces :
     - **NIC1** : DMZ (10.11.12.2/24)
     - **NIC2** : Interne pour gestion de haute disponibilité (192.168.100.2/24)
     - **NIC3** : Réseau de synchronisation (172.168.1.2/30)
   - Passerelle Virtuelle (VGW) : 192.168.100.99/24

5. **Machine 5** : Noeud1
   - Rôle : Serveur réel pour service web
   - Interface :
     - **NIC1** : Interne pour communication avec directeurs (192.168.100.10/24)
   - Passerelle par défaut : 192.168.100.99

6. **Machine 6** : Noeud2
   - Rôle : Serveur réel pour service web
   - Interface :
     - **NIC1** : Interne pour communication avec directeurs (192.168.100.20/24)
   - Passerelle par défaut : 192.168.100.99




#### **Section 1 : Configuration et rôle de la VIP et du VGW**

1. **Quelle est la fonction principale d'une VIP (Virtual IP) dans une infrastructure réseau ?**  
   a) Fournir une passerelle Internet pour les machines du réseau.  
   b) Offrir une adresse IP unique et stable partagée entre plusieurs serveurs pour la haute disponibilité.  
   c) Gérer la configuration DNS entre les machines.  
   d) Attribuer dynamiquement des adresses IP aux serveurs backend.

   **Réponse : b**

---

2. **Quelle adresse IP est attribuée à la VIP dans l'exemple ci-dessus ?**  
   a) 192.168.100.99  
   b) 10.11.12.99  
   c) 20.21.22.254  
   d) 172.168.1.2  

   **Réponse : b**

---

3. **Pourquoi la VIP doit être sur le même réseau que les machines Master et Backup ?**  
   a) Pour simplifier la configuration DNS.  
   b) Pour garantir que les clients peuvent accéder à la VIP sans routage supplémentaire.  
   c) Pour éviter les conflits d'adresses IP.  
   d) Parce que VRRP ne fonctionne pas avec plusieurs réseaux.

   **Réponse : b**

---

4. **Quelle commande permet de vérifier si la VIP est attribuée à une machine ?**  
   a) `ping <adresse VIP>`  
   b) `ip addr show <interface>`  
   c) `nslookup <nom de la VIP>`  
   d) `systemctl status keepalived`

   **Réponse : b**

---

5. **Qu'est-ce qui déclenche la bascule de la VIP entre le Master et le Backup ?**  
   a) Une modification manuelle de la configuration.  
   b) Un redémarrage du service DNS.  
   c) Une défaillance du Master détectée par Keepalived via VRRP.  
   d) Une perte de connectivité Internet sur le Backup.

   **Réponse : c**

---

6. **Pourquoi est-il important d'utiliser une VGW (Virtual Gateway) sur le réseau Backend ?**  
   a) Pour fournir un point d'accès unique aux machines backend même en cas de panne.  
   b) Pour permettre la synchronisation entre les machines Master et Backup.  
   c) Pour garantir une connectivité Internet stable.  
   d) Pour assurer la résolution DNS entre les machines backend.

   **Réponse : a**

---

#### **Section 2 : Interfaces réseau (NIC) dans l'infrastructure**

7. **Combien de NIC sont configurées sur le Master et le Backup dans cette architecture ?**  
   a) 1  
   b) 2  
   c) 3  
   d) 4  

   **Réponse : c**

---

8. **Quelle interface réseau est utilisée pour la VIP dans l'exemple ?**  
   a) NIC1 (DMZ)  
   b) NIC2 (Backend)  
   c) NIC3 (Sync)  
   d) Toutes les interfaces  

   **Réponse : a**

---

9. **Pourquoi chaque machine backend (Noeud1, Noeud2) utilise-t-elle une seule NIC ?**  
   a) Pour simplifier la configuration réseau.  
   b) Parce que le trafic des backend est isolé sur le réseau 192.168.100.0/24.  
   c) Parce que les machines backend n'ont pas besoin d'accéder au réseau DMZ.  
   d) Toutes ces réponses.  

   **Réponse : d**

---

10. **Comment configurer une passerelle par défaut sur une machine backend (Noeud1 ou Noeud2) ?**  
   a) Modifier le fichier `/etc/hosts`.  
   b) Ajouter `gateway4: 192.168.100.99` dans le fichier de configuration Netplan.  
   c) Redémarrer le service DNS.  
   d) Configurer une VIP différente sur chaque backend.  

   **Réponse : b**

---

#### **Section 3 : Protocole VRRP et Keepalived**

11. **Quel est le rôle principal du protocole VRRP dans Keepalived ?**  
   a) Configurer automatiquement les DNS pour la VIP.  
   b) Assurer la redondance de la VIP entre plusieurs serveurs.  
   c) Activer le routage dynamique sur le réseau Backend.  
   d) Synchroniser les sessions entre les machines backend.  

   **Réponse : b**

---

12. **Que représente le paramètre `priority` dans la configuration VRRP ?**  
   a) La priorité des paquets réseau entre les machines.  
   b) L'ordre de basculement entre le Master et le Backup.  
   c) La fréquence des annonces envoyées par Keepalived.  
   d) La configuration de la passerelle par défaut.  

   **Réponse : b**

---

13. **Quelle est la différence entre le Master et le Backup dans VRRP ?**  
   a) Le Master gère activement la VIP, tandis que le Backup est en attente pour prendre le relais en cas de panne.  
   b) Le Master et le Backup gèrent la VIP simultanément.  
   c) Le Master ne participe pas à la synchronisation des configurations, contrairement au Backup.  
   d) Le Backup est toujours configuré avec une priorité plus élevée que le Master.  

   **Réponse : a**

---

14. **Quelle commande permet de redémarrer le service Keepalived sur une machine ?**  
   a) `sudo service keepalived restart`  
   b) `sudo systemctl restart keepalived`  
   c) `sudo systemctl enable keepalived`  
   d) `sudo restart keepalived.conf`  

   **Réponse : b**

---

15. **Que se passe-t-il si deux machines dans un cluster VRRP ont la même priorité ?**  
   a) Les deux machines partageront la VIP.  
   b) Une des machines sera élue aléatoirement comme Master.  
   c) La VIP sera automatiquement désactivée.  
   d) Le Backup prendra toujours la priorité sur le Master.  

   **Réponse : b**

---

#### **Section 4 : Dépannage et tests**

16. **Quelle commande permet de tester la connectivité avec une VIP depuis une machine cliente ?**  
   a) `ping <adresse VIP>`  
   b) `ip addr show <interface>`  
   c) `dig <adresse VIP>`  
   d) `systemctl status keepalived`  

   **Réponse : a**

---

17. **Que vérifier si la VIP ne bascule pas correctement entre le Master et le Backup ?**  
   a) La priorité dans la configuration VRRP.  
   b) La connectivité entre les machines Master et Backup.  
   c) L'état du service Keepalived sur chaque machine.  
   d) Toutes ces réponses.  

   **Réponse : d**

---

18. **Comment tester manuellement le basculement de la VIP entre le Master et le Backup ?**  
   a) Arrêter le service Keepalived sur le Master et vérifier que le Backup prend la VIP.  
   b) Modifier la priorité sur le Master et redémarrer le service.  
   c) Pinger la VIP depuis une machine cliente et observer les réponses.  
   d) Toutes ces réponses.  

   **Réponse : d**

---

19. **Si une machine backend ne peut pas atteindre Internet via la VGW, quelles vérifications effectuer ?**  
   a) Vérifier que la passerelle par défaut est configurée correctement.  
   b) Tester la connectivité entre la machine backend et la VGW (192.168.100.99).  
   c) S'assurer que la VIP est active sur le Master ou le Backup.  
   d) Toutes ces réponses.  

   **Réponse : d**

---

20. **Pourquoi est-il recommandé de configurer un mot de passe pour le protocole VRRP ?**  
   a) Pour empêcher un attaquant de prendre le contrôle de la VIP.  
   b) Pour synchroniser les configurations DNS entre les machines.  
   c) Pour garantir que le service Keepalived redémarre automatiquement après une panne.  
   d) Pour permettre au Backup de surveiller le Master.  

   **Réponse : a**






####  Concepts généraux de Keepalived et VRRP**

21. **Quelle est la fonction principale de Keepalived dans une infrastructure réseau ?**  
   a) Gérer les configurations DNS.  
   b) Offrir une redondance de services à l'aide d'une VIP.  
   c) Configurer automatiquement les interfaces réseau.  
   d) Activer la gestion des pare-feu.

   **Réponse : b**

---

22. **Quel protocole est utilisé par Keepalived pour gérer la redondance de la VIP ?**  
   a) BGP (Border Gateway Protocol).  
   b) RIP (Routing Information Protocol).  
   c) VRRP (Virtual Router Redundancy Protocol).  
   d) ICMP (Internet Control Message Protocol).

   **Réponse : c**

---

23. **Dans le protocole VRRP, quelle est la machine désignée comme le routeur principal actif ?**  
   a) Backup.  
   b) Master.  
   c) Gateway.  
   d) Client.

   **Réponse : b**

---

24. **Quelle est la fréquence par défaut des annonces (advertisements) VRRP envoyées par le Master ?**  
   a) 1 seconde.  
   b) 5 secondes.  
   c) 10 secondes.  
   d) 15 secondes.

   **Réponse : a**

---

25. **Pourquoi utilise-t-on un ID unique dans la configuration VRRP (`virtual_router_id`) ?**  
   a) Pour identifier un cluster VRRP particulier.  
   b) Pour attribuer une priorité unique au Master.  
   c) Pour synchroniser les DNS du cluster.  
   d) Pour configurer une passerelle par défaut.

   **Réponse : a**

---

#### **Section 2 : Configuration des interfaces réseau**

26. **Pourquoi le Master et le Backup ont-ils besoin de plusieurs interfaces réseau ?**  
   a) Pour isoler les rôles des différentes interfaces (DMZ, Backend, Synchronisation).  
   b) Pour garantir que les services restent accessibles même en cas de panne d'une interface.  
   c) Pour séparer le trafic de gestion, de synchronisation, et de production.  
   d) Toutes ces réponses.

   **Réponse : d**

---

27. **Quelle interface réseau est utilisée pour la VIP dans cette architecture ?**  
   a) NIC1 (DMZ).  
   b) NIC2 (Backend).  
   c) NIC3 (Synchronisation).  
   d) Toutes les interfaces.

   **Réponse : a**

---

28. **Combien d'interfaces réseau sont nécessaires sur le Master et le Backup dans cette configuration ?**  
   a) 1.  
   b) 2.  
   c) 3.  
   d) 4.

   **Réponse : c**

---

29. **Pourquoi une interface dédiée à la synchronisation (NIC3) est-elle utilisée entre le Master et le Backup ?**  
   a) Pour éviter que le trafic de production n'affecte la synchronisation des états entre Master et Backup.  
   b) Pour simplifier la configuration des VIP.  
   c) Pour garantir une redondance de la synchronisation via DNS.  
   d) Pour permettre au Backup d'accéder à Internet via la synchronisation.

   **Réponse : a**

---

30. **Que se passe-t-il si le réseau Backend (NIC2) est mal configuré ?**  
    a) La synchronisation entre le Master et le Backup échouera.  
    b) Les machines backend (Noeud1, Noeud2) ne pourront pas utiliser la VGW.  
    c) Le trafic de production sera interrompu.  
    d) Toutes ces réponses.

    **Réponse : d**

---

#### **Section 3 : Priorités et basculement**

31. **Que détermine le paramètre `priority` dans la configuration VRRP ?**  
    a) L'adresse IP de la VIP.  
    b) L'intervalle d'annonces entre les serveurs.  
    c) L'ordre des serveurs pour gérer la VIP.  
    d) La fréquence des basculements.

    **Réponse : c**

---

32. **Quelle est la priorité par défaut dans Keepalived si elle n'est pas spécifiée ?**  
    a) 50.  
    b) 100.  
    c) 110.  
    d) 90.

    **Réponse : b**

---

33. **Que se passe-t-il si le Master a une priorité inférieure au Backup dans la configuration VRRP ?**  
    a) Le Backup deviendra le Master même si le Master est fonctionnel.  
    b) La VIP sera automatiquement désactivée.  
    c) La synchronisation entre le Master et le Backup échouera.  
    d) Rien, le Backup reste passif.

    **Réponse : a**

---

34. **Comment simuler une panne du Master pour tester le basculement (failover) ?**  
    a) Désactiver l'interface réseau utilisée pour la VIP.  
    b) Arrêter le service Keepalived sur le Master.  
    c) Modifier la priorité du Master à une valeur inférieure à celle du Backup.  
    d) Toutes ces réponses.

    **Réponse : d**

---

35. **Quelle commande permet de vérifier l'adresse IP active de la VIP sur le Backup après un basculement ?**  
    a) `ping <adresse VIP>`  
    b) `ip addr show <interface>`  
    c) `systemctl status keepalived`  
    d) `dig -x <adresse VIP>`

    **Réponse : b**

---

#### **Section 4 : Dépannage de Keepalived et VRRP**

36. **Quels sont les symptômes d'une configuration VRRP incorrecte ?**  
    a) La VIP n'est pas attribuée à aucune machine.  
    b) Le basculement ne fonctionne pas en cas de panne du Master.  
    c) Les annonces VRRP ne sont pas reçues par le Backup.  
    d) Toutes ces réponses.

    **Réponse : d**

---

37. **Comment vérifier si les annonces VRRP sont correctement envoyées entre le Master et le Backup ?**  
    a) Utiliser la commande `tcpdump` pour capturer les paquets VRRP.  
    b) Tester la connectivité entre les interfaces réseau des deux machines.  
    c) Vérifier les logs de Keepalived.  
    d) Toutes ces réponses.

    **Réponse : d**

---

38. **Que vérifier si le Backup ne prend pas le contrôle de la VIP après une panne du Master ?**  
    a) La priorité du Backup doit être inférieure à celle du Master.  
    b) Les annonces VRRP doivent être reçues par le Backup.  
    c) Le service Keepalived doit être actif sur le Backup.  
    d) Toutes ces réponses.

    **Réponse : d**

---

39. **Que se passe-t-il si deux serveurs VRRP revendiquent simultanément la VIP ?**  
    a) Le réseau détectera un conflit d'adresse IP.  
    b) Les clients ne pourront pas atteindre la VIP.  
    c) Les services associés à la VIP deviendront instables.  
    d) Toutes ces réponses.

    **Réponse : d**

---

40. **Comment résoudre un conflit d'adresse IP entre deux serveurs VRRP ?**  
    a) Vérifier que les priorités sont correctement configurées.  
    b) Redémarrer Keepalived sur les deux machines.  
    c) Assurer que chaque machine a un ID unique pour le VRRP.  
    d) Toutes ces réponses.

    **Réponse : d**

---

#### **Section 5 : Tests et validation**

41. **Quel outil peut être utilisé pour tester la connectivité entre le Master et le Backup ?**  
    a) `ping`.  
    b) `tcpdump`.  
    c) `traceroute`.  
    d) Toutes ces réponses.

    **Réponse : d**

---

42. **Comment s'assurer que la VIP est correctement transférée après un basculement ?**  
    a) Tester l'accès à la VIP depuis une machine cliente après l'arrêt du Master.  
    b) Capturer le trafic VRRP pour vérifier les annonces envoyées par le Backup.  
    c) Vérifier que l'adresse VIP est attribuée au Backup avec `ip addr`.  
    d) Toutes ces réponses.

    **Réponse : d**

---

43. **Quel paramètre est crucial pour garantir une synchronisation correcte entre le Master et le Backup dans Keepalived ?**  
    a) `state`.  
    b) `priority`.  
    c) `authentication`.  
    d) `virtual_router_id`.

    **Réponse : d**

---

44. **Pourquoi utiliser un mot de passe partagé dans la configuration VRRP ?**  
    a) Pour empêcher des machines externes de rejoindre le cluster VRRP.  
    b) Pour chiffrer les annonces VRRP entre le Master et le Backup.  
    c) Pour améliorer la synchronisation des priorités.  
    d) Pour activer la redondance entre les machines.

    **Réponse : a**

---

45. **Quelle commande permet de surveiller les logs de Keepalived ?**  
    a) `journalctl -u keepalived`.  
    b) `tail -f /var/log/syslog`.  
    c) `systemctl status keepalived`.  
    d) Toutes ces réponses.

    **Réponse : d**

