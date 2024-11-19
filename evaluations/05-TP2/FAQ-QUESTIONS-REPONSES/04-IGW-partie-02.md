Le **VGW** (Virtual Gateway) est une **passerelle virtuelle**. Elle est utilisée dans des configurations de haute disponibilité pour offrir une **adresse IP stable et partagée** entre plusieurs machines (ou serveurs) afin de gérer le trafic réseau de manière redondante.

Dans le contexte de votre infrastructure avec **Keepalived**, le **VGW** permet de garantir que les clients ou les services connectés continuent de communiquer sans interruption, même si l'un des serveurs principaux tombe en panne.

---

### **Explication du VGW (Virtual Gateway) dans ce TP**

#### **1. Rôle du VGW :**
Le **VGW** est utilisé comme **passerelle par défaut** pour les machines du réseau backend (**Noeud1 et Noeud2**) :
- Les machines **Noeud1 (192.168.100.10)** et **Noeud2 (192.168.100.20)** sont configurées pour utiliser le **VGW (192.168.100.99)** comme passerelle.
- Si le serveur **Master** tombe en panne, le serveur **Backup** prendra automatiquement le rôle et conservera l’adresse IP **192.168.100.99**, grâce au protocole VRRP (Virtual Router Redundancy Protocol).

#### **2. Adresse IP du VGW :**
- **VGW (192.168.100.99/24)** est une adresse IP **virtuelle** partagée entre les serveurs **Master** et **Backup**.
- Seul le serveur actif (généralement le Master) répond aux requêtes envoyées à cette adresse.
- Si une bascule (failover) se produit, l’adresse IP est immédiatement transférée au serveur Backup, qui devient actif.

---

### **Comment fonctionne le VGW avec Keepalived ?**

1. **Protocole VRRP :**
   Keepalived utilise le protocole VRRP pour gérer la redondance. Le VRRP attribue une **adresse IP virtuelle (VIP)**, dans ce cas le **VGW (192.168.100.99)**, qui bascule automatiquement entre le Master et le Backup.

2. **Configuration des machines backend :**
   Les machines backend (**Noeud1** et **Noeud2**) sont configurées pour utiliser **192.168.100.99** comme passerelle. Elles n’ont pas besoin de savoir si le serveur actif est Master ou Backup.

3. **État des serveurs :**
   - **Master actif** : Répond aux requêtes pour le VGW (192.168.100.99).
   - **Backup passif** : Surveille l’état du Master et attend qu’il tombe en panne pour prendre la main.

---

### **Configuration typique du VGW avec Keepalived**

#### **Fichier de configuration sur le Master (`/etc/keepalived/keepalived.conf`) :**
```text
vrrp_instance VI_1 {
    state MASTER                    # Le Master est actif
    interface eth1                  # Interface associée au VGW (Backend)
    virtual_router_id 51            # Identifiant VRRP unique
    priority 100                    # Priorité du Master (supérieure au Backup)
    advert_int 1                    # Intervalle d’annonce (en secondes)
    authentication {
        auth_type PASS              # Authentification par mot de passe
        auth_pass 1234              # Mot de passe partagé
    }
    virtual_ipaddress {
        192.168.100.99              # Adresse IP virtuelle (VGW)
    }
}
```

#### **Fichier de configuration sur le Backup (`/etc/keepalived/keepalived.conf`) :**
```text
vrrp_instance VI_1 {
    state BACKUP                    # Le Backup est passif
    interface eth1                  # Interface associée au VGW (Backend)
    virtual_router_id 51            # Identifiant VRRP unique (identique au Master)
    priority 90                     # Priorité inférieure au Master
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1234
    }
    virtual_ipaddress {
        192.168.100.99              # Adresse IP virtuelle (VGW)
    }
}
```

---

### **Pourquoi utiliser un VGW ?**

1. **Haute disponibilité :**
   - Si le Master tombe en panne, le Backup prend automatiquement le contrôle sans nécessiter de modifications sur les machines backend.

2. **Simplification de la configuration des clients :**
   - Les machines backend (**Noeud1, Noeud2**) configurent **192.168.100.99** comme passerelle par défaut. Elles ne se soucient pas de savoir quel serveur est actif.

3. **Fiabilité et redondance :**
   - Le VGW garantit un point d’accès réseau stable même en cas de panne.

---

### **Tester le VGW**

1. Configurez **Noeud1** et **Noeud2** pour utiliser **192.168.100.99** comme passerelle :
   ```bash
   sudo nano /etc/netplan/01-netcfg.yaml
   ```
   Exemple :
   ```yaml
   network:
     version: 2
     ethernets:
       enp0s3:
         addresses:
           - 192.168.100.10/24  # Pour Noeud1
         gateway4: 192.168.100.99
   ```

2. Testez la connectivité depuis **Noeud1** :
   ```bash
   ping 8.8.8.8
   ```

3. **Simulez une panne du Master** :
   - Sur le Master, arrêtez Keepalived :
     ```bash
     sudo systemctl stop keepalived
     ```

4. Vérifiez que le Backup a pris le contrôle :
   - Sur le Backup, exécutez :
     ```bash
     ip addr show eth1
     ```
   - Vous devriez voir **192.168.100.99** attribuée à l’interface **eth1**.

---

### **Résumé**

- **VGW (Virtual Gateway)** est une adresse IP virtuelle partagée entre plusieurs serveurs pour assurer la redondance et la haute disponibilité.
- Dans ce TP, le VGW est configuré comme passerelle par défaut pour les machines backend (**Noeud1 et Noeud2**).
- Le VGW est géré par **Keepalived** et bascule automatiquement entre le Master et le Backup en cas de panne.

