# Pratique 01 : Mise en Place d'un Contrôleur de Domaine et de Serveurs Membres

# Schéma 

```plaintext
                        ┌─────────────────────────────────────┐
                        │                                     │
                        │     Routeur / Passerelle (Gateway)  │
                        │        IP: 192.168.1.1              │
                        │        Masque : 255.255.255.0       │
                        │                                     │
                        └─────────────────────────────────────┘
                                     │
                                     │
─────────────────────────────────────┼────────────────────────────────────────
                                     │
                                     │
┌──────────────────────────┐       ┌──────────────────────────┐       ┌──────────────────────────┐
│                          │       │                          │       │                          │
│   CAFE-DC1               │       │   CAFE-SVR1              │       │   CAFE-CORE               │
│   (Contrôleur de Domaine)│       │   (Serveur Membre)       │       │   (Serveur Membre)        │
│   IP: 192.168.1.200      │       │   IP: 192.168.1.201      │       │   IP: 192.168.1.202       │
│   Masque : 255.255.255.0 │       │   Masque : 255.255.255.0 │       │   Masque : 255.255.255.0  │
│   Passerelle: 192.168.1.1│       │   Passerelle: 192.168.1.1│       │   Passerelle: 192.168.1.1 │
│   DNS: 192.168.1.200     │       │   DNS: 192.168.1.200     │       │   DNS: 192.168.1.200      │
│   DNS Forwarder: 8.8.8.8 │       │                          │       │                          │
│                          │       │                          │       │                          │
└──────────────────────────┘       └──────────────────────────┘       └──────────────────────────┘
```

**Informations incluses :**
- **Passerelle (Gateway)** : 192.168.1.1 pour toutes les machines.
- **Masque de sous-réseau** : 255.255.255.0 pour toutes les machines.
- **Adresse IP de chaque machine** : 192.168.1.200 pour CAFE-DC1, 192.168.1.201 pour CAFE-SVR1, et 192.168.1.202 pour CAFE-CORE.
- **DNS primaire** : 192.168.1.200 (CAFE-DC1) pour toutes les machines.
- **DNS Forwarder** (8.8.8.8) est spécifiquement indiqué sur CAFE-DC1, car il relaie les requêtes DNS externes.



# Étapes Détaillées pour la Configuration

# 1. Préparation des Machines

#### 1.1. Choisir le Matériel ou les Machines Virtuelles
- Vous devez avoir trois ordinateurs ou machines virtuelles (VM) sur lesquelles vous allez installer Windows Server 2016.
- Pour ce travail, chaque machine représentera un serveur avec les rôles suivants :
  - **CAFE-DC1** : Ce sera votre Contrôleur de Domaine (DC).
  - **CAFE-SVR1** : Un Serveur Membre qui sera joint au domaine.
  - **CAFE-CORE** : Un autre Serveur Membre, également joint au domaine.

#### 1.2. Installation de Windows Server 2019
- Installez Windows Server 2016 sur chaque machine. Vous aurez besoin d'un support d'installation (comme une clé USB ou un ISO si vous utilisez des machines virtuelles).
- Pendant l'installation, assurez-vous de choisir des noms d'ordinateur correspondant aux noms mentionnés (CAFE-DC1, CAFE-SVR1, CAFE-CORE).
- **Mot de passe suggéré pour tous les serveurs** : `Cafe2024!`
- Connectez-vous à chaque serveur après l'installation en utilisant le compte administrateur et le mot de passe défini (`Cafe2024!`).

#### 1.3. Mise à Jour Initiale
- Avant de commencer la configuration réseau, assurez-vous que chaque serveur est à jour.
- Ouvrez **Windows Update** et installez toutes les mises à jour disponibles.
- Redémarrez les serveurs après l'installation des mises à jour.

# 2. Configuration Réseau de Base

#### 2.1. Accès aux Paramètres Réseau
- Après l'installation de Windows Server, connectez-vous à chaque serveur.
- Ouvrez le **Panneau de Configuration** → **Centre Réseau et Partage** → **Modifier les paramètres de la carte**.
- Faites un clic droit sur votre carte réseau (par exemple, **Ethernet**), puis cliquez sur **Propriétés**.
- Sélectionnez **Protocole Internet Version 4 (TCP/IPv4)**, puis cliquez sur **Propriétés**.

#### 2.2. Configuration de l'Adresse IP Statique
Pour chaque serveur, configurez les paramètres réseau comme suit :

- **CAFE-DC1** :
  - **Adresse IP** : `192.168.1.200`
  - **Masque de sous-réseau** : `255.255.255.0`
  - **Passerelle par défaut** : `192.168.1.1`
  - **Serveur DNS préféré** : `192.168.1.200`
  - **Serveur DNS auxiliaire** : Laissez vide ou entrez `8.8.8.8` pour le forwarding DNS.

- **CAFE-SVR1** :
  - **Adresse IP** : `192.168.1.201`
  - **Masque de sous-réseau** : `255.255.255.0`
  - **Passerelle par défaut** : `192.168.1.1`
  - **Serveur DNS préféré** : `192.168.1.200`

- **CAFE-CORE** :
  - **Adresse IP** : `192.168.1.202`
  - **Masque de sous-réseau** : `255.255.255.0`
  - **Passerelle par défaut** : `192.168.1.1`
  - **Serveur DNS préféré** : `192.168.1.200`

#### 2.3. Validation des Paramètres
- Pour vérifier que les paramètres sont correctement configurés, ouvrez une **invite de commande** sur chaque serveur et tapez la commande suivante :
  ```plaintext
  ipconfig /all
  ```
  - Cela affichera toutes les informations réseau. Vérifiez que les adresses IP, les passerelles et les serveurs DNS sont corrects.
  
- Testez la connectivité entre les serveurs en utilisant la commande `ping` :
  ```plaintext
  ping 192.168.1.201  (Depuis CAFE-DC1 pour vérifier la connexion avec CAFE-SVR1)
  ping 192.168.1.202  (Depuis CAFE-DC1 pour vérifier la connexion avec CAFE-CORE)
  ```

#### 2.4. Configuration du Pare-feu
- Activez et configurez le pare-feu Windows pour chaque machine.
- Ouvrez **Pare-feu Windows avec fonctions avancées de sécurité** et assurez-vous que les règles pour les services DNS, DHCP, et Active Directory sont activées pour permettre la communication entre les serveurs.

# 3. Installation et Configuration du Contrôleur de Domaine (CAFE-DC1)

#### 3.1. Installation du Rôle Active Directory Domain Services
- Sur **CAFE-DC1**, ouvrez **Gestionnaire de serveur**.
- Cliquez sur **Ajouter des rôles et des fonctionnalités**.
- Sélectionnez **Rôle basé sur une installation ou installation des services de rôle**.
- Cochez la case **Active Directory Domain Services (AD DS)**.
- Suivez l'assistant jusqu'à la fin et installez le rôle.

#### 3.2. Promotion en Contrôleur de Domaine
- Une fois l'installation terminée, vous verrez une notification indiquant que ce serveur doit être promu en contrôleur de domaine.
- Cliquez sur la notification, puis sélectionnez **Promouvoir ce serveur en contrôleur de domaine**.
- Choisissez **Ajouter une nouvelle forêt** et entrez un nom de domaine, par exemple `cafe.local`.
- Suivez l'assistant pour configurer le domaine. Choisissez le niveau fonctionnel approprié (Windows Server 2016 par exemple).
- Le serveur redémarrera après la promotion.

#### 3.3. Configuration du DNS Forwarder
- Après le redémarrage, ouvrez **Gestionnaire DNS** depuis **Outils** dans le **Gestionnaire de serveur**.
- Faites un clic droit sur le serveur, puis cliquez sur **Propriétés**.
- Allez à l'onglet **Redirecteurs** et ajoutez `8.8.8.8` comme redirecteur DNS.
- Cela permettra à votre serveur DNS de transférer les requêtes qu'il ne peut pas résoudre localement vers les serveurs DNS publics de Google.

# 4. Joindre les Serveurs Membres au Domaine

#### 4.1. Joindre CAFE-SVR1 au Domaine
- Sur CAFE-SVR1, faites un clic droit sur **Ce PC** et cliquez sur **Propriétés**.
- Cliquez sur **Modifier les paramètres** dans la section **Nom de l’ordinateur, domaine et paramètres de groupe de travail**.
- Cliquez sur le bouton **Modifier**, sélectionnez **Domaine** et entrez `cafe.local`.
- Cliquez sur **OK** et entrez les informations d'identification de l'administrateur du domaine lorsque cela est demandé (`Cafe2024!`).
- Redémarrez le serveur après l'adhésion au domaine.

#### 4.2. Joindre CAFE-CORE au Domaine
- Répétez les mêmes étapes que pour CAFE-SVR1 pour joindre CAFE-CORE au domaine.

#### 4.3. Vérification de l'Adhésion au Domaine
- Connectez-vous avec un compte de domaine sur CAFE-SVR1 et CAFE-CORE pour vérifier que l’adhésion au domaine a été réussie.
- Ouvrez une **invite de commandes** et tapez :
  ```plaintext
  echo %logonserver%
  ```

 Cette commande vous indiquera sur quel contrôleur de domaine vous êtes connecté.

# 5. Vérification Finale

#### 5.1. Vérification dans Active Directory
- Connectez-vous à **CAFE-DC1**.
- Ouvrez **Utilisateurs et ordinateurs Active Directory**.
- Vérifiez que **CAFE-SVR1** et **CAFE-CORE** apparaissent sous **Ordinateurs** dans le domaine `cafe.local`.

#### 5.2. Test de Connectivité
- Depuis **CAFE-DC1**, essayez de pinguer **CAFE-SVR1** et **CAFE-CORE** pour vérifier la connectivité.
- Assurez-vous que les serveurs répondent et qu'il n'y a pas de perte de paquets.

#### 5.3. Test d’Authentification
- Essayez de vous connecter à **CAFE-SVR1** et **CAFE-CORE** en utilisant un compte utilisateur du domaine pour vérifier que l’authentification au domaine fonctionne correctement.

# 6. Sécurisation et Maintenance

#### 6.1. Gestion des Utilisateurs
- Créez un compte utilisateur dans Active Directory avec les privilèges nécessaires pour les tâches quotidiennes.
- Configurez des groupes de sécurité pour organiser les utilisateurs en fonction de leurs rôles.

#### 6.2. Configuration des Sauvegardes
- Configurez une stratégie de sauvegarde pour sauvegarder régulièrement le contrôleur de domaine (CAFE-DC1). Vous pouvez utiliser l’outil **Windows Server Backup**.
- Assurez-vous que les sauvegardes incluent le système d'état, car il contient des informations cruciales pour la restauration d'Active Directory.

#### 6.3. Configuration des GPO (Group Policy Objects)
- Utilisez les **Stratégies de Groupe** pour appliquer des configurations et des politiques de sécurité aux ordinateurs et utilisateurs du domaine.
- Par exemple, vous pouvez définir une politique pour forcer les utilisateurs à changer leur mot de passe tous les 90 jours.

#### 6.4. Vérification Régulière
- Effectuez des vérifications régulières pour vous assurer que tous les serveurs fonctionnent correctement.
- Vérifiez les journaux d'événements sur chaque serveur pour identifier et résoudre tout problème potentiel.

#### 6.5. Documentation
- Gardez une documentation à jour de toutes les configurations que vous effectuez.
- Notez les noms d'utilisateur, mots de passe, adresses IP, et toutes les personnalisations importantes.

# Conclusion
- En suivant ces étapes, vous aurez mis en place un environnement réseau avec un contrôleur de domaine et deux serveurs membres, tous configurés avec des adresses IP statiques et connectés à un domaine Active Directory. 
- Ce laboratoire est une excellente base pour commencer à apprendre la gestion des réseaux et des serveurs sous Windows Server.

------

# Annexe 1 - Configuration des Cartes Réseau pour le Laboratoire

------


- Dans cette annexe, nous allons configurer les cartes réseau de vos machines virtuelles pour permettre à certaines d'entre elles d'accéder à Internet tout en restant connectées au réseau interne pour la communication avec les autres machines du laboratoire.

## Configuration des Cartes Réseau dans VirtualBox

### 1. Configuration de l'Adaptateur pour le Réseau Interne (Internal Network)

Pour permettre aux machines virtuelles de communiquer entre elles dans un environnement isolé, nous utiliserons l'**Internal Network**.

#### Étapes :
1. **Ouvrez VirtualBox** et sélectionnez la machine virtuelle que vous souhaitez configurer (par exemple, **CAFE-DC1**).
2. Cliquez sur **Paramètres**.
3. Allez dans l'onglet **Réseau**.
4. Sous **Adaptateur 1** :
   - **Activer le réseau** doit être coché.
   - Dans le menu déroulant **Connecté à**, sélectionnez **Internal Network**.
   - Laissez le nom du réseau interne par défaut (`intnet`) ou modifiez-le selon vos préférences, mais assurez-vous que toutes les machines utilisent le même nom.
5. Cliquez sur **OK** pour appliquer les changements.
6. **Répétez ces étapes pour chaque machine virtuelle** (CAFE-DC1, CAFE-SVR1, CAFE-CORE), en vous assurant qu'elles sont toutes connectées au même **Internal Network**.

### 2. Ajout d'un Deuxième Adaptateur pour l'Accès à Internet (NAT)

Pour permettre à une machine virtuelle d'accéder à Internet tout en restant connectée au réseau interne, nous allons ajouter un deuxième adaptateur réseau configuré en **NAT**.

#### Étapes :
1. **Ouvrez VirtualBox** et sélectionnez la machine virtuelle à configurer (par exemple, **CAFE-DC1**).
2. Cliquez sur **Paramètres**.
3. Allez dans l'onglet **Réseau**.
4. Sous **Adaptateur 2** :
   - Cochez la case **Activer le réseau**.
   - Dans le menu déroulant **Connecté à**, sélectionnez **NAT**.
5. Cliquez sur **OK** pour appliquer les changements.
6. **Répétez ces étapes pour chaque machine virtuelle** qui doit avoir accès à Internet.

### 3. Vérification et Test

Après avoir configuré les adaptateurs réseau, suivez les étapes suivantes pour vérifier que tout fonctionne correctement.

#### 3.1. Vérification de la Connectivité Interne
- Démarrez les machines virtuelles et ouvrez une **invite de commande**.
- Testez la connectivité interne en utilisant la commande `ping` :
  ```plaintext
  ping 192.168.1.201  (Depuis CAFE-DC1 pour vérifier la connexion avec CAFE-SVR1)
  ping 192.168.1.202  (Depuis CAFE-DC1 pour vérifier la connexion avec CAFE-CORE)
  ```
- Assurez-vous que les machines peuvent se pinger entre elles.

#### 3.2. Vérification de l'Accès à Internet
- Ouvrez une **invite de commande** sur une machine qui doit avoir accès à Internet (par exemple, **CAFE-DC1**).
- Tapez la commande suivante pour tester la connectivité Internet :
  ```plaintext
  ping www.google.com
  ```
- Si vous recevez des réponses, cela signifie que l'accès à Internet est fonctionnel.

### 4. Résolution des Problèmes

Si l'une des machines ne parvient pas à se connecter correctement, voici quelques étapes de dépannage :

#### 4.1. Problèmes de Connectivité Interne
- Assurez-vous que toutes les machines sont connectées au même **Internal Network**.
- Vérifiez les configurations IP de chaque machine avec la commande `ipconfig /all` pour vous assurer que les adresses IP et les DNS sont correctement configurés.

#### 4.2. Problèmes d'Accès à Internet
- Assurez-vous que **Adaptateur 2** est bien configuré en **NAT**.
- Redémarrez la machine virtuelle après avoir fait les changements.
- Vérifiez si les paramètres DNS sont correctement configurés.



- Cette annexe 1  vous permet de configurer un réseau mixte où certaines machines peuvent accéder à Internet tout en restant connectées à un réseau interne pour la communication entre les serveurs. Avec ces étapes, vous devriez être en mesure de créer un environnement de laboratoire flexible et sécurisé, adapté à différents scénarios d'apprentissage et de test.


----

# ANNEXE 2 - Utilisation de POWERSHELL

----

- Mettre en place votre environnement de laboratoire avec trois serveurs, en utilisant PowerShell pour automatiser une grande partie de la configuration, y compris la configuration réseau, l'installation d'Active Directory, et l'ajout des serveurs membres au domaine.

# 1. **Schéma de Réseau**

```plaintext
                        ┌─────────────────────────────────────┐
                        │                                     │
                        │     Routeur / Passerelle (Gateway)  │
                        │        IP: 192.168.1.1              │
                        │                                     │
                        └─────────────────────────────────────┘
                                     │
                                     │
─────────────────────────────────────┼────────────────────────────────────────
                                     │
                                     │
┌──────────────────────────┐       ┌──────────────────────────┐       ┌──────────────────────────┐
│                          │       │                          │       │                          │
│   CAFE-DC1               │       │   CAFE-SVR1              │       │   CAFE-CORE               │
│   (Contrôleur de domaine)│       │   (Serveur Membre)       │       │   (Serveur Membre)        │
│   IP: 192.168.1.200      │       │   IP: 192.168.1.201      │       │   IP: 192.168.1.202       │
│   DNS: 192.168.1.200     │       │   DNS: 192.168.1.200     │       │   DNS: 192.168.1.200      │
│   DNS Forwarder: 8.8.8.8 │       │                          │       │   DNS Forwarder: 8.8.8.8  │
│                          │       │                          │       │                          │
└──────────────────────────┘       └──────────────────────────┘       └──────────────────────────┘
```

# 2. **Préparation des Machines**

#### a. **Choisir le Matériel ou les Machines Virtuelles**
- Vous avez besoin de trois machines virtuelles (VM) :
  - **CAFE-DC1** : Contrôleur de Domaine
  - **CAFE-SVR1** : Serveur Membre
  - **CAFE-CORE** : Serveur Membre
  
#### b. **Installation de Windows Server 2019**
- Installez Windows Server 2019 sur chaque machine avec les noms d'ordinateur suivants : CAFE-DC1, CAFE-SVR1, CAFE-CORE.
- **Mot de passe suggéré pour tous les serveurs** : `Cafe2024!`

# 3. **Configuration Réseau de Base avec PowerShell**

#### a. **Configurer CAFE-DC1**

1. **Renommer le Serveur**
   ```powershell
   Rename-Computer -NewName "CAFE-DC1" -Restart
   ```

2. **Configurer l'Adresse IP Statique et le DNS**
   ```powershell
   New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.1.200 -PrefixLength 24 -DefaultGateway 192.168.1.1
   Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 192.168.1.200, 8.8.8.8
   ```

#### b. **Configurer CAFE-SVR1 et CAFE-CORE**

Répétez les étapes suivantes pour **CAFE-SVR1** et **CAFE-CORE** :

1. **Renommer le Serveur**
   ```powershell
   Rename-Computer -NewName "CAFE-SVR1" -Restart
   ```

2. **Configurer l'Adresse IP Statique et le DNS**
   Pour **CAFE-SVR1** :
   ```powershell
   New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.1.201 -PrefixLength 24 -DefaultGateway 192.168.1.1
   Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 192.168.1.200
   ```
   Pour **CAFE-CORE** :
   ```powershell
   New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.1.202 -PrefixLength 24 -DefaultGateway 192.168.1.1
   Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 192.168.1.200
   ```

# 4. **Installation et Configuration du Contrôleur de Domaine avec PowerShell**

#### a. **Installer le Rôle Active Directory Domain Services (AD DS)**
Sur **CAFE-DC1**, exécutez :
```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
```

#### b. **Promouvoir CAFE-DC1 en Contrôleur de Domaine**
```powershell
Install-ADDSForest -DomainName "cafe.local" -SafeModeAdministratorPassword (ConvertTo-SecureString -AsPlainText "Cafe2024!" -Force) -Force -Restart
```

# 5. **Joindre les Serveurs Membres au Domaine**

#### a. **Joindre CAFE-SVR1 au Domaine**
Sur **CAFE-SVR1** :
```powershell
Add-Computer -DomainName "cafe.local" -Credential cafe\Administrator -Restart
```

#### b. **Joindre CAFE-CORE au Domaine**
Sur **CAFE-CORE** :
```powershell
Add-Computer -DomainName "cafe.local" -Credential cafe\Administrator -Restart
```

# 6. **Configuration DNS et Sécurisation**

#### a. **Configurer le DNS Forwarder sur CAFE-DC1**
```powershell
Add-DnsServerForwarder -IPAddress "8.8.8.8"
```

# 7. **Vérification Finale**

#### a. **Vérifier les Objets du Domaine**
Sur **CAFE-DC1**, assurez-vous que **CAFE-SVR1** et **CAFE-CORE** apparaissent dans Active Directory :
```powershell
Get-ADComputer -Filter *
```

#### b. **Tester la Connectivité**
Testez la connectivité entre les serveurs :
```powershell
Test-Connection -ComputerName "CAFE-SVR1"
Test-Connection -ComputerName "CAFE-CORE"
```

# 8. **Sécurisation et Maintenance**

#### a. **Création d'Utilisateurs et de Groupes**
Créez des utilisateurs et groupes pour la gestion des accès :
```powershell
New-ADUser -Name "John Doe" -GivenName "John" -Surname "Doe" -SamAccountName "jdoe" -UserPrincipalName "jdoe@cafe.local" -Path "OU=Users,DC=cafe,DC=local" -AccountPassword (ConvertTo-SecureString "Cafe2024!" -AsPlainText -Force) -Enabled $true
```


- En suivant ce guide, vous avez automatisé la configuration d'un contrôleur de domaine et de deux serveurs membres en utilisant PowerShell.
- Cette approche vous permet de gérer efficacement un environnement de laboratoire tout en gagnant du temps avec des configurations répétitives.


----------
# Références :
----------

# pfSense Configuration Lab: Mastering Network Security and Management: 
- https://medium.com/@piusppk/pfsense-configuration-lab-mastering-network-security-and-management-fa7aee675c8e
- https://www.youtube.com/watch?v=zdAglC3AmJo (How to Install and Configure pfSense on VMware)

# Configurez AD DS
- https://www.youtube.com/watch?v=A4Epd6uuolk
# GPO lab
- https://www.youtube.com/watch?v=zdAglC3AmJo

# https://www.youtube.com/watch?v=d-uZXg7jN8Y
# https://www.youtube.com/watch?v=A4Epd6uuolk
