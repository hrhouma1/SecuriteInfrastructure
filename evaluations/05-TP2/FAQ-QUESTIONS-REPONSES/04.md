# Configure IGW

## **1. Préparer l'environnement de laboratoire**

### **Description des machines pour la configuration :**
- **Machine 1** : Master (Directeur Principal)
  - OS : Ubuntu Server/Debian.
  - NIC1 : DMZ (10.11.12.1/24).
  - NIC2 : Backend (192.168.100.1/24).
  - NIC3 : Synchronisation (172.168.1.1/30).

- **Machine 2** : Backup (Directeur Secondaire)
  - OS : Ubuntu Server/Debian.
  - NIC1 : DMZ (10.11.12.2/24).
  - NIC2 : Backend (192.168.100.2/24).
  - NIC3 : Synchronisation (172.168.1.2/30).

- **VGW (Virtual Gateway)** :
  - Adresse IP virtuelle : **192.168.100.99** (Backend).
  - Cette IP sera partagée entre le Master et le Backup.

---

### **Configuration dans VMware :**
1. **Assignez les cartes réseau :**
   - Master :
     - NIC1 (DMZ) → VMnet2.
     - NIC2 (Backend) → VMnet3.
     - NIC3 (Sync) → VMnet4.
   - Backup :
     - NIC1 (DMZ) → VMnet2.
     - NIC2 (Backend) → VMnet3.
     - NIC3 (Sync) → VMnet4.

2. **Attribution d'adresses IP statiques dans chaque machine :**
   - Master :
     ```bash
     sudo nano /etc/netplan/01-netcfg.yaml
     ```
     Contenu pour le Master :
     ```yaml
     network:
       version: 2
       ethernets:
         enp0s3: # DMZ
           addresses:
             - 10.11.12.1/24
         enp0s8: # Backend
           addresses:
             - 192.168.100.1/24
         enp0s9: # Sync
           addresses:
             - 172.168.1.1/30
     ```
     - Appliquez :
       ```bash
       sudo netplan apply
       ```

   - Backup :
     ```bash
     sudo nano /etc/netplan/01-netcfg.yaml
     ```
     Contenu pour le Backup :
     ```yaml
     network:
       version: 2
       ethernets:
         enp0s3: # DMZ
           addresses:
             - 10.11.12.2/24
         enp0s8: # Backend
           addresses:
             - 192.168.100.2/24
         enp0s9: # Sync
           addresses:
             - 172.168.1.2/30
     ```
     - Appliquez :
       ```bash
       sudo netplan apply
       ```

---

## **2. Installer Keepalived**

1. Installez **Keepalived** sur les deux machines (Master et Backup) :
   ```bash
   sudo apt update
   sudo apt install keepalived -y
   ```

2. Vérifiez que Keepalived est installé :
   ```bash
   keepalived --version
   ```

---

## **3. Configurer Keepalived pour le Master**

1. **Fichier de configuration : `/etc/keepalived/keepalived.conf`**
   Ouvrez le fichier :
   ```bash
   sudo nano /etc/keepalived/keepalived.conf
   ```

   Ajoutez la configuration suivante :
   ```text
   vrrp_instance VI_1 {
       state MASTER                     # Déclare cette machine comme MASTER
       interface enp0s8                 # Interface du réseau Backend
       virtual_router_id 51             # ID unique pour le VGW (identique sur Master et Backup)
       priority 100                     # Priorité élevée pour le Master
       advert_int 1                     # Intervalle d’annonce (1 seconde)
       authentication {
           auth_type PASS               # Authentification par mot de passe
           auth_pass 1234               # Mot de passe partagé
       }
       virtual_ipaddress {
           192.168.100.99               # Adresse IP virtuelle (VGW)
       }
   }
   ```

2. Redémarrez Keepalived :
   ```bash
   sudo systemctl restart keepalived
   ```

3. Vérifiez que le VGW est actif sur le Master :
   ```bash
   ip addr show enp0s8
   ```
   Vous devriez voir **192.168.100.99** attribuée à l’interface `enp0s8`.

---

## **4. Configurer Keepalived pour le Backup**

1. Ouvrez le fichier de configuration sur le Backup :
   ```bash
   sudo nano /etc/keepalived/keepalived.conf
   ```

2. Ajoutez la configuration suivante :
   ```text
   vrrp_instance VI_1 {
       state BACKUP                     # Déclare cette machine comme BACKUP
       interface enp0s8                 # Interface du réseau Backend
       virtual_router_id 51             # ID unique pour le VGW (identique au Master)
       priority 90                      # Priorité plus basse que le Master
       advert_int 1                     # Intervalle d’annonce (1 seconde)
       authentication {
           auth_type PASS               # Authentification par mot de passe
           auth_pass 1234               # Mot de passe partagé
       }
       virtual_ipaddress {
           192.168.100.99               # Adresse IP virtuelle (VGW)
       }
   }
   ```

3. Redémarrez Keepalived :
   ```bash
   sudo systemctl restart keepalived
   ```

---

## **5. Configurer les clients Backend (Noeud1 et Noeud2)**

1. Sur chaque machine backend (Noeud1 et Noeud2), configurez **192.168.100.99** comme passerelle par défaut.
   Exemple sur Noeud1 :
   ```bash
   sudo nano /etc/netplan/01-netcfg.yaml
   ```

   Contenu pour Noeud1 :
   ```yaml
   network:
     version: 2
     ethernets:
       enp0s3:
         addresses:
           - 192.168.100.10/24
         gateway4: 192.168.100.99       # Passerelle par défaut (VGW)
   ```

2. Appliquez la configuration :
   ```bash
   sudo netplan apply
   ```

3. Testez la connectivité :
   - Depuis Noeud1, essayez de pinger une adresse Internet :
     ```bash
     ping 8.8.8.8
     ```

---

## **6. Tester le basculement (failover)**

1. **Vérifiez que le Master est actif** :
   - Sur le Master :
     ```bash
     ip addr show enp0s8
     ```
     Résultat attendu : **192.168.100.99** est attribuée.

2. **Simulez une panne du Master** :
   - Arrêtez Keepalived sur le Master :
     ```bash
     sudo systemctl stop keepalived
     ```

3. **Vérifiez que le Backup a pris le contrôle** :
   - Sur le Backup :
     ```bash
     ip addr show enp0s8
     ```
     Résultat attendu : **192.168.100.99** est maintenant attribuée au Backup.

4. **Relancez Keepalived sur le Master** :
   - Sur le Master :
     ```bash
     sudo systemctl start keepalived
     ```

---

## **Résumé**

1. **Le VGW (Virtual Gateway)** est une adresse IP virtuelle partagée entre le Master et le Backup.
2. **Keepalived** gère le VGW à l’aide du protocole VRRP pour assurer la redondance.
3. **Les clients backend (Noeud1 et Noeud2)** utilisent le VGW comme passerelle par défaut.
4. **Le failover** garantit une continuité de service en cas de panne du Master.

