### Contexte de l'exercice

Dans cet exemple, nous allons mettre en place une configuration où :
- **VM1** est la machine qui souhaite accéder à Internet (client).
- **VM2** est une machine Linux qui sert de routeur entre **VM1** et l'Internet (via un tunnel ou NAT).
- **VM3** peut représenter une passerelle externe ou un serveur distant sur Internet (optionnel).

Nous allons utiliser **iptables** pour créer un tunnel (ou une redirection NAT) entre **VM1** et Internet, en passant par **VM2**.

### Prérequis

- Trois machines virtuelles sur **VirtualBox** ou **VMware** :
  - **VM1** : Client (par exemple, avec Ubuntu ou Debian).
  - **VM2** : Routeur (avec **iptables** installé).
  - **VM3** (facultatif) : Un serveur distant pour tester la connectivité.
  
- **VM2** a deux interfaces réseau :
  - Une interface connectée à **VM1** (réseau local privé).
  - Une interface connectée à Internet ou à un réseau externe (réseau public).

### 1. Configuration des interfaces réseau

#### **Sur VM2 (Routeur)**

Vérifiez que vous avez deux interfaces réseau, l'une pour la connexion locale avec **VM1** et l'autre pour la connexion externe.

- Interface pour le réseau interne (avec **VM1**) : `eth1` (par exemple).
- Interface pour le réseau externe (Internet ou autre réseau) : `eth0` (par exemple).

Vous pouvez vérifier les interfaces avec la commande suivante :

```bash
ip addr show
```

Assignez des adresses IP aux interfaces si ce n'est pas déjà fait.

```bash
# Interface locale (réseau interne avec VM1)
sudo ip addr add 192.168.1.1/24 dev eth1

# Interface externe (connectée à Internet)
sudo dhclient eth0  # Si l'IP est obtenue via DHCP
```

#### **Sur VM1 (Client)**

Assignez une adresse IP à **VM1**, dans le même réseau que l'interface interne de **VM2**.

```bash
sudo ip addr add 192.168.1.2/24 dev eth0
sudo ip route add default via 192.168.1.1  # Route par défaut via VM2
```

### 2. Activer le forwarding sur **VM2**

Pour que **VM2** puisse transférer des paquets entre ses deux interfaces réseau, vous devez activer le **forwarding IP**.

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

Pour rendre cette modification persistante après un redémarrage, éditez le fichier **/etc/sysctl.conf** et ajoutez ou modifiez la ligne suivante :

```bash
net.ipv4.ip_forward=1
```

### 3. Configurer le NAT avec **iptables** sur **VM2**

Pour permettre à **VM1** d'accéder à Internet via **VM2**, vous devez configurer un **NAT (Network Address Translation)** sur **VM2**. Cela redirige le trafic sortant de **VM1** vers Internet via l'interface externe de **VM2**.

```bash
# Supprime les anciennes règles iptables pour éviter les conflits
sudo iptables -F
sudo iptables -t nat -F

# Configure le masquerading (NAT) pour l'interface externe
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

# Autoriser le forwarding entre les deux interfaces (interne et externe)
sudo iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT
sudo iptables -A FORWARD -i eth0 -o eth1 -m state --state RELATED,ESTABLISHED -j ACCEPT
```

#### Explication des commandes :
1. **iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE** :  
   Cette règle permet de masquer les adresses IP internes (réseau local) derrière l'IP de **VM2** lorsqu'elles sortent sur **eth0** (interface externe). Le **masquerading** est une forme de NAT dynamique qui traduit les adresses IP.
   
2. **iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT** :  
   Autorise les paquets à être transférés depuis l'interface interne (**eth1**) vers l'interface externe (**eth0**).

3. **iptables -A FORWARD -i eth0 -o eth1 -m state --state RELATED,ESTABLISHED -j ACCEPT** :  
   Cette règle autorise le trafic retour (des réponses des connexions) à passer de l'interface externe à l'interface interne.

### 4. Tester la connexion

Sur **VM1**, essayez de **pinguer** un site web ou une adresse IP externe pour vérifier si le trafic est bien transféré via **VM2**.

```bash
ping 8.8.8.8  # Adresse IP de Google DNS
```

Si vous obtenez des réponses, cela signifie que le **NAT** et le **forwarding** fonctionnent correctement, et que **VM1** peut accéder à Internet via **VM2**.

### 5. (Optionnel) Créer un tunnel avec **iptables** (VPN-like)

Si vous souhaitez établir un tunnel sécurisé entre **VM1** et un serveur distant (similaire à un VPN), vous pouvez utiliser **IPsec** ou des outils comme **OpenVPN**. Voici un exemple très simple d'utilisation de **GRE tunneling** avec **iptables**.

#### Sur **VM2** (Routeur) :
```bash
# Créez un tunnel GRE entre VM2 et un serveur distant (par exemple VM3)
sudo ip tunnel add gre1 mode gre remote 203.0.113.1 local 192.168.1.1 ttl 255
sudo ip addr add 10.0.0.1/24 dev gre1
sudo ip link set gre1 up
```

#### Sur **VM3** (Serveur distant) :
```bash
# Créez le même tunnel sur le serveur distant
sudo ip tunnel add gre1 mode gre remote 192.168.1.1 local 203.0.113.1 ttl 255
sudo ip addr add 10.0.0.2/24 dev gre1
sudo ip link set gre1 up
```

Ensuite, vous pouvez **pinguer** l'adresse **10.0.0.2** (adresse du tunnel sur **VM3**) depuis **VM2** pour vérifier que le tunnel est opérationnel :

```bash
ping 10.0.0.2  # Tester la connexion à travers le tunnel GRE
```

### 6. Sauvegarder les règles **iptables**

Une fois que vous avez configuré les règles **iptables**, vous pouvez les sauvegarder pour qu'elles persistent après un redémarrage.

```bash
sudo iptables-save > /etc/iptables/rules.v4
```

### Conclusion

Avec ces commandes, vous pouvez configurer un tunnel ou un routage NAT avec **iptables** sur Linux. Cette manipulation peut être réalisée dans un environnement virtualisé avec **VirtualBox** ou **VMware**. Vous pouvez expérimenter avec différentes configurations, comme ajouter des règles de sécurité ou créer des tunnels GRE ou IPsec pour des connexions plus sécurisées.


--------------------------------
# Annexe 
--------------------------------

Voici un schéma  pour représenter la configuration de **tunneling** et **NAT** avec **iptables** dans un environnement virtualisé avec **VM1**, **VM2** (routeur), et **VM3** (serveur distant).

### Schéma ASCII : Configuration avec NAT et tunneling

```plaintext
                      +---------------------------+
                      |       Internet            |
                      +---------------------------+
                                |
                                |
                         +------------------+
                         | eth0 (externe)   |
                         | Adresse IP : DHCP|
                         |------------------|  (VM2 - Routeur Linux)
                         |       VM2        |
                         |   (Routeur NAT)  |
                         |------------------|
                         | eth1 (interne)   |
                         | IP : 192.168.1.1 |
                         +------------------+
                                |
                                |
                  +------------------------+
                  | eth0 (client local)    |
                  | IP : 192.168.1.2       |
                  |------------------------|  (VM1 - Client)
                  |        VM1             |
                  |------------------------|
                  |  Accès via NAT (VM2)   |
                  +------------------------+

                      +------------------------+
                      | gre1 (tunnel GRE)      |
                      | IP : 10.0.0.1          |
                      |------------------------|  (VM2 - Tunnel GRE)
                      |   Tunnel GRE entre VM2 |
                      |   et VM3               |
                      +------------------------+
                                |
                                |
                        +------------------+
                        |  eth0 (serveur)  |
                        |  IP : 203.0.113.1|
                        |------------------|  (VM3 - Serveur distant)
                        |       VM3        |
                        |------------------|
                        | gre1 (tunnel GRE)|
                        | IP : 10.0.0.2    |
                        +------------------+
```

### Explication du schéma :

1. **VM1 (Client local)** :
   - Adresse IP interne : `192.168.1.2` sur le réseau local connecté à **VM2** (le routeur).
   - Les paquets de données provenant de **VM1** sont envoyés via **VM2** en utilisant NAT pour accéder à Internet.

2. **VM2 (Routeur Linux)** :
   - Interface **eth0** connectée à Internet avec une adresse IP dynamique attribuée par DHCP.
   - Interface **eth1** connectée à **VM1** via un réseau interne local (IP : `192.168.1.1`).
   - Le **NAT** est configuré pour rediriger le trafic sortant de **VM1** vers Internet via **eth0**.
   - Un **tunnel GRE** est configuré entre **VM2** et **VM3** pour permettre un tunnel sécurisé entre les deux machines.
   - Adresse IP du tunnel GRE sur **VM2** : `10.0.0.1`.

3. **VM3 (Serveur distant)** :
   - Adresse IP publique : `203.0.113.1`.
   - **Tunnel GRE** configuré avec **VM2** pour créer une connexion directe entre les deux machines (utilisée comme un VPN).
   - Adresse IP du tunnel GRE sur **VM3** : `10.0.0.2`.

### Détails supplémentaires :
- Le **tunnel GRE** entre **VM2** et **VM3** encapsule les paquets, ce qui permet de sécuriser la connexion entre ces deux machines.
- Le **NAT** sur **VM2** permet à **VM1** de masquer son adresse IP interne (`192.168.1.2`) lorsque les paquets sortent sur Internet via **eth0**.

Ce schéma permet de visualiser la topologie réseau avec **iptables** pour configurer le NAT et le tunneling dans un environnement virtualisé. 

---------------------------------------
# Annexe 2 - c'est quoi GRE ?
---------------------------------------

**GRE (Generic Routing Encapsulation)** est un protocole de tunneling qui encapsule des paquets de différentes couches de réseau, permettant ainsi le transport de ces paquets à travers des réseaux IP. C’est une méthode très courante pour créer des **tunnels** entre deux réseaux distants ou entre des machines sur un même réseau, en encapsulant les paquets à l'intérieur de paquets IP supplémentaires.

### Fonctionnement de GRE dans cet exemple

Dans notre schéma, **VM2** (le routeur) et **VM3** (le serveur distant) sont connectés via un **tunnel GRE**.

1. **Tunnel GRE** :
   - Le tunnel GRE est créé pour encapsuler les paquets envoyés entre **VM2** et **VM3**.
   - Chaque paquet envoyé par **VM2** à destination de **VM3** est encapsulé dans un autre paquet IP par GRE, puis envoyé à **VM3**.
   - Le tunnel GRE fonctionne comme un tunnel virtuel, reliant les deux machines (VM2 et VM3) à travers un réseau intermédiaire (qui pourrait être Internet ou un autre réseau IP).

2. **Utilisation du tunnel GRE** :
   - Le tunnel GRE est comme un **tunnel virtuel** qui relie directement **VM2** et **VM3**, en encapsulant les paquets de données pour qu'ils puissent voyager de manière sécurisée.
   - Par exemple, si **VM2** veut envoyer des données à **VM3**, le paquet est d'abord encapsulé dans un paquet GRE, puis envoyé à **VM3** à travers le réseau IP. À l'arrivée sur **VM3**, le paquet GRE est décapsulé pour obtenir le paquet de données original.

### Pourquoi utiliser GRE ?

1. **Encapsulation de différents types de paquets** : GRE permet d'encapsuler différents types de protocoles, pas seulement IP, ce qui en fait un protocole flexible.
   
2. **Création de tunnels** : Dans notre cas, GRE est utilisé pour créer un **tunnel** entre **VM2** et **VM3**, permettant une connexion directe entre les deux machines à travers un réseau externe (comme Internet).

3. **Transparence** : Le tunnel GRE permet de faire passer des données entre deux réseaux distants comme si elles étaient sur le même réseau local.

### Exemple d'utilisation de GRE dans le réseau

Disons que **VM2** et **VM3** sont dans des sous-réseaux différents, et que nous voulons les relier comme s'ils étaient directement connectés. Le **tunnel GRE** permet d'encapsuler les paquets envoyés entre ces deux machines, et de les envoyer à travers un réseau intermédiaire, comme Internet.

- **VM2** a une interface GRE (`gre1`) avec l'adresse IP interne `10.0.0.1`.
- **VM3** a une interface GRE (`gre1`) avec l'adresse IP interne `10.0.0.2`.
- Le trafic entre les deux passe par ce tunnel, qui encapsule les paquets pour qu'ils puissent traverser le réseau intermédiaire.

### Schéma plus simple pour visualiser le tunnel GRE

```plaintext
     VM2 (Routeur)                    Internet                  VM3 (Serveur)
+--------------------+           +-------------------+       +---------------------+
| Interface gre1     |           |                   |       | Interface gre1      |
| IP : 10.0.0.1      |<--------- | ----Tunnel GRE---- |------>| IP : 10.0.0.2       |
+--------------------+           +-------------------+       +---------------------+
| eth0 : Connexion Internet       |                   |       | eth0 : Connexion publique |
+--------------------+           +-------------------+       +---------------------+
```

Dans cet exemple :
- **VM2** encapsule les paquets dans le tunnel GRE, en les envoyant via Internet à **VM3**.
- **VM3** reçoit ces paquets, les décapsule, et traite les données comme si elles provenaient directement de **VM2** sans passer par Internet.

### Avantage du GRE :
- **Flexibilité** : Vous pouvez transporter différents types de protocoles via un tunnel GRE, et il peut être utilisé dans des scénarios où d'autres types de tunnels (comme IPsec) ne sont pas nécessaires ou sont trop lourds.
- **Support de différents protocoles** : GRE est capable de transporter des protocoles qui ne sont pas forcément supportés par d'autres types de tunnels.

En résumé, dans cet exemple, le **tunnel GRE** entre **VM2** et **VM3** permet d'encapsuler et de transporter des paquets de manière sécurisée entre deux machines distantes. Cela fonctionne comme un **tunnel virtuel** pour relier les deux machines à travers un réseau intermédiaire, qu'il s'agisse d'Internet ou d'un réseau plus complexe.


