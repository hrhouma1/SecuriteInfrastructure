- L'objectif de cette architecture est d'assurer la haute disponibilité avec **Keepalived**
- Vous configurez des **VIP (Virtual IP)** et un **VGW (Virtual Gateway)** pour assurer la haute disponibilité avec **Keepalived**
- Discutons les détails sur les **VMnet nécessaires** dans VMware. 
- Chaque VMnet représente un réseau distinct ou un VLAN, selon notre configuration.

---

# 01 - **Nombre de VMnet requis**
Pour une architecture robuste, vous avez besoin de **4 VMnet distincts** :

1. **VMnet1 : Réseau Interne**
   - Utilisé pour connecter :
     - Machine 1 (Routeur) sur NIC2 : **20.21.22.254/24**.
     - Machine 2 (Mail Server) sur NIC1 : **20.21.22.10/24**.

2. **VMnet2 : Réseau DMZ**
   - Utilisé pour connecter :
     - Machine 1 (Routeur) sur NIC3 : **10.11.12.254/24**.
     - Machine 3 (Master) sur NIC1 : **10.11.12.1/24**.
     - Machine 4 (Backup) sur NIC1 : **10.11.12.2/24**.
     - VIP : **10.11.12.99/24** (géré par Keepalived).

3. **VMnet3 : Réseau Backend**
   - Utilisé pour connecter :
     - Machine 3 (Master) sur NIC2 : **192.168.100.1/24**.
     - Machine 4 (Backup) sur NIC2 : **192.168.100.2/24**.
     - Machine 5 (Noeud1) sur NIC1 : **192.168.100.10/24**.
     - Machine 6 (Noeud2) sur NIC1 : **192.168.100.20/24**.
     - VGW : **192.168.100.99/24** (géré par Keepalived).

4. **VMnet4 : Réseau de Synchronisation**
   - Utilisé pour connecter :
     - Machine 3 (Master) sur NIC3 : **172.168.1.1/30**.
     - Machine 4 (Backup) sur NIC3 : **172.168.1.2/30**.
   - Ce réseau est dédié à la synchronisation entre Master et Backup pour les services de haute disponibilité.

---

# 02 - **Configuration des VMnet dans VMware Workstation**

1. **Ouvrir le gestionnaire de réseau VMware :**
   - Allez dans **Edit > Virtual Network Editor**.

2. **Créer les 4 VMnet :**
   - Cliquez sur **Add Network** et configurez les réseaux comme suit :
     - **VMnet1 (Interne)** : Host-Only.
     - **VMnet2 (DMZ)** : Host-Only.
     - **VMnet3 (Backend)** : Host-Only.
     - **VMnet4 (Synchronisation)** : Host-Only.

3. **Désactiver le DHCP sur tous les VMnet :**
   - Décochez **Use local DHCP service** pour chaque VMnet, car les IP sont configurées statiquement.

---

# 03 - **Attribution des VMnet aux Machines**

### **Machine 1 : Routeur**
- **NIC1** : NAT (Accès Internet).
- **NIC2** : VMnet1 (Interne - 20.21.22.254/24).
- **NIC3** : VMnet2 (DMZ - 10.11.12.254/24).

### **Machine 2 : Mail Server**
- **NIC1** : VMnet1 (Interne - 20.21.22.10/24).

### **Machine 3 : Master**
- **NIC1** : VMnet2 (DMZ - 10.11.12.1/24).
- **NIC2** : VMnet3 (Backend - 192.168.100.1/24).
- **NIC3** : VMnet4 (Synchronisation - 172.168.1.1/30).

### **Machine 4 : Backup**
- **NIC1** : VMnet2 (DMZ - 10.11.12.2/24).
- **NIC2** : VMnet3 (Backend - 192.168.100.2/24).
- **NIC3** : VMnet4 (Synchronisation - 172.168.1.2/30).

### **Machine 5 : Noeud1**
- **NIC1** : VMnet3 (Backend - 192.168.100.10/24).

### **Machine 6 : Noeud2**
- **NIC1** : VMnet3 (Backend - 192.168.100.20/24).

---

# 04 -  **Configuration des IP Statique sur Chaque Machine**

Configurez les adresses IP pour chaque machine selon les réseaux attribués :

### Exemple pour Machine 1 (Routeur) :
1. **Éditez le fichier de configuration réseau** :
   ```bash
   sudo nano /etc/netplan/01-netcfg.yaml
   ```

2. **Contenu** :
   ```yaml
   network:
     version: 2
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

# 05 - **Pourquoi 4 VMnet sont nécessaires ?**

1. **Isolation des réseaux** :
   - Chaque VMnet représente un réseau logique séparé, garantissant l’isolation du trafic entre Interne, DMZ, Backend, et Synchronisation.

2. **Scalabilité** :
   - Si vous ajoutez des serveurs ou des fonctionnalités à l’avenir, chaque réseau a déjà une configuration dédiée.

3. **Respect de la topologie réseau** :
   - La séparation des réseaux garantit que chaque rôle (Routeur, Mail Server, Master, Backup, etc.) fonctionne comme prévu.



# 06 - Annexe : 

### **Les Types de VMnet Disponibles dans VMware**

1. **NAT (Network Address Translation)** :
   - Le réseau est connecté à l’Internet via la machine hôte (l’ordinateur physique qui exécute VMware).
   - Les machines virtuelles utilisent l'adresse IP privée attribuée par VMware.
   - Avantage : Accès à Internet.
   - Inconvénient : Les autres machines ne peuvent pas accéder directement aux VM via NAT.

2. **Bridged** :
   - Les machines virtuelles partagent la même interface réseau physique que l’hôte et obtiennent une adresse IP sur le réseau local.
   - Avantage : Les machines virtuelles peuvent communiquer avec des machines physiques et d'autres VM sur le réseau.
   - Inconvénient : Nécessite un réseau local physique configuré.

3. **Host-Only** :
   - Le réseau est isolé et les machines virtuelles ne peuvent communiquer qu'entre elles et avec l’hôte.
   - Avantage : Très sécurisé, idéal pour des laboratoires isolés.
   - Inconvénient : Pas d’accès à Internet, sauf si NAT est combiné.

4. **Custom VMnet** :
   - Réseaux créés dans l’éditeur de réseau virtuel VMware (Virtual Network Editor).
   - Avantage : Permet une gestion avancée des VLANs et de la connectivité.

---

### **Choix des VMnet pour ce TP**

Voici une analyse réseau pour chaque segment de votre architecture :

1. **Réseau Interne (20.21.22.0/24)** :
   - **But** : Permettre la communication entre le **Routeur** et le **Serveur Mail**.
   - **Type de VMnet recommandé** : **Host-Only**.
     - **Pourquoi ?**
       - C’est un réseau interne isolé qui n’a pas besoin d’interagir avec Internet directement.
       - La sécurité est renforcée car seules les machines connectées au réseau peuvent communiquer.

2. **Réseau DMZ (10.11.12.0/24)** :
   - **But** : Permettre l’accès aux services exposés, comme la VIP utilisée pour la DMZ.
   - **Type de VMnet recommandé** : **Bridged** ou **Host-Only** (selon vos besoins).
     - **Pourquoi Bridged ?**
       - Si vous voulez tester une DMZ qui interagit avec un réseau physique ou externe.
     - **Pourquoi Host-Only ?**
       - Si votre TP est strictement en laboratoire et vous n’avez pas besoin que les services soient accessibles en dehors des VM.
     - **Recommandation ici :** **Host-Only** est suffisant pour un laboratoire.

3. **Réseau Backend (192.168.100.0/24)** :
   - **But** : Connecter les serveurs réels (**Noeud1**, **Noeud2**) avec les directeurs (**Master**, **Backup**) via le **VGW**.
   - **Type de VMnet recommandé** : **Host-Only**.
     - **Pourquoi ?**
       - Ce réseau est interne, sécurisé et dédié uniquement à la communication entre ces machines.
       - Il n’a pas besoin d’accéder à Internet ni à un réseau physique.

4. **Réseau de Synchronisation (172.168.1.0/30)** :
   - **But** : Permettre la synchronisation privée entre le **Master** et le **Backup**.
   - **Type de VMnet recommandé** : **Host-Only**.
     - **Pourquoi ?**
       - Ce réseau est critique et doit être isolé des autres segments pour éviter tout problème de sécurité ou d’interférence.
       - La communication est uniquement entre deux machines.

---

### **Conclusion : Pourquoi utiliser principalement Host-Only ?**

- **Isolation des réseaux :** Chaque segment est indépendant, ce qui simplifie la gestion des sous-réseaux et garantit une sécurité accrue.
- **Reproductibilité :** En laboratoire, utiliser **Host-Only** vous garantit que l’architecture reste fonctionnelle quelle que soit la configuration réseau physique de l’hôte.
- **Pas d’accès à Internet nécessaire :** Les réseaux DMZ, Backend et Sync n’ont pas besoin d’accéder à Internet directement.
- **Simplicité pour les débutants :** Les étudiants n’ont pas à gérer des configurations externes complexes comme des VLANs physiques ou des routeurs physiques.

---

### **Pourquoi ne pas tout configurer en Bridged ?**

1. Le réseau Bridged nécessite un réseau physique configuré correctement, avec suffisamment d’adresses IP disponibles. Si l’hôte change de réseau (Wi-Fi, Ethernet), les VM peuvent perdre leur connectivité.
2. Cela peut poser des problèmes de sécurité en exposant les machines virtuelles directement au réseau externe.
3. Pour un TP en laboratoire, Bridged n’est pas nécessaire sauf pour des cas spécifiques (comme simuler une vraie DMZ avec accès au réseau physique).

---

### **Recommandation Finale pour Votre TP**

| Réseau              | Adresse         | Type de VMnet | Justification                              |
|---------------------|-----------------|---------------|-------------------------------------------|
| **Réseau Interne**  | 20.21.22.0/24  | Host-Only     | Communication sécurisée Routeur ↔ Mail.    |
| **Réseau DMZ**      | 10.11.12.0/24  | Host-Only     | VIP isolée pour DMZ. (Bridged si externe). |
| **Réseau Backend**  | 192.168.100.0/24 | Host-Only     | VGW pour les Noeuds et Directeurs.         |
| **Réseau Sync**     | 172.168.1.0/30 | Host-Only     | Synchronisation privée Master ↔ Backup.    |

- **Nombre total de VMnet : 4.**
- **NAT sur le Routeur :** Permet l’accès Internet uniquement via le Routeur pour tester certaines fonctionnalités.



# 07 - Annexe 02 - **Pourquoi Host-Only est idéal pour ce TP ?**

1. **Isolement des réseaux :**
   - Chaque réseau (Interne, DMZ, Backend, Sync) est isolé grâce à **Host-Only**.
   - Cela garantit qu’aucun trafic non sollicité (provenant de l’hôte ou d’Internet) ne perturbera vos tests, ce qui est essentiel pour des environnements de laboratoire.

2. **Facilité d'implémentation :**
   - Avec Host-Only, vous n’avez pas besoin de configurer un VLAN physique ou de dépendre de la configuration réseau de votre hôte.
   - Tout se passe dans un environnement contrôlé.

3. **Pas de dépendance au réseau externe :**
   - Si vous utilisez **Bridged**, vous dépendez du réseau physique de votre hôte (Wi-Fi, Ethernet, etc.), ce qui peut poser problème si l’hôte change de réseau ou si le réseau n’est pas correctement configuré.

4. **Sécurité :**
   - Host-Only isole complètement vos machines virtuelles du réseau externe, sauf si vous configurez explicitement NAT sur le Routeur.

5. **Accès Internet limité via NAT :**
   - La seule machine ayant un accès Internet est le **Routeur** (via NAT), qui partage la connexion Internet avec le réseau DMZ si nécessaire.

---

### **Pourquoi 4 VMnet ?**

1. **Réseau Interne (VMnet1)** :
   - Concerne uniquement **Machine 1 (Routeur)** et **Machine 2 (Mail)**.
   - Isolé pour les services internes, comme le DNS ou le serveur de mail.

2. **Réseau DMZ (VMnet2)** :
   - Concerne **Machine 1 (Routeur)**, **Machine 3 (Master)**, **Machine 4 (Backup)**.
   - Héberge la **VIP (10.11.12.99)**, partagée entre Master et Backup.

3. **Réseau Backend (VMnet3)** :
   - Concerne **Machine 3 (Master)**, **Machine 4 (Backup)**, **Machine 5 (Noeud1)** et **Machine 6 (Noeud2)**.
   - Héberge la **VGW (192.168.100.99)**, qui agit comme passerelle pour les Noeuds.

4. **Réseau de Synchronisation (VMnet4)** :
   - Concerne uniquement **Machine 3 (Master)** et **Machine 4 (Backup)**.
   - Permet la synchronisation privée entre les deux directeurs.

---

### **Scénarios où Host-Only est Insuffisant**

1. **Si vous voulez simuler un réseau DMZ réel avec des connexions externes** :
   - Vous pouvez passer le **Réseau DMZ (VMnet2)** en **Bridged**, ce qui permettra à la DMZ d’être accessible depuis le réseau physique (LAN de votre bureau ou université).

2. **Si le Backend doit se connecter à d'autres réseaux physiques ou virtuels** :
   - Dans ce cas, passez le **Backend (VMnet3)** en Bridged ou NAT, selon les besoins.

---

### **Que faire si vous avez des doutes ?**

1. **Tester avec Host-Only dans un premier temps :**
   - Configurez toutes les VM selon ce guide.
   - Vérifiez que :
     - Les machines communiquent correctement au sein de chaque réseau (ping entre les machines d’un même VMnet).
     - Les VIP et VGW basculent correctement entre Master et Backup.
     - Les Noeuds Backend utilisent la VGW comme passerelle par défaut.

2. **Si vous constatez un problème de connectivité externe ou de besoin d’interaction avec le réseau physique** :
   - Passez **VMnet2 (DMZ)** ou **VMnet3 (Backend)** en **Bridged** pour tester un accès externe.

---

### **Confirmation du choix :**

| Réseau              | VMnet   | Type       | Raison                                                                 |
|---------------------|---------|------------|------------------------------------------------------------------------|
| **Réseau Interne**  | VMnet1  | Host-Only  | Isolé pour Routeur et Mail Server.                                     |
| **Réseau DMZ**      | VMnet2  | Host-Only  | VIP isolée pour DMZ (sécurisé). Bridged si interaction externe requise.|
| **Réseau Backend**  | VMnet3  | Host-Only  | VGW isolé pour les Noeuds et Directeurs.                               |
| **Réseau Sync**     | VMnet4  | Host-Only  | Synchronisation privée Master ↔ Backup.                                |

---

### **Résumé Final**

1. **Nombre de VMnet : 4.**
   - VMnet1 : Réseau Interne.
   - VMnet2 : Réseau DMZ.
   - VMnet3 : Réseau Backend.
   - VMnet4 : Réseau Synchronisation.

2. **Pourquoi Host-Only ?**
   - Simplicité pour un laboratoire isolé.
   - Sécurité renforcée.
   - Pas de dépendance au réseau physique.

3. **Quand envisager Bridged ?**
   - Si vous souhaitez exposer le réseau DMZ ou Backend à des utilisateurs externes ou à un réseau physique.

Si vous suivez ce guide, votre configuration sera robuste pour les objectifs de TP, et Host-Only garantit que tout reste fonctionnel dans un environnement isolé. 😊

