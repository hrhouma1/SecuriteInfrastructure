-----------
# 01 - Pratique

Ce guide couvre :
- La création et configuration des machines virtuelles (Routeur, Mail, Master, Backup, Noeud1, Noeud2).
- Les adresses IP pour chaque machine.
- L'installation et la configuration des services (DNS, NTP, NAT, Postfix, Keepalived, Apache).
- Les étapes pour tester le basculement avec Keepalived.
- La capture et l'analyse du trafic VRRP avec `tcpdump` et Wireshark.


--------------------
# 02 - Schèma 

Je vous propose un schéma montrant chaque machine avec son système d'exploitation, ses interfaces réseau, et les adresses IP associées :

```plaintext
                              +----------------+
                              |   Internet     |
                              +--------+-------+
                                       |
                                       |
                                (NAT Interface)
                                       |
                                       |
                               +-------+--------+
                               |                |
                               |    Routeur     |  Ubuntu Server
                               |                |
                               |----------------|
                               | eth0: NAT      |  (Internet Access)
                               | eth1: 20.21.22.254/24  (Interne)
                               | eth2: 10.11.12.254/24  (DMZ)
                               +-------+--------+
                                       |
                                       |
                -----------------------+---------------------
                |                                          |
                |                                          |
        +-------+--------+                       +---------+----------+
        |                |                       |                    |
        |     Mail       |                       |     Switch DMZ     |
        |                |                       |                    |
        |----------------|                       |--------------------|
        | eth0: 20.21.22.10/24                   | eth1: 10.11.12.254/24
        +---------------+                        +--------------------+
                |                                          |
                |                                          |
                |                    -----------------------+---------------------
                |                    |                                         |
                |                    |                                         |
        +-------+--------+    +------+------+                          +-------+--------+
        |                |    |             |                          |                |
        |     Master     |    |   Backup    |                          |     Noeud1     |
        |                |    |             |                          |                |
        |----------------|    |-------------|                          |----------------|
        | eth0: 10.11.12.1/24 | eth0: 10.11.12.2/24                    | eth0: 192.168.100.10/24
        | eth1: 192.168.100.1/24 | eth1: 192.168.100.2/24              +---------------+
        +---------------+                        +---------------+                      |
                |                                          |                           |
                |                                          |                           |
                |                                          |                           |
                |                                          |                           |
                |                                          |                           |
        +-------+--------+                          +-------+--------+           +-------+--------+
        |                |                          |                |           |                |
        |     VIP:       |                          |     Noeud2     |           | Keepalived     |
        | 10.11.12.99/24 |                          |                |           | Configuration   |
        |                |                          |                |           |                |
        |----------------|                          |----------------|           |----------------|
```

### Explications des Machines et Configurations

- **Routeur** (Ubuntu Server)
  - Interface `eth0` : NAT pour accès Internet
  - Interface `eth1` : Adresse IP `20.21.22.254/24` (Réseau Interne)
  - Interface `eth2` : Adresse IP `10.11.12.254/24` (DMZ)

- **Mail Server** (Ubuntu Server)
  - Interface `eth0` : Adresse IP `20.21.22.10/24`
  - Service : Postfix configuré pour le domaine `test.local` avec les comptes `root@test.local` et `user1@test.local`.

- **Master** (Ubuntu Server) — Machine principale pour le basculement avec Keepalived
  - Interface `eth0` : Adresse IP `10.11.12.1/24` (DMZ)
  - Interface `eth1` : Adresse IP `192.168.100.1/24` (Réseau Interne)
  - VIP (Adresse Virtuelle) : `10.11.12.99/24`
  - Service Keepalived configuré en tant que **MASTER**

- **Backup** (Ubuntu Server) — Machine de secours pour le basculement avec Keepalived
  - Interface `eth0` : Adresse IP `10.11.12.2/24` (DMZ)
  - Interface `eth1` : Adresse IP `192.168.100.2/24` (Réseau Interne)
  - VIP (Adresse Virtuelle) : `10.11.12.99/24`
  - Service Keepalived configuré en tant que **BACKUP**

- **Noeud1** (Ubuntu Server) — Serveur Web
  - Interface `eth0` : Adresse IP `192.168.100.10/24`
  - Service : Apache configuré pour servir des pages web.

- **Noeud2** (Ubuntu Server) — Serveur Web
  - Interface `eth0` : Adresse IP `192.168.100.20/24`
  - Service : Apache configuré pour servir des pages web.

### Détails Supplémentaires :

1. **Routage** : Le Routeur est configuré pour permettre la communication entre les réseaux Interne et DMZ, avec NAT activé pour que le DMZ puisse accéder à Internet.
   
2. **DNS** : Bind9 est installé sur le Routeur, configuré pour le domaine `test.local` pour résoudre les noms d’hôtes des machines.

3. **NTP** : Le Routeur est configuré comme serveur NTP pour synchroniser l'heure sur les autres machines du réseau.

4. **VIP** (Adresse Virtuelle) : `10.11.12.99/24`, partagée entre Master et Backup pour assurer la haute disponibilité.

5. **Keepalived** : Configure un basculement automatique entre Master et Backup. Un script de vérification de la mémoire est utilisé pour s'assurer que les serveurs ont suffisamment de ressources.

6. **Services Web** : Noeud1 et Noeud2 exécutent Apache pour fournir des services web en interne.

7. **Analyse Réseau** : Une capture de trafic réseau peut être réalisée sur les interfaces du Master ou du Backup pour surveiller le protocole VRRP lors d'un basculement.


--------------------
# 03 - Étapes:


### **Prérequis**
Avant de commencer, assurez-vous d'avoir :
- **VMware Workstation** ou **VirtualBox** installé pour créer les machines virtuelles.
- **ISO d'Ubuntu Server** (ou une autre distribution Linux) pour chaque machine.
- Des privilèges administratifs pour effectuer les configurations réseau et installer les services.

---

## **1. Création et Configuration des Machines Virtuelles**

### **A. Routeur**
1. Créez une VM **Routeur** avec les caractéristiques suivantes :
   - Nom de la VM : **Routeur**
   - Mémoire : 1 Go
   - CPU : 1 vCPU
   - Stockage : 10 Go
   - **3 Interfaces Réseau** :
     - **Interface 1** : NAT (pour accès à Internet)
     - **Interface 2** : Réseau Interne (20.21.22.254/24)
     - **Interface 3** : DMZ (10.11.12.254/24)

2. **Installation de l'OS et connexion SSH**
   - Démarrez la VM et installez Ubuntu Server.
   - Configurez un accès SSH pour faciliter les configurations.
   - Une fois connecté, mettez à jour la machine :
     ```bash
     sudo apt update && sudo apt upgrade -y
     ```

3. **Configurer les Interfaces Réseau**
   - Ouvrez le fichier de configuration réseau :
     ```bash
     sudo nano /etc/netplan/00-installer-config.yaml
     ```
   - Configurez les interfaces pour le Routeur :
     ```yaml
     network:
       version: 2
       ethernets:
         eth0:
           dhcp4: yes
         eth1:
           addresses: [20.21.22.254/24]
         eth2:
           addresses: [10.11.12.254/24]
     ```
   - Appliquez la configuration :
     ```bash
     sudo netplan apply
     ```

4. **Activer le Routage IP**
   - Activez le routage IP pour permettre le transfert entre réseaux :
     ```bash
     sudo sysctl -w net.ipv4.ip_forward=1
     echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
     ```

5. **Configurer le NAT avec iptables**
   - Activez le NAT pour que le réseau DMZ puisse accéder à Internet via l’interface NAT :
     ```bash
     sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
     ```
   - Pour rendre cette configuration permanente, installez `iptables-persistent` :
     ```bash
     sudo apt install iptables-persistent -y
     sudo netfilter-persistent save
     sudo netfilter-persistent reload
     ```

6. **Installation et configuration de NTP**
   - Installez le service NTP pour synchroniser l’heure sur les autres machines :
     ```bash
     sudo apt install -y ntp
     ```

7. **Installation et configuration de Bind9 (DNS)**
   - Installez Bind9 pour configurer un serveur DNS local :
     ```bash
     sudo apt install -y bind9 bind9utils bind9-doc
     ```
   - Ajoutez les zones DNS dans `/etc/bind/named.conf.local` :
     ```bash
     sudo nano /etc/bind/named.conf.local
     ```
     Ajoutez les configurations suivantes :
     ```bash
     zone "test.local" {
         type master;
         file "/etc/bind/db.test.local";
     };

     zone "22.21.20.in-addr.arpa" {
         type master;
         file "/etc/bind/db.20.21.22";
     };

     zone "12.11.10.in-addr.arpa" {
         type master;
         file "/etc/bind/db.10.11.12";
     };
     ```
   - Créez le fichier de zone `db.test.local` pour `test.local` :
     ```bash
     sudo cp /etc/bind/db.local /etc/bind/db.test.local
     sudo nano /etc/bind/db.test.local
     ```
     Configurez-le comme suit :
     ```bash
     $TTL    604800
     @       IN      SOA     test.local. root.test.local. (
                           2         ; Serial
                       604800         ; Refresh
                        86400         ; Retry
                      2419200         ; Expire
                       604800 )       ; Negative Cache TTL
     ;
     @       IN      NS      test.local.
     @       IN      A       10.11.12.254
     master  IN      A       10.11.12.1
     backup  IN      A       10.11.12.2
     mail    IN      A       20.21.22.10
     noeud1  IN      A       192.168.100.10
     noeud2  IN      A       192.168.100.20
     ```
   - Redémarrez Bind9 :
     ```bash
     sudo systemctl restart bind9
     ```

### **B. Mail Server**
1. Créez une VM pour **Mail** avec les caractéristiques suivantes :
   - Nom de la VM : **Mail**
   - Mémoire : 1 Go
   - CPU : 1 vCPU
   - Stockage : 10 Go
   - Interface réseau : Adresse statique **20.21.22.10/24**

2. **Installation et configuration de Postfix**
   - Installez Postfix :
     ```bash
     sudo apt update
     sudo apt install -y postfix
     ```
   - Lors de la configuration, choisissez `Internet Site` et définissez le nom de domaine sur `test.local`.

3. **Créer les comptes email**
   - Ajoutez deux utilisateurs pour la messagerie :
     ```bash
     sudo adduser root
     sudo adduser user1
     ```

### **C. Master et Backup (Keepalived et LVS)**
1. **Création des Machines Master et Backup**
   - Créez deux machines virtuelles, **Master** et **Backup**.
   - Pour **Master** :
     - NIC1 : 10.11.12.1/24
     - NIC2 : 192.168.100.1/24
   - Pour **Backup** :
     - NIC1 : 10.11.12.2/24
     - NIC2 : 192.168.100.2/24

2. **Installation de Keepalived**
   - Sur chaque machine (Master et Backup), installez Keepalived :
     ```bash
     sudo apt update
     sudo apt install -y keepalived
     ```

3. **Configuration de Keepalived sur Master**
   - Ouvrez le fichier de configuration de Keepalived (`/etc/keepalived/keepalived.conf`) :
     ```bash
     sudo nano /etc/keepalived/keepalived.conf
     ```
   - Configurez comme suit :
     ```bash
     vrrp_instance VI_1 {
         state MASTER
         interface eth0
         virtual_router_id 51
         priority 100
         advert_int 1
         virtual_ipaddress {
             10.11.12.99
         }
         authentication {
             auth_type PASS
             auth_pass 1111
         }
         track_script {
             chk_service
         }
         notify_email {
             user1@test.local
         }
         smtp_server mail.test.local
         smtp_connect_timeout 30
         router_id LVS_MASTER
     }

     vrrp_script chk_service {
         script "/etc/keepalived/check_memory.sh"
         interval 2
         weight 2
     }
     ```

4. **Configuration de Keepalived sur Backup**
   - Copiez la configuration de Master et remplacez `state MASTER` par `state BACKUP` et `priority 100` par `priority 90`.

5. **Script de vérification de mémoire (Healthchecker)**
   - Créez le script `/etc/keepalived/check_memory.sh` :
     ```bash
     sudo nano /etc/keepalived/check_memory.sh
     ```
   - Contenu du script :
     ```bash
     #!/bin/bash
     MEM_FREE=$(free | awk '/Mem/{print $4/$2 * 100.0}')
     if (( $(echo "$MEM_FREE > 50.0" | bc -l) )); then
         exit 0
     else
         exit 1
     fi
     ```
   - Rendez-le exécutable :
     ```bash
     sudo chmod +x /etc/keepalived/check_memory.sh
     ```

---

### **Configuration des Serveurs Web (Noeud1 et Noeud2)**
1. **Création des Machines Noeud1 et Noeud2**
   - Créez deux machines virtuelles pour Noeud1 et Noeud2.
   - **Adresse IP de Noeud1** : 192.168.100.10/24
   - **Adresse IP de Noe

ud2** : 192.168.100.20/24

2. **Installation de Apache**
   - Installez Apache sur chaque serveur :
     ```bash
     sudo apt update
     sudo apt install -y apache2
     ```
   - Créez une page web par défaut dans `/var/www/html/index.html`.

---

### **Capture de Trafic Réseau avec tcpdump**

1. **Sur le Master ou Backup**, capturez le trafic VRRP lors d'un basculement :
   ```bash
   sudo tcpdump -i eth0 -w captureVRRP.pcap
   ```

2. **Analyser avec Wireshark**
   - Téléchargez et ouvrez `captureVRRP.pcap` dans Wireshark pour analyser les paquets VRRP.


