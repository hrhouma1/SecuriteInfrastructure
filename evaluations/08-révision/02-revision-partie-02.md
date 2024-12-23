# Partie 02 - *Exemple d'architecture : Haute Disponibilité avec Keepalived*

---

### **Présentation**

L’exercice pratique repose sur une infrastructure réseau visant à configurer et tester une solution de haute disponibilité en utilisant **Keepalived** pour gérer une **VIP (Virtual IP)** et une **VGW (Virtual Gateway)**. La configuration est basée sur des machines virtuelles connectées à différents réseaux virtuels (**VMnet**) pour simuler un environnement réaliste.

---

### 01 - **Diagramme de l’Architecture**

```
                            +----------------+
                            |     CLIENT      |
                            |  Windows 10 Pro |
                            |-----------------|
                            | VMnet3 (NAT)    |
                            | DHCP            |
                            +-----------------+
                                   |
                          +--------------------+
                          |      Routeur       |
                          |     (PfSense)      |
                          +--------------------+
                         NIC1: NAT (Internet)
                         NIC2: VMnet3 (20.21.22.1/24)
                         NIC3: VMnet2 (10.11.12.254/24)
                                   |
             +---------------------+---------------------+
             |                                           |
  +--------------------+                    +-----------------------+
  |      Master         |                    |        Backup        |
  |    Ubuntu Server    |                    |    Ubuntu Server     |
  |---------------------|                    |----------------------|
  | NIC1: VMnet2 (10.11.12.1/24)            | NIC1: VMnet2 (10.11.12.2/24)
  | NIC2: VMnet4 (192.168.100.1/24)         | NIC2: VMnet4 (192.168.100.2/24)
  | NIC3: VMnet5 (172.168.1.1/30)           | NIC3: VMnet5 (172.168.1.2/30)
  +--------------------+                    +-----------------------+
           |                                             |
    +--------------------+                    +-----------------------+
    |      Noeud1        |                    |         Noeud2        |
    |    Ubuntu Server    |                    |    Ubuntu Server     |
    |---------------------|                    |----------------------|
    | NIC1: VMnet4 (192.168.100.10/24)        | NIC1: VMnet4 (192.168.100.20/24)
    | Default GW: 192.168.100.99              | Default GW: 192.168.100.99
    +--------------------+                    +-----------------------+
```

---

### 02 - **Tableau des Rôles et Adresses IP**

| **Nom**      | **Réseau (VMnet)** | **Adresse IP**       | **Rôle**                         | **Fonctionnalités**                                 |
|--------------|--------------------|----------------------|-----------------------------------|----------------------------------------------------|
| **PfSense**  | VMnet3 (LAN)       | 20.21.22.1/24        | Routeur, DNS, NAT                | Assure le routage entre les réseaux.              |
|              | VMnet2 (DMZ)       | 10.11.12.254/24      |                                   |                                                    |
| **Master**   | VMnet2 (DMZ)       | 10.11.12.1/24        | Directeur Principal               | Gère la VIP et VGW pour les nœuds.                |
|              | VMnet4 (Backend)   | 192.168.100.1/24     |                                   |                                                    |
|              | VMnet5 (Sync)      | 172.168.1.1/30       | Synchronisation                  | Synchronisation Keepalived avec le Backup.        |
| **Backup**   | VMnet2 (DMZ)       | 10.11.12.2/24        | Directeur Secondaire             | Prend le relais en cas de panne du Master.        |
|              | VMnet4 (Backend)   | 192.168.100.2/24     |                                   |                                                    |
|              | VMnet5 (Sync)      | 172.168.1.2/30       | Synchronisation                  | Synchronisation Keepalived avec le Master.        |
| **Noeud1**   | VMnet4 (Backend)   | 192.168.100.10/24    | Serveur Réel                     | Héberge des services web.                         |
| **Noeud2**   | VMnet4 (Backend)   | 192.168.100.20/24    | Serveur Réel                     | Héberge des services web.                         |
| **VGW**      | VMnet4 (Backend)   | 192.168.100.99/24    | Passerelle Virtuelle             | Assure le routage des requêtes Backend.           |
| **VIP**      | VMnet2 (DMZ)       | 10.11.12.99/24       | Virtual IP                       | Adresse virtuelle pour les directeurs Keepalived. |

---

### 03 - **Description des Réseaux**

1. **VMnet3 (Interne - LAN) :**
   - Connecte le Client et le Routeur.
   - Assure l’accès Internet via NAT.

2. **VMnet2 (DMZ) :**
   - Connecte les Directeurs (Master et Backup) et le Routeur.
   - Héberge la **VIP (10.11.12.99)**, qui distribue le trafic.

3. **VMnet4 (Backend) :**
   - Connecte les Directeurs et les Nœuds.
   - Héberge la **VGW (192.168.100.99)** pour assurer la communication.

4. **VMnet5 (Synchronisation) :**
   - Connecte le Master et le Backup uniquement.
   - Permet la synchronisation privée via Keepalived.

---

### Partie 02 - *QUESTIONS*

---

#### **01. Pourquoi utiliser des réseaux VMnet distincts dans cette architecture ?**

**Réponse :**
- Pour isoler les flux (Interne, DMZ, Backend, et Synchronisation).
- Évite les conflits de trafic.
- Renforce la sécurité en séparant les rôles (ex. : DMZ pour le trafic public, Backend pour les services internes).

---

#### **02. Expliquez le rôle de la Virtual IP (VIP) dans cette architecture.**

**Réponse :**
- La VIP (10.11.12.99) représente l’adresse publique du cluster.
- Elle distribue les requêtes entrantes entre les Directeurs Master et Backup.
- En cas de panne, Keepalived bascule automatiquement la VIP vers le serveur fonctionnel.

---

#### **03. Quelle est la fonction de la VGW (192.168.100.99) dans le réseau Backend ?**

**Réponse :**
- La VGW est la passerelle virtuelle utilisée par les nœuds Backend.
- Elle redirige le trafic des nœuds vers les Directeurs Master/Backup, selon leur disponibilité.

---

#### **04. Pourquoi un réseau dédié (VMnet5) est-il nécessaire pour la synchronisation ?**

**Réponse :**
- Permet de sécuriser les données de synchronisation entre le Master et le Backup.
- Empêche les perturbations dues au trafic DMZ ou Backend.

---

#### **05. Pourquoi est-il important que les nœuds Backend utilisent la VGW comme passerelle ?**

**Réponse :**
- Cela garantit que le trafic est routé via les Directeurs (Master/Backup).
- Assure une haute disponibilité même en cas de panne d’un Directeur.

---

#### **06. Si un nœud ne peut pas pinguer la VGW, quelles vérifications effectueriez-vous ?**

**Réponse :**
1. Vérifiez la configuration IP du nœud :
   ```bash
   ip route
   ```
2. Assurez-vous que la VGW est active sur le Master ou le Backup.
3. Vérifiez la connectivité réseau sur VMnet4.

---

#### **07. Expliquez pourquoi le Routeur est connecté à plusieurs réseaux VMnet.**

**Réponse :**
- **VMnet3 :** Assure l’accès Internet via NAT.
- **VMnet2 :** Connecte le réseau DMZ pour gérer le trafic des Directeurs.
- Permet le routage entre les réseaux et l’accès à Internet.

---

#### **08. Pourquoi est-il nécessaire d’attribuer des adresses IP fixes aux machines ?**

**Réponse :**
- Assure une communication stable entre les machines.
- Permet à Keepalived de fonctionner correctement avec les VIP et VGW.
- Simplifie le dépannage et la gestion réseau.

---

#### **09. Si le Client ne peut pas accéder à la VIP, quelles étapes de diagnostic effectueriez-vous ?**

**Réponse :**
1. Pingez la VIP (10.11.12.99) depuis le Client.
2. Vérifiez que le Master gère la VIP (commande `ip addr show`).
3. Testez la connectivité entre le Client et VMnet3.

---

#### **10. Expliquez comment Keepalived gère le basculement entre Master et Backup.**

**Réponse :**
- Keepalived utilise le protocole VRRP pour surveiller la disponibilité des Directeurs.
- Si le Master tombe en panne, le Backup prend en charge la VIP et la VGW.































#### **11. Quel est le rôle de la synchronisation entre le Master et le Backup dans Keepalived ?**

**Réponse :**
- La synchronisation garantit que le Backup dispose des informations nécessaires pour prendre la relève en cas de panne du Master.
- Permet de répliquer l’état de la configuration, y compris la gestion de la VIP et de la VGW.

---

#### **12. Si la VIP n’apparaît pas sur le Master, quelles vérifications devez-vous effectuer ?**

**Réponse :**
1. Assurez-vous que le service Keepalived est actif :
   ```bash
   sudo systemctl status keepalived
   ```
2. Vérifiez la configuration de Keepalived (`/etc/keepalived/keepalived.conf`) pour la déclaration de la VIP.
3. Assurez-vous que l’interface réseau associée à la VIP est active (commande `ip addr show`).

---

#### **13. Pourquoi est-il important que la VIP soit sur le même réseau que les adresses des Directeurs ?**

**Réponse :**
- La VIP doit être sur le même réseau (VMnet2 - DMZ) que les adresses des Directeurs pour que le trafic soit routé correctement.
- Si elle est sur un autre réseau, les clients ne pourront pas atteindre les serveurs.

---

#### **14. Pourquoi les Nœuds (Backend) ne sont-ils pas directement exposés à la DMZ ?**

**Réponse :**
- Les Nœuds Backend hébergent les services réels, mais ils ne sont accessibles qu’indirectement via les Directeurs.
- Cela renforce la sécurité et permet aux Directeurs de gérer la haute disponibilité via la VGW.

---

#### **15. Expliquez le rôle de VRRP dans Keepalived.**

**Réponse :**
- VRRP (Virtual Router Redundancy Protocol) est utilisé pour élire dynamiquement un Directeur principal (Master) et un secondaire (Backup).
- Il permet de basculer automatiquement la VIP et la VGW vers le Backup si le Master devient indisponible.

---

#### **16. Que se passe-t-il si deux machines revendiquent simultanément la VIP ?**

**Réponse :**
- Un conflit d’adresse IP se produit, entraînant des erreurs réseau.
- VRRP permet de gérer ce scénario en s’assurant qu’un seul Directeur peut posséder la VIP à la fois.

---

#### **17. Quelle commande utiliseriez-vous pour vérifier si la VGW est active sur le Master ?**

**Réponse :**
```bash
ip addr show
```
- Vous devriez voir l’adresse IP virtuelle (VGW : 192.168.100.99) attribuée à l’interface Backend du Master.

---

#### **18. Pourquoi les Nœuds utilisent-ils une passerelle virtuelle (VGW) au lieu d’un Directeur spécifique ?**

**Réponse :**
- La VGW permet de rediriger le trafic Backend vers le Directeur actif (Master ou Backup).
- Cela garantit que le trafic continue à fonctionner en cas de panne d’un Directeur.

---

#### **19. Quels tests effectueriez-vous pour vérifier la connectivité sur VMnet4 (Backend) ?**

**Réponse :**
1. **Ping entre les Nœuds et la VGW** :
   - Depuis Noeud1, testez :
     ```bash
     ping 192.168.100.99
     ```
2. **Ping entre les Nœuds et les Directeurs** :
   - Depuis Noeud1, testez :
     ```bash
     ping 192.168.100.1
     ```
   - Depuis Noeud2, testez :
     ```bash
     ping 192.168.100.2
     ```

---

#### **20. Si la synchronisation entre Master et Backup échoue, quelles étapes suivriez-vous pour résoudre le problème ?**

**Réponse :**
1. Vérifiez la connectivité sur le réseau de synchronisation (VMnet5) entre Master et Backup :
   ```bash
   ping 172.168.1.2
   ```
2. Vérifiez les journaux de Keepalived sur les deux machines pour identifier les erreurs :
   ```bash
   sudo tail -f /var/log/syslog
   ```
3. Assurez-vous que la configuration de Keepalived inclut bien la directive `vrrp_instance` avec les mêmes identifiants sur les deux Directeurs.

---

### Section Bonus : **Scénarios Pratiques**

#### **21. Scénario :**
Le client peut pinger la VIP (10.11.12.99) mais ne peut pas accéder aux services des Nœuds. Quelle pourrait être la cause ?

**Réponse :**
- La VGW (192.168.100.99) n’est pas active ou configurée correctement.
- Les Nœuds n’ont pas la VGW comme passerelle par défaut.
- Les services web sur les Nœuds ne fonctionnent pas ou sont mal configurés.

---

#### **22. Scénario :**
Après avoir arrêté le Master, le Backup ne prend pas la VIP. Que vérifiez-vous ?

**Réponse :**
1. Vérifiez que le service Keepalived est actif sur le Backup.
2. Vérifiez la priorité du Backup dans la configuration Keepalived (elle doit être inférieure à celle du Master).
3. Testez la connectivité sur le réseau de synchronisation (VMnet5).

---

#### **23. Scénario :**
Les Nœuds peuvent pinguer la VGW, mais le client ne peut pas accéder aux services hébergés. Quelles vérifications effectueriez-vous ?

**Réponse :**
1. Assurez-vous que les services web sur les Nœuds fonctionnent correctement.
2. Vérifiez que le routage est activé sur les Directeurs pour rediriger le trafic du réseau DMZ vers le Backend.


















#### **24. Pourquoi utilise-t-on des adresses privées pour les réseaux DMZ et Backend ?**

**Réponse :**
- Les adresses privées (comme celles des réseaux 10.0.0.0/8 ou 192.168.0.0/16) sont isolées du réseau public et offrent une sécurité supplémentaire.
- Cela empêche un accès direct depuis Internet aux services sensibles (comme les Nœuds ou les Directeurs).

---

#### **25. Pourquoi Keepalived utilise-t-il une priorité pour les Directeurs (Master et Backup) ?**

**Réponse :**
- La priorité détermine quel Directeur doit être actif en temps normal.
- Le Directeur avec la priorité la plus élevée (par exemple, `priority 100` sur le Master) gère la VIP.
- En cas de panne, le Backup (priorité plus faible, par exemple `priority 90`) prend le relais.

---

#### **26. Comment s’assurer que les services web sur les Nœuds sont accessibles ?**

**Réponse :**
1. Connectez-vous à Noeud1 et Noeud2.
2. Testez l’accès localement en ouvrant un navigateur ou en utilisant `curl` :
   ```bash
   curl http://localhost
   ```
3. Testez à partir d’une autre machine sur le réseau Backend (VMnet4) en accédant à l’adresse IP du Nœud :
   ```bash
   curl http://192.168.100.10
   ```

---

#### **27. Pourquoi est-il important de désactiver le DHCP sur les réseaux VMnet ?**

**Réponse :**
- Dans une architecture avec des IP fixes, le DHCP pourrait attribuer automatiquement des adresses qui entreraient en conflit avec les IP fixes configurées sur les machines.
- Les réseaux DMZ, Backend et Sync doivent rester isolés avec des adresses configurées manuellement pour garantir une communication stable.

---

#### **28. Quelles différences majeures existe-t-il entre une **VIP** et une **VGW** ?**

**Réponse :**

| **Aspect**          | **VIP (Virtual IP)**               | **VGW (Virtual Gateway)**            |
|----------------------|------------------------------------|--------------------------------------|
| **Rôle**             | Adresse IP virtuelle pour un service (ex : DMZ). | Passerelle virtuelle pour le routage. |
| **Localisation**     | Configurée sur les Directeurs (DMZ). | Configurée sur les Nœuds (Backend).  |
| **Fonctionnalité**   | Permet de distribuer les requêtes entrantes. | Permet de router le trafic interne. |

---

#### **29. Quels outils utiliseriez-vous pour diagnostiquer les problèmes sur le réseau Sync (VMnet5) ?**

**Réponse :**
1. **Ping** pour tester la connectivité entre Master et Backup :
   ```bash
   ping 172.168.1.2
   ```
2. **Traceroute** pour vérifier le chemin des paquets :
   ```bash
   traceroute 172.168.1.2
   ```
3. **Tcpdump** pour capturer et analyser le trafic VRRP entre les Directeurs :
   ```bash
   sudo tcpdump -i eth2 vrrp
   ```

---

#### **30. Quelles erreurs pourraient empêcher le basculement automatique de la VIP sur le Backup ?**

**Réponse :**
1. La configuration Keepalived sur le Backup est incorrecte (ex : priorité mal définie).
2. Problème de connectivité sur le réseau Sync (VMnet5).
3. Le service Keepalived n’est pas actif sur le Backup.
4. Le Master n’a pas correctement libéré la VIP en cas de panne.

---

#### **31. Pourquoi isoler le réseau

#### **31. Pourquoi isoler le réseau de synchronisation (VMnet5) entre le Master et le Backup ?**

**Réponse :**
- **Sécurité :** Les données échangées entre le Master et le Backup, comme les informations sur l’état des services et les configurations, sont sensibles et ne doivent pas être exposées aux autres réseaux.
- **Stabilité :** En isolant le réseau Sync, on évite toute perturbation due au trafic généré par les réseaux DMZ ou Backend.
- **Performance :** La synchronisation reste rapide et efficace sans être affectée par d'autres flux.

---

#### **32. Pourquoi est-il recommandé de tester le ping entre les Nœuds et la VGW après configuration ?**

**Réponse :**
- **Vérifier la connectivité réseau :** Assure que les Nœuds peuvent communiquer avec leur passerelle (VGW : 192.168.100.99).
- **Diagnostiquer les erreurs de routage :** Si le ping échoue, cela pourrait indiquer un problème de configuration sur le Master, le Backup, ou les interfaces des Nœuds.
- **S’assurer de la haute disponibilité :** Le test garantit que le VGW fonctionne même si le Master ou le Backup tombe.

---

#### **33. Comment vérifier que la VIP est bien configurée sur le réseau DMZ (VMnet2) ?**

**Réponse :**
1. Sur le Master, exécutez la commande suivante :
   ```bash
   ip addr show
   ```
2. Vérifiez que l’adresse **10.11.12.99/24** (VIP) est associée à l’interface NIC1 sur VMnet2.
3. Pingez la VIP depuis une machine sur le réseau DMZ :
   ```bash
   ping 10.11.12.99
   ```

---

#### **34. Si la VIP fonctionne mais que le client ne reçoit pas de réponse, quelles vérifications effectuer ?**

**Réponse :**
1. Vérifiez que les services web sur les Nœuds (Backend) sont actifs.
2. Assurez-vous que les Nœuds utilisent la VGW comme passerelle par défaut.
3. Testez si le routage est actif entre le réseau DMZ (VMnet2) et le Backend (VMnet4) sur le Master ou Backup.

---

#### **35. Pourquoi est-il essentiel que le routeur (PfSense) assure le NAT uniquement pour la DMZ ?**

**Réponse :**
- La DMZ est la seule zone qui nécessite un accès Internet pour gérer les requêtes des clients externes.
- Le Backend et les réseaux internes (Sync) doivent rester isolés pour éviter tout risque de fuite ou d’intrusion.

---

#### **36. Pourquoi configurer des priorités différentes entre le Master et le Backup dans Keepalived ?**

**Réponse :**
- La priorité détermine l’ordre dans lequel les Directeurs gèrent la VIP et la VGW.
- En cas de retour du Master après une panne, sa priorité plus élevée lui permet de reprendre automatiquement le contrôle (fonction de préemption).

---

#### **37. Quelles informations devez-vous inclure dans la configuration Keepalived sur le Master ?**

**Réponse :**
1. **VRRP Instance** pour la gestion de la VIP :
   ```bash
   vrrp_instance VI_1 {
       state MASTER
       interface eth0
       virtual_router_id 50
       priority 100
       advert_int 1
       virtual_ipaddress {
           10.11.12.99
       }
   }
   ```
2. **Configuration de la VGW (Backend)** pour le routage :
   ```bash
   vrrp_instance VI_2 {
       state MASTER
       interface eth1
       virtual_router_id 51
       priority 100
       advert_int 1
       virtual_ipaddress {
           192.168.100.99
       }
   }
   ```

---

#### **38. Pourquoi désactiver le mode "Préemption" peut-il être utile dans certains cas ?**

**Réponse :**
- Désactiver la préemption empêche le Master de reprendre automatiquement la VIP après une panne, même s’il revient en ligne.
- Cela peut être utile si le Master nécessite une maintenance ou des tests avant de reprendre son rôle.

---

#### **39. Quels risques pourraient survenir si les réseaux DMZ et Backend ne sont pas isolés ?**

**Réponse :**
- **Problèmes de performance :** Le trafic Backend pourrait saturer le réseau DMZ, affectant les requêtes des clients.
- **Risque de sécurité :** Les services internes (Backend) pourraient être exposés à des attaques venant de la DMZ.
- **Conflits de routage :** Les flux entre les deux réseaux pourraient interférer, créant des erreurs dans le routage.

---

#### **40. Expliquez comment tester la redondance avec Keepalived ?**

**Réponse :**
1. **Simulez une panne du Master :**
   - Arrêtez le service Keepalived sur le Master :
     ```bash
     sudo systemctl stop keepalived
     ```
2. **Vérifiez que le Backup prend la VIP et la VGW :**
   - Sur le Backup, exécutez :
     ```bash
     ip addr show
     ```
3. **Testez la connectivité :**
   - Pingez la VIP et la VGW depuis le Client et les Nœuds.
4. **Relancez le Master et vérifiez le basculement inverse :**
   ```bash
   sudo systemctl start keepalived
   ```








#### **41. Pourquoi est-il important d'utiliser une Virtual Gateway (VGW) dans cette infrastructure ?**
- a) Pour permettre l'accès des machines backend à Internet même en cas de panne.
- b) Pour distribuer les requêtes des clients entre plusieurs serveurs backend.
- c) Pour simplifier la configuration des Nœuds en utilisant une seule passerelle par défaut.
- d) Pour fournir un routage entre le réseau DMZ et le réseau Backend.

**Réponse : c**

---

#### **42. Si le VGW (192.168.100.99) est mal configuré sur le Backup, que se passera-t-il si le Master tombe ?**
- a) Les Nœuds perdront leur connectivité vers l'extérieur.
- b) Le Backup prendra automatiquement la VIP sans impact.
- c) Les requêtes des Nœuds seront redirigées vers le réseau DMZ.
- d) Les Nœuds continueront de fonctionner normalement grâce au DNS.

**Réponse : a**

---

#### **43. Pourquoi est-il nécessaire d'utiliser le protocole VRRP dans Keepalived ?**
- a) Pour garantir la synchronisation entre les Master et Backup.
- b) Pour attribuer automatiquement des adresses IP aux machines.
- c) Pour gérer la redondance de la VIP ou du VGW entre plusieurs serveurs.
- d) Pour permettre aux clients de résoudre les adresses IP en noms d'hôte.

**Réponse : c**

---

#### **44. Lors d'une panne du Master, que devez-vous vérifier sur le Backup pour garantir que le VGW fonctionne ?**
- a) Que l'adresse IP virtuelle **192.168.100.99** est attribuée à l'interface Backend.
- b) Que le DNS est actif sur la machine Backup.
- c) Que le Backup peut pinger l'adresse du Master sur le réseau DMZ.
- d) Que le service Keepalived du Master redémarre automatiquement.

**Réponse : a**

---

#### **45. Pourquoi est-il important de configurer les machines backend pour utiliser le VGW comme passerelle par défaut ?**
- a) Pour éviter des erreurs dans la résolution DNS des Nœuds.
- b) Pour permettre aux Nœuds de communiquer avec le réseau DMZ en toute sécurité.
- c) Pour que les Nœuds puissent accéder à d'autres réseaux via la passerelle, même en cas de panne.
- d) Pour éviter la surcharge des interfaces réseau des Nœuds.

**Réponse : c**

---

#### **46. Comment tester que la VIP (192.168.100.99) est fonctionnelle ?**
- a) Pingez la VIP depuis une machine dans le réseau DMZ.
- b) Utilisez la commande `ip addr show` sur le Master ou le Backup.
- c) Arrêtez le service Keepalived sur le Master et vérifiez que le Backup prend la VIP.
- d) Toutes ces réponses.

**Réponse : d**

---

#### **47. Pourquoi est-il nécessaire de configurer une zone inversée (reverse DNS) pour chaque sous-réseau ?**
- a) Pour permettre à Bind9 de résoudre les noms d'hôte en adresses IP.
- b) Pour garantir que les outils réseau comme `ping` ou `nslookup` fonctionnent correctement.
- c) Pour fournir une résolution d'adresse IP en noms d'hôte, notamment pour des services comme Postfix.
- d) Toutes ces réponses.

**Réponse : d**

---

#### **48. Quelle commande permet de tester la configuration des zones inversées ?**
- a) `dig @<IP_DNS> -x <adresse_IP>`
- b) `ping -r <adresse_IP>`
- c) `named-checkzone <nom_de_zone> <fichier_de_zone>`
- d) `bind9-checkconf`

**Réponse : a**

---

#### **49. Si un serveur de mail refuse une connexion SMTP en raison d'un problème DNS, que devez-vous vérifier en priorité ?**
- a) La configuration de la zone directe.
- b) La présence d'une zone inversée pour le réseau du serveur de mail.
- c) La synchronisation des zones DNS entre le Master et le Backup.
- d) La connectivité entre le serveur de mail et le client.

**Réponse : b**

---

#### **50. Pourquoi le réseau Backend (192.168.100.0/24) utilise-t-il une zone inversée distincte ?**
- a) Pour isoler les Nœuds des autres réseaux en termes de résolution DNS.
- b) Parce que chaque sous-réseau doit avoir une zone inversée unique.
- c) Pour permettre aux services internes, comme le routage, de fonctionner correctement.
- d) Toutes ces réponses.

**Réponse : d**

---

#### **51. Que signifie un échec de la commande `dig -x 192.168.100.10` ?**
- a) La configuration de Bind9 est incorrecte.
- b) Le fichier de zone inversée n'est pas correctement associé au réseau.
- c) Le DNS ne parvient pas à résoudre l'adresse IP en nom d'hôte.
- d) Toutes ces réponses.

**Réponse : d**

---

#### **52. Que devez-vous vérifier si le Backup ne prend pas le VGW après une panne du Master ?**
- a) La priorité dans la configuration Keepalived du Backup.
- b) L'état du service Keepalived sur le Backup.
- c) La connectivité entre le Backup et le réseau Sync.
- d) Toutes ces réponses.

**Réponse : d**

---

#### **53. Pourquoi est-il recommandé de tester les zones inversées avec la commande `nslookup` après configuration ?**
- a) Pour vérifier que chaque adresse IP a un enregistrement PTR valide.
- b) Pour s'assurer que les noms d'hôte correspondent aux adresses IP attribuées.
- c) Pour diagnostiquer les problèmes de résolution DNS dans les outils réseau.
- d) Toutes ces réponses.

**Réponse : d**

---

#### **54. Si une machine backend ne peut pas accéder à Internet via la VGW, quelles vérifications effectuer ?**
- a) Assurez-vous que la passerelle par défaut est configurée sur **192.168.100.99**.
- b) Vérifiez que la VIP est active sur le Master ou le Backup.
- c) Testez la connectivité avec un ping vers une IP externe (ex : 8.8.8.8).
- d) Toutes ces réponses.

**Réponse : d**

---

#### **55. Si la commande `ping 192.168.100.99` échoue depuis Noeud1, quelle pourrait être la cause ?**
- a) Le VGW n'est pas actif sur le Master ou le Backup.
- b) La configuration réseau de Noeud1 est incorrecte.
- c) Le réseau Backend (VMnet3) est mal configuré dans VMware.
- d) Toutes ces réponses.

**Réponse : d**

---

#### **56. Pourquoi la zone inversée pour le réseau DMZ (10.11.12.0/24) inclut-elle des enregistrements pour le Master et le Backup ?**
- a) Pour permettre aux outils de diagnostic d'identifier les serveurs du réseau DMZ.
- b) Pour fournir une résolution complète entre les adresses IP et les noms d'hôte.
- c) Pour assurer la compatibilité avec les services qui utilisent le DNS.
- d) Toutes ces réponses.

**Réponse : d**

---

#### **57. Si le réseau Sync (172.168.1.0/30) ne fonctionne pas correctement, quel impact cela a-t-il sur le VGW ?**
- a) Le Backup ne pourra pas prendre le contrôle du VGW en cas de panne.
- b) Les Nœuds backend perdront leur connectivité.
- c) La résolution DNS cessera de fonctionner.
- d) Aucune conséquence directe sur le VGW.

**Réponse : a**

---

#### **58. Comment le VGW améliore-t-il la gestion des réseaux backend ?**
- a) En offrant une seule passerelle stable pour les Nœuds backend.
- b) En simplifiant la configuration des clients backend.
- c) En garantissant une continuité de service même en cas de panne.
- d) Toutes ces réponses.

**Réponse : d**

---

#### **59. Pourquoi utiliser Keepalived pour gérer le VGW dans ce TP plutôt qu'un autre outil ?**
- a) Keepalived est léger, simple à configurer, et supporte VRRP.
- b) Il offre une redondance robuste pour les services critiques.
- c) Il est natif de Linux, adapté à l'architecture utilisée.
- d) Toutes ces réponses.

**Réponse : d**

---

#### **60. Pourquoi est-il recommandé de tester le basculement (failover) manuellement ?**
- a) Pour s'assurer que le Backup est correctement configuré.
- b) Pour diagnostiquer les problèmes potentiels de connectivité ou de priorité.
- c) Pour valider que la VGW bascule correctement entre Master et Backup.
- d) Toutes ces réponses.

**Réponse : d**






-----------------------
# Résumé et annexe**
-----------------------

1. **La VIP (10.11.12.99)** est une adresse IP virtuelle qui bascule automatiquement entre le Master et le Backup.
2. **Keepalived** utilise le protocole VRRP pour gérer la VIP.
3. **La priorité détermine l'ordre des machines** :
   - Master : Priorité 100.
   - Backup : Priorité 90.
4. En cas de panne du Master, la VIP est transférée au Backup.
5. Les services connectés à la VIP continuent de fonctionner sans interruption.



