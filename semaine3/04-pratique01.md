------
# Pratique : Désactivation de SMB 1.0 et Configuration du Chiffrement SMB sur des Partages
----------

## Objectifs de la démonstration

Dans cette démonstration, vous allez apprendre à :

1. **Comprendre l'environnement AD DS (Active Directory Domain Services)**
2. **Créer un contrôleur de domaine avec AD DS**
3. **Ajouter une machine Windows 10 au domaine**
4. **Désactiver SMB 1.x sur Windows 10**
5. **Désactiver SMB 1.x sur Windows Server 2016**
6. **Configurer un partage pour le chiffrement SMB**
7. **Capturer le trafic SMB chiffré**
8. **Utiliser les outils d’analyse pour vérifier le chiffrement**

## Schéma de la Configuration

Le schéma suivant illustre la configuration que vous allez mettre en place. Chaque élément du schéma représente une partie essentielle du réseau.

```ascii
                               +------------------------+
                               |      Réseau Local       |
                               |   (Domain Network)      |
                               +-----------+-------------+
                                           |
                                           |
                                +----------+-----------+
                                |   Active Directory   |
                                |   Domain Services    |
                                |    (Windows Server)  |
                                +----------+-----------+
                                           |
                                           |
                                +----------+-----------+
                                |        Windows 10     |
                                |       Client (CL1)    |
                                |     (Joined to AD)    |
                                +-----------------------+
```

### Explication du Schéma

- **Active Directory Domain Services (AD DS) :** C'est le cœur de l'infrastructure réseau. AD DS permet de centraliser la gestion des utilisateurs, des ordinateurs et des ressources dans un domaine. Dans ce lab, il est installé sur un serveur Windows (par exemple, Windows Server 2016).
- **Windows 10 Client (CL1) :** C'est un ordinateur Windows 10 qui sera joint au domaine géré par AD DS. Ce client sera utilisé pour tester l'accès aux ressources partagées et vérifier le chiffrement SMB.

## Étapes de Configuration Détaillées

# 1. Comprendre l'Environnement AD DS

Avant de commencer, il est important de comprendre ce que fait Active Directory Domain Services (AD DS). AD DS est un service qui permet de centraliser la gestion des utilisateurs, des groupes et des ordinateurs sur un réseau. Il vous permet de créer un domaine (une sorte de "club" réseau) où les utilisateurs et les ordinateurs peuvent être ajoutés. Une fois qu'un ordinateur est ajouté au domaine, il peut accéder aux ressources partagées, telles que les dossiers et les imprimantes, en fonction des autorisations définies.

# 2. Créer un Contrôleur de Domaine AD DS sur Windows Server

1. **Installation d'Active Directory Domain Services (AD DS) :**
   - Sur votre serveur Windows (ex: Windows Server 2016), ouvrez le **Gestionnaire de serveur**.
   - Allez dans **Gérer > Ajouter des rôles et fonctionnalités**.
   - Suivez l'assistant pour installer **Active Directory Domain Services**. Ce service permet au serveur de devenir un contrôleur de domaine.

2. **Promotion du Serveur en tant que Contrôleur de Domaine :**
   - Après avoir installé AD DS, vous verrez une notification dans le Gestionnaire de serveur indiquant qu'il faut configurer AD DS.
   - Cliquez sur **Promouvoir ce serveur en contrôleur de domaine**.
   - Choisissez de **Ajouter une nouvelle forêt** et entrez un nom pour votre domaine (par exemple, `mon-domaine.local`).
   - Configurez les paramètres DNS et veillez à ce que le serveur soit configuré pour gérer les requêtes DNS.
   - Finalisez l'installation et redémarrez le serveur une fois la promotion terminée.

# 3. Configurer le Réseau pour le Domaine

1. **Vérification de la Connectivité Réseau :**
   - Assurez-vous que le serveur AD DS et la machine Windows 10 sont sur le même réseau local (LAN) ou qu'ils peuvent communiquer via un VPN sécurisé si vous travaillez sur un réseau étendu.
   - Les deux machines doivent pouvoir se "voir" sur le réseau pour que la machine Windows 10 puisse rejoindre le domaine.

2. **Configuration des Pare-feu :**
   - Ouvrez les ports nécessaires pour les services de domaine (tels que les services DNS, SMB, etc.) sur les pare-feu des deux machines. Cela permettra une communication fluide entre les machines.

# 4. Ajouter la Machine Windows 10 au Domaine

1. **Préparation de la Machine Windows 10 (CL1) :**
   - Assurez-vous que Windows 10 est configuré pour utiliser le serveur DNS du contrôleur de domaine (AD DS) comme serveur DNS principal.
   - Pour ce faire, allez dans **Paramètres > Réseau et Internet > Centre Réseau et partage > Modifier les paramètres de la carte**.
   - Faites un clic droit sur votre connexion réseau, choisissez **Propriétés**, sélectionnez **Protocole Internet Version 4 (TCP/IPv4)**, puis cliquez sur **Propriétés**.
   - Entrez l'adresse IP du serveur AD DS comme **Serveur DNS préféré**.

2. **Rejoindre le Domaine :**
   - Ouvrez les **Paramètres > Système > À propos > Modifier les paramètres**.
   - Cliquez sur **Modifier...** sous **Nom de l'ordinateur, domaine et groupe de travail**.
   - Sélectionnez **Domaine** et entrez le nom du domaine (ex: `mon-domaine.local`).
   - Vous serez invité à entrer un nom d'utilisateur et un mot de passe pour un compte avec les droits d'administrateur de domaine.
   - Après l'adhésion réussie au domaine, redémarrez la machine pour appliquer les modifications.

# 5. Désactiver SMB 1.x sur Windows 10

SMB 1.x est une ancienne version du protocole SMB qui présente des vulnérabilités de sécurité. Il est donc important de le désactiver.

1. **Ouvrir Windows PowerShell sur CL1 :**
   - Cliquez sur **Démarrer** et tapez **PowerShell**. Faites un clic droit sur **Windows PowerShell** et choisissez **Exécuter en tant qu'administrateur**.

2. **Exécuter la commande pour désactiver SMB 1.x :**
   - À l'invite de commandes, tapez la commande suivante et appuyez sur Entrée :
   ```shell
   Set-SmbServerConfiguration –EnableSMB1Protocol $false
   ```
   - Cette commande désactive SMB 1.x sur votre machine Windows 10. Il est recommandé de redémarrer l'ordinateur pour s'assurer que les modifications sont appliquées.

# 6. Désactiver SMB 1.x sur Windows Server 2016

De la même manière que pour Windows 10, nous allons désactiver SMB 1.x sur le serveur pour des raisons de sécurité.

1. **Ouvrir Windows PowerShell sur SVR1 :**
   - Connectez-vous à votre serveur Windows 2016 et ouvrez **Windows PowerShell** en tant qu'administrateur.

2. **Exécuter la commande pour désactiver SMB 1.x :**
   - Tapez la commande suivante et appuyez sur Entrée :
   ```shell
   Set-SmbServerConfiguration –EnableSMB1Protocol $false
   ```

3. **Vérification de la Désactivation :**
   - Pour vérifier que SMB 1.x est bien désactivé, tapez la commande suivante :
   ```shell
   Get-SmbServerConfiguration | Select EnableSMB1Protocol
   ```
   - Cette commande doit retourner `False`, confirmant que SMB 1.x est désactivé.

# 7. Configurer un Partage pour le Chiffrement SMB

Le chiffrement SMB est essentiel pour protéger les données en transit sur le réseau. Nous allons configurer un partage de fichiers avec le chiffrement activé.

1. **Créer un Partage Chiffré :**
   - Sur **SVR1**, ouvrez **Windows PowerShell** et exécutez la commande suivante pour créer un nouveau partage avec le chiffrement activé :
   ```shell
   New-SmbShare –Name “Chapitre2” –Path “D:\Labo\Chapitre2” –EncryptData $true
   ```
   - Cette commande crée un partage de fichiers nommé "Chapitre2" dans le dossier `D:\Labo\Chapitre2` avec le chiffrement activé.

2. **Définir les Autorisations du Partage :**
   - Après avoir créé le partage, vous devez définir les autorisations pour permettre à d'autres utilisateurs d'y accéder. Tapez la commande suivante pour donner à "Everyone" un accès complet :
   ```shell
   Grant-FileShareAccess –Name Chapitre2 –AccountName “Everyone” –AccessRight Full
   ```

3. **Tester l'Accès au Partage :**
   - Depuis **CL1**, ouvrez l'Explorateur de fichiers et accédez au partage en tapant `\\SVR1\Chapitre2` dans la barre d'adresse. Vous devriez pouvoir accéder au dossier partagé.

# 8. Capturer le Trafic SMB Chiffré

Pour vérifier que le chiffrement SMB fonctionne correctement, nous allons capturer et analyser le trafic réseau.

1. **Démarrer la Capture sur SVR1 :**
   - Sur **SVR1**, ouvrez **Microsoft Message Analyzer** (ou un autre outil de capture réseau).
   - Dans la page **Start**, cliquez sur **Start Local Trace** pour commencer la capture du trafic réseau.

2. **Transférer un Fichier depuis CL1 :**
   - Sur **CL1**, copiez un fichier vers le partage réseau que vous avez créé (`\\SVR1\Chapitre2`). Cela générera du trafic SMB chiffré.

3. **Arrêter la Capture sur SVR1 :**
   - Une fois le fichier transféré, revenez sur **SVR1** et arrêtez la capture dans Microsoft Message Analyzer.

# 9. Examiner les Outils d’Analyse

Nous allons maintenant analyser le trafic capturé pour vérifier que les données sont bien chiffrées.

1. **Filtrer le Trafic Capturé :**
   - Dans Microsoft Message Analyzer, entrez le filtre suivant pour n'afficher que le trafic SMB2 provenant de l'adresse IP du client :
   ```shell
   (*address==172.16.0.40) and (SMB2)
   ```
   - Cliquez sur **Apply** pour appliquer le filtre.

2. **Analyser les Résultats :**
   - Triez les résultats par **Summary** pour voir le type de messages capturés. Vous devriez voir des entrées marquées **TransformMessage, Encrypted**, ce qui signifie que les données sont bien chiffrées pendant leur transfert.

## Conclusion

Cette démonstration a couvert l'ensemble des étapes nécessaires pour sécuriser un environnement réseau en utilisant SMB 3.x. Nous avons désactivé des versions obsolètes du protocole SMB, configuré un partage chiffré, et utilisé des outils d'analyse pour vérifier la sécurité des données en transit. En suivant ces étapes, vous aurez acquis une bonne compréhension de la manière de protéger efficacement les partages de fichiers dans un réseau d'entreprise.

