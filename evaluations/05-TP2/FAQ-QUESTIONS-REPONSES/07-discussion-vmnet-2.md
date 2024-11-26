

**Question : Configuration des réseaux pour la haute disponibilité avec Keepalived**

Dans une infrastructure virtuelle utilisant VMware, vous devez configurer plusieurs machines connectées à différents réseaux pour assurer la haute disponibilité avec **Keepalived**, en utilisant une **VIP (Virtual IP)** et une **VGW (Virtual Gateway)**.

### **Objectifs :**
1. Configurer **4 réseaux virtuels distincts (VMnet)** pour simuler les différentes zones réseau (Interne, DMZ, Backend, et Synchronisation).
2. Vérifier que les machines connectées à chaque réseau peuvent communiquer entre elles.
3. Garantir que la VIP (10.11.12.99) et le VGW (192.168.100.99) fonctionnent correctement avec Keepalived.

---

### **Contexte de l'architecture :**
- **Machines et réseaux concernés** :
  - **Réseau Interne (20.21.22.0/24)** : Pour le DNS et le serveur mail.
  - **Réseau DMZ (10.11.12.0/24)** : Pour la VIP gérée par Keepalived.
  - **Réseau Backend (192.168.100.0/24)** : Pour la VGW gérée par Keepalived.
  - **Réseau Sync (172.168.1.0/30)** : Pour la synchronisation entre le Master et le Backup.
- **Nombre total de VMnet** : 4.
- **Configuration réseau** :
  - Les adresses IP doivent être définies statiquement.
  - Chaque VMnet est configuré en **Host-Only**, sauf si un accès externe est nécessaire.

---

### **Questions :**

1. **Création des VMnet :**
   - Quels types de VMnet configurez-vous pour chaque réseau ? Justifiez le choix de **Host-Only** pour ce TP.
   - Si vous devez permettre un accès externe (par exemple, pour la DMZ), quel type de VMnet conviendrait ?

2. **Connectivité entre les machines :**
   - Expliquez comment les machines (par exemple, Master et Backup) peuvent communiquer entre elles dans un réseau **Host-Only**.
   - Décrivez les tests que vous effectuerez pour valider la connectivité et le fonctionnement des VIP et VGW.

3. **Alternative à Host-Only :**
   - Dans quel cas envisageriez-vous de configurer un réseau en **Bridged** ?
   - Quels seraient les impacts d’un réseau Bridged sur la sécurité et la gestion des adresses IP dans ce TP ?

4. **VRRP et Keepalived :**
   - Pourquoi le protocole VRRP fonctionne-t-il correctement dans un réseau Host-Only ?
   - Si le VGW (192.168.100.99) ou la VIP (10.11.12.99) ne répond pas, quels outils utiliseriez-vous pour diagnostiquer les problèmes ?

----------------------
# Réponse : 
Pour clarifier et confirmer : 

Avec une configuration **Host-Only**, les machines **dans un même VMnet** pourront communiquer entre elles sans problème, ce qui est essentiel pour les VIP et VGW dans ce TP. 
- Voici pourquoi **Host-Only est suffisant pour ce TP avec Keepalived**, et pourquoi les machines pourront se connecter correctement.


### **Comment fonctionne Host-Only pour ce TP ?**

1. **Connectivité dans un même VMnet :**
   - Les machines qui partagent le même VMnet (par exemple, VMnet2 pour la DMZ ou VMnet3 pour le Backend) pourront se pinguer et échanger des paquets sans problème.
   - **Keepalived** utilise la communication VRRP (Virtual Router Redundancy Protocol) pour gérer les VIP (10.11.12.99) et VGW (192.168.100.99). Cette communication se fait **localement** sur le réseau où sont les machines connectées.

2. **Host-Only et isolation réseau :**
   - Chaque VMnet est isolé des autres réseaux (Internet ou réseaux physiques). Cela garantit qu'aucun trafic externe ne perturbe les communications entre vos machines virtuelles.
   - Dans ce TP, chaque réseau a un rôle précis :
     - DMZ (VMnet2) : Pour la VIP.
     - Backend (VMnet3) : Pour le VGW.
     - Interne (VMnet1) : Pour le DNS et le mail.
     - Sync (VMnet4) : Pour la communication privée entre Master et Backup.

3. **Accès Internet pour NAT :**
   - Si vous avez besoin d'accès Internet pour certains tests, seule la machine **Routeur** aura une interface configurée en NAT pour partager cette connexion.

---

### **Pourquoi cela fonctionne pour Keepalived avec VRRP ?**

**Keepalived (VIP et VGW)** repose sur des communications **locales** entre les machines du même réseau. Voici comment cela fonctionne dans votre TP :

1. **VIP (10.11.12.99) dans la DMZ (VMnet2)** :
   - Le Master (10.11.12.1) et le Backup (10.11.12.2) sont connectés au même réseau **VMnet2 (DMZ)**.
   - Keepalived envoie des paquets VRRP sur ce réseau pour annoncer quel serveur gère la VIP.
   - Les paquets VRRP sont transmis en multicast, et Host-Only permet cela **dans le même VMnet**.

2. **VGW (192.168.100.99) dans le Backend (VMnet3)** :
   - Le Master (192.168.100.1), le Backup (192.168.100.2), et les Noeuds (192.168.100.10, 192.168.100.20) sont tous connectés au **Backend (VMnet3)**.
   - Les Noeuds utilisent la passerelle par défaut (VGW : 192.168.100.99).
   - Keepalived gère la bascule de VGW entre le Master et le Backup via le même principe VRRP.

3. **Host-Only prend en charge VRRP** :
   - Host-Only autorise la transmission de paquets multicast VRRP (générés par Keepalived) entre les machines connectées au même VMnet.

---

### **Tester les connexions entre les machines**

Après avoir configuré les **VMnet et les adresses IP** :

1. **Vérifiez la connectivité entre les machines d'un même réseau :**
   - Depuis **Master** (10.11.12.1), pingez **Backup** (10.11.12.2) dans le DMZ :
     ```bash
     ping 10.11.12.2
     ```
   - Depuis **Master** (192.168.100.1), pingez **Noeud1** (192.168.100.10) dans le Backend :
     ```bash
     ping 192.168.100.10
     ```

2. **Testez VRRP avec tcpdump** :
   - Sur le Master, capturez les paquets VRRP envoyés par Keepalived :
     ```bash
     sudo tcpdump -i enp0s3 vrrp
     ```
   - Vous devriez voir des annonces VRRP sur l’interface **enp0s3** (DMZ).

---

### **Cas où Host-Only ne suffit pas**

Si vous voulez que les machines d’un réseau puissent **interagir avec des machines extérieures** (par exemple, exposer la DMZ au réseau physique de votre hôte), **Host-Only ne suffit pas.**

#### Dans ce cas, vous pouvez :
1. Passer **VMnet2 (DMZ)** ou **VMnet3 (Backend)** en **Bridged** :
   - Cela permettra au réseau DMZ ou Backend de communiquer avec le réseau physique de l’hôte.
2. Configurer un **pont réseau** (Bridged) pour la machine Routeur :
   - Cela permettra au Routeur de jouer le rôle de passerelle pour l’ensemble des réseaux internes.

---

### **Confirmation :**

| Réseau              | VMnet   | Type de VMnet | Fonctionnement avec Host-Only                    |
|---------------------|---------|---------------|--------------------------------------------------|
| **Réseau Interne**  | VMnet1  | Host-Only     | Connecte Routeur et Serveur Mail, DNS opérationnel.|
| **Réseau DMZ**      | VMnet2  | Host-Only     | VRRP (VIP : 10.11.12.99) entre Master et Backup.  |
| **Réseau Backend**  | VMnet3  | Host-Only     | VRRP (VGW : 192.168.100.99) et connecte Noeuds.   |
| **Réseau Sync**     | VMnet4  | Host-Only     | Synchronisation privée entre Master et Backup.   |

---

### **Conclusion**

- Les machines communiquent entre elles dans un **Host-Only** tant qu'elles partagent le même VMnet.
- Cette configuration est suffisante pour un laboratoire avec **Keepalived**, car tout le trafic reste local et isolé.
- Si vous devez exposer un réseau à l’extérieur ou interagir avec un réseau physique, utilisez **Bridged** pour ce segment précis.

