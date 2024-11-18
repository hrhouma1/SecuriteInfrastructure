

## **1. Utiliser VirtualBox pour la virtualisation**
Téléchargez **VirtualBox** depuis son site officiel (gratuit) et installez-le. VirtualBox permet de simuler des réseaux en connectant plusieurs machines virtuelles.

---

### **2. Configurer les cartes réseau dans VirtualBox**
Chaque machine a des **interfaces réseau (NIC)**. Voici comment les configurer :

- Ouvrez VirtualBox, cliquez sur une machine (par exemple, Machine 1 - Routeur), puis allez dans **Paramètres > Réseau**.

### **Schéma global des interfaces réseau**
```
Machine 1 : Routeur
 - NIC1 : NAT (pour accès Internet)
 - NIC2 : Réseau interne "Interne" (20.21.22.0/24)
 - NIC3 : Réseau interne "DMZ" (10.11.12.0/24)

Machine 2 : Serveur Mail
 - NIC : Réseau interne "Interne" (20.21.22.0/24)

Machine 3 : Master
 - NIC1 : Réseau interne "DMZ" (10.11.12.0/24)
 - NIC2 : Réseau interne "Backend" (192.168.100.0/24)
 - NIC3 : Réseau interne "Sync" (172.168.1.0/30)

Machine 4 : Backup
 - NIC1 : Réseau interne "DMZ" (10.11.12.0/24)
 - NIC2 : Réseau interne "Backend" (192.168.100.0/24)
 - NIC3 : Réseau interne "Sync" (172.168.1.0/30)

Machine 5 : Noeud1
 - NIC : Réseau interne "Backend" (192.168.100.0/24)

Machine 6 : Noeud2
 - NIC : Réseau interne "Backend" (192.168.100.0/24)
```

---

### **Étape 1 : Créer les réseaux internes dans VirtualBox**
1. Allez dans **Fichier > Gestionnaire de réseaux hôtes** dans VirtualBox.
2. Cliquez sur **Créer** pour chaque réseau interne utilisé dans le TP :
   - **"Interne"** : pour le réseau interne (20.21.22.0/24).
   - **"DMZ"** : pour le réseau DMZ (10.11.12.0/24).
   - **"Backend"** : pour le réseau des serveurs backend (192.168.100.0/24).
   - **"Sync"** : pour la synchronisation entre Master et Backup (172.168.1.0/30).

---

### **Étape 2 : Connecter les cartes réseau des machines**
1. **Machine 1 : Routeur**
   - NIC1 : **Mode NAT** (accès à Internet via votre hôte).
   - NIC2 : Réseau attaché à **Interne**.
   - NIC3 : Réseau attaché à **DMZ**.

2. **Machine 2 : Serveur Mail**
   - NIC : Réseau attaché à **Interne**.

3. **Machine 3 : Master**
   - NIC1 : Réseau attaché à **DMZ**.
   - NIC2 : Réseau attaché à **Backend**.
   - NIC3 : Réseau attaché à **Sync**.

4. **Machine 4 : Backup**
   - NIC1 : Réseau attaché à **DMZ**.
   - NIC2 : Réseau attaché à **Backend**.
   - NIC3 : Réseau attaché à **Sync**.

5. **Machine 5 : Noeud1**
   - NIC : Réseau attaché à **Backend**.

6. **Machine 6 : Noeud2**
   - NIC : Réseau attaché à **Backend**.

---

### **3. Configurer les IP statiques pour chaque machine**
#### **Machine 1 : Routeur**
- Commande pour configurer les IP statiques :
  ```bash
  sudo nano /etc/netplan/01-netcfg.yaml
  ```
- Exemple de configuration :
  ```yaml
  network:
    version: 2
    renderer: networkd
    ethernets:
      enp0s3: # NIC1 (NAT)
        dhcp4: true
      enp0s8: # NIC2 (Interne)
        addresses:
          - 20.21.22.254/24
      enp0s9: # NIC3 (DMZ)
        addresses:
          - 10.11.12.254/24
  ```
- Appliquer la configuration :
  ```bash
  sudo netplan apply
  ```

#### **Machine 2 : Serveur Mail**
- IP : `20.21.22.10/24` (Interne)
- Commande :
  ```bash
  sudo nano /etc/netplan/01-netcfg.yaml
  ```
- Exemple de configuration :
  ```yaml
  network:
    version: 2
    renderer: networkd
    ethernets:
      enp0s8:
        addresses:
          - 20.21.22.10/24
        gateway4: 20.21.22.254
        nameservers:
          addresses:
            - 20.21.22.254
  ```
- Appliquer :
  ```bash
  sudo netplan apply
  ```

#### **Machine 3 : Master**
- NIC1 : `10.11.12.1/24` (DMZ)
- NIC2 : `192.168.100.1/24` (Backend)
- NIC3 : `172.168.1.1/30` (Sync)
- Commande :
  ```bash
  sudo nano /etc/netplan/01-netcfg.yaml
  ```
- Exemple :
  ```yaml
  network:
    version: 2
    renderer: networkd
    ethernets:
      enp0s8:
        addresses:
          - 10.11.12.1/24
      enp0s9:
        addresses:
          - 192.168.100.1/24
      enp0s10:
        addresses:
          - 172.168.1.1/30
  ```
- Appliquer :
  ```bash
  sudo netplan apply
  ```

---

### **4. Vérifier la connectivité réseau**
1. **Depuis Machine 2 (Serveur Mail)** :
   - Testez la connexion au Routeur (20.21.22.254) :
     ```bash
     ping 20.21.22.254
     ```

2. **Depuis Machine 5 (Noeud1)** :
   - Testez la connexion au Master (192.168.100.1) :
     ```bash
     ping 192.168.100.1
     ```

3. **Depuis Machine 3 (Master)** :
   - Testez la connexion au Backup (172.168.1.2) :
     ```bash
     ping 172.168.1.2
     ```

---

### **5. Configurer le routage sur Machine 1 (Routeur)**
Pour permettre la communication entre les réseaux DMZ, Backend et Interne :
1. Activez le routage IP :
   - Éditez `/etc/sysctl.conf` :
     ```bash
     sudo nano /etc/sysctl.conf
     ```
   - Décommentez :
     ```text
     net.ipv4.ip_forward=1
     ```
   - Appliquez :
     ```bash
     sudo sysctl -p
     ```

2. Ajoutez des règles de routage avec **iptables** :
   - Commandes :
     ```bash
     sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
     sudo iptables -A FORWARD -i enp0s8 -o enp0s9 -j ACCEPT
     sudo iptables -A FORWARD -i enp0s9 -o enp0s8 -j ACCEPT
     ```


