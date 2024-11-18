
## **1. Préparer VMware Workstation**

1. **Télécharger et installer VMware Workstation** (si ce n’est pas déjà fait).
2. **Créer les VLANs (réseaux virtuels)** :
   - Ouvrez VMware Workstation.
   - Allez dans **Edit > Virtual Network Editor**.
   - Créez 4 réseaux pour les différents segments VLAN :
     - **VMnet1 (Interne)** : 20.21.22.0/24
     - **VMnet2 (DMZ)** : 10.11.12.0/24
     - **VMnet3 (Backend)** : 192.168.100.0/24
     - **VMnet4 (Sync)** : 172.168.1.0/30
   - **Configuration pour chaque VMnet** :
     - Décochez **Use local DHCP service**.
     - Assignez manuellement les plages d’adresses :
       - VMnet1 : 20.21.22.0/24
       - VMnet2 : 10.11.12.0/24
       - VMnet3 : 192.168.100.0/24
       - VMnet4 : 172.168.1.0/30

---

## **2. Configuration des cartes réseau pour chaque machine**

### **Machine 1 : Routeur**
- **OS** : Ubuntu Server/Debian.
- **Cartes réseau** :
  - **NIC1** : **NAT** (pour l’accès Internet).
  - **NIC2** : Connecté à **VMnet1 (Interne)**.
  - **NIC3** : Connecté à **VMnet2 (DMZ)**.
- **IP Configuration** :
  - NIC1 (NAT) : Automatique (attribuée par VMware NAT).
  - NIC2 (Interne) : **20.21.22.254/24**.
  - NIC3 (DMZ) : **10.11.12.254/24**.

---

### **Machine 2 : Serveur Mail**
- **OS** : Ubuntu Server/Debian.
- **Cartes réseau** :
  - **NIC** : Connecté à **VMnet1 (Interne)**.
- **IP Configuration** :
  - NIC : **20.21.22.10/24**.
  - Passerelle : **20.21.22.254** (Routeur).

---

### **Machine 3 : Master**
- **OS** : Ubuntu Server/Debian.
- **Cartes réseau** :
  - **NIC1** : Connecté à **VMnet2 (DMZ)**.
  - **NIC2** : Connecté à **VMnet3 (Backend)**.
  - **NIC3** : Connecté à **VMnet4 (Sync)**.
- **IP Configuration** :
  - NIC1 (DMZ) : **10.11.12.1/24**.
  - NIC2 (Backend) : **192.168.100.1/24**.
  - NIC3 (Sync) : **172.168.1.1/30**.
  - VIP : **10.11.12.99/24**.

---

### **Machine 4 : Backup**
- **OS** : Ubuntu Server/Debian.
- **Cartes réseau** :
  - **NIC1** : Connecté à **VMnet2 (DMZ)**.
  - **NIC2** : Connecté à **VMnet3 (Backend)**.
  - **NIC3** : Connecté à **VMnet4 (Sync)**.
- **IP Configuration** :
  - NIC1 (DMZ) : **10.11.12.2/24**.
  - NIC2 (Backend) : **192.168.100.2/24**.
  - NIC3 (Sync) : **172.168.1.2/30**.
  - Passerelle virtuelle (VGW) : **192.168.100.99/24**.

---

### **Machine 5 : Noeud1**
- **OS** : Ubuntu Server/Debian.
- **Cartes réseau** :
  - **NIC** : Connecté à **VMnet3 (Backend)**.
- **IP Configuration** :
  - NIC : **192.168.100.10/24**.
  - Passerelle : **192.168.100.99**.

---

### **Machine 6 : Noeud2**
- **OS** : Ubuntu Server/Debian.
- **Cartes réseau** :
  - **NIC** : Connecté à **VMnet3 (Backend)**.
- **IP Configuration** :
  - NIC : **192.168.100.20/24**.
  - Passerelle : **192.168.100.99**.

---

## **3. Configuration réseau (IP statique)**

Pour chaque machine (après installation de l'OS), configurez les IP manuellement :

1. **Éditez les fichiers Netplan** (Ubuntu Server/Debian) :
   ```bash
   sudo nano /etc/netplan/01-netcfg.yaml
   ```
2. **Exemple pour une machine (Machine 1 - Routeur)** :
   ```yaml
   network:
     version: 2
     renderer: networkd
     ethernets:
       enp0s3: # NIC1 - NAT
         dhcp4: true
       enp0s8: # NIC2 - Interne
         addresses:
           - 20.21.22.254/24
       enp0s9: # NIC3 - DMZ
         addresses:
           - 10.11.12.254/24
   ```
3. **Appliquez la configuration** :
   ```bash
   sudo netplan apply
   ```

---

## **4. Configuration du routage (sur Machine 1 - Routeur)**

1. Activez le routage IP :
   - Éditez `/etc/sysctl.conf` :
     ```bash
     sudo nano /etc/sysctl.conf
     ```
   - Décommentez :
     ```text
     net.ipv4.ip_forward=1
     ```
   - Appliquez :
     ```bash
     sudo sysctl -p
     ```

2. Configurez NAT pour que le réseau DMZ accède à Internet :
   ```bash
   sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
   sudo iptables -A FORWARD -i enp0s8 -o enp0s9 -j ACCEPT
   sudo iptables -A FORWARD -i enp0s9 -o enp0s8 -j ACCEPT
   ```

---

## **5. Vérification des connexions**

1. Depuis le **Serveur Mail (Machine 2)**, testez la connectivité avec le Routeur :
   ```bash
   ping 20.21.22.254
   ```

2. Depuis le **Master (Machine 3)**, testez la connexion au Backup :
   ```bash
   ping 172.168.1.2
   ```

3. Depuis le **Noeud1 (Machine 5)**, testez la connexion au Master :
   ```bash
   ping 192.168.100.1
   ```

---

## **6. Capturer le trafic entre Master et Backup (VRRP)**

1. Installez `tcpdump` sur le Master :
   ```bash
   sudo apt install tcpdump
   ```
2. Lancez une capture VRRP :
   ```bash
   sudo tcpdump -i enp0s8 vrrp -w captureVRRP.pcap
   ```
3. Transférez le fichier `.pcap` vers votre machine locale et ouvrez-le avec **Wireshark** pour analyse.




-------------
-------------
-------------

# Annexe 01 -  **Configuration avec VLANs dans VMware Workstation**

VMware Workstation ne gère pas directement les VLANs comme VMware ESXi. Cependant, tu peux utiliser des VLANs **virtuels** dans ton architecture en suivant ces étapes :

---

### **Étape 1 : Installer un Switch virtuel VLAN (logiciel)**
VMware Workstation ne gère pas directement les VLANs, mais tu peux :
1. **Ajouter une machine virtuelle dédiée** pour jouer le rôle d’un **Switch VLAN**.
2. Installe un système comme **pfsense**, **Open vSwitch**, ou tout autre routeur logiciel compatible VLAN.

Si ce n’est pas possible, tu peux simuler les VLANs en utilisant plusieurs réseaux distincts avec les adaptateurs VMnet comme expliqué précédemment.

---

### **Étape 2 : Attribuer des VLANs via une interface VMnet**
Tu peux simuler un réseau VLAN dans VMware Workstation en suivant cette approche :
- Chaque **VMnet (VMnet1, VMnet2, etc.)** sera associé à un VLAN logique.
- Configure chaque **VMnet** pour représenter un VLAN spécifique.

### **Table des VLANs :**
```
+---------+-------------------+---------------------+
| Réseau  | VLAN              | VMnet              |
+---------+-------------------+---------------------+
| Interne | VLAN 10           | VMnet1             |
| DMZ     | VLAN 20           | VMnet2             |
| Backend | VLAN 30           | VMnet3             |
| Sync    | VLAN 40           | VMnet4             |
+---------+-------------------+---------------------+
```

---

### **Étape 3 : Configurer les adaptateurs réseau sur chaque machine**

Dans VMware Workstation, pour chaque machine :
1. Ouvre les **Paramètres de la machine virtuelle**.
2. Accède à l’onglet **Network Adapter**.
3. Configure l’adaptateur comme suit :
   - **VMnet1** : VLAN 10 (Interne).
   - **VMnet2** : VLAN 20 (DMZ).
   - **VMnet3** : VLAN 30 (Backend).
   - **VMnet4** : VLAN 40 (Sync).

Pour chaque machine, connecte les cartes réseau au bon réseau **VMnet**.

---

### **Étape 4 : Configurer les VLANs sur un routeur (Machine 1 : Routeur)**

Pour permettre la communication entre les VLANs :
1. Configure un routeur ou un logiciel (comme pfsense ou Open vSwitch).
2. Active le routage inter-VLAN :
   - Attribue des sous-réseaux aux VLANs.
   - Active **IPv4 forwarding** pour permettre aux réseaux de communiquer.

Exemple de configuration pour une machine Linux (Ubuntu/Debian) jouant le rôle de routeur :
```bash
sudo nano /etc/network/interfaces
```
Ajoute les VLANs :
```bash
auto enp0s3.10
iface enp0s3.10 inet static
  address 20.21.22.254
  netmask 255.255.255.0

auto enp0s3.20
iface enp0s3.20 inet static
  address 10.11.12.254
  netmask 255.255.255.0

auto enp0s3.30
iface enp0s3.30 inet static
  address 192.168.100.1
  netmask 255.255.255.0
```

---

### **Étape 5 : Configurer un Switch avec VLAN Tags**

Si tu utilises une solution comme **Open vSwitch** ou un Switch physique :
- Configure chaque port pour un **VLAN spécifique**.
- Exemple :
  - **Port 1** : VLAN 10 (Interne).
  - **Port 2** : VLAN 20 (DMZ).
  - **Port 3** : VLAN 30 (Backend).

---

### **Étape 6 : Vérification**

Une fois que tout est configuré :
1. Vérifie que les machines dans le même VLAN peuvent se pinger (ex : Noeud1 et Noeud2 dans VLAN 30).
2. Vérifie que les machines entre VLANs communiquent via le routeur.

---

### Pourquoi ne pas utiliser uniquement Host-Only ?
- **Host-Only** isole les réseaux, mais n’offre pas de gestion fine comme les VLANs (tags 802.1Q, interconnexion, etc.).
- Avec les VLANs, tu peux mieux **segmenter et contrôler le trafic réseau**, notamment dans des configurations complexes.







-------------
-------------
-------------

# Annexe 02 -  *table complète** détaillant les configurations des machines, les systèmes d’exploitation (**OS**), les interfaces réseau, les types de cartes réseau, et les VLAN associés.

---

```
+--------------+------------------+-----------------------------+-------------------+-------------------+--------+
| Machine      | OS               | Carte Réseau               | Type de Carte     | Adresse IP        | VLAN   |
+--------------+------------------+-----------------------------+-------------------+-------------------+--------+
| Routeur      | Ubuntu Server    | NIC1 (NAT)                 | NAT               | Dynamique (DHCP)  | N/A    |
| (Machine 1)  | ou Debian        | NIC2 (VMnet1 - Interne)     | VLAN 10 (Host-Only)| 20.21.22.254/24   | 10     |
|              |                  | NIC3 (VMnet2 - DMZ)         | VLAN 20 (Host-Only)| 10.11.12.254/24   | 20     |
|              |                  | NIC4 (VMnet3 - Backend)     | VLAN 30 (Host-Only)| 192.168.100.254/24| 30     |
+--------------+------------------+-----------------------------+-------------------+-------------------+--------+
| Serveur Mail | Ubuntu Server    | NIC (VMnet1 - Interne)      | VLAN 10 (Host-Only)| 20.21.22.10/24    | 10     |
| (Machine 2)  | ou Debian        |                             |                   |                   |        |
+--------------+------------------+-----------------------------+-------------------+-------------------+--------+
| Master       | Ubuntu Server    | NIC1 (VMnet2 - DMZ)         | VLAN 20 (Host-Only)| 10.11.12.1/24     | 20     |
| (Machine 3)  | ou Debian        | NIC2 (VMnet3 - Backend)     | VLAN 30 (Host-Only)| 192.168.100.1/24  | 30     |
|              |                  | NIC3 (VMnet4 - Sync)        | VLAN 40 (Host-Only)| 172.168.1.1/30    | 40     |
|              |                  | VIP                        | Virtual IP         | 10.11.12.99/24    | N/A    |
+--------------+------------------+-----------------------------+-------------------+-------------------+--------+
| Backup       | Ubuntu Server    | NIC1 (VMnet2 - DMZ)         | VLAN 20 (Host-Only)| 10.11.12.2/24     | 20     |
| (Machine 4)  | ou Debian        | NIC2 (VMnet3 - Backend)     | VLAN 30 (Host-Only)| 192.168.100.2/24  | 30     |
|              |                  | NIC3 (VMnet4 - Sync)        | VLAN 40 (Host-Only)| 172.168.1.2/30    | 40     |
|              |                  | VGW                        | Virtual Gateway    | 192.168.100.99/24 | N/A    |
+--------------+------------------+-----------------------------+-------------------+-------------------+--------+
| Noeud1       | Ubuntu Server    | NIC (VMnet3 - Backend)      | VLAN 30 (Host-Only)| 192.168.100.10/24 | 30     |
| (Machine 5)  | ou Debian        |                             |                   |                   |        |
+--------------+------------------+-----------------------------+-------------------+-------------------+--------+
| Noeud2       | Ubuntu Server    | NIC (VMnet3 - Backend)      | VLAN 30 (Host-Only)| 192.168.100.20/24 | 30     |
| (Machine 6)  | ou Debian        |                             |                   |                   |        |
+--------------+------------------+-----------------------------+-------------------+-------------------+--------+
```

---

### **Légende des colonnes**
1. **Machine** :
   - Nom et rôle de chaque machine dans l'architecture (Routeur, Serveur Mail, Master, etc.).

2. **OS** :
   - Système d'exploitation installé sur chaque machine.
   - Toutes les machines utilisent **Ubuntu Server** ou **Debian** pour leur flexibilité.

3. **Carte Réseau** :
   - Interface réseau utilisée sur chaque machine (NIC1, NIC2, etc.).
   - Correspond à un réseau logique ou VLAN.

4. **Type de Carte** :
   - Type de connexion réseau configurée dans VMware Workstation.
     - **NAT** : Permet à la machine d'accéder à Internet via l’hôte.
     - **Host-Only** : Isolé dans un réseau local simulé (simulant un VLAN).
     - **Virtual IP** : Adresse IP virtuelle utilisée pour la redondance.

5. **Adresse IP** :
   - Adresse IP assignée statiquement à chaque interface réseau.

6. **VLAN** :
   - VLAN logique associé à chaque réseau.

---

### **Configurer chaque machine dans VMware**

#### **Routeur (Machine 1)**
1. **Configurer 4 cartes réseau** :
   - NIC1 : NAT (Internet).
   - NIC2 : VMnet1 (Interne).
   - NIC3 : VMnet2 (DMZ).
   - NIC4 : VMnet3 (Backend).

2. **Configurer les VLANs sur le routeur** :
   - Configure les sous-réseaux et active le routage entre les VLANs.

---

#### **Serveur Mail (Machine 2)**
1. **Configurer 1 carte réseau** :
   - NIC1 : VMnet1 (Interne).
2. **Assigner l’adresse IP** :
   - **20.21.22.10/24** avec la passerelle **20.21.22.254**.

---

#### **Master (Machine 3)**
1. **Configurer 3 cartes réseau** :
   - NIC1 : VMnet2 (DMZ).
   - NIC2 : VMnet3 (Backend).
   - NIC3 : VMnet4 (Sync).

2. **Assigner les IPs** :
   - DMZ : **10.11.12.1/24**.
   - Backend : **192.168.100.1/24**.
   - Sync : **172.168.1.1/30**.

3. Configurez Keepalived pour utiliser une adresse **VIP (10.11.12.99)**.

---

#### **Backup (Machine 4)**
1. **Configurer 3 cartes réseau** :
   - NIC1 : VMnet2 (DMZ).
   - NIC2 : VMnet3 (Backend).
   - NIC3 : VMnet4 (Sync).

2. **Assigner les IPs** :
   - DMZ : **10.11.12.2/24**.
   - Backend : **192.168.100.2/24**.
   - Sync : **172.168.1.2/30**.

3. Configurez Keepalived pour utiliser une **passerelle virtuelle (VGW)** : **192.168.100.99**.

---

#### **Noeud1 (Machine 5)**
1. **Configurer 1 carte réseau** :
   - NIC1 : VMnet3 (Backend).

2. **Assigner l’adresse IP** :
   - **192.168.100.10/24** avec la passerelle **192.168.100.99**.

---

#### **Noeud2 (Machine 6)**
1. **Configurer 1 carte réseau** :
   - NIC1 : VMnet3 (Backend).

2. **Assigner l’adresse IP** :
   - **192.168.100.20/24** avec la passerelle **192.168.100.99**.

---

### **Configurer le routage inter-VLAN (Routeur - Machine 1)**
Activez le routage inter-VLAN avec les étapes suivantes :
1. Éditez `/etc/netplan/01-netcfg.yaml` pour inclure les VLANs :
   ```yaml
   network:
     version: 2
     renderer: networkd
     ethernets:
       enp0s8:
         addresses:
           - 20.21.22.254/24
       enp0s9:
         addresses:
           - 10.11.12.254/24
       enp0s10:
         addresses:
           - 192.168.100.254/24
   ```
2. Activez **IPv4 Forwarding** :
   ```bash
   sudo nano /etc/sysctl.conf
   ```
   Décommentez :
   ```
   net.ipv4.ip_forward=1
   ```
   Appliquez :
   ```bash
   sudo sysctl -p
   ```

---

### **Tester la configuration**
1. Depuis chaque machine, **ping** la passerelle correspondante (ex. 20.21.22.254, 10.11.12.254).
2. Testez la communication entre VLANs (par exemple, de Noeud1 à Master).
