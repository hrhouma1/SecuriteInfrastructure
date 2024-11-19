

## **1. Qu'est-ce qu'une VIP ?**

- La **VIP (Virtual IP)** est une **adresse IP virtuelle** partagée entre plusieurs machines pour offrir un service de haute disponibilité.
- Dans ce TP, la **VIP (10.11.12.99)** est utilisée sur le réseau **DMZ**.
- Si la machine **Master** tombe en panne, la **Backup** prendra le relais et continuera à répondre aux requêtes envoyées à la **VIP**.

---

## **2. Préparer l'environnement de laboratoire**

### **Machines à configurer :**
1. **Machine 1** : Master
   - **OS** : Ubuntu Server/Debian.
   - **Adresse IP** : 
     - DMZ : **10.11.12.1/24** (NIC1).
   - **VIP** : **10.11.12.99/24**.

2. **Machine 2** : Backup
   - **OS** : Ubuntu Server/Debian.
   - **Adresse IP** : 
     - DMZ : **10.11.12.2/24** (NIC1).
   - **VIP** : **10.11.12.99/24** (activée uniquement en cas de panne du Master).

---

### **Configuration réseau dans VMware :**
1. **Configurer les cartes réseau :**
   - Master :
     - NIC1 → **VMnet2 (DMZ)**.
   - Backup :
     - NIC1 → **VMnet2 (DMZ)**.

2. **Attribution des adresses IP statiques :**
   Configurez les adresses IP des machines **Master** et **Backup** comme suit.

- **Master** :
  ```bash
  sudo nano /etc/netplan/01-netcfg.yaml
  ```
  Contenu :
  ```yaml
  network:
    version: 2
    ethernets:
      enp0s3: # DMZ
        addresses:
          - 10.11.12.1/24
  ```
  Appliquez :
  ```bash
  sudo netplan apply
  ```

- **Backup** :
  ```bash
  sudo nano /etc/netplan/01-netcfg.yaml
  ```
  Contenu :
  ```yaml
  network:
    version: 2
    ethernets:
      enp0s3: # DMZ
        addresses:
          - 10.11.12.2/24
  ```
  Appliquez :
  ```bash
  sudo netplan apply
  ```

---

## **3. Installer Keepalived**

1. **Installer Keepalived** sur les deux machines :
   ```bash
   sudo apt update
   sudo apt install keepalived -y
   ```

2. **Vérifiez que Keepalived est installé** :
   ```bash
   keepalived --version
   ```

---

## **4. Configurer la VIP sur le Master**

1. **Fichier de configuration pour le Master** :
   Ouvrez le fichier `/etc/keepalived/keepalived.conf` :
   ```bash
   sudo nano /etc/keepalived/keepalived.conf
   ```

2. Ajoutez la configuration suivante :
   ```text
   vrrp_instance VI_1 {
       state MASTER                     # Déclare cette machine comme MASTER
       interface enp0s3                 # Interface du réseau DMZ
       virtual_router_id 50             # ID unique pour le VRRP (identique pour Master et Backup)
       priority 100                     # Priorité plus élevée que le Backup
       advert_int 1                     # Intervalle d’annonce (1 seconde)
       authentication {
           auth_type PASS               # Authentification par mot de passe
           auth_pass 1234               # Mot de passe partagé
       }
       virtual_ipaddress {
           10.11.12.99/24               # Adresse IP virtuelle (VIP)
       }
   }
   ```

3. **Redémarrez Keepalived** :
   ```bash
   sudo systemctl restart keepalived
   ```

4. **Vérifiez que la VIP est active** :
   ```bash
   ip addr show enp0s3
   ```
   Résultat attendu : L'adresse **10.11.12.99** doit apparaître sur l'interface **enp0s3**.

---

## **5. Configurer la VIP sur le Backup**

1. **Fichier de configuration pour le Backup** :
   Ouvrez le fichier `/etc/keepalived/keepalived.conf` :
   ```bash
   sudo nano /etc/keepalived/keepalived.conf
   ```

2. Ajoutez la configuration suivante :
   ```text
   vrrp_instance VI_1 {
       state BACKUP                     # Déclare cette machine comme BACKUP
       interface enp0s3                 # Interface du réseau DMZ
       virtual_router_id 50             # ID unique pour le VRRP (identique pour Master et Backup)
       priority 90                      # Priorité plus basse que le Master
       advert_int 1                     # Intervalle d’annonce (1 seconde)
       authentication {
           auth_type PASS               # Authentification par mot de passe
           auth_pass 1234               # Mot de passe partagé
       }
       virtual_ipaddress {
           10.11.12.99/24               # Adresse IP virtuelle (VIP)
       }
   }
   ```

3. **Redémarrez Keepalived** :
   ```bash
   sudo systemctl restart keepalived
   ```

4. **Vérifiez que la VIP est inactive (attente)** :
   - La commande suivante ne doit pas afficher **10.11.12.99** tant que le Master est actif :
     ```bash
     ip addr show enp0s3
     ```

---

## **6. Tester la VIP**

1. **Vérifiez que la VIP est active sur le Master** :
   ```bash
   ip addr show enp0s3
   ```
   Résultat attendu : **10.11.12.99** est attribuée au Master.

2. **Simulez une panne du Master** :
   - Arrêtez Keepalived sur le Master :
     ```bash
     sudo systemctl stop keepalived
     ```

3. **Vérifiez que la VIP a basculé sur le Backup** :
   - Sur le Backup :
     ```bash
     ip addr show enp0s3
     ```
   Résultat attendu : **10.11.12.99** est maintenant attribuée au Backup.

4. **Relancez Keepalived sur le Master** :
   - Sur le Master :
     ```bash
     sudo systemctl start keepalived
     ```

5. **Vérifiez que la VIP revient sur le Master** :
   - Sur le Master :
     ```bash
     ip addr show enp0s3
     ```

---

## **7. Résumé des étapes importantes**

1. **La VIP (10.11.12.99)** est une adresse IP virtuelle qui bascule automatiquement entre le Master et le Backup.
2. **Keepalived** utilise le protocole VRRP pour gérer la VIP.
3. **La priorité détermine l'ordre des machines** :
   - Master : Priorité 100.
   - Backup : Priorité 90.
4. En cas de panne du Master, la VIP est transférée au Backup.
5. Les services connectés à la VIP continuent de fonctionner sans interruption.
