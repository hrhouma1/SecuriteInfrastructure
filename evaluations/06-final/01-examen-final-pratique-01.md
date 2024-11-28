# Architecture de l'examen final

----------------

```
                          +-------------------+
                          |      CLIENT       |
                          |   Windows 10 Pro  |
                          |-------------------|
                          |   VMnet1          |
                          | IP: DHCP/Fixe     |
                          +-------------------+
                                 |
                         +-------------------+
                         |     VMnet1        | (Gestion & Domaine)
                         +-------------------+
                          /          |         \
           +----------------+ +----------------+ +----------------+
           |      DC         | |      SRV1      | |      SRV2      |
           | Windows Server  | | Windows Server | | Windows Server |
           | 2019 (AD DS)    | | 2019           | | 2019           |
           |-----------------| |----------------| |----------------|
           | VMnet1          | | VMnet1         | | VMnet1         |
           | IP: 192.168.1.1 | | IP: 192.168.1.10| | IP: 192.168.1.20|
           | DNS/DHCP        | | VIP: 192.168.2.100 (via NLB)      |
           +-----------------+ +----------------+ +----------------+
                                 |                 |
                          +-------------------+ +-------------------+
                          |     VMnet2        | |     VMnet2        |
                          +-------------------+ +-------------------+
                          | IP: 192.168.2.10  | | IP: 192.168.2.20  |
                          |   (Trafic NLB)    | |   (Trafic NLB)    |
                          +-------------------+ +-------------------+
                                 |                 |
                          +-------------------------------+
                          |         VIP (Cluster NLB)     |
                          |       IP: 192.168.2.100       |
                          +-------------------------------+
```

---

### **Explications des réseaux et interfaces**

1. **Réseaux (VMnets) :**
   - **VMnet1 (Gestion et Domaine)** :
     - Gère les communications entre le **Contrôleur de Domaine (DC)**, les serveurs (SRV1 et SRV2), et le client (CLIENT).
     - IP utilisées : `192.168.1.x`.
   - **VMnet2 (Trafic NLB)** :
     - Utilisé exclusivement pour le trafic du cluster NLB.
     - IP utilisées : `192.168.2.x`.

2. **Détails des Machines et Rôles :**
   - **DC** :
     - Connecté uniquement à **VMnet1**.
     - Fournit les services de **DNS, DHCP, et Active Directory**.
   - **SRV1 et SRV2** :
     - Chaque serveur a deux interfaces réseau :
       - **VMnet1** : Pour la gestion et la communication avec le domaine.
       - **VMnet2** : Pour le trafic NLB.
     - Partagent une adresse IP virtuelle (VIP) sur **VMnet2** : `192.168.2.100`.
   - **CLIENT** :
     - Connecté à **VMnet1** uniquement.
     - Utilisé pour tester l’accès au cluster NLB (`http://192.168.2.100`) et au domaine.

3. **VIP (Virtual IP)** :
   - L’adresse virtuelle du cluster NLB est configurée sur **VMnet2** : `192.168.2.100`.
   - Gérée par les serveurs SRV1 et SRV2 pour la répartition de charge.

---

### **Table des Adresses et Rôles**

| **Nom**         | **VMnet** | **Adresse IP**         | **Rôle**                              |
|------------------|-----------|-------------------------|----------------------------------------|
| **DC**           | VMnet1    | 192.168.1.1            | Contrôleur de domaine (AD DS), DNS    |
| **SRV1**         | VMnet1    | 192.168.1.10           | Nœud 1 du cluster NLB (gestion)       |
|                  | VMnet2    | 192.168.2.10           | Trafic NLB                             |
| **SRV2**         | VMnet1    | 192.168.1.20           | Nœud 2 du cluster NLB (gestion)       |
|                  | VMnet2    | 192.168.2.20           | Trafic NLB                             |
| **VIP (Cluster)**| VMnet2    | 192.168.2.100          | Adresse virtuelle partagée (NLB)      |
| **CLIENT**       | VMnet1    | DHCP ou 192.168.1.50   | Machine cliente pour tests et accès   |
