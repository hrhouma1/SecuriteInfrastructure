# Question:

- Nombre et rôle de chaque machine ?


# Réponse :


*Cette version précise chaque machine par son nom de fonction, son rôle, et ses adresses IP/interfaces pour faciliter l’installation et la configuration de l’architecture.*

![image](https://github.com/user-attachments/assets/14b14be9-42a1-4e64-8366-5a003e854a5d)


```
                          +----------------+
                          |    Internet    |
                          +----------------+
                                 |
                                 |
                           +-------------+
                           |   Machine 1 |
                           |   Routeur   |
                           |-------------|
                           | DNS, NTP    |
                           | Routage     |
                           +-------------+
                           | NIC1 (NAT)  |
                           | NIC2 (Interne 20.21.22.254/24)  |
                           | NIC3 (DMZ 10.11.12.254/24)      |
                                 |
               ------------------+--------------------
               |                                       |
       +----------------+                     +------------------+
       |   Machine 2    |                     |   Switch (DMZ)  |
       | Mail Server    |                     +------------------+
       |----------------|                           |
       | 20.21.22.10/24 |              --------------------------
       +----------------+              |                    |
                                  +----------------+    +----------------+
                                  |   Machine 3   |    |    Machine 4   |
                                  |    Master     |    |    Backup      |
                                  |---------------|    |----------------|
                                  | NIC1: 10.11.12.1   | NIC1: 10.11.12.2
                                  | NIC2: 192.168.100.1| NIC2: 192.168.100.2
                                  | NIC3: 172.168.1.1  | NIC3: 172.168.1.2
                                  | VIP: 10.11.12.99   | VGW: 192.168.100.99
                                  +----------------+    +----------------+
                                            |                    |
                                            |                    |
                                     +----------------+   +----------------+
                                     |   Machine 5    |   |    Machine 6   |
                                     |    Noeud1      |   |     Noeud2     |
                                     |--------------- |   |---------------- |
                                     | NIC1: 192.168.100.10 | NIC1: 192.168.100.20
                                     | Def. Gateway: 192.168.100.99 |
                                     +----------------+   +----------------+
```

### Informations de chaque machine

1. **Machine 1** : Routeur
   - Rôles : DNS, NTP, Routage
   - Interfaces :
     - **NIC1** : NAT (Internet)
     - **NIC2** : Réseau Interne (20.21.22.254/24)
     - **NIC3** : Réseau DMZ (10.11.12.254/24)

2. **Machine 2** : Mail Server
   - Rôle : Serveur Mail avec Postfix
   - Interface :
     - **NIC** : Réseau Interne (20.21.22.10/24)

3. **Machine 3** : Master (Directeur principal)
   - Rôle : Serveur de haute disponibilité avec Keepalived
   - Interfaces :
     - **NIC1** : DMZ (10.11.12.1/24)
     - **NIC2** : Interne pour gestion de haute disponibilité (192.168.100.1/24)
     - **NIC3** : Réseau de synchronisation (172.168.1.1/30)
   - VIP (adresse IP virtuelle) : 10.11.12.99/24

4. **Machine 4** : Backup (Directeur secondaire)
   - Rôle : Serveur de haute disponibilité avec Keepalived
   - Interfaces :
     - **NIC1** : DMZ (10.11.12.2/24)
     - **NIC2** : Interne pour gestion de haute disponibilité (192.168.100.2/24)
     - **NIC3** : Réseau de synchronisation (172.168.1.2/30)
   - Passerelle Virtuelle (VGW) : 192.168.100.99/24

5. **Machine 5** : Noeud1
   - Rôle : Serveur réel pour service web
   - Interface :
     - **NIC1** : Interne pour communication avec directeurs (192.168.100.10/24)
   - Passerelle par défaut : 192.168.100.99

6. **Machine 6** : Noeud2
   - Rôle : Serveur réel pour service web
   - Interface :
     - **NIC1** : Interne pour communication avec directeurs (192.168.100.20/24)
   - Passerelle par défaut : 192.168.100.99

