
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
