# Mise en place d’un VPN avec authentification RADIUS via Windows Server et pfSense

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

#### 3.2. Exporter la configuration VPN depuis pfSense
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

