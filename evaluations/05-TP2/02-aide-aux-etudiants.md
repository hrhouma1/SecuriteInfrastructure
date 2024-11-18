
# 01 - Poropostion de travail


###### Schéma global **mis à jour** avec les **systèmes d'exploitation (OS)** pour chaque machine

---

```
                                      +----------------+
                                      |     Internet    |
                                      +----------------+
                                              |
                                              |
                                        +-------------+
                                        |   Routeur    |
                                        |  Machine 1   |
                                        +-------------+
                                  OS : Ubuntu Server/Debian
                                  NIC1 (NAT): Internet
                                  NIC2: 20.21.22.254/24
                                  NIC3: 10.11.12.254/24
                                              |
                   +--------------------------+--------------------------+
                   |                                                     |
        +---------------------+                               +---------------------+
        |     Serveur Mail     |                               |         DMZ         |
        |      Machine 2       |                               +---------------------+
        +---------------------+                                           |
         OS : Ubuntu Server/Debian                                       |
         NIC: 20.21.22.10/24                                            +---------------------+
                                                                         |     Machine 3       |
                                                                         |       Master        |
                                                                         |---------------------|
                                                                         | NIC1: 10.11.12.1    |
                                                                         | NIC2: 192.168.100.1 |
                                                                         | NIC3: 172.168.1.1   |
                                                                         | VIP: 10.11.12.99    |
                                                                         | OS : Ubuntu Server  |
                                                                         +---------------------+
                                                                                 |
                                                                         +---------------------+
                                                                         |     Machine 4       |
                                                                         |       Backup        |
                                                                         |---------------------|
                                                                         | NIC1: 10.11.12.2    |
                                                                         | NIC2: 192.168.100.2 |
                                                                         | NIC3: 172.168.1.2   |
                                                                         | VGW: 192.168.100.99 |
                                                                         | OS : Ubuntu Server  |
                                                                         +---------------------+
                                              +--------------------------+--------------------------+
                                              |                                                     |
                                    +---------------------+                               +---------------------+
                                    |     Machine 5       |                               |     Machine 6       |
                                    |       Noeud1        |                               |       Noeud2        |
                                    |---------------------|                               |---------------------|
                                    | NIC1: 192.168.100.10|                               | NIC1: 192.168.100.20|
                                    | Gateway: 192.168.100.99 |                           | Gateway: 192.168.100.99 |
                                    | OS : Ubuntu Server      |                           | OS : Ubuntu Server      |
                                    +---------------------+                               +---------------------+
```

---

### **Explications des systèmes d'exploitation (OS) :**

1. **Machine 1 : Routeur**
   - **OS** : Ubuntu Server/Debian.
   - Raison : Les services DNS, NTP, et le routage fonctionnent de manière optimale sur Linux.

2. **Machine 2 : Serveur Mail**
   - **OS** : Ubuntu Server/Debian.
   - Raison : Postfix est conçu pour fonctionner sous Linux et offre une configuration simple et robuste.

3. **Machine 3 : Master (Directeur principal)**
   - **OS** : Ubuntu Server/Debian.
   - Raison : Keepalived fonctionne principalement sur Linux pour la gestion de haute disponibilité.

4. **Machine 4 : Backup (Directeur secondaire)**
   - **OS** : Ubuntu Server/Debian.
   - Raison : Identique au Master, Backup nécessite un environnement Linux pour Keepalived.

5. **Machine 5 : Noeud1**
   - **OS** : Ubuntu Server/Debian.
   - Raison : Les serveurs web comme Apache ou Nginx sont mieux pris en charge sous Linux.

6. **Machine 6 : Noeud2**
   - **OS** : Ubuntu Server/Debian.
   - Raison : Identique à Noeud1, le serveur web fonctionne sous Linux.

---

### **Instructions pour configurer l'OS :**
- **Téléchargez Ubuntu Server (version 20.04 ou supérieure)** depuis le site officiel.
- **Installer chaque machine** en suivant le schéma ci-dessus.
- Durant l'installation :
  1. Assurez-vous que **chaque machine utilise une IP statique** dans son réseau respectif.
  2. Configurez le partitionnement et les utilisateurs lors de l'installation.

---

### **Résumé :**
- **Toutes les machines** utilisent **Ubuntu Server ou Debian**, sauf le **client final**, qui peut être Windows.
- **Pourquoi Linux ?** Parce qu’il est gratuit, robuste et offre une flexibilité optimale pour les services réseaux comme le DNS, le routage, le mail, le web, et la haute disponibilité.

















# 02 - Implémantation
## **1. Machine 1 : Routeur**
- **Système d'exploitation : Ubuntu Server/Debian**
- **Rôles : DNS, NTP, routage**
- **Interfaces réseau :**
  - **NIC1 (Internet)** : Configuré avec NAT.
  - **NIC2 (Interne)** : 20.21.22.254/24.
  - **NIC3 (DMZ)** : 10.11.12.254/24.

### **Schéma  :**
```
                  +----------------+
                  |  Machine 1     |
                  |   Routeur      |
                  +----------------+
                   NIC1 (NAT) -> Internet
                   NIC2 -> 20.21.22.254/24 (Réseau Interne)
                   NIC3 -> 10.11.12.254/24 (DMZ)
```

### **Instructions :**
1. **Installer le système d’exploitation (Ubuntu Server ou Debian).**
   - Téléchargez l'image ISO depuis le site officiel.
   - Lors de l'installation :
     - Configurez une IP statique pour chaque interface réseau.
     - NIC1 : Configuré sur NAT (automatique via VirtualBox/VMware).
     - NIC2 : Configurez manuellement (IP 20.21.22.254/24).
     - NIC3 : Configurez manuellement (IP 10.11.12.254/24).

2. **Activer le routage entre les réseaux (interne et DMZ).**
   - Ouvrez le fichier `/etc/sysctl.conf` :
     ```bash
     sudo nano /etc/sysctl.conf
     ```
   - Décommentez la ligne suivante :
     ```
     net.ipv4.ip_forward=1
     ```
   - Appliquez les modifications :
     ```bash
     sudo sysctl -p
     ```

3. **Configurer le DNS avec Bind9.**
   - Installez Bind9 :
     ```bash
     sudo apt update
     sudo apt install bind9
     ```
   - Configurez la zone DNS `test.local` :
     - Modifiez `/etc/bind/named.conf.local` :
       ```text
       zone "test.local" {
           type master;
           file "/etc/bind/db.test.local";
       };

       zone "22.21.20.in-addr.arpa" {
           type master;
           file "/etc/bind/db.22.21.20";
       };

       zone "12.11.10.in-addr.arpa" {
           type master;
           file "/etc/bind/db.12.11.10";
       };
       ```
   - Créez le fichier `/etc/bind/db.test.local` :
     ```bash
     sudo nano /etc/bind/db.test.local
     ```
     - Contenu du fichier :
       ```text
       $TTL    604800
       @       IN      SOA     test.local. root.test.local. (
                             2         ; Serial
                             604800    ; Refresh
                             86400     ; Retry
                             2419200   ; Expire
                             604800 )  ; Negative Cache TTL
       ;
       @       IN      NS      test.local.
       @       IN      A       10.11.12.254
       router  IN      A       10.11.12.254
       mail    IN      A       20.21.22.10
       master  IN      A       10.11.12.1
       backup  IN      A       10.11.12.2
       noeud1  IN      A       192.168.100.10
       noeud2  IN      A       192.168.100.20
       ```
   - Relancez le service :
     ```bash
     sudo systemctl restart bind9
     ```

4. **Configurer le NTP.**
   - Installez NTP :
     ```bash
     sudo apt install ntp
     ```
   - Modifiez `/etc/ntp.conf` pour permettre la synchronisation des autres machines :
     ```text
     restrict 20.21.22.0 mask 255.255.255.0 nomodify notrap
     restrict 10.11.12.0 mask 255.255.255.0 nomodify notrap
     ```

---

## **2. Machine 2 : Serveur Mail**
- **Système d'exploitation : Ubuntu Server/Debian**
- **Rôles : Serveur Mail avec Postfix**
- **Adresse IP : 20.21.22.10/24**

### **Schéma ASCII :**
```
                  +----------------+
                  |  Machine 2     |
                  | Serveur Mail   |
                  +----------------+
                   NIC1 -> 20.21.22.10/24 (Réseau Interne)
```

### **Instructions :**
1. **Installer Postfix.**
   ```bash
   sudo apt update
   sudo apt install postfix
   ```

2. **Configurer Postfix pour `test.local`.**
   - Ouvrez `/etc/postfix/main.cf` :
     ```bash
     sudo nano /etc/postfix/main.cf
     ```
   - Ajoutez/Modifiez les lignes suivantes :
     ```text
     myhostname = mail.test.local
     mydomain = test.local
     myorigin = /etc/mailname
     mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
     ```

3. **Créer les utilisateurs `root` et `user1`.**
   ```bash
   sudo adduser root
   sudo adduser user1
   ```

---

## **3. Machines 3 et 4 : Master et Backup**
- **Système d'exploitation : Ubuntu Server/Debian**
- **Rôles : Haute disponibilité avec Keepalived**
- **Adresses IP :**
  - Master :
    - NIC1 : 10.11.12.1/24
    - NIC2 : 192.168.100.1/24
    - NIC3 : 172.168.1.1/30
  - Backup :
    - NIC1 : 10.11.12.2/24
    - NIC2 : 192.168.100.2/24
    - NIC3 : 172.168.1.2/30

### **Schéma ASCII :**
```
          +----------------+    +----------------+
          |  Machine 3     |    |  Machine 4     |
          |    Master      |    |    Backup      |
          +----------------+    +----------------+
           NIC1 -> 10.11.12.1    NIC1 -> 10.11.12.2
           NIC2 -> 192.168.100.1 NIC2 -> 192.168.100.2
           NIC3 -> 172.168.1.1   NIC3 -> 172.168.1.2
           VIP : 10.11.12.99     VGW : 192.168.100.99
```

### **Instructions :**
1. **Installer Keepalived.**
   ```bash
   sudo apt install keepalived
   ```

2. **Configurer Keepalived sur Master (`/etc/keepalived/keepalived.conf`).**
   ```text
   vrrp_instance VI_1 {
       state MASTER
       interface eth0
       virtual_router_id 51
       priority 100
       advert_int 1
       authentication {
           auth_type PASS
           auth_pass 1234
       }
       virtual_ipaddress {
           10.11.12.99
       }
   }
   ```

3. **Configurer Keepalived sur Backup (changez `state` en `BACKUP`).**

---

## **4. Machines 5 et 6 : Noeud1 et Noeud2**
- **Système d'exploitation : Ubuntu Server/Debian**
- **Rôles : Serveurs Web**
- **Adresses IP :**
  - Noeud1 : 192.168.100.10/24
  - Noeud2 : 192.168.100.20/24

### **Schéma ASCII :**
```
          +----------------+    +----------------+
          |  Machine 5     |    |  Machine 6     |
          |   Noeud1       |    |   Noeud2       |
          +----------------+    +----------------+
           NIC1 -> 192.168.100.10 NIC1 -> 192.168.100.20
           Gateway : 192.168.100.99
```

### **Instructions :**
1. **Installer Apache.**
   ```bash
   sudo apt update
   sudo apt install apache2
   ```

2. **Configurer un site web simple.**
   - Modifiez `/var/www/html/index.html` :
     ```html
     <h1>Bienvenue sur Noeud1 (ou Noeud2)</h1>
     ```
