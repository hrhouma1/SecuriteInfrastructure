# Mise en place d’un VPN avec authentification RADIUS via Windows Server et pfSense

# Référence : 
- https://www.youtube.com/watch?v=zQh7MnJUvc8&ab_channel=MDF-IT%E2%80%9CMDFBelgium_Vlogs%E2%80%9DChannel
- https://www.youtube.com/watch?app=desktop&v=9x91nZcveIE&ab_channel=DOIT%2Fmostafaahmed
- https://www.securew2.com/blog/how-to-set-up-a-microsoft-radius-server
- https://learn.microsoft.com/en-us/azure/vpn-gateway/point-to-site-how-to-radius-ps (optionnel - powershell)
  
# Contexte général
Un **VPN (Virtual Private Network)** est comme un tunnel sécurisé qui relie un utilisateur, même à distance, au réseau local d’une entreprise ou d’une école. Cela permet à l’utilisateur d’accéder aux fichiers, applications et ressources de ce réseau en toute sécurité, même s’il est à l'extérieur du bureau. 

Pour cela, nous allons utiliser **pfSense**, un logiciel gratuit qui fait office de routeur et de pare-feu, pour gérer les connexions VPN. Nous allons utiliser **Windows Server** pour vérifier l’identité des utilisateurs grâce à un serveur appelé **RADIUS**, qui va demander à **Active Directory** (un autre service dans Windows) si l'utilisateur qui tente de se connecter est bien autorisé.

# Prérequis
- Un serveur **Windows Server 2019/2016/2022** où **Active Directory** et **Network Policy Server (NPS)** sont installés.
- **pfSense** installé et configuré sur votre réseau local.
- Une machine **Windows 10** sur le même réseau pour tester la connexion VPN.

# Étape 1 : Configurer Windows Server pour l’authentification RADIUS

**Qu'est-ce que le serveur RADIUS ?**  
RADIUS est un service qui aide à vérifier l’identité des personnes qui veulent se connecter au réseau. Quand quelqu’un essaie de se connecter via le VPN, **pfSense** envoie une demande au serveur **RADIUS** pour demander si cette personne est bien autorisée à se connecter. Si RADIUS donne son accord, la connexion est autorisée.

#### 1.1. Installer le rôle NPS (Network Policy Server)
1. **Ouvrir le Gestionnaire de serveur** sur votre Windows Server :
   - Le **Gestionnaire de serveur** est l’outil principal pour gérer ce que fait votre serveur. Il vous permet d’ajouter ou de supprimer des "rôles" (des fonctionnalités supplémentaires).
   - Vous le trouverez dans le **Menu Démarrer**.
2. Une fois ouvert, cliquez sur **Ajouter des rôles et des fonctionnalités**.
3. Dans les options, sélectionnez **Installation basée sur un rôle ou une fonctionnalité**.
4. Cochez la case **Accès réseau et stratégie d’accès à distance (Network Policy and Access Services)**. 
   - Cela va installer un service appelé **NPS (Network Policy Server)**, qui est le serveur RADIUS.
5. Suivez les étapes jusqu'à la fin pour installer cette fonctionnalité.

#### 1.2. Configurer NPS pour accepter les requêtes RADIUS
Maintenant que NPS est installé, il faut le configurer pour qu’il sache que **pfSense** va lui envoyer des requêtes.

1. Ouvrez **NPS** (Network Policy Server) depuis le **Menu Démarrer**.
2. Dans le menu NPS, allez dans **RADIUS Clients et Serveurs** puis cliquez sur **Clients RADIUS**.
   - Un **client RADIUS** est un appareil (comme pfSense) qui demande à RADIUS de vérifier l’identité des utilisateurs.
3. Cliquez sur **Nouveau** pour ajouter **pfSense** comme client.
   - Donnez-lui un nom (par exemple `pfSenseVPN`).
   - Entrez l'adresse IP de votre **pfSense** (vous pouvez la trouver dans les paramètres réseau de pfSense, généralement quelque chose comme `192.168.1.X`).
   - Entrez un **secret partagé** : c’est un mot de passe secret que vous allez utiliser pour que **pfSense** et **Windows Server** se fassent confiance.

#### 1.3. Créer une politique réseau pour les utilisateurs VPN
Nous allons maintenant dire à NPS quelles sont les personnes qui ont le droit de se connecter au VPN.

1. Dans NPS, allez dans **Politiques réseau** et créez une nouvelle **politique réseau**.
2. Ajoutez une **condition d’accès** qui va spécifier que seuls les utilisateurs d’un certain groupe (comme le groupe **VPNUsers**) peuvent se connecter.
   - Ce groupe peut être créé dans **Active Directory**, où vous gérez vos utilisateurs et vos groupes.
3. Configurez l’authentification en choisissant **MS-CHAPv2** ou **PEAP**. Ces méthodes d'authentification sont comme des serrures très sécurisées qui protègent vos données lorsqu'elles circulent sur Internet.

# Étape 2 : Configurer pfSense pour gérer les connexions VPN

**Qu’est-ce que pfSense ?**  
pfSense est un logiciel qui fonctionne comme un routeur et un pare-feu. Un routeur fait passer le trafic entre différents réseaux, et un pare-feu protège votre réseau des connexions non autorisées. Nous allons le configurer pour qu’il crée un tunnel VPN, et nous allons lui dire d’utiliser le serveur **RADIUS** sur **Windows Server** pour vérifier qui peut se connecter.

#### 2.1. Accéder à l'interface web de pfSense
1. Ouvrez votre navigateur web et tapez l’adresse IP de votre **pfSense** (généralement quelque chose comme `https://192.168.1.1`).
2. Connectez-vous à l'interface avec vos identifiants administrateur.

#### 2.2. Configurer OpenVPN sur pfSense
Nous allons maintenant configurer le serveur **OpenVPN** sur pfSense. **OpenVPN** est un type de VPN open source qui permet de créer des tunnels VPN sécurisés.

1. Dans le menu principal de pfSense, allez dans **VPN** -> **OpenVPN** -> **Serveurs**.
2. Cliquez sur **Ajouter** pour configurer un nouveau serveur VPN.
   - **Mode du serveur** : Sélectionnez **Remote Access (User Auth)** (accès à distance avec authentification utilisateur).
   - **Backend d'authentification** : Sélectionnez **RADIUS**.
   - Entrez l'adresse IP du **Windows Server** et le **secret partagé** que vous avez configuré plus tôt.
   - Choisissez un port VPN (par défaut : **1194** pour OpenVPN).

#### 2.3. Ajouter des règles de pare-feu
pfSense a besoin de règles pour autoriser les connexions VPN.

1. Allez dans **Pare-feu** -> **Règles** -> **OpenVPN**.
2. Ajoutez une règle pour autoriser le trafic entrant et sortant sur le port que vous avez configuré (par exemple, le port **1194** pour OpenVPN).

# Étape 3 : Configurer le client Windows 10 pour le VPN

#### 3.1. Installation du client VPN
Pour que la machine Windows 10 puisse se connecter au VPN, vous devez installer un logiciel VPN.

1. Téléchargez et installez le client **OpenVPN** pour Windows 10 depuis le site officiel d'OpenVPN (gratuit et open source).

#### 3.2. Exporter la configuration VPN depuis pfSense (voir annexe 3 en cas de problèmes)
pfSense peut créer un fichier de configuration VPN que vous utiliserez sur le client Windows 10.

1. Dans pfSense, allez dans **VPN** -> **OpenVPN** -> **Client Export**.
2. Exportez le fichier de configuration `.ovpn` pour Windows.
3. Transférez ce fichier sur votre machine **Windows 10**.

#### 3.3. Configurer OpenVPN sur Windows 10
1. Ouvrez le client **OpenVPN** sur Windows 10.
2. Importez le fichier de configuration `.ovpn` que vous avez téléchargé depuis pfSense.
3. Connectez-vous en utilisant les identifiants d'un utilisateur appartenant au groupe **VPNUsers** d'Active Directory.

# Étape 4 : Vérification et test

#### 4.1. Tester la connexion
1. Depuis la machine **Windows 10**, ouvrez le client **OpenVPN** et essayez de vous connecter.
2. Si tout est correctement configuré, la connexion VPN sera établie, et **pfSense** demandera à **Windows Server** (via RADIUS) de vérifier l’identité de l’utilisateur.
3. Une fois connecté, essayez d'accéder aux ressources de votre réseau local pour vérifier que tout fonctionne.

#### 4.2. Vérification des journaux
Si quelque chose ne fonctionne pas, vous pouvez vérifier les journaux pour comprendre ce qui ne va pas :
- **Sur pfSense** : Allez dans **Status** -> **System Logs** -> **OpenVPN** pour voir les détails des connexions.
- **Sur Windows Server** : Ouvrez le **Visualiseur d'événements** et consultez les logs RADIUS dans NPS pour voir s’il y a des erreurs d'authentification.

# Conclusion

En suivant ces étapes détaillées, vous aurez configuré un VPN avec authentification RADIUS sur **Windows Server** et **pfSense**, et vous aurez testé la connexion avec une machine **Windows 10**. Cela permet de sécuriser les connexions à distance tout en vérifiant l’identité des utilisateurs via **Active Directory**.

--------------------------------------------------
# Annexe 1 - proposition d'adressage 
--------------------------------------------------

Je vous propose un exemple de configuration d'adresses IP pour les différentes machines et équipements dans ce réseau, qui pourrait être utilisé pour votre mise en place de VPN avec authentification RADIUS via **Windows Server** et **pfSense**.

### Configuration des adresses IP

1. **Windows Server** (Serveur Active Directory + Serveur RADIUS NPS) :
   - Adresse IP : `192.168.1.10`
   - Masque de sous-réseau : `255.255.255.0`
   - Passerelle par défaut : `192.168.1.1` (adresse IP de pfSense)
   - DNS : `192.168.1.10` (lui-même, car il sert également de contrôleur de domaine et DNS)

2. **pfSense** (Routeur et VPN) :
   - **Interface LAN** (vers le réseau local) :
     - Adresse IP : `192.168.1.1`
     - Masque de sous-réseau : `255.255.255.0`
   - **Interface WAN** (vers l'extérieur, par exemple Internet) :
     - Adresse IP : obtenue dynamiquement via DHCP ou configurée en IP statique par votre fournisseur d'accès à Internet (ex. `198.51.100.10`)
     - DNS : Utilisez des serveurs DNS publics (ex. `8.8.8.8` pour Google DNS)

3. **Client VPN** (Windows 10) :
   - Si le client se trouve sur le **réseau local** (même réseau que Windows Server et pfSense), par exemple pour les tests :
     - Adresse IP locale : `192.168.1.20`
     - Masque de sous-réseau : `255.255.255.0`
     - Passerelle par défaut : `192.168.1.1` (pfSense)
   - Si le client se connecte depuis l'extérieur (réseau public) :
     - Adresse IP dynamique ou fixe attribuée par le FAI du client.
     - IP VPN attribuée par pfSense lorsqu'il se connecte via le tunnel VPN (ex. `10.8.0.2` si vous configurez une plage IP pour les clients VPN).

4. **Plage d'adresses pour les clients VPN** :
   - pfSense va attribuer une adresse IP aux clients qui se connectent au VPN.
   - Exemple de plage d'adresses pour les clients VPN : `10.8.0.0/24`
     - Adresse du serveur VPN (pfSense) : `10.8.0.1`
     - Plage attribuable aux clients VPN : `10.8.0.2` à `10.8.0.254`

# Résumé des adresses IP

| Composant              | Adresse IP         | Masque             | Remarque                               |
|------------------------|--------------------|--------------------|----------------------------------------|
| **Windows Server**      | `192.168.1.10`     | `255.255.255.0`    | Contrôleur de domaine, DNS, RADIUS     |
| **pfSense (LAN)**       | `192.168.1.1`      | `255.255.255.0`    | Passerelle par défaut du réseau local  |
| **pfSense (WAN)**       | `198.51.100.10`    | `255.255.255.0`    | Adresse publique du FAI (exemple)     |
| **Client Windows 10**   | `192.168.1.20`     | `255.255.255.0`    | Test local ou IP VPN si à distance    |
| **Plage VPN Clients**   | `10.8.0.0/24`      | `255.255.255.0`    | IP attribuées aux clients via VPN     |

### Note :
- **Serveur DNS** : Si votre **Windows Server** joue également le rôle de serveur DNS, configurez les clients pour utiliser l'adresse IP de Windows Server (`192.168.1.10`) comme serveur DNS.
- **Plage d’adresses VPN** : Assurez-vous que la plage d'adresses que vous attribuez aux clients VPN ne chevauche pas le réseau local interne (`192.168.1.0/24`), afin d’éviter des conflits d’adresses IP.

--------------------------------------------
# Annexe 2  - flux de communication
--------------------------------------------


```plaintext
                          INTERNET
                              |
                              |
                +------------------------------+
                |      Interface WAN (pfSense)  |  
                |      Adresse IP : 198.51.100.10|
                +------------------------------+
                              |
                              |
             ------------------------------------------
             |         Routeur/Pare-feu pfSense        |
             |      (gère le trafic réseau et VPN)     |
             |  -------------------------------------- |
             |  |  Interface LAN : 192.168.1.1        |   
             |  -------------------------------------- |
             |               |                        |
             ------------------------------------------
                              |
                              |
            +-----------------+---------------------+
            |                                   |    
            |                                   |
+-----------------------+           +-----------------------+  
|    Windows Server      |           |   Client VPN (Win10)   |
|  (Active Directory +   |           |   (sur le même réseau) |
|    RADIUS NPS)         |           |  Adresse IP locale     |
|-----------------------|           |      192.168.1.20      |
| Adresse IP : 192.168.1.10|           +-----------------------+
|-----------------------|                        |
|  Demandes d'authentification                   |
|     pour le VPN                               |  
|                                               |
| RADIUS vérifie si l'utilisateur               |  
|  peut accéder au VPN                          |
+-----------------------+                       |
                                                |
                                        Connexion VPN validée
                                       (si authentification réussie)
                                                |
                                  ----------------------------------
                                  |   Tunnel VPN sécurisé établi   |
                                  | (via OpenVPN ou IPSec sur pfSense)
                                  ----------------------------------

+------------------------------------------------------------+
|            PLAGE D'ADRESSES IP POUR LES CLIENTS VPN         |
|              (Attribuées automatiquement par pfSense)       |
|         Exemple : 10.8.0.0/24 (pour connexions VPN)         |
|         IP serveur VPN : 10.8.0.1                           |
|         IP client VPN : 10.8.0.2 (par exemple)              |
+------------------------------------------------------------+
```

# Détails supplémentaires :

1. **Internet** : Représente le réseau extérieur auquel le client VPN peut se connecter pour accéder au réseau interne via pfSense.
   
2. **pfSense (Routeur/Pare-feu)** :  
   - **Interface WAN** : Cette interface est connectée à Internet et a une adresse IP publique (exemple : `198.51.100.10`). Cette adresse peut être assignée par votre fournisseur d'accès Internet (FAI).
   - **Interface LAN** : Connectée au réseau interne, elle gère les connexions locales avec une adresse IP privée (`192.168.1.1`).
   - **Rôle** : pfSense fait office de routeur et de pare-feu, gère le VPN et filtre les connexions.

3. **Windows Server (RADIUS et Active Directory)** :  
   - Ce serveur vérifie l’identité des utilisateurs qui se connectent au VPN à travers RADIUS.
   - **Adresse IP** : `192.168.1.10`.
   - Il héberge **Active Directory** pour gérer les utilisateurs et **RADIUS** pour vérifier que les utilisateurs sont autorisés à se connecter au VPN.

4. **Client VPN (Windows 10)** :  
   - C’est l’ordinateur d’un utilisateur qui veut se connecter au réseau à distance via le VPN.
   - Si le client est dans le même réseau que pfSense (pour les tests), il peut avoir l'adresse **192.168.1.20**.
   - S'il se connecte depuis un réseau extérieur (ex. depuis chez lui), une adresse VPN lui sera attribuée automatiquement par pfSense (par exemple, **10.8.0.2**).

5. **Plage d'adresses pour les clients VPN** :
   - Les adresses VPN commencent par **10.8.0.X** (par exemple, **10.8.0.2** pour un utilisateur connecté).
   - pfSense attribue automatiquement une IP VPN lorsque l'utilisateur se connecte via le VPN.

---

# Explication des flux :
1. **Connexion VPN** : L’utilisateur sur le **client Windows 10** essaie de se connecter via le VPN à **pfSense**. Il envoie ses identifiants (nom d’utilisateur et mot de passe).
   
2. **Vérification des identifiants (RADIUS)** : pfSense envoie les identifiants au **serveur RADIUS** sur **Windows Server** (IP : `192.168.1.10`).
   
3. **Validation (RADIUS)** : Le serveur RADIUS vérifie ces informations dans **Active Directory** (base d’utilisateurs), et si l’utilisateur est autorisé, il informe pfSense que la connexion VPN peut être autorisée.

4. **Connexion VPN établie** : Si l'authentification est réussie, pfSense établit un **tunnel VPN sécurisé** entre le client VPN (Windows 10) et le réseau interne.

---

# Ce que vous devez comprendre :
- **Chaque composant a une adresse IP spécifique** pour fonctionner correctement. pfSense a une adresse IP publique (WAN) pour se connecter à Internet et une adresse IP privée (LAN) pour gérer les connexions locales.
- **pfSense et Windows Server** travaillent ensemble pour gérer la sécurité des connexions VPN. pfSense vérifie l’identité des utilisateurs avec l’aide du serveur RADIUS sur Windows Server.
- **Le tunnel VPN sécurisé** permet à un utilisateur d’accéder aux ressources internes comme s’il était sur le réseau local, même s’il est à distance.




-----------------------
# Résumé des interfaces dont vous aurez besoin pour mettre en place votre infrastructure VPN avec **pfSense** et **Windows Server** :
-----------------------

### 1. **Interfaces pour pfSense** :
pfSense jouera le rôle de routeur/pare-feu, et vous aurez besoin d'au moins **deux interfaces réseau** :

- **Interface WAN (Wide Area Network)** :
  - Connectée à Internet.
  - Adresse IP publique fournie par votre fournisseur d'accès Internet (ex. `198.51.100.10`).
  - Permet à pfSense de recevoir les connexions VPN des utilisateurs distants.

- **Interface LAN (Local Area Network)** :
  - Connectée au réseau interne local.
  - Adresse IP privée (ex. `192.168.1.1`).
  - Permet à pfSense de gérer le trafic réseau local et de communiquer avec le serveur Windows (RADIUS) et d'autres appareils locaux.

### 2. **Interfaces pour Windows Server** :
Le serveur Windows doit être connecté au réseau local pour communiquer avec pfSense et fournir l’authentification RADIUS :

- **Interface LAN** :
  - Connectée au même réseau local que pfSense.
  - Adresse IP privée (ex. `192.168.1.10`).
  - Utilisée pour que pfSense envoie les requêtes d'authentification RADIUS.

### 3. **Client VPN (Windows 10)** :
Pour tester la connexion, le **client VPN** aura besoin d'une connexion réseau :

- **Interface réseau du client** :
  - **Si local (test)** : Adresse IP sur le même réseau local que pfSense et Windows Server (ex. `192.168.1.20`).
  - **Si à distance (Internet)** : Une adresse IP dynamique ou fixe attribuée par le fournisseur d’accès (utilisera le VPN pour se connecter au réseau interne via pfSense).

### Résumé des interfaces nécessaires :
- **pfSense** :
  - **Interface WAN** (Internet).
  - **Interface LAN** (réseau local).

- **Windows Server** :
  - **Interface LAN** (même réseau local que pfSense).

- **Client VPN** :
  - Une seule interface (local ou distant) selon le scénario de test.

En résumé, pour **pfSense**, vous aurez besoin d'au moins **deux interfaces réseau** (une pour WAN et une pour LAN), et **Windows Server** n’aura besoin que d’une seule interface réseau **LAN** pour communiquer avec pfSense.

- Ce tableau résume les interfaces et les adresses IP nécessaires pour chaque composant de votre infrastructure VPN.

| **Composant**        | **Nom de l'interface** | **Rôle de l'interface**         | **Adresse IP (exemple)**  | **Remarque**                                       |
|----------------------|------------------------|---------------------------------|---------------------------|----------------------------------------------------|
| **pfSense**          | WAN                    | Connexion à Internet            | `198.51.100.10`           | Adresse IP publique attribuée par le FAI            |
| **pfSense**          | LAN                    | Connexion au réseau local       | `192.168.1.1`             | Adresse IP privée pour gérer le trafic local        |
| **Windows Server**    | LAN                    | Authentification RADIUS et AD   | `192.168.1.10`            | Connecté au réseau local, communique avec pfSense   |
| **Client VPN (Local)**| LAN                    | Test de connexion VPN local     | `192.168.1.20`            | Adresse IP privée pour les tests dans le réseau local|
| **Client VPN (Distant)**| WAN                   | Connexion VPN via Internet      | Adresse IP dynamique (FAI) | Adresse IP fournie par le FAI pour les connexions distantes |

### Explications :
- **pfSense** aura deux interfaces :
  - **WAN** : connectée à Internet avec une adresse IP publique attribuée par votre fournisseur d'accès Internet (FAI).
  - **LAN** : connectée au réseau interne avec une adresse IP privée (par exemple, `192.168.1.1`).
  
- **Windows Server** n'a besoin que d'une **interface LAN** avec une adresse IP privée (exemple : `192.168.1.10`) pour communiquer avec pfSense et gérer les authentifications via RADIUS.

- Le **client VPN** peut être soit sur le réseau local pour tester (ex. : `192.168.1.20`), soit connecté depuis Internet avec une adresse IP dynamique ou fixe fournie par le FAI.

-----------------
# Annexe 3 - problème d'exportation
----------------


- Si vous ne trouvez pas l'option d'exportation dans **pfSense**, il est probable que le **package d'exportation OpenVPN Client Export Utility** ne soit pas installé par défaut. 
- Je vous propose les étapes pour installer ce package et exporter la configuration `.ovpn` :

### Étape 1 : Installation du package `OpenVPN Client Export Utility`
1. Connectez-vous à l'interface web de votre **pfSense**.
2. Allez dans le menu **System** -> **Package Manager**.
3. Dans **Package Manager**, cliquez sur l’onglet **Available Packages**.
4. Recherchez `OpenVPN Client Export`.
5. Cliquez sur **Install** à côté du package `OpenVPN Client Export Utility`.
6. Confirmez l'installation en cliquant sur **Confirm**.

### Étape 2 : Exporter le fichier de configuration VPN `.ovpn`
1. Une fois le package installé, allez dans **VPN** -> **OpenVPN**.
2. Cliquez sur l’onglet **Client Export** (qui est apparu après l'installation du package).
3. Sélectionnez le **Serveur OpenVPN** que vous avez configuré.
4. Faites défiler la page jusqu'à la section **Client Install Packages**.
5. Trouvez l’option **Current Windows Installer** ou **Inline Configurations** et cliquez sur **Download** à côté de l’option qui correspond à votre système d'exploitation (par exemple, `.ovpn` pour Windows).
6. Téléchargez et enregistrez le fichier de configuration `.ovpn`.

### Étape 3 : Transférer et importer le fichier `.ovpn` sur votre client Windows 10
1. Copiez le fichier `.ovpn` sur la machine **Windows 10** où vous souhaitez tester la connexion.
2. Ouvrez le client **OpenVPN** sur Windows 10.
3. Cliquez sur **Importer un profil** et sélectionnez le fichier `.ovpn` que vous venez de transférer.
4. Connectez-vous en utilisant vos identifiants (nom d'utilisateur et mot de passe).

Cela devrait permettre à votre client **OpenVPN** de se connecter correctement à votre serveur **pfSense** avec la configuration adéquate.

### Problèmes courants :
- Si le package `OpenVPN Client Export Utility` n'apparaît pas dans la liste, assurez-vous que vous utilisez la version de pfSense la plus récente.
- Vérifiez que le service **OpenVPN** sur pfSense est bien démarré et configuré pour l’authentification RADIUS.
- Si vous avez des erreurs de connexion lors de l'importation du fichier `.ovpn`, vérifiez les paramètres de serveur et l'adresse IP configurée dans le fichier.

Cela devrait résoudre notre problème d'exportation de configuration depuis pfSense.
# Référence :
- https://docs.netgate.com/pfsense/en/latest/packages/openvpn-client-export.html
